---
layout: post
title: "Cross Site Scripting (XSS) and Cross Site Request Forgery (CSRF) Prevention Cheatsheet"
date: 2021-01-09 11:32:04 +0000
comments: true
categories: 
---

Cross Site Request Forgery (CSRF) and Cross Site Scripting (XSS) are two of the most common (but certain not only) exploits the developers of web applications need to be familiar with.

<!--more-->

## Cross Site Request Forgery

Cross Site Request Forgery (CSRF) is basically tricking a user into making a request to a service that they are not meaning to. This is achieved by getting their browser to make a request to a service, using information the browser will automatically send with the request (usually a session cookie) so the service will authenticate and act on the request; an attacker need not know or have access to the user's credentials or session details, and only that they direct the user's pre-authorized browser to make the request on their behalf.

In order for this attack to work, the browser must already be in a state where it contains the context necessary to successfully perform a request. This usually means (but is not necessarily limited to), the user already being signed into a service, without terminating their current session.

It's important to point out, although this attack utilises a user's browser, it's actually concerned with executing code on a _server_, by making the corresponding request. Most countermeasures are also ultimately enforced on the server.

### A simple example
                   
Let's consider a simple example of how a CSRF attack works: an attacker may post a hyperlink to a popular service, say a social media platform, on a public forum or other website that they control, and try and get a user to click it by presenting it as something of interest. If a user is still signed into the other service, clicking on the link could make a request to delete their account or make a post on their behalf. 

The means of making a request to the target service need not be so obvious and doesn't actually require any user interaction at all. There are certain HTML tags that point to external resources that a browser will automatically make a HTTP request for in order to display or use. One such example is a `<img>` (image) tag - when a browser downloads a web page, it will automatically request the associated images. An attacker can use this to instead make a request to a target service.

### CSRF Prevention techniques
                     
#### Lean on the Single Origin Principle

The Single Origin Principle (SOP) is the name given to a core principle of a collection of restrictions that span different browser APIs and features - each with slight differences - but all with the same intent of governing how resources from one origin can interact with resources of another. The most relevant of those browser features are DOM Access, `XMLHTTPRequest`s and Cookies (but they are more). While SOP a good start, because of the way the web historically functioned, there are many exclusions where SOP is not applied by browsers, and therefore provides no protection against CSRF (or similar) attacks. 

SOP chooses its exclusions based on a number of assumptions about the type of cross-origin requests that are safe to perform, which developers must ensure hold true if they want to guard against CSRF:

* Make sure all `GET`, `HEAD` and `OPTION` requests are free of side-effects
                     
### Implement Cross Origin Resource Sharing

Cross Origin Resource Sharing (CORS) is a way to selectively permit requests that would otherwise be forbidden by the SOP. It doesn't add any more protection on top of SOP; it walks it back for cases where you trust the request to came from a given origin.

For most `GET`, `HEAD` and `POST` requests that have no body, the browser once again assumes making the request is free of side-effects and waits until the request responds before validating whether it should be used. It does so by checking for the presence and value of a `Access-Control-Allow-Origin` header with a value that matches the current origin (using a custom matching algorithm). For all other cross origin requests that would normally be forbidden by SOP, it performs a `OPTIONS` pre-flight request that contains the details of the request it is planning on making. The server responds to this request in a similar manner, and the client is able to determine whether it has approval to make the actual request.   
  
Developers can change or require a request to be made in such a way that is no longer qualifies as an exclusion, and in doing so, cause the browser to apply SOP restrictions and employ CORS:

* When a request uses JSON or XML, require it to be submitted over XHR, which forces the browser to use the `XMLHTTPRequest`s interface.  
* For JSON or XML APIs, configure your server to always verify the `Content-Type` header is correct: Forms are limited to three content types: `application/x-www-form-urlencoded`, `multipart/form-data`, and `text/plain`. To get a content type `application/json` you need to use a `XMLHTTPRequest`, but unless the `Content-Type` is enforced, there is nothing stopping an attacker submitting valid JSON through a form using one of those 3 content types, and in doing so, subverting SOP.
                              
#### CSRF tokens

While SOP and CORS help verify a request is coming from an approved origin, CSRF tokens check the user has previously preformed an action or followed an expected sequence of actions before making the request. 

##### Synchroniser Token Pattern

The Synchroniser Token Pattern is where a token is issued by the server and stored in the user's session cookies, as well as another location the client can then use to copy it into a subsequent request; the two instances of the token are then matched before the server is prepared to accept the request.

###### Form field synchroniser tokens

In the case of a HTML form, every time a client requests a page that contains a form, the server stores a random token as a cookie (in the response's headers), and includes that same token as a hidden form field (in the response's HTML body). When the user fills out the form and sends it back to the server, the hidden field containing the token is amongst the data sent in the request body, and the token stored in the cookie is included with the request's headers. The server can then compare the token in the form data with the one in the cookie: if they're the same, the server knows the request was made with a form that it generated (and not one that was generated by an attacker). This assumes the header has been properly transmitted over HTTPS and marked as `Secure` and `HttpOnly`. 

CSRF tokens can also be submitted as custom headers back to the server, but _not_ using cookies (that's where the reference copy of the token is) and _not_ in the URL or its query parameters. CSRF tokens must be generated securely, and then kept secret by avoiding them being logged, whereas URLs are recorded in many places over the course of a HTTP request and appear in `Referer` headers when redirecting to other sites.

The CSRF token is changed with every new user session, or preferably with each individual request. If you do generate a unique token per request, this may cause issues if a user goes back (using the browser's Back button or the `history` API) to a previous page with a form on it (which has an old token as a hidden field). If they attempt to submit the form with the old token in the request body and the new one from their latest request in the cookie headers - the server will report a failed match.

It's worth noting that CSRF tokens do nothing to prove the form has not been tampered with since it was initially "issued" by the server, and so does not protect against Cross Site Scripting or other attacks - more on this later.

#### Double submit cookie

A *Cookie-to-header token* is a way for JavaScript applications (using AJAX requests) to achieve a similar result to transmitting synchroniser tokens in form data. The server generates a pseudo-random value and sets it as a cookie (separate from the user's session) when the client makes its first request (even before they've necessarily authenticated). The client then submits this value as a custom header with every request back to the server, which it checks before honouring the request. This relies on the cookie _not_ being `httponly` and for the JavaScript application to be delivered from a domain that has permission to read the cookie value from the session response and set the custom header on subsequent requests.

Using custom HTTP headers to transmit CSRF tokens is considered more secure than using form fields because, by default only JavaScript running on the same origin can add custom headers to requests (this can be extended to include cross-origin requests by permitting the custom header in the server's CORS configuration). This is effectively another technique to ensure the browser is applying the SOP and CORS.

This technique is only effective if you know your subdomains are fully secure and only accept HTTPS connections, due to the possible vulnerabilities:

* (Possibly compromised) subdomains can write cookies to parent domains
* Cookies can be sent over plain HTTP (which can be sniffed or intercepted)

##### Encryption token pattern

The limitations of a Cookie-to-header token can be guarded against by using an encrypted token instead of the token directly. 

The Encryption Token Pattern involves the server generating a token by encrypting the user's session id and a timestamp, and including it in the form data or custom headers the client sends with its next request. Rather than comparing this with a token stored in the user's cookie, the server decrypts the token and compares the session id to the one stored in the user's session cookie, and the timestamp against a sliding window to prevent the same token being used in the future. If the digest is not present, or cannot be decrypted with the encryption key known only to the server, than the request is denied. 

A HMAC function can be used instead of an encryption/decryption algorithm in much the same manner, in what's called HMAC Based Token Pattern.

#### Using cookie prefixes

There is a standard for two Cookie Prefixes that further restrict how cookies can be accessed by sites in browsers that support it:

* A `__Secure-` prefix makes a cookie accessible to a site delivered over HTTPS only.
* A `__Host-` prefix is equivalent to the secure one, plus further restricting a cookie to only be available to a site from the same domain that it was set on (making it no longer possible for a subdomain to override the cookie).

The full RFC can be seen [here](https://tools.ietf.org/html/draft-ietf-httpbis-cookie-prefixes-00).

The `__Host-` prefix can be used with the techniques that place tokens in cookies.
               
### Checking Source and Origin headers

Among the _Forbidden Headers_ that cannot be set or changed via JavaScrip are the `Origin` and `Referer` headers, which are set by the browser and indicate where the request originated. By configuring your server to compare the domains of both (if present) to where you know your application to be deployed, you can confirm the request is not a cross-origin one. Care must be taken when comparing the origins to compare the entire value (not just a subset of it) to prevent an attacker registering a subdomain that is a superset of yours: `my.domain.com.badguys.com`.

(OWASP)[https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html#identifying-source-origin-via-originreferer-header] suggests blocking requests that have neither header set, or failing that, to log these requests and actively review them.

There are some normal cases to be aware of:

* IE 11 does not add an `Origin` header for CORS requests across sites in a ["trusted site zone"](https://docs.microsoft.com/en-us/troubleshoot/browsers/ie-security-zones-registry-entries)
* A `302` redirect to another origin does not set the `Origin` header
* There are [cases where the browser is required to set the Origin to null](https://stackoverflow.com/a/42242802/1337796) - typically for privacy concerns
* Most browsers only include the `Origin` header for `POST`, `DELETE` and `PUT` requests (cross-origin requests always include it)
* There are equally [use cases](https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html#identifying-the-target-origin) where the `Referer` header is not set, or stripped out for privacy reasons.
                            
Usually your server(s) are behind one or more layers of routing or proxying and it's not easy to reliably determine the domain your application has been deployed to, from the `Host` header (which is rewritten when a request is proxied; the original value is typically available in the `X-Forwarded-Host` header, though). So it's recommended to configure each deployment with the correct static value, if you can.

### SameSite cookie attribute

Another way you can mitigate against CSRF attacks is to tell the browser not to submit cookies for cross-domain requests, by using the `SameSite` attribute of the `Set-Cookie` header. It has 3 possible values:

* `Strict`: Prevents the browser sending any stored cookies for a domain when following links from another. This prevents being able to follow links from other websites and automatically be signed in.
* `Lax`: (Default) Blocks only for requests that typically have side effects (e.g. `POST` requests)
* `None`: Always sends the cookie for cross-domain requests

This approach should be used in conjunction with the techniques discussed above (not instead of). Unless the user is using a browser that supports this option (which is only "most" modern ones), they will receive no protection from it.

### User interaction for high risk operations

For high impact actions such as changing a user's password, you should consider CSRF defense techniques that actually require interaction from the user, such as requesting the re-entry of their password, or re-authenticating through a secure means like 2 factor authentication. CAPTCHAs are a less secure option, but make it more difficult for an attacker to automate the generation of the request.

#### How detected or suspected CSRF attacks should be handled

(OWASP)[https://cheatsheetseries.owasp.org/cheatsheets/Cross-Site_Request_Forgery_Prevention_Cheat_Sheet.html#use-built-in-or-existing-csrf-implementations-for-csrf-protection] suggests detected CSRF requests should be:

> aborted, [the] session of the user terminated and the event logged as a potential CSRF attack in progress

## Content Security Policy (CSP)

What's included and what's not
