---
layout: post
title: Quicktip -  Access .plist files with RubyCocoa
---

<p>As part of the automated deployment process for a cocoa application, I needed to access a .plist file to change some values before the application gets bundled and uploaded.</p>

<p>Since plist files are plain XML files, I could have used something like <em>libxml</em> to get the job done but since this script runs on a Mac, I have access to <a href="http://rubycocoa.sourceforge.net/HomePage" title="RubyCocoa: Welcome" target="_BLANK">RubyCocoa</a>. That spares me the XML parsing and writing and gives me a simple persistent dictionary instead. This is what I ended up with:
<script src="http://gist.github.com/287995.js?file=ruby_cocoa_plist.rb"></script>
</p>

<p>I think that is pretty neat. However, I ran into a small pitfall when I tried to compare an NSString with a regular expression. I got this warning:
  <pre>'NSString#=~' doesn't work correctly. Because it returns byte indexes. 
    Please use 'String#=~' instead.</pre>
</p>

<p>The solution is pretty simple, just call <em>to_s</em> on the dictionary values.
  <script src="http://gist.github.com/288008.js?file=nsstring_regular_expression.rb"></script>
</p>
