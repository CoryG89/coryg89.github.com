---
layout: post
title: "Easily Access Webcam With CamMan.js"
listTitle: "CamMan.js"
group: "Project"
description: "A JavaScript library and event-based API for managing and accessing the webcam and microphone using WebRTC."
highlights: true
category: "Technical"
tags: ["HTML5", "JavaScript", "WebRTC", "Webcam"]
---
{% include JB/setup %}

I recently read [*How You Can Build an HTML Photobooth App*][robinson] by
John Robinson and thought it was a great article. He explains some of the basic
math behind image effects such as grayscale, sepia, and others. I thought it
was interesting enough to try to make my own demo. He includes some basic code
to access the webcam via WebRTC, but I wanted something a little more robust.
I looked for good libraries out there to do this, but couldn't find exactly
what I wanted so I decided to roll my own, using 
[**`leemachin/say-cheese`**][leemachin] as a basis to start with. I added the
ability to easily draw video out to multiple canvases from the same source
video, allowing each of them to be manipulated separately.

CamMan.js is [**available on GitHub**][repo]. You can check out my Photobooth
[**example app**][demo] which I was able to easily create using CamMan.js.

### The Basics

The simplest thing to get your webcam video onto a page.

```
<html>
  <head>
    <title>Hello CamMan</title>
    <script type="text/javascript" src="CamMan.js"></script>
  </head>
  <body>
    <div id="container"></div>
    <script type="text/javascript">
       var camMan = new CamMan({ container: 'container' });
       camMan.start();
    </script>
  </body>
</html>
```

By default passing in a container id with the constructor options will cause
the source video element to be injected into the container. If you'd rather
have a canvas element, you can wait until the webcam has been initialized and
then get a canvas. CamMan.js exposes an event-based API:

```
var camMan = new CamMan();
camMan.on('start', function () {
	camMan.getCanvas('container', function (canvas) {
        var ctx = canvas.getContext('2d');
        var imgData = ctx.getImageData(0, 0, canvas.width, canvas.height);

		 /** Manipulate array of pixel data -- imgData.data */

         ctx.putImageData(imgData)
    });
});
```

The callback passed to `camMan.getCanvas` is executed once per frame, allowing
you to manipulate each frame before it is drawn to the canvas. Using this
capability I was able to quickly create my Photobooth app using image filter
effects.

Here is an example filter which will invert the colors of each pixel:

```
var inverse = function inverse(imageData) {
    var data = imageData.data;
    for (var i = 0; i < data.length; i += 4) {
		data[i] = 255 - data[i];
		data[i + 1] = 255 - data[i + 1];
		data[i + 2] = 255 - data[i + 2];
    }
    return imageData;
};
```

And here is another that converts a video to greyscale by setting the color
components of each pixel to be the average value of all three color channels.


	var grayScale = function grayScale(imageData) {
		var data = imageData.data;
		for (var i = 0; i < data.length; i += 4) {
		   var red = data[i];
			var green = data[i + 1];
			var blue = data[i + 2];
			var average = (red + green + blue) / 3;
			data[i] = data[i + 1] = data[i + 2] = average;
		}
		return imageData;
	};

### Resources

 - [**MDN - WebRTC** -- *Taking Webcam Photos*][mdn]
 - [**John Robinson** -- *How You Can Build an HTML Photobooth App*][robinson]
 - [**GitHub** -- **`leemachin/say-cheese`**][leemachin]


[robinson]: http://www.storminthecastle.com/2013/05/07/how-you-can-build-an-html5-photobooth-app/
[leemachin]: https://github.com/leemachin/say-cheese
[repo]: https://github.com/CoryG89/CamMan.js
[demo]: http://coryg89.github.io/CamMan.js/example
[mdn]: https://developer.mozilla.org/en-US/docs/WebRTC/Taking_webcam_photos