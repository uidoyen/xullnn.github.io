---
title:  "Recognize basic OO concepts in OO JavaScript"
categories:
tags: [Programming]
---

Recognize basic OO concepts in OO JavaScript

> **[Object-oriented programming (OOP)](https://en.wikipedia.org/wiki/Object-oriented_programming)** is a programming paradigm based on the concept of "objects", which can contain data, in the form of fields (often known as attributes or properties), and code, in the form of procedures (often known as methods).

If you are familiar with the concept of OOP and have learn one or more OO languages before. You should know a class is basically a predefined template which can be used to create objects. And these objects can get some states and behaviors from the class they instantiated from. It's a pretty neat concept that manifests in many OO languages.

But if you've learned OO programming in Javascript, you may feel there's something unnatural. That's not the OOP you learned before.

Javascript use constructor to instantiate object instances. Normally, instances get states(properties) from their constructor and get behaviors(methods) from their constructor's prototype object -- You can inspect this prototype object by retrieve the constructor's `prototype` property.

```js
function Person(age, name) {
  this.age = age; // state(property)
  this.name = name; // state(property)
}

Person.prototype.act = function act() {  // behavior(method)
  let behavior = this.name + " is " + this.age.toString() + " years old and he is walking.";
  console.log(behavior);
}

Person.prototype // Person { act: [Function: act] }
var p = new Person(18, "Bob"); // instantiation
p.act(); // perform some behavior
// logs: Bob is 18 years old and he is walking.
```

Note that in Javascript, the convention of naming a constructor -- which in fact is a function -- is to capitalize it, so it can be differentiated from normal functions.

Let us review some key concepts in OOP:
- class: a template to create objects(instances of that class)
- instance: objects instantiated from their class
- instantiation: the process of creating an object from a class
- states/attributes/properties: data held by an object
- behaviors/methods: functions(procedures/code) held by an object

Now let's take the Javascript example above and see how to do the similar thing in several other languages in an OO style. Basically what all the code sections do are:

- create a `Person` class which
  - defines a constructor(instantiation) method to set object instances' `age` and `name` states
  - defines a simple method `act`
- instantiate an `Person` object
- call `act` on the object which logs message `"Bob is 18 years old and he is walking."`

#### The Python way

```py
class Person:
    def __init__(self, age, name):
        self.age = age
        self.name = name

    def act(self):
        behavior = self.name + " is " + str(self.age) + " years old and he is walking.";
        print(behavior)

p = Person(18, 'Bob')
p.act()
```

#### The Ruby way

```ruby
class Person
  def initialize(age, name)
    @age = age;
    @name = name;
  end

  def act
    behavior = @name + " is " + @age.to_s + " years old and he is walking.";
    puts behavior;
  end
end

p = Person.new(18, 'Bob')
p.act
```

#### The Java way

```java
public class TestPerson {
  public static void main(String[] args) {
    class Person { // just focus on the code below ----------------------------
      int age;
      String name;

      public Person(int age, String name) {
        this.age = age;
        this.name = name;
      };

      public void act() {
        String behavior = name + " is " + Integer.toString(age) + " years old and he is walking.";
        System.out.println(behavior);
      };
    }  // ---------------------------------------------------------------------

    Person p = new Person(18, "Bob");
    p.act();
  }
}
```

#### Commonalities behind syntax difference

Despite syntax difference, the 3 examples from python, ruby and java are analogous to each other. If you are familiar with basic OO concepts, you'll immediately recognize what each of the code snippet is doing. Because the OO concept and thinking process behind the scene are the same:

- class definition go first
- then there is an object instantiated from the class

#### Searching the concepts in JavaScript

Here's the Js code example again:

```js
function Person(age, name) {
  this.age = age; // state(property)
  this.name = name; // state(property)
}

Person.prototype.act = function act() {  // behavior(method)
  let behavior = this.name + " is " + this.age.toString() + " years old and he is walking.";
  console.log(behavior);
}

Person.prototype // Person { act: [Function: act] }
var p = new Person(18, "Bob"); // instantiation
p.act(); // perform some behavior
// logs: Bob is 18 years old and he is walking.
```

Let's bring the basic OO concepts into JavaScript to see if we can find the corresponding thing there.

##### Constructor corresponds to instantiation

Should the `Person` be the "class" here? From the capitalization and the way we instantiate `Person` object(`new Person(18, "Bob")`), it seems to be so. But if it is so, why don't we define the `act` method in this "class"? Further more, where is the counterpart of `__init__`, `initialize`?

In Javascript, `Person` is called a constructor. In fact that is the concept behind `__init__`, `initialize`, in Java the constructor is the code: `public Person...` which is inside `class Person...`. That's the method will be called if we instantiate objects from the real `Person` class -- no matter in python or ruby or java.

If we defined `act` method inside the `Person` constructor in Javascript, could this make `Person` more like a "class"? No. If we did so, it would create a new copy of `act` for every newly instantiated `Person` object. In other words, instead of referencing the `act` defined in the class, every instance get their own version of `act` although the code is actually the same.

##### Prototype corresponds to class

If `Person` is not the class, who is? Since we defined some behavior in `Person`'s prototype, it could be the second guess.

In OOP, the topic of class usually entails the topic of inheritance. For example, `class Dog` inherits from `class Mammal` inherits from `class Creature`. And we also talk about this inheritance chain in Javascript.

Taking the example of Ruby code above, we can easily check the inheritance chain:

```Ruby
Person.ancestors # => [Person, Object, Kernel, BasicObject]
```

In Javascript, since `Person` is a constructor -- the method used to instantiate instance -- not a class(conceptually speaking). So the inheritance should be constituted by prototype objects.

```js
Object.getPrototypeOf(Person.prototype) === Object.prototype // true
```

Here is a very important difference between `prototype` property of a function and what we get from `Object.getPrototypeOf()`. To me this is a very confusing usage and I wrote a post to make this clearer. To simply put the difference:

- `Object.getPrototypeOf()` works on the inheritance chain, conceptually it looks for the superclass of a class. In this case we are looking for the 'superclass' of `Person.prototype`
- `Person.prototype` is the object functions as a class, it's where the `Person` instances' behaviors get defined.

In our simple Js example, `Person.prototype` inherits from `Object.prototype`. This means `Person.prototype` also inherits all the properties and methods defined in `Object.prototype`. This makes `Person`'s instances have the ability to call [all methods defined on `Object.prototype`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/prototype).

#### What's unnatural here

First, Javascript first has a constructor which is used to instantiate objects, then it kind of hanging a `prototype`(conceptually, a class) on that constructor.

Second, the concept of "class", "instance", "inheritance" are messed up by the ambiguity of the word "prototype".

What makes this worse is the `instanceof` operator. In our Js example, `Person` is a constructor, but `new Person(18, 'Bob') instanceof Person` returns `true`. An object is an instance of a constructor? Shouldn't an object be an instance of a class? Without having a close look at the [doc](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/instanceof), it's easy for us to misunderstand what this operator really does.

> The `instanceof` operator tests whether the `prototype` property of a constructor appears anywhere in the prototype chain of an object.

It checks if `Person.prototype` appears anywhere in the prototype chain of an instance. If we were allowed to call something like `p instanceof Person.prototype` where `p` is a `Person` instance, things would be far more clear. Unfortunately, this syntax throws: `TypeError: Right-hand side of 'instanceof' is not callable`.

#### How to get out of the swamp

The short answer is: focus on the basic concepts and OO thinking process.

The commonalities of normal use cases are:

- class defines behaviors(for instances)
- object(instance) instantiated from class
- constructor defines states/attributes/properties for instance

What about the messy terms in JavaScript? Let's add:

- constructors are functions(methods)
- classes are objects

Then check this in JavaScript:

```js
typeof Person; // 'function'
typeof String;  // 'function'
typeof Object; //'function'

typeof Person.prototype; // 'object'
typeof String.prototype; // 'object'
typeof Object.prototype; // 'object'
```

That's why they(`Person`, `String`, `Object`) are called constructor and why they are not "classes" conceptually.

Another important thing is when we encounter the word "prototype" in JavaScript, we should slow down, think about what does "prototype" mean under the current context. When we talk about inheritance, what we are talking about must be objects. When we talk about constructors, what we are talking about must be functions.

Here I draw a simplified graph about classic OO model and OO JavaScript model.

![](https://image-for-articles.s3-ap-southeast-1.amazonaws.com/image-bucket-1/oojs.jpeg)

What they both share is the basic OO concept. However, according to the graph, one big difference I haven't mentioned is the visible parts are different. Classic OO model exposes classes to the audience while OO JavaScript exposes constructors to audience.

#### Conclusion

Often, the fog of syntax, terms and other details may rise in front of our eyes, but there's always something that can guide us -- the basic concepts.
