---
layout: post
title: "Loading Assets In JavaScript or WebGL"
description: "A guide to using JavaScript for loading assets in parallel asynchronously."
category: "Technical"
tags: ["JavaScript", "Three.JS", "WebGL"]
---
{% include JB/setup %}

No one likes loading screens, but sometimes they are needed and appropriate
depending on exactly what you're doing. A while back, I posted a 
[WebGL demo][moon-demo] that I created which featured high-resolution satellite
maps of the Moon and basic normal mapped lighting effects. I wanted to keep the
resolution of the maps as high as possible while keeping the loading speed good
enough to keep people from leaving before they saw anything. I settled on
scaling my moon map down from `8192 x 4096` to `4096 x 2048`.

[moon-demo]: http://coryg89.github.io/technical/2013/06/01/photorealistic-3d-moon-demo-in-webgl-and-javascript/
