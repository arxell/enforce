|  Branch	| Status  |
| :-------:	| :--- 	  |
|  Master: 	| [![Build Status](https://travis-ci.org/RussBaz/enforce.svg?branch=master)](https://travis-ci.org/RussBaz/enforce) [![Coverage Status](https://coveralls.io/repos/github/RussBaz/enforce/badge.svg?branch=master)](https://coveralls.io/github/RussBaz/enforce?branch=master)	|
|   Dev:  	| [![Build Status](https://travis-ci.org/RussBaz/enforce.svg?branch=dev)](https://travis-ci.org/RussBaz/enforce) [![Coverage Status](https://coveralls.io/repos/github/RussBaz/enforce/badge.svg?branch=dev)](https://coveralls.io/github/RussBaz/enforce?branch=dev)	|

#Enforce

Enforce is a simple Python 3.5 (or higher) application for enforcing a runtime
type checking based on type hints (PEP 484).

This project is not quite yet ready for production but we will be happy if you
give it a try.

* [Installation](#installation)
* [Usage](#usage)
* [Contributing](#contributing)
* [Enforce Wiki](https://github.com/RussBaz/enforce/wiki)

##Installation

Stable 0.2 - Already suitable for majority of applications.

    pip install enforce

Dev current - "Bleeding edge" features that, while are fairly consistent, may
change.

    pip install git+https://github.com/RussBaz/enforce.git@dev

## Usage

Type enforcement is done using decorators around functions that you desire to be
checked. By default, this decorator will ensure that any variables passed into
the function call match its declaration. This includes integers, strings, etc.
as well as lists, dictionaries, and more complex objects.

Note, this behavior means that for a large nested structure, every item in that
structure will be checked. This may be a nightmare for performance! See
[caveats](#caveats) for more details.

###Examples

* Basic example:
```python
import enforce

@enforce.runtime_validation
def foo(text: str) -> None:
    print(text)
```
* Decorator Configuration
```python
@runtime_validation(group='best group', enable=True)
def foo(a: List[str]):
    pass
```
* Group Tags
```python
enforce.config(enable=False)
enforce.set_group('foo', True)

@runtime_validation(group='foo')
def test1(a: typing.List[str]): return a

@runtime_validation(group='foo')
def test2(a: typing.List[str]): return a

@runtime_validation(group='bar')
def test3(a: typing.List[str]): return a

with self.assertRaises(RuntimeTypeError):
    test1(5)

with self.assertRaises(RuntimeTypeError):
    test2(5)

test3(5)

enforce.config(enable=True)
```

Also, you can deactivate at runtime all 'enforcers', each one of them
individually or as a group (using 'group' tags).

### Caveats

Enabling/Disabling type checking via group tags is done at initialisation, *not* at runtime. This is a known issue that is in the process of being
fixed. At a high level, this means that it's not quite supported to dynamically
disable checking at runtime, and this toggle feature works best manually for
now.

We are still working on deciding the best approach for iterables, please
reference [PR #15](https://github.com/RussBaz/enforce/pull/15) for context.

Currently the type checker will examine every object in a list. This means that
for large structures performance can be a nightmare. A known feature request
(that is in the works) is to set the recursion limit as well as a maximum size
limit, as to not have to check every item in some arbitrary object
(essentially trading performance for type safety). Another viable option is to check the
contents of lists lazily. This is a preferred method and would become a default one when implemented.

Generics and containers are NOT yet supported. Callables and TypeVars should be
generally supported (including covariacne and contravariance).

## Contributing

Please check out our active issues on our Github page to see what work needs to
be done, and feel free to create a new issue if you find a bug.

Actual development is done in the 'dev' branch, which is merged to master at
milestones.
