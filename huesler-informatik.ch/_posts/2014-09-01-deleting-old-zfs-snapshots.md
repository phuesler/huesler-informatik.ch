---
layout: post
title: "Deleting old ZFS snapshots"
---

I needed to delete old [zfs](http://docs.oracle.com/cd/E19253-01/819-5461/zfsover-2/) snapshots so
I wrote a little [bash](http://www.gnu.org/software/bash/) script that does this for me. In addition to time parameters you can specify a match pattern as well as instructing the script to run a test run,
which simulates the deletion of snapshots but does not actually delete them.

{% highlight bash %}
$: ./scripts/delete_snapshots.sh
No grep pattern supplied

delete_old_snapshots.sh -s SECONDS -m MINUTES -h HOURS -d DAYS -p GREP_PATTERN [-t]
 arguments:
 -s delete snapshots older than SECONDS
 -m delete snapshots older than MINUTES
 -h delete snapshots older than HOURS
 -d delete snapshots older than DAYS
 -p the grep pattern to use
 -t test run, do not destroy the snapshots just print them
{% endhighlight %}

Simulate the deletion of snapshots older than 1 day containing the word 'foo'

{% highlight bash %}
$: ./scripts/delete_snapshots.sh -d 1 -p foo -t
{% endhighlight %}

Delete snapshots older than 3 hours and 5 seconds:

{% highlight bash %}
$: ./scripts/delete_snapshots.sh -s 5 -p foo -h 1
{% endhighlight %}

Delete snapshots older than 1 second containing any characters (this is the nuclear option):

{% highlight bash %}
$: ./scripts/delete_snapshots.sh -s 1 -p '.'
{% endhighlight %}



Here is the script in its full glory. You can also find the latest version on my [github repository](https://github.com/phuesler/zfs-scripts/blob/master/scripts/delete_snapshots.sh).


{% highlight bash %}
#!/usr/bin/env bash

usage="$(basename "$0") -s SECONDS -m MINUTES -h HOURS -d DAYS -p GREP_PATTERN [-t]\n

arguments:\n
    -s delete snapshots older than SECONDS\n
    -m delete snapshots older than MINUTES\n
    -h delete snapshots older than HOURS\n
    -d delete snapshots older than DAYS\n
    -p the grep pattern to use\n
    -t test run, do not destroy the snapshots just print them
"

while getopts ":ts:m:h:d:p:" opt; do
  case $opt in
    s)
      seconds=$OPTARG
      ;;
    m)
      minutes=$OPTARG
      ;;
    h)
      hours=$OPTARG
      ;;
    d)
      days=$OPTARG
      ;;
    p)
      pattern=$OPTARG
      ;;
    t)
      test_run=true
      ;;
    \?)
      echo "Invalid option: -$OPTARG" 1>&2
      echo -e $usage 1>&2
      exit 1
      ;;
    :)
      echo "Option -$OPTARG requires an argument." 1>&2
      echo -e $usage 1>&2
      exit 1
      ;;
  esac
done

if [[ -z $pattern ]]; then
  echo -e "No grep pattern supplied\n" 1>&2
  echo -e $usage 1>&2
  exit 1
fi

if [[ -z $days ]]; then
  days=0
fi

if [[ -z $hours ]]; then
  hours=0
fi

if [[ -z $minutes ]]; then
  minutes=0
fi

if [[ -z $seconds ]]; then
  seconds=0
fi

compare_seconds=$(($days * 24 * 60 * 60 + $hours * 60 * 60 + $minutes * 60 + $seconds))
if [ $compare_seconds -lt 1 ]; then
  echo -e time has to be in the past 1>&2
  echo -e $usage 1>&2
  exit 1
fi

compare_timestamp=`date -j -v-$(echo $compare_seconds)S +"%s"`
# get a list of snapshots sorted by creation date, so that we get the oldest first
# This will allow us to skip the loop early
snapshots=`zfs list -H -t snapshot -o name,creation -s creation | grep $pattern`
# so for in uses \n as a delimiter

if [[ -z $snapshots ]]; then
  echo "no snapshots found for pattern $pattern"
  exit 0
fi


old_ifs=$IFS
IFS=$'\n'
for line in $snapshots; do
  snapshot=`echo $line | cut -f 1`
  creation_date=`echo $line | cut -f 2`
  creation_date_timestamp=`date -j -f "%a %b %d %H:%M %Y" "$creation_date" "+%s"`

  # Check if the creation date of a snapshot is less than our compare date
  # Meaning if it is older than our compare date
  # It is younger, we can stop processing since we the list is sorted by
  # compare date '-s creation'
  if [ $creation_date_timestamp -lt $compare_timestamp ]
  then
    if [[ -z $test_run ]]; then
      echo "DELETE: $snapshot from $creation_date"
      zfs destroy $snapshot
    else
      echo "WOULD DELETE: $snapshot from $creation_date"
    fi
  else
    echo "KEEP: $snapshot from $creation_date"
    echo "No more snapshots to be processed for $pattern. Skipping.."
    break
  fi
done
IFS=$old_ifs

{% endhighlight %}

