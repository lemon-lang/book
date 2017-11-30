% The Lemon Programming Language
% Zhicheng Wei
% 2017-11-08

## Part I - Basic Part

### Chapter 1 - Overview

#### 1.1 Get source code and build

Lemon using git management source code, you can learn about git from [git-scm.org](http://git-scm.org).

	$ git checkout git@github.com:lemon-lang/lemon.git

then inside lemon's source code folder run

	$ make

if build success, have `lemon` and `liblemon.so`, `lemon` is execute file, `liblemon.so` is required runtime lib.

Windows platform need [TDM-GCC](http://tdm-gcc.tdragon.net/download) tool.

	mingw32-make

#### 1.2 Build Options

In the build phase, have some options you can use.

* `DEBUG`, debug compiler flags, 0 is off.
* `STATIC`, 0 build with dynamic-linked library, 1 build with static-linked.
* `USE_MALLOC`, stdlib's malloc ensure return aligned pointer
* `MODULE_OS`, POSIX builtin os library
* `MODULE_SOCKET`, BSD Socket builtin library

default build also can use with options:

	$ make DEBUG=0 STATIC=0 USE_MALLOC=0 MODULE_OS=1 MODULE_SOCKET=1

The most important is STATIC options, if STATIC = 1, then build without liblemon.so instead liblemon.a, and the lemon execute file can't run lemon source that's import dynamic library from C.

#### 1.3 Run lemon

run lemon from source.

	$ ./lemon lemon.lm

or run lemon shell

	$ ./lemon
	Lemon Version 0.0.1
	Copyright 2017 Zhicheng Wei
	Type '\help' for more information, '\exit' or ^D exit

	>>> 

#### 1.4 Print stuff

print 'hello,world' from lemon shell, `print` can print any lemon object.

	>>> print('hello,world');
	hello,world
	>>> 
### Chapter 2 - Variables and Types

#### 2.1 Variable declare.

lemon is dynamic type language, declare a variable just use keyword `var`, example

```
var a;
```

you can set variable's value at declare, example

```
var a = 100;
```

you can declare multiple variable at once, example

```
var a, b, c;
```

multiple variable declare can't set value.

#### 2.2 Value Types

##### 2.2.1 integer

Integer is basic type of lemon, It's represent any integer from very little to very big. The whole integer range limit is depends on memory, example

	100;
	18446744073709551616;
	340282366920938463463374607431768211456;

all validate integer.

##### 2.2.2 number

Number is float pointer, lemon's implement number use double, example

	10.1;
	.5;

##### 2.2.3 string

String is array of byte, define string within `'` or `"`, example

	'hello,world'
	"hello,world"

String use escape to encode special char, example

* `\t` Horizontal Tab
* `\n` Line Feed
* `\r` Carriage Return

Methods of string object

* `upper()` return a copy of the string converted to uppercase. example, `'Hello,World'.upper()` -> `'HELLO,WORLD'`.
* `lower()` return a copy of the string converted to uppercase. `'Hello,World'.lower()` -> `'hello,world'`.
* `trim()` return a copy of the string with leading and trailing whitespace removed. example, `' Hello,World '.trim()` -> `Hello,World`.
* `ltrim()` return a copy of the string with leading whitespace removed. example, `' Hello,World '.ltrim()` -> `Hello,World `.
* `rtrim()` return a copy of the string with trailing whitespace removed. example, `' Hello,World '.trim()` -> ` Hello,World`.
* `find(substring)` return lowest index of substring in string. example, `'Hello,World'.find('l')` -> `2`.
* `rfind(substring)` return highest index of substring in string. example, `'Hello,World'.find('l')` -> `9`.
* `replace(old, new)` return a copy of string replaced every old in string to new. example, `'Hello,World'.replace('l', '1');` -> `'He11o,Wor1d'`.
* `split(delimiter)` return an array split by delimiter. example, `'Hello,World'.split(',')` -> `['Hello', 'World']`
* `join(iterable)` return a string which is the concatenation of the strings in the iterable, example `','.join(['Hello', 'World'])` -> `'Hello,World'`.
* `format()` return a string of replace placeholder `{}` to arguments, example `'Hello, {}'.format('World')` -> `'Hello, World'`.
* `startswith(prefix)` return true if prefix is string's prefix, otherwise return false, example `'Hello,World'.startswith('Hello')` -> `true`
* `endswith(suffix)` return true if suffix is string's suffix, otherwise return false, example `'Hello,World'.endswith('World')` -> `true`

##### 2.2.4 true false

boolean value `true` `false`, and other type can be `false`,example

	nil
	0
	''
	[]
	{}

###### 2.2.5 nil and sentinel

`nil` is a value represent for nothing, It's variable default value and function default return value.

`sentinel` is another value represent for nothing, but `sentinel` is mean's stop or non-exist value, like stop a iterator or delete a key from dictionary.

##### 2.3 Assign and Unpack

Variable use `=` assign a value, and lemon is a dynamic type language, you can assign any value to variable, example

	a = 10;
	a = 'hello';
	a = 0.0;

Lemon also support array unpack, you can assign multiple variable at once, example

	a, b, c = [10, 'hello', 0.0];

Variable `a` is `10`, variable b is `'hello'` and variable `c` is `0.0`.

If array unpack items short then variables, rest variable assign to `nil`, example

	a, b, c = [10, 'hello'];

Variable `a` is `10`, variable `b` is `'hello'` and variable `c` is `nil`;

If variables short then array unpack items, rest items will discard, example

	a, b, c = [10, 'hello', 0.0, 'world'];

Variable `a` is `10`, variable `b` is `'hello'` and variable `c` is `0.0`, `'world'` is discard.

### Chapter 3 - Collection Types

#### 3.1  Value type and Reference type

Last chapter we talk about lemon's basic type, like integer, number and string, those type are also **value type**. That mean's those type are readonly, they shouldn't have function update in place. And this chapter's we talk type is **reference type**, those type is also a container of other values, you can add or delete item from those types.

#### 3.2 Array

Array is a collection of elements, each element with a start with 0 index, represent element's position, example

	a = ['a', 'b', 'c', 'd'];

Variable `a` is an array with elements `'a'`, `'b'`, `'c'` and `'d'` in this specific ordered.

Access array's elements with index, example `a[0]` is 'a', `a[1]` is 'b'. or use negative index, then index of element is array's length + negative index, example `a[-1]` is 'd', `a[-2]` is 'c'.use `a[0] = 10` to set an item with index.

If an index large then or equal array's length, array will throw an exception, example

	<ItemError('['a', 'b', 'c', 'd']' index out of range)> 

Get bulk elements from array, example `a[0:2]` is `['a', 'b']`, get all elements index >= 0 and index < 2. Starts with 0, 0 can ignore.`a[0:2]` is same as `a[:2]`.

Methods of array

* `append(obj)` add obj to array's tail, example `a.append('e');` a become `['a', 'b', 'c', 'd', 'e']`.
* `pop()` return array last element and delete it from array, example `a.pop()`, return 'd' and a become `['a', 'b', 'c']` (a was `['a', 'b', 'c', 'd']`).
* `__length__()` function, get count of elements in array, example `a.__length__()` return `4` (a was `['a', 'b', 'c', 'd']`).

#### 3.3 Dictionary

Dictionary is a collection of elements, every element has a key, Items in dictionary is orderless, example

	a = {'a': 1, 'b': 2', 'c': 3, 'd': 4}

Get elements with key, example

	a['a'];

Set elements with key, example

	a['a'] = 10;

Delete elements from key, example

	delete a['a'];

Method of dictionary

* `keys()` return a array of all keys from dictionary, example `a.keys()` -> `['a', 'b', 'c', 'd']`.

### Chapter 4 - Operators and Expressions

#### 4.1 Arithmetic Operators

* `+` add operator, example `5 + 2` result is `7`
* `-` minus operator, example `5 - 2` result is `3`
* `*` times operator, example `5 * 2` result is `10`
* `/` divide operator, example `5 / 2` result is `2`
* `%` mod operator, example `5 % 2` result is `1`
* `<<` left shift operator, example `5 << 2` result is `20`
* `>>` right shift operator, example `5 >> 2` result is `1`
* `~` binary not operator, example `~5` result is `-6`
* `|` binary or operator, example `5 | 2` result is `7`
* `&` binary and operator, example `5 & 2` result is `0`

If both left and and right operand is integer then result is integer. if one operand is number other is integer then result will be a number.

Number is not support `<<`, `>>`, `~`, `|` and `&` operator.

String support `+` operator, example `'hello' + 'world'` result is `'helloworld'`.

#### 4.2 Relational and Logical Operators

* `<`  less then operator, example `1 < 2` result is `true`
* `<=` less then or equal operator, example `1 <= 2` result is `true`, `2 <= 2` result is `true`
* `==` equal operator, example `1 == 2` result is `false`, `2 == 2` result is `true`
* `>=` large then or equal operator, example `1 >= 2` result is `false`
* `>` large then operator, example `1 > 2` result is `false`
* `||` logical or operator, example `0 || 1` result is `1`
* `&&` logical and operator, example `1 && 0` result is `0`
* `!` logical not operator, example `!1` result is `false`.

`||`, `&&` and `!` is short-circuit evaluation, once operator has certain value, It stop evaluation rest expression, example `1 || func()` the func() will never execute.

All object support `==`,  `||`, `&&` and `!`. only integer and number support compare operator.

#### 4.3 Assignment Operators and Expressions

* `+=` add and assign, example `a += 1` result is `a = a + 1`
* `-=` minus and assign, example `a -= 1` result is `a = a - 1`
* `*=` times and assign, example `a *= 2` result is `a = a * 2`
* `/=` divide and assign, example `a /= 2` result is `a = a / 2`
* `%=` mod and assign, example `a %= 2` result is `a = a % 2`
* `<<=` left shift and assign, example `a <<= 2` result is `a = a << 2`
* `>>=` right shift and assign, example `a >>= 2` result is `a = a >> 2`

#### 4.4 Conditional Expressions

* `?:` conditional expression, example `a > b ? a : b`  if `a > b` result is `a`, otherwise result is `b`

#### 4.5 In Expressions

* `in` in expression, example `1 in [1,2,3,4]` result is `true`

### Chapter 5 - Control Flow

#### 5.1 if-else

Syntax

	if (condition) {
		block_stmt1
	} else {
		block_stmt2
	}

If `condition` expression is `true`, run `block_stmt1`, otherwise run `block_stmt2`. Else part is optional, example

	if (condition) {
		block_stmt1
	}

The `block_stmt1` only run when `condition` expression is `true`.

If-else can nested use, example

	if (condition1) {
		block_stmt1
	} else {
		if (condition2)  {
			block_stmt2
		} else {
			block_stmt3
		}
	}

When `condition1` is `true`, run block_stmt1, block_stmt2 and block_stmt3 not run. When `condition1` is `false` and `condition2` is `true`, block_stmt2 run. When both `condition1` and `condition2` is `false`, `block_stmt3` run.

#### 5.2 else-if

Nesting if-else-if-else have a short version, example

	if (condition1) {
		block_stmt1
	} else {
		if (condition2)  {
			block_stmt2
		} else {
			block_stmt3
		}
	}

Short version

	if (condition1) {
		block_stmt1
	} else if (condition2)  {
		block_stmt2
	} else {
		block_stmt3
	}

#### 5.3 while loop

Syntax

	while (condition) {
		block_stmt1
	}

When `condition` is `true`, the `block_stmt1` run repeatly until `condition` become `false`.

#### 5.4 for loop

Syntax

	for (init_expr; cond_expr; step_expr) {
		block_stmt1
	}

The for-loop will result follow steps,

1. Evalution `init_expr`
2. When `cond_expr` is `true`, run block_stmt1, else exit loop.
3. Evalution `step_expr` and goto step 2.

The `init_expr` support declare statement, and `step_expr` support assign statement, example


	for (var a = 0; a < 100; a += 1) {
		print(a);
	}

#### 5.5 for-in loop

Syntax

	for (variable in expr) {
		block_stmt1
	}

The `expr` must return an iterable object, the variable will assign iterable object's elements from first to last, and each time run `block_stmt1`.

Variable support declare, example

	for (var a in items) {
		print(a);
	}

#### 5.6 break and continue

Break statement will exit whole loop, example, 

	for (var i = 0; i < 10; i+=1) {
		if (i == 5) {
			break;
		}
		print(i);
	}

The loop will exit when `i == 5` is `true`.

Continue statement will exit current repeat,example

	for (var i = 0; i < 10; i+=1) {
		if (i == 5) {
			continue;
		}
		print(i);
	}

output

	1
	2
	3
	4
	6
	7
	8
	9

The continue skip print when `i == 5` is `true`.

### Chapter 6 - Function

#### 6.1 Define Function

Syntax

	def name(var a, var b = 0) {
		block_stmt
	}

Name is function's name, `block_stmt` is function body, `a` is required parameter and `b` is optional parameter.

Call a function, example

* `name(1)` only pass required parameter, 
* `name(1, 2)` pass two parameters, 
* `name(a = 1, b = 2)` pass parameter with name.

Define function with variable parameters, example

	def name(var a, var b, var *c) {
	}

Then call, example

	name(1,2,3,4,5,6,7,8,9);

In this function, parameter `a` is `1`, parameter `b` is `2`, and parameter `c` is `[3, 4, 5, 6, 7, 8, 9]`, all extra arguments will fill into `c` as an array.

Define function with variable name parameters, example

	def name(var a, var b, var **c) {
	}

Then call, example

	name(1, 2, foo=100, bar=200);

In this function, parameter `a` is `1`, parameter `b` is `2`, and parameter `c` is `{'bar': 200, 'foo': 100}`, all extra arguments will fill into `c` as an dictionary.

Function can have both variable parameters and variable name parameters, example

	def name(var a, var b, var *c, var **d) {
	}

Call function with variable arguments, example

	var a = [1,2];
	name(*a);

`*a` will expand to two arguments pass to function, like follow example

	name(1, 2);

Variable name arguments, example

	var a = {'a': 1, 'b': 2};
	name(**a);

`**a` will expand to two name arguments pass to function, like follow example

	name(a = 1, b = 2);

Function name is optional, example

	var f = def(var a) {print(a);};

Then you can use f call function.

Function is expression.can be a argument, example

	def hello(var f) {
		f(100);
	}

	hello(def(var a){print(a);});


#### 6.2 Local Variable and Variable Scope

Every declared variable in function only visible in function scope, example

	def func() {
		var a;
		var b;
		print(a, b);
	}

Variable `a` and variable `b` is the function local variable, they are only use in function scope (except closure).

Function can has block scope, example

	def func() {
		var a;
		{
			var a;
		}
	}

First `a` and second `a` is different variable, in second `a`'s block, will unable use first variable `a`.

#### 6.3 Nested Function and Closure

Function can defined with in a function, example

	def func1() {
		def func2() {
		}
	}

If return a function defined in this function, that make returned function to closure function, example

	def func1(var a) {
		def func2() {
			print(a);
		}
		return func2;
	}

When call `func1(100)`, return value is `func2` with `a = 100` binding, and even func1 is returned, the variable `a` will still exist, because the `func2` have reference of `a`, variable `a` will useable until `func2` is destroyed.

If call `func1` mutliple times, the `func1` will return different `func2` with different value binding, this is closure.

### Chapter 7 - Class

#### 7.1 Define Class

Syntax

	class Greeting {
		var welcome = 'hello';

		def greeting(var name) {
			print(self.welcome, name);
		}
	}

Define class without super class, lemon support multiple inherent, example


	class Greeting(Hello, World) {
	}

Class Greeting inherent from Hello and World.

`var welcome` in class is class `Greeting` attribute, function `greeting` is class `Greeting` method. The class's method can call from class (can't use self) or class's instance (can use self).

Instance a class, example

	var greeting = Greeting();

`__init__` function, example

	class Greeting {
		def __init__(var name) {
			self.name = name;
		}
	}

`__init__` will call after the instance create, arguments is passing to from class call, example

	var greeting = Greeting('Zhicheng');

#### 7.2 Class attribute and Instance Attribute

There are two different attribute the instance can have, class's attribute and instance attribute, example

	class Greeting {
		var name = 'Stranger'; // class attribute
		def greeting(var name) {
			self.name = name; // instance attribute
		}
	}

Instance's attribute is set in self or instance variable, the class define attribute is always class's attribute, example

	var greeting1 = Greeting();
	print(greeting1.name); // class's attribute
	greeting1.greeting('Zhicheng');
	print(greeting1.name); // instance attribute, set by self.name = name

	var greeting2 = Greeting();
	print(greeting2.name); // still class's attribute
	greeting2.name = 'Zhicheng';
	print(greeting2.name); // instance's attribute

#### 7.3 Inheritance and super class

When class define a method have a same name with it's super class, that's will overwrite super class's method, but you can use super call super class methods, example

	class A {
		def hi() {
			print("hi, I'm A");
		}
	}

	class B(A) {
		def hi() {
			super.hi();  // call A's hi function
			print("hi, I'm B");
		}
	}

In multiple inherent class, can specific a super class function, example

	class A {
		def hi() {
			print("hi, I'm A");
		}
	}

	class B {
		def hi() {
			print("hi, I'm B");
		}
	}

	class C(A, B) {
		def hi() {
			super(B).hi();  // call B's hi function
			print("Hi, I'm C");
		}
	}

#### 7.4 self and super object

In method  `self` is reference instance of  class. super can get super class's method, in lemon function can dynamic assign, but the self's binding will not change, example

	class A {
		def hi() {
			print("hi, I'm A");
		}
	}
	class B {
		def hi() {
			print("hi, I'm B");
		}
	}
	var a = A();
	var b = B();

	a.hi = b.hi;
	a.hi();  // call B's hi();

And beware of reference type in class, this may cause some confuse, example

	class A {
		var names = [];
	}

	var a1 = A();
	a1.names.append('Zhicheng');

	var a2 = A();
	a2.names.append('John');
	print(a1.names);
	print(a2.names);

Both `a1.names` and `a2.names` is `['Zhicheng', 'John']`, because names is class A's attribute. set instance's attribute in `__init__` .

### Chapter 8 - Exception

#### 8.1 throw

Syntax

	throw expression;

Expression's result need be a Exception object or instance of Exception's subclass, example

	class JustException(Exception) {
	}

#### 8.2  try-catch

Syntax

	try {
		stmts1;
	} catch (ExceptionType e) {
		stmts2
	} catch (Exception e) {
		stmts4;
	} finally {
		stmts5;
	}

The finally `stmts5` always run, after `stms1` or any of catch catched.the catch can only catch instance of `ExceptionType` or instance of `ExceptionType` subclass, the var `e` is instance of `Exception` throwed by `stmts1`. `catch (Exception e)` can catch all exceptions, otherwise try-catch will throw upper level try-catch.

#### 8.2 Exception objects

* `TypeError` general type's error.
* `ItemError` any kind of use index or name access get items
* `MemoryError` memory relative error.
* `RuntimeError` virtual machine relative error.
* `ArgumentError`  arguments of function.
* `AttributeError` get/set attribute relative error.
* `ArithmeticError` arithmetic relative error.
* `NotCallableError` called an not callable object.
* `NotIterableError` iter a not iterable object.
* `NotImplementedError` not implemented function or method.

### Chapter 9 - Import

#### 9.1 import

Syntax

	import 'hello.lm';
	import './hello.lm';
	import '../hello.lm';
	import '/Users/zhicheng/hello.lm';

Lemon have four path resolve,

* bare file, first search current working directory, if not found, search from LEMON_PATH.
* current file directory, `./hello.lm` search `hello.lm` in current file directory.
* current file parent directory, `../hello.lm` search `hello.lm` in current file parent directory.
* absolute path, import module from absolute path.


#### 9.2 Name Resolve

`import` support `as` to rename module, example

	import 'hello.lm' as hello;

If no `as` in `import`, lemon will auto resolve module name, module's file name will become module name, and change every unsupport character to `_` to emit a legal module name, example

	import 'hello-world.lm';

Module `hello-world.lm` will named hello_world.

## Part II - Advance Part

### Chapter 10 - Accessor

#### 10.1 setter and getter

Syntax:

	def foo_getter(var x) {
		return x + 1;
	}

	def foo_setter(var x) {
		return x - 1;
	}

	@getter(foo_getter)
	@setter(foo_setter)
	var foo = 0;

Anytime get variable `foo`'s value, will call `foo_getter` and pass `foo`'s original value to parameter `x`, `foo_getter` return a new value, that will be variable `foo`'s expression value.

Anytime(except in variable declare statement) assign `foo`'s value, will call `foo_setter` and pass new value to parameter `x`, `foo_setter` return a new value, that will be `foo`'s final value.

You can set multiple getter and setter, they'll execute from bottom-up. first getter get variable's original value, after getter will  get previous getter's return value.

The keyword `getter` is optional, example

	def foo_getter(var x) {
		return x;
	}

	@foo_getter
	var foo;

#### 10.2 basic usage

Examples

	// have log of every time use variable
	def foo_logger(var x) {
		// logging about x use
		var x;
	}

	// make a variable readonly in getter way
	def readonly(var x) {
		return x;
	}

	// make a variable readonly in setter way
	def readonly_setter(var x) {
		throw TypeError('trying set readonly object');
	}


#### 10.3 advance usage

Example of use accessor as a type checker.

	def typecheck(var type) {
		return function(var x) {
			if (!x.__instanceof__(type)) {
				throw TypeError('wrong type');
			}
			return x;
		};
	}

	@setter(typecheck(integer))
	var a = 10;

	a = 20; // ok

	a = 'hello'; // not ok

### Chapter 11 - Iterator

#### 11.1 Iterator object and iterable object

In `for-in` loop, not only collection type, any iterator object or iterable object can use as right value, example

	class Range {
		def __init__(var max) {
			self.max = max;
		}

		def __iterator__() {
			self.value = 0;
			return self;
		}

		def __next__() {
			if (self.value == self.max) {
				return sentinel;
			}
			var value = self.value;
			self.value += 1;
			return value;
		}
	}

	for (var a in Range(11)) {
		print(a);
	}
output

	0
	1
	2
	3
	4
	5
	6
	7
	8
	9

First call `__iterator__` get iterable object, then call `__next__` and every iter, until `__next__` return `sentinel` stop iterator.

Iterator object is used by builtin object like array and dictionary.

### Chapter 12 - Coroutine

#### 12.1 yield function.

`yield` function can dynamic make a function become a coroutine, example

	def hi() {
		yield(1);
		yield(2);
	}

	var co = hi();
	print(co.current());
	co.resume();
	print(co.current());

output

	1
	2

#### 12.2 resume and transfer

`resume` and `transfer` both can continue run coroutine, but `resume` will returned to caller, `transfer` will transfer current coroutine's execute context to another coroutine, so `transfer` can only call with in coroutine, transfer example [Coroutine - Wikipedia](https://en.wikipedia.org/wiki/Coroutine)

	var q = array();

	var p;
	var c;
	var n = 0;
	def produce() {
		yield(0);
		while (true) {
			print("produce");
			q.append(n);
			c.transfer();
		}
	}
	def consume() {
		yield(0);
		while (true) {
			print("consume");
			var a = q.pop();
			p.transfer();
		}
	}
	p = produce();
	c = consume();
	p.resume();

### Chapter 13 - Continuation

#### 13.1 callcc function.

`callcc` is call-with-current-continuation [https://en.wikipedia.org/wiki/Call-with-current-continuation](https://en.wikipedia.org/wiki/Call-with-current-continuation)

`callcc` take a function argument and call it with a continuation argument, the continuation can restart function with `callcc`'s position, example

	var cont;

	def f() {
		print(1);
		callcc(function(var cc) { cont = cc; }));
		print(2);
	}

	f(); // output 1 , 2
	cont(); // output 2

#### 13.2 Demo

Demo from wikipedia. [Continuation - Wikipedia](https://en.wikipedia.org/wiki/Continuation)

	var the_continuation;

	def test() {
		var i = 0;
		callcc(function(var k){the_continuation = k;});
		i += 1;
		return i;
	}
	print(test());

	print(the_continuation());
	print(the_continuation());

	var another_continuation = the_continuation;

	print(test());
	print(the_continuation());
	print(another_continuation());

## Part III - The C API

### Chapter 14 - Input and Compile

#### 14.1 initialize and set parameter

Example from `main.c`

	struct lemon *lemon;
	lemon = lemon_create();
	builtin_init(lemon);

	lobject_set_item(lemon, 
			 lemon->l_modules,
			 lstring_create(lemon, "os", 2),
			 os_module(lemon));
	lemon_input_set_file(lemon, "code.lm");
	lemon_compile(lemon);
	lemon_machine_reset(lemon);
	lemon_machine_execute(lemon);

For simple reason I don't add error check, check `main.c` for error check version.

Everything start with `lemon_create()`, this function initialize all party of lemon, and required object.  after `lemon_create` initialize module with `builtin_init`, and manual initialize `os_module`, there is the place you add custom module.  use `lemon_input_set_file` set lemon source code file or `lemon_input_set_buffer` set source code from string instead file.  use `lemon_compile` compile source code, use `lemon_machine_reset` reset machine and finally `lemon_machine_execute` execute code. all part is done.

### Chapter 15 - The Virtual Machine

#### 15.1 execute and execute_loop

There are two execute function in lemon virtual machine, `main.c` use `lemon_machine_execute` and `shell.c` use `lemon_machine_execute_loop`, the different between is the `lemon_machine_execute` will clean up virtual machine's stack and frame. `lemon_machine_execute_loop` only execute code, the state of virtual machine will keep at code finished state.

#### 15.2 frame object

`frame` is most important object in lemon. It's record the current call context, local variables. There are two type of `frame`

1. lemon object frame
2. C function frame

C function used by C callable object, because the frame will not returned by `return` opcode, so sometime need manual call `lemon_machine_return_frame`.

and frame also can use to capture values, example

	struct lobject *
	callback(struct lemon *lemon,
		 struct lframe *frame,
		 struct lobject *retval)
	{
		/* use of previous function's return value */	
		return retval;
	}

	frame = lemon_machine_push_new_frame(lemon, 
		NULL, NULL, callback, 0);
	/* call object */
	retval = lobject_call(lemon, callable, argc, argv);
	/*
	 * call lemon_machine_return_frame if needed, retval is returned from call object
	 */
	// lemon_machine_return_frame(lemon, retval)

#### 15.3 pause frame

There's also a special frame, `pause frame`,  `lemon_machine_execute_loop`  will return if meet `pause frame`, example

	struct lframe *pause;
	pause = lemon_machine_add_pause(lemon);

	lobject_call(lemon, callable, argc, argv);
	retval = lemon_machine_execute_loop(lemon);
	lemon_machine_del_pause(lemon, pause);

This is design for C function call lemon function and need return value from function. if return value is not urgant, regular frame can do the trick.

### Chapter 16 - Create Function and Type

#### 16.1 C function demo.

Create a C function in lemon only need one c function, example

	static struct lobject *
	hello(struct lemon *lemon,
	      struct lobject *self,
	      int argc, struct lobject *argv[])
	{
		printf("hello,world\n");
		return lemon->l_nil; // this is required
	}

And add function to lemon before compile.

	const char *cstr = "hello";
	struct lobject *name = lstring_create(lemon, cstr, strlen(cstr));
	struct lobject *function = lfunction_create(lemon, name, NULL, hello);
	lemon_add_global(lemon, cstr, function);

#### 16.2 object demo

Create a new object required a structure and a function, example

	struct an_object {
		struct lobject object;
	};

	static struct lobject *
	an_object_method(struct lemon *lemon,
			 struct lobject *self,
			 int method, int argc,
			 struct lobject *argv[])
	{
		switch (method) {
		case LOBJECT_METHOD_STRING:
			return lstring_create(lemon, "an_object", 8);

		default:
			return lobject_default(lemon, self, method, argc, argv);
		}
	}

Create object use `lobject_create` ,example

	struct lobject *object = lobject_create(lemon,
		sizeof(struct an_object), an_object_method);

All `LOBJECT_METHOD_XXX` defined in `lobject.h`.  More example please check lemon's source code.


This work is licensed under a [Creative Commons Attribution 4.0 International License](http://creativecommons.org/licenses/by/4.0/).
