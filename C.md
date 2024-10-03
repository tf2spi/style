# C Coding Style Guidelines

* When making libraries, remember your project may be ported/compiled in all kinds of projects, so...
	- Compile with all non-experimental warnings and error on warning
		* ``gcc/clang``: ``-Wall -Wextra -Werror``
		* ``msvc``: ``/W4 /WX``
	- Adhere to ``C99`` and ``C++14`` standards
		* ``gcc/clang``: ``-pedantic -std=c99``
		* ``g++/clang++``: ``-pedantic -std=c99``
		* ``msvc``: ``/std:c++14 /permissive-``
* When building for both debug and release, add ``-Wformat=2 -D_FORTIFY_SOURCE=2`` when compiling with ``gcc/clang``
	- These are not on by default with ``-Wall -Wextra`` but can really save you from simple screwups, especially in release
* When building for debug, turn on various sanitizers
	- Even when security isn't a concern, it ends up catching bad reads/writes sooner rather than later, helping debuggability
	- Note that both ``msvc``  and ``clang/gcc`` will include sanitizers so check out the options for those
* When building for release, if there are pointer/memory tagging extensions, like ``PAC`` or ``MTE`` on ``ARM``, use those
	- They have hardly any performance penalty and make exploits much less consistent
	- Memory tagging also catches bad reads/writes sooner rather than later so they help debugging release builds
	- For ``MTE``, use mode ``SYNC`` for debug and ``ASYNC`` for release (though this should be default anyways)
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
	a = 1,
	b = 2,
	c = 3,
	related_reserved_x_y_z = 0x7fffffff,
};

static const int x = 1;
#define x 1
static const int y = 2;
#define y 2

// In ISO C99, enums are always int, so this would've been illegal to put in enum
static const int64_t z = 0x800000000;
#define z 0x800000000
```

