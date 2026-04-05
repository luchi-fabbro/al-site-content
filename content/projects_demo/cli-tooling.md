---
title: "CLI productivity kit"
description: "Small scripts and aliases that speed up day-to-day repo work—nothing fancy, just sharp."
image: "https://images.unsplash.com/photo-1555066931-4365d14bab8c?w=1200&h=675&fit=crop"
repo: "https://github.com/yourusername/cli-productivity-kit"
order: 2
featured: true
tech:
  - Rust
  - Shell
draft: true
---

A placeholder project card to test the **grid layout**, **tech** pills, and **GitHub** button.

## Checklist before you ship

- [ ] README explains install steps
- [ ] `--help` matches real flags
- [ ] CI runs on PRs

### Nested commands conceptually

1. **Parse** arguments
   - Support `--dry-run`
   - Support config file
2. **Execute** safely
3. **Report** exit codes

## Body content

You can put longer write-ups here: motivation, screenshots via Markdown images, and links.

![Terminal aesthetic](https://images.unsplash.com/photo-1629654297299-c852622fb2d5?w=720&h=420&fit=crop)

### Example command block

```bash
# Dry run — no side effects
./scripts/sync-content.sh --dry-run

# With verbose logging
LOG_LEVEL=debug ./scripts/sync-content.sh
```

### Rust-flavored snippet

```rust
fn main() {
    println!("Hello from the edge-adjacent toolkit");
}
```

When you are ready, swap the `repo` URL and add a `demo` link if you have a live tool.
