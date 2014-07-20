---
layout: post
title: simple_app_config -  A little ruby helper to define and access the app configuration
---

<p>Having a convenient way to store an applications configuration is something I want in every Ruby project. There are plenty of solutions for Ruby on Rails but none of them did really match my taste, because they did way too much. I wanted something  simple that does not depend on Ruby on Rails and does not try to be too clever.
</p>

<p>Here are my requirements for a configuration lib:
<ul>
<li>Nesting and namespaces</li>
<li>Simple string interpolation</li>
<li>Support for environments e.g testing, production, debug</li>
<li>Defaults for each environment</li>
</ul>
</p>
<p>
 And this is what I came up with:
<ul>
<li>Use YAML (defaults, namespaces, scopes)</li>
<li>Parse the YAML file with ERB</li>
<li>Return an OpenStruct object for easy method accessors</li>
</ul>
</p>
<p>
The code and and information on how to install and use the lib can be found here: <a href="http://github.com/phuesler/simple_app_config" target="_BLANK">http://github.com/phuesler/simple_app_config</a></p>
