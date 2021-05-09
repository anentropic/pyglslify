# TODO

Probably nothing. It's simple and it works.

I spent way too long looking for a way to avoid the subporcess call (why?) via some kind of embedded JS runtime. I'd used `PyMiniRacer` V8 in the past, but it has no provision for working with modules. We need an embedded Nodejs but they don't really exist. We can use JS tooling to convert Nodejs modules to ES6 modules but we still need an embedded interpreter that exposes an API for pre-loading modules into a script context. Deno is in Rust and can do that but doesn't currently expose an interface for getting values out AFAICT.

I found QuickJS which is in C++ and supports ES2020. There are Rust and WASM versions around - we can load WASM directly in Python via a lib without making our own binding. There is some C++ example code that shows how to load modules but I could not see how to do it for the WASM version. So I'd have to turn the C++ example into a Python lib.

Now I found this:

- https://github.com/HiRoFa/quickjs_es_runtime
  shows how to load ES modules via quickjs engine from Rust
- https://github.com/HiRoFa/GreenCopperRuntime
  looks like it will eventually provide Nodejs apis like `fs` and commonjs `require` (we'll need these)

So at some point it's probably possible to build a PyO3 lib that runs `glslify` via `GreenCopperRuntime`.
