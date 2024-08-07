# 05 - C++ Module 06
## The Project

## Research 
- [Article on C++ casts](https://cplusplus.com/articles/iG3hAqkS/)
- [Article on (Un)Serialization](https://isocpp.org/wiki/faq/serialization)
- [Using nan & inf (StackOverflow)](https://stackoverflow.com/questions/1923837/how-to-use-nan-and-inf-in-c)
- [dynamic_cast (CPP Reference)](https://en.cppreference.com/w/cpp/language/dynamic_cast)
- [Typecasting (C Plus Plus)](https://cplusplus.com/doc/oldtutorial/typecasting/)

## What I learned
There are four casting operators in C++
  - ``static_cast``
  - ``reinterpret_cast``
  - ``dynamic_cast``
  - ``const_cast``
``const_cast`` allows you to cast a const variable to a non-const variable, or visa versa. But given you can generally implicitly cast a non-const variable to a const variable, and you should never modify a value marked as const, this shouldn't be used.

Consquentially, the three exercises of this module are designed to use, and in fact require, each of the other types of casts.
Regarding the other types:
 - ``static_cast``
   - Essentially allows for making explicit implicit casts. 
   - Lets us cast between equivalent types:
      - int arr[] <-> int* arr;
	  - int a <-> enum a;
	  - (void *) <-> (int *)
   - Lets us do inializing conversion (casting number types to each other, like ints to doubles, or doubles to int)
 - ``reinterpret_cast``
   - Tells the compiler to reinterpet a given pointer as a different type. 
   - Can convert a pointer into an integer (something static_cast cannot do).
   - Cannot change the 'const'ness of a value.
 - ``dynamic_cast``
   - Lets us cast between different parts of an inheritance tree.
     - When casting a base pointer to a derived class, returns null pointer if the target pointer doesn't align with the memory stored.
	 - When casting a base reference to a derived class, will throw an exception if insufficient derived class.
