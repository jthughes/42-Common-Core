# 00 - Libft
## The Project
Reimplment 26 standard library functions using a strict style guide, compile them into a custom library, and add 11 (+ 9 for the bonus) additional useful functions for use in future projects.

## What I learned
This was a great way to start learning C:
- Most functions could be implemented in under 25 lines, allowing you to focus on writing small independent scripts.
- The style guide ([Norminette](https://github.com/42School/norminette)) put a number of restrictions, limiting line length, line count, variables in a function and more, making you take care to implement an efficient/elegant solution rather than just submitting anything that compiled and seemed to pass tests.
- No external functions were allowed (beyond ``write()`` and ``malloc()`` when relevant), again helping you think through the problem to understand how the functions worked rather than use other tools to avoid understanding the functions.
- The functions were all in 4-5 different groups, with many functions be variations on other functions in the same group, allowing you to reinforce learning.
- These different function groups covered a useful set of fundamental concepts: 
  - Working with C-strings (null terminated character arrays) (eg. strlcpy, strlcat, strncmp, etc.)
  - Working with memory (i.e. memcpy, memcmp, memmove, etc.)
  - Classifying ascii characters (eg. isalnum, isprint, etc.)
  - Printing characters, strings and numbers to filedescriptors
  - Converting strings to other data types (eg. atoi)
  - Working with linked lists

Below are some of the functions I found particularly interesting or challenging to implement.

### strlcat / strlcpy
The functions ``strlcat`` and ``strlcpy`` are the safer versions of ``strncat``, ``strcat``, ``strncpy``, and ``strcpy``.

For these sets of functions, the simplest versions (``strcpy`` and ``strcat``) rely on the user allocating sufficient memory at the destination. ``strncpy`` and ``strncat`` help by limiting the number of characters copied, which if it is set appropriately based on the size of the strings involved and the destination size could prevent attempts to copy data beyond the end of the memory allocation.

What makes these functions safer is that compared to the n versions, these return the number of bytes that should have been copied, which can be compared with the number of bytes provided to the function to copy to work out if the destination has a truncated result.

### memmove
This function does much the same thing as ``memcpy`` (that is, copy n bytes from source address to destination address) however it allows the destination and source to overlap (which ``memcpy`` does not allow). 

While a simple problem to solve, it was interesting working out how choose the direction (front to back, or back to front) to copy bytes to ensure a successful copy when the addresses did overlap.

### strncmp
While I ended up with a simple and elegant solution, this was one of the functions I experienced the most bugs when writing it. But the experience was valuable, as I've used the function a lot since, and mistakenly misused it a few of those times, where having done the work to implement it made it a lot easier to spot why my code was breaking when using the function.

It was also nice to learn the term "[lexicographic](https://en.wikipedia.org/wiki/Lexicographic_order)".

### putnbr
A custom functioned required by the project, this lets you write an integer to the a file descriptor.

This was a bit of a challenge, having to work out how long (in characters) the string representation of the number would be, handling negative numbers, and being careful with the max and min integers, given the way they are allocated, there is an extra negative number as compared to positive numbers (-2147483648 vs 2147483647). My initial naive approaches had further problems with the above whenever you had multiple zeroes in a number as well.

I ultimately ended up with a recursive solution, whereby I could handle the min int and negative cases, then use the recurse to handle printing results in the correct order.

```c
void	ft_putnbr_fd(int n, int fd)
{
	if (n == -2147483648)
	{
		write(fd, "-2147483648", 11);
		return ;
	}
	if (n < 0)
	{
		write(fd, "-", 1);
		n = -n;
	}
	if (n > 9)
		ft_putnbr_fd(n / 10, fd);
	n = n % 10 + '0';
	write(fd, &n, 1);
}
```

### split
Another custom function that would become very useful in later projects, split takes a C-string and a character, and splits up the string into a NULL-terminated array of strings with with the specified character being the separator.

Example:
```c
char** new_array = split("Hello there! General Kenobi...", ' ');
/*
array = ["Hello", "there!", "General", "Kenboi...", NULL]
*/
```

This was particularly useful in later projects like PipeX or Minishell, where you might want to take a string output and format it as an argument array for a call to a function like ``execve``.
