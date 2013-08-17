---
layout: post
title: "MDNJump: Fastest Way To Reference MDN"
description: "Chrome extension which allows you to jump directly to the documentation you need from the Mozilla Developer Network"
category: "Technical"
highlights: true
tags: ["Chrome", "Chrome Extensions"]
---
{% include JB/setup %}

[MDNJump][mdnjump] is my first Chrome extension. The Mozilla Developer Network
([MDN][mdn]) is generally my favorite reference for front-end devleopment so I
created MDNJump in order to more easily reference the source for documentation. 
MDNJump was also an excuse to quickly learn about Chrome extensions and it is
available on GitHub at [CoryG89/MDNJump][mdnjump-repo]. It was only a few days
ago I started reading through some of the documentation for the different
Chrome APIs for extensions, but I've already have envisioned *and* implemented
an extension that is useful for my in-browser workflow. This is partly because
Google has made it so extending Chrome is not much harder than doing basic web
development with HTML markup and JavaScript. Accessing the Chrome APIs is just
like working with any other JavaScript API. It's absolutely great. They also
have [good documentation][docs] available. I wrote a quick
[overview guide][guide-post] for creating extensions, recording what I've
learned. It covers many of the major architectural aspects of extensions and
goes over most useful and most commonly used APIs available.

#### Usage and Interface

MDNJump extends Chrome in two ways:

  - **Omnibox keyword** - Chrome's address/search bar is called the Omnibox.
       By default, the registered keyword for MDNJump is `mdn-` which, when
       entered in the omnibox, followed by either the `TAB` or `SPACE` key,
       activates MDNJump in the Omnibox. After MDNJump has been activated
       you may enter a query and when you press enter you will jump directly to
       the best MDN reference documentation for that query.

  - **Context Menu** - MDNJump also adds an entry to the right-click
       context menu when the menu is activated on a text selection. Highlight
       some text on a page such as `window.location` or `string` and
       jump directly to great documentation.

#### What Makes It So Special?

Nothing really. However, I don't know of a faster way to look something up
on MDN and that was the goal. If you search the Chrome Webstore for `MDN` the
only other extension you get is MDNSearch. Like MDNJump it registers an omnibox
keyword, but when used it brings you to a search results page instead of
directly to what you were looking for.

#### What Does MDNJump Do Different?

I was dissatisifed with extensions such as MDNSearch mentioned above. It's a
good extension that does what it sets out to, but I wanted to jump directly
to the top reference for my particular query. If you give MDNSearch the query
`window.location` it will load the following search URL:

<pre><code class="text bash">https://developer.mozilla.org/search?q=window.location</code></pre>

On the otherhand, instead of loading the search results page, MDNJump will send
you directly to the top documentation for that query instead:

<pre><code class="text bash">https://developer.mozilla.org/en-US/docs/Web/API/window.location</code></pre>

#### How Does It Work?

MDNJump works by leveraging Google's Search API and it's
*I'm Feeling Lucky* feature. Instead of using the Mozilla URL, we instead
use a Google Search URL with the `btnI` field specified:

<pre><code class="text bash">https://www.google.com/search?btnI&amp;q=site:https://developer.mozilla.org+%s</code></pre>

The above URL pattern will perform a Google Search for query `%s`, restricted 
to the given domain `https://developer.mozilla.org`. Because the `btnI` field is
specified, it will activate *I'm Feeling Lucky* and automatically redirect us to
the top result.

MDNJump uses this technique in order to jump directly to the best MDN reference
for your queries.

<pre><code class="text javascript">/** Google query URL with btnI key activates I'm Feeling Lucky */
var googleUrl = 'https://google.com/search?btnI&amp;q=';
var mdnUrl = 'https://developer.mozilla.org';

/** Returns a Google query URL using I'm Feeling Lucky given an unformatted
    query and optional domain string to restict the search to */
function getLucky (query, site) {
    site = site ? ['site:', site, '+'].join('') : '';
    return [googleUrl, site, query].join('');
}

chrome.omnibox.onInputEntered.addListener(function (query) {
    var luckyUrl = getLucky(query, mdnUrl);
    chrome.tabs.update({ url: luckyUrl });
});</code></pre>

[mdnjump]: https://chrome.google.com/webstore/detail/mdnjump/mfjekjhknpjgpgchoghlemhgchpmkiha?hl=en-US
[mdnjump-repo]: https://github.com/CoryG89/MDNJump
[guide-post]: ../../13/how-to-create-your-own-chrome-extensions
[docs]: http://developer.chrome.com/extensions/overview.html
[mdn]: https://developer.mozilla.org
