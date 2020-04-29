## Example of [build configurations](https://docs.bazel.build/versions/2.1.0/skylark/config.html)

This is a fork of the [original reproduction][orig_repro] of an [issue][issue]
regarding workspace prefixes on user defined configuration flags. This
reproduction has been updated to show how the situation has improved in Bazel
and where the issue remains.

[orig_repro]: https://github.com/ulysses4ever/bazel-user-settings-example/
[issue]: https://github.com/bazelbuild/bazel/issues/9177

### The minimal working setup

If you do:

```bash
bazel clean && bazel build //:my_drink
```
it will print:
```
DEBUG: .../rules.bzl:7:9: Get the default (False)
```

In contrast, if you do
```bash
bazel clean && bazel build //:my_drink --@rules_example//:favorite_flavor=True
```
it will print:
```
DEBUG: .../rules.bzl:5:9: Get the opposite of default (True)
```

I.e. the workspace name prefix is understood, so long as it is the workspace
name of an external workspace.

### The remaining bug (branch: [bug](https://github.com/ulysses4ever/bazel-user-settings-example/commit/1d79b746b0323e0450a99aedea9e8e3c3d924c07))

Change into the `rules_example` directory so that the flag becomes local.

If you do:

```bash
(cd rules_example && bazel clean && bazel build //:my_drink)
```
it will print:
```
DEBUG: .../rules.bzl:7:9: Get the default (False)
```

In contrast, if you do
```bash
(cd rules_example && bazel build //:my_drink --//:favorite_flavor=True)
```
it will print:
```
DEBUG: .../rules.bzl:5:9: Get the opposite of default (True)
```
I.e. the flag is understood so long as it's label is local.

But, if you do
```bash
(cd rules_example && bazel build //:my_drink --@rules_example//:favorite_flavor=True)
```
it will print:
```
DEBUG: .../rules.bzl:7:9: Get the default (False)
```
I.e. the flag is silently ignored, which is wrong.
