

<h1 align=center>CMake for absolte beginners (by a recent absolute beginner)</h1>

Go [here](https://makefiletutorial.com/) for advanced stuff. 

<h1 align=center>Building and installing applications you downloaded that already have a Makefile</h1>

Go to the source folder you downloaded and extract it. Open a terminal. If the source directory already has a build folder cd to it, if not cd to the source directory and run "mkdir build" then "cd build" after that run "make" if you are on a UNIX system (like Linux or MacOS) and you want the application to be installed where your normal applications are so you can find it easily and search for it run "make install". 

<hr/>

<h1 align=center>Building and installing applications you downloaded that only have a CMakelists.txt</h1>

Go to the source folder you downloaded and extract it. Open a terminal. If the source directory already has a build folder cd to it, if not cd to the source directory and run "mkdir build" then "cd build" after that run "cmake .." then run "make" if you are on a UNIX system (like Linux or MacOS) and you want the application to be installed where your normal applications are so you can find it easily and search for it run "make install". 

<hr/>

# Dependency errors 

To work with ZT You will need:

- Zig 0.11.* Main branch build. Nightly might work as well (last tested `0.12.0-dev.1856+94c63f31f`).
- Ubuntu: `sudo apt install build-essential xorg-dev`

### Current Status

- ZT when used for the purpose of GL/ImGui libraries is very stable
- ZT.App is still receiving breaking changes as I find where I can make
the library more flexible for casual use, but overall I find it convenient for
applications and games

See [the example](/example/src/main.zig) for what ZT.App has to offer.

### Why

ZT is intended for an extremely broad group of developers in zig realtime graphics and applications, as it does not railroad you into
using its app+windowing interface to function, and is better viewed as the following goals being accomplished without
any resistance:

- Linear Algebra and Math types
- Cross platform windowing (GLFW) within Zig
- OpenGL binding within Zig
- Completely up to date (c)ImGui Docking branch bindings within Zig
- STB_Image bindings within Zig
- (Optional) barebones wrappers around basic opengl constructs like shaders/textures/buffers

and additionally a ready to go combination of all 3 that lets you just immediately use close to the metal
OpenGL constructs to just work on your application with convenience for use as desktop application code such as
Energy Saving mode.

# Getting Started

First you'll want to clone this into your zig project's folder, and `const ztBuild = @import("path/to/ZT/build.zig")` 
in your own `build.zig` to import this framework's build.zig, and that will expose some important functions
to link ZT into your project.

- `ztBuild.link(exe)` will add ZT's packages to your exe and link the source files for GLFW/GL/ImGui
- (optional) `ztBuild.addBinaryContent("path/to/binContent")` adds binary content to your zig-out folder output, basically the folder structure
ends up being as if `path/to/binContent` was the root folder containing your executable. This is smart and will skip older assets.

So with `ztBuild` imported you just `ztBuild.link(exe)` and you can start importing and using
ZT, or if you so choose, completely ignore ZT and use raw opengl/glfw/imgui.

Then getting started is as easy as this:

```Zig
const std = @import("std");
const zt = @import("zt");

/// SampleData will be available through the context anywhere.
const SampleData = struct {
    yourData: i32 = 0,
};

const SampleApplication = zt.App(SampleData);

pub fn main() !void {
    var context = try SampleApplication.begin(std.heap.c_allocator);
    // Config here,
    while(context.open) {
        context.beginFrame();
        // Application code here!
        context.data.yourData += 1;
        context.endFrame();
    }
    // Unload here
    context.deinit();
}
```

For a more indepth example [see the example file that shows opengl rendering mixed with imgui and more](example/src/main.zig)

## Gotcha:

- ZT.App.begin sets its own GLFW user pointer! Its important too, so use something else for your storage, or if you really want the functionality,
let me know and I'll see how I can enable your usecase within ZT.
- By linking ZT the following packages are available to your app on both windows and ubuntu: `zt`, `gl`, `glfw`, `imgui`, `stb_image`
- ImVec2 and ImVec4 are both substituted with zlm's Vec2 and Vec4 structs respectively, you can use both interchangeably.
- Disabling power saving mode will let GLFW handle buffer flip timing, so likely will be at vsync fps rather than on every
event, unless you disable vsync.
- Need direct access to the input queue? Your context contains an ArrayList of tagged unions that summarizes every input event.
Try to use this instead of overriding GLFW event callbacks.

## Credits

- Example Font - https://github.com/uswds/public-sans
- Inspiration and Code Snippets - https://github.com/digitalcreature/ube (Thanks sammy for all the help!)
