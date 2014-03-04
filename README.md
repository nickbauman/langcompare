# Compare Ruby, Python, Clojure and Java

I'm writing this primarily to help me get a better understanding of Ruby based on some of the languages I already know.

## How are varargs done?

### Ruby

Splat arguments

```ruby
def greeting(*people)
    people.each { |person| puts "Hello #{person}!" }
end

greeting "Rachelle", "Joe", "John"
```

### Python

Args (and kwargs)

```python
def greeting(*people):
    for person in people:
        print "Hello {0}".format(person)

greeting("Rachelle", "Joe", "John")
```

### Clojure 

Ampersand args, which have the additional feature of being optional

```clojure
(defn greeting [& people]
    (doseq [name people] (println (str "Hello " name))))

(greeting "Rachelle" "Joe" "John")
```

### Java

Varargs, also optional using an ellipses, which creates a zero-length array when not used 

```java
class Example {
    void greeting(String... people) {
        for(String person: people)
            System.out.println("Hello " + person);
    }
}

new Example().greeting("Rachelle", "Joe", "John");
```

## How are closures done?

Closures were born in Lisp. True closures are first class. They allow capturing outer scope within an inner scope. They can be anonymous, assigned to symbols, values or variables, passed to functions, and treated as dependencies.

### Ruby

Ruby has several different things that are closure-like. Each of them are not quite like a real Lisp closure.

*Blocks.* are like anonymous functions, except they're not quite first class. They can work for methods that are programmed to accept them, such as the each method which hangs off a collection.

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

*Procs.* are first class anonymous functions. The irony is that blocks are just syntactic sugar to make Procs look more
"Rubyish". So behind every block you'll find a Proc that's not quite as flexible but uses a more concise syntax. So
you could do this, for example:

```ruby
f = Proc.new do |i| puts i end
# or, alternatively:
f = Proc.new {|i| puts i}
# notice the irregular call requirement here
f.call("foo")
"foo"
```

*Lambdas.* are like procs, these are first class anonymous functions. But they check their airity and can override the return call. This means that Procs (and by extension, blocks which are merely special cases of Procs) expect to take over the return behavior of whatever is using them and Lambdas do not. _This is the closest to a Lisp first class function._

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

(This is a classic problem with so-called hybrid OO/Functional languages: they use _syntax_ to imbibe meaning to the code, where functional languages do not use this crutch, instead relying on recombinant _semantics_ to achieve more consistent, comprehensible approaches.)

Lambdas are very close to first-class functions in Lisp. Except you cannot create a dependency on a Lambda because of Ruby's "everything is an object" mantra. The closest thing to achieving this is to use:

*Modules.* A module is similar to an _abstract class_ in Java or a _Category_ in Objective C. You can take behavior (functions) and bolt them into a stateless "module" instance:

```ruby
module Renaisannce
   def by_phi(x)
       x / ((Math.sqrt(5)+1) / 2)
   end
   
   def fib(n)
       n <= 1 ? n :  fibonacci( n - 1 ) + fibonacci( n - 2 ) 
   end
end

import Renaisannce

Renaisannce.by_phi 5
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


*Labmdas* have their place in Python, too. These are the closest to pure functional Lisp closures.


