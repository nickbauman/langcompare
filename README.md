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
    (reduce (fn[_ person] (println (str "Hello " person))) nil people))

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
