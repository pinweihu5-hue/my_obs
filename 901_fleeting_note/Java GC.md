

[(806) Java's Garbage Collection Explained - How It Saves your Lazy Programmer Butt - YouTube](https://www.youtube.com/watch?v=Mlbyft_MFYM&list=WL&index=27)
- assign to null, to other 都會讓本來 var 沒有 reference -> 全部都沒有ref， gc 就會在下次 clean 掉
- youung and old generation
	- 一直都有就移到 old gen 了
	- "mark and sweet" old freq 比較 low
	- ps: mark 還有被ref 的, sweet 掉沒有的
e