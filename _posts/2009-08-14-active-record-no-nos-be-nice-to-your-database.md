---
layout: post
title: Active Record No No's. Be nice to your database
---

<p>ActiveRecord, the ORM of Ruby on Rails is a great tool to model database backed applications without writing a lot of repetitive SQL queries. By following the principle "Convention over Configuration", you don't need to setup a lot of configuration to get started. So no getter and setters and no mapping definitions if you follow the conventions. At first, it feels a little bit like magic, except that it is not. In the end, it is still generating SQL for you. That means, that even Active Record does a great job for you, it cannot protect you from bad queries.</p>

<p>During the past few months, I have seen my fair share of code that makes your database cry havoc and even worse, takes down an entire application on subsequent requests. Most of these mistakes could have been easily avoided by following one simple guidline: <em>"Be reasonable. Before committing your code, take a look at the generated SQL and keep in mind, that the production database might have more than just 10 records inside."</em><br />
<img src="/public/images/posts/reasonable_cat.jpg" />
</p>

<p>So let's take a look at some example code that might be trouble for your application:
  <script src="http://gist.github.com/167944.js"></script>
  At first sight, this doesn't look very bad. It gets the job done and probably performs nicely in a development environment. First, let's look at the SQL being generated:
  <script src="http://gist.github.com/167954.js"></script>
  We are fetching every single column of every user in our system just to get the id's. Even worse, for every user in the database, ActiveRecord creates a new user object. That takes time and uses up memory. Just imagine what would happen if you have a couple of thousands or more user in your database. At best, the request will just timeout after 60 seconds and everything goes back to normal. Worst case, memory consumption rockets, takes out the server and the database is jammed with long running queries. Not bad for just one line of code.
</p>
<p>
  Let's assume this line of code was somewhere inside a view in the admin interface providing the option to assign a project owner. To fix the problem, we can do the following:
  <ol>
    <li>Paginate</li>
    <li>Don't fetch all columns</li>
    <li>Move the code to the controller, that's where it belongs</li>
  </ol>
  <script src="http://gist.github.com/167978.js"></script>
</p>
<p>Here are some other gems I have found hidden somewhere in a view:
      <script src="http://gist.github.com/167799.js"></script>
    <script src="http://gist.github.com/167802.js"></script>
  <script src="http://gist.github.com/167801.js"></script>  
</p>
<h3>Tips</h3>
<p>So here are my tips to prevent these kind of time bombs in your application:
  <ul>
    <li>Look at the generated SQL</li>
    <li>Let AR/SQL do the math (conditions,count,sum, 
    named_scope, sorting, ordering) instead of using ruby's enumerable functions</li>
    <li>Paginate, but paginate in SQL not on ruby collections (User.paginate vs. User.all.paginate)</li>
    <li>Only fetch what you really need  (:select => "id, name") </li>
    <li>Eager load associations with :include to avoid the n + 1 problem (Post.all.each{|post| puts post.author.name} vs. Post.all(:include => :author))</li>
    <li>Whenever possible fetch all records you need from inside a controller or a model</li>
  </ul>  
</p>
<h3>Check SQL logs</h3>
<p>To get a feeling of what is happening behind the scenes, I like to try things out in script/console and look at the generated SQL. To see it directly in irb, I change the Rails logger to STDOUT in my ~/.irbrc file:
  <script src="http://gist.github.com/167983.js"></script>
</p>
<p>To get even more information, there are plenty of cool profiling tools that hook into your Rails app and give you a nice GUI with all the information you need. Try out <a href="http://www.newrelic.com/" title="New Relic .:. Rails Application Performance Monitor">New Relic</a>, <a href="http://www.fiveruns.com/products/tuneup" title="Free Ruby on Rails application profiling for developers - FiveRuns TuneUp" target="_blank">FiveRuns TuneUp</a> or <a href="http://www.brynary.com/2009/4/22/rack-bug-debugging-toolbar-in-four-minutes" title="Rack::Bug debugging toolbar in four minutes" target="_blank">Rack::Bug</a>. There is also great <a href="http://railscasts.com/episodes/161-three-profiling-tools" title="Railscasts - Three Profiling Tools" target="_blank">Railscast</a> explaining all of these three tools.</p>
