# C Coding Style Guidelines

* When making macros, make them ``SCREAMING_CASE`` so they're easy to identify
	- Macros may not be valid for all expressions and can have interesting side effects
	- Some debuggers treat macros specially so it's useful to be able to identify what is a macro
	- If macros are used for compatibility (like ``errno`` in ``glibc``), at least document the case
* Prefer to make constants using ``static const`` or ``enum`` over making it a macro
	- Some debug files don't support macro expansion and some reserve it at higher debug levels (like ``-g3``)
	- If it must be a macro (such as array size, switch case, or is exported), this can work as well
```
static const int myconst = 2;
#define myconst 2
```
* If a group of constants are related, use ``enum`` to define the constants rather than macros. Otherwise, use ``static const``.
	- This makes it easy to dump the enum in a debugger (like ``ptype`` in ``gdb``)
	- If related constants can't be represented in the ``enum``, reserve a place in enum with their names instead
```
enum related {
	A = 1,
	B = 2,
	C = 3,
	RELATED_RESERVED_X_Y_Z = 0x7fffffff,
};

static const int X = 1;
#define X 1
static const int Y = 2;
#define Y 2
static const int64_t Z = 0x800000000;
#define Z 0x800000000
```
* When making libraries, remember your project may be ported/compiled in all kinds of projects, so...
	- Compile with all non-experimental warnings and error on warning
		* ``gcc/clang``: ``-Wall -Wextra -Werror``
		* ``msvc``: ``/W4 /WX``
	- Adhere to ``C99`` and ``C++14`` standards
		* ``gcc/clang``: ``-pedantic -std=c99``
		* ``msvc``: ``/std:c++14 /permissive-``
