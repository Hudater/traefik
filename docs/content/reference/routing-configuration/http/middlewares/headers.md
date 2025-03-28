---
title: "Traefik Headers Documentation"
description: "In Traefik Proxy, the HTTP headers middleware manages the headers of requests and responses. Read the technical documentation."
---

![Headers](../../../../assets/img/middleware/headers.png)

The Headers middleware manages the headers of requests and responses.

By default, the following headers are automatically added when proxying requests:

| Property                  | HTTP Header                |
|---------------------------|----------------------------|
| Client's IP               | X-Forwarded-For, X-Real-Ip |
| Host                      | X-Forwarded-Host           |
| Port                      | X-Forwarded-Port           |
| Protocol                  | X-Forwarded-Proto          |
| Proxy Server's Hostname   | X-Forwarded-Server         |

## Configuration Examples

### Adding Headers to the Request and the Response

The following example adds the `X-Script-Name` header to the proxied request and the `X-Custom-Response-Header` header to the response

```yaml tab="Structured (YAML)"
http:
  middlewares:
    testHeader:
      headers:
        customRequestHeaders:
          X-Script-Name: "test"
        customResponseHeaders:
          X-Custom-Response-Header: "value"
```

```toml tab="Structured (TOML)"
[http.middlewares]
  [http.middlewares.testHeader.headers]
    [http.middlewares.testHeader.headers.customRequestHeaders]
        X-Script-Name = "test"
    [http.middlewares.testHeader.headers.customResponseHeaders]
        X-Custom-Response-Header = "value"
```

```yaml tab="Labels"
labels:
  - "traefik.http.middlewares.testHeader.headers.customrequestheaders.X-Script-Name=test"
  - "traefik.http.middlewares.testHeader.headers.customresponseheaders.X-Custom-Response-Header=value"
```

```json tab="Tags"
{
  //...
  "Tags": [
    "traefik.http.middlewares.testheader.headers.customrequestheaders.X-Script-Name=test",
    "traefik.http.middlewares.testheader.headers.customresponseheaders.X-Custom-Response-Header=value"
  ]
}

```

```yaml tab="Kubernetes"
apiVersion: traefik.io/v1alpha1
kind: Middleware
metadata:
  name: test-header
spec:
  headers:
    customRequestHeaders:
      X-Script-Name: "test"
    customResponseHeaders:
      X-Custom-Response-Header: "value"
```

### Adding and Removing Headers

In the following example, requests are proxied with an extra `X-Script-Name` header while their `X-Custom-Request-Header` header gets stripped,
and responses are stripped of their `X-Custom-Response-Header` header.

```yaml tab="Structured (YAML)"
http:
  middlewares:
    testHeader:
      headers:
        customRequestHeaders:
          X-Script-Name: "test" # Adds
          X-Custom-Request-Header: "" # Removes
        customResponseHeaders:
          X-Custom-Response-Header: "" # Removes
```

```toml tab="Structured (TOML)"
[http.middlewares]
  [http.middlewares.testHeader.headers]
    [http.middlewares.testHeader.headers.customRequestHeaders]
        X-Script-Name = "test" # Adds
        X-Custom-Request-Header = "" # Removes
    [http.middlewares.testHeader.headers.customResponseHeaders]
        X-Custom-Response-Header = "" # Removes
```

```yaml tab="Labels"
labels:
  - "traefik.http.middlewares.testheader.headers.customrequestheaders.X-Script-Name=test"
  - "traefik.http.middlewares.testheader.headers.customrequestheaders.X-Custom-Request-Header="
  - "traefik.http.middlewares.testheader.headers.customresponseheaders.X-Custom-Response-Header="
```

```json tab="Tags"
{
  "Tags" : [
    "traefik.http.middlewares.testheader.headers.customrequestheaders.X-Script-Name=test",
    "traefik.http.middlewares.testheader.headers.customrequestheaders.X-Custom-Request-Header=",
    "traefik.http.middlewares.testheader.headers.customresponseheaders.X-Custom-Response-Header="
  ]
}
```

```yaml tab="Kubernetes"
apiVersion: traefik.io/v1alpha1
kind: Middleware
metadata:
  name: test-header
spec:
  headers:
    customRequestHeaders:
      X-Script-Name: "test" # Adds
      X-Custom-Request-Header: "" # Removes
    customResponseHeaders:
      X-Custom-Response-Header: "" # Removes
```

### Using Security Headers

Security-related headers (HSTS headers, Browser XSS filter, etc) can be managed similarly to custom headers as shown above.
This functionality makes it possible to easily use security features by adding headers.

```yaml tab="Structured (YAML)"
http:
  middlewares:
    testHeader:
      headers:
        frameDeny: true
        browserXssFilter: true
```

```toml tab="Structured (TOML)"
[http.middlewares]
  [http.middlewares.testHeader.headers]
    frameDeny = true
    browserXssFilter = true
```

```yaml tab="Labels"
labels:
  - "traefik.http.middlewares.testHeader.headers.framedeny=true"
  - "traefik.http.middlewares.testHeader.headers.browserxssfilter=true"
```

```json tab="Tags"
{
  "Tags" : [
    "traefik.http.middlewares.testheader.headers.framedeny=true",
    "traefik.http.middlewares.testheader.headers.browserxssfilter=true"
  ]
}

```

```yaml tab="Kubernetes"
apiVersion: traefik.io/v1alpha1
kind: Middleware
metadata:
  name: test-header
spec:
  headers:
    frameDeny: true
    browserXssFilter: true
```

### CORS Headers

CORS (Cross-Origin Resource Sharing) headers can be added and configured in a manner similar to the custom headers above.
This functionality allows for more advanced security features to quickly be set.
If CORS headers are set, then the middleware does not pass preflight requests to any service,
instead the response will be generated and sent back to the client directly.  
Please note that the example below is by no means authoritative or exhaustive,
and should not be used as is for production.

```yaml tab="Structured (YAML)"
http:
  middlewares:
    testHeader:
      headers:
        accessControlAllowMethods:
          - GET
          - OPTIONS
          - PUT
        accessControlAllowHeaders: "*"
        accessControlAllowOriginList:
          - https://foo.bar.org
          - https://example.org
        accessControlMaxAge: 100
        addVaryHeader: true
```

```toml tab="Structured (TOML)"
[http.middlewares]
  [http.middlewares.testHeader.headers]
    accessControlAllowMethods = ["GET", "OPTIONS", "PUT"]
    accessControlAllowHeaders = [ "*" ]
    accessControlAllowOriginList = ["https://foo.bar.org","https://example.org"]
    accessControlMaxAge = 100
    addVaryHeader = true
```

```yaml tab="Labels"
labels:
  - "traefik.http.middlewares.testheader.headers.accesscontrolallowmethods=GET,OPTIONS,PUT"
  - "traefik.http.middlewares.testheader.headers.accesscontrolallowheaders=*"
  - "traefik.http.middlewares.testheader.headers.accesscontrolalloworiginlist=https://foo.bar.org,https://example.org"
  - "traefik.http.middlewares.testheader.headers.accesscontrolmaxage=100"
  - "traefik.http.middlewares.testheader.headers.addvaryheader=true"
```

```json tab="Tags"
{
  "Tags" : [
    "traefik.http.middlewares.testheader.headers.accesscontrolallowmethods=GET,OPTIONS,PUT",
     "traefik.http.middlewares.testheader.headers.accesscontrolallowheaders=*",
    "traefik.http.middlewares.testheader.headers.accesscontrolalloworiginlist=https://foo.bar.org,https://example.org",
    "traefik.http.middlewares.testheader.headers.accesscontrolmaxage=100",
    "traefik.http.middlewares.testheader.headers.addvaryheader=true"
  ]
}
```

```yaml tab="Kubernetes"
apiVersion: traefik.io/v1alpha1
kind: Middleware
metadata:
  name: test-header
spec:
  headers:
    accessControlAllowMethods:
      - "GET"
      - "OPTIONS"
      - "PUT"
    accessControlAllowHeaders:
      - "*"
    accessControlAllowOriginList:
      - "https://foo.bar.org"
      - "https://example.org"
    accessControlMaxAge: 100
    addVaryHeader: true
```

## Configuration Options

!!! warning

    Custom headers will overwrite existing headers if they have identical names.

!!! note ""

    The detailed documentation for security headers can be found in [unrolled/secure](https://github.com/unrolled/secure#available-options).

| Field                         | Description                                       | Default   | Required |
| ----------------------------- | ------------------------------------------------- | --------- | -------- |
| `customRequestHeaders`          | Lists the header names and values for requests.  | [] | No |
| `customResponseHeaders`         | Lists the header names and values for responses. | [] | No |
| `accessControlAllowCredentials` | Indicates if the request can include user credentials.| false     | No |
| `accessControlAllowHeaders`     | Specifies allowed request header names.          | [] | No |
| `accessControlAllowMethods`     | Specifies allowed request methods.               | [] | No |
| `accessControlAllowOriginList`  | Specifies allowed origins. More information [here](#accesscontrolalloworiginlist)      | []      | No |
| `accessControlAllowOriginListRegex` | Allows origins matching regex. More information [here](#accesscontrolalloworiginlistregex)            | []      | No |
| `accessControlExposeHeaders`    | Specifies which headers are safe to expose to the API of a CORS API specification.       |  []    | No |
| `accessControlMaxAge`           | Time (in seconds) to cache preflight requests.   | 0         | No |
| `addVaryHeader`                | Used in conjunction with `accessControlAllowOriginList` to determine whether the `Vary` header should be added or modified to demonstrate that server responses can differ based on the value of the origin header. | false     | No |
| `allowedHosts`                  | Lists allowed domain names.                      | []      | No |
| `hostsProxyHeaders`             | Specifies header keys for proxied hostname.      | []      | No |
| `sslProxyHeaders`               | Defines a set of header keys with associated values that would indicate a valid HTTPS request. It can be useful when using other proxies (example: `"X-Forwarded-Proto": "https"`).        |   {}   | No |
| `stsSeconds`                    | Max age for `Strict-Transport-Security` header.    | 0         | No |
| `stsIncludeSubdomains`          | If set to `true`, the `includeSubDomains` directive is appended to the `Strict-Transport-Security` header.    | false     | No |
| `stsPreload`                    | Adds preload flag to STS header.                 | false     | No |
| `forceSTSHeader`                | Adds STS header for HTTP connections.            | false     | No |
| `frameDeny`                     | Set `frameDeny` to `true` to add the `X-Frame-Options` header with the value of `DENY`.                | false     | No |
| `customFrameOptionsValue`       | allows the `X-Frame-Options` header value to be set with a custom value. This overrides the `FrameDeny` option.  |    ""  | No |
| `contentTypeNosniff`            | Set `contentTypeNosniff` to true to add the `X-Content-Type-Options` header with the value `nosniff`.  | false     | No |
| `browserXssFilter`              | Set `browserXssFilter` to true to add the `X-XSS-Protection` header with the value `1; mode=block`.  | false     | No |
| `customBrowserXSSValue`         | allows the `X-XSS-Protection` header value to be set with a custom value. This overrides the `BrowserXssFilter` option.   | false | No |
| `contentSecurityPolicy`         | allows the `Content-Security-Policy` header value to be set with a custom value.           | false | No |
| `contentSecurityPolicyReportOnly` | allows the `Content-Security-Policy-Report-Only` header value to be set with a custom value.    |   ""  | No |
| `publicKey`                     | Implements HPKP for certificate pinning.         |  "" | No |
| `referrerPolicy`                | Controls forwarding of `Referer` header.         | "" | No |
| `permissionsPolicy`             | allows sites to control browser features.                   | ""      | No |
| `isDevelopment`                 | Set `true` when developing to mitigate the unwanted effects of the `AllowedHosts`, SSL, and STS options. Usually testing takes place using HTTP, not HTTPS, and on `localhost`, not your production domain.    | false     | No |

### `accessControlAllowOriginList`

The `accessControlAllowOriginList` indicates whether a resource can be shared by returning different values.

A wildcard origin `*` can also be configured, and matches all requests.
If this value is set by a backend service, it will be overwritten by Traefik.

This value can contain a list of allowed origins.

More information including how to use the settings can be found at:

- [Mozilla.org](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Access-Control-Allow-Origin)
- [w3](https://fetch.spec.whatwg.org/#http-access-control-allow-origin)
- [IETF](https://tools.ietf.org/html/rfc6454#section-7.1)

Traefik no longer supports the `null` value, as it is [no longer recommended as a return value](https://w3c.github.io/webappsec-cors-for-developers/#avoid-returning-access-control-allow-origin-null).

### `accessControlAllowOriginListRegex`

The `accessControlAllowOriginListRegex` option is the counterpart of the `accessControlAllowOriginList` option with regular expressions instead of origin values.
It allows all origins that contain any match of a regular expression in the `accessControlAllowOriginList`.

!!! tip

    Regular expressions and replacements can be tested using online tools such as [Go Playground](https://play.golang.org/p/mWU9p-wk2ru) or the [Regex101](https://regex101.com/r/58sIgx/2).

    When defining a regular expression within YAML, any escaped character needs to be escaped twice: `example\.com` needs to be written as `example\\.com`.

{!traefik-for-business-applications.md!}
