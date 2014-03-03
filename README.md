# langcompare

Compare Ruby to Python, Clojure and Java. I'm writing this primarily to help me get a better understanding of Ruby based on some of the languages I already know.

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

### Ruby

Ruby has several different things that are closure-like. Each of them are not quite like a Lisp-1 closure.

Blocks. These are like anonymous functions, except they're not quite first class. They can work for methods that are programmed to accept them, such as the each method which hangs off a collection.

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

Procs. First class anonymous functions. The irony is that blocks are just syntactic sugar to make Procs look more
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

Lambdas. Like procs, these are first class anonymous functions. But they check their airity and can override the return call. This means that Procs (and by extension, blocks which are merely special cases of Procs) expect to take over the return behavior of whatever is using them and Lambdas do not. _This is the closest to a Lisp-1 first class function._

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

