# 01 - ft_printf
## The Project
Build a simplified (without buffering with only a subset of the format flags) version of printf for use in the other projects.

## What I learned
This project introduced using the stdarg.h header to provide access to the va_arg functions/macros, allowing for a variable sized list of arguments in our functions.

To use these, we declare a va_list variable to reference the variable argument list. We use ``va_start``, providing it the va_list we declared, and provide it the name of the last of our fixed arguments (it requires us to have a least one fixed argument). This done, we can retrieve arguments from the va_list one at time using ``va_arg``.

``va_arg`` introduces the biggest problem with working with these arguments - we have to specify the type of the argument we're trying to access without the ability to test for the type, and we have no indication of how many arguments have been passed.

I later used these to create a ``strconcat`` function (see below), where I was able to solve these problems by having the function be for joining strings (well, character arrays) together, so this will only work if provided character arrays, and I require that the final argument be NULL, as if we had provided a NULL terminated array of strings.
```c
char	*ft_strconcat(char *str, ...)
{
	va_list	argv;
	char	**array;
	int		index;
	int		size;
	char	*output;

	va_start(argv, str);
	array = import_args(str, argv);
	if (!array)
		return (NULL);
	size = ft_strarr_characters(array);
	output = malloc(sizeof(char) * (size + 1));
	if (!output)
	{
		free(array);
		return (NULL);
	}
	index = 0;
	output[0] = '\0';
	while (array[index])
		ft_strlcat(output, array[index++], -1);
	free(array);
	return (output);
}
```

For printf, we solve these problems with the format string. Inside the string, using % followed by particular characters, we indicate that variables of particular types will be provided in the specified order.

```c
int	ft_printf(const char *s, ...)
{
	va_list		args;
	int			index;
	int			written_bytes;
	t_bool		error;
	t_format	format;

	va_start(args, s);
	index = 0;
	written_bytes = 0;
	error = FALSE;
	while (s[index] && !error)
	{
		while (s[index] && s[index] != '%')
			written_bytes += write_e(STDOUT_FILENO, &s[index++], 1, &error);
		if (!s[index] || error)
			break ;
		index++;
		set_format(&format, s, &index, &error);
		written_bytes += apply_conversion(&format, args, &error);
		index++;
	}
	if (error)
		return (-1);
	return (written_bytes);
}
```

My approach to implementing printf was to not worry about buffering and just print characters as quickly as we can. So we move through a tring writing out each character until we encounter a '%' character. From there, we detect what sort of variable we are working with, with the associated formatting requirements, print the result of converting the value to a formatted string, and then continue working through the format string.

Of particular challenge with this project was attempting the bonuses, involving the various formatting options for strings, pointers, integers, etc.

```c
int	print_p(void *p, t_format *format, t_bool *error)
{
	if (ON_LINUX && !p)
	{
		format->use_precision = FALSE;
		return (print_s("(nil)", format, error));
	}
	if (!format->f_hash)
		format->f_hash = TRUE;
	return (print_u((unsigned long) p, format, error));
}

int	print_u(unsigned long n, t_format *format, t_bool *error)
{
	const int	size = init_size(n, format);
	int			precison_padding;
	int			padding;
	int			count;

	count = 0;
	precison_padding = init_precision_padding(format, size);
	padding = init_padding(format, size, precison_padding, n);
	if (format->use_precision && !(format->precision) && !n)
		padding += 1;
	if (format->use_field_width && !format->f_dash
		&& !(format->f_zero && !format->use_precision))
		count += putchars_e(' ', padding, STDOUT_FILENO, error);
	if (format->f_hash && (n || format->conversion == 'p'))
		count += apply_alt_form(n, format->conversion, error);
	if (format->use_field_width && !format->f_dash
		&& (format->f_zero && !format->use_precision))
		count += putchars_e('0', padding, STDOUT_FILENO, error);
	count += putchars_e('0', precison_padding, STDOUT_FILENO, error);
	if (!(format->use_precision && !(format->precision) && !n))
		count += putnbr_u_e(n, format->conversion, STDOUT_FILENO, error);
	if (format->use_field_width && format->f_dash)
		count += putchars_e(' ', padding, STDOUT_FILENO, error);
	return (count);
}
```
Developing on Ubuntu from home, and MacOS on campus, I quickly discovered that the printf specification does not specify all of the details you might think, leaving aspects up to the implementer to decide. For example, how difference systems formatted the pointer address 0 vs a null string vs the 0 unsigned int varied.

It also took awhile to balance the various format options in the right order, in a way that was consistent between different types of variables. You can see what I ended up with the sample above.

In the end, I didn't quite manage to achieve the full 125% - the bonus which required implementing precision and field-width required implementing the * option in place of a number, indicating that the length/precision would be specified by one of the va_args. I believe I'd need to rearchitecture my approach as in the first instance, I would need to pass the args list through the function evaluating the field width to retrieve the next value. But when there's also the option of specifying *which* numbered item in the va_list to use, that fundamentally changes the required approach.

If I approached implementing this function again, I would try to do a few things differently. To support specifying which argument to retrieve the field width from, I would do a parsing pass on the format string first to identify how many args to expect, and of what types. I'd use that to process the va_list into some sort of custom dictionary type structure so that I have the full list of variables ready to access. I would also consider trying to implementing ``sprintf`` instead as I think all of the other printf variants, including printf could be simply implemented using sprintf: creating a formatted string, and then outputing the string to STDOUT, or a specified fd, etc. 