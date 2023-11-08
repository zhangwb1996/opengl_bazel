# opengl_bazel

on windows, run `bazel build //src:main`: link failed..., unresolved external symbol.

```
opengl_bazel
    src
        main.cpp
        BUILD
    third
        include
           GL
           GLFW
           ...
        lib
            glew32.lib
            glfw3.lib
            ...
        BUILD
WORKSPACE
```

src/BUILD:

```python
load("@rules_cc//cc:defs.bzl", "cc_binary")

cc_binary(
    name = "main",
    srcs = ["main.cpp"],
    copts = ["-I third/include"],
    linkopts = ["-DEFAULTLIB:opengl32.lib"],
    deps = [
        "//third:thirdlib",
    ],
)
```

third/BUILD:

```python
load("@rules_cc//cc:defs.bzl", "cc_import", "cc_library")

cc_import(
    name = "glew32",
    hdrs = glob(["include/GL/*.h"]),
    static_library = "lib/glew32.lib",
    visibility = ["//src:__pkg__"],
)

cc_import(
    name = "glfw3",
    hdrs = glob(["include/GLFW/*.h"]),
    static_library = "lib/glfw3.lib",
    visibility = ["//src:__pkg__"],
)

cc_import(
    name = "soil2",
    hdrs = glob(["include/SOIL2/*.h"]),
    static_library = "lib/soil2-debug.lib",
    visibility = ["//src:__pkg__"],
)

cc_library(
    name = "glm",
    srcs = glob(["include/glm/*.hpp"]),
    hdrs = glob(["include/glm/*.h"]),
    visibility = ["//src:__pkg__"],
)

cc_library(
    name = "thirdlib",
    linkopts = ["-DEFAULTLIB:opengl32.lib"],
    visibility = ["//src:__pkg__"],
    deps = [
        ":glew32",
        ":glfw3",
        ":glm",
        ":soil2",
    ],
)
```
