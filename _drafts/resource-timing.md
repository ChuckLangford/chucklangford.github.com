---
layout: post
title: Resource Timing
---

My previous post was all about Navigation Timing.  Continuing with the theme of performance api's, it's time to take a look at Resource Timing.

### Resource Timing Defined

First of all, let's define resource timing in layman's terms.  Resource timing allows us to measure the performance metrics of each individual resource that a webpage references.  This means we can see how long each image took to download, or how much time it took to get all of our individual javascript and css files.  We can even measure (with varying accuracy) the latency of third party resources.

This level of detail can be put to good use when you're trying to improve the performance of your website.  We can see the slowest performing resource of a page and focus our efforts on that resource.  We can use this api to reveal resources that we didn't expect (think an image or font file defined in css).  We could even use it to build a waterfall chart with pure javascript.

### Getting Started

There are a couple of things we need to be aware of before we get into the details of what the API offers.  Mainly, how to access it.

The resource api is exposed via the window.performance object.  To actually retrieve the information we want, we have to call the performance object's getEntriesByType function, making sure to pass  in the type of object we want to retrieve (in this case, 'resource'):

    var resources = window.performance.getEntriesByType('resource');

This will return an array of PerformanceResourceTiming objects that hold the timing data we're after.

### The API

According to the W3C, the PerformanceResourceTiming interface extends the PeformanceEntry interface, so we get the following four attributes:

**name** - This is the url of the resource that was retrieved.  This url will not change from the originally requested url, even if the browser has to redirect to retrieve the resource.

**entryType** - Since we're in the Resource Timing API, all of our retrieved resource will return an entryType == 'resource'.

**startTime** - This returns either the time just before the browser starts to fetch the resource, or, if a redirect was involved, we'll get the time that the redirect started.

**duration** - This is the difference between the responseEnd and startTime values.

The PerformanceResourceTiming interface defines these new values for us to use:

**initiatorType** - The initiatorType is the string representation of the element that caused the resource request.  For instance, an <img> tag would show up in the Resource Timing API as 'img'.  However, if the resource is retrieved via CSS using the url() syntax, then the initiator value will be 'css'.  If the request was initiated via AJAX, the type will be 'xmlhttprequest'.

**redirectStart** - If a resource is requested and the browser is redirected to another url, redirectStart returns the time when the browser starts to request the redirected url.

**redirectEnd** - 

**fetchStart** - 

**domainLookupStart** - 

**domainLookupEnd** - 

**connectStart** - 

**connectEnd** - 

**secureConnectionStart** - 

**requestStart** - 

**responseStart** - 

**responseEnd** - 

### Examples

