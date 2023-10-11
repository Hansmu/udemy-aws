<h2>Related concepts</h2>
**Cookies**
An HTTP cookie is a piece of data that a server sends to a web browser.
Then, the web browser stores the HTTP cookie on the user’s computer and sends it back to the same server in the later requests.

Example server response to set the cookie:
```
HTTP/1.1 200 OK
Content-type: text/html
Set-Cookie:username=admin
...
```

The web browser stores this information and sends it back to the server via the Cookie HTTP header for the next request as follows:
```
GET /index.html HTTP/1.1
Cookie: username=admin
...
```

In practice, cookies serve the following purposes:
* Session management – cookies allow you to manage any information that the server should remember.
For example, logins, shopping carts, etc.
* Personalization – cookies allow you to store user preferences, themes, and setting specific to a user.
* Tracking – cookies help record and analyze user behaviors in advertising.

A cookie consists of the following information stored by the web browser:
* Name – a unique name that identifies the cookie.
The cookie names are case-insensitive.
It means that Username and username are the same cookies.
* Value – string value of the cookie. It must be URL-encoded.
* Domain – a domain for which the cookie is valid.
* Path – path without the domain for which the cookie should be sent to the server.
For example, you can specify that the cookie is accessible only from the https://www.javascripttutorial.net/dom/ so pages at https://www.javascripttutoiral.net/ won’t send the cookie information.
* Expiration – timestamp that indicates when the web browser should delete the cookie (or when the browser should stop sending the cookie to the server).
The expiration date is set as a date in GMT format: Wdy, DD-Mon-YYYY HH:MM:SS GMT.
The expiration date allows the cookies to be stored in the user’s web browsers even after users close the web browsers.
* Secure flag – if specified, the web browser only sends the cookie to the server only via an SSL connection (https, not http)

The name, value, domain, path, expiration, and secure flag are separated by a semicolon and space. 
For example:
```
HTTP/1.1 200 OK
Content-type: text/html
Set-Cookie:user=john
; expire=Tue, 12-December-2030 12:10:00 GMT; domain=javascripttutorial.net; path=/dom; secure
...
```

**CORS**

This mechanism only exists in the browser. 
It doesn't exist on a server machine.
This mechanism in the browser can be turned off.
* An origin is a scheme (protocol), host (domain) and port
    * E.g. https://www.example.com (implied port is 443 for HTTPS, 80 for HTTP)
        * Scheme: HTTPS
        * Host: www.example.com
        * Port: 443
* CORS means Cross-Origin Resource sharing, so we're going to be getting resources from a different origin.
* Allows making requests only if the other origin specifically allows other origins to make requests to it
    * Same origin: http://example.com/app1 & http://example.com/app2
    * Different origin: http://example.com & http://other.example.com
* The requests won't be fulfilled unless the other origin allows for the requests, using CORS headers (ex: Access-Control-Allow-Origin)
* An OPTIONS request is sent to ask if CORS requests are allowed
  ![diagram](./images/cors-example.JPG)
* If a client does a cross-origin request on our S3 bucket, we need to enable the correct CORS headers
* You can allow for a specific origin or for * (all origins)
* The CORS definition is in a separate block in the bucket configuration.
  Make sure that there isn't a trailing slash for the domain.