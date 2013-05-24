---
layout: page
title: Hello World!
listingTitle: Home
tagline: Let's build something.
---
{% include JB/setup %}

My name is Cory Gross. I am a software developer graduating with my Bachelor's
in Software Engineering from Auburn University in December of 2013. I am
interested in working with a like-minded team to build good software that
people actually use. I am also interested in things like: open source
technologies, standards, and platforms such as the Web, mobile platforms, game
development, and technology in general. I am the type that is interested in
technology for it's own sake, but I also want to make an impact with my work.
I love learning to use new tools and technology and I have a passion for open
source.
 
I enjoy the startup atmosphere and could definitely see myself working in the
Silicon Valley area with a small team. However, I also think it would also be
amazing to work at some of the larger tech companies that I have admired.
Another passion of mine is games and I would love to develop them for a living
if I landed a job with the right studio.

You can contact me anytime with questions about my experience or with
information about a job. If you're a developer and have questions about any of
my articles or projects please do not hesitate to contact me as well. I enjoy
solving technical problems and love people being able to learn from things
I've done in the past.

Here's a sample "posts list".

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>

[1]: https://github.com/mojombo/jekyll
[2]: https://gtihub.com/twitter/bootstrap