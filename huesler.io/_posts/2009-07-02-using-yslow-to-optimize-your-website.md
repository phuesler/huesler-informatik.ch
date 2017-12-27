---
layout: post
title: Using YSlow to optimize your website
---

<p>In the last <a href="http://www.huesler-informatik.ch/2009/07/02/dont-just-throw-in-more-hardware-part-1/" title="Don't just throw in more hardware (Part 1) | huesler informatik">post</a> I've introduced simple techniques to improve the performance of a web application. In this post I will talk about performance optimization on the browser side. With a few easy steps, the pages will load a lot faster and you will reduce bandwidth consumption and load on your servers.</p>

<p>Yahoo! has released a great tool called <a href="http://developer.yahoo.com/yslow/" title="Yahoo! YSlow for Firebug">YSlow</a>. YSlow analyzes web pages and suggests ways to improve their performance based on a set of rules for high performance web pages. It is an addon for Firebug, which in turn is an add-on for Firefox.</p>
<p>
There are 22 rules defined in YSlow:
  <ol>
    <li>Minimize HTTP Requests</li>
    <li>Use a Content Delivery Network</li>
    <li>Add an Expires or a Cache-Control Header</li>
    <li>Gzip Components</li>
    <li>Put StyleSheets at the Top</li>
    <li>Put Scripts at the Bottom</li>
    <li>Avoid CSS Expressions</li>
    <li>Make JavaScript and CSS External</li>
    <li>Reduce DNS Lookups</li>
    <li>Minify JavaScript and CSS</li>
    <li>Avoid Redirects</li>
    <li>Remove Duplicate Scripts</li>
    <li>Configure ETags</li>
    <li>Make AJAX Cacheable</li>
    <li>Use GET for AJAX Requests</li>
    <li>Reduce the Number of DOM Elements</li>
    <li>No 404s</li>
    <li>Reduce Cookie Size</li>
    <li>Use Cookie-Free Domains for Components</li>
    <li>Avoid Filters</li>
    <li>Do Not Scale Images in HTML</li>
    <li>Make favicon.ico Small and Cacheable</li>
  </ol>
</p>
<p>Since I like to learn by example, I've created a simple, rather ugly web application called Cootweets. It will be some sort of a "geo tagged flickr image with a tweet attached to it"-thing. So throughout this blog post, I will try to optimize the start page of Cootweets to satisfy YSlow's requirements. I won't talk about every rule, just the ones that YSlow complained about. Here is what the start page looks like: :<br/>
/public/images/posts/="/public/images/posts/cootweets_home_post_format.jpg" alt="cootweets_home_post_format" title="cootweets_home_post_format" width="389" height="362"/>
</p>
<p>So lets get started and make the start page load faster.</p>
<h3>Make fewer HTTP requests (B)</h3>
<pre>
  This page has 6 external Javascript scripts. Try combining them into one.
  This page has 3 external stylesheets. Try combining them into one.
</pre>
<h4>What is this about?</h4>
<p>After a browser has received a response from your server, it parses its content and tries to render it. It downloads every resource included in the HTML like CSS, Javascript, Flash and images. That means, for each resource, the browser fires up a new HTTP request. So by combining files, we can reduce the number of requests by 7.</p>

<h4>Lets fix it</h4>
<p>Let's combine the CSS and Javascript. If you are running Ruby on Rails, you can simply tell the <a href="http://api.rubyonrails.org/classes/ActionView/Helpers/AssetTagHelper.html#M002004" title="Module: ActionView::Helpers::AssetTagHelper">asset tag helper</a> to combine the files by passing in the :cache option. There is also a plugin called <a href="http://synthesis.sbecker.net/pages/asset_packager" title="Scott Becker - AssetPackager â€“ JavaScript and CSS Asset Compression for Production Rails Apps">AssetPackager</a> which compresses the files too.</p>

<p>Giving it a second thought, I realize that I don't even need Javascript on the start page, it's just a habit to include the jQuery files, because I use them so frequently. So getting rid of it, is the best option at this point.</p>

<h3>Add Expires headers (F)</h3>
<p>
<pre>
  There are 7 static components without a far-future expiration date.

      * (no expires) http://localhost:3000/stylesheets/all.css?...
      * (no expires) http://localhost:3000/javascripts/all.js?...
      * (no expires) http://localhost:3000/images/compass.png
      * (no expires) http://localhost:3000/images/claim.png?...
      * (no expires) http://localhost:3000/images/camera.png?...
      * (no expires) http://localhost:3000/images/geotag.png?...
      * (no expires) http://localhost:3000/images/tweety.png?...  
</pre>
</p>
<h4>What is this about?</h4>
<p>Expires headers basically tell the browser how long it can cache a specific component. That avoids unnecessary HTTP requests on subsequent page views. That speeds up page load, saves bandwidth and reduces the  load on the servers. If you use a <a href="http://en.wikipedia.org/wiki/Content_delivery_network" title="Content delivery network - Wikipedia, the free encyclopedia">CDN</a>, setting the Expires headers will save you money.</p>

<h4>Lets fix it</h4>
<p>The implementation highly depends on your setup but the idea is the same. Tell your webserver to add a fare future Expires headers to static assets like CSS, Javascript and images. There is however one pitfall. What happens if you change a file without changing its name? The browser will probabely use the cached version. So we need to add some kind of versioning to our assets. With Cootweets we are in luck, because Rails appends a timestamp to the URLs created with the asset tag helper. If a file changes, its URL changes and the browser will discard the cached version.</p>

<p>Cootweets runs on Nginx and Passenger. Here is how I changed the Nginx configuration:<br/>
<script src="http://gist.github.com/142745.js"></script>
</p>
<p>
If you are running Apache, the solution is similar, but somehow I could not convince Apache only to cache assets that have a time stamp appended. I tried the same regular expression I used with Nginx, but then Apache just didn't set any Expires headers at all. This actually makes sense, because the directive is called FilesMatch and not RequestMatch. I have come up with a workaround using Apache variables. <br/>
<script src="http://gist.github.com/149535.js"></script>
However, this does not set the time dynamically. Well of course, we could alter the Apache configuration on each deploy to fix this but that seems cumbersome for such a little task. If someone knows how to do this dynamically with an Apache directive, please let me know.
</p>
<p>You can check if it works by looking at the HTTP headers returned from the webserver. YSlow offers a convenient way to do this but you can also use <a href="http://curl.haxx.se/" title="cURL and libcurl">curl</a> with the --head option to look at the headers.<br/>
<a href="http://www.huesler-informatik.ch/wp-content/uploads/2009/08/expire_headers.jpg" title="Expires headers in YSlow">/public/images/posts/="/public/images/posts/expire_headers_post_format.jpg" alt="expire_headers_post_format" title="expire_headers_post_format" width="389" height="212"/></a>
<br/>
<script src="http://gist.github.com/149521.js"></script>
<script src="http://gist.github.com/149522.js"></script>
</p>
<p>Looking at the browser logs, I have noticed something strange. Even though the Expires headers are set correctly, Safari and Firefox still requested the components at the server and got back a 304 Not modified. While this is an improvement, I prefer no requests over a few requests. It took me quiet some time to figure out that this only happens when an explicit reload is triggered in the browser.</p>

<h3>Compress components with GZip (D)</h3>
<p>
<pre>
  There are 3 plain text components that should be sent compressed

      * http://localhost:3000/
      * http://localhost:3000/stylesheets/base_packaged.css?...
      * http://localhost:3000/javascripts/base_packaged.js?...
</pre>
</p>
<p>You can save bandwidth by compressing HTML pages, CSS stylesheets and Javascript. Luckily, you don't have  to modify your application. The web server can take care of this. Here is the configuration for Nginx:<br/>
<script src="http://gist.github.com/149510.js"></script>
An here for Apache:<br/>
<script src="http://gist.github.com/149511.js"></script>
</p>
<h3>Results</h3>
<p>With a few simple changes, we have reduced the number of requests and the size of the data being transferred. Comparing an empty with a primed cache, I reduced the total number of requests from 8 down to 2 in my sample application. In terms of used bandwidth, the reduction is from 416.9K to 0.6K. For the sake of the example, I have included the Javascript, even though the page doesn't use any. There is also a CSS image that has no Expires header and therefore gets requested on every page load.<br/>
<a href="/public/images/posts/cache_statistics.jpg" title="cache statistics">
/public/images/posts/="/public/images/posts/cache_statistics-300x124.jpg" alt="cache_statistics" title="cache statistics" width="300" height="124" class="alignnone size-medium wp-image-104" /></a>
</p>
<p>
  YSlow is a very useful tool to quickly analyze the page load performance. It offers a rich set of information and tools comfortably integrated in Firefox. Google has recently released a similar tool called <a href="http://code.google.com/speed/page-speed/" title="Page Speed Home">Page Speed</a>. The cool thing about page speed is that it actually estimates the bandwitdh reduction that could be achieved, so make sure to check it out too.
</p>
