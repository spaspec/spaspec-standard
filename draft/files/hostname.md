# The `_spaspec/hostname` file

Every line in `_spaspec/hostname` specifies the name of a "virtual host" the site should respond to requests for. Each successive name specified on the line (separated by one or more space characters) will redirect to the initial name on that line.

Names beginning with `https://` will always redirect to the HTTPS version of the specified name at the beginning of the line: other names will preserve the protocol of the original request (`http://` or `https://`).

Invalid host names are ignored.

Lines beginning with # are treated as comments and ignored.

If present, implementations SHOULD use this file for matching virtual hosts to their corresponding sites, to the greatest extent possible. (For instance, a toolbelt for a service that manages hostnames separately from data in the code repository could prompt the user to associate the application with the domains specified in `_spaspec/hostname`.)

## Recommended extension

Implementations SHOULD handle requests for domains both with and without `www.` prefixes. If only one of these names is specified in `hostname`, the implementation SHOULD redirect requests for the non-specified variant to the specified variant.
