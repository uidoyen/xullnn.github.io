---
title:  "Recognize basic OO concepts in OO JavaScript"
categories:
tags: [Programming]
---

Recognize basic OO concepts in OO JavaScript

### 1 Some background

If you are familiar with the concept of OOP and have tasted one or more OO languages before. You should know a class is basically a predefined template which can be used to create objects. And these objects can get some states and behaviors from the class they instantiated from. It's a pretty neat concept that manifests in many OO languages.

> **[Object-oriented programming (OOP)](https://en.wikipedia.org/wiki/Object-oriented_programming)** is a programming paradigm based on the concept of "objects", which can contain data, in the form of fields (often known as attributes or properties), and code, in the form of procedures (often known as methods).

But if you've learned OOP in Javascript, you may feel there's something unnatural. That's not the OOP you learned before. JavaScript proclaims that it's a ["prototype-based" language](https://developer.mozilla.org/en-US/docs/Glossary/Prototype-based_programming). What is "prototypal-based"?

> [Prototype-based programming](https://en.wikipedia.org/wiki/Prototype-based_programming) is a style of object-oriented programming in which behavior reuse (known as inheritance) is performed via a process of reusing existing objects via delegation that serve as prototypes.

Since prototype-based programming is still *'a style of object-oriented programming'*. So it can't deviate too far from the basic concepts of OOP. The main purpose of writing this post is to recognize these concepts from the surface of the 'unnatural' things.

### 2 A basic OO example in JavaScript

Let's inspect some OO code in JavaScript. Javascript use constructor to instantiate object instances. Normally, instances get states(properties) from their constructor and get behaviors(methods) from their constructor's prototype object -- You can inspect this prototype object by retrieve the constructor's `prototype` property.

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

First, Let us review some key terms in OOP:
- class: a template(blueprint) to create objects(instances of that class)
- instance: objects instantiated from their class
- instantiation: the process of creating an object from a class
- states/attributes/properties: data held by an object
- behaviors/methods: functions(procedures/code) held by an object


### 3 Same OO case in different languages

Now let's keep the Javascript example above and see how to do similar thing in several other languages with OO style. Basically what all the code does is:

- create a `Person` class which
  - defines a constructor(instantiation) method to set object instances' `age` and `name` states
  - defines a simple instance method `act`
- instantiate a `Person` object
- call `act` on the object which logs message `"Bob is 18 years old and he is walking."`

##### 3.1 The Python way

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

##### 3.2 The Ruby way

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

##### 3.3 The Java way

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

### 4 Commonalities behind syntax difference

Despite the syntax differences, the 3 examples from python, ruby and java are analogous to each other. If you are familiar with basic OO concepts, you'll immediately recognize what each of the code snippet is doing. Because the OO concepts and thinking processes behind the scene are the same:

- class definition go first
- then objects can be instantiated from the class

### 5 Searching OO concepts in JavaScript

Here's the Js example code again:

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

Let's bring the basic OO concepts into this JavaScript example, see if we can find some of them there.

##### 5.1 Constructor and instantiation

Where is JavaScript's counterpart of `__init__`, `initialize` method? Method name with `init`(java's syntax is different) in it often used as an interface to instantiate object from a class, it is usually called *constructor*.

In Javascript, `Person` function is constructor. In fact it shares the concept behind `__init__`, `initialize`. That's the method that will be called if we instantiate objects from the real `Person` class -- no matter in python or ruby or java. Although Java's syntax of defining constructor is similar to JavaScript's, but in Java -- like the other two languages -- the constructor is defined *inside* the class.

##### 5.2 Prototype and class

According to the other 3 languages' naming convention for a class, `Person` really looks like a class in JavaScript. Plus the syntax we instantiate `Person` object(`new Person(18, "Bob")`), it seems `Person` ought to be the class. But now we know it's a constructor. Maybe a constructor can also be class? Can we just define the `act` method inside `Person` function then it becomes a class?

No. If we did so, it would create a new copy of `act` for every newly instantiated `Person` object. In other words, instead of referencing the `act` defined in the class, every instance get their own version of `act` although the code is actually the same.

If `Person` is not the class, who is? Since we defined some behavior in `Person`'s prototype, it could be the second guess.

In OOP, the topic of class usually entails the topic of inheritance. For example, `class Dog` inherits from `class Mammal` inherits from `class Creature`. Taking the example of Ruby code above, we can easily check the inheritance chain:

```Ruby
Person.ancestors # => [Person, Object, Kernel, BasicObject]
```

As it comes to the topic of OOP in JavaScript, we also talk about inheritance chain. To construct an inheritance chain, we need objects(or classes), not constructor functions. So JavaScript inheritance chain is constituted by prototype objects. Note what on the chain are objects, this demonstrates again the `Person` *function* can't be the conceptual class.

There is a method called `Object.getPrototypeOf()` which takes an object as argument and returns the prototype(class) of it.

If the passed in object is an instance object, for example `new Person(18, 'Bob')`, it returns the prototype(class) object it instantiated from. This returned prototype object is actually a private attribute of the instance, and this private attribute is actually gotten from the constructor's `prototype` property. This means after instantiating an object, changing the `prototype` property of the constructor won't affect the prototype(class) of the previously created instance.

```js
var p = new Person(18, 'Bob'); // p is an instance of its class
Object.getPrototypeOf(p) === Person.prototype; // true

function Fish() {};
Person.prototype = Fish.prototype;
Object.getPrototypeOf(p) // still:  Person { act: [Function: act] }
```

If the passed in object is a prototype object -- object held by a constructor's `prototype` property. Then we are moving at the prototypal chain or say the inheritance chain.

```js
Object.getPrototypeOf(Person.prototype) === Object.prototype; // true
```

In our simple Js example, `Person.prototype` inherits from `Object.prototype`, both of them are objects. Precisely speaking, `Person.prototype` is an instance of `Object.prototype`.

```js
Person.prototype instanceof Object; // true
```

This means `Person.prototype` also inherits all the properties and methods defined in `Object.prototype`. This makes `Person`'s instances have the ability to call [all methods defined on `Object.prototype`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/prototype).

### 6 What's unnatural here

First, Javascript first has a constructor which is used to instantiate objects, then it kind of hanging a `prototype`(conceptually, a class) on that constructor.

```js
var p = new Person(18, 'Bob'); // p is an instance of its class
Object.getPrototypeOf(p) === Person.prototype; // true

function Fish() {};
Person.prototype = Fish.prototype; // Person's `prototype` property changed
Object.getPrototypeOf(p) // still:  Person { act: [Function: act] }

var f = new Person(20, 'Joe');
f.act()
// Thrown:
// TypeError: f.act is not a function
```

Look, prototype can be substituted just by hanging another one on the constructor's `prototype` object, but the unchanging constructor on the surface doesn't tell the big change under the hood.

Second, the meaning of "prototype" in JavaScript is not easy to distinguish without context. `prototype` is a property of a constructor, then the constructor pass this property to all the instances. The syntax `Person.prototype` makes it feels like we are calling out the prototype(class) of `Person`, but what is the real prototype(class) of `Person`?

```js
Object.getPrototypeOf(Person) === Person.prototype; // false
Object.getPrototypeOf(Person);
// [Function]
```

What makes this worse is the `instanceof` operator. In our Js example, `Person` is a constructor, but `new Person(18, 'Bob') instanceof Person` returns `true`. An object is an instance of a constructor? Shouldn't an object be an instance of a class? Without having a close look at the [doc](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/instanceof), it's easy for us to misunderstand what this operator really does.

> The `instanceof` operator tests whether the `prototype` property of a constructor appears anywhere in the prototype chain of an object.

It checks if `Person.prototype` appears anywhere in the prototype chain of an instance. If we were allowed to call something like `p instanceof Person.prototype` where `p` is a `Person` instance, things would be more clear. Unfortunately, this syntax throws: `TypeError: Right-hand side of 'instanceof' is not callable`.

### 7 How to get out of the swamp

##### 7.1 back to the basic concepts

The short answer is: focus on the basic concepts and OO thinking process.

Remember the commonalities of normal OO use cases are:

- class defines behaviors(for instances)
- object(instance) instantiated from class
- constructor
  - defines states/attributes/properties for instances
  - is the interface to instantiate new object of a class

What about the messy terms in JavaScript? Let's add:

- constructors are functions(methods) --> not objects --> so they can't be conceptual class
- classes are objects --> only objects can form inheritance chain

Then check this in JavaScript:

```js
typeof Person; // 'function'
typeof String;  // 'function'
typeof Object; //'function'

// the chain is here
typeof Person.prototype; // 'object'
typeof String.prototype; // 'object'
typeof Object.prototype; // 'object'
```

That's why they(`Person`, `String`, `Object`) are called constructor and why they are not "classes" conceptually. Only `Something.prototype` object is the class. Another proof is methods like `Array.prototype.map()`. According to the dot notation chain, we know `map` is a property of `Array.prototype` object. But when we use it we put an array instance in the front like `[1, 2, 3].map(...)`. A method defined in one object but can be used by another object. Do you smell the 'class' there?

##### 7.2 think beyond terms

Terms may not make sense at all time. The term "prototype" in JavaScript may confuse us if we are not aware of the context. And I haven't mentioned `constructor` is a property of a constructor's `prototype` object's property, which references back to the constructor, which is actually passed to all the instances. When we encounter the word "prototype" in JavaScript, we should slow down, think about what does "prototype" mean under the current context. But there's one thing keeps the same -- when we talk about 'prototype', we are talking about objects and class, we are talking about inheritance.

##### 7.2 Realize what causes your confusion

Here I draw a simplified graph to show you a main difference between classic OO model and OO JavaScript model.

![](https://image-for-articles.s3-ap-southeast-1.amazonaws.com/image-bucket-1/oojs.jpg)

What they both share is the basic OO concepts. However, according to the graph, one big difference I haven't mentioned is the visible parts are different. Classic OO model exposes *classes* to the audience while OO JavaScript exposes *constructors* to audience. And as I mentioned before, substitution of the `prototype` object of a constructor can't be seen if we only look at the constructor.

### Conclusion

Often, the fog of syntax, terms and other details may rise in front of our eyes, but there's always something that can guide us -- the basic concepts.
