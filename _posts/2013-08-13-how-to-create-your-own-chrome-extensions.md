---
layout: post
title: "How To Create Your Own Chrome Extensions"
description: "Simple guide to extending your browser with Chrome extensions"
category: "Technical"
highlights: true
tags: ["Chrome", "Chrome Extensions"]
---
{% include JB/setup %}

I really like the Chrome browser. It wasn't until recently that I looked into
creating extensions and was surprised by how simple it was. If you know basic
HTML, CSS, JavaScript then you know all you need to be able to extend your
Chrome browser in multitudes of ways. I decided to write up a simple guide
explaining some of the basic ways you can easily extend Chrome. This is going
to be a quick crash course to give you a general idea of what all you can easily
do with Chrome extensions. Please let me know if you find any errors in the
following. I do not claim to be an expert in any of the following topics.


### Getting Started With Extensions

In order to get started creating a new extension all you need to do is create
a new root directory for your extension. The only strictly required file is
`manifest.json`. Also recommended are several images which are used as icons
and at least one JavaScript file providing some functionality. Also commonly 
included are HTML documents, stylesheets, additional images, and other
resources.


#### The Manifest

Every extension must contain a file in the root directory with filename
`manifest.json`. The manifest declares the name, version, permissions,
configuration options, and other meta data associated with the extension.
Manifest version 1 was deprecated in Chrome 18 and support will be phased out
according to this [schedule][man-schedule]. If you are using an older extensions
manifest as a guide you will want to make sure it includes the entry
`"manifest_version": 2`.

Google has published a list of all the
[fields supported in manifest version 2.][manifest-files].


#### Background Pages

Most extensions will have an entry in the `manfiest.json` which looks like the
following:


<pre><code class="text json">{
  "background": {
    "scripts": ["index.js", "other.js"]
  }
}</code></pre>

This entry specifies two scripts which will be loaded and kept in memory
running in the background. These scripts will run in the context of the
[background page][background-pages], a generated page running in the extension's
process which exists for the lifetime of the extension. The background page can
be used to maintain state and acts as a controller for the rest of the
extensions UI pages. If you need to declare some markup for use with your 
background page you may do so by specifying by passing in a HTML document 
filename to the `page` option.


#### Event Pages

Background pages are by default persistent, they are loaded in when the
extension starts and are kept in memory constantly. This is sometimes needed if
the background page is used to maintain some state that must be constantly
accessible to the rest of the extension. You should use 
[event pages][event-pages] instead whenever possible. An event page is simply
a background page for an extension that includes a `"persistent": false` entry.
This tells chrome not to keep the background page loaded in memory all the time.
Instead, an event page initially is run, but then once the script goes idle it
is unloaded and is only loaded again when it's needed (in response to some
event).

This is all you need to know to start adding some functionality.


### Interaction

Interacting with the browser and providing functionality for your extension is
made possibly by leveraging one of many APIs available.


#### chrome.* APIs

Chrome apps and extensions enjoy access to the [chrome.* APIs][chrome-apis]. 
These allow you to manipulate the browser in various different ways. These APIs
will typically be leveraged from your background scripts. Here are some of the
more useful APIs I have looked over:

  * [**`chrome.tabs`**][chrome.tabs] - Create, update, remove, query, and
manipulate the browser tabs.
  * [**`chrome.history`**][chrome.history] - Access the user's browsing history.
  * [**`chrome.bookmarks`**][chrome.history] - Add, edit, remove, and search the
user's bookmarks.
  * [**`chrome.events`**][chrome.events] - Listen for and manage events raised
in the browser.
  * [**`chrome.commands`**][chrome.commands] - Override or add keyboard
commands.
  * [**`chrome.contextMenus`**][chrome.contextMenus] - Add items to the context
menu activated under a given set of contexts.
  * [**`chrome.omnibox`**][chrome.omnibox] - Add a keyword to the omnibox
(address bar) allowing the user to issue a command to the extension on
activation.


#### Other APIs

Chrome apps and extensions can also make use of [other APIs][other-apis]
including new HTML5 APIs such as local storage, geolocation, cache, and canvas.
You can also use the regular JavaScript and webkit APIs as well.

#### Declaring Permissions

Some of the functionality in certain Chrome APIs will not be available unless
the permission associated with the given API functionality is not declared in
the `manifest.json` file. Declare them by specifying the `permissions` field,
setting it's value to an array of permission names or
[match patterns][match-patterns]:

<pre><code class="text json">{
  "permissions": [
    "contextMenus", 
    "tabs",

    "https://google.com/*",
    "https://developer.mozilla.org/*"
  ]
}</code></pre>

In the above declaration the first two strings in the array are permission
names granting permissions for the `chrome.contextMenus` and `chrome.tabs` APIs
respectively. The last two strings in the array are match patterns which will
match URLS beginning with `https://google.com/` and 
`https://developer.mozilla.org/`

### User Interface

Chrome extensions are restricted to a limited UI surface, but several options
for providing different forms of an interface depending on the needs of the
extension.


#### Browser Actions

[Browser actions][browser-actions] allow you to provide a button in the top
right hand corner of the browser with your extensions `16 x 16` icon. Browser
actions should be used whenever the action to be taken applies to the browser
globally and not a particular page. To use a browser action in your extension
you must declare it in your extension's `manifest.json` by specifying the
`browser_action` field:

<pre><code class="text json">{
  "browser_action": {
    "default_icon": {                    
      "19": "images/icon19.png",
      "38": "images/icon38.png"
    },
    "default_title": "tooltip text here",
    "default_popup": "popup.html"
  }
}</code></pre>

A browser action UI can have an icon, a tooltip, a badge, and a popup. A badge
is a small amount of text that may be dynmaically overlayed onto the icon of the
browser action. You may also leverage the 
[**`chrome.browserAction`**][browser-actions] API in order to respond to
events associated with the browser action.


#### Page Actions

[Page actions][page-actions] allow you to add a button in the right hand side of 
the Omnibox (address bar). This is similar to browser actions except that page
actions are meant to apply only to the particular active page. Page actions
must be declared in the `manfiest.json` using the entry `page_action`, the
fields are supported for `page_action` as `browser_action`. Page actions can
be manipulated using the [**`chrome.pageAction`**][page-actions] API and
can be dynamically shown and hidden on particular tabs. Page actions can also
have an icon, tooltip, and a popup. However, page actions cannot have a badge.


#### Context Menus

[Context menus][chrome.contextMenus] are another way to provide a user interface
for the user to interact with your extension. The context menu in Chrome is a
menu that is activated on right click which is different based on the context
in which it was activated. The [**`chrome.contextMenus`**][chrome.contextMenus]
API allows us to add menu items to the context menu for different activation
contexts. To use the API you must declare the associated `contextMenus`
permission in your extension's `manifest.json`. 

The available activation contexts for extensions are: `all`, `page`, `frame`,
`selection`, `link`, `editable`, `image`, `video`, and `audio`. The following
example requires the `contextMenus` and `tabs` permissions and will add a root
context menu item for the extension, and then a sub menu item which will
duplicate the current page in a new tab.

<pre><code class="text javascript">var root = chrome.contextMenus.create({
    title: 'MyExtension',
    contexts: ['page']
}, function () {
    var subMenu = chrome.contextMenus.create({
        title: 'Duplicate Tab'
        contexts: ['page'],
        parentId: root,
        onclick: function (evt) {
            chrome.tabs.create({ url: evt.pageUrl })
        }
    });
});</code></pre>


#### Omnibox

The Omnibox is what Chrome calls it's address bar / search bar. It makes another
UI surface available to extensions through the 
[**`chrome.omnibox`**][chrome.omnibox] API which allows an extension to respond
to a particular keyword being entered into the omnibox. The keyword is declared
in the extension's `manifest.json` like so:

<pre><code class="text json">{
  "omnibox": {
    "keyword": "ext-"
  }
}</code></pre>

The following will register a keyword for the extension which will activate
when the user enters `ext-` followed by the the `SPACE` or `TAB` key. Omnibox
keywords are declarative via `manifest.json` only in the sense that they
cannot be changed at runtime via javascript. The user can change the keyword by
right clicking the omnibox and clicking 'Edit search engines...'. Omnibox
keywords are case insensitive. It is not possible for a single extension to
register multiple keywords.

The `chrome.omnibox` API allows you to add event handlers to respond to input
being changed or entered after the keyword has been activated.

#### Options Pages

[Options pages][options-pages] are a common UI element made available to
extensions are which can be declared in `manfiest.json`. This options page is
opened whenever a user clicks `Options` next to your extension's listing on
their `chrome://extensions` page. This is often combined with a storage API in
order to store persistent configurable options for the user. It is also possible
to open the options page from a script using the
[**`chrome.tabs`**][chrome.tabs] API.

#### Override Pages

[Override pages][override-pages] allow you to specify a page which will replace
any one of the following pages (a single extension cannot replace more than
one):

 - **Bookmark Manager** - Page that appears when user enters 
`chrome://bookmarks`. Can also be opened from Chrome's menu.
 - **History** - Page that appears when user enters `chrome://history`. Can also
be opened from Chrome's menu.
 - **New Tab** - The page that appears when a new tab is first created or when
 the user enters `chrome://newtab`

These replacement pages are specified declaratively in the extension's
`manifest.json` file by specifying the `chrome_url_overrides` field like so:

<pre><code class="text json">{
  "chrome_url_overrides": {
    "bookmarks": "newBookmarkManager.html",
  }
}</code></pre>

Instead of using `bookmarks` in the above declaration, you could also use either
`history` or `newtab`.


#### Content Scripts

[Content scripts][content-scripts] are scripts associated with your extension 
that run in the context of web pages. Content scripts are meant to give you
access to the DOM for the web pages for which they match. You can define an
array of content script entries in `manifest.json` by specifying the
`content_scripts` field like so:

<pre><code class="text json">{
  "content_scripts": [
    {
      "matches": ["http://www.google.com/*",
      "css": ["custom-google-styles.css"],
      "js": ["custom-google-script-1.js", "custom-google-script-2.js"]
    },
    {
      "matches": ["http://*"],
      "css": ["global-styles.css"],
      "js": ["global-script.js"]
    }
  ]
}</code></pre>

You can also use [programmatic injection][programmatic-injection] to dynmically
inject JavaScript or CSS into a page using the `chrome.tabs` API.

Content scripts are limited by the following restrictions:

 * Cannot use `chrome.*` APIs (except for parts of `chrome.extension`).
 * Use variables or functions defined by their extension's scripts.
 * Use variables or functions defined by web page's scripts.
 * Use variables or functions defined by other content scripts

Content scripts may indirectly interact with their extension's scripts (and thus
indirectly with the `chrome.*` APIs) through [message passing][message-passing].


### Some Examples

Chrome has a great [Sample Extensions][sample-extensions] page allowing you to
search through their sample applications which cover use of each of the APIs
available. You can also read my other [post][mdnjump-post] about my first
extension [MDNJump][mdnjump]. It's also available on GitHub at
[CoryG89/MDNJump][mdnjump-repo]

[manifest-files]: http://developer.chrome.com/extensions/manifest.html
[sample-extensions]: http://developer.chrome.com/extensions/samples.html
[background-pages]: http://developer.chrome.com/extensions/background_pages.html
[event-pages]: http://developer.chrome.com/extensions/event_pages.html
[options-pages]: http://developer.chrome.com/extensions/options.html
[override-pages]: http://developer.chrome.com/extensions/override.html

[browser-actions]: http://developer.chrome.com/extensions/browserAction.html
[page-actions]: http://developer.chrome.com/extensions/pageAction.html

[chrome-apis]: http://developer.chrome.com/extensions/api_index.html
[chrome.tabs]: http://developer.chrome.com/extensions/tabs.html
[chrome.history]: http://developer.chrome.com/extensions/downloads.html
[chrome.bookmarks]: http://developer.chrome.com/extensions/bookmarks.html
[chrome.events]: http://developer.chrome.com/extensions/events.html
[chrome.commands]: http://developer.chrome.com/extensions/commands.html
[chrome.contextMenus]: http://developer.chrome.com/extensions/contextMenus.html
[chrome.omnibox]: http://developer.chrome.com/extensions/omnibox.html
[permissions]: http://developer.chrome.com/extensions/declare_permissions.html

[other-apis]: http://developer.chrome.com/extensions/api_index.htmll

[content-scripts]: http://developer.chrome.com/extensions/content_scripts.html
[programmatic-injection]: http://developer.chrome.com/extensions/content_scripts.html#pi
[message-passing]: http://developer.chrome.com/extensions/messaging.html
[match-patterns]: http://developer.chrome.com/extensions/match_patterns.html

[mdnjump]: https://chrome.google.com/webstore/detail/mdnjump/mfjekjhknpjgpgchoghlemhgchpmkiha?hl=en-US
[mdnjump-post]: ../../14/mdnjump-fastest-way-to-reference-mdn
[mdnjump-repo]: https://github.com/CoryG89/MDNJump

[man-schedule]: http://developer.chrome.com/extensions/manifestVersion.html#manifest-v1-support-schedule
