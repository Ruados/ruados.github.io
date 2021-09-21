---
layout: post
title: "The Laws of Time!"
excerpt: "Little guide on how to become a time God"
categories: [tutorial, RHCSA, RHEL]
comments: true
image:
  feature: https://ruados.github.io/post_images/20210921_LawsOfTime_12_cron.png
  credit: Make a wild guess
---

You see, computers are machines, we ain't. We need the computers to do stuff while we're not looking at them, or work or something else, or we just want the pc to do something while we sleeping at home.

For that, we have several tool, but we'll focus just on two of the most primary to be found in essentially all unix like installation. We'll also briefly touch ntp just in the context of RHCSA.

Again, today we'll be using CentOS Stream 8.

So what tools we've got? 
 - at
 - cron

Both are incredibly simple, but we'll go over them now.

#at

Before and foremost, we must know whether the at daemon is running or not.
To do that, we simply run:

    {% raw %}
	systemctl status atd
    {% endraw %}

In case you are running a minimal installation, as I am, and do not have this package included, you can install easily enough using:
    {% raw %}
	dnf install -y at
    {% endraw %}

![Installation of at, nothing to see here.](https://ruados.github.io/post_images/20210921_LawsOfTime_03.png)

Again, if you see that the service isn't running, you can just do as shared in the screenshot.

![Services](https://ruados.github.io/post_images/20210921_LawsOfTime_04.png)
    {% raw %}
	systemctl enable --now atd
    {% endraw %}
The enable is to enable the service every time the system boots up. The --now is for the service to also start now.
If we wanted a service to run, but only during current runtime, we'd use just start instead.


The command at is used essentially ad-hoc. We want a specific task to run once at (heh) 3 minutes from now.
To do that, we simply:
    {% raw %}
	at now + 3 minutes
    {% endraw %}
From here, you hit enter, and then you can put the command that you need to be executed. To then close the at shell, simply do CTRL+D.
See here the example given:

![Time never stops.](https://ruados.github.io/post_images/20210921_LawsOfTime_05.png)

As you can see, I first ran date simply to show the date and time, then I executed the at command to run date at 3 minutos from now with the output being redirected to lawless.

And alas, when the time came, there it was.
Should mention, atq is at query, it is to query what tasks are planned.

![Multiple jobs.](https://ruados.github.io/post_images/20210921_LawsOfTime_06.png)

The syntax is very flexible. As you can see from the screenshot, we can use a lot of different time formats, but it's very human readable.

Now, say I don't remember what will run next Sunday. For that I simply do:

    {% raw %}
	at -c [ID]
    {% endraw %}

![A lot of text. Spooky.](https://ruados.github.io/post_images/20210921_LawsOfTime_08.png)

As you can see, lots of stuff, but most are system variables, we can see in the end what we inputed to the ran.

Last command. Say you no longer want to run job 4. For that you do atrm 4. And that's it for at.

![Queries.](https://ruados.github.io/post_images/20210921_LawsOfTime_09.png)

#cron

Oh cron, my cron. Love to hate you.

First things first, to check if cron service is running 
    {% raw %}
	systemctl status crond
    {% endraw %}

![More services.](https://ruados.github.io/post_images/20210921_LawsOfTime_10_cron.png)

With a simple cat, we can see most of what there's to configure:

![Standard boring empty crontab.](https://ruados.github.io/post_images/20210921_LawsOfTime_11_cron.png)

This I should mention, is the main cron file. You can use this file to configure all.

With that out of the way, allow me to share a friendly website: https://crontab.guru/

![vim as the editor](https://ruados.github.io/post_images/20210921_LawsOfTime_12_cron.png)
Here's an example of a job running every 5th minute, with the root user, you can specify the time as it's in there described. 

Besides this file, you can also as a regular user have crontabs of your own.
Here's the commands:
    {% raw %}
	crontab -l #to list the jobs of the current user
	crontab -u [user] -l #to see the jobs of another user
	crontab -e #to edit your own crontab
    {% endraw %}

There are also several folders you can put your scripts on, that will be launched automatically with cron.

    {% raw %}
	/etc/cron.hourly
	/etc/cron.daily
	/etc/cron.weekly
	/etc/cron.monthly
    {% endraw %}
Here you can drop any script you want to run, and it will run. Each folder is rather self explanatory.

#chrony

Chrony can be both be an ntp client and an ntp server. More often than not that is the case.
Network Time Protocol. This is a simple ancient service. What for? To keep things on time.

There are several ntp clients out there, but for RHCSA8 purposes, we'll only look at chrony. ntpd is being discontinued anyway, for those who care.

    {% raw %}
	dnf install chrony -y
    {% endraw %}

This is the command to install chrony out of the repos. 

The config file is /etc/chrony.conf. Bet you couldn't guess.

![Unaltered chrony config.](https://ruados.github.io/post_images/20210921_LawsOfTime_13_ntp.png)

For purposes of RHCSA, to add a new ntp server, we add the following at the beginning of the file:

    {% raw %}
	server [IP|FQDN] iburst
    {% endraw %}

The exam will give you the information needed for you to configure this.

We must of course activate the service now, and before that, activate the ntp setting on systemd.

    {% raw %}
	timedatectl set-ntp true
	systemctl enable --now chronyd
    {% endraw %}

And that's it. Congrats, now your machine is synced in time with other machines.

![Time never truly stops.](https://ruados.github.io/post_images/20210921_LawsOfTime_14_ntp.png)

As I'm pulling from a pool of other servers, we can see we are synced with other multiple servers.
On an entreprise environment, we'd only have a single local server synced with the atomic clock off the internet, and the rest of the machines in sync in this server. the server itself though could feed to others its own time, not necessarily in sync with the one authority in time, what's important is that the machines have the same time, or other things and services on the network can go off sync.

And that'll be for today folks.

See you space cowboy.
