[[low_level_idx]]


---

[(592) a strange but powerful interview question - YouTube](https://www.youtube.com/watch?v=V2h_hJ5MSpY)


這個會出現 up
不知道 why
maybe due to some compiler optmize
```c

#include <stdio.h>
#include <stdbool.h>


// but this is not a good idea
// since the stack 
bool up_or_down()
{
   // volatile 可以强制编译器不优化
   volatile int x, y = 0;

   if (&x > &y)
   {
      return true;
   }
   return false;
}

int main()
{
   printf("%s\n ", up_or_down() ? "up" : "down");
}

```



this will show down as expected
```c
#include <stdio.h>
#include <stdbool.h>

bool up_or_down(int *other)
{
   int x;

   if (!other) // 1. null will go here, first time
   {
      return up_or_down(&x); // 2. and then call itself
   }

   // second time, it will go here, since other is not null
   // and if x ptr is bigger than other, it will return true
   // means the stack is up, o.w. the stack is down
   return &x > other;
}

int main()
{
   printf("%s\n ", up_or_down(NULL) ? "up" : "down");
}

```