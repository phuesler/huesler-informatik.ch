---
layout: post
title: Rails -  Testing rescue action in public behavior
---

<p>
  Rails handles exceptions that occur in production mode by rendering the file <em>public/500.html</em> or one of the other two (404.html and 422.html) if appropriate. Generally this is a good default, but in some cases it is a requirement to redirect the user to, or render specific pages for different types of exceptions. For instance, when an application provides a JSON API, it would be bad to return a 500.html to the caller when something goes wrong.
</p>

<p>There are generally two ways do this in Rails. Either overwrite <em>rescue_action_in_public</em> in your controller or use the newer <em>rescue_from</em> to map exceptions to a handler. Ryan Daigle wrote a short but comprehensive <a href="http://ryandaigle.com/articles/2007/9/24/what-s-new-in-edge-rails-better-exception-handling#comment-form" title="Ryan's Scraps: What's New in Edge Rails: Better Exception Handling" target="_BLANK">blog post</a> on this topic.</p>

<p>However, testing this is a bit tricky. <em>config.consider_all_requests_local</em> needs to be set to false and <em>local_request?</em> should be set to false as well. Testing this manually in the browser is cumbersome and error prone, so I needed a way to verify the behavior with a controller test. A quick <em>ack --ruby rescue_action_in_public actionpack-2.3.4</em> turned up <em>/actionpack-2.3.4/test/controller/rescue_test.rb</em> exactly what I needed.</p>

<p>
  As it turns out, this is actually quiet easy to test. Basically, there are four things to consider and you are good to go:
</p>

<b>1. Simulate the production environment in a setup method</b>
<p>
  <script src="http://gist.github.com/287899.js?file=setup_test_rescue_action_in_public.rb"></script>
</p>

<b>2. Create a dummy controller with actions that reproduce the errors you want to test</b>
<p>
  <script src="http://gist.github.com/287910.js?file=dummy_controller_rescue_action_in_public.rb"></script>
</p>

<b>3. Generate a route for your dummy controller if you have deleted the default routes in <em>routes.rb</em></b>
<p>
  <script src="http://gist.github.com/287919.js?file=draw_route_for_dummy_controller.rb"></script>
</p>

<b>4. Call those methods in your test cases</b>
<p><script src="http://gist.github.com/287916.js?file=assertions_rescue_action_in_public.rb"></script></p>

<p>
  With these four steps we got most uses cases covered. I ran into a problem, where I wanted to test an error that occurred during template rendering and required some special exception handling code. This is what worked for me:
  <script src="http://gist.github.com/287934.js?file=test_template_rendering_errors.rb"></script>
</p>
