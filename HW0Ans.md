## Chapter 1

In which our intrepid hero battles standard out, standard error, file descriptors and writing to files.

### Hello, World! (system call style)
1. Write a program that uses `write()` to print out "Hi! My name is `<Your Name>`".

```c
#include <unistd.h>
int main() {
	write(1, "Hi! My name is Mohammad Zayyad\n", 32);
	return 0;
}
```



### Hello, Standard Error Stream!
2. Write a function to print out a triangle of height `n` to standard error.
   - Your function should have the signature `void write_triangle(int n)` and should use `write()`.
   - The triangle should look like this, for n = 3:
   ```C
   *
   **
   ***
   ```
```C
#include <unistd.h>
void write_triangle(int n){
	int len;
	for(len = 1; len <= n; len++){
		int line;
		for (line = 0; line < len; line++){
			write(1, "*", 1);
		}
		write(1, "\n", 2);
	}
}
```
### Writing to files
3. Take your program from "Hello, World!" modify it write to a file called `hello_world.txt`.
   - Make sure to to use correct flags and a correct mode for `open()` (`man 2 open` is your friend).
```C
#include <unistd.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <fcntl.h>
int main() {
	mode_t mode = S_IRUSR | S_IRUSR;
	int fildes = open("hello_world.txt", O_CREAT | O_TRUNC | O_RDWR , mode);
	write(fildes, "Hi! My name is Mohammad Zayyad\n", 32);
	close(fildes);
	return 0;
}
```
### Not everything is a system call
4. Take your program from "Writing to files" and replace `write()` with `printf()`.
   - Make sure to print to the file instead of standard out!
```C
#include <stdio.h>
#include <unistd.h>
#include <sys/stat.h>
#include <sys/types.h>
#include <fcntl.h>

int main() {
	mode_t mode = S_IRUSR | S_IRUSR;
	close(1);
	int fildes = open("hello_world.txt", O_CREAT | O_TRUNC | O_RDWR , mode);
	printf("Hi! My name is Mohammad Zayyad\n");
	close(fildes);
	return 0;
}
```

5. What are some differences between `write()` and `printf()`?

Write() has the ability to specify which output stream you want to write to, whether that a file, stdout, or the error logs. For write(), you also need to declare the number of bytes you want to write out, whereas printf() does that for you. Additionally, with printf() you can print variable values and it doesn't need to be constant. In order to use printf() to print to a file you need to close the main output stream then open the file so that printf() when printing to output 1 will print to the file. 

## Chapter 2

Sizing up C types and their limits, `int` and `char` arrays, and incrementing pointers

### Not all bytes are 8 bits?
1. How many bits are there in a byte?
```C
There are at least 8 but the amount is based on the architecture
```

2. How many bytes are there in a `char`?

```C
One
```

3. How many bytes the following are on your machine?
   - `int`, `double`, `float`, `long`, and `long long`

```C
Respectively: 4, 8, 4, 4, 8
```

### Follow the int pointer
4. On a machine with 8 byte integers:
```C
int main(){
    int data[8];
} 
```
If the address of data is `0x7fbd9d40`, then what is the address of `data+2`?

```C
0x7fbd950
```

5. What is `data[3]` equivalent to in C?
   - Hint: what does C convert `data[3]` to before dereferencing the address?

```C
data + 3*sizeof(int)
```


### `sizeof` character arrays, incrementing pointers
  
Remember, the type of a string constant `"abc"` is an array.

6. Why does this segfault?
```C
char *ptr = "hello";
*ptr = 'J';
```
```C
Because you're attempting to change the value of a constant
```

7. What does `sizeof("Hello\0World")` return?

```C
12
```

8. What does `strlen("Hello\0World")` return?
```C
5
```

9. Give an example of X such that `sizeof(X)` is 3.
```C
char* X = "ab";

```

10. Give an example of Y such that `sizeof(Y)` might be 4 or 8 depending on the machine.

```C
long Y = 0;
```

## Chapter 3

Program arguments, environment variables, and working with character arrays (strings)

### Program arguments, `argc`, `argv`
1. What are two ways to find the length of `argv`?

```C
using argc and iterative through argv until you reach a null char*
```

2. What does `argv[0]` represent?
```C
The execution call
```

### Environment Variables
3. Where are the pointers to environment variables stored (on the stack, the heap, somewhere else)?
### String searching (strings are just char arrays)
4. On a machine where pointers are 8 bytes, and with the following code:
```C
char *ptr = "Hello";
char array[] = "Hello";
```
What are the values of `sizeof(ptr)` and `sizeof(array)`? Why?

### Lifetime of automatic variables

5. What data structure manages the lifetime of automatic variables?

## Chapter 4

Heap and stack memory, and working with structs

### Memory allocation using `malloc`, the heap, and time
1. If I want to use data after the lifetime of the function it was created in ends, where should I put it? How do I put it there?
2. What are the differences between heap and stack memory?
3. Are there other kinds of memory in a process?
4. Fill in the blank: "In a good C program, for every malloc, there is a ___".
### Heap allocation gotchas
5. What is one reason `malloc` can fail?
6. What are some differences between `time()` and `ctime()`?
7. What is wrong with this code snippet?
```C
free(ptr);
free(ptr);
```
8. What is wrong with this code snippet?
```C
free(ptr);
printf("%s\n", ptr);
```
9. How can one avoid the previous two mistakes? 
### `struct`, `typedef`s, and a linked list
10. Create a `struct` that represents a `Person`. Then make a `typedef`, so that `struct Person` can be replaced with a single word. A person should contain the following information: their name (a string), their age (an integer), and a list of their friends (stored as a pointer to an array of pointers to `Person`s).
11. Now, make two persons on the heap, "Agent Smith" and "Sonny Moore", who are 128 and 256 years old respectively and are friends with each other.
### Duplicating strings, memory allocation and deallocation of structures
Create functions to create and destroy a Person (Person's and their names should live on the heap).
12. `create()` should take a name and age. The name should be copied onto the heap. Use malloc to reserve sufficient memory for everyone having up to ten friends. Be sure initialize all fields (why?).
13. `destroy()` should free up not only the memory of the person struct, but also free all of its attributes that are stored on the heap. Destroying one person should not destroy any others.

## Chapter 5 

Text input and output and parsing using `getchar`, `gets`, and `getline`.

### Reading characters, trouble with gets
1. What functions can be used for getting characters from `stdin` and writing them to `stdout`?
2. Name one issue with `gets()`.
### Introducing `sscanf` and friends
3. Write code that parses the string "Hello 5 World" and initializes 3 variables to "Hello", 5, and "World".
### `getline` is useful
4. What does one need to define before including `getline()`?
5. Write a C program to print out the content of a file line-by-line using `getline()`.

## C Development

These are general tips for compiling and developing using a compiler and git. Some web searches will be useful here

1. What compiler flag is used to generate a debug build?
2. You modify the Makefile to generate debug builds and type `make` again. Explain why this is insufficient to generate a new build.
3. Are tabs or spaces used to indent the commands after the rule in a Makefile?
4. What does `git commit` do? What's a `sha` in the context of git?
5. What does `git log` show you?
6. What does `git status` tell you and how would the contents of `.gitignore` change its output?
7. What does `git push` do? Why is it not just sufficient to commit with `git commit -m 'fixed all bugs' `?
8. What does a non-fast-forward error `git push` reject mean? What is the most common way of dealing with this?

## Optional (Just for fun)
- Convert your a song lyrics into System Programming and C code and share on Ed.
- Find, in your opinion, the best and worst C code on the web and post the link to Ed.
- Write a short C program with a deliberate subtle C bug and post it on Ed to see if others can spot your bug.
- Do you have any cool/disastrous system programming bugs you've heard about? Feel free to share with your peers and the course staff on Ed.
