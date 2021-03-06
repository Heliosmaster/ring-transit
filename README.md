# ring-transit

Standard Ring middleware functions for handling [Transit](https://github.com/cognitect/transit-format) requests and responses.

## Disclaimer

This library was created less than a week after Transit was released and was
modeled after the very
similar [ring-json](https://github.com/ring-clojure/ring-json). Since then
other, more well-thought-out libraries have emerged that are *much* more
actively maintained. Some of these are:

- [ring-middleware-format](https://github.com/ngrunwald/ring-middleware-format):
  A general-purpose ring middleware for handling lots of different content
  types. **If you're building a ring-based app, `ring-middleware-format` is
  recommended over this library**.
- [liberator-transit](https://github.com/sattvik/liberator-transit): If you're
  using [liberator](https://github.com/clojure-liberator/liberator), this may be a good choice.

## Installation

Add the following in `project.clj` under `:dependencies`:

```
[ring-transit "0.1.6"]
```

## Usage

I've based the api and source on [`ring/ring-json`](https://github.com/ring-clojure/ring-json).

The `wrap-transit-response` middleware will convert any response with a
collection as a body (e.g. map, vector, set, seq, etc) into Transit:

```clojure
(use '[ring.middleware.transit :only [wrap-transit-response]]
     '[ring.util.response :only [response]])

(defn handler [request]
  (response {:foo "bar"}))

(def app
  (wrap-transit-response handler {:encoding :json, :opts {}}))
```

`:opts` is a map of options that will be passed to
[`transit/writer`](https://github.com/cognitect/transit-clj/blob/master/src/cognitect/transit.clj#L121)

**Note:** the `:encoding` option must be one of `#{:json :json-verbose :msgpack}`. The default
is `:json`.

The `wrap-transit-body` middleware will parse the body of any request
with a transit content-type into a Clojure data structure:

```clojure
(use '[ring.middleware.transit :only [wrap-transit-body]]
     '[ring.util.response :only [response]])

(defn handler [request]
  (prn (get-in request [:body "user"]))
  (response "Uploaded user."))

(def app
  (wrap-transit-body handler {:keywords? true :opts {}}))
```

`:opts` is a map of options that will be passed to
[`transit/reader`](https://github.com/cognitect/transit-clj/blob/master/src/cognitect/transit.clj#L254)

**Note:** The keywords? option will attempt to recursively convert all keys
to keywords (nested maps allowed!).

The `wrap-transit-params` middleware will parse any request with a transit
content-type and body and merge the resulting parameters into a params
map:

```clojure
(use '[ring.middleware.transit :only [wrap-transit-params]]
     '[ring.util.response :only [response]])

(defn handler [request]
  (prn (get-in request [:params "user"]))
  (response "Uploaded user."))

(def app
  (wrap-transit-params handler {:opts {}}))
```

`:opts` is a map of options that will be passed to
[`transit/reader`](https://github.com/cognitect/transit-clj/blob/master/src/cognitect/transit.clj#L254)


## License

Copyright © 2016 Josh Lehman

Distributed under the Eclipse Public License either version 1.0 or (at
your option) any later version.
