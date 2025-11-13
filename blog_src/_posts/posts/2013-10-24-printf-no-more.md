---
layout: post
title: "Printf No More"
date: 2013-10-24
categories: 
  - "software"
---

I wondered to a colleague if there is a way to get [GDB](http://www.sourceware.org/gdb/) to print the value of a variable when it changes or when a certain program location is reached _without_ breaking; in effect, placing printfs in my program automatically.

His Google-foo was better than mine he and found a [stackoverflow](http://stackoverflow.com/questions/6206447/gdb-logging-something-instead-of-breaking) solution.  For example, something like

```
  set pagination off
  break foo.c:42
  commands
  silent
  print x
  cont
  end
```

prints the value of x whenever line 42 in file foo.c is reached, but just keeps running the program.  You can stick a little script like this in your .gdbinit file, which you can reload with

```
  source .gdbinit

```

This is particularly useful if you're debugging a remote target that doesn't support print statements. It's a cool trick for embedded systems debugging.
