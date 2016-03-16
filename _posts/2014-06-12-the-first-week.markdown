---
layout: post
title:  "The First Week"
date:   2014-06-12 12:00
categories: mozilla internship demonology
---

How do I...
===========

There is a question that exists at the heart of all programming disciplines,
that divides the wheat from the chaff, that casts apart the cream from the crop,
that abuses metaphors to make a point more dramatically. This universal question is
no other than "How do I...?"

When learning a new language, a new library, or any new tool, "How do I" forms
the basis for learning the underlying system. If I am trying to use a sewing
machine, "How do I make a pair of pants?" will lead me down the path of
needle-threading, seaming, pedalling, and countless other skills. Most
importantly, I will learn these skills on an as-needed basis, without the need
to study the machine's book-sized manual.

In programming, this practice is so wide-spread that there are meta-"How do I"
commentaries. My favorite two of these are the both dreaded and beloved yak
stack and the opaque WAYRTTD.

A yak stack is where all of the sub-"How do I"'s end up. For example, if I am
trying to butter toast, I first need to know how to get butter, locate a knife,
and operate a toaster. WAYRTTD stands for "What Are You Really Trying To Do?",
a question evoked by the more specific "How do I"'s.

This week, my yak stack overflowed several times and WAYRRTDs occasionally
prompted me to divulge my true intentions. My task was to read the operating
system's logs and save them to a file. Little did I know that this task was an
iceberg of complexity. My first hint came when any file IO function called on
the log file failed. Because Firefox OS uses the Netscape Portable Runtime,
which for brevity I will refer to as Satan, there were two different ways of
opening the file, and eight different ways of reading from the file once it was
opened. Satan also made sure each of these operations were abstracted behind
several layers of indirection. I spent the better part of the day trying to
bargain with Satan to get one of these combinations of opening and reading to
function. After I gave up on bargaining with the dark lord, it was time to
start building the yak stack.

If mashing together opening and reading functions is trying out different
chants your local Satanist swears will summon the demon himself, the yak stack
is opening a portal to the first circle of hell to pal it up with the virtuous
pagans. To put it in simpler terms: "Abandon all hope, ye who enter here."

My deliverance came in the form of the lovely people of Mozilla. My mentor gave
me the username of a knowledgeable person on IRC who was able to take my
current study of the demonic arts and convert it to my first real amount of
progress. After this introduction, everything was sunshine and rainbows. Any
problems I could not solve I would bounce of this friendly individual and our
collective wills would quickly demolish it.

To summarize, if you are trying to summon the devil, make sure you can talk to
a very skilled demonologist.
