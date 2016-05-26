# `_spaspec` explained

## What's all this then?

This is a standard format for defining static-file HTTP server behavior, the kind you need when you're building what's commonly known today as a "[Single-page application][]" consisting of static files.

While a plain respond-to-requests-with-the-matching-path-under-this-directory hosting is enough to handle the *fundamentals* of static site building, the fact remains that most apps need features like redirects, internal URL rewrites, custom headers, and custom error pages. Sometimes, this is just for cosmetic purposes, implementing [cool URLs that don't change][URIs] instead of exposing the guts of the implementation file structure. Other times, it's absolutely *essential*, for implementing headers needed by a core component like [CORS][].

However, despite how necessary these features are, they've remained tied into server-specific config files like [Apache's XML-based `httpd.conf` / hidden `.htaccess`][apache] and Nginx's [nginx.conf][nginx]. Writing for either one of these entails learning a lot of ugly, fragile, non-portable directives (potentially spread across several locations), and requires a host that gives you access to the server's config files, and has the modules you need loaded (which usually boils down to running your own dedicated VPS, of one kind or another).

While there has been a proliferation of good static-file-hosting providers in the last few years, most of these haven't gone further than allowing the user to upload a file named "404.html" to be served as a custom error page when the server can't find a file at the given path. While you can use this file for some [ugly][1] [hacks][2], faking proper request routing after the error page has loaded, it's clumsy, it's [HTTP-breaking][], and it's still not enough for applications that need special headers.

## Enter the Static Page Application Specification Standard

The `_spaspec` directory in a site's root tells the server a few things. For one, it tells it that, like `<!DOCTYPE html>`, we want to *opt out of the implementation's quirks*, in favor of a set of straightforward, sensible behaviors for file serving:

- All requests for paths containing `/../` (or `/./` or `//`) get a redirect response to the normalized path: no [directory traversal attacks][] here
- No executable files (like `.php` or `cgi-bin`) - if we needed executables, we wouldn't have made a *static* page application
- No generating a "directory listing" page whenever a directory with no `index.html` is requested. If the URL doesn't point to a readable resource, the response should be Not Found.
- No falling back to IIS-only filenames like `default.html` or `index.htm`.

It also contains a set of files for defining app-specific behavior:

- `routes`, defining redirects and rewrites
- `headers`, defining custom headers
- `hostname`, defining the name of the virtual host

You can also add default pages for any status code returned by the site. Want to make all paths return a specific page with a `200 OK` status code? Just add that page as `_spaspec/200.html`. Want to include a specific error users will see if they try to POST over one of your pages? Add `_spaspec/405.html`.

We're (I'm) [looking for feedback][issue #1] to keep this from being [just another competing format][xkcd 927]. From experience, it *is* possible to make one common standard, which results in a solid, portable base once the spec firms up and the implementations get going (see: USB, Wi-Fi, Unicode, HTML5) - it just takes a firm understanding of the problem space.

[Single-page application]: https://en.wikipedia.org/wiki/Single-page_application
[URIs]: https://www.w3.org/Provider/Style/URI.html
[CORS]: https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS
[xkcd 927]: https://xkcd.com/927/
[apache]: https://httpd.apache.org/docs/current/configuring.html
[nginx]: http://nginx.org/en/docs/example.html
[1]: http://www.backalleycoder.com/2016/05/13/sghpa-the-single-page-app-hack-for-github-pages/
[2]: https://github.com/rafrex/spa-github-pages
[HTTP-breaking]: http://racksburg.com/choosing-an-http-status-code/
[Directory traversal attack]: https://en.wikipedia.org/wiki/Directory_traversal_attack
[issue  #1]: https://github.com/spaspec/spaspec-standard/issues/1
