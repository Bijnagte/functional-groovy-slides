## Functional Programming?
* Referential Transparency
* Side effect free
* Declarative vs Imperative
* First class / higher order functions
* Concurrency friendly
* State free
---
## A Definition
Wikipedia:
> a style of building the structure and elements of computer programs, that treats computation as the evaluation of mathematical functions and avoids state and mutable data. Functional programming emphasizes functions that produce results that depend only on their inputs and not on the program state - i.e. pure mathematical functions. 
---
## Sample code
```
> git clone https://github.com/Bijnagte/functional-groovy-code.git
> cd functional-groovy-code/src/main/groovy
> groovysh
```
---
## Immutability

* consistent inputs
* safe sharing
* values not places
* cacheable

---
## @Immutable
Immutability the groovy way

``` groovy
import groovy.transform.Immutable

@Immutable
class ImmutableType {
	int integer
	String string
	List list
}
```
---
## Recursion
Minimizing moving parts by eliminating mutation
``` groovy
sizeOfList = { list, counter = 0 ->
    if (list.size() == 0) {
        counter
    } else {
        sizeOfList(list.tail(), counter + 1)
    }
}

sizeOfList(1..100)

=> 100

sizeOfList(1..10000)

=> java.lang.StackOverflowError
```
---
## Trampoline
Tail call optimization
``` groovy
sizeOfList = { list, counter = 0 ->
	if (list.size() == 0) {
		counter
	} else {
		sizeOfList.trampoline(list.tail(), counter + 1)
	}
}.trampoline()

sizeOfList(1..10000)

=> 10000

```
---
## @TailRecursive
_Groovy 2.3 + only_
``` groovy
import groovy.transform.TailRecursive

@TailRecursive
long sizeOfList(list, counter = 0) {
	if (list.size() == 0) {
		counter
	} else {
		sizeOfList(list.tail(), counter + 1)
	}
}
sizeOfList(1..10000)

=> 10000
```
---
## Closures as first class functions
Closures can be...

assigned to variables
``` groovy
def greet = { greeting, recipient ->  "$greeting $recipient" }
greet('hello', 'world')

=> 'hello world'
```
used in maps
``` groovy
thing = [func: {-> 'called' } ]
thing.func()

=> 'called'
```
passed as arguments
``` groovy
capitalize = { string -> string.toUpperCase() }
callWithString = { string, function -> function(string) }
callWithString('hello', capitalize)

=> 'HELLO'

callWithString('hello') { it.reverse() }

=> 'olleh'
```
---
## Methods vs functions
Methods are functions with a hidden first argument 'this'

``` groovy
@Immutable
class Point {
    int x
    int y
    Point plus(Point other) {
        new Point(this.x + other.x, this.y + other.y)
    }
}

static Point plus(Point that, Point other) {
    new Point(that.x + other.x, that.y + other.y)
}
a = new Point(1, 2)
b = new Point(3, 4) 

plus(a, b) == a.plus(b)

```
---
## Categories - static functions as methods
```
class PointFunctions {
	static Point minus(Point that, Point other) {
	    new Point(that.x - other.x, that.y - other.y)
	}
}
use(PointFunctions) {
	a - b	
}

=> Point(-2, -2)
```
---
## Extensions - registration of Categories
Categories applied globally in the Groovy runtime

[DefaultGroovyMethods](https://github.com/groovy/groovy-core/blob/master/src/main/org/codehaus/groovy/runtime/DefaultGroovyMethods.java)

[Collection GroovyDoc](http://groovy.codehaus.org/groovy-jdk/java/util/Collection.html)
---
## Imperative vs declarative
``` groovy
int addIntegers(int... integers) {
	int result = 0

	for (int i = 0; i < integers.size(); i++) {
		result += integers[i]
	}
	result
}

addIntegers(1, 2, 3) == [1, 2, 3].sum()

```
---
## Functions operating on data

Alan Perlis:
> It is better to have 100 functions operate on one data structure than 10 functions on 10 data structures.

---
## Collect - groovy's map
``` groovy
list = [1, 2, 3, 4, 5, 6]
multiplied = list.collect { it * 2 }

=> [2, 4, 6, 8, 10, 12]
```
---
## findAll - groovy's filter
``` groovy
list = [1, 2, 3, 4, 5, 6]
filtered = list.findAll { it % 2 == 0 }

=> [2, 4, 6]
```
---
## inject - groovy's reduce
``` groovy
list = [1, 2, 3, 4, 5, 6]
list.inject(1) { a, b -> a * b }

=> 720
```
---
## Currying
Partial application of functions
``` groovy
people = ['Jane', 'Dave', 'Wendy']
nth = { sequence, index -> sequence[index] }
nthPerson = nth.curry(people)
nthPerson(2)

=> 'Wendy'

second = nth.rcurry(1)
second(people)

=> 'Dave'

```
---
## Function composition
Combining simple functions to build more complicated ones
``` groovy
reverse = { it.reverse() }
capitalize = { it.toUpperCase() }
third = { it[2] }

input = ['hello', 'there', 'people']

thirdReverse = third >> reverse
thirdReverse(input)
=> 'elpoep'

reverseThird = third << reverse
reverseThird(input)
=> 'hello'

process = third >> reverse >> capitalize
process(input)
=>'ELPOEP'

```
---
## Memoization
Caching repeatable results
```groovy
counter = 0

fn = { arg -> counter ++ }
(1..10).each { fn(2) }
 
counter
 
counter = 0
 
memoizedFn = fn.memoize()
(1..10).each { memoizedFn(2) }
 
counter

```
---
# Benefits
* Ability to reason about increases
* Testability
* Safer concurrency
* Code reuse 
* Performance optimizations

---
## Questions?
