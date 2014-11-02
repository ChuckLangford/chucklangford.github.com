---
layout: post
title: User Timing 
---

After reviewing both the Navigation and Resource timing specifications, we've finally arrived at the User timing spec.  This is the last of the performance timing specifications I'll review, and thankfully, one of the most straightforward.

### User Timing in a Nutshell

The User Timing api allows you to measure the performance of your javascript.  Quite simply, it allows you to specify a start time at any point in your code, an end point at any point in your code, and then measure the difference between the two.

### "I Can Already Do That With Date.now()"

Yes you can.  So why do we need an api for something we can already do?  The spec tells us: "...the precision of this timestamp varies between user agents."  To put it another way, the browsers are too inconsistent in how they report Date.now().

### Marks

Before getting into the details, it's important to understand that all time measurements created with the User Timing api are relative to the navigation start time of the page.  In other words, when you navigate to the page, the timer starts at 0 and works it way up.

To record a start or end time, you use the mark method;

    performance.mark("startTime1");
    yourCode();
    performance.mark("endTime1");

The only things you need to know when creating a mark are 1) You can't use a reserved word and 2) You can reuse the same name; the value will just be overwritten each time.

In order to access the values of a mark you created, request the marks from the performance object:

    var perfEntries = performance.getEntriesByType("mark");
    for (var i = 0; i < perfEntries.length; i++) {
      if (window.console) console.log("Name: "        + perfEntries[i].name      + 
                                      " Entry Type: " + perfEntries[i].entryType +
                                      " Start Time: " + perfEntries[i].startTime + 
                                      " Duration: "   + perfEntries[i].duration  + "\n");
    }

**name** - This is the name you gave the mark.

**entryType** - This will be "mark".

**startTime** - This is the time value that was recorded when your mark function ran.

**duration** - For a mark, this will return a 0.


### Measures

The measure function is a convenience method that finds the difference between two marks and stores it in a named measure.

    performance.measure('myNewMeasure', 'markOneStart', 'markOneEnd');

Retrieving measure values is done exactly the same way as marks except you specify "measure" as the type to retrieve:

    var perfEntries = performance.getEntriesByType("measure");

**name** - This is the name you have assigned to the measure.

**entryType** - This will be "measure".

**startTime** - This is the time the measure was taken.

**duration** - This is the calculated duration of the measure.

The measure function also has two other modes of operation you need to be aware.  First, if you only specify a measure name, then the reported duration will be the difference between the navigation start and the current time.

Second, if you only specify a measure name and a start mark name, then the reported duration will be the difference between the start mark and the current time.

### Methods to be Aware Of

**clearMarks** - If you call this method with no name, you'll clear all the marks on the page.  If you specify a mark name, it will clear all the marks with that name.  If you specify a mark name that doesn't exist, then this function does nothing.

**clearMeasures** - This behaves in all the same ways as the clearMarks function except it applies to your measures.

### Example

Here's an example of how you might use this code in an external javascript file.  Once again, I'm relying heavily on a [repo](https://github.com/AloisReitbauer/w3cinpractice) from Alois Reitbauer.

    var scriptSource = 'myScriptName'; 

    performance.mark(scriptSource + 'Start');

    var delay= 5 * 1000 // 10 seconds
    var quitAt = Date.now() + delay;
    while (Date.now() <= quitAt){
      ; // good old busy waiting - doing nothing
    }

    performance.measure(scriptSource, scriptSource + 'Start');

With this in your javascript file, you query the measure types to find the duration of your scripts. 
