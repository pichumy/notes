When a user visits a site that it has visited before, the browser will consult the cache for static assets such as javascript, css, and images.

This is viewable in chrome://cache

The browser knows what to cache based on the headers in the http request.

* ETag
* Cache-Control
* Expires
* Last-Modified

Etag, or entity tag, is a string that serves as a validation token. This is usually hashed based on the file contents. A server can include this ETag in a response, and the browser can use this for future requests to determine if it needs to update. If the hash is the same, the resource is the same, and the server sends a 304 code with an empty body. The browser then knows it's safe to use the cached copy. Etag is only sent when the file has expired from the cache.

Cache-control is used to control cache behavior, expiration, and validation. Can use multiple at once.

Cache-Control: public means that the resource can e cached by any cache (browser, CDN, etc)

Cache-Control: private means that the resource can only be cached by the browser

Cache-Control: no-store means that the browser should always request the resource from the server

Cache-Control no-cache means that the browser can cache the file, but to not use it until it checks with the server to validate that it has the latest version. This validation uses an ETag header. This is usually used with html files.

* Expiration  

Cache-Control: max-age=60 specifies that the max length of time in seconds the resource should be cached. 60 means it should be cached for 1 minute. RFC 2616 recommends the maximum value should be no longer than 1 year. (max-age=31536000).

Cache-Control: s-max-age=60. This would be used by intermediate caches like CDNs.

* Validation

Cache-Control: must-revalidate

This tells the cache it must verify the status of the stale resource before using it, and expired ones should not be used.

* Expires

Header from HTTP 1.0 era, still used on many sites. This header provides an expiration date after which the asset should be considered invalid. Browser will ignore if there's a max-age directive in Cache-control.

* Last-Modified

Also from HTTP 1.0 era. This contains date and time from when the resource was last modified.

* HTML Meta Tag

Prior to HTML5, using meta tags inside html to specify cache-control was valid.

<meta http-equiv="Cache-control" content="no-cache">

This is not discouraged and not valid HTML5. Only browsers can parse this, intermediate caches can not.

* HTTP Response

HTTP responses look like this
```
Accept-Ranges: bytes
Cache-Control: max-age=3600
Connection: Keep-Alive
Content-Length: 4361
Content-Type: image/png
Date: Tue, 25 Jul 2017 17:26:16 GMT
ETag: "1109-554221c5c8540"
Expires: Tue, 25 Jul 2017 18:26:16 GMT
Keep-Alive: timeout=5, max=93
Last-Modified: Wed, 12 Jul 2017 17:26:05 GMT
Server: Apache
```

Line2 tells us the cache's max age is 1 hour
Line5 tells us that this is a png image  
Line7 shows us an Etag value used for validation after the 1 hour mark to verify that the resource hasn't changed.
Line8 is expires which can be ignored.
Line 10 is the Last-Modified header which shows when the image was last modified.

* Cache Busting

We can force the browser to bypass the cache by changing the filename. (Useful for things like when favicons aren't working). To the browser, this will appear as a completely new resource so it will refetch it from the server.

Cache busting allows us to keep long max-age values for resources that may change frequently.

* Versioning

Using version numbers in filenames can be useful for this.

assets/js/app-v2.min.js

* Fingerprinting

You could use a fingerprint based on file contents inside the file name

app-d41d8cd98f00b204e9800998ecf8427e.min.js

* Append Query String

You could append a string to the filename as its created

app.min.js?version=2

* Best Practices

Do:
* Use Cache-Control and ETag headers to control cache behavior for static assets.
* Set long max-age values to reap benefits of browser cache
* Use fingerprinting or versioning to cache bust
Don't:
* Use HTML meta tags
* Use query strings
