# CS144: Computer Network

## 课程简介

- 所属大学：Stanford
- 先修要求：一定的计算机系统基础，CS106L
- 编程语言：C++
- 课程难度：🌟🌟🌟🌟🌟
- 预计学时：100 小时

这门课的主讲人之一是网络领域的巨擘 [Nick McKeown](http://yuba.stanford.edu/~nickm/index.html) 教授。这位拥有自己创业公司的学界业界双巨佬会在他慕课每一章节的最后采访一位业界的高管或者学界的高人，非常开阔眼界。

在这门课的 Project 中，你将用 C++ 循序渐进地搭建出整个 TCP/IP 协议栈，实现 IP 路由以及 ARP 协议，最后利用你自己的协议栈代替 Linux Kernel 的网络协议栈和其他学生的计算机进行通信，非常 amazing！

## 课程资源

- 课程网站：[https://cs144.github.io/](https://cs144.github.io/)
- 课程视频：[https://www.youtube.com/watch?v=r2WZNaFyrbQ&list=PL6RdenZrxrw9inR-IJv-erlOKRHjymxMN](https://www.youtube.com/watch?v=r2WZNaFyrbQ&list=PL6RdenZrxrw9inR-IJv-erlOKRHjymxMN)
- 课程教材：无
- 课程作业：[https://cs144.github.io/](https://cs144.github.io/)，8 个 Project 带你实现整个 TCP/IP 协议栈

## 资源汇总

- [PKUFlyingPig](https://github.com/PKUFlyingPig/CS144-Computer-Network)
- [Lexssama's Blogs](https://lexssama.github.io/tags/CS144/)
- [huangrt01](https://github.com/huangrt01/CS-Notes/blob/master/Notes/Output/Computer-Networking-Lab-CS144-Stanford.md)
- [kiprey](https://kiprey.github.io/tags/CS144/)
- [康宇 PL's Blog](https://www.cnblogs.com/kangyupl/p/stanford_cs144_labs.html)
- [doraemonzzz](http://doraemonzzz.com/tags/CS144/)
- [ViXbob's libsponge](https://vixbob.moe/25.html)
- [吃着土豆坐地铁的博客](https://www.epis2048.net/categories/Code/Stanford-CS144/)
- [Smith](https://www.inlighting.org/archives/2021-cs144-notes/)
- [星遥见](https://www.cnblogs.com/weijunji/tag/CS144/)
- [EIMadrigal](https://www.cnblogs.com/EIMadrigal/p/15500472.html)
- [Joey](http://yuzijun.life/2021-02/CS144)



今天写完了Lab 1，一顿操作throughput优化到了13.22Gbits，兴奋之余给几点tips

不要用std::map<uint64_t, std::string>，搜索区间顺而想到二叉树的想法没错，但std::map的insert和erase比较慢，尤其是erase，std::map适合海量数据下的查找，而非频繁的插入删除（如果想锻炼code能力的当我没说）
最容易想到的方法之一是std::unordered_map<uint64_t, char> 然而哈希表的写入和擦除速度同样感人，实测速度为0.3Gbits。这个方法唯一的好处就是写起来简单，调试简单。我当初写完就30多行。
用好std::move
鉴于本实验恶心的边界条件，不建议在初期就对代码做逻辑上的优化
（暗示）数据量不到50万的话，顺序结构也是很快的



CS144 2024 WINTER 重新开放了代码。我刚完成lab0并且写了一篇文章放在知乎上，链接是CS144 2024 WINTER Lab Checkpoint 0: networking warmup - 加德满都的风铃的文章 - 知乎 https://zhuanlan.zhihu.com/p/677623671
期待大家拉我建群一起讨论，qq：1223841837




===

熬了几天夜终于把lab4写完了，简单记录了一下lab4的debug过程，放在我仓库的README里了，没有大佬们的博客那么详细，希望能帮助到一些人。


1
1 条回复
@thewangcj
thewangcj
2 月 23 日
被lab4折磨了三天，我也是一个一个用例单独跑的，整的我怀疑人生，还以为自己的理解有问题，为什么大家的代码都这么简单，看了你这笔记才知道原来用的是 relaxed 版本啊，具体命令在 tests.cmake ：
add_custom_target (check_lab4 COMMAND "${PROJECT_SOURCE_DIR}/tun.sh" check 144 145
COMMAND ${CMAKE_CTEST_COMMAND} --output-on-failure --timeout 10 -R "^t_"
COMMENT "Testing libsponge...")
只会执行 t_ 开头的测试用例，我真的……


==

EIMadrigal的博客搬家了，https://eimadrigal.github.io/posts/cs144-lab/
Joey的博客我这边打不开，不知道其他人能不能打开