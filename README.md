langcompare
===========

Compare Ruby to Python, Clojure and Java

**How are varargs done?

Ruby: Splat arguments

    def greeting(*people)
        people.each { |person| puts "Hello #{person}!" }
    end

    greeting "Rachelle", "Joe", "John"

Python: args (and kwargs)

    def greeting(*people):
        for person in people:
            print "Hello {0}".format(person)

    greeting("Rachelle", "Joe", "John")

Clojure: Ampersand args, which have the additional feature of being optional

    (defn greeting [& people]
        (reduce (fn[_ person] (println (str "Hello " person))) nil people))

    (greeting "Rachelle" "Joe" "John")

Java: Simply varargs, also optional using an ellipses, which creates a zero-length array when not used 

    class Example {
        void greeting(String... people) {
            for(String person: people)
                System.out.println("Hello " + person);
        }
    }

    new Example().greeting("Rachelle", "Joe", "John")
