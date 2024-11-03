# 01 - get_next_line
## The Project
Build a function that can be added to your personal library that returns the next line of text from a file descriptor.

## What I learned
This felt like a simple problem but ended up being more involved than I expected in trying to appropriately work with various types of source.

One of the challenges was the restriction to only use ``read``, ``malloc``, and ``free``. This prevented use of functions to scan through a file and identify the location of `\n` characters first before allocating and copying data, although that sort of approach would have worked anyway - not all file descriptors point to text files. Notably, the standard input (reserved file descriptor 0, although the ``dup`` functions could redirect those) is a potential source to read a line from, with pipes (explored later in PipeX and minishell) acting similar, where once bytes have been read from the buffer, they are removed and cannot be reread. And while not necessarily explored (it could come up in ft_irc or webserv), network sockets are readable file descriptors also which could similarly be a concern.

The solution required us then to read data into a buffer until a new line was found, create a new string of that new line, remove it from the buffer, and retain any leftover bytes. Due to applications like receiving data over the network, where fewer larger requests might be more performant, this buffer needed to be adjustable at compile time.

Balancing the process ended up being more of a delicate balance that expected, particularly when also supporting reading from multiple open file descriptors at the same time, but it was a valuable exercise to work through, and provided an opportunity to learn about static variables (used to retain the left over buffered data between function calls). 

