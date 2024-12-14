# 05 - C++ Module 05
## The Project
This project was all about learning to use C++ exceptions with the try / catch blocks.
## What I learned
- ``noexcept`` is a ``C++11`` keyword ([Wikipedia](https://en.wikipedia.org/wiki/C++11#Features_removed_or_deprecated), [CPP Reference](https://en.cppreference.com/w/cpp/language/noexcept_spec)) which replaces the (initially) counterintuitively named ``throw()`` ([CPP Reference](https://en.cppreference.com/w/cpp/language/except_spec)) exception specifier available in ``c++98``.
- ``throw()`` is actually used to provide a list of types it throws. By specifying without any types, you are indicating that no type will be thrown, i.e. it doesn't throw any exceptions. 