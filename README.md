# elastic-apm-trace-context

[![npm](https://img.shields.io/npm/v/elastic-apm-trace-context.svg)](https://www.npmjs.com/package/elastic-apm-trace-context)
[![Build status](https://travis-ci.org/elastic/elastic-apm-trace-context.svg?branch=master)](https://travis-ci.org/elastic/elastic-apm-trace-context)
[![codecov](https://img.shields.io/codecov/c/github/elastic/elastic-apm-trace-context.svg)](https://codecov.io/gh/elastic/elastic-apm-trace-context)
[![Standard - JavaScript Style Guide](https://img.shields.io/badge/code%20style-standard-brightgreen.svg?style=flat)](https://github.com/feross/standard)

This is a basic implementation of the W3C trace context header spec.

## Installation

```
npm install elastic-apm-trace-context
```

## Example Usage

```js
const crypto = require('crypto')
const TraceContext = require('elastic-apm-trace-context')

const version = Buffer.alloc(1).toString('hex')
const traceId = crypto.randomBytes(16).toString('hex')
const id = crypto.randomBytes(8).toString('hex')
const flags = '01'

const header = `${version}-${traceId}-${id}-${flags}`

const context = TraceContext.fromString(header)
```

## API

### `new TraceContext(buffer)`

Construct a new `TraceContext` instance from an existing buffer. The contents are binary data that corresponds to the structure of the [W3C traceparent header][traceparent] format, with separators removed.

### `TraceContext.fromString(header)`

Reconstruct a `TraceContext` instance from a formatted [W3C traceparent header][traceparent] string.

### `TraceContext.startOrResume(parent, settings)`

Resume from a parent context, if given, or start a new context. Accepts another `TraceContext` instance, a [W3C traceparent header][traceparent] string, or a `Span` or `Transaction` instance from [elastic-apm-node](http://npmjs.org/package/elastic-apm-node).

Requires a `settings` object with a `transactionSampleRate` value from 0.0 to 1.0 to generate a sampling decision for the context. This will only be applied when starting a _new_ context. When continuing an existing context, the sampling decision will be propagated into all child contexts.

### `context.recorded`

Returns `true` if this `TraceContext` is sampled.

### `context.traceId`

The `traceId` property will propagate through all children in the tree to link them all together.

### `context.id`

The `id` property is used to uniquely identify a given `TraceContext` instance within the tree.

### `context.parentId`

The `parentId` property links this context to its direct parent in the tree.

### `context.flags`

The `flags` property is used to store metadata such as the sampling decision.

### `context.version`

The `version` property corresponds to the version segment of the [W3C traceparent header][traceparent].

### `context.child()`

Create a new `TraceContext` instance that is a child of this one.

### `context.toString()`

Formats the `TraceContext` instance as a [W3C traceparent header][traceparent].

### `context.ensureParentId()`

Return the parent ID, if there is none, generate one. This is useful in browser instrumentation to produce a starting span around a browser request which was not instrumented prior to page load.

## License

[MIT](LICENSE)

[traceparent]: https://github.com/w3c/trace-context/blob/master/spec/20-http_header_format.md
