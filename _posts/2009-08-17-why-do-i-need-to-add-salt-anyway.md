---
layout: post
title: Why do I need to add salt anyway?
---

<p>Storing user credentials is a requirement of most of todays web applications. A lot of people have written tutorials on how to do it securely, mentioning words like hash, <a href="http://en.wikipedia.org/wiki/MD5" title="MD5 - Wikipedia, the free encyclopedia" target="_BLANK">MD5</a> and <a href="http://en.wikipedia.org/wiki/SHA_hash_functions" title="SHA hash functions - Wikipedia, the free encyclopedia" target="_BLANK">SHA</a>. Still, I have come across situations where I had to learn, that the story behind secure storage of user credentials is not entirely clear to some developers and a lot of product owners.</p>

<p>Here is the motivation for this article. Recently, I have stumbled upon a production database that stores all passwords in plaintext. And a few days later, a customer service representative of a german phone company told me the password to access my account.
</p>
<p>Let me make this clear: This is never a good idea! Your security might get compromised and along with that, all data in your database gets stolen. As a result, an attacker not only has access to all your users data. Since users tend to use the same password for all kinds of services, an attacker gets potential access to those services as well.</p>

<p>You generally hear or read that you should store a hash of the password using algorithms like MD5 or SHA-2. Since the former is no longer <a href="http://web.archive.org/web/20070604205756/http://www.infosec.sdu.edu.cn/paper/md5-attack.pdf" title="" target="_BLANK">secure</a>, SHA-2 is the better choice. But that solves only half of the problem. Using <a href="http://en.wikipedia.org/wiki/Rainbow_table" title="Rainbow table - Wikipedia, the free encyclopedia" target="_BLANK">rainbow tables</a>, an attacker can figure out passwords, especially weak ones, fairly quick.</p>

<p>How does that rainbow table thing work? The nature of cryptographic hash algorithms like MD5 or SHA is, that it is infeasible to find the input for a given hash. A rainbow table is a lookup table that contains the hashes of commonly used passwords and words found in a dictionary. So if you find the hash in the rainbow table, you know the password.</p>

<p>That is why you should add salt to the passwords before you hash them. In it's simplest form, you just append a salt to the password before creating the hash. This makes the length of the password longer and two users with the same passwords have a different hash key. Assuming the salt is per password, fairly random and of appropriate length, in order to succeed, an attacker needs to compute tables for each salt value used. That is computationally infeasible. In layman's terms: It would take forever to do this.</p>

<p>I really hope, this post helped to make the story behind passwords, hash and salt a bit clearer.</p>
