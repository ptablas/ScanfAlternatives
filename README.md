# ScanfAlternatives


Self-feedback in response of using scanf() in combination with while ((c = getchar()) != '\n' && c != EOF).
Is Messy and there are better options.

This document, code snippet and functions are based on: "A beginners' guide away from scanf()" by Felix Palmen.
Published 2017-06-08 on sekrit.de

Reasons why NOT use scanf() as exposed on article:

	1. If scanf() is asked to convert a number && No number is present at input:
		- scanf() will not write anything into the used variable.

		- that variable is now unitialized.
		- UNDEFINED BEHAVIOUR -> output = (WHATEVER is in that register || CRASH) && SECURITY ISSUES.

	2. If while no number is present we scan:
		- scanf() will never stop scanning.

		- it does not read, it parses. Hence, data is still on input buffer.
		- using fflush(stdin) is UNDEFINED BEHAVIOUR. Only way to clean an input buffer is by READING IT.

	3. If scaning a string > n, which is then assigned to char[n]
		- scanf() does not know where to stop and tries to assign more data to the array than it's been made available.

		- BUFFER OVERFLOW -> UNDEFINED BEHAVIOUR that overwrites data placed after overflowed buffer
		- if overflowed re-writes a function's return address: user could ask any other code to be run -> SECURITY ISSUES

		TO AVOID BUFFER OVERFLOW:
		- field widths should be used: if array is 40 chars long, scanf should not read more than 39 (strings need finish w/ a 0)

		BUT STILL:
		- scanf() parses words not strings. i.e. it parses strings that finish at the next white space.
		- something different can be specified using %[*] (parses as long as input chars are *).
		- but now it will not skip new lines. if input is only a new line: UNDEFINED BEHAVIOUR. (will scan anything/crash/security issues).

SAFER/CLEANER ALTERNATIVES:

	1. char * fgets(char *str, int n, FILE *stream)
		- n is size of string to be read + accounts for 0 byte at end.
		- if nothings is read return is ptr to NULL
		
		ISSUE: it will read the \n char as well, it needs to be removed: ex. name[strcspn(name, "\n")] = 0;

	2. For numbers: fgets() with strtol(const char *nptr, char **endptr, int base).
		- endptr points to first character that could not be converted.
		- base allows numeric base of input. ex. 2 for binary, 10 for usual numbering system.
		- it sets errno: allows to check range of available numbers (i.e. whether numbers are too small/big for conversion).
