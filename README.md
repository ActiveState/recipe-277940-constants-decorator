This repository covers the following recipe from code.activestate.com:

[DECORATOR FOR BINDINGCONSTANTS AT COMPILE TIME
](https://code.activestate.com/recipes/277940-decorator-for-bindingconstants-at-compile-time/)

*Created by Raymond Hettinger on Tue, 13 Apr 2004*

Decorator for automatic code optimization. If a global is known at compile time, replace it with a constant. Fold tuples of constants into a single constant. Fold constant attribute lookups into a single constant.

When hand optimizing functions, it is typical to include lines like _len=len for looking-up the len function at compile time and binding to a local constant. The speed-up is substantial (trading one or more dictionary lookups for a single C-speed array lookup).

make_constants() performs those optimizations automatically; leaving the original code free of clutter like _len=len.

In the example, observe replacements of "random" from the global namespace and "len", "ValueError", "list", "xrange", and "int" from the builtin namespace. All of those are recoded as constants. If "random" had not already been imported at binding time, then it would have been left as a runtime global lookup.

Constant binding is especially useful for:

- Functions with many references to global constants (sre_compile for example).
- Functions relying on builtins (such as len(), True, and False).
- Functions that raise or catch exceptions (such as IndexError).
- Recursive functions (to optimize the function's reference to itself).
- Functions that call other functions defined in the same module.
- Functions accessing names imported into the global namespace (i.e. from random import random).
- After binding globals to constants, the decorator makes a second pass and folds constant attribute lookups and tuples of constants. The first frequently occurs with module references (such as string.hexdigits). The second commonly occurs in for-loops and conditionals using the "in" operator such as: for x in ('a','b','c').

Binding should be applied selectively to those functions where speed is important and dynamic updates are not desired (i.e. the globals do not change). In more dynamic environments, a more conservative approach is to set builtin_only to True so that only the builtins get optimized (this includes functions like len(), exceptions like IndexError, and constants like True or False). Alternatively, individual variable names can be added to a stoplist so that the function knows to leave them unchanged.

Note, make_constants() works like other function decorators such as classmethod() and staticmethod(), so it can be used inside a class definition to optimize methods.

For versions of Python before Py2.4, the @decorator syntax was not available. To invoke the decoration, do so after the function is defined with:

```
sample = _make_constants(sample, verbose=True)
```

Also, due to the way decorators work, the latter form must be used to optimize recursive function calls.

Instead of decorating every function and method in a module, the bind_all() utility function is provided. Call bind_all(my_class) after the class definition or put bind_all(sys.modules[__name__]) as the last line in a module to be optimized.

## Usage

If you already have the [State Tool] installed you can simply run

```
state activate ActiveState-Recipes/ActiveState-Recipes/recipe-277940-constants-decorator
```

If you do not have the [State Tool] installed you can use the following convenient one-liner.

Linux: 
```
sh <(curl -q https://platform.activestate.com/dl/cli/install.sh) -n -f && state activate --path $HOME/ActiveState-Recipes/ActiveState-Recipes/recipe-277940-constants-decorator ActiveState-Recipes/ActiveState-Recipes/recipe-277940-constants-decorator
```

Windows: 
```
powershell "Set-Item -Path Env:NOPROMPT_INSTALL -Value 'true'; IEX(New-Object Net.WebClient).downloadString('https://platform.activestate.com/dl/cli/install.ps1')" && state activate --path %APPDATA%/ActiveState-Recipes/ActiveState-Recipes/recipe-277940-constants-decorator ActiveState-Recipes/ActiveState-Recipes/recipe-277940-constants-decorator
```

macOS: not yet supported

[State Tool]: https://www.activestate.com/products/platform/state-tool/