# How to practically use Performance API to measure performance

## 

October 28, 2019 6 min read 

![Measuring performance with performance API.](https://blog.logrocket.com/wp-content/uploads/2019/10/API-logo-nocdn-2.png)

Historically we’ve had limited information regarding performance metrics on the client-side of performance monitoring. We’ve also been met with limitations in API browsers that obstructed us from accurately measuring performance.

Fortunately, this is starting to change thanks to new performance-oriented APIs. Now, the browser’s [Performance API](https://developer.mozilla.org/en-US/docs/Web/API/Performance_API) provides tools to accurately measure the performance of Web pages.

Before we dig into what these Performance APIs are, let’s look at some compelling reasons for why you should be using them.

### Benefits of using Performance API

- These APIs augment experience when using performance profiling in dev tools
- Chrome dev tools and other tools like Lighthouse are only helpful during the development phase. But with the Performance APIs, we can get real user measurement (RUM) in production.
- We can get really precise timestamps, which makes the analysis of these performance metrics very accurate.

Now let’s talk about what these APIs are.

“Performance API is part of the High Resolution Time API, but is enhanced by the [Performance Timeline API](https://developer.mozilla.org/en-US/docs/Web/API/Performance_Timeline), the [Navigation Timing API](https://developer.mozilla.org/en-US/docs/Web/API/Navigation_timing_API), the [User Timing API](https://developer.mozilla.org/en-US/docs/Web/API/User_Timing_API), and the [Resource Timing API](https://developer.mozilla.org/en-US/docs/Web/API/Resource_Timing_API).” – MDN

You’ll encounter a confusing slew of terms such as High Resolution Time, Performance Timeline API, etc, whenever reading about the Performance API, which makes it hard to understand what exactly it is and how you can make use of it to measure web performance.

Let’s break down these terms to get a better understanding.

### High resolution time

A high resolution time is precise up to fractions of a millisecond.

Comparatively, time based on the `Date` is accurate only up to the millisecond. This precision makes it ideal for yielding accurate measurements of time.

A high resolution time measured by User-Agent (UA) does not change with any changes in system time because it is taken from a global clock created by the UA.

Every measurement measured in the Performance API is a high resolution time. That’s why you’ll always hear that Performance API is a part of the High Resolution Time API.

### Performance timeline API

The [Performance Timeline API](https://www.w3.org/TR/performance-timeline-2/) is an extension of the Performance API. The extension provides interfaces to retrieve performance metrics based on specific filter criteria.

Performance Timeline API provides the following three methods, which are included in the `performance` interface:

- `getEntries()`
- `getEntriesByName()`
- `getEntriesByType()`

Each method returns a list of performance entries gathered from all of the other extensions of the Performance API.

`PerformanceObserver` is another interface included in the API. It watches for new entries in a given list of performance entries and notifies of the same.

### Performance entries

The things we measure with the Performance API are referred to as `entries`. These are the performance entries that are available to us:

- `mark`
- `measure`
- `navigation`
- `resource`
- `paint`
- `frame`

We’ll make use of these entries with the respective APIs to measure performance.

### What can we measure?

Let’s look at some practical measurements we can do with these APIs.

### Using navigation timing API and resource timing API

There is a significant overlap between these two APIs, so we’ll discuss them together.

Both are used to measure different resources. We will not go into the details of this overlap, but if you’re curious you can have a look at this [processing model](https://www.w3.org/TR/navigation-timing-2/#processing-model) which might help you understand this overlap better.

```
// Get Navigation Timing entries:
const navigationEntries = performance.getEntriesByType("navigation")[0]; // returns an array of a single object by default so we're directly getting that out.

// output:
{
  "name": "https://awebsite.com",
  "entryType": "navigation",
  "startTime": 0,
  "duration": 7816.495000151917,
  "initiatorType": "navigation",
  "nextHopProtocol": "",
  "workerStart": 9.504999965429306,
  "redirectStart": 0,
  "redirectEnd": 0,
  "fetchStart": 39.72000000067055,
  "domainLookupStart": 39.72000000067055,
  "domainLookupEnd": 39.72000000067055,
  "connectStart": 39.72000000067055,
  "connectEnd": 39.72000000067055,
  "secureConnectionStart": 0,
  "requestStart": 39.72000000067055,
  "responseStart": 6608.200000133365,
  "responseEnd": 6640.834999969229,
  "transferSize": 0,
  "encodedBodySize": 0,
  "decodedBodySize": 0,
  "serverTiming": [],
  "unloadEventStart": 0,
  "unloadEventEnd": 0,
  "domInteractive": 6812.060000142083,
  "domContentLoadedEventStart": 6812.115000095218,
  "domContentLoadedEventEnd": 6813.680000137538,
  "domComplete": 7727.995000081137,
  "loadEventStart": 7760.385000146925,
  "loadEventEnd": 7816.495000151917,
  "type": "navigate",
  "redirectCount": 0
}
// Get Resource Timing entries
const resourceListEntries = performance.getEntriesByType("resource");
```

This will return an array of resource timing objects. A single object will look like this:

```
{
  "name": "https://awebsite.com/images/image.png",
  "entryType": "resource",
  "startTime": 237.85999999381602,
  "duration": 11.274999938905239,
  "initiatorType": "img",
  "nextHopProtocol": "h2",
  "workerStart": 0,
  "redirectStart": 0,
  "redirectEnd": 0,
  "fetchStart": 237.85999999381602,
  "domainLookupStart": 237.85999999381602,
  "domainLookupEnd": 237.85999999381602,
  "connectStart": 237.85999999381602,
  "connectEnd": 237.85999999381602,
  "secureConnectionStart": 0,
  "requestStart": 243.38999995961785,
  "responseStart": 244.40500000491738,
  "responseEnd": 249.13499993272126,
  "transferSize": 0,
  "encodedBodySize": 29009,
  "decodedBodySize": 29009,
  "serverTiming": []
}
```

- **Measure DNS time**: When a user requests a URL, the Domain Name System (DNS) is queried to translate a domain to an IP address.

Both Navigation and Resource Timing expose two DNS-related metrics:

–`domainLookupStart`: marks when a DNS lookup starts.
–`domainLookupEnd`: marks when a DNS lookup ends.

```
// Measuring DNS lookup time
const dnsTime = navigationEntries.domainLookupEnd - navigationEntries.domainLookupStart;
```

**Gotcha**: Both `domainLookupStart` and `domainLookupEnd` can be `0` for a resource served by a third party if that host doesn’t set a proper `Timing-Allow-Origin` response header.

- **Measure request and response timings**

Both Navigation and Resource Timing describe requests and responses with these metrics-

- `fetchStart` marks when the browser starts to fetch a resource. This doesn’t directly mark when the browser makes a network request for a resource, but rather it marks when it begins checking caches (like HTTP and service worker caches) to see if a network request is even necessary.
- `requestStart` is when the browser issues the network request
- `responseStart` is when the first byte of the response arrives
- `responseEnd` is when the last byte of the response arrives
- `workerStart` marks when a request is being fetched from a service worker. This will always be `0` if a service worker isn’t installed for the current page.

```
// Request + Request Time
const totalTime = navigationEntries.responseEnd - navigationEntries.requestStart;
// Response time with cache seek
const fetchTime = navigationEntries.responseEnd - navigationEntries.fetchStart;

// Response time with Service worker
let workerTime = 0;
if (navigationEntries.workerStart > 0) {
workerTime = navigationEntries.responseEnd - navigationEntries.workerStart;
}

// Time To First Byte
const ttfb = navigationEntries.responseStart - navigationEntries.requestStart;

// Redirect Time
const redirectTime = navigationEntries.redirectEnd - navigationEntries.redirectStart;
```

- **Measure HTTP header size**

```
const headerSize = navigationEntries.transferSize - navigationEntries.encodedBodySize;
```

`transferSize` is the total size of the resource including HTTP headers.
`encodedBodySize` is the compressed size of the resource *excluding* HTTP headers.
`decodedBodySize` is the decompressed size of the resource (again, excluding HTTP headers).

- **Measure load time of resources**

```
resourceListEntries.forEach(resource => {
  if (resource.initiatorType == 'img') {
    console.info(`Time taken to load ${resource.name}: `, resource.responseEnd - resource.startTime);
  }
});
```

The `initiatorType` property returns the type of resource that initiated the performance entry. In the above example, we are only concerned with images, but we can also check for `script`, `css`, `xmlhttprequest`, etc.

- **Get metrics for a single resource**

We can do this by using `getEntriesByName`, which gets a performance entry by its name. Here it will be the URL for that resource:

```
const impResourceTime = performance.getEntriesByName("https://awebsite.com/imp-resource.png");
```

Additionally, document processing metrics are also available to us such as `domInteractive`, `domContentLoadedEventStart`, `domContentLoadedEventEnd`, and `domComplete`.

The `duration` property conveys the load time of the document.

### Using paint timing API

Painting is any activity by the browser that involves drawing pixels on the browser window. We can measure the “First Time to Paint” and “First Contentful Paint” with this API.
`first-paint:` The point at which the browser has painted the first pixel on the page
`first-contentful-paint`: The point at which the first bit of content is painted – i.e. something which is defined in the DOM. This could be text, image, or canvas render.

```
const paintEntries = performance.getEntriesByType("paint");
```

This will return an array consisting of two objects:

```
[
  {
    "name": "first-paint",
    "entryType": "paint",
    "startTime": 17718.514999956824,
    "duration": 0
  },
  {
    "name": "first-contentful-paint",
    "entryType": "paint",
    "startTime": 17718.519999994896,
    "duration": 0
  }
]
```

From the entries, we can extract out the metrics:

```
paintEntries.forEach((paintMetric) => {
  console.info(`${paintMetric.name}: ${paintMetric.startTime}`);
});
```

### Using user timing

The User Timing API provides us with methods we can call at different places in our app, which lets us track where the time is being spent.

We can measure performance for scripts, how long specific JavaScript tasks are taking, and even the latency in how users interact with the page.

The mark method provided by this API is the main tool in our user timing analysis toolkit.

It stores a timestamp for us. What’s super useful about `mark()` is that we can name the timestamp, and the API will remember the name and the timestamp as a single unit.

Calling `mark()` at various places in our application lets us work out how much time it took to hit that mark in our web app.

```
performance.mark('starting_calculations')
const multiply = 82 * 21;
performance.mark('ending_calculations')

performance.mark('starting_awesome_script')
function awesomeScript() {
  console.log('doing awesome stuff')
}
performance.mark('ending_awesome_script');
```

Once we’ve set a bunch of timing marks, we then want to find out the elapsed time between these marks.

This is where the `measure()` method comes into play.

The `measure()` method calculates the elapsed time between marks, and can also measure the time between our mark and any of the well-known event names in the [PerformanceTiming](https://www.w3.org/TR/navigation-timing/#sec-navigation-timing-interface) interface, such as `paint`, `navigation`, etc.

The `measure` method takes in 3 arguments: first is the name of the measure itself (which can be anything), then the name of the starting mark, and finally the name of the ending mark.

So, the above example with `measure` would be:

```
performance.mark('starting_calculations')
const multiply = 82 * 21;
performance.mark('ending_calculations')
+ performance.measure("multiply_measure", "starting_calculations", "ending_calculations");

performance.mark('starting_awesome_script')
function awesomeScript() {
  console.log('doing awesome stuff')
}
performance.mark('starting_awesome_script');
+ performance.measure("awesome_script", "starting_awesome_script", "starting_awesome_script");
```

To get all our `measure`s, we can use our trusty `getEntriesByType`:

```
const measures = performance.getEntriesByType('measure');
    measures.forEach(measureItem => {
      console.log(`${measureItem.name}: ${measureItem.duration}`);
    });
```

This API is great for narrowing down the performance hot-spots in our web app to create a clear picture of where time is being spent.

------

Awesome! We have gathered all sorts of performance metrics. Now we can send all this data back to our monitoring tool, or send it to be stored someplace and analyzed for later.

Keep in mind, these APIs are not available everywhere. But, the great thing is that methods like `getEntriesByType` won’t throw errors if they can’t find anything.

So we can check if anything is returned by `getEntriesByType` or not and then do our PerformanceAPI measurements:

```
if (performance.getEntriesByType("navigation").length > 0) {
  // We have Navigation Timing API
}
```

### Bonus: Use Performance API with Puppeteer

[Puppeteer](https://pptr.dev/) is a headless Node library that provides a high-level API to control Chrome or Chromium over the [DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/). Puppeteer runs headless by default.

Most things that you can do manually in the browser can be done using Puppeteer!

Here’s an example of using Navigation Timing API to extract timing metrics:

```
const puppeteer = require('puppeteer');

(async () => {
  const browser = await puppeteer.launch();
  const page = await browser.newPage();
  await page.goto('https://awebsite.com'); // change to your website
  
  // Executes Navigation API within the page context
  const performanceTiming = JSON.parse(
      await page.evaluate(() => JSON.stringify(window.performance.timing))
  );
  console.log('performanceTiming', performanceTiming)
  await browser.close();
})();
```

This returns a timing object as seen previously in the Navigation Timing API section:

```
{
  "navigationStart": 1570451005291,
  "unloadEventStart": 1570451005728,
  "unloadEventEnd": 1570451006183,
  "redirectStart": 0,
  "redirectEnd": 0,
  "fetchStart": 1570451005302,
  "domainLookupStart": 1570451005302,
  "domainLookupEnd": 1570451005302,
  "connectStart": 1570451005302,
  "connectEnd": 1570451005302,
  "secureConnectionStart": 0,
  "requestStart": 1570451005309,
  "responseStart": 1570451005681,
  "responseEnd": 1570451006173,
  "domLoading": 1570451006246,
  "domInteractive": 1570451010094,
  "domContentLoadedEventStart": 1570451010094,
  "domContentLoadedEventEnd": 1570451010096,
  "domComplete": 1570451012756,
  "loadEventStart": 1570451012756,
  "loadEventEnd": 1570451012801
}
```

You can learn more about Puppeteer on the [official website](https://pptr.dev/) and also check out some of its uses in [this repo](https://github.com/ananyaneogi/puppeteer-uses).

## [LogRocket](https://logrocket.com/signup/): Full visibility into your web apps

[![LogRocket Dashboard Free Trial Banner](https://blog.logrocket.com/wp-content/uploads/2017/03/1d0cd-1s_rmyo6nbrasp-xtvbaxfg.png)](https://logrocket.com/signup/)

[LogRocket](https://logrocket.com/signup/) is a frontend application monitoring solution that lets you replay problems as if they happened in your own browser. Instead of guessing why errors happen, or asking users for screenshots and log dumps, LogRocket lets you replay the session to quickly understand what went wrong. It works perfectly with any app, regardless of framework, and has plugins to log additional context from Redux, Vuex, and @ngrx/store.

In addition to logging Redux actions and state, LogRocket records console logs, JavaScript errors, stacktraces, network requests/responses with headers + bodies, browser metadata, and custom logs. It also instruments the DOM to record the HTML and CSS on the page, recreating pixel-perfect videos of even the most complex single-page apps.

[Try it for free](https://logrocket.com/signup/).

### Additional tesources

1. [MDN Performance API](https://developer.mozilla.org/en-US/docs/Web/API/Performance_API)
2. [A Primer for Web Performance Timing APIs](https://w3c.github.io/perf-timing-primer/)
3. [Test website performance with Puppeteer](https://michaljanaszek.com/blog/test-website-performance-with-puppeteer)
    