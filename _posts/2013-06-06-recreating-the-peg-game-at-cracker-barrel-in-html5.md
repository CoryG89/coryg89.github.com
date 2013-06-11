---
layout: post
title: "Recreating the Peg Game at Cracker Barrel in HTML5"
listTitle: "Peg Game"
description: "Detailing my recreation of the triangular table top peg game known as Peg Solitaire."
group: "Project"
category: "Technical"
tags: ["HTML5", "Canvas", "KineticJS", "Game Development"]
highlights: true
---
{% include JB/setup %}

This past New Year's Eve going into 2013, me and my girlfriend went home to see
our parents and we went out to eat with her parents at Cracker Barrel. While we
were waiting on our food, I got to playing the little triangular wooden peg
game that they have on the tables. While we were there I thought a bit about
how I might implement the game.

For those of you who still have no idea what game I am talking about. I did a 
little digging and found out that it is actually a variant of an older game 
known as [**Peg Solitaire**][game-wiki], or just Solitaire in the UK (Patience
is the name used for the card game most Americans call Solitaire). You can also
just go ahead and see the final product and checkout the [**final game**][game]
if you want.

Here is a picture of the Cracker Barrel's version of the game

[![Cracker Barrel Peg Solitaire][cb-game-pic]][cb-game-pic]

The day after we ate at Cracker Barrel I went to my parent's house to spend time
with them, while my girlfriend stayed with her family. I got pretty bored so I
decided that I might try creating a playable demo of the game. I had been
looking at [**KineticJS**][kinetic], a JavaScript library written by Eric
Rowell. Rowell is also the author of [*HTML5 Canvas Cookbook*][cookbook]. The
KineticJS website says that it is

> an HTML5 Canvas JavaScript framework that enables high performance animations,
> transitions, node nesting, layering, filtering, caching, event handling for
> desktop and mobile applications, and much more.

After using it, I tend to feel the above description is pretty accurate. 
Basically it allows you to work with the HTML5 Canvas API much more efficiently
with much less effort, while giving you a whole bunch of other goodies in the
process. For me working with this JavaScript library is much more enjoyable than
working with the HTML5 Canvas API directly. It also takes care of all the
performance workarounds like caching and such which you must implement youself
if you use the straight Canvas API.

I wanted a simple project that I could use as an excuse to try out KineticJS
and I felt this little peg game would be perfect. I would create the game using
KineticJS and only a single texture for the wood.

[![Texture][wood-texture]][wood-texture]

So I started out thinking about this by numbering each of the holes on the
board and I arrived at a game board defined by the following fifteen positions
(0-14).
 
                  14
                13  12
              11  10  09 
            08  07  06  05
          04  03  02  01  00

I thought about making a graph with each hole being a node, but I noticed
something I hadn't before at Cracker Barrel. From each position on the board,
except for 3 of them, there are only two possible jumps you can make. The other
3 positions `02`, `09`, and `11` each have four possible jumps from the given
position.

With this is mind I was able to create a very small lookup table for each
position on the board, where for each position the table had an entry which
contained a list of ordered pairs representing the possible moves for that
board position. The first component of the ordered pair represented the 'jump'
position and the second component represented the 'land' position.

Here is how I did it in JavaSript:

	var MoveTable = [];

	/** Row one (positions 0-4), bottom row */
	MoveTable[0] = [{jumpPos:1, landPos:2}, {jumpPos:5, landPos:9}];
	MoveTable[1] = [{jumpPos:2, landPos:3}, {jumpPos:6, landPos:10}];
	MoveTable[2] = [{jumpPos:1, landPos:0}, {jumpPos:3, landPos:4},
					{jumpPos:6, landPos:9}, {jumpPos:7, landPos:11}];
	MoveTable[3] = [{jumpPos:2, landPos:1}, {jumpPos:7, landPos:10}];
	MoveTable[4] = [{jumpPos:3, landPos:2}, {jumpPos:8, landPos:11}];

	/** Row two (positions 5-8) */
	MoveTable[5] = [{jumpPos:6, landPos:7}, {jumpPos:9,  landPos:12}];
	MoveTable[6] = [{jumpPos:7, landPos:8}, {jumpPos:10, landPos:13}];
	MoveTable[7] = [{jumpPos:6, landPos:5}, {jumpPos:10, landPos:12}];
	MoveTable[8] = [{jumpPos:7, landPos:6}, {jumpPos:11, landPos:13}];

	/** Row three (positions 9-11) */
	MoveTable[9] =  [{jumpPos:5,  landPos:0},  {jumpPos:6,  landPos:2}, 
					 {jumpPos:10, landPos:11}, {jumpPos:12, landPos:14}];
	MoveTable[10] = [{jumpPos:6,  landPos:1},  {jumpPos:7,  landPos:3}];
	MoveTable[11] = [{jumpPos:7,  landPos:2},  {jumpPos:8,  landPos:4}, 
					 {jumpPos:10, landPos:9},  {jumpPos:13, landPos:14}];

	/** Row four (positions 12 and 13) */
	MoveTable[12] = [{jumpPos:9,  landPos:5}, {jumpPos:10, landPos:7}];
	MoveTable[13] = [{jumpPos:10, landPos:6}, {jumpPos:11, landPos:8}];

	/** Row five (position 14), top row */
	MoveTable[14] = [{jumpPos:12, landPos:9}, {jumpPos:13, landPos:11}];

Looking at the above table you can see that for entry for position 0, 
`MoveTable[0]`, has a list of two possible moves from its position on the
board. From position `0`, you can jump over `1`, landing on `2`;  or you can
jump over `5`, landing on `9`.

Using this data structure, you can easily implement a game which is able to
calculate a list of current valid moves based on which holes on the board are
occupied or not. Basically the algorithm goes:

 - Determine which positions on the board are occupied.
 - Consider each entry in the `MoveTable` for each occupied position.
 - Create a list of current possible moves for each occupied position by
   deciding which moves from the `MoveTable` entry are valid.
 - A move is considered valid if and only if the following two conditions are
   satisfied.
   1. The `jumpPos` position must be occupied.
   2. The `landPos` position must *not* be occupied.
 - Activate all pegs with at least one valid move and highlight the move for
   the player.

The full commented source code is [**available on GitHub**][repo] and there
is also a [**playable demo**][game] you can check out as well. If you study the
source code keep in mind that I was learning to use KineticJS while I was
creating this and was also just learning JavaScript and getting back into web
technologies in general. My implementation would be a lot cleaner if I was to
write this from scratch today.. I have learned a lot in 6 months, as JavaScript
has been my primary focus. Maybe one day I'll rewrite it when I have nothing
better to do. Still I figure someone might want to check it out. Please just
don't take this as a prime example of my software engineering skills.

On another note, I know there are probably way more efficient ways to implement
this than using the method I outline above. You could probably implement this
using a graph with nodes for each board position. If anyone has any ideas or has
done something similar to this before I'd be interested in hearing from you.

[game]: {{ BASE_PATH }}/PegGame
[cb-game-pic]: {{ ASSET_PATH }}img/crackerbarrel.jpg
[wood-texture]: {{ ASSET_PATH }}img/wood-texture.jpg
[repo]: https://github.com/CoryG89/PegGame
[kinetic]: http://kineticjs.com
[cookbook]: http://www.amazon.com/HTML5-Canvas-Cookbook-Eric-Rowell/dp/1849691363
[game-wiki]: http://en.wikipedia.org/wiki/Peg_solitaire