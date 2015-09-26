# WordPress HTTP/2 Integration

WordPress core has an opportunity to provide HTTP/2 optimizations for sites, themes, and plugins that wish to support it. In this document, I will outline a plan for how WordPress might facilitate and promote a HTTP/2 optimizations in environments in which that would be beneficial.

HTTP/2 optimization in WordPress breaks down into two concerns:

1. Detecting HTTP/2
2. Enabling individual optimizations

Each of these concerns are addressed separately below.

## Protocol sniffing

In order to build a page with HTTP/2 optimizations, the WordPress application must have the following knowledge:

1. The protocol being used by the client making the request
2. The protocols supported by the current server running the WordPress application, or any proxy servers handling the request in front of the WordPress application server

If these two pieces of information are known, WordPress will know whether or not to serve the HTTP/2 optimized version of the site as show in Figure 1.

---

*Figure 1: HTTP/2 Decision Matrix*

|                 | HTTP/1.1 Server | HTTP/2 Server |
| :-------------: | :-------------: | :-----------: |
| HTTP/1.1 Client | HTTP/1.1        | HTTP/1.1      |
| HTTP/2 Client   | HTTP/1.1        | HTTP/2        |

---

Much like `is_ssl()`, an `is_http2()`-esque function will help WordPress core, as well as plugin and theme authors, make decisions about page construction based on the negotiated protocol. For instance, a theme author could decide to deliver non-bundled resources when `is_http2()` is true, while falling back to bundles when `is_http2()` is false. In fact, there could be a future where this is handling within the "enqueues" API itself. I use `is_http2()` here only for sake of argument. I discuss the actual functions below.

### Protocol Awareness

**Client Protocol**

The success of this API depends on WordPress being able to understand the context of the current request. A common HTTP request looks something like:

```
GET / HTTP/1.1
Host: http2.wphttp.com
accept-encoding: gzip, deflate, sdch
accept-language: en-US,en;q=0.8
user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_10_2) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/43.0.2357.81 Safari/537.36
accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8
authority: http2.wphttp.com
```

This example was recorded from a Chrome 43 request. The HTTP protocol in the request is denoted at the beginning of the HTTP request. In our example, the first line, `GET / HTTP/1.1`, clearly shows that the client is requesting the resource with HTTP/1.1.

PHP's [`$_SERVER`](http://php.net/manual/en/reserved.variables.server.php) super global contains the request protocol in the `SERVER_PROTOCOL` index (*note that although it's titled `SERVER_PROTOCOL` it is truly the protocol used by the client*). It will produce values such as `HTTP/1.1`. This is a reliable method of getting the client protocol request; however, this isn't without some complications.

Many WordPress setups are behind a wall of proxies. Some might use edge networks, varnish layers, or other reverse proxies to handle a request. The value produced by `$_SERVER['SERVER_PROTOCOL']` will depend on the HTTP protocol used between the proxy server and the WordPress application server. Let's look at some examples.

* Non-proxied WordPress

    HTTP/2 Request -> WordPress Application server = HTTP/2

* Proxied WordPress

    HTTP/2 Request -> Nghttpx Request Via HTTP/1.1 -> Varnish Request Via HTTP/1.1 -> WordPress Application Server = HTTP/1.1

While PHP produces reliable `$_SERVER['SERVER_PROTOCOL']` results, reverse proxy scenarios can certainly confuse the matter. That said, this is easily handled by the site administrator by passing header information containing the initiating HTTP protocol. An analogous situation is handling HTTP vs. HTTPS requests using one of the many headers that proxies send. There has been [ample discussion](https://core.trac.wordpress.org/search?q=x-forwarded-proto) of this TLS issue in core and adding support for these headers has been [strongly argued against](https://core.trac.wordpress.org/ticket/31288). Given the highly custom nature of proxied setups, we should provide documentation to handle this scenario (and perhaps some debug plugins) instead of baking this into core itself.

It seems practical to be able to get the client protocol when handling requests to WordPress, with the only complication being handling proxies.

**Server Protocol**

The protocols supported by the server are harder to determine. As far as I am aware, there is no function in PHP that can get this information. This information would need to be provided via the site administrator. Only the site admin would know what versions of HTTP the site actually supports.

Not having WordPress be able to automatically detect this is frustrating as it introduces a rather complex configuration step. The administrator now needs to understand what an HTTP protocol is, then go about figuring out which ones her server supports. And to make this more difficult, she needs to be aware of how proxies will impact this decision (e.g., WordPress application server runs Nginx, which support HTTP/2, but you actually are connecting to the client using Super Awesome CDN, which does not support HTTP/2).

Alternatively, I could envision a plugin that can generate the configuration information needed. We could use cURL (i.e., WP_Http) requests to test different protocol support. The biggest problem here is that cURL does not likely have vast HTTP/2 support in the real world. As an alternative to the alternative, we could set up a service to test protocol support. We'd integrate this with a WordPress plugin that can grab info from that service and produce a config value for the admin to use.

Unless we can find a simple way to do this with PHP, we have to settle on a clunky admin configuration step to advertise server support.

### Other Considerations

*This is not only about HTTP/2*

In building this API, we need to be future oriented to ensure that this API does not limit us when new protocols are developed. There are already rumblings about HTTP/3 (and QUIC is a hot topic) and we should be in a place where we can easily provide HTTP/3 optimizations without having to reimagine this API. While this document is speaking in terms of HTTP/2 and HTTP/1.1 only, these protocols are merely examples and the resulting API should be capable of handling new protocols.

*Caching implications*

A cornerstone of a scalable and performant WordPress deployment is to use a page cache for the output of the WordPress applications work. After an initial request, subsequent requests are retrieved from cache and do not invoke a WordPress load.

In a world where we generate pages differently based on negotiated protocol, we need to make sure that different variants of the page are cached depending on client protocol. Fortunately, we should be able to take advantage of the [Vary](http://tools.ietf.org/html/rfc7231#section-7.1.4) caching mechanics to help with this (*note: I'm not certain how this can work with different HTTP protocols, but someone out there has to be working on this*).

### Mechanics

Given some of the ideas above, let's look at what this might look like in code to determine what protocol a server and client are using.

```php
function wp_get_default_protocol() {
    $protocol = 'http/1.1';

    if ( is_defined( 'DEFAULT_HTTP_PROTOCOL' ) ) {
        $protocol = DEFAULT_HTTP_PROTOCOL;
    }

    return $protocol;
}

function wp_get_server_protocols() {
    // These are defined in wp-config.php
    global $server_protocols;
    return $server_protocols;
}

function wp_get_request_client_protocol() {
    $protocol = '';

    if ( isset( $_SERVER['SERVER_PROTOCOL'] ) ) {
        $protocol = $_SERVER['SERVER_PROTOCOL'];
    }

    return $protocol;
}

function wp_get_negotiated_request_protocol() {
    $negotiated_protocol = wp_get_default_protocol();
    $request_protocol    = wp_get_request_client_protocol();
    $supported_protocols = wp_get_server_protocols();

    if ( in_array( $request_protocol, $supported_protocols ) ) {
        $negotiated_protocol = $request_protocol;
    }

    return $negotiated_protocol;
}

function is_http_protocol( $version ) {
    return strtolower( $version ) === strtolower( wp_get_negotiated_request_protocol() );
}
```

Themes, plugins, and core itself can then make decisions about how to generate the page based on protocol used:

```php
add_action( 'wp_enqueue_scripts', function() {
    if ( true === is_http_protocol( 'http/2' ) ) {
        // Enqueue a lot of small scripts
    } else {
        // Enqueue one concatenated script
    }
} );
```

Note that this code would allow the administrator to have control over what exactly the application will support. By altering `$server_protocols` and `DEFAULT_HTTP_PROTOCOL`, she can easily configure an HTTP/1.1 or HTTP/2-only application. It also allows for easy configuration for future protocols.

## Optimizations

HTTP/2 is fully backwards compatible with HTTP/1.1. It promises performance improvements through addressing key latency problems with previous versions of HTTP. While WordPress will Just Work&#174; with HTTP/2, optimizations can increase performance.

HTTP/2 marks a sea change in the way web developers have built sites in the past. Practices that we've considered "best practices" in the past become anti-patterns in HTTP/2. Rather than look at HTTP/2 as one big optimization, we can break down application optimizations for HTTP/2 that we can implement to make the most of HTTP/2. I will break down a few optimizations and suggest some paths towards implementing them.

### Unbundled resources

Probably the most talked about and well understood optimization for HTTP/2 is unbundling resources. Web developers have grown accustomed to bundling resources to save HTTP requests to work around head-of-line blocking issues and lack of multiplexing in HTTP/1.1. Resource bundling includes, script and CSS file concatenation, image spriting and resource inlining. In HTTP/2, each of these practices become an anti-pattern and best performance and reliability come from serving each resource individually. Fortunately, this means web developers get to stop doing the cumbersome build tasks needed for resource inlining.

When we think about unbundling resources in WordPress for HTTP/2 optimization, we are essentially asking developers to provide un-concatenated resources along side concatenated resources so either can be served depending on the request protocol being used. There are many mechanisms that can be used to handle this. Let's explore some here.

**`wp_enqueue_*` methods**

Perhaps one simple way of doing enqueueing one set of resources or another would be to add data to scripts and styles handled by the `wp_enqueue_script` and `wp_enueue_style` functions. Imagine if you add extra context data to an enqueue and WordPress can chose which scripts/styles to display depending on that context. For instance:

```php
add_action( 'wp_enqueue_scripts', function() {
    wp_enqueue_script(
        'my-bundled-resource',
        'http://www.example.org/path/to/resource.js',
        array( 'jquery' ),
        '1.3.4',
        true
    );

    wp_script_add_data(
        'my-bundled-resource',
        'protocol',
        array(
            'http/1.1',
            'http/1.0',
        )
    );

    wp_enqueue_script(
        'my-unbundled-resource-1',
        'http://www.example.org/path/to/resource-1.js',
        array( 'jquery' ),
        '1.3.4',
        true
    );

    wp_script_add_data(
        'my-unbundled-resource-1',
        'protocol',
        array(
            'http/2',
        )
    );

    wp_enqueue_script(
        'my-unbundled-resource-2',
        'http://www.example.org/path/to/resource-2.js',
        array( 'jquery', 'my-unbundled-resource-1' ),
        '1.3.4',
        true
    );

    wp_script_add_data(
        'my-unbundled-resource-2',
        'protocol',
        array(
            'http/2',
        )
    );
} );
```

The WP Enqueues API would load the necessary resources depending on the current protocol. If the current protocol is HTTP/1.1, only `my-bundled-resource` would be loaded. If the current protocol is HTTP/2, both `my-unbundled-resource-1` and `my-unbundled-resource-2` would be loaded. This method allows plugin and theme developers to make WordPress aware of the resources available for different contexts and allow WordPress (or the administrator) decide which resources to load.

This method is merely an initial suggestion and I think it could work well, although the "enqueue-then-add-data" workflow is annoying. Perhaps we could have a wrapper function?

Another idea would be to add another argument to the *enqueue* functions for specifying contexts. There are a lot of contextual reasons for a script/stylesheet loading or not loading. It might be interesting to explore adding another argument to the enqueue functions that allow you to set contexts (e.g., load one script for HTTPS and another for HTTP). For instance:

```php
add_action( 'wp_enqueue_scripts', function() {
    wp_enqueue_script(
        'my-bundled-resource',
        'http://www.example.org/path/to/resource.js',
        array( 'jquery' ),
        '1.3.4',
        true,
        array(
            'http-protocol' => 'http/1.1',
        )
    );

    wp_enqueue_script(
        'my-unbundled-resource-1',
        'http://www.example.org/path/to/resource-1.js',
        array( 'jquery' ),
        '1.3.4',
        true,
        array(
            'http-protocol' => 'http/2',
        )
    );

    wp_enqueue_script(
        'my-unbundled-resource-2',
        'http://www.example.org/path/to/resource-2.js',
        array( 'jquery', 'my-unbundled-resource-1' ),
        '1.3.4',
        true
        array(
            'http-protocol' => 'http/2',
        )
    );
} );
```

I think this is an interesting concept. This makes it really easy to conditionally load scripts based on a context. We could have a number of "core contexts" that are supported out of the box with appropriate filtering to allow for additional contexts should they be needed. I think this is forward thinking as there are other contexts that we could easily add should the need arise. We could also use this to register scripts/styles that are enqueued only when a shortcode is used on a page. There are a lot of possibilities with this[^1].

[^1]: Imagine how this could get us past the rather annoying code that has us first `wp_register_script` a script then use `wp_enqueue_script` later if some condition is matched. We could instead place the condition in the last parameter of the `wp_register_script` call and it would be enqueued properly.

**Other unbundled resources**

When resources are not loaded via WordPress' Enqueues API, we need to have another method for loading resources conditionally.  I think this is as easy as using the functions discussed above. For instance:

```php
if ( true === is_http_protocol( 'http/2' ) ) {
    // unbundled resource
} else {
    // bundled resource
}
```

I do not see a need to create a separate function for this purpose as I think the previously discussed functions will handle it quite well. If there are any special classes of unbundled resources that we need to handle, we could create special functions for that.

### Server push

Server push is one of the more exciting features that HTTP/2 offers and it does not have a correlate in HTTP/1.1. Server push addresses the problem of browsers having to discover resources within a parsed document before they are requested and handled. For example, in HTTP/1.1, a browser requests the HTML for a page, parses the document, discovers the CSS resource and makes a request for the resource. The discovery process is time consuming and slows the initial rendering of the site. HTTP/2 addresses this by offering server push. Server push allows a resource to advertise a required subresource earlier in the process allowing the browser to begin retrieval of the subresource more quickly. For instance, the browser would request the HTML and the server would respond with a special header advertising the CSS subresource, thus kicking off the download of the CSS almost immediately.

In HTTP/1.1, web developers have embedded important subresources  in HTML documents to essentially "push" them out with the parent resource. The problem with this approach is that you end up pushing this inlined subresource with every single request. There is no concept of caching this subresource without cumbersome JS work arounds. HTTP/2's support of server push is an excellent feature that allows us to get around this limitation.

A "pushed" subresource is advertised in a response header from the parent resource. As an example, this page pushes the main CSS for the site:

```
~ ❯❯❯ curl -svo /dev/null https://www.tollmanz.com/wp-kses-performance/
...
> GET /wp-kses-performance/ HTTP/1.1
> User-Agent: curl/7.37.1
> Host: www.tollmanz.com
> Accept: */*
>
< HTTP/1.1 200 OK
< Date: Sat, 26 Sep 2015 21:15:32 GMT
< Content-Type: text/html; charset=UTF-8
< Content-Length: 19352
< Last-Modified: Wed, 12 Aug 2015 01:42:49 GMT
< Etag: "55caa499-4b98"
< Expires: Sun, 27 Sep 2015 09:15:32 GMT
< Cache-Control: max-age=43200
< Link: </css/main.css>; rel=preload; as=stylesheet
...
<
{ [data not shown]
* Connection #0 to host www.tollmanz.com left intact
```

In WordPress we'd need a mechanism to set the `Link` header. Setting headers in WordPress is trivial. Depending on the resource, I think we have a few different options for pushing resources.

**Non-script/style resources**

In pure PHP, pushing a resource would look like:

```php
header( 'Link: </wp-content/themes/thirty-thirtysix/images/logo.png>; rel=preload; as=image' );
```

As long as this code is called before the response begins, the header will be sent successfully.

I'm hesitant to suggest building a wrapper function for this. The semantics of the `Link` header are likely to change as web developers start working with server push and changes are needed. I'd hate to paint ourselves into a corner with an API that doesn't work for that long or constantly needs updates.

**Scripts and styles**

Since we have an API for adding scripts and styles, it makes sense to build in a solution for pushing via the Enqueues API. Before we get to that, let's discuss one concern with this.

The accepted best practice for adding scripts and styles to WordPress is via the `wp_enqueues_*` functions hooked onto the *`wp_enqueue_scripts` hook*. For server push, this is a problem, primarily because this hook runs *after HTML is already sent to the browser*. Since WordPress does not have awareness of whether or not a script will be added to the current request until after the headers are sent, it cannot send the `Link` header. It's too late.

There are some nasty workarounds for this issue (e.g., running the `wp_enqueue_scripts` and related hooks early to discover what will be called) that are not advisable. As such, we might  need a mechanism for identifying a script/style that should be pushed. The condition for the push should be satisfied by the time that the `init` hook is completed.

At this time, I don't have a good solution for this. We could always add it as a normal header and not tie it into the enqueues mechanism, but I think we'd be missing a good opportunity to work it into something that the community is very familiar with already.

### Other HTTP/2 optimizations

The two other interesting HTTP/2 optimizations are prioritization and domain sharding.

HTTP/2 enables servers to have more influence over the prioritization of subresources; however, this involves a careful negotiation between the server and client. At this point, I am not aware of any server that allows you to custom configure this, so I do not think there is much we can do here.

Domain sharding is a practice that web developers adopted to get past HTTP/1.1's lack of multiplexing. It allows you to open more connections to your server to serve subresources in parallel. Fortunately, WordPress does not do any sharding by default so there is nothing for us to do here.

## What say you?

This is a starting point for discussion. I really want to hear what everyone thinks about these ideas, as well as everyone else's ideas. I think that the actual code implementation of these features will be relatively easy. The tough part is coming up with a forward thinking plan that sets up WordPress to handle HTTP/2 and beyond. Let's get this right.
