---
id: 1227
title: How to download huge folder from Dropbox
date: 2015-10-14T15:53:49+00:00
author: latobcode
layout: post
guid: http://code.jamming.com.ua/?p=1227
permalink: /how-to-download-folder-from-dropbox/
categories:
  - Programming
tags:
  - download
  - dropbox
  - javascript
  - parse
  - wget
---
If you face a problem to download folder from Dropbox which contains tons of files, no known browser extension can help you. Dropbox moves each file download to it&#8217;s separate page and you can&#8217;t do it directly.

When I faced this problem I knew I would need to create my own solution and quick googling just confirmed that.

I opened javascript console and extracted all links from the folder. Then I replaced &#8220;dl=0&#8221; to &#8220;dl=1&#8221; to get actual download link.

<pre><code class="language-javascript">var links = document.querySelectorAll("div.filename a")
var processed = Array.prototype.map.call(links, 
  function(link) { 
    return link["href"].replace("dl=0", "dl=1"); 
})
console.log(processed.join("\n"))</code></pre>

After I copied those to file _links\_to\_download_. If your _processed_ array is too big, you can print it to console by chunks, using _<code class="language-">slice(start, end)</code>_ method from Javascript. Now the problem is to download them.

I came up with _wget_ for such problem. Linux and OS X users should have wget available (OS X users can install it via e.g. homebrew). Windows users have to download it separately, install and add to the _PATH_ environmental variable. Additionaly I used _&#8211;trust-server-names_ and _&#8211;content-disposition_ parameters to save real filenames instead of dropbox hashed url. Then I faced a problem that it fails to download a file on first request and request timeout is quite big so I&#8217;ve set it to 5 seconds. Now it makes several timed-out requests, but they quickly resolve to the successful one.

<pre><code class="language-">wget --content-disposition --trust-server-names 
  --timeout=5 -i links_to_download</code></pre>

Also in order to download via https in Windows you probably will need to use &#8220;_&#8211;no-check-certificate_&#8220;.