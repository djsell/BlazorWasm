This project shows bugs with Blazor when building for Webassembly.

Blazor does not work when attempting to debug applications using emcc optimization flags.

In order to reproduce:
```
$> dotnet publish -c release
$> cd bin/release/net6.0/publish/wwwroot
$> dotnet-serve
```

Open the application in the browser and check the console.

For example, ASSERTIONS=2 will produce:
```
blazor.webassembly.js:1 Error: Failed to start platform. Reason: RuntimeError: abort(Assertion failed: native function `malloc` called before runtime initialization) at Error
    at jsStackTrace (dotnet.6.0.0-preview.7.21377.19.js:1513)
    at stackTrace (dotnet.6.0.0-preview.7.21377.19.js:1534)
    at abort (dotnet.6.0.0-preview.7.21377.19.js:1247)
    at assert (dotnet.6.0.0-preview.7.21377.19.js:696)
    at Object._malloc (dotnet.6.0.0-preview.7.21377.19.js:1270)
    at h (blazor.webassembly.js:1)
    at lt (blazor.webassembly.js:1)
```
https://emscripten.org/docs/porting/Debugging.html#debugging-assertions

This is because Blazor does not use Module.onRuntimeInitialized to run initialization at the correct time.

https://emscripten.org/docs/getting_started/FAQ.html#how-can-i-tell-when-the-page-is-fully-loaded-and-it-is-safe-to-call-compiled-functions

An example of how Mono uses it:
https://github.com/dotnet/runtime/blob/v6.0.0-preview.7.21377.19/src/mono/sample/wasm/browser/runtime.js#L13