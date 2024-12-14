# 05 - C++ Module 08
## The Project
Learn how to use templates by implementing a function template, a class with a method template, and a class template, while also learning to utilise the Standard Template Library headers, namely Algorithms and Containers.

## Research 
- https://stackoverflow.com/questions/18613770/stl-container-as-a-template-parameter#answer-18614245
- https://stackoverflow.com/questions/21329307/return-an-iterator-inside-a-template
- https://cplusplus.com/doc/oldtutorial/templates/
- https://www.youtube.com/watch?v=Fv8oj8EdssY
- https://stackoverflow.com/questions/525365/does-stdstack-expose-iterators

## What I learned
Templates are certainly nice - I adapted my logging class to utilise a template with a template specialisation rather than half a dozen different overloads to handle different data types:
```c
enum LOG_COLOR
{
	COLOR_NONE,
	COLOR_RED,
	COLOR_GREEN,
	COLOR_YELLOW,
	COLOR_BLUE,
	COLOR_MAGENTA,
	COLOR_CYAN,
	COLOR_RESET
};

class	Log
{
	public:
		static void	out(std::string message, LOG_COLOR color = COLOR_NONE);
		static void	nl(std::string message = "", LOG_COLOR color = COLOR_NONE);

		template <typename T>
		static std::string str(T t) 
		{
			std::stringstream	stream;
			stream << t;
			return (stream.str());
		}
		
		template <>
		std::string str<bool>(bool t)
		{
			if (t)
				return "true";
			return "false";
		}

	private:
		Log();
		Log(Log& instance);
		Log& operator=(Log copy);
		~Log();
		void	swap(Log& other);

		static void output(std::string message, LOG_COLOR color = COLOR_NONE);

		static std::string const colors[8];
};
```
It was interesting to get a feel for how the standard library containers were implented by expanding on one using a class template. 