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

**name** - This is the url of the resource that was retrieved.  This url will not change from the originally requested url, even if the browser has to redirect to retrieve the resource.

**entryType** - Since we're in the Resource Timing API, all of our retrieved resources will return an entryType == 'resource'.

**startTime** - This returns either the time just before the browser starts to fetch the resource, or, if a redirect was involved, we'll get the time that the redirect started.

**duration** - This is the difference between the responseEnd and startTime values.

**initiatorType** - The initiatorType is the string representation of the element that caused the resource request.  For instance, an img tag would show up in the Resource Timing API as 'img'.  However, if the resource is retrieved via CSS using the url() syntax, then the initiator value will be 'css'.  If the request was initiated via AJAX, the type will be 'xmlhttprequest'.

**redirectStart** - If a resource is requested and the browser is redirected to another url on the same domain, redirectStart returns the time when the browser starts to request the redirected url.  If we get redirected to a url on a different domain, then we have two scenarios.  We'll either get the redirect start time as described before, or we'll get a zero.  It all depends on what the W3C spec calls the "timing allow check algorithm."

The algorithm is really nothing more than the value of the Timing-Allow-Origin HTTP response header.  Here's how the algorithm works; quoted from [the spec](http://www.w3.org/TR/resource-timing/#timing-allow-check):

> The timing allow check algorithm, which checks whether a cross-origin resource's timing information can be shared with the current document, is as follows:
>
>   1.  If the HTTP response includes zero or more than one Timing-Allow-Origin header values, return fail and terminate this algorithm.
>
>   2.  If the Timing-Allow-Origin header value is the test "*" character, return pass and terminate this algorithm.
>
>   3.  If the value of Timing-Allow-Origin is not a case-sensitive match for the value of the origin of the current document, return fail and terminate this algorithm.
>
>   4.  Return pass.

When we're interacting with a domain that is not ours, redirectStart (as well as several other attributes) will return a value only if that third party website has configured itself to be Resource Timing friendly.

**redirectEnd** - Returns the time after receiving the last byte of the response of the last redirect.  This attribute also depends on the Timing-Allow-Origin header when dealing with third party domains so you'll get a zero if the third party site isn't configured properly.

**fetchStart** - If there are no redirects, this is the time before the browser starts to fetch the resource.  If there are redirects, this is the time before the browser starts to fetch the final redirected resource.

**domainLookupStart** - This is the time just before the browser performs a DNS lookup on the resource.  If the browser is using a persistent connection or has the resource in cache, then this value will be equal to the fetchStart value.  Again, if the resource is on a third party domain and that domain isn't configured properly, you'll get a zero.

**domainLookupEnd** - Pretty much what you might expect, this is the time immediately after the DNS lookup.  Unless, of course, the connection is persistent or the resource is in cache, then this is just the fetchStart time again.  The same "third party website configuration" rules apply to this attribute as well. 

**connectStart** - This is the time after the DNS lookup is complete and just before the connection is started.  If the connection is persistent or the resource is cached, then this will be equal to domainLookupEnd.  Like most of the other attributes, the third party website configuration rules all apply. 

**connectEnd** - This represents the time just after establishing a connection.  This will be equal to domainLookupEnd if we're running a persistent connection or using cache; also, third party website configuration applies.  It's worth noting that connectEnd includes the time to establish SSL connections.   

**secureConnectionStart** - This one is interesting because it's optional for browsers to implement.  If a browser doesn't support this, the value will be undefined.  If the site isn't using https, the value will be zero, otherwise, this is the time just before the browser starts the handshake process.  

**requestStart** - Now that the browser has done all the necessary lookups, it can finally request the resource.  This attribute is the time value just before the request.  Third party website configuration rules apply. 

**responseStart** - This is the time just after the browser receives the first byte of the resource.  Third party configuration rules apply.

**responseEnd** - This is the time just after the browser receives the last byte of the resource.

In addition to these attributes, there are two functions and an event we need to be aware of when using resource timing:

**clearResourceTimings()** - clears the current PerformanceResourceTiming values.

**setResourceTimingBufferSize()** - sets the maximum PerformanceResourceTiming values that the browser should store.  Defaults to 150.

**onresourcetimingbufferfull** - this fires immediately after the browser's buffer is full.

### Examples

So we've finally made it to the fun part.  I'm going to take code from a Github [repo](https://github.com/AloisReitbauer/w3cinpractice) by Alois Reitbauer (one of the Performance group members).

Here's a straight forward example on how to just log out the load time of all the images on a page:

    var resourceList = window.performance.getEntriesByType("resource");
    for (i = 0; i < resourceList.length; i++){
      if (resourceList[i].initiatorType == "img") {
        console.log('Load time is ' + (resourceList[i].responseEnd - resourceList[i].startTime + ' ms'));
      }
    }

This example showcases a pretty creative use of the api.  It allows us to detect which resources (in this case images) never actually loaded.

    // find all images in the DOM
    var elems= document.getElementsByTagName('img');
    var definedImages = new Array();
    for (i = 0; i < elems.length; i++) {
        definedImages[definedImages.length] = "http://localhost:3000/" + elems[0].getAttribute('src');
    }

    // find all images that were loaded
    var loadedImages = new Array ();
    var resourceList = window.performance.getEntriesByType("resource");
    for (i = 0; i < resourceList.length; i++){
        if (resourceList[i].initiatorType == "img") {
            loadedImages[loadedImages.length]= resourceList[i].name;
        }
    }

    // check the difference
    for (i = 0; i < definedImages.length; i++){
        if(loadedImages.indexOf(definedImages[i]) < 0){
          console.log('Image ' + definedImages[i] + ' failed to load');
        } else {
          console.log('Image ' + definedImages[i] + ' loaded successfully');
        }
    }

Finally, here's an example that displays our top ten heaviest page resources:

    var topX = 0;
    var resourceList = window.performance.getEntriesByType("resource");
    resourceList.sort (function (a,b){return (a.duration - b.duration) * -1});
    for (i = 0; i < resourceList.length && topX < 10 ; i++){
        if (resourceList[i].initiatorType == "img") {
            ++topX;
            console.log('Load time ' + resourceList[i].name  +  ' is ' + ((resourceList[i].duration).toFixed(2) + ' ms'));
        }
    }

These are just a few examples of the resource api.  Checkout Alois' repo for more.
