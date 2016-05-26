# The `_spaspec/headers` file

Each line beginning with a `/` specifies a route to match requests against. If a request's path matches the route, all the headers specified from the first header following the route to the next specified route MUST be applied to the response, as detailed below.

## Applying response headers

Implementations MUST include headers with unrecognized names (but otherwise valid content) verbatim.

If a header has a recognized name that specifies a behavior the implementation is capable of following, such as `Content-Encoding`, the response SHOULD be altered to fit that header if specified: if the response is not altered to match the specified header, the specified header MUST NOT be included in the response.

*Note: the list of standard headers that implementations should regard with special treatment should probably be listed here, explicitly and exhaustively.*

Implementations MUST ignore the following standard headers, which would conflict with normal HTTP operations:

- `Connection`
- `Content-Length`
- `Content-MD5` (obsolete anyway)
- `Content-Range`
- `Date`
- `Location` (use [`_spaspec/routes`][routes] instead)
- `Trailer`
- `Upgrade`

[routes]: routes.md
