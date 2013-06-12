---
layout: post
title: "Release Open Source HTML5 Games on the Windows Store"
description: "Releasing an open source KineticJS game on the Windows Store"
category: "Experiences"
highlights: true
tags: ["KineticJS", "Open Source", "Windows Store", "Canvas", "HTML5", "Game Development"]
---
{% include JB/setup %}

I had a buddy in my game development course at Auburn who had just starting
working at Microsoft Campus Partner and was pushing a program they were running
for students in March called App Madness where they gave a $100 gift card to
students who submitted 'sufficiently unique' apps to the store during the
month.

I knew that you could use HTML5 and JavaScript to create apps and games in
Windows 8 and had already upgraded my Dell Studio XPS laptop to Windows 8 when
it came out, so I was totally down to aim for one of those gift cards. I had
also been curious how easily you could porting over to Windows 8 would be for
an existing browser based game built with one of the popular HTML5 Canvas
JavaScript frameworks such as [**EaselJS**][createjs] or
[**KineticJS**][kineticjs].

I had previously posted about my [**first game with KineticJS**][peg-game-post],
it turned out pretty well so I decided to try to port it over as a Windows 8 app
that could be submitted to the Windows 8 store for this App Madness challenge.
The game may look familiar if you've ever eaten at Cracker Barrel. The 
[**source is on GitHub**][repo] and you can also [**play the demo**][demo] in
your browser anytime.

Porting the game over was easy, I used the basic HTML5 template, created a
container div for my KineticJS stage and was all set. The only change I had to
make to get my JavaScript code working with WinJS was to replace my game over
message code which used a basic JavaScript alert:

	alert(msg);

This is not supported when using WinJS and `alert` will return as `undefined`.
In order to fix this you can replace your calls to `alert` with the following:

	var gameOverDialog = new Windows.UI.Popups.MessageDialog(msg);
	gameOverDialog.showAsync();

However, if you have multiple calls it would probably be better to create your
own alias to `alert` like so:

    var alert = function (msg) {
		var gameOverDialog = new Windows.UI.Popups.MessageDialog(msg);
		gameOverDialog.showAsync();
    };

I am not sure why WinJS doesn't just include aliases like the above to make it
easier for porting browser-based games to the Windows 8 platform. Would seem
like a no-brainer thing to do for me, however, I guess it's easy enough to add
the code above and all of your original `alert` calls should continue working
normally.

I created an App Package using Visual Studio 2012 and ran the new
Windows App Certification Kit. This is a piece of software which Microsoft
released in order to allow you to run which is supposed to tell you if there is
anything obviously wrong with your app package _before_ you submit it to the
Windows Store. However, I don't think it's a coincidence the acronym for this
thing is WACK. It's nice, except for the fact that it doesn't pick up on
everything it could. 

I tried to submit my app for all markets to get my app in
as many different countries as possible. The WACK passed, but then during actual
certification for the Store it failed because games cannot be submitted to some
markets without a rating certficiation. This is something that should be checked
by the automated WACK. Also at the end it will give you a link to the results at
the end as well as a link to submit the package to the store if it passes. If
you are runnning Chrome as your default browser then the entire WACK will
utterly crash when you click one of them. Don't worry everything is fine, your
package you need to upload is within your project folder under the directory
`AppPackages`. You just need to log into your 
[**Windows Development Dashboard**][dashboard] to upload it. However, you have
no way of knowing what happened or what to do next when it crashes.

In the end my game was [**listed in the Windows Store**][win-store-link], and 
it's taken about two days for each release that I've done since. Overall I'd say
it was a pretty painless process. Microsoft's Windows Store has the surprising
policy of OSI approved open source licenses trumping their standard license.
Because of these reasons I am hoping to see lots of open source browser based
HTML5 games ported to the Windows Store in the future.

[peg-game-post]: {{ BASE_PATH }}/technical/2013/06/06/recreating-the-peg-game-at-cracker-barrel-in-html5/
[repo]: https://github.com/CoryG89/PegGame
[demo]: http://coryg89.github.io/PegGame
[createjs]: http://createjs.com
[kineticjs]: http://kineticjs.com
[dashboard]: http://dev.windows.com
[win-store-link]: http://apps.microsoft.com/windows/en-us/app/winpegs/f488a6d2-41ef-4278-89db-be25d452cff6