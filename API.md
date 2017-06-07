<!-- Generated by documentation.js. Update this documentation by updating the source code. -->

### Table of Contents

-   [expressMiddleware](#expressmiddleware)
-   [Span](#span)
    -   [context](#context)
    -   [tracer](#tracer)
    -   [setOperationName](#setoperationname)
    -   [setBaggageItem](#setbaggageitem)
    -   [getBaggageItem](#getbaggageitem)
    -   [setTag](#settag)
    -   [addTags](#addtags)
    -   [log](#log)
    -   [finish](#finish)
-   [Tracer](#tracer-1)
    -   [constructor](#constructor)
    -   [startSpan](#startspan)
    -   [inject](#inject)
    -   [extract](#extract)

## expressMiddleware

**Parameters**

-   `opts` **[Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)?** 
    -   `opts.operationNameBuilder` **[Function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function)?** 

Returns **[Function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/function)** 

## Span

**Extends opentracing.Span**

Span represents a logical unit of work as part of a broader Trace. Examples
of span might include remote procedure calls or a in-process function calls
to sub-components. A Trace has a single, top-level "root" Span that in turn
may have zero or more child Spans, which in turn may have children.

### context

Returns the SpanContext object associated with this Span.

Returns **SpanContext** 

### tracer

Returns the Tracer object used to create this Span.

Returns **[Tracer](#tracer)** 

### setOperationName

Sets the string name for the logical operation this span represents.

**Parameters**

-   `name` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** 

### setBaggageItem

Sets a key:value pair on this Span that also propagates to future
children of the associated Span.

setBaggageItem() enables powerful functionality given a full-stack
opentracing integration (e.g., arbitrary application data from a web
client can make it, transparently, all the way into the depths of a
storage system), and with it some powerful costs: use this feature with
care.

IMPORTANT NOTE #1: setBaggageItem() will only propagate baggage items to
_future_ causal descendants of the associated Span.

IMPORTANT NOTE #2: Use this thoughtfully and with care. Every key and
value is copied into every local _and remote_ child of the associated
Span, and that can add up to a lot of network and cpu overhead.

**Parameters**

-   `key` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** 
-   `value` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** 

### getBaggageItem

Returns the value for a baggage item given its key.

**Parameters**

-   `key` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** The key for the given trace attribute.

Returns **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** String value for the given key, or undefined if the key does not
        correspond to a set trace attribute.

### setTag

Adds a single tag to the span.  See `addTags()` for details.

**Parameters**

-   `key` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** 
-   `value` **any** 

### addTags

Adds the given key value pairs to the set of span tags.

Multiple calls to addTags() results in the tags being the superset of
all calls.

The behavior of setting the same key multiple times on the same span
is undefined.

The supported type of the values is implementation-dependent.
Implementations are expected to safely handle all types of values but
may choose to ignore unrecognized / unhandle-able values (e.g. objects
with cyclic references, function objects).

**Parameters**

-   `keyValues`  

Returns **\[type]** [description]

### log

Add a log record to this Span, optionally at a user-provided timestamp.

For example:

    span.log({
        size: rpc.size(),  // numeric value
        URI: rpc.URI(),  // string value
        payload: rpc.payload(),  // Object value
        "keys can be arbitrary strings": rpc.foo(),
    });

    span.log({
        "error.description": someError.description(),
    }, someError.timestampMillis());

**Parameters**

-   `keyValues`  
-   `timestamp` **[number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number)** An optional parameter specifying the timestamp in milliseconds
           since the Unix epoch. Fractional values are allowed so that
           timestamps with sub-millisecond accuracy can be represented. If
           not specified, the implementation is expected to use its notion
           of the current time of the call.
-   `keyValuePairs` **[object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)** An object mapping string keys to arbitrary value types. All
           Tracer implementations should support bool, string, and numeric
           value types, and some may also support Object values.

### finish

Sets the end timestamp and finalizes Span state.

With the exception of calls to Span.context() (which are always allowed),
finish() must be the last call made to any span instance, and to do
otherwise leads to undefined behavior.

**Parameters**

-   `finishTime` **[number](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Number)** Optional finish time in milliseconds as a Unix timestamp. Decimal
            values are supported for timestamps with sub-millisecond accuracy.
            If not specified, the current time (as defined by the
            implementation) will be used.

## Tracer

Tracer is the entry-point between the instrumentation API and the tracing
implementation.

The default object acts as a no-op implementation.

Note to implementators: derived classes can choose to directly implement the
methods in the "OpenTracing API methods" section, or optionally the subset of
underscore-prefixed methods to pick up the argument checking and handling
automatically from the base class.

**Parameters**

-   `opts`  

### constructor

Construct a new tracer.

**Parameters**

-   `opts` **[object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)** 

### startSpan

Starts and returns a new Span representing a logical unit of work.

For example:

    // Start a new (parentless) root Span:
    var parent = Tracer.startSpan('DoWork');

    // Start a new (child) Span:
    var child = Tracer.startSpan('Subroutine', {
        childOf: parent.context(),
    });

**Parameters**

-   `name` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** the name of the operation.
-   `fields` **[object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)?** the fields to set on the newly created span.
    -   `fields.childOf` **SpanContext?** a parent SpanContext (or Span,
               for convenience) that the newly-started span will be the child of
               (per REFERENCE_CHILD_OF). If specified, `fields.references` must
               be unspecified.
    -   `fields.tags` **[object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)?** set of key-value pairs which will be set
               as tags on the newly created Span. Ownership of the object is
               passed to the created span for efficiency reasons (the caller
               should not modify this object after calling startSpan).

Returns **[Span](#span)** a new Span object.

### inject

Injects the given SpanContext instance for cross-process propagation
within `carrier`. The expected type of `carrier` depends on the value of
\`format.

OpenTracing defines a common set of `format` values (see
FORMAT_TEXT_MAP, FORMAT_HTTP_HEADERS, and FORMAT_BINARY), and each has
an expected carrier type.

Consider this pseudocode example:

    var clientSpan = ...;
    ...
    // Inject clientSpan into a text carrier.
    var headersCarrier = {};
    Tracer.inject(clientSpan.context(), Tracer.FORMAT_HTTP_HEADERS, headersCarrier);
    // Incorporate the textCarrier into the outbound HTTP request header
    // map.
    Object.assign(outboundHTTPReq.headers, headersCarrier);
    // ... send the httpReq

**Parameters**

-   `spanContext` **SpanContext** the SpanContext to inject into the
            carrier object. As a convenience, a Span instance may be passed
            in instead (in which case its .context() is used for the
            inject()).
-   `format` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** the format of the carrier.
-   `carrier` **any** see the documentation for the chosen `format`
            for a description of the carrier object.

### extract

Returns a SpanContext instance extracted from `carrier` in the given
`format`.

OpenTracing defines a common set of `format` values (see
FORMAT_TEXT_MAP, FORMAT_HTTP_HEADERS, and FORMAT_BINARY), and each has
an expected carrier type.

Consider this pseudocode example:

    // Use the inbound HTTP request's headers as a text map carrier.
    var headersCarrier = inboundHTTPReq.headers;
    var wireCtx = Tracer.extract(Tracer.FORMAT_HTTP_HEADERS, headersCarrier);
    var serverSpan = Tracer.startSpan('...', { childOf : wireCtx });

**Parameters**

-   `format` **[string](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String)** the format of the carrier.
-   `carrier` **any** the type of the carrier object is determined by
            the format.

Returns **SpanContext** The extracted SpanContext, or null if no such SpanContext could
        be found in `carrier`