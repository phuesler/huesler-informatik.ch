---
layout: post
title: "Exporting skype conversations/chat logs on OSX"
---

Today I wanted to export my [Skype](https://www.skype.com) chat logs but I could not find a good way other than copy and pasting it page by page.

After a little googling it turned out, that Skype is using [SQLite](http://www.sqlite.org) as a storage engine so it is as simple as finding the main database and then load it up into SQLite.


## TLDR; Get all messages

On OSX you need 3 things:

* A Terminal
* Your skype username
* [SQLite](http://www.sqlite.org)

While I'm comfortable doing this all on the terminal, you can also use any of the [SQLite GUIs out there.](https://www.google.com/search?&rls=en&q=sqlite+gui&ie=UTF-8&oe=UTF-8#q=sqlite+gui+osx&rls=en)

Then do the following:

1. Quit Skype, otherwhise the database will be locked
2. Open up your Terminal
3. Type in the following and do not forget to replace `your_skype_username` with your actual skype name

{% highlight bash %}
sqlite3 ~/Library/Application\ Support/Skype/your_skype_username/main.db "select author, timestamp, body_xml from messages;" > ~/Downloads/skype_chat_history.txt
{% endhighlight %}

Hat tip to [Jako](http://superuser.com/users/98595/jako) for giving me the [inspiration](http://superuser.com/questions/312119/is-there-a-way-to-export-chat-history-in-skype-5-2-x-for-os-x)

## Poking around

Since it is a regular SQLite database you can load it up into an interactive shell and look around a little bit.

{% highlight bash %}
$: sqlite3 ~/Library/Application\ Support/Skype/your_skype_username/main.db
SQLite version 3.7.13 2012-07-17 17:46:21
Enter ".help" for instructions
Enter SQL statements terminated with a ";"
sqlite>
{% endhighlight %}

### Quitting the shell
{% highlight SQL %}
sqlite>.quit
{% endhighlight %}


### Listing the tables

{% highlight bash %}
sqlite> .tables
Accounts          ChatMembers       DbMeta            SMSes
Alerts            Chats             LegacyMessages    Transfers
AppSchemaVersion  ContactGroups     MediaDocuments    VideoMessages
CallMembers       Contacts          Messages          Videos
Calls             Conversations     Participants      Voicemails
{% endhighlight %}

### Show table definition

So we can see what the columns mean

{% highlight bash %}
sqlite> PRAGMA table_info(Messages);
0|id|INTEGER|1||1
1|is_permanent|INTEGER|0||0
2|convo_id|INTEGER|0||0
3|chatname|TEXT|0||0
4|author|TEXT|0||0
5|from_dispname|TEXT|0||0
6|author_was_live|INTEGER|0||0
7|guid|BLOB|0||0
8|dialog_partner|TEXT|0||0
9|timestamp|INTEGER|0||0
10|type|INTEGER|0||0
11|sending_status|INTEGER|0||0
12|consumption_status|INTEGER|0||0
13|edited_by|TEXT|0||0
14|edited_timestamp|INTEGER|0||0
15|param_key|INTEGER|0||0
16|param_value|INTEGER|0||0
17|body_xml|TEXT|0||0
18|identities|TEXT|0||0
19|reason|TEXT|0||0
20|leavereason|INTEGER|0||0
21|participant_count|INTEGER|0||0
22|error_code|INTEGER|0||0
23|chatmsg_type|INTEGER|0||0
24|chatmsg_status|INTEGER|0||0
25|body_is_rawxml|INTEGER|0||0
26|oldoptions|INTEGER|0||0
27|newoptions|INTEGER|0||0
28|newrole|INTEGER|0||0
29|pk_id|INTEGER|0||0
30|crc|INTEGER|0||0
31|remote_id|INTEGER|0||0
32|call_guid|TEXT|0||0
33|extprop_chatmsg_ft_index_timestamp|INTEGER|0||0
34|extprop_chatmsg_is_pending|INTEGER|0||0
{% endhighlight %}

### Listing all skype users I have ever exchanged messages with


{% highlight sql %}
sqlite> select dialog_partner from messages group by dialog_partner;

user1
user2
user3
{% endhighlight %}

### Listing the conversation with a specific skype user

Let's say I was talking to `peterpan` for the last year or so and want to see all messages:

{% highlight sql %}
sqlite> select author,timestamp, body_xml from messages where dialog_partner = 'peterpan';
{% endhighlight %}


### Listing all chats

{% highlight sql %}
sqlite> select name, friendlyname, topic from chats;
#a_redacted_skype_username/$a_redacted_skype_username;a_redacted_id|Peter Pan, Michael Jordan, Hans Foo|Chat Topic
{% endhighlight %}


### Listing all messages from a specific chat

We could do this in one SQL statement but for illustration purposes, I'm splitting it up into statements.

First we need to find the chat id of the conversation we are interested in:


{% highlight sql %}
sqlite> select name from chats where topic = 'Name of the Chat';
#a_skype_user_name/$another_skype_username;aa0aaaa0a000aa00
{% endhighlight %}

Then we can use this value to find list all the messages from this conversation:

{% highlight sql %}
sqlite> select author, timestamp, body from messages where chatname = '#a_skype_user_name/$another_skype_username;aa0aaaa0a000aa00';
{% endhighlight %}

### Exporting a result to a CSV file

{% highlight sql %}
sqlite> .mode csv
sqlite> .output /tmp/my_result.csv
sqlite> select * from messages;
sqlite> .quit
{% endhighlight %}

{% highlight bash %}
$: open /tmp/my_result.csv
{% endhighlight %}

### Closing thoughts

Judging by the tables, there are a lot more things to find in the database:

{% highlight bash %}
sqlite> .tables
Accounts          ChatMembers       DbMeta            SMSes
Alerts            Chats             LegacyMessages    Transfers
AppSchemaVersion  ContactGroups     MediaDocuments    VideoMessages
CallMembers       Contacts          Messages          Videos
Calls             Conversations     Participants      Voicemails
{% endhighlight %}

At this point I am not sure why there is a table called `Conversations`, I can only assume that it is the parent table for `Calls`, `SMSes`, `VideoMessages`, `Messages` etc.
