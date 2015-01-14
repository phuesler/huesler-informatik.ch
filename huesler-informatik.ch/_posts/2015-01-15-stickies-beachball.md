---
layout: post
title: "Stickies is beachballing - OSX Yosemite"
---

[Stickies](http://support.apple.com/kb/PH18738) kept beachballing on me upon startup and would eventually crash.
Checking [dmesg](http://www.freebsd.org/cgi/man.cgi?query=dmesg&sektion=8&n=1) in the [Terminal](http://blog.teamtreehouse.com/introduction-to-the-mac-os-x-command-line) revealed that it had issues handling its own application state.

{% highlight bash %}
$: sudo dmesg
...
Sandbox: Stickies(22579) System Policy: deny file-write-unlink /Users/phuesler/Library/Saved Application State/com.apple.Stickies.savedState/restorecount.plist
...
{% endhighlight %}

I have fixed it by deleting `restorecount.plist` but making a backup of it first, just in case:

{% highlight bash %}
$: cp -p “~/Library/Saved Application State/com.apple.Stickies.savedState/restorecount.plist” /tmp/restorecount.plist
$: rm -rf “~/Library/Saved Application State/com.apple.Stickies.savedState/restorecount.plist”
{% endhighlight %}

