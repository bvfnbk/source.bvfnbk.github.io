---
title: "Building a Webjar"
date: 2023-01-20T14:01:06+01:00
includeInPostList: true
draft: false
tags:

- gradle
- jar
- web
- html
- js
- css

---

Everytime when I create an application including a web-based front-end, I am thinking about the "best" way to build and
package its artifacts up. The choices for JVM-based artifacts are limited but front-end artifacts basically provide all
file formats and scripting languages available to the platform the artifact is built upon.

Recently, I started to serve the frontend assets as static files from within my service for better or for worse. Now the
obvious question is, whether my service may _depend_ on the front-end assets in the same way it depends on its back-end
libraries.

## Scenario

The prove-of-concept application consists out of a web page and a service. The service is supposed to serve the assets
and a simple REST end-point.

The UI provides an input element asking for a name which will be sent to the backend to compose a message which will be
shown in a span.

## Implementation

The implementation can be found at https://github.com/bvfnbk/webjars-example. It basically implements two modules, a
front-end module providing the HTML, CSS and JavaScript assets and a back-end module serving the assets _and_ a REST
endpoint.

First, the back-end does not use _WebJars_. All front-end assets are served from a static directory. In the next 
step, _Apache Maven_ is used to build a _WebJar_ out of the front-end assets. The service then uses the dependency to
serve the front-end assets.

## Improvements

### Resource Path Mapping

The current implementation requires long paths to access the assets (paths are to be prefixed with 
`/assets/webjars-frontend`, which may be shortend to `/webjars-frontend` by configuration, but still...).

1. The back-end may serve the `index.html` from a _template_, inserting the concrete paths to the assets.
2. The back-end modifies the `index.html` from the _WebJar_.

### Clean-Up Build

The front-end module should be built using _Gradle_ as well. Both modules could be build (and eventually) run by a 
_parent_ project, making it unnecessary to deploy the front-end module into your local `.m2/repository`.

## Resources

* https://github.com/bvfnbk/webjars-example
* https://www.webjars.org
