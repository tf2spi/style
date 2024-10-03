# CPP Style Guidelines

* Everything from the ``C`` style guidelines is still applicable here
* Please, for the love of god, when making a library...
	- Don't use RTTI nor exceptions. Those are disabled on some platforms and compilers
	- Use ``C`` linkage on exports so dynamically linked users don't have to deal with name mangling
	- Make the core structs and functions compatible with ``C``
	- You may then make ``C++`` wrappers and bindings over these core structs and functions
		* This makes ``RAII`` and friends an **optional** nicety rather than a requirement
