---
layout: post
title: Globalize bug -  Where have my methods gone?
---

<p>Two weeks ago I stumbled upon a weird <a href="http://globalize-rails.org/" title="Globalize" target="_BLANK">globalize2</a> behavior in a <a href="http://rubyonrails.org/" title="Ruby on Rails" target="_BLANK">Ruby on Rails</a> application I was working on.</p>

<p><a href="http://globalize-rails.org/" title="Globalize" target="_BLANK">globalize2</a> creates a has many association on a translated model that points to a proxy class. This proxy class holds the model translation for any given language. So if I tell it to translate a model called Tag, it will create a class called TagTranslation on the fly.</p>

<p>I needed to add behavior to TagTranslation. I wanted to add a before save hook that makes transformations on an attribute before it gets saved. Therefore, I created an active record model called TagTranslation and added the hook. This worked wonderfully in test environment but not in the development environment. For some reason, the transformations weren't made.</p>

<p>A short debugging session in script/console revealed that there was no before save hook on TagTranslation in development mode. More precisely, the class never got loaded. So I dug into the globalize2 code and found out that it just overrides an existing class with the same name.</p>
<p>So what happened? In that particular case, this happened when config.cache_classes was set to false in the rails config. This is the default in the development environment. What is difference then? When config.cache_classes is set to true (Default in production and test) then Rails eager loads the classes at startup. The class Tag gets loaded before TagTranslation and therefore globalize has already created a proxy class. So when our TagTranslation gets loaded it just reopens an existing class and adds stuff to it. What happens in development mode? Rails only loads models when triggered through Object.const_missing. When the constant Tag is referenced somewhere in the code, the class Tag is loaded and globalize creates a proxy class called TagTranslation as well as a has many association. The association normally triggers the associated class to be loaded (our TagTranslation model) but in this case, the class already exists and therefore our TagTranslation model never gets loaded.</p>

<p>How to fix it? I have a fork on github with a failing test and a fix:  <a href="http://github.com/phuesler/globalize2" title="phuesler's globalize2 at master - GitHub" target="_BLANK">http://github.com/phuesler/globalize2</a>. Alternatively you can make sure that your class gets loaded explicitly. You have to do this after you call globalize's translate method. Here is how you could do it for the aforementioned Tag model:
<script src="http://gist.github.com/207018.js"></script>
</p>
