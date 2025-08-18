[[CS61B Data Structures and Algorithms|cs61b_index]]


[2.1 Mystery of the Walrus · Hug61B](https://joshhug.gitbooks.io/hug61b/content/chap2/chap21.html)
- 這裡的 copies the bits, 是指 binary
	- 如果是 primitive type, 就是那個值本身
	- 如果是非 primitive type (array, obj, instance), 那就是 address
- 因此 如果是 reference type, 那 a3 = new Dog(1,2) , a3 存的是 dog instance 的 memory address
![[IMG-cs61B linkedlist note-20241003104933842.png]]



![[IMG-cs61B linkedlist note-20241003104948782.png]]




[(608) [Lists2, Video 1C] Introducing the SLList - SLList Methods - YouTube](https://www.youtube.com/watch?v=inV6cAEERRI&list=PL8FaHk7qbOD4cp06tWA8i9m20pQLvcgE7&index=3)
a wrapper is more ez to use 
![[IMG-cs61B linkedlist note-20241003105001928.png|735]]



invariants on single linked list
![[IMG-cs61B linkedlist note-20241003105002043.png|793]]

==

array list
- why not just use linked list?
	- say you have 1 million element and you want to get the middle item -> u  need to scan it!  
	- some way to solve it, like use array, or tree? but we begin with array here.