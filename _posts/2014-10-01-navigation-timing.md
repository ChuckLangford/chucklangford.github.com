---
layout: post
title: Navigation Timing
---

I was fortunate enough to be able to attend the Velocity conference in New York and I have to say, I'm pretty pumped about what I've learned.  There's actually so much to share that it'll take a little time to get through so today I thought I'd start with the timing api's.

### What are the Timing API's?

Long story short, the api's allow us to measure real web page timing metrics using javascript.  This is awesome because we can report on exactly what kind of experience our users are actually having when they visit our sites.

There are three separate api's that are available: Navigation, Resource, and User.  We'll go over Navigation Timing in this post.

### Navigation Timing

Navigation timing is where you should start your performance measurements; it tells us how long our pages take to load.  In fact, it goes into incredible detail about each step the browser is taking to retrieve a web page.  Here's a quick rundown of the information that the api provides.

**navigationStart** - This attribute returns the time immediately after the page unload event has finished.  Basically, if there was a previous page loaded in the browser and you navigate to a new one, a page unload event occurrs.  navigationStart returns the time immediately after this event.

**unloadEventStart** - As you leave a web page within a browser, the browser fires an unload event.  The unloadEventStart attribute returns the starting time of the unload event.  You would use this with the next attribute, unloadEventEnd, to measure how long the unload event takes to complete.  This attribute, and it's sibling, only work if there was a previous document and that document was on the same domain.

**unloadEventEnd** - This is the end time of the unload event.  unloadEventEnd - unloadEventStart = How long the unload event took.

**redirectStart** - If one of your pages redirects to another page on the same domain, this attribute gives us the starting time of the document fetch.  Like the previous two attributes, it works best with it's sibling...

**redirectEnd** - There are a lot of Start/End pairs in the api.  redirectEnd - redirectStart = How long it took redirect to another page and receive all the bytes for the new page.

**fetchStart** - This returns the time before the browser issues an HTTP GET and before it checks it's local cache.

**domainLookupStart** - This one is what you expect it to be, the start time of the browser issuing a DNS lookup for a resource, but there is a bit more to understand.  If your browser has a persistent connection to the server or if the resource is found in the local browser cache, then this attribute will return the same value as fetchStart.

**domainLookupEnd** - Again, this one will either be the end time of looking up the domain or, if the resource is in browser cache, it will return the same value as fetchStart.  There's also one more interesting piece of info to know, browsers can cache their own DNS records.  So these two attributes, domainLookupStart and domainLookupEnd, can reflect the DNS lookup values from the browser's cache.

**connectStart** - After a browser has retrieved the DNS records, it's very next step is to connect to our web server.  This attribute reflects the starting time of that connection.  If the resource was in the local cache or the browser has a persistent connection, this value is equal to domainLookupEnd.

**connectEnd** - Hurray we established a connection, here's how long it took: connectEnd - connectStart.  This attribute is also subject to local cache or persistent connections (so it would equal the same thing as domainLookupEnd).  connectEnd also encompasses the time to establish SSL handshakes.  Finally, if a connection attempt fails before it succeeds, then connectStart and connectEnd will only tell us the time values for the successful connection.

**secureConnectionStart** - Unfortunately, this one is currently optional for the browsers.  If a browser does implement it, this is the time just before the SSL handshake process.  If a browser doesn't implement this, it is suppossed to return undefined.

**requestStart** - This one confused me for a bit.  requestStart is the time value of when the browser requests the page.  At first, it sounds a lot like fetchStart, and for the most part these two values are probably going to be really close.  The difference, however, is that fetchStart happens before cache/DNS lookups and before connections.  requestStart represents the start time after all those previous steps have finished and the browser can confidently ask for the resource from the correct place.  Notice that there is no requestEnd attribute.  From the documentation and various W3C chat's I found, a requestEnd attribute was both difficult to define and implement.

**responseStart** - This is the time immediately after the browser has received the first byte from the server/cache/local resource.

**responseEnd** - This is either the time immediately after receiving the last byte from the server/cache/local resource or it's the time just before the transport connection is closed, whichever comes first.

**domLoading** - This is the time just before the browser sets the "current document readiness" state to "loading".  Honestly, I had no idea what any of that meant so I looked it up.  Here's the link [http://www.w3.org/TR/html5/dom.html#current-document-readiness](http://www.w3.org/TR/html5/dom.html#current-document-readiness).  Basically, the document object has [three states](http://www.w3.org/TR/html5/dom.html#the-document-object): loading, interactive, and complete (loading while the DOM is still loading, interactive once the DOM is parsed but we're still waiting on subresources, and complete is all done).  You can see it by going to your browser's console and typing document.readyState.

**domInteractive** - If any of my domLoading explanation made sense, then this should be apparent.  This is the time just before the document readiness is set to "interactive".  It also represents the time after blocking scripts have been executed (inline scripts or src scripts that didn't use defer or asynch).

**domContentLoadedEventStart** - This is the time right before the DOMContentLoaded event.  It also represents the time after the browser has parsed all the scripts that used the defer attribute and not the async attribute.  (So when do async script run?  As soon as they are downloaded they are run... asynchronously).

**domContentLoadedEventEnd** - This is the time right after the DOMContentLoaded event.  This happens once all scripts that are not part of the load event have completed.

**domComplete** - This is just before the document readiness state is set to complete.  

**loadEventStart** - This is just before the document's load event is fired.

**loadEventEnd** - This is just after the document's load event.  If the event was not fired or not completed, this is zero.

### So What Do We Do With All This?

Measure All The Things!  Literally, just start measuring.  There's a lot of information at our fingertips and it can be combined in various useful ways.  Here's an example from a GitHub repo by Alois Reitbauer (a member of the W3C performance group).  You can find the repo here: [https://github.com/AloisReitbauer/w3cinpractice](https://github.com/AloisReitbauer/w3cinpractice)

	var timing= performance.timing;
    var loadTime= (timing.loadEventEnd- timing.navigationStart)/1000;
    console.log('Page took ' + loadTime.toFixed(2) + ' seconds to load');

That's just the start.  By recording various combinations of these, we can start to understand real user metrics (RUM) and make our users' experiences better.

Happpy Coding.