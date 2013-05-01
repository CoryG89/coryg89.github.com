---
layout: page
title: Hello World!
tagline: Let's build something.
---
{% include JB/setup %}

My name is Cory Gross. I am a software developer graduating with my Bachelor's in Software Engineering from Auburn University in December of 2013. I am interested in working with a like-minded team to build good software that people actually use. I like Web/mobile development, games, networking, and technology in general. I have a passion for open source and love learning to use new tools. This site is hosted for free on GitHub's open source platform and was built using [**Jekyll Bootstrap**][1].
 
I enjoy the startup atmosphere and could definitely see myself working in the Silicon Valley area with a small team. However, I also think it would also be amazing to work at some of the larger tech companies that I have admired. Another passion of mine is games and I would love to develop them for a living if I landed a job with the right studio.

You can contact me anytime with questions about my experience or with information about a job. If you're a developer and have questions about any of my articles or projects please do not hesitate to contact me as well. I enjoy solving technical problems and love people being able to learn from things I've done in the past.
    
## Sample Posts

This blog contains sample posts which help stage pages and blog data. When you don't need the samples anymore just delete the `_posts/core-samples` folder.

    $ rm -rf _posts/core-samples


## Testing Highlighting

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

This theme is still unfinished. If you'd like to be added as a contributor, [please fork](http://github.com/plusjade/jekyll-bootstrap)! We need to clean up the themes, make theme usage guides with theme-specific markup examples.

[1]: http://github.com/plusjade/jekyll-bootstrap