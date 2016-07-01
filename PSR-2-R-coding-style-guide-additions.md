# Coding Style Guide Additions
#Базовый стандарт - на английском языке, дополнения - на русском.

Стандарт предлагается для написания нового кода. Стиль имеющегося кода в случае несоответствия менять без необходимости не рекомендуется.

## Properties and variables.

* Все свойства и переменные независимо от типов используют $camelCase для названий.
`$snake_case` запрещен. Это же правило касается протоколов клиент-сервер:

```json
//bad
{
	"is_main": 1
}

//good
{
	"isMain": 1
}
```

В полях БД используется только `snake_case`. В параметрах подстановки шаблонов и строковых литералах допустимы обе нотации.

* Модификаторов видимости private рекомендуется избегать.


```php
class Foo {

	public $requestHandler;

	protected $isBool;

	public function foo($countVar, myObject $myObject) {
		$countVar++;
		$myObject->bar();
	}

}
```

## Structure
- Use parentheses when instantiating classes regardless of the number of arguments the constructor has.
- Declare class properties before methods.
- Declare public methods first, then protected ones and finally private ones.
The exceptions (when using PHPUnit) to this rule are the class constructor and the `setUp` and `tearDown` methods of PHPUnit tests,
which should always be the first methods to increase readability.

##Namespaces

Неймспейс должен содержать любой файл, содержащий объявление класса, а также скрипт.

При импорте классов мы придерживаемся двух рекомендаций: 
* Не импортируем мого классов из одного неймспейса, предпочитая импортировать базовый

```php
//Bad
use Karaoke\Entity\Song;
use Karaoke\Entity\Content;
use Karaoke\Entity\Foo;

echo Song::class . Content::class . Foo::class;

//Good
use Karaoke\Entity;

echo Entity\Song::class . Entity\Content::class . Entity\Foo::class

```

* Используемое в коде имя класса должно быть однозначным и определенным, оно не должно требовать перехода к объявлению, чтобы понять, что это за сущность

```php
//Bad
use Karaoke\Entity\Song;
use Karaoke\Exception\Logic;

$song = new Song();
throw new Logic;

<...>

use Karaoke\View\Song;

$song = new Song();

//Good
use Karaoke\Entity;
use Karaoke\Exception;

$song = new Entity\Song();
throw new Exception\Logic;

<...>

use Karaoke\View;

$song = new View\Song();

```

## Traits

Трейты использовать запрещено.

##Reflections
Использовать reflection api запрещено.

##get_class
Избегать использования get_class.
Не использовать конструкции типа new $class не в сущностях, предназначенных для порождения объектов. 

## Ternary Operator
Ternary operators should not ever be nested.
Optionally parentheses can be used around the condition check of the ternary for clarity:

```php
// Nested ternaries are bad
$variable = isset($options['variable']) ? isset($options['othervar']) ? true : false : false;

// Good, simple and readable
$variable = isset($options['variable']) ? $options['variable'] : true;
```

Тернарные операторы допускается разбивать на три строки, но только в случае, когда это повышает читабельность кода: 

```php
$variable = (isset($options['variable']) && $iLikeTernaryAndDonnaWannaUseIfElse)
    ? $options['variable'] 
    : true;
```

## Control Structures
Do not use keyword control structures.

```php
// Bad
if ($isAdmin):
	echo '<p>You are the admin user.</p>';
endif;

// Good
if ($isAdmin) {
	echo '<p>You are the admin user.</p>';
}

```

## PHP Open Tags
Always use `<?php`.

## Comparison

Always try to be as strict as possible. If a non-strict test is deliberate it might be
wise to comment it as such to avoid confusing it for a mistake.

For testing if a variable is null, it is recommended to use a strict check:
```php
// Faster and easier than is_null() call
if ($value === null) {
      // ...
}
```

The value to check against should be placed on the right side:
```php
// Yoda style not recommended
if (null === $this->foo()) {
    // ...
}

// Recommended for better (human) readability
if ($this->foo() === null) {
    // ...
}
```

### Comparison methods
Consistenty is key here. The project should use one througout the code. In general stick to the short version.

`is_int()` should be used instead of `is_integer()`.
Use `is_writable()` instead of `is_writeable()`.

### Avoid conditional assigment
Especially with not using Yoda conditions one should never use single `=` inside conditions.
So avoid conditional assigments:
```php
// Conditional assigment not recommended
if (($variable = $this->foo()) === null) {
    // ...
}

// Recommended
$variable = $this->foo();
if ($variable === null) {
    // ...
}
```

## Whitespace

* Use one newline at the end of the file. This avoids "missing newline" issues in several git hosting
environments

```php
class Foo extends Bar implements FooInterface {

	public function foo($a, $b = null) {
		// Code here
	}

	public function bar() {
		// Method body
	}

}

```

### Inter-Line Alignment
Don't use inter-line alignment. It is

a) Not useful in combination with tabs as indentation (and personal length adjustment of it).
b) Very bad for change diffs, as it creates a lot of noise. It is also additional work.

```php
public function bar() {
	$foo = 'bar';
	$bazdib = 'gir';
	$zim = 'irk';

	$varname = '1234' . aVeryLongFunctionName()
		. 'foo' . otherFunction();
}
```
The aligment would have to completely change for the above three variables, if one of them
would be named longer, renaming all involved and aligned variables with it. Bad idea.

The second example would result in the same issue. So always use simple spaces for inline and
one-tab indents for multiline aligments.

### Strings and Concatination

`'` or `"`? Both work, as long as they are used consistent throughout a file.

In case a string contains `'`, it is applicable to switch to `"` here to avoid
the usage of `\` escapes:
```php
$sql = "UPDATE TABLE 'foo' SET ContactName='Alfred Schmidt', City='Hamburg' WHERE ...";
```

Use a space before and after `.`:
```php
$myString = 'a string' . $variable . 'more string stuff etc';
```

## Multi-line declaration/condition/concatenation
All operators should go in the newline as first character:
```php
$foo = 'Some String'
	. ' concatinated';
```

Multi-line control structures should have the parentheses in their own lines (similar to classes and methods):
```php
if (
	($a == $b)
	&& ($b == $c)
	|| (Foo::CONST == $d)
) {
	$a = $d;
}
```

### Careful with deep arrays
One mistaked that gets often made:
```php
$foo = [[
	0,
	1, 2
], 3, 4];
```
This would effectivly change all lines (and their indentation), if the array structure got normalized.
Arrays also need to minimize effects on the resulting diff, and as such indentation must always be the right one:
```php
$foo = [[
		0,
		1, 2
	], 3, 4];
```
for example, max. normalized as:
```php
$foo = [
	[
		0,
		1,
		2
	],
	3,
	4
];
```
As you can see, entries like `0` would not need any change on reorganizing, thus reducing overhead in work and making diffs easier to read as
they only show actual changes made.

## Typehinting
Arguments that expect objects, arrays or callbacks (callable) can be typehinted. 

При возможности используем тайпхинтинг на интерфейсы (`UserInterface`) вместо классов объектов (`User`). Разумеется, это не касается случая, когда явно используется метод объекта, но не интерфейса.

```php
/**
 * Some method description.
 *
 * @param ModelInterface $Model The model to use.
 * @param array $array Some array value.
 * @param callable $callback Some callback.
 * @param bool $boolean Some boolean value.
 */
public function foo(ModelInterface $Model, array $array, callable $callback, $boolean) {
}
```
Here $Model must be an instance of ModelInterface, $array must be an array and $callback must be of type callable (a valid callback).

Note that if you want to allow $array to be also an instance of ArrayObject you should not typehint as array accepts only the primitive type:
```php
/**
 * Some method description.
 *
 * @param array|\ArrayObject $array Some array value.
 */
public function foo($array) {
}
```

## Method Chaining
Method chaining should have multiple methods spread across separate lines, and indented with one ident.


```php
$email->from('foo@example.com')
    ->to('bar@example.com')
    ->subject('A great message')
    ->send();
```

## Casting

Для явного преобразования типо допустимы как конструкции типа `(int)`, так и функции типа `intval()`

## Commenting Code
All comments should be written in English, and should in a clear way
describe the commented block of code.

Inline code should use `//` and a single space afterwards.
Use sentences with a capital first letter and a full stop if possible:
```php
// This is a nice inline comment.
$this->doSomehing();
```

Comment blocks, with the exception of the first block in a file, should always be preceded by a newline.

### Tags
Required tags for each function/method are:

* `@param` if applicable
* `@return`
* `@throws` if applicable
* `@triggers` if applicable

in this order.

Constructors/destructors may not have a `@return` statement,
as they by definition should not return anything.

Additionally these may be useful:

* `@internal` if applicable
* `@see` or `@link`
* `@deprecated` if applicable

For PHPUnit:

* `@covers` if applicable
* `@expectedException` if applicable

For additional tags see [phpDocumentator](http://phpdoc.org/).

The `@package` and `@subpackage` annotations are not used.

### Variable types
Variable types for use in DocBlocks:

* `mixed` - A variable with undefined (or multiple) type. Shall be avoided where possible.
* `int` - Integer type variable (whole number).
* `float` - Float/Double/Real type (point number).
* `bool` - Logical type (true or false).
* `string` - String type (any value in `""` or `''`).
* `null` - Null type. Usually used in conjunction with another type.
* `array` - Array type.
* `object` - Object type. A specific class name should be used if possible.
* `resource` - Resource type (returned by for example mysql_connect()). Remember that when you specify the type as mixed, you should indicate whether it is unknown, or what the possible types are.
* `callable` - Callable function.

You can also combine types using the pipe char:
```
int|bool
```
When combining types it is recommended to order them by the primary expectation in descending order.

For more than two types it is usually best to just use `mixed`.

In the description itself the verbose versions of `int` and `bool` are used.
Use sentences with a capital first letter and a full stop if possible:
```php
/**
 * Returns output of input.
 *
 * @param int|bool $input Input as integer or boolean value.
 * @return int|bool Output pretty much the same.
 */
public function foo($input) {
    return $input;
}
```

When returning the object itself, e.g. for chaining, one should use `$this` instead:
```php
/**
 * Foo function.
 *
 * @return $this
 */
public function foo() {
    return $this;
}
```
This is especially important for IDEs, as they otherwise do not support chaining.

## Naming Conventions
- Use namespaces for all classes.
- Suffix interfaces with `Interface`.
- Suffix traits with `Trait`.

## Writing better code

* Try to accept and return as few types as possible (mixing integer, boolean, string, array, object etc is usually not too good)
* Try to return early in methods/functions to avoid unnecessary depths

### Example for return early
```php
// Bad
public function foo($input, $anotherInput = null) {
	if ($input) {
		if ($anotherInput) {
			if ($anotherInput === $input) {
				// Code
				return true;
			}
		}
	}
	return false;
}

// Good
public function foo($input, $anotherInput = null) {
	if (!$input || !$anotherInput || $anotherInput !== $input) {
		return false;
	}
	// Code
	return true;
}

```

### Avoid no-op methods
```php
// Bad
public function foo($input = null) {
	if ($input === null) {
		return;
	}
	...
}

// Good
public function foo($input) {
	if ($input === null) {
		return;
	}
	...
}

```
The first would allow no-ops like `$this->foo()` which does not do any operation at all.
So semantically this makes no sense. In this case no default value may be used and a first argument is actually required
for the first if statement to make sense (`$this->foo($requiredArgument)`). You can still pass null, of course, to break out early.
Default values may only be used if their usage does make this method still do an operation (apart from returning early).

### Avoid `private` for class methods/properties
Most of the time `private` is used too eagerly, where `protected` would suffice.
Allow extending classes to extend the code. Don't assume it doesn't have to.
This is especially important for frameworks or vendor libraries that people would like to enhance or
customize in their applications.

Read some more about it [here](http://aperiplus.sourceforge.net/visibility.php).

In case you are acquainted with the "Open/Close Principle", it is in some cases OK to use
private to define clear public interfaces for classes.

### Underscores for Private/Protected

Начинать названия методов с _ запрещено.


### Return void vs null
`@return void` shall be used to document when a method is expected not to return anything, and when there is just a `return;` as "early return". Explicitly returning with `return null;` or `return $this->foo();` shall be documented be as `@return null` etc.

```php
/**
 * @param string $input
 * @return void
 */
public function setFoo($input) {
	if (!$input) {
		return;
	}
	if ($input === 'foofoo') {
		$this->set('special', $input);
		return;
	}
	$this->set('input', $input);
}

/**
 * @param string $var
 * @return string|null
 */
public function get($var) {
	if (!isset($this->config[$var]) {
		return null;
	}
	return $this->config[$var];
}
```
Mixing void and other types must not be used, as a method cannot be expected to return and not return at the same time. Use `null` here instead: `@return \MyObject|null`.

## Example Addresses
For all example URL and mail addresses use `example.com`, `example.org` and `example.net`, for example:

* Email: `someone@example.com`
* WWW: `http://www.example.com`
* FTP: `ftp://ftp.example.com`

The `example.com` domain name has been reserved for this (see [RFC 2606](http://tools.ietf.org/html/rfc2606.html)) and is recommended
for use in documentation or as examples.

## Line Length (Relaxed addition to original recommendation)

Крайне желательно не выходить за рамки 120 символов на строку.

## Other

## Further considerations
While so far the main focus was on the developer (readability), there are some additional optional guidelines that can help to further reduce diff size on code modification (maintainability).
Those can and should be automated, though, as there is no point in forcing the developer to take care of those manually.

The main idea is to keep each line independant from the others so removing or adding lines has minimal impact.

### Multi-line arrays
Arrays that span across multiple lines can have a trailing comma to make sure that adding new rows does not change the previous row, as well.
```php
$array = [
	'first',
	'second', // Note the trailing comma
];
```

### Multi-line logic

точки с запятой на новую строку не переносим.

```php
$object
	->doFirst()
	->doSecond();
```

###Naming

* Название таблиц в БД в единственном числе: "user", "song", "foo"
* 
