---
layout: post
title: iPhone testing, the lay of the land
---

<p>Today, someone asked about acceptance testing of iPhone applications. I answered that there are unit testing frameworks for objective-c but no solutions similar to what <a href="http://seleniumhq.org/" title="Selenium web application testing system" taget="_BLANK">Selenium</a> is for web applications.</p>

<p>An hour later during a boxing bag session, I do this to clear my head and as a replacement for cigarette breaks, I figured that my answer was probably wrong. It was based on information I have gathered a few months ago. So I decided to do my homework and brush up my knowledge.</p>

<p>Apple has written a great <a href="http://developer.apple.com/tools/unittest.html" title="Test Driving Your Code with OCUnit" target="_BLANK">introduction</a> about unit testing for objective-c. That's a good start, but since an iPhone application is rather GUI heavy, I want to drive the user interface and access its components. In addition to that, I want to assert conditions, otherwise it would be a simplified macro recorder.</p>

<p>Recently I have read <a href="http://www.pragprog.com/titles/idgtr/scripted-gui-testing-with-ruby" title="The Pragmatic Bookshelf | Scripted GUI Testing with Ruby" target="_BLANK">Scripted GUI Testing with Ruby</a>. I really liked how the author used RSpec and its story runner to test applications. So I figured it would be great to drive an iPhone application in a similar way, preferably with something that works like <a href="http://cukes.info/" title="Cucumber - Making BDD fun">Cucumber</a>.</p>

<p><a href="http://cocoawithlove.com/" title="Cocoa with Love" target="_BLANK">Matt Gallagher</a> has found a <a href="http://cocoawithlove.com/2008/11/automated-user-interface-testing-on.html" title="Cocoa with Love: Automated user interface testing on the iPhone" target="_BLANK">way</a> to run automated, scripted tests on an iPhone app's user interface. Basically, he creates a <a href="http://en.wikipedia.org/wiki/Objective-C#Categories" title="Objective-C - Wikipedia, the free encyclopedia" target="_BLANK">category</a> on <em>UIView</em> with a method that can be called on any <em>UIView</em> object to get a description of it and all its children. To make selecting a specific element easier, he converts the description to XML so that one can use XPATH queries. He also dug deep into undocumented cocoa code to be able to simulate touch events. Those are needed to manipulated the user interface.</p>

<p>There is a project called <a href="http://code.google.com/p/bromine/" title="bromine - Project Hosting on Google Code" target="_BLANK">bromine</a> which uses Matt's approach to do automated user interface testing. That's a good start but I don't really like to write my tests in XML. XML is meant for machines to read, not humans.</p>

<p>Ian Dees, the guy who write <a href="http://www.pragprog.com/titles/idgtr/scripted-gui-testing-with-ruby" title="The Pragmatic Bookshelf | Scripted GUI Testing with Ruby" target="_BLANK">Scripted GUI Testing with Ruby</a>, seemed to have the same feeling. He took Matt Gallagher's code, made it work with Cucumber and called it <a href="http://github.com/undees/brominet/tree/master" title="undees's brominet at master - GitHub" target="_BLANK">brominet</a>. He also gave a great <a href="http://en.oreilly.com/oscon2009/public/schedule/detail/8073" title="Testing iPhone apps with Ruby and Cucumber: OSCON 2009 - O'Reilly Conferences, July 20 - 24, 2009, San Jose, CA" target="_BLANK">presentation</a> on that topic. This is definitely an interesting approach, especially because it uses Cucumber.</p>

<p>Last but not least, I stumbled upon a project called <a href="http://code.google.com/p/uispec/" title="uispec - Project Hosting on Google Code" target="_BLANK">uispec</a>. It's a <a href="http://en.wikipedia.org/wiki/Behavior_Driven_Development" title="Behavior Driven Development - Wikipedia, the free encyclopedia" target="_BLANK">BDD</a> framework which is modeled after RSpec and drives the iPhone UI. The tests can either be written in objective-c or a DSL called <a href="http://code.google.com/p/uispec/wiki/Documentation#UIScript" title="Documentation - 
 uispec - UISpec Documentation - Project Hosting on Google Code" target="_BLANK">UIScript</a>. I gave their sample project a try and recored a video showing it in action.</p>

<object width="560" height="340"><param name="movie" value="http://www.youtube.com/v/-kWYatRKnYo&hl=en&fs=1&rel=0"></param><param name="allowFullScreen" value="true"></param><param name="allowscriptaccess" value="always"></param><embed src="http://www.youtube.com/v/-kWYatRKnYo&hl=en&fs=1&rel=0" type="application/x-shockwave-flash" allowscriptaccess="always" allowfullscreen="true" width="560" height="340"></embed></object>

<p>I must say, uispec looks very promising. Especially the control selectors look simple and straightforward, even though the console output could be a little bit nicer. One thing I don't like are all the warnings the compiler produced. For the sample project, I got 132 warnings. For example:
  <pre>warning: 'UIQuery' may not respond to '-text'</pre>
 In my opinion, compiler warnings should be treated as errors and therefore should be resolved. I don't like to work on a project, that generates over 100 warnings just because a test framework is in use.
</p>

<p>For now, I think a combination of uispec and Cucumber would be my preferred solution to do automated iPhone testing.</p>
