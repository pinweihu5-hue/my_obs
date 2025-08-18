- [[bad api doc, run 32 working set (win) in 64 GiB machine]]
- [[use C to know if the current architecture stack is growth up or down]]
- [[zig_idx]]
- [[build from scratch]]




[(602) How Linux Kernel Prints Text on Screen - YouTube](https://www.youtube.com/watch?v=aAuw2EVCBBg&list=WL&index=14)
- dl the linux kernal (a verison)
- use qemu to load its kernal
- write a simple client side hello world
- use gdb, can use kermal voa qemu and run the client code in debug mode
- can set break pt on kernal mode
- modify the ptr on the fly the result the output result




when I can not use garbege collected language
- when I need to have determistic result of every operaiton
	- GC lang will handle on its own and dev can not control
	- 因為 release mem or collect mem s.t. 會 cause a system a pause, and some sys is sensitive to this, like flight control control
	- some game engine can not afford this sudden pause in game expereince
- some embeded system have very limited resouce, gc need to fine level of control
- low-lantecy req. system, same reason as above
- OS: build os mostly need to self-control gc
- device driver: need to directly mem access
- current lang candidate: C, rust, Zig


