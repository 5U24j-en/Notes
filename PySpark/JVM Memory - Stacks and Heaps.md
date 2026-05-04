

- Machine Code
- Stack - Data Structure
	- You can add things in a stack and remove things from the stack only from the above.
	- You cant add / remove anything from the middle or Bottom
	- Example - Lets have a Program  
		- Create a Variable and assign a value
		- To create a variable you need to allocate some Memory
		- Everything requires memory , like Function
		- Inside the function you declare another variable , the function works and completes.
		- Now you can pop the allocated memory from the stack and continue the program
	

- Heap 
	- You can store items in any order and access it from anywhere
	- Adding items to Heap is more expensive than adding items to a Stack due to the flexibility
	- Heap uses a **Pointer** (**A memory address**) that is stored in the stack pointing to address of the **actual values of the variable in the Heap**.
	- Objects are Stored in Heap
	


Reference Types on the HEAP
Value Types on Stack or Heap

## Application Memory

- ![](../attachments/Pasted%20image%2020260424221620.png)

- **Example Code**
```java
#include <stdio.h>
int total;

int Square(int x)
{
    return x * x;
}
int SquareOfSum(int x, int y)
{
    int z = Square(x + y);
    return z;
}
int main()
{
    int a = 4, b = 8;
    total = SquareOfSum(a, b);
    printf("output = %d", total);
}
```


- ## Stack 

![](../attachments/Pasted%20image%2020260424220332.png)

![](../attachments/Pasted%20image%2020260424220839.png)

![](../attachments/Pasted%20image%2020260424221056.png)

### Note
- Memory allocated for Stack **DOES NOT GROW** in runtime
- Allocation and Deallocation happens by set of rules 
	- New function added on top of stack
	- Function completed , then removed from the top of the stack
- If we allocating a large Data Type like array , we need to know the size of the Array to allocate in the stack


- ## Heap

```java
#include <stdio.h>
#include <stdlib.h>
int main()
{
    int a; // goes on stack
    int *p;

    p = (int*)malloc(sizeof(int));
    *p = 10;
    
    free(p);
    
    p = (int*)malloc(20 * sizeof(int));
}

```

![](../attachments/Pasted%20image%2020260424222639.png)
![](../attachments/Pasted%20image%2020260424223237.png)


### Note
- Size can vary in runtime
- Not Set rule for allocation and deallocation of memory
- Programmer has the control of the Memory
- Free store of memory
- **In Languages like C we need to manually control the memory in Heap**
- **In Languages like Python, Java we have Garbage collectors that periodically remove Free variables in heap**
- A **memory leak** happens when a program allocates memory (usually on the heap) but **never releases it back to the system**, even when it’s no longer needed.
- Garbage Collector only removes objects that are **no longer reachable**.

👉 If your program still holds a reference to an object (even if you don’t actually need it), **GC will NOT remove it**.