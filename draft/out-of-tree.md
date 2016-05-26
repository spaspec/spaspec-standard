# Special files outside of `_spaspec`

This document lists some special global files defined by static site implementations before `_spaspec`, and how implementations respecting `_spaspec` should handle these prior conventions.

In the presence of a recognized `_spaspec` directory, if any of these files are also present, server implementations MUST follow the [default routing rules][routes], which respond to requests for these files like any other file. Although the most [DRY][] approach, for systems that don't natively recognize `_spaspec`, is to derive these files at build time from the definitions in `_spaspec`, authors MAY choose to maintain them, separately, as part of their tree, for cross-compatibility with non-`_spaspec`-respecting hosts, in which case they should include rules in `_spaspec/routes` to hide them when `_spaspec` is active:

```
/CNAME 404
/ROUTER 404
/200.html 404

# by default, 404.html will be served as 200 OK
/404.html 404

# Underscore-prefixed paths like _redirects and _headers are hidden by default
```

## `404.html`

**Used by*: GitHub Pages, Surge, Netlify

This is the same as `_spaspec/404.html`. See [the documentation on status pages][status pages].

## `CNAME`

**Used by**: GitHub Pages, Surge

A `CNAME` file in the root of a site specifies a hostname to serve the site in response to requests for. It is similar to [`_spaspec/hostname`][hostname], except that it doesn't support more than one name.

Like `_spaspec/hostname`, Surge supports including `https://` before the hostname to enforce upgrading to HTTPS (it also supports `http://` for *downgrading* to HTTP, instead of leaving HTTPS connections secure as `_spaspec/hostname` dictates). This extension is not supported by GitHub Pages, which only accepts the domain name. See [GitHub's documentation](https://help.github.com/articles/setting-up-your-pages-site-repository/).

## `200.html`

**Used by*: Surge

This is the same as `_spaspec/200.html`. See [the documentation on status pages][status pages].

## `ROUTER`

**Used by**: Surge (Plus)

A `ROUTER` file is like the [`_spaspec/routes`][routes] file, with a few differences:

- Only 3XX redirects are accepted.
- The status code is specified first, and the matching path is specified second.
- Path matching uses named parameters instead of numbered captures.

See [the documentation](https://surge.sh/help/adding-redirects).

## `_redirects`

**Used by**: Netlify

A `_redirects` file is like the [`_spaspec/routes`][routes] file, with a few differences:

- The status code is specified last, after the destination.
- Path matching uses named parameters instead of numbered captures. (The `*` is referred to as `:splat` in the destination.)
- Query strings are specified as a separate, optional field after the matching path.
- Routes match explicit file locations in the tree before routing, unless an exclamation point is appended to the status code (ie. `200!`).
- `200` routes support serving remote files as a proxy (see [issue #7][]).

See [the documentation](https://www.netlify.com/docs/redirects).

[DRY]: https://en.wikipedia.org/wiki/Don%27t_repeat_yourself
[routes]: files/routes.md
[hostname]: files/hostname.md
[status pages]: files/status-pages.md
[issue #7]: https://github.com/spaspec/spaspec-standard/issues/7
