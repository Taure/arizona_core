# arizona_core

Core template engine, rendering pipeline, and component model for Erlang. Extracted from the [Arizona Framework](https://github.com/arizona-framework/arizona) for standalone use with any web server.

Requires OTP 28+.

## Features

- **Compile-time template optimization** via parse transforms — templates are analyzed and optimized at compile time, not runtime
- **Differential rendering** — only changed parts of the DOM are sent over the wire
- **Component system** — stateful components, stateless components, and page-level views with full lifecycle management
- **Multiple template syntaxes** — HTML, Erlang terms, and Markdown
- **PubSub** — topic-based publish/subscribe for real-time updates
- **Web server agnostic** — works with Nova, Cowboy, or any Erlang HTTP server via the request adapter behaviour

## Installation

Add `arizona_core` to your `rebar.config` dependencies:

```erlang
{deps, [
    {arizona_core, "~> 0.1"}
]}.
```

## Quick Example

A stateful counter component:

```erlang
-module(counter).
-compile({parse_transform, arizona_parse_transform}).
-behaviour(arizona_stateful).
-export([mount/1, render/1, handle_event/3]).

mount(Bindings) ->
    arizona_stateful:new(?MODULE, Bindings#{count => 0}).

render(Bindings) ->
    arizona_template:from_html(~"""
    <div id="{arizona_template:get_binding(id, Bindings)}">
        <p>Count: {arizona_template:get_binding(count, Bindings)}</p>
        <button onclick="arizona.pushEventTo(
            '{arizona_template:get_binding(id, Bindings)}', 'increment'
        )">+</button>
    </div>
    """).

handle_event(~"increment", _Params, State) ->
    Count = arizona_stateful:get_binding(count, State),
    {[], arizona_stateful:put_binding(count, Count + 1, State)}.
```

## Documentation

Full documentation is available on [HexDocs](https://hexdocs.pm/arizona_core).

## License

Apache License 2.0 — see [LICENSE.md](LICENSE.md) for details.

Original copyright: William Fank Thomé.
