Limit source files to a single top-level class

就是一個 file 一個 top-level class

这个教训很清楚:永远不要将多个顶级类或多個接口放在一个源文件中。 遵循这个规则保证在编译时不能有多个定义。 这又保证了编译生成的类文件以及生成的程序的行为与源文件传递给编译器的顺序无关。

(要看例子看原文)