---
aliases:
  - cs61b_index
---

# note
- [[cs61b  Java ADT]]
- [[cs61b 寫 unitTest 的好處]]
- [[cs61B linkedlist note]]
- [[cs61b array note]]
- [[cs61b 4.1 inheritance]]
- [[cs61b 4.2- Extends, Casting, Higher Order Functions]]
- [[cs61b 4.3-Subtype Polymorphism vs. HoFs]]
- [[cs61b 4.4. Exceptions, Iterators, Object Methods]]
- [[cs61b 13. Asymptotics I]]
- [[cs61b Disjoint Sets, quick union]]
- [[cs61b Asymptotics II]]
- [[cs61b midterm point and box solution]]
- [[cs61b ADT, sets, maps, BST]]
- [[cs61b B-Trees]]
- [[cs61b Red Black Trees or LLRB]]
- [[cs61b Hashing]]
- [[cs61b pq and heap]]
- [[cs61b tries]]
- [[cs61b k-d tree, multi-d tree]]
- [[cs61b Tree and Graph Traversals]]
- [[cs61b Graph Traversal and impl ]]
- [[cs61b  Graph Shortest Paths]]
- [[cs61b Minimum Spanning Trees]]
- [[cs61b reductions and decomposition]]
- [[cs61b graph related algo summary]]
- [[cs61b Basic Sorts]]
- [[cs61b quick sort]]
- [[cs61b Software Engineering I]]




- main page: [Main | CS 61B Spring 2019](https://sp19.datastructur.es/)
  - 其他年份 [Home | CS 61B Spring 2024](https://sp24.datastructur.es/)
- text book: [1.1 Essentials · Hug61B](https://joshhug.gitbooks.io/hug61b/content/chap1/chap11.html)
- [[cs61b exam study]]
- [Log In | Gradescope](https://www.gradescope.com/courses/20666)
- [Visualize code in Python, JavaScript, C, C++, and Java](https://pythontutor.com/render.html#mode=edit)
- Java 的資料結構寫法參考 -> [Algorithms and Data Structures Cheatsheet](https://algs4.cs.princeton.edu/cheatsheet/)



2024_09_15-09_09_39
!!! 先暫停一下 !!! -> 先去 prep interview and job switch
進度到這一行
這一個 hw 還沒做
![[IMG-CS61B Data Structures and Algorithms-20250810200102668.png]]



- [ ] java book, 最後把沒看的看一看





- 進入 sorting 課程前
	- 想要另外花時間了解的 -> 想要把 tree and algo 的理解程度可以讓我理解 stack, queue, pq 一樣熟練, 可以easy recall the 機制然後未來比較有機會去運用他們
	- [ ] tree 的應用和機制彙整
		- [ ] tree-algo那邊提供的 code 有哪些? check algo book / cheatsheet
			- [ ] [BST.java](https://algs4.cs.princeton.edu/32bst/BST.java.html)
			- [ ] [NonrecursiveBST.java](https://algs4.cs.princeton.edu/32bst/NonrecursiveBST.java.html)
			- [ ] [RedBlackLiteBST.java](https://algs4.cs.princeton.edu/33balanced/RedBlackLiteBST.java.html)
			- [ ] [RedBlackBST.java](https://algs4.cs.princeton.edu/33balanced/RedBlackBST.java.html)
			- [ ] [AVLTreeST.java](https://algs4.cs.princeton.edu/99misc/AVLTreeST.java.html)
			- [ ] [Splay tree - Wikipedia](https://en.wikipedia.org/wiki/Splay_tree) and [SplayBST.java](https://algs4.cs.princeton.edu/33balanced/SplayBST.java.html)
	- [ ] graph 的應用和機制彙整
		- [ ] graph 那邊提供的 code 有哪些? check  algo book / cheatsheet







三次考試也有題目和答案 -> [Resources | CS 61B Spring 2019](https://sp19.datastructur.es/resources.html)
![[IMG-CS61B Data Structures and Algorithms-20250810200109628.png|476]]

# 先 skip

- [NBody Simulation | CS 61B Spring 2019](https://sp19.datastructur.es/materials/proj/proj0/proj0)
  - /Users/re4388/project/personal/CS61B/skeleton-sp19/proj0

# intro

## 课程简介

- 所属大学：UC Berkeley
- 先修要求：CS61A
- 编程语言：Java
- 课程难度：🌟🌟🌟
- 预计学时：60 小时

伯克利 CS61 系列的第二门课程，注重数据结构与算法的设计，同时让学生有机会接触上千行的工程代码，通过 Java 初步领会软件工程的思想。

我上的是 2018 年春季学期的版本，该课的开课老师 Josh Hug 教授慷慨地将 autograder 开源了，大家可以通过网站公开的邀请码在  [gradescope](https://gradescope.com/)  免费加入课程，从而方便地测评自己的代码。

这门课所有的编程作业都是使用 Java 完成的。没有 Java 基础的同学也不用担心，课程会有保姆级的教程，从 IDEA（一款主流的 Java 编程环境）的配置讲起，把 Java 的核心语法与特性事无巨细地讲授，大家完全不用担心跟不上的问题。

这门课的作业质量也是绝绝子。14 个 lab 会让你自己实现课上所讲的绝大部分数据结构，10 个 Homework 会让你运用数据结构和算法解决实际问题， 另外还有 3 个 Project 更是让你有机会接触上千行的工程代码，在实战中磨练自己的 Java 能力。

## 课程资源

- 课程网站：[spring2024](https://sp24.datastructur.es/), [fall2023](https://fa23.datastructur.es/), [spring2023](https://sp23.datastructur.es/), [spring2018](https://sp18.datastructur.es/)
- 课程视频：原版视频参见课程网站，B 站有中文翻译搬运。
- 课程教材：无
- 课程作业：每年略有不同，18 年春季学期有 14 个 Lab，10 个 Homework 以及 3 个 Project，具体要求详见课程网站。

## 资源汇总

@PKUFlyingPig 在学习这门课中用到的所有资源和作业实现都汇总在  [PKUFlyingPig/CS61B - GitHub](https://github.com/PKUFlyingPig/CS61B)  中。

- 作業上傳: [Log In | Gradescope](https://www.gradescope.com/courses/20666)
