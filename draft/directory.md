# The `_spaspec` directory

## Files within the `_spaspec` directory

While files inside `_spaspec` SHOULD use LF line endings, implementations MUST be capable of handling files with either LF or CRLF line endings.

## Serving sites with a `_spaspec` directory

When a `_spaspec` directory is present, implementations MUST follow any rules specified in [`_spaspec/routes`][routes], including the implicit default rules (even if no `_spaspec/routes` file is present). This includes responding to a request for any directory without an `index.html` as a 404 response rather than generating an index page.

[routes]: files/routes.md

While implementations MAY alter the contents of the site directory at build time to achieve the specified behavior for the server's internal implementation, server implementations MUST NOT alter the contents of the site directory at runtime (ie. when hosting).

Implementations MUST respect ONLY the `_spaspec` directory under the root of the site. `_spaspec` directories anywhere else in the tree are treated exclusively as ordinary directories of ordinary files.
