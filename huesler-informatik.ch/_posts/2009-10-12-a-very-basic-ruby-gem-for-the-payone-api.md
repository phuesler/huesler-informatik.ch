---
layout: post
title: A very basic ruby gem for the payone API
---

<p>This is a short post about a small ruby gem I have written awhile ago. It's a simple interface to <a href="http://www.payone.de/" title="PAYONE ONLINE PAYMENT - Smart. Simple. Safe." target="_BLANK">Payone's HTTP API</a>. Payone is a <a href="http://en.wikipedia.org/wiki/Payment_service_provider" title="Payment service provider - Wikipedia, the free encyclopedia" target="_BLANK">payment service provider</a> in Germany that offers several different payment methods like credit card, direct debit and so on and so forth.
</p>

<p>The gem is still a little bit rough around the edges and doesn't do much more than sending http post requests. So you still have to know the payone API. There is a lot of room for improvement, I am thinking about an API similar to <a href="http://www.activemerchant.org/" title="Active Merchant" target="_BLANK">ActiveMerchant</a> or even an integration.</p>

<p>For the impatient here is how you install it:
	<code>
	  gem install gemcutter
	  gem tumble

	  gem install payone_connect	
	</code>
	
	And here is how you use it:
	<code>
	  require 'payone_connect'
	  payone = PayoneConnect.new(api_key,params_hash)
	  puts payone.request[:status]
	</code>
</p>

<p>
Enough said, here you can find the code: <a href="http://github.com/phuesler/payone_connect" title="phuesler's payone_connect at master - GitHub" target="_BLANK">http://github.com/phuesler/payone_connect</a>
</p>
