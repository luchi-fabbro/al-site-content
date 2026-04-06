---
title: "FM Barcode API"
description: "A free REST API for generating barcodes in 20+ formats, purpose-built for FileMaker's InsertFromURL function. Still running. Still free. Hundreds of millions of barcodes generated."
image: "https://cdn.datanut.net/assets/images/barcode1.png"
demo: ""
order: 4
featured: true
date: "2013-02-01"
tech:
  - FileMaker
  - PHP
  - API
  - Barcode
draft: false
---

FM Barcode API is a free REST API for generating barcodes in over 20 formats, built specifically for FileMaker developers. Pass a handful of GET parameters, get back a PNG. Drop it into a container field. Done.

It launched in February 2013 as one of the first APIs written specifically for FileMaker users around the then-new `InsertFromURL` script step — a native FileMaker function that finally gave developers a clean way to make HTTP requests without a plugin. FM Barcode API was designed from the start to take full advantage of that.

It was featured by several developers during [Pause on Error](https://www.pausewithus.com/) in 2013, which helped it find its footing in the FileMaker community quickly.

Over a decade later, it is still running and still completely free. To date it has generated hundreds of millions of barcodes for developers, businesses, and FileMaker solutions all over the world. That number keeps going up.

---

## How it works

The API follows a straightforward REST pattern. Construct a URL with your parameters, call it via `InsertFromURL`, and the response is a ready-to-use barcode PNG.

```
GET https://your-api-endpoint/?type=qrcode&data=Hello+World&width=200&height=200
```

The FileMaker `InsertFromURL` function handles the request natively — no plugin required, no additional dependencies.

[Download FileMaker Example File](https://cdn.datanut.net/assets/downloads/Barcode_Example.zip)

---

## Supported formats

The API supports over 20 barcode formats across linear, postal, and 2D types:

Codabar, Code 11, Code 39, Code 39 Extended, Code 93, Code 128, EAN-8, EAN-13, GS1-128 (EAN-128), ISBN-10, ISBN-13, Interleaved 2 of 5, Standard 2 of 5, MSI Plessey, UPC-A, UPC-E, UPC Extension 2, UPC Extension 5, PostNet, QR Code, PDF417

<div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(180px, 1fr)); gap: 1rem; margin-top: 1.5rem;">
  <img src="https://cdn.datanut.net/assets/images/barcode1.png" alt="FM Barcode API example 1" style="width: 100%; border-radius: 4px;" />
  <img src="https://cdn.datanut.net/assets/images/barcode2.png" alt="FM Barcode API example 2" style="width: 100%; border-radius: 4px;" />
  <img src="https://cdn.datanut.net/assets/images/barcode3.png" alt="FM Barcode API example 3" style="width: 100%; border-radius: 4px;" />
  <img src="https://cdn.datanut.net/assets/images/barcode4.png" alt="FM Barcode API example 4" style="width: 100%; border-radius: 4px;" />
</div>

---

## A note on longevity

Most side projects have a natural shelf life. APIs get deprecated, services go down, auth layers change. FM Barcode API has been quietly running for over a decade with no paywalls, no accounts, and no sign-up required.

The fact that it has generated hundreds of millions of barcodes without charging a cent is either a testament to the FileMaker community's resourcefulness, a reflection of how useful a simple well-designed API can be, or both. Probably both.

If you've used it over the years — thank you. It's one of those small tools that turned out to matter more than expected.
