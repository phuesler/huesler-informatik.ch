---
layout: post
title: Quicktip - Use RubyGems default options to save you some typing
---

<p>
 I hardly ever use the local <a href="http://rdoc.sourceforge.net/" title="RDoc - Document Generator for Ruby Source" target="_BLANK">RDoc</a> documentation of my installed Ruby gems. Typing in <em>--no-rdoc --no-ri</em> every time I install a gem is cumbersome. The gem takes longer to install and it uses up unnecessary disk space. That annoyance goes doubly when installing gems on a server.
</p>

<p>Luckily, <a href="http://docs.rubygems.org/" title="RubyGems Manuals" target="_BLANK">RubyGems</a> allows to set default options in a file called <em>.gemrc</em> which should be placed in your home directory. The syntax is in <a href="http://www.yaml.org/" title="The Official YAML Web Site" target="_BLANK">YAML</a> format, therefore straightforward for a Ruby programmer. Just add this line to <em>.gemrc</em> and you are golden.
 <pre>
   gem: --no-rdoc --no-ri
 </pre>
</p>

<p>A list of all options can be found <a href="http://docs.rubygems.org/read/chapter/11" title="gem Command Reference |  RubyGems Manuals" target="_BLANK">here.</a></p>