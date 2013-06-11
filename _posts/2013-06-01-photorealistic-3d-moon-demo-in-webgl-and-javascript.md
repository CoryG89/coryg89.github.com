---
layout: post
title: "Photorealistic 3D Moon Demo in WebGL and JavaScript"
listTitle: "WebGL Moon Demo"
displayTitle: "3D Moon Demo in WebGL and JavaScript"
description: "Creating a Photorealistic 3D Moon Demo in WebGL and JavaScript using Three.JS and GLSL shaders."
group: "Project"
category: "Technical"
highlights: true
tags: ["WebGL", "JavaScript", "3D", "HTML5", "Three.JS"]
---
{% include JB/setup %}

[![Moon Demo Screenshot][screen1]][screen1]

### Introduction

A while back I wanted to learn a bit about GLSL and how to write shaders. I
already had some experience with [**Three.JS**][three.js] and using its WebGL
renderer and I knew that it allowed custom shaders to be utilized through its
`ShaderMaterial` object. I read up a bit on GLSL shaders and how,
they worked. One tutorial I followed to write my first basic shader was 
[_An Introduction to Shaders_][html5rocks] written by Paul Lewis.

After I was able to recreate the shaders in the tutorial and apply them to a
mesh in a Three.JS scene, I wanted to do something a little more involved. I
wondered about applying lighting per pixel using GLSL shaders. I decided I could
try to make a 3D demo of the Moon and use lighting shaders to simulate the Moon
changing phases. I wanted my demo to be as photorealistic as possible.

You can check out the [**live demo**][demo], or 
[**view the source code on GitHub**][repo] to check out the result.

### The Hunt

In order to create a photorealistic demo I set out to find the highest quality
public domain maps of the Moon available. I found out that there is good data
published through the Map a Planet initiative and available on the
[**USGS PDS site**][USGS]. The best data we have for our Moon was actually
gathered by the [**Clementine spacecraft**][Clementine]. It is possible to
process a greyscale image of the entire surface of our Moon using this data,
and as far as I know the best maps of the lunar surface we have today were
derived from this data. I was delighted to know that all this great data
available from USGS is public domain.

The best map I could find was processed by [**Jens Meyer**][Jens Meyer] and
apparently also darkened up by [**Steve Albers**][Steve Albers]. It and other
high quality maps can be found on Steve Albers homepage and available free for
personal non-commercial use. I say this map is the _best_ in the sense that it
has the highest resolution and detail compared to other maps I could find.

The original resolution of the map found on Steve Albers' homepage is
`8192x4096`. I am using a version scaled down in both width and height by
50% giving a texture at resolution `4096x2048`. I find this size suitable for
my demo.

My scaled down version is shown here:

[![Scaled Moon Map][scaled_map]][scaled_map]

### Just Need A Few More Things

After this I generated 6 randomized subtle star patterns in order to create a
a starry skybox for my scene. I also used the [NVIDIA Normal Map Filter][nvidia]
in order to generate a normal map for the Moon map.

[![Scaled Moon Normal Map][scaled_normal]][scaled_normal]

### The Shaders

Here is the vertex shader which creates a Tangent-Binormal-Normal matrix for
each vertex, passing it to the fragment shader.

```
attribute vec4 tangent;
        
uniform vec2 uvScale;
uniform vec3 lightPosition;

varying vec2 vUv;
varying mat3 tbn;
varying vec3 vLightVector;

void main() {
    vUv = uvScale * uv;
            
    // Create the Tangent-Binormal-Normal Matrix used for transforming
    // coordinates from object space to tangent space
    vec3 vNormal = normalize(normalMatrix * normal);
    vec3 vTangent = normalize( normalMatrix * tangent.xyz );
    vec3 vBinormal = normalize(cross( vNormal, vTangent ) * tangent.w);
    tbn = mat3(vTangent, vBinormal, vNormal);
        
    // Calculate the Vertex-to-Light Vector 
    vec4 lightVector = viewMatrix * vec4(lightPosition, 1.0);
    vec4 modelViewPosition = modelViewMatrix * vec4(position, 1.0);
    vLightVector = normalize(lightVector.xyz - modelViewPosition.xyz);
    
    gl_Position = projectionMatrix * modelViewMatrix * vec4(position, 1.0);
}
```

The frament shader is passed the TBN matrix for each vertex and uses it to
transform the vectors from the normal map to tangent space.

```
uniform sampler2D textureMap;
uniform sampler2D normalMap;     
varying vec2 vUv;
varying mat3 tbn;
varying vec3 vLightVector;

void main() {
	// Transform texture coordinate of normal map to a range (-1.0, 1.0)
	vec3 normalCoordinate = texture2D(normalMap, vUv).xyz * 2.0 - 1.0;

	// Transform the normal vector in the RGB channels to tangent space
	vec3 normal = normalize(tbn * normalCoordinate.rgb);

	// Intensity calculated as dot of normal and vertex-light vector
	float intensity = max(0.07, dot(normal, vLightVector));

	// Adjustments to alpha and intensity per color channel may be made
	vec4 lighting = vec4(intensity, intensity, intensity, 1.0);

	// Final color is calculated with the lighting applied
	gl_FragColor = texture2D(textureMap, vUv) * lighting;
}
```

[screen1]: https://raw.github.com/CoryG89/MoonDemo/master/img/screens/screen1.png

[skybox1]: https://raw.github.com/CoryG89/MoonDemo/master/img/starfield/back.png
[skybox2]: https://raw.github.com/CoryG89/MoonDemo/master/img/starfield/front.png
[skybox3]: https://raw.github.com/CoryG89/MoonDemo/master/img/starfield/left.png
[skybox4]: https://raw.github.com/CoryG89/MoonDemo/master/img/starfield/right.png
[skybox5]: https://raw.github.com/CoryG89/MoonDemo/master/img/starfield/top.png
[skybox6]: https://raw.github.com/CoryG89/MoonDemo/master/img/starfield/bottom.png

[repo]: http://github.com/CoryG89/MoonDemo
[demo]: http://coryg89.github.io/MoonDemo
[scaled_map]: https://raw.github.com/CoryG89/MoonDemo/master/img/maps/moon.jpg
[scaled_normal]: https://raw.github.com/CoryG89/MoonDemo/master/img/maps/normal.jpg
[three.js]: http://mrdoob.github.io/three.js
[html5rocks]: http://www.html5rocks.com/en/tutorials/webgl/shaders/
[usgs]: http://pdsmaps.wr.usgs.gov/PDS/public/explorer/html/mmfront.htm
[Steve Albers]: http://laps.noaa.gov/albers/sos/sos.html
[Jens Meyer]: http://home.arcor.de/jimpage/earth.html
[Clementine]: http://en.wikipedia.org/wiki/Clementine_(spacecraft)
[chrome]: https://google.com/chrome
[nvidia]: https://developer.nvidia.com/nvidia-texture-tools-adobe-photoshop