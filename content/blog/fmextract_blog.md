---
title: "fmextract and the FMP12 on-disk format"
description: "A command-line exporter for unencrypted FileMaker .fmp12 files — what lives on disk, how sectors and chunks relate to tables and containers, and why Evan Miller’s fmptools was the right foundation."
date: "2026-04-24"
tags:
  - FileMaker
  - FMP12
  - CLI
  - Data export
  - Reverse engineering
  - fmptools
draft: false
---

Your rows and container files are not “inside FileMaker” in some abstract sense — they are bytes in a proprietary binary layout. If you have an **unencrypted** `.fmp12` and you need CSV, JSON, SQLite, or real files on disk without running FileMaker Server or Pro, that layout is the whole story. **fmextract** is a small CLI that walks that structure and exports it. This post is a readable tour of what that means on disk, where the important markers sit, and how containers connect to FileMaker’s **object pool** — with enough honesty about limits that you can decide when the tool fits and when it does not.

**Downloads (v1.0.2)**

- [Linux x86_64](https://cdn.datanut.net/assets/downloads/fmextract/fmextract-1.0.2-linux-x86_64.tar.gz) — `fmextract-1.0.2-linux-x86_64.tar.gz`
- [macOS ARM64](https://cdn.datanut.net/assets/downloads/fmextract/fmextract-1.0.2-macos-arm64.tar.gz) — `fmextract-1.0.2-macos-arm64.tar.gz`

fmextract does **not** run FileMaker. It does **not** decrypt **Encryption at Rest (EAR)** files: those are detected from the header and rejected with a clear error.

---

## Standing on fmptools

The parsing engine in this line of work is **[fmptools](https://github.com/evanmiller/fmptools)** by **Evan Miller**, MIT-licensed — a C library, reference programs (`fmp2json`, `fmp2sqlite`, and friends), and an unusually good internal spec: the **`HACKING`** file in the upstream tree. That document explains the **path model** (push/pop integers forming a logical tree), the **chunk archetypes** (path ops, simple data, segmented data, short/long key–value), and **fp5 vs fmp12** differences — not only hex dumps.

In code terms, the layering is deliberate: **`fmp.c`** handles file I/O, header detection, and the sector graph; **`block.c`** turns a sector payload into a linked list of chunk records; **`read_values.c`** interprets chunks in table/column context. fmextract adds product-style export, logging, container file output, broader opcode coverage where real files demanded it, and release automation. Any serious write-up should **credit fmptools** and link the upstream repository — this work is a fork and extension, not a from-scratch reinvention.

---

## What fmextract does, in one breath

It opens the file as a binary, follows FileMaker’s internal **sector → block → chunk** graph, and exports:

- **All tables** (or one table via `-t`) to **CSV**, **JSON**, or **SQLite**
- **Container fields** (images, PDFs, ZIPs, and so on) either as **files in a directory** or as **BLOBs** in SQLite mode
- An optional **second pass** `--dump-container-pool`: every **v7 object-pool** blob indexed under FileMaker’s internal path convention, written under `container_pool/<Table>/pool_<objectid>.<ext>` plus a **`_manifest.tsv`**

That last mode matters when you care that **the bytes exist** even if row/column mapping through the layout parser is thin for a particular file version — more on that below.

---

## Why that is useful

- **Data liberation** — Analytics, warehousing, litigation hold, or migration without a live FileMaker stack.
- **Automation** — Scriptable, CI-friendly CLI; no GUI.
- **Containers** — Media and documents that live inside the file (or in referenced pool storage) can be **materialized as normal files**, with extensions inferred from **magic bytes** after the decode path the format requires.
- **Forensics and debugging** — Pool dump mode surfaces **every** indexed pool object for a table, not only rows that map cleanly through the high-level export.
- **Transparency** — The format is proprietary; tools like this document **observed structure** so teams can reason about backup size, PII inside binaries, and risk.

---

## A short hex tour of the file

The reader loads the **first 1024 bytes** as a header (`read_header` in upstream `fmp.c`). Unless stated otherwise, offsets are from **file offset `0x00000000`**.

### Fixed magic (15 bytes)

The file must begin with this sequence (hex):

```text
Offset   Hex bytes
------   ------------------------------------------------
0x00     00 01 00 00  00 02 00 01  00 05 00 02  00 02 C0
```

In source this is the `MAGICK` string in `fmp.c`. If it does not match, you do not have what this codepath considers a valid FileMaker database header.

### Format tag at byte 15

Immediately after those 15 bytes, **five ASCII characters** starting at **offset 15** decide the broad format path:

| Bytes at 15–19 (ASCII) | Meaning in this codebase |
| ---------------------- | -------------------------- |
| **`HBAM7`** | **FMP12-style**: **4096-byte sectors**, payload obfuscation **XOR `0x5A`**, modern text decoding. |
| **`HBAMe`** | **Encryption at Rest** — **not supported**; open fails with an encrypted-file error. |
| Anything else (historically) | Treated as **older / fp5-style**: **1024-byte sectors**, MacRoman via `iconv`, different sector header layout. |

So **`0x0F`–`0x13`** is the first place an analyst distinguishes **FMP12-capable** files from **legacy** or **encrypted** variants.

### A few more header fields (still inside the first 1024 bytes)

| Approx. offset | Role |
| -------------- | ---- |
| **`0x209` (521)** | **Version class:** `buf[521] == 0x1E` → internal `version_num = 12`, else **`7`**. That gates **v7 vs v12** bytecode handling in `block.c` (`process_block_v7` vs legacy). |
| **`0x213` (531)** | **Seven-byte** fixed-width **date string** (parsed with `strptime` as `%d%b%y` when available). |
| **`0x21D` (541)** | **Pascal string**: one length byte, then ASCII creator/version text. |

These offsets are what `read_header` actually reads — not folklore copied from a random forum post.

### Where “real” sectors start

For **FMP12 (`HBAM7`)**, the implementation seeks past **one** full **4096-byte** sector after the header buffer, then reads **4096-byte** sectors for the block graph. For legacy **1024-byte** format, it skips **two** sectors (**2048** bytes) before the main stream. In practice the **first navigable FMP12 sector** often lands around **`0x1000`** — but **sector linkage is authoritative**, not a single hardcoded jump for every variant.

---

## FMP12 sectors: header, payload, links

For **`HBAM7`** files, each sector is **4096 bytes**. The **first 20 bytes** are a header; the rest is **payload** bytecode:

| Offset in sector | Size | Content |
| ---------------- | ---- | ------- |
| **0** | 1 | **Deleted** flag (`1` = deleted) |
| **1** | 1 | **Level** |
| **4** | 4 | **Previous sector ID** (little-endian style via `copy_int`) |
| **8** | 4 | **Next sector ID** |
| **20** | **4076** | **Payload** (chunk bytecode stream) |

Sectors form a **doubly linked list** by `prev_id` / `next_id`. They are **not** guaranteed to appear in logical order on disk — the runtime stitches the stream by following those pointers. Total file size is sanity-checked against **`next_id`** from the first block.

For FMP12, “payload length” is effectively “everything after the 20-byte header” (`4096 - 20 = 4076` bytes).

---

## XOR `0x5A` and why your JPEG might not look like a JPEG at a naive offset

For FMP12-class files, **`xor_mask = 0x5A`**. Many **textual** payload bytes are stored XORed before they are interpreted as length-prefixed strings or path integers.

**Binary containers** are the subtle case. **Inline** container payloads handled in `read_values.c` are often **XOR-decoded** when testing magic (see paths around `emit_value` / `fmp_embedded_binary_payload_offset`). **Object pool** segments indexed from the container map are described in comments as **not** XOR-masked in the same way; **`fmp_read_container`** concatenates **raw** segment bytes from parsed chunks.

So when reverse-engineering a stubborn file: if **`FF D8 FF`** does not appear where you expect, try **XOR with `0x5A`** or look for **wrapper bytes** before the true start-of-image. fmextract includes a **capped scan** for embedded magics after XOR for inline cases — a nod to real-world messiness, not a guarantee that every future encoding stays tame.

---

## Chunks and the path model (without pretending the opcode table is finite)

`HACKING` describes the conceptual model; **`process_block_v7`** in `block.c` implements it for modern files. At a high level, chunks fall into a small set of ideas:

1. **Path push** — enter a deeper logical path (often integers).
2. **Path pop** — step up one level.
3. **Simple data** — raw bytes at the current path.
4. **Data segment** — `(segment_index, bytes)` for **split** large values.
5. **Field ref simple** — integer key → bytes value.
6. **Field ref long** — byte-sequence key → bytes value.

The **path** works like a **tree address**: push `3`, push `1`, push `5` → the next chunk’s data conceptually lives at **`[3][1][5]`**; a pop returns toward the root of that stack.

The **first byte** of a chunk selects a decoder branch. Examples you will see in **`process_block_v7`** include:

| Opcode / range | Typical role in this codebase |
| -------------- | ------------------------------ |
| **`0x0F`** | **`DATA_SEGMENT`**: segment index, 16-bit length, then bytes. |
| **`0x00`** | **`FIELD_REF_SIMPLE`** with a following length byte. |
| **`< 0x40`** (with constraints) | **`FIELD_REF_LONG`**-style regions. |
| **`0xC0`** | **Path pop**. |
| **`0xC1`–`0xFE`** | **Path push** with length derived from the opcode. |
| **`0xFF`** + sub-opcode | **Extended** layouts. |

fmextract’s fork widens coverage with **aliases** for additional on-disk variants (for example extended short field encodings and mirrored opcodes) so more files classify as **known chunk types** instead of aborting the parse. That is engineering for the field, not a claim that FileMaker will never ship a new encoding — **they can and will**.

---

## Containers: three ideas people mix up

### 1. Logical object

What you actually want: the **assembled** JPEG, PDF, or ZIP — either one contiguous decoded range or **`DATA_SEGMENT`** pieces concatenated in **`segment_index` order**.

### 2. Object pool

For **v7 / FMP12**, pool storage is indexed under a path shaped like:

```text
[ table_index + 128 ] [ 31 ] [ 5 ] [ object_id ]  →  DATA_SEGMENT chunks
```

- **`segment_index == 0`** — skipped in the indexer (treated as FileMaker metadata).
- **`segment_index >= 1`** — payload bytes that belong to the object.

**`fmp_read_container(map, object_id)`** allocates a buffer and **concatenates all segments** for that `object_id` in **`segment_index` order**.

### 3. Physical file offsets

What **`xxd`** shows between two pieces of the same image might be **padding, other records, or free space**. That padding is **not** part of the JPEG. **Assembly is defined at the chunk layer**, not by scanning the whole file for one contiguous `FF D8 … FF D9` run — though that heuristic can still help a human **locate** where data landed during manual forensics.

---

## How normal export surfaces containers

1. **Inline binary** — Values may arrive prefixed with **`__B64__:`** (base64 of XOR-decoded payload); fmextract **decodes** and writes a file.
2. **Pool reference** — Field values that end with Unicode **U+E000** (UTF-8 **`EE 80 80`**) carry a leading hex **`object_id`**; **`fmp_read_container`** fills the buffer from the pool map.

**Magic → extension** (after decode / as stored), in the usual spirit:

| Magic | Typical extension |
| ----- | ----------------- |
| `FF D8 FF` | `.jpg` |
| `89 50 4E 47` | `.png` |
| `GIF8` | `.gif` |
| `%PDF` | `.pdf` |
| `PK\x03\x04` | `.zip` |
| `BM` | `.bmp` |
| `II*\0` / `MM\0*` | `.tiff` |
| `D0 CF 11 E0` | `.doc` (OLE) |

If magic is not at offset zero, **`fmp_embedded_binary_payload_offset`** scans (with a cap) for known signatures after XOR.

---

## Limitations worth stating out loud

- **Proprietary format** — This is **reverse engineering**. Future FileMaker builds can introduce **new opcodes** or path layouts; “works on my corpus” is not “complete formal specification.”
- **EAR / `HBAMe`** — Refused by design. The tool does not pretend to decrypt your file.
- **Row/column mapping** — Pool dump can prove **the bytes exist** when CSV mapping is incomplete; fixing mapping is **parser and schema work**, not “grep harder.”
- **Legal and compliance** — Extraction may implicate **license terms**, **privacy**, and **retention** policies. That is not something a README can settle for your organization — treat it as a governance question, not only a technical one.

---

## Quick reference

| Item | Value / location |
| ---- | ---------------- |
| File magic (15 B) | Starts `00 01 00 00 … 00 02 C0` at offset **0** |
| Format tag | **`HBAM7`** vs **`HBAMe`** at offset **15** |
| FMP12 sector size | **4096** bytes |
| FMP12 sector header | **20** bytes; payload **4076** bytes |
| FMP12 XOR mask | **`0x5A`** |
| Internal `version_num` switch | Byte **521** (`0x209`): **`0x1E`** → 12, else **7** |
| Creator / Pro version string | Pascal string at **541** |
| Container pool path | **`[table_index+128][31][5][object_id]`** + `DATA_SEGMENT` |

---

## Closing

A `.fmp12` is a structured binary with a linked sector graph, XOR-obfuscated text paths, and a chunk bytecode machine that maps into tables, fields, and container pools. fmextract is one way to **materialize** that structure into formats the rest of your stack already understands — with explicit support boundaries so nobody mistakes it for a decryption tool or a promise of perpetual format completeness.

If you hit a file that parses oddly, the right next step is usually a **minimal reproducer** and a **diff in chunk classification** — the same feedback loop that made fmptools strong in the first place. Upstream remains **[github.com/evanmiller/fmptools](https://github.com/evanmiller/fmptools)**; improvements that belong in the core parser deserve to flow back there when they are clean and general.
