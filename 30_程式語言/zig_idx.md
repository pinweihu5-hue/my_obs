[[low_level_idx]]

a system lang alterative to C, rust

when to use it?
- you want a more easily written sys-lang then rust
- you want a more type safe then C 
Cavent?
- younger lang, maybe you will encounter some toolchain and 3rd party you need but won't exist yet
- as hire mgmt, less ppl fluent on it
- less doc and resource to learn

---

# basic
[Getting Started ⚡ Zig Programming Language](https://ziglang.org/learn/getting-started/#setting-up-path-on-linux-macos-bsd)
	[Learn zig in Y Minutes](https://learnxinyminutes.com/docs/zig/)




[(592) Zig for Impatient Devs - YouTube](https://www.youtube.com/watch?v=5I4ZkmMS4-0&list=WL&index=4)



[(592) Zig in 100 Seconds - YouTube](https://www.youtube.com/watch?v=kxT8-C1vmd4&list=WL&index=1)
-> /Users/re4388/project/personal/lang/zig/zig01





[(592) zig will change programming forever - YouTube](https://www.youtube.com/watch?v=pnnx1bkFXng)
C and rust compare:
- C is too powerful, you can do wahtever u want
	- you can muaniluate mem as you want
	- you can free and allocate mem as you want
	- not a mem safe lang
- Rust is try to be mem safe and as powerful as C, so it try to catch as many as bug in compliled time

zig
- Cross-compilation capability 
	- provided by default, without any dependency. Different libc are provided to ease the process. Cross-compilation works from, and to, any operating system and architecture.
- some feature make it more predictable
	- no hidden control flow (C does, preprocess, macro)
	- no operator overload (JS have, like + can be concate or math operator, C does)
	- No hidden memory allocation, no hidden control flow, everything you read is everything you get, you don't have to guess whether this functions aborts, returns -1 or 0,  or whether it sets ernno.
- have compile time keyword, let you run certain part of code in compile time
	- Comptime is capturing 90% of the power of C++ templates/Macros, while still being very readable and type safe
- have defer
	- a marco to allow you to put allocator and releaser together
	- defer not only "places" the code at the end of the function, but at all exit points (if there is an early return for instance) like a destructor in C++. (and the futur defer statement in C hopefully)
- mem allocator
	- not like C, directly control mem, Zig have a wrapper to let you control mem more safety (not that as safe and difficult to use in rust)
	- in Zig, you can swap allocator in code (C can not)
	- Allocators are first class citizen. Even the Std is build around that which is amazing. I really don't get how a manual memory managed language like C didn't come with some form of interface for allocators.
- error handling
	- like go and rust, treat error as return val not a exception
	- and force you to handle the error explicitly
	- The error handling and all the builting safety features makes it so much easier to write fast and correct code.
- philosophy
	- Rust philosophy: make it hard to write bad code
	- Zig philosophy: make it easy to write good code
	-  Zig found the right balance of freedom, meaning you can do exactly what you are doing in C (aka crazy casting and weird stuff unlike Rust) but at the same time the language design makes it very inconvenient and verbose to do so. Which makes it actually easier to just to the right thing and not take any shortcuts. So for once the Type system is actually one that doesn't deceive you because of how loose it is like C or how tight it is like Rust.
- interops
	- one of the best features of Zig - seamless integration with C! It's really cool, you can straight import and start using C libs without writing any FFI.
	- The interops with C is the most natural, intuitive, and straightforward that I've ever seen. You literally just add an @cImport("header.h"); and  a exe.addCsourceFile("") in your build.zig and you are good to go.
- testing
	- Zig also has integrated unit testing, which makes it so easier and cheaper to test code. In C I would literally spend 30 minutes writing some code and one hour testing it properly. In Zig you write a function write 2/3 tests forget about it and just do a quick zig build test and you are good to go. Which is also why it's so easy to refactor Zig btw.
- build system
	- he build system is insanely good, I replaced make/cmake with Zig, and with Zig itself it's really amazing.
	- Let's all agree on the fact that Zig has by far the best build system. It is literally built into the language itself. No more bullshit Makefiles, pkgconf or Ninja. Don't even get me started with CMake.
