---  
alias:  
creation_date: 2024-07-20 09:41  
tags: 
related:
- 

---  

主要用這個
- [CS106L: Standard C++ Programming](https://web.stanford.edu/class/archive/cs/cs106l/cs106l.1204/lectures.html)
- [CS106L - 2019 Winter - YouTube](https://www.youtube.com/playlist?list=PLoCMsyE1cvdUJvvBjBOJKf3rc1xj7_G7g)


## note
[[106L stream2]]



- [x] recap and finish stream ptt
- [ ] read the next slide 
	- [web.stanford.edu/class/archive/cs/cs106l/cs106l.1204/lectures/sequence\_containers/3-Sequence Containers.pdf](https://web.stanford.edu/class/archive/cs/cs106l/cs106l.1204/lectures/sequence_containers/3-Sequence%20Containers.pdf)
- [ ] see vid
- [ ] repeat above step until hit assigment one, after Functions and Algorithms complete





# resource
- 官網: [CS 106L: Standard C++ Programming](https://web.stanford.edu/class/cs106l/)
	- [# CS106L Autumn 2018](https://web.stanford.edu/class/archive/cs/cs106l/cs106l.1192/lectures.html)
- 影片：[(606) CS 106L Fall 2019 - Lecture 0: Introduction (Screencast) - YouTube](https://www.youtube.com/watch?v=_WvkljZzGug&list=PLCgD3ws8aVdolCexlz8f3U-RROA0s5jWA&index=3)
- if you think C++ is good, you can move to next step -> [Stanford CS106B/X - CS自学指南](https://csdiy.wiki/%E7%BC%96%E7%A8%8B%E5%85%A5%E9%97%A8/cpp/CS106B_CS106X/)
- 課堂提到的參考資料
	- [C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/CppCoreGuidelines)




![[IMG-CS106L Standard C++ Programming-20250805201107733.png]]

# intro
## 课程简介

- 所属大学：Stanford
- 先修要求：最好掌握至少一门编程语言
- 编程语言：C++
- 课程难度：🌟🌟🌟
- 预计学时：20 小时

我从大一开始一直都是写的 C++ 代码，直到学完这门课我才意识到，我写的 C++ 代码大概只是 C 语言 + `cin`/`cout` 而已。

这门课会深入到很多标准 C++ 的特性和语法，让你编写出高质量的 C++ 代码。例如 auto binding, uniform initialization, lambda function, move semantics，RAII 等技巧都在我此后的代码生涯中被反复用到，非常实用。

值得一提的是，这门课的作业里你会实现一个 HashMap（类似于 STL 中的 `unordered_map`), 这个作业几乎把整个课程串联了起来，非常考验代码能力。特别是 `iterator` 的实现，做完这个作业我开始理解为什么 Linus 对 C/C++ 嗤之以鼻了，因为真的很难写对。

总的来讲这门课并不难，但是信息量很大，需要你在之后的开发实践中反复巩固。Stanford 之所以单开一门 C++ 的编程课，是因为它后续的很多 CS 课程 Project 都是基于 C++ 的。例如 CS144 计算机网络和 CS143 编译器。这两门课在本书中均有收录。

## 课程资源

- 课程网站：[http://web.stanford.edu/class/cs106l/](http://web.stanford.edu/class/cs106l/)
- 课程视频：[https://www.youtube.com/channel/UCSqr6y-eaQT_qZJVUm_4QxQ/playlists](https://www.youtube.com/channel/UCSqr6y-eaQT_qZJVUm_4QxQ/playlists)
- 课程教材：[http://web.stanford.edu/class/cs106l/full_course_reader.pdf](http://web.stanford.edu/class/cs106l/full_course_reader.pdf)
- Assignment1 下载网址：[https://github.com/snme/cs106L-assignment1](https://github.com/snme/cs106L-assignment1)
- Assignment2 下载网址：[https://github.com/snme/cs106L-assignment2](https://github.com/snme/cs106L-assignment2)
- 课程作业：具体内容见课程网站，我做的时候一共是两个：
- 实现一个 WikiRacer 的小游戏
- 实现一个类似 STL 库的 HashMap

## 资源汇总

@PKUFlyingPig 在学习这门课中用到的所有资源和作业实现都汇总在 [PKUFlyingPig/CS106L - GitHub](https://github.com/PKUFlyingPig/CS106L) 中。