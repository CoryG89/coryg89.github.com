---
layout: default
title: DevBlog of Cory Gross
---
{% include JB/setup %}

# Hello World!

My name is Cory Gross. I am a software developer graduating in December 2013
from Auburn University. I am interested in building great software that people
use, web and mobile development, games, and technology in general. I have a
passion for open source and love learning to use new tools. This site is hosted
for free on GitHub Pages and was built using [**Jekyll Bootstrap**][1] source
platform.
    
    title : My Blog =)
    
    author :
      name : Name Lastname
      email : blah@email.test
      github : username
      twitter : username

The theme should reference these variables whenever needed.
    
## Sample Posts

This blog contains sample posts which help stage pages and blog data.
When you don't need the samples anymore just delete the `_posts/core-samples` folder.

    $ rm -rf _posts/core-samples


## Testing Pygments

	function () {
		return Math.cos(0.5) + Math.sin(0.3)
	}


Here's a sample "posts list".

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

## To-Do

This theme is still unfinished. If you'd like to be added as a contributor, [please fork](http://github.com/plusjade/jekyll-bootstrap)!
We need to clean up the themes, make theme usage guides with theme-specific markup examples.

[1]: http://github.com/plusjade/jekyll-bootstrap