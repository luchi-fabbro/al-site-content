---
title: "The Rather Large Array"
description: "Programming and hardware integration for a 24-camera, LED-controlled gallery installation at Art Center College of Design — part of the MADE UP: Design's Fictions exhibition."
image: "https://cdn.datanut.net/assets/images/twilight_smaller.jpg"
demo: "https://www.timdurfee.com/The-Rather-Large-Array"
order:
featured: false
date: "2011-01-29"
tech:
  - MDP
  - Installation
  - Physical
  - Arduino
  - xBee
  - PHP
draft: false
---

*MADE UP: Design's Fictions* was an exhibition hosted by the Media Design Practices program at [Art Center College of Design](https://www.artcenter.edu) in South Pasadena, CA, running January 29 through March 20, 2011. The Rather Large Array was its centerpiece: a 50x50 foot suspended installation designed by [Tim Durfee](https://www.timdurfee.com) that functioned simultaneously as gallery structure and live scanning apparatus. Every ten minutes, it would photograph the space with 24 cameras, compile the images into a designed PDF, and print them to the back of the exhibition programs — so visitors often found themselves inside the catalog alongside the work on display.

The installation won the [AIA Los Angeles](https://www.aialosangeles.org) Honor Award and was a finalist for the Architizer A+ Award.

<img src="https://cdn.datanut.net/assets/images/twilight_smaller.jpg" alt="The Rather Large Array installation" width="100%" style="border-radius: 4px;" />

<video controls width="100%" style="margin-top: 1rem; border-radius: 4px;">
  <source src="https://cdn.datanut.net/assets/videos/rla_timelapse.mp4" type="video/mp4" />
  Your browser does not support the video tag.
</video>

---

## My role

I was initially approached by Jayne Vidheecharoen to assist with the camera technology. After working through various prototypes it became clear the scope was larger than anticipated. The final system needed to pull images symmetrically from an array of 24 cameras, coordinate LED switching across the structure, generate a designed PDF from the imagery, upload it via FTP for printing, and project it onto a screen — all triggered on a timed sequence from a single operator interface.

I built a web-based control panel in HTML, CSS, jQuery, Ajax, and PHP. The panel allowed an operator to set a trigger interval (ten minutes by default) and kick off the full pre-programmed sequence. I also built supporting tools for managing the cameras, PDFs, and LEDs independently.

For the LED control, we used xBees — wireless microcontrollers — running four LEDs per unit across seven xBees for a total of 28 LEDs, with one additional xBee dedicated as the base station.

A big thank you to Jayne Vidheecharoen for sitting through the programming sessions, soldering wires to LEDs, and building the grids that made all of it work.

<div style="display: flex; gap: 1rem; margin-top: 1.5rem; flex-wrap: wrap;">
  <img src="https://cdn.datanut.net/assets/images/04_TimDurfee-amp-TheRatherLargeArray_PHOTOCatherine-R.-Wygal-Deanna-McClure.jpg" alt="The Rather Large Array — gallery view" width="48%" style="border-radius: 4px; min-width: 200px; flex: 1;" />
  <img src="https://cdn.datanut.net/assets/images/08_TimDurfee-amp_TheRatherLargeArray_outputsystem_resize.jpg" alt="The Rather Large Array — output system" width="48%" style="border-radius: 4px; min-width: 200px; flex: 1;" />
</div>

---

## About the exhibition

*MADE UP: Design's Fictions* presented the work of major and emerging international practices that forecast, hypothesize, muse, role-play, or otherwise speculate to produce work relevant to our increasingly confusing and accelerated world. The Rather Large Array served as both display apparatus and active participant in the show, its structure making visible the same questions the exhibition was asking: what happens when sensing and recording become inseparable from architecture itself?

The construction approach was intentionally sustainable. Almost none of the fourteen 20-foot Douglas Fir beams were cut, the steel rigging was rented for the installation period, and the 768 PVC pipes were donated to a non-profit housing and urban gardening organization after the show closed.

---

## Credits

| Role | Detail |
| ---- | ------ |
| Curated by | Tim Durfee with Haelim Paek |
| Installation | Tim Durfee and Like Now |
| Engineering | Buro Happold |
| Graphic design | Brian Roettinger |
| Programming | Ewan Branda, Angelo Luchi |
| Project team | Haila Adamo, Laura Goard, Fallon James, Mike Manalo, Matthew Manos, Salvador Orara, Nicholas Paradowski, Bora Shin, Jayne Vidheecharoen |
| Institution | [Art Center College of Design](https://www.artcenter.edu), Pasadena, CA |
| Award | [AIA Los Angeles](https://www.aialosangeles.org) Honor Award |
