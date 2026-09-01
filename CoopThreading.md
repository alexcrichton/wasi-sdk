# Support for Cooperative Threading

The component model specification is in the process of adding and defining
intrinsics for guests to use to implement cooperative multithreading. The goal
of this effort is to enable compiling applications using `pthread_*` style
threading APIs, for example, to work with components. These intrinsics are
described in the [component model explainer][explain] and this feature
corresponds to the 🧵 emoji. The Wasmtime runtime supports this proposal behind
the `-Wcomponent-model-threading` flag.

[explain]: https://github.com/WebAssembly/component-model/blob/main/design/mvp/Explainer.md

Due to this feature being experimental and not officially included in any
published WASI version yet it is off-by-default in wasi-sdk. By default
`pthread_*`-style APIs either return an error (such as when spawning a thread)
or trap (such as when blocking on a condition variable). Note that for
compatibility, though, all `pthread_*` APIs are always available. The wasi-sdk
distribution, however, does enable effectively swapping out these
implementations in an opt-in basis to test out cooperative multithreading.

Assuming that you've downloaded wasi-sdk and unpacked it within `$WASI_SDK_PATH`
there is a sysroot located at `$WASI_SDK_PATH/share/wasi-sysroot/experimental-coop-threads`
which can be used to experiment with cooperative multithreading. You can use
this in your project like so:

```console
$WASI_SDK_PATH/bin/wasm32-wasip3-clang foo.c -o foo.wasm --sysroot \
    $WASI_SDK_PATH/share/wasi-sysroot/experimental-coop-threads -pthread
```

Note the `--sysroot` and `-pthread` flags to indicate that threading is being
used. When combined these flags should produce a binary which can then be run in
Wasmtime, for example, like so:

```console
wasmtime -W component-model-threading ./foo.wasm
```

The alternative sysroot has support for `pthread_*` symbols in wasi-libc which
additionally means support in C++ through the `<thread>` header, for example.

It is intended in the near-ish future (before the end of 2026) that this
experimental sysroot will become the default and wasi-libc/wasi-sdk will support
cooperative multithreading by default. This decision will require the WASI
subgroup to vote on including the cooperative multithreading feature in a future
WASI release, and then wasi-libc/wasi-sdk will update to this release.
