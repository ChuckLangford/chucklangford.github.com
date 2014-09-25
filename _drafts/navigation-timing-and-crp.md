---
layout: post
title: Browser Timing API's
---

I was fortunate enough to be able to attend the Velocity conference in New York and I have to say, I'm pretty pumped about what I've learned.  There's actually so much to share that it'll take a little time to get through so today I thought I'd start with the timing api's.

### What are the Timing API's?

Long story short, the api's allow us to measure real web page timing metrics using javascript.  This is awesome because we can report on exactly what kind of experience our users are actually having when they visit our sites.

There are three separate api's that are available: Navigation, Resource, and User.

### Navigation Timing

Navigation timing is where you should start your performance measurements; it tells us how long our pages take to load.  In fact, it goes into incredible detail about each step the browser is taking to actually retrieve a web page.  Let's take a brief look at what the api can do.

**navigationStart** - This attribute returns the time immediately after the page unload event has finished.  Basically, if there was a previous page loaded in the browser and you navigate to a new one, a page unload event occurrs.  navigationStart returns the time immediately after this event.

**unloadEventStart**

**unloadEventEnd**

**redirectStart**

**redirectEnd**

**fetchStart**

**domainLookupStart**

**domainLookupEnd**

**connectStart**

**connectEnd**

**secureConnectionStart**

**requestStart**

**responseStart**

**responseEnd**

**domLoading**

**domInteractive**

**domContentLoadedEventStart**

**domContentLoadedEventEnd**

**domComplete**

**loadEventStart**

**loadEventEnd**


### Resource Timing

### User Timing