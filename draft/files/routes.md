# The `_spaspec/routes` file

The `_spaspec/routes` file defines behaviors for responding to requests for paths matching specified patterns.

## Routing behavior

Requests are matched against a series of rules: the first rule that matches the incoming request is used to respond to it. In this way, rules MUST be authored from most-specific to most-general. This allows higher-concatenated routes to override fallback routes specified through concatenation (see "Default behaviors" below).

## Route specification lines

A route specification is a space-separated line (where "space" is defined as a sequence of one or more unescaped space or horizontal tab characters). The first field on a route line is a path to match incoming requests against. The second field is the HTTP status code for the type of response that should be served for that request. Successive fields specify the URL and/or file path to respond with (depending on the type of response status).

Routes without a leading slash in the path match are invalid.

Routes may contain globs, with `*` matching one or more of any character except `/`, and `**` matching zero or more of any character. More than two unescaped asterisks in a row is invalid.

Routes may include query strings. These query strings are matched against the keys of a request's query string. A query string with a glob in a key is invalid.

Response paths may include numeric references to the globs in the path, as the character `$` followed by one **or more** unescaped digits referring to a glob in the matching path (starting with `$1` for the first glob). References to glob indexes beyond the range of actual globs will be replaced with the empty string. `$0` will be replaced with the entire matched path.

Route lines may escape any single character with a backslash (`\`), causing the following character to be interpreted literally. This may be used to include literal `*` characters in a matching path, literal `$` characters in a response content path (or literal digits after a matched glob, eg. `$1\00` for the content of the first glob followed by "00"), or literal spaces / tabs in a content path.

## Route behavior by response class

Routes specifying 1XX responses MUST be ignored, since HTTP responses with 1XX statuses are protocol-specific and not within the realm of content.

Routes specifying 2XX, 4XX, or 5XX responses specify the file to read for response content as their third field. If no third field is defined, the server MUST serve the default page/content for that status code (from [the corresponding status page within `_spaspec`][status files], if present).

[status files]: status-files.md

Routes specifying 3XX responses specify the path or URL to redirect to as their third field. The behavior of the fourth field is currently undefined and should not be used.

Rules that specify files only match if the file they specify exists. If the specified file does not exist (including if it is a directory),

## Other lines

Lines beginning with a `#` are to be treated as comments, and MUST not impact the behavior of the app. Comment lines MAY be included by implementations that convert `_spaspec/routes` to a native configuration file, as comments in that configuration file's native comment syntax.

Implementations MUST ignore any lines that are invalid or otherwise unhandled, such as lines with a non-numeric status. Implementations MAY give warnings for statuses they do not recognize, but lines beneath the invalid status MUST be included.

## Default behaviors

```
/_** 404
/**/index.html 301 /$1/
/**/ 200 $1/index.html
/**/* 200 $1/$2.html
/** 200 $1
/**/* 301 /$1/$2/
/** 200 _spaspec/200.html
/** 404 _spaspec/404.html
/** 404
```

## Implementation

Implementations MUST 301 redirect any request for a path containing `/../` to the corresponding path with the preceding level removed. Implementations MUST NOT respond to requests for paths containing `/../` with content from the corresponding path on the system (ie. breaking out of the site root).

Implementations MAY discard rules that can never be matched due to a higher-specified rule.

Implementations SHOULD reevaluate 3XX redirects internally to determine a final status and location for the request, collapsing all intermediate redirects to the final location (and applicable status) so as to reduce traffic and avoid user agents' limits on redirect following (most browsers will refuse to follow more than five successive redirects).

Implementations that generate static file hierarchies without the ability to specify more complex routing behaviors SHOULD link any files specified by 200 rules where the target filename incorporates a complete match (eg. a rule specifying `/foo/bar/*/about 200 foobars/$1.html` should link every `.html` file in `foobars` as an `about` file in a drectory with the file's name under `/foo/bar`).
