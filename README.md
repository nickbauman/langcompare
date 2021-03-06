# Compare Ruby, Python, Clojure and Java

I'm writing this primarily to help me get a better understanding of Ruby based on some of the languages I already know.

## How are varargs done?

### Ruby

*Splat arguments* are ruby's way of doing varargs.

```ruby
def greeting(*people)
    people.each { |person| puts "Hello #{person}!" }
end

greeting "Rachelle", "Joe", "John"
Hello Rachelle!
Hello Joe!
Hello John!
 => ["Rachelle", "Joe", "John"] 
```

### Python

*Args* (and kwargs) are python's way of doing varargs

```python
def greeting(*people):
    for person in people:
        print "Hello {0}".format(person)

greeting("Rachelle", "Joe", "John")
Hello Rachelle
Hello Joe
Hello John
```

### Clojure 

Ampersand args

```clojure
(defn greeting [& people]
    (doseq [name people] (println (str "Hello " name))))

(greeting "Rachelle" "Joe" "John")
Hello Rachelle
Hello Joe
Hello John
nil
```

### Java

Varargs uses an ellipses, which creates a zero-length array when not used 

```java
class Example {
    void greeting(String... people) {
        for(String person: people)
            System.out.println("Hello " + person);
    }
}

new Example().greeting("Rachelle", "Joe", "John");
Hello Rachelle
Hello Joe
Hello John
```

## How are closures done?

Closures were born in Lisp. True closures are first class. They allow capturing outer scope within an inner scope. They can be anonymous, assigned to symbols, variables or as values, passed to functions, and treated as dependencies.

### Ruby

Ruby has several different things that are closure-like. Each of them are not quite like a real Lisp closure.

*Blocks* are the most commonly used. Similar to anonymous functions, except they're not quite first class. They can work for methods that are programmed to accept them, such as the each method which hangs off a collection.

```ruby
[1, 2, 3, 4, 5].each { |i| puts i }
1
2
3
4
5
```

You cannot do something like this, however:

```ruby
# blocks are not first-class
f = {|i| puts i}
SyntaxError: (irb):4: syntax error, unexpected tPIPE
```

*Procs* are first-class anonymous functions. (In fact blocks are just syntactic sugar around Procs) So you could do this, for example:

```ruby
f = Proc.new do |i| puts i end
# or, alternatively:
f = Proc.new {|i| puts i}
# notice the irregular call requirement here
f.call("foo")
"foo"
# in later versions of Ruby you can use square brackets instead of call:
f["foo"]
"foo"
```

*Lambdas* are also procs, these are first class anonymous functions. But they check their airity and can override the return call. This means that Procs (and by extension, blocks which are merely special cases of Procs) expect to take over the return behavior of whatever is using them and Lambdas do not. _This is the closest to a Lisp first class closure._ 

But Lambdas are Procs, too: they merely set a flag on the Proc to enable the Lamda behavior.

So to illustrate the difference here:

```ruby
def test_return(code)
    code.call
    return "done"
end

puts test_return(Proc.new { return "this is a Proc" })
LocalJumpError: unexpected return

puts test_return(lambda { return "this is a lambda"})
done
```

Long story short, don't put a return in any Proc (or block) and you will be fine. However, if you find you need one, use a Lambda.

Lambdas are very close to first-class functions in Lisp. Except you cannot create a dependency on a Lambda because of Ruby's "everything is an object" mantra. The closest thing to achieving this is to use:

*Modules.* A module is similar to an _abstract class_ in Java or a _Category_ in Objective C. You can take behavior (functions) and bolt them into a stateless "module" instance:

```ruby
module Renaisannce
   Renaisannce.lambda_by_phi = lambda { x / ((Math.sqrt(5)+1) / 2) }
   f = lambda do |n|
        n <= 1 ? n :  f( n - 1 ) + f( n - 2 )
   end
   Renaisannce.lambda_fib = f
end

x = Renaisannce.by_phi 5
x.call
 => 3.090169943749474
 
y = Renaisannce.lambda_fib 5
y.call
 => 3.090169943749474
```


### Python

Python has a few closure-like approaches, too. The simplest is a straightforward function within a function.

```python
def foo(x):
    m = 4
    def bar(y):
        return m * x
    return bar(6)

foo(6)
24
```

Note you _can_ do this with Ruby: define functions within functions. They just can't refer to local variables defined in the outer scope of the function, so this fails as a closure:

```ruby
def foo(a)
   x = 4
   def bar(b)
     b * x 
   end
   bar(a)
end

foo(6)
NameError: undefined local variable or method `x' for main:Object
```


*Lambdas* have their place in Python, too. These are the closest to pure functional Lisp closures python offers. They can be assigned. Passed. They can refer to other values in the outer scope. They can even be included as dependencies. In spite of all the options Ruby seems to offer, python is much more Functional than Ruby with much less mechanics of syntax.

```python
r = 17
f = lambda x: x * 2 + r
f(4)
25

def foo(a):
  d = 5
  g = lambda m: m * 2 + f(2) + 5
  return g(a)

foo(9)
44
```

Not all is well, however. In fact lambdas are so pure in Python that they cannot contain statements. Lisps do not need statements. They achieve what other languages use statements for with special forms or macros. Python, however, does not have special forms or macros. So if you need a conditional in your python lambda, you'll have to resort to python's expressions that return truthy or falsey.

```python
f = lambda x: 0 == x % 2 and x * 2 or 0

f(2)
4

f(3)
0
```

Lambdas can be imported as dependencies from other namespaces just like ordinary functions can be. They need not be directly attached to objects or modules.

### Clojure

Clojure is a Lisp-1 language. Closures are, erm, Clojure's bread and butter. Because of its Lisp-1 heritage, its homoiconicity implies that even a simple datastructure like a vector already behaves as function.

```clojure
(def v [1 2 3 4])

(v 2)
3
```

Clojure's anonymous function comes in two forms.

```clojure
(fn[x] x * 2)

#(% * 2)
```

The first is the canonical form. The second is the _sugar_ form. Sugar is what Clojuristas refer to as syntactic adornments to the parser to make the language a little more consise (some would say merely terse) idom.
