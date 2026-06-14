# Python Code Format Research

This reference serves the `python-code-format` skill.

Within the scope of this skill and its related documents, Python 3.12 is the baseline for language and style guidance. This statement does not by itself update the Python version baseline of the repository as a whole.

## Line Length

1. Keep each line within 80 characters, except in the following cases:
   - long import statements
   - URLs in comments
   - Pylint disable comments, such as `# pylint: disable=invalid-name`

2. Do not use backslashes for line continuation. The following examples are incorrect:

```python
if width == 0 and height == 0 and \
    color == 'red' and emphasis == 'bold':

bridge_questions.clarification_on \
    .average_airspeed_of.unladen_swallow = 'American or European?'

with very_long_first_expression_function() as spam, \
        very_long_second_expression_function() as beans, \
        third_thing() as eggs:
    place_order(eggs, beans, spam, beans)
```

3. Python implicitly joins lines inside parentheses, brackets, and braces. You can use this to handle text that does not fit on one line. For example:

```python
x = ('loooooooooooooooooooogloooooooooooo'
     'looooooooogooooooooooooooooooooooooooooog')

foo_bar(self, width, height, color='black', design=None, x='foo',
        emphasis=None, highlight=0)

if (width == 0 and height == 0 and
    color == 'red' and emphasis == 'bold'):

(bridge_questions.clarification_on
 .average_airspeed_of.unladen_swallow) = 'American or European?'

with (
    very_long_first_expression_function() as spam,
    very_long_second_expression_function() as beans,
    third_thing() as eggs,
):
    place_order(eggs, beans, spam, beans)
```

4. If necessary, a long URL may remain on one line inside a comment. A correct example is:

```python
# See details at
# https://www.example.com/us/developer/documentation/api/content/v2.0/csv_file_name_extension_full_specification.html
```

---

## Indentation

1. Use 4 spaces for indentation.

2. Never use tabs, and never mix tabs with spaces.

3. For line continuation, either align wrapped elements vertically or use a 4-space hanging indent.

```python
# Yes: aligned with the opening expression
foo = long_function_name(var_one, var_two,
                         var_three, var_four)
# Aligned under the opening value in a dict
foo = {
    long_dictionary_key: value1 +
                         value2,
    ...
}
# Four-space indent in a dict
foo = {
    long_dictionary_key:
        long_dictionary_value,
    ...
}
```

```python
# No: the first wrapped line must not contain an element in this form
foo = long_function_name(var_one, var_two,
    var_three, var_four)
# No: the indentation handling in the dict is wrong
foo = {
    long_dictionary_key:
        long_dictionary_value,
        ...  # Note the difference here
}
```

---

## Blank Lines

1. Leave two blank lines between top-level definitions, such as functions or classes.

2. Leave one blank line between method definitions. Also leave one blank line between a class definition and its first method.

---

## Spaces

1. Do not put spaces inside parentheses.

```python
# Yes
spam(ham[1], {eggs: 2}, [])
# No
spam( ham[ 1 ], { eggs: 2 }, [ ] )
```

2. Do not put spaces before commas, semicolons, or colons, but do put spaces after them.

```python
# Yes
if x == 4:
    print(x, y)
x, y = y, x
```

```python
# No
if x == 4 :
    print(x, y)
x , y = y , x
```

3. Do not put a space before the left parenthesis in a parameter list, index operation, or slice.

```python
# Yes
spam(1)
dict['key'] = list[index]

# No
spam (1)
dict ['key'] = list [index]
```

4. Put one space on both sides of binary operators, such as assignment (`=`), comparison (`==`, `<`, `>`, `!=`, `<=`, `>=`, `in`, `not in`, `is`, `is not`), and boolean operators (`and`, `or`, `not`).

Note: the `<>` operator was completely removed in Python 3 and must not be used.

```python
# Yes
x == 1
# No
x<1
```

5. When `=` is used to indicate a keyword argument or a default parameter value, do not put spaces around it.

```python
# Yes
def complex(real, imag=0.0): return magic(r=real, i=imag)
# No
def complex(real, imag = 0.0): return magic(r = real, i = imag)
```

6. Do not use spaces to vertically align tokens across multiple lines, because this creates a maintenance burden. This applies to `:`, `#`, `=`, and similar tokens.

```python
# Yes
foo = 1000  # Comment
long_name = 2  # Comments do not need alignment

dictionary = {
    'foo': 1,
    'long_name': 2,
}
```

```python
# No
foo           = 1000  # Comment
long_name     = 2     # Comments do not need alignment

dictionary = {
    'foo'      : 1,
    'long_name': 2,
}
```

---

## Shebang

Most `.py` files do not need to begin with `#!`. According to [PEP 394](http://www.python.org/dev/peps/pep-0394/), the main file of a program should begin with `#!/usr/bin/env python3` (recommended, for virtual-environment compatibility), or use `#!/usr/bin/python3`.

Note: Python 2 officially reached end of life in January 2020 and should no longer use `#!/usr/bin/python2`.

Translator's note: In computer science, a [shebang](http://en.wikipedia.org/wiki/Shebang_(Unix)) (also called a hashbang) is a string made of a number sign and an exclamation mark (`#!`) and appears as the first two characters of the first line of a text file. When a file contains a shebang, a program loader on a Unix-like operating system parses the content after the shebang, treats it as an interpreter directive, invokes that interpreter, and passes the path of the file containing the shebang as an argument to that interpreter. For example, a file beginning with `#!/bin/sh` will actually invoke `/bin/sh` when executed.

The shebang is used first to help the kernel find the Python interpreter, but it is ignored when importing a module. Therefore, only files intended to be executed directly need it.

---

## Strings

1. Use [f-strings](https://docs.python.org/3/reference/lexical_analysis.html#f-strings), the `%` operator, or the `format` method for string formatting. You may use `+` for a one-time concatenation, but do not use `+` for formatting.

```python
# Correct
x = f'Name: {name}; Score: {n}'
x = '%s, %s!' % (imperative, expletive)
x = '{}, {}'.format(first, second)
x = 'Name: %s; Score: %d' % (name, n)
x = 'Name: %(name)s; Score: %(score)d' % {'name': name, 'score': n}
x = 'Name: {}; Score: {}'.format(name, n)
x = a + b

# Incorrect
x = first + ', ' + second
x = 'Name: ' + name + '; Score: ' + str(n)
```

2. Do not accumulate strings in a loop with `+` or `+=`. This can produce quadratic rather than linear time complexity. CPython may optimize some cases, but that is an implementation detail. We cannot easily predict when that optimization will apply, and it may change in the future.

```python
# Correct
items = ['<table>']
for last_name, first_name in employee_list:
    items.append('<tr><td>%s, %s</td></tr>' % (last_name, first_name))
items.append('</table>')
employee_table = ''.join(items)

# Incorrect
employee_table = '<table>'
for last_name, first_name in employee_list:
    employee_table += '<tr><td>%s, %s</td></tr>' % (last_name, first_name)
employee_table += '</table>'
```

3. Keep quote style consistent within the same file. Choose either `'` or `"` and do not change your mind. If using the other quote style avoids backslash escaping, that is acceptable.

```python
# Correct
Python('Why are you covering your eyes?')
# "I'm" contains a single quote, so the outer quotes may differ here
Gollum("I'm scared of lint errors.")
Narrator('"Very well!" thought a happy Python reviewer.')

# Incorrect
Python("Why are you covering your eyes?")
Gollum('The linter. It shines. It wants to blind us.')
Gollum("The great formatter endures forever. It watches. It watches.")
```

4. For multiline strings, prefer `"""` over `'''`. Only if the project uses `'` for ordinary strings may `'''` be used for multiline strings outside docstrings. In all cases, docstrings must use `"""`.

Multiline strings do not follow the indentation of the surrounding code. If you need to avoid extra spaces inside the string, concatenate multiple one-line strings or use [textwrap.dedent()](https://docs.python.org/3/library/textwrap.html#textwrap.dedent) to remove leading indentation from each line.

```python
# Incorrect
    long_string = """This looks awkward.
Do not do this.
"""

# Correct
long_string = """If you can tolerate extra spaces,
    you may do this."""

long_string = ("If you cannot tolerate extra spaces,\n" +
               "you may do this.")

long_string = ("If you cannot tolerate extra spaces,\n"
               "you may also do this.")

import textwrap

long_string = textwrap.dedent("""\
  This also works, because textwrap.dedent()
  removes the common leading spaces from each line.""")
```

Note that the backslash here does not violate the ban on explicit line continuation. In this case, the backslash is used to escape a newline inside a string literal.

5. For **logging**:

For logging functions whose first argument is a format string containing `%` placeholders, always use a string literal as the first argument, not an f-string, and pass placeholder values as the remaining arguments. Some logging implementations collect the unexpanded format string as a searchable item. This also avoids rendering messages that are configured not to be emitted.

```python
# Correct
import tensorflow as tf
logger = tf.get_logger()
logger.info('TensorFlow version: %s', tf.__version__)

import os
from absl import logging

logging.info('Current $PAGER: %s', os.getenv('PAGER', default=''))

homedir = os.getenv('HOME')
if homedir is None or not os.access(homedir, os.W_OK):
    logging.error('Cannot write to home directory, $HOME=%r', homedir)

# Incorrect
import os
from absl import logging

logging.info('Current $PAGER:')
logging.info(os.getenv('PAGER', default=''))

homedir = os.getenv('HOME')
if homedir is None or not os.access(homedir, os.W_OK):
    logging.error(f'Cannot write to home directory, $HOME={homedir!r}')
```

6. For **error messages**:

Error messages, such as the message strings of exceptions like `ValueError` and messages shown to users, should follow these three rules:
- The message must precisely match the real error condition.
- Inserted fragments must be clearly distinguishable.
- The message should be easy for simple automated processing, such as grepping.

```python
# Correct
if not 0 <= p <= 1:
    raise ValueError(f'This is not a probability value: {p!r}')

try:
    os.rmdir(workdir)
except OSError as error:
    logging.warning('Cannot remove this directory (reason: %r): %r',
                    error, workdir)

# Incorrect
if p < 0 or p > 1:  # Problem: this is also false for float("nan")!
    raise ValueError(f'This is not a probability value: {p!r}')

try:
    os.rmdir(workdir)
except OSError:
    # Problem: the message makes an incorrect guess.
    # The deletion may have failed for another reason, which would mislead debugging.
    logging.warning('The directory has already been removed: %s', workdir)

try:
    os.rmdir(workdir)
except OSError:
    # Problem: this message is hard to search, and some values of `workdir`
    # would be confusing. If someone called this code with workdir = 'removed',
    # the warning would become:
    # "Cannot remove removed directory."
    logging.warning('Cannot remove %s directory.', workdir)
```

---

## Files and Sockets

1. Explicitly close files and sockets after use. Naturally, this rule also extends to other closeable resources that internally use sockets, such as database connections, and to other resources that must be shut down similarly. Other examples include [mmap](https://docs.python.org/3/library/mmap.html) mappings, [h5py file objects](https://docs.h5py.org/en/stable/high/file.html), and [matplotlib.pyplot image windows](https://matplotlib.org/2.1.0/api/_as_gen/matplotlib.pyplot.close.html).

2. Keeping files, sockets, or other stateful objects open unnecessarily has many drawbacks:
   - They may consume limited system resources such as file descriptors. If code uses many such resources without returning them to the system promptly, avoidable resource exhaustion can occur.
   - Keeping files open can block other operations, such as moving or deleting files and unmounting file systems.
   - If multiple parts of a program share files or sockets, unexpected reads or writes may still occur even when the resource is logically considered closed. If the resource is truly closed, those operations will raise exceptions and expose the problem earlier.

3. In addition, even if files, sockets, and other similar resources are automatically closed during destruction, it is still improper to tie resource state to object lifetime:
   - There is no guarantee about when the runtime will actually call `__del__`. Different Python implementations use different memory-management techniques, such as delayed garbage collection, which may arbitrarily and indefinitely extend an object's lifetime.
   - Unexpected references to a file, such as global objects or exception tracebacks, may keep it alive longer than expected.

4. The [with statement](https://docs.python.org/3/reference/compound_stmts.html#the-with-statement) is recommended for managing files and similar resources:

```python
with open('hello.txt') as hello_file:
    for line in hello_file:
        print(line)
```

5. For file-like objects that do not support the `with` statement, use `contextlib.closing()`:

```python
import contextlib
import urllib.request

with contextlib.closing(urllib.request.urlopen('https://www.python.org/')) as front_page:
    for line in front_page:
        print(line)
```

---

## Getters and Setters

In particular, if reading or writing a variable is complex or expensive now, or is likely to become so in the foreseeable future, you should use this kind of function.

If a getter and setter pair is only used to read and write an internal attribute, you should replace them with a public attribute. By contrast, if a write operation invalidates part of the state or triggers rebuilding, then a setter is needed. An explicit function call indicates that special work may happen. If only simple logic is involved, or if getters and setters are no longer needed after a refactor, you may replace them with a `property`.

Getters and setters should follow naming conventions such as `get_foo()` and `set_foo()`.

---

## Naming

1. Function names, variable names, and file names should be descriptive and avoid abbreviations. In particular, avoid abbreviations that are ambiguous or unfamiliar to people outside the project, and do not abbreviate by dropping letters from words.

2. **Names to avoid**:
   - Single-character names, except in the following specifically approved cases:
     1. counters and iterators, such as `i`, `j`, and `k`
     2. `e` for an exception in a `try/except` statement
     3. `f` for a file handle in a `with` statement
   - package or module names containing hyphens (`-`)
   - names with both leading and trailing double underscores, such as `__double_leading_and_trailing_underscore__` (these names are reserved by Python)

3. **Naming conventions**:
   - The term "internal" means usable only within a module, or protected/private within a class.
   - To some extent, prefixing a module variable or function name with a single underscore (`_`) protects it. Linters will warn about access to protected members.
   - Prefixing an instance variable or method name with double underscores (`__`, also called dunder) effectively makes it private to the class through name mangling. We do not encourage this usage, because it seriously hurts readability and testability and does not provide **true** privacy. A single underscore is recommended.
   - Related classes and top-level functions should be placed in the same module. Unlike Java, there is no need to restrict a module to a single class.
   - Class names should use CapWords, but module names should use lowercase with underscores, such as `lower_with_under.py`. Some older modules use names such as `CapWords.py`, but this naming style is no longer encouraged because it becomes confusing when a module name and a class name are the same. For example: "Wait, did I just write `import StringIO` or `from StringIO import StringIO`?"
   - New **unit test** files should follow PEP 8 and use lowercase-with-underscores method names, such as `test_<method_under_test>_<state>`. Some older modules have method names in styles such as `CapWords`. To preserve style consistency, you may separate logical parts of the name with underscores after the word `test` and after the method name. One possible format is `test<method_under_test>_<state>`.

4. **Conventions based on Guido's recommendations**

Description

| Type | Public | Internal |
| --- | --- | --- |
| Package | lower_with_under | |
| Module | lower_with_under | _lower_with_under |
| Class | CapWords | _CapWords |
| Exception | CapWords | |
| Function | lower_with_under | _lower_with_under |
| Global constant / class constant | CAPS_WITH_UNDER | _CAPS_WITH_UNDER |
| Global variable / class variable | lower_with_under | _lower_with_under |
| Instance variable | lower_with_under | _lower_with_under (protected) |
| Method name | lower_with_under | _lower_with_under (protected) |
| Function parameter / method parameter | lower_with_under | |
| Local variable | lower_with_under | |

Examples

| Type | Public | Internal |
| --- | --- | --- |
| Package | lower_with_under | |
| Module | lower_with_under | _lower_with_under |
| Class | CapWords | _CapWords |
| Exception | CapWords | |
| Function | lower_with_under() | _lower_with_under() |
| Global constant / class constant | CAPS_WITH_UNDER | _CAPS_WITH_UNDER |
| Global variable / class variable | lower_with_under | _lower_with_under |
| Instance variable | lower_with_under | _lower_with_under |
| Method name | lower_with_under() | _lower_with_under() |
| Function parameter / method parameter | lower_with_under | |
| Local variable | lower_with_under | |

---

## Comments & Docstrings

This chapter mainly summarizes the format of DocStrings. In code that contains processing logic, the effective amount of source-code comments must be above 20%.

Python docstrings are used to document code. A docstring is a string that appears as the first statement in a package, module, class, or function. These strings can be extracted automatically through an object's `__doc__` attribute and are used by `pydoc`. Try running `pydoc` on your module and observe the result.

Docstrings must use triple double quotes `"""` (according to [PEP 257](https://www.python.org/dev/peps/pep-0257/)). A docstring should be a one-line summary, with that line kept within 80 characters and ending with a period, question mark, or exclamation mark. If more commentary is needed, which is recommended, the summary must be followed immediately by a blank line and then the remaining content, aligned with the first quote on the first line of the docstring. More formatting rules for docstrings are given below.

### Test Modules

Test files do not need a module-level docstring. Write one only when it can provide additional information. For example, you may describe special requirements for running the tests, explain uncommon initialization patterns, or describe external-environment dependencies.

For example:

```python
"""This blaze test uses golden files.

To update these files, run the following in the 'google3' directory:
`blaze run //foo/bar:foo_test -- --update_golden_files`
"""
```

Do not use a docstring that adds no extra information:

```python
"""Tests for foo.bar."""
```

### Functions

A function must have a docstring unless it satisfies all of the following:
- it is not externally visible
- it is very short
- it is obvious

A docstring should provide enough information that callers can use the function without reading its code. It should describe the calling syntax and semantic information of the function, rather than specific implementation details, unless those details affect how the function should be used. For example, if the function has the side effect of mutating an object that was passed in, that should be documented in the docstring. For subtle or important implementation details that do not concern callers, comments inside the code are better than placing them in the docstring.

A docstring may be written as a descriptive sentence (`"""Fetches rows from a Bigtable."""`) or as an imperative sentence (`"""Fetch rows from a Bigtable."""`), but the style within one file should be consistent. For data descriptors decorated with `@property`, the docstring should use the same style as an attribute or a [function parameter](https://zh-google-styleguide.readthedocs.io/en/latest/google-python-styleguide/python_style_rules.html#doc-function-args), such as `"""Bigtable path."""` rather than `"""Returns the Bigtable path."""`.

For a subclass method that overrides a base-class method, a simple docstring such as `"""See base class."""` may be used to guide readers to the base-class docstring. This avoids copying the same base-class docstring everywhere. However, if the overriding subclass method behaves differently from the base-class method or has additional details to document, such as extra side effects, then the subclass method's docstring should at least describe those differences.

Some function characteristics should be documented in the special sections listed below. Each section has a one-line heading ending with a colon. Aside from the heading line, the rest of the section should use a hanging indent of 2 or 4 spaces, consistently within the same file. If the function name and signature are self-explanatory enough that a one-line docstring is sufficient, these sections may be omitted.

Define the docstring on the first line of the function body with either triple single quotes `'''` or triple double quotes `"""`. You can access the docstring of a function through `__doc__` (note the double underscores). The docstring convention is: the first line briefly describes the function, the second line is blank, and the third line gives a more detailed description.

Example:

```python
def add(num1, num2):
  """Return the sum of the two input numbers.

  :param num1: first addend
  :param num2: second addend
  :return: sum
  """
  return num1 + num2


print(add.__doc__)
```

**Args:**

List all parameter names. After a parameter name, write a colon, then either a space or a newline, and then the description. If the description is so long that it exceeds 80 characters on one line, the description should use a hanging indent of 2 or 4 spaces more than the line containing the parameter name, consistently within the file. If the code has no type annotations, the description should state the required type. If a function has parameters such as `*foo` (variadic positional arguments) or `**bar` (arbitrary keyword arguments), list them as `*foo` and `**bar`.

**Returns:**

Generators should use `Yields:`.

Describe the type and meaning of the return value. If the function only returns `None`, this section may be omitted. If the docstring begins with Returns or Yields, for example `"""Returns rows from Bigtable as tuples of strings."""`, and that sentence is already sufficient to describe the return value, this section may also be omitted.

Do not imitate NumPy-style documentation when documenting tuple return values. In such docs, tuple returns are often written as if the function returned multiple named values rather than one tuple. Instead, describe them like this: "Returns: a tuple `(mat_a, mat_b)`, where `mat_a` is ..., and ...". The helper names used in the docstring do not need to match internal variable names in the function body, because those names are not part of the API.

**Raises:**

List all exceptions that are relevant to the interface, together with their descriptions. Use a format similar to the Args section: exception name, colon, space or newline, then a hanging indent. Do not document exceptions that are raised when callers violate the API's usage conditions, because doing so would make the effects of violating the API part of the API itself, which is contradictory.

Currently common docstring styles are:

1. reST style

This is currently a popular style and is the preferred format of **Sphinx**. It is relatively compact.

```python
"""
This is a reST style.

:param param1: this is a first param
:param param2: this is a second param
:returns: this is a description of what is returned
:raises KeyError: raises an exception
"""
```

2. Google style (**currently the main style in use**)

```python
This is a Google style doc.

Parameters:
 param1 - this is the first param
 param2 - this is the second param

Returns:
 This is a description of what is returned

Raises:
 KeyError - raises an exception
```

3. NumPy style

This is a format developed on top of the Google style.

```python
My numpydoc description of a kind
of very exhaustive numpydoc format docstring.

Parameters
----------
first : array_like
 the 1st param name 'first'
second :
 the 2nd param
third : {'value', 'other'}, optional
 the 3rd param, by default 'value'

Returns
-------
string
 a value in a string

Raises
------
KeyError
 when a key error
OtherError
 when another error
```

4. Epytext

Java style.

```python
"""
This is a javadoc style.

@param param1: this is a first param
@param param2: this is a second param
@returns: this is a description of what is returned
@raises KeyError: raises an exception
"""
```

Example:

```python
def fetch_smalltable_rows(
    table_handle: smalltable.Table,
    keys: Sequence[bytes | str],
    require_all_keys: bool = False,
) -> Mapping[bytes, tuple[str, ...]]:
    """Fetch rows from a Smalltable.

    Retrieve rows for the specified keys from the Table instance represented
    by table_handle. If a key is a string, it will be encoded as UTF-8.

    Args:
        table_handle: An open smalltable.Table instance.
        keys: A sequence of strings representing the row keys to fetch.
            Strings will be encoded as UTF-8.
        require_all_keys: If True, return only rows for which all keys have
            corresponding data.

    Returns:
        A dictionary mapping keys to row data. Row data is a tuple of strings.
        For example:

        {b'Serak': ('Rigel VII', 'Preparer'),
         b'Zim': ('Irk', 'Invader'),
         b'Lrrr': ('Omicron Persei 8', 'Emperor')}

        Returned keys are always byte strings. If a key from the `keys`
        argument is absent from the dictionary, that row was not found in the
        table, and `require_all_keys` must be false.

    Raises:
        IOError: An error occurred while accessing the smalltable.
    """
```

### Classes

A class definition should have a docstring below it describing the class. If your class has public attributes, they should be documented in an `Attributes` section, using a format similar to the `Args` section for functions.

```python
class SampleClass(object):
    """Summary of the class.

    More information here....
    More information here....

    Attributes:
        likes_spam: A boolean indicating whether we like spam for lunch.
        eggs: An integer recording how many eggs were laid.
    """

    def __init__(self, likes_spam=False):
        """Initialize SampleClass with something."""
        self.likes_spam = likes_spam
        self.eggs = 0

    def public_method(self):
        """Perform some operation."""
```

The class docstring should begin with a one-line summary describing what an instance of the class represents. This means that subclasses of `Exception` should describe what the exception means, rather than the condition under which it is raised. A class docstring should not contain meaningless repetition, such as saying that the class is a kind of class.

Correct:

```python
class CheeseShopAddress:
    """The address of a cheese shop.

    ...
    """

class OutOfCheeseError(Exception):
    """No cheese is available."""
```

Incorrect:

```python
class CheeseShopAddress:
    """A class that describes the address of a cheese shop.

    ...
    """

class OutOfCheeseError(Exception):
    """Raised when no cheese is available."""
```

Related link: [Python Language Rules](https://zh-google-styleguide.readthedocs.io/en/latest/google-python-styleguide/python_language_rules.html)

---

## Python 3.12 Feature and Style Supplements

### Relaxed f-string Restrictions (PEP 701)

Python 3.12 formally incorporates f-strings into the official grammar through PEP 701 and removes the following historical restrictions, making code more flexible and consistent:

**1. The same kind of quotes may be reused inside expressions** (`SyntaxError` in 3.11 and earlier)

```python
# Correct in Python 3.12+
name = "Alice"
message = f"{'Hello'}, {name}"

# Equivalent older style, still legal
message = f"{'Hello'}, {name}"
```

**2. Backslashes may be used inside expressions**

```python
# Correct in Python 3.12+
paths = ['/usr', '/bin']
result = f"Path list:\n{'\n'.join(paths)}"
```

**3. Multiline expressions and comments may appear inside expressions**

```python
# Correct in Python 3.12+
total = f"{
    sum(range(10))  # Compute the total from 0 to 9
}"
```

> Note: PEP 701 introduces no semantic changes. Existing f-string code remains fully backward compatible. In logging scenarios, `%` placeholders are still recommended instead of f-strings. See item 5 in the "Strings" section.

---

### Generic Type Parameter Syntax (PEP 695)

Python 3.12 provides more concise native syntax for generic functions, generic classes, and type aliases. `TypeVar` no longer needs to be declared manually.

```python
# Before Python 3.12
from typing import TypeVar
T = TypeVar("T")

def first(lst: list[T]) -> T:
    return lst[0]

class Stack(Generic[T]):
    def push(self, item: T) -> None: ...

# New syntax in Python 3.12+ (PEP 695), recommended
def first[T](lst: list[T]) -> T:
    return lst[0]

class Stack[T]:
    def push(self, item: T) -> None: ...

# Type aliases also have dedicated syntax
type Vector = list[float]
type Matrix[T] = list[list[T]]
```

---

### `@override` Decorator (PEP 698)

Python 3.12 adds the `@override` decorator to the `typing` module. It is used to explicitly mark that a subclass method overrides a parent-class method. Type checkers such as mypy and pyright can use this annotation to detect errors early when a base-class method signature changes.

```python
from typing import override

class Base:
    def process(self, data: str) -> None:
        ...

# Correct: explicitly marks the override relationship
class Child(Base):
    @override
    def process(self, data: str) -> None:
        super().process(data)
        # Extra processing logic

# Incorrect: the method name is misspelled, and a type checker can detect it
class BrokenChild(Base):
    @override
    def proccess(self, data: str) -> None:  # One extra 'c'; the type checker will report an error
        ...
```

For subclass methods that override base-class methods, it is recommended to keep a brief docstring and add `@override` at the same time, so that the design intent is clearly expressed. See the "Comments & Docstrings — Functions" section.
