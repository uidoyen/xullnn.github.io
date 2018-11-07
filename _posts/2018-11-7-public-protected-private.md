---
title:  "Explore instance public, protected and private method"
categories:
tags: [Ruby]
---

*In this post, all discussions are under the context of instance methods. Things may get different when it comes to class methods.*

When being asked the question: "What's the differences among public, protected and private method?" We may answer:

- Public methods are methods that are available to anyone who knows either the class name or the object's name.
- Private methods are not accessible outside of the class definition at all, and are only accessible from inside the class when called without `self`.
- From outside the class, protected methods act just like private methods; from inside the class, protected methods are accessible just like public methods.

These statements are not wrong, but not accurate. And what are "inside the class definition" and "outside the class definition"? These are questions that we beginner programmers think we would have understand, but actually we might not.

### Inside or Outside?

"Inside the class definition" is easy to understand.

```ruby
class Dog
  def bark
    puts "WangWang...Wang!"
  end
end
```

Apparently the area between `class Dog` and `end` is inside. So logically, areas other than inside are all outside, right? Maybe like this:

![](https://s3-ap-southeast-1.amazonaws.com/image-for-articles/image-bucket-1/outside.jpg)

This doesn't help. Let's recall the first statement:

> Public methods are methods that are available to anyone who knows either the class name or the object's name.

When would we involve the **class's name** or the **object's name**? When we done with defining a class like `Dog`, there're many ways we may involve the class name or the object's name. Some of them are:

#### 1) call instance method directly on an instance of this class

```ruby
Dog.new.bark
```

#### 2) pass an instance of this class as an argument to another class' method

```ruby
class Dog
  def bark
    puts "WangWang...Wang!"
  end
end

class Person
  def walk_pet(pet)
    puts "Walking the #{pet.class}"
    pet.bark
  end
end

dog = Dog.new
Person.new.walk_pet(dog)
# Walking the Dog
# WangWang...Wang!
#  => nil
```

#### 3) when an instance of this class serve as another class' state(means they are collaborator objects)

```ruby
class Dog
  def bark
    puts "WangWang...Wang!"
  end
end

class Person
  attr_reader :pet
  def initialize(pet)
    @pet = pet
  end
end

bob = Person.new(Dog.new)
bob.pet.bark
# WangWang...Wang!
#  => nil
```

What about invoking a public method **inside class definition**? or even inside other class' class definition

```ruby
class Dog
  def bark
    puts "WangWang...Wang!"
  end

  self.new.bark
end
# WangWang...Wang!
#  => nil

class Cat
  Dog.new.bark
end
# WangWang...Wang!
#  => nil
```

It works too but we'd not likely to do this in practice.

Through these examples we realize that **"outside the class definition"** means a lot. It could mean "other than class definition"; it could mean "inside other classes' definition"; it could mean many other situations. Be aware of what is inside and outside class definition are important to understand the difference among public, protected and private method, and thankfully we are clear now.

### 1 Public methods

Things are clear now, public methods are accessible throughout the whole program if we know the class name, or we get an instance of this class, this could be done in more than one way, like assigning the instance to a local variable then pass it to another method, or assigning it to instance variable, or directly instantiating a new instance inside the argument list, and so on.

### 2 Private methods

> Private methods are not accessible outside of the class definition at all, and are only accessible from inside the class when called without `self`.

Let's verify this statement step by step:

##### 1) not accessible outside of the class definition at all

First let's see a workable example:

```ruby
class Dog
  def sleep
    dream
  end

  private

  def dream
    puts "I am flying!"
  end
end

Dog.new.sleep
# I am flying!
#  => nil
```

Here we catered all the requirements, only call it from inside class definition without prepending a `self`

Next let's leave the class definition:

```ruby
Dog.new.dream # most straight way
# NoMethodError (private method `dream' called for #<Dog:0x00007fed299e1f88>)
```

No, not work.

What about other "outside the class definition" scenarios? For other possible scenarios, the final step of all expressions is call the method directly on a `Dog` object, so it's the same with `Dog.new.dream`. So they won't work too.

##### 2) are only accessible from inside the class when called without `self`

To verify this we just add a `self` infront of `dream` in `sleep` method

```ruby
class Dog
  def sleep
    self.dream
  end

  private

  def dream
    puts "I am flying!"
  end
end

Dog.new.sleep
```

This time we got `NoMethodError (private method `dream' called for #<Dog:0x00007fb0f6079708>)`

So this not work too. Can we now say that the statement about private is true?

No. When involving inheritance, things changed.

##### 3) private methods in inheritance hierarchy

Let's take a look at this example:

```ruby
class Dog
  def sleep
    dream
  end

  private

  def dream
    puts "I am flying!"
  end
end

class BullDog < Dog
end

class FrenchBullDog < BullDog
end

BullDog.new.sleep
# I am flying!
#  => nil
FrenchBullDog.new.sleep
# I am flying!
#  => nil
```

`BullDog` and `FrenchBullDog` are both descendant classes of `Dog`. Rigorously saying, they are not inside `Dog`'s class definition. But they can access the private method in the right way too. Let's perform a further verification:

```ruby
class Dog
  def sleep
    dream
  end

  private

  def dream
    puts "I am flying!"
  end
end

class BullDog < Dog
  def day_dream
    dream
  end
end

BullDog.new.day_dream
# I am flying!
#  => nil
```

Things are getting clear, if we follow the rule "do not prepend any receiver to a private method", we can actually access private methods inside the whole inheritance hierarchy. But the precondition is superclass' private methods can be accessed in subclasses, not vice versa.

So we may wanna change the statement to:

> Private methods are only accessible inside its first defined class and this class' subclasses, and are only accessible when called without `self`.

*the rule about `self` has an exception about private setter method in Ruby*

### 3 Protect methods

First we'll verify this:

#### 1) From outside the class, protected methods act just like private methods.

```ruby
class Dog
  def sleep
    dream
  end

  protected

  def dream
    puts "I am flying!"
  end
end

Dog.new.dream
```

We got `NoMethodError (protected method 'dream' called for #<Dog:0x00007fb0f786c400>)`. Basically the same result with private method.

Now let's change the example to right usage.

```ruby
class Dog
  def sleep
    dream
  end

  protected

  def dream
    puts "I am flying!"
  end
end

Dog.new.sleep
```

The above code are calling protected method `dream` just like when we were calling a private one. So when we are outside the class definition, we can't give protected method a receiver, so the first half statement is right so far.

#### 2) From inside the class, protected methods are accessible just like public methods.

What's "just like public methods"? How this would be like inside the class definition?

Remember the statement "available to anyone who knows either the class name or the object's name"? Now we need to talk this on the context of "inside class definition".

**Know the class name inside class definition.**

How? Anywhere from within the class definition and outside instance method definitions is of the context of the class, we can grasp it by using `self`

**Know the object name inside class definition.**

Similarly, when we write `self` inside any instance method definitions, we are grasping the instance(object) that are calling this method.

1. know object name by instance methods' `self`

```ruby
class Dog
  def sleep
    self.dream
  end

  protected

  def dream
    puts "I am flying!"
  end
end

Dog.new.sleep
# I am flying!
#  => nil
```

It works. Inside instance method we can invoke protected method while prepending the receiver.

2. know object's name by pass in a same type object

```ruby
class Dog
  def sleep_with(another_dog)
    another_dog.dream
  end

  protected

  def dream
    puts "I am flying!"
  end
end

Dog.new.sleep_with(Dog.new)
# I am flying!
#  => nil
```

This works too.

Let's incorporate inheritance.

```ruby
class Dog
  def sleep_with(another_dog)
    another_dog.dream
  end

  protected

  def dream
    puts "I am flying!"
  end
end

class BullDog < Dog
end

Dog.new.sleep_with(BullDog.new)
# I am flying!
#  => nil
```

This works too.

3. know object name by class definition's `self`

```ruby
class Dog
  def sleep
    self.dream
  end

  protected

  def dream
    puts "I am flying!"
  end

  self.new.sleep # public
  self.new.dream # protected
end
```

Notice the last two line inside class definition, we first called the public method `sleep` by using `self.new` as receiver, then did the same thing with protected method `dream`. The message `"I am flying!"` was only printed out one time, then an exception raised.

We got `NoMethodError (protected method 'dream' called for #<Dog:0x00007fb0f786da08>)`

This partly disproved the statement "From inside the class, protected methods are accessible just like public methods". We were insdie class definition, we were using protected method `dream` as it were a public method, but things went wrong. So it's not exactly the same with public method.

#### 3) protected methods in inheritance hierarchy

We've discussed private methods under this situation. The answer for protected methods is same. Protected methods are also accessible for the subclasses which inherit from where the protected methods are first defined.

### Summary

We wrote so many examples to verify every statement, some of them are not so accurate. But the purpose of this post is not to disprove any statement. The real purpose is to better understand public, protected and private method. At first we might easily mess up the rules, and it seems we have so many details to remember or to notice. But when we step out of all the details and syntax, we just need to be aware of several main points:

- What is the exactly meaning of inside/outside class definition?
  - the more accurate description is inside current class and its subclasses's class definition.
- **Context** and **`self`** is the core to understand.
  - for public methods: no matter inside or outside class definition, we can invoke public methods by sending it to right objects.
  - for protected methods, they are accessible when:
    - 1) inside method definition
    - 2) the receiver is an instance of current class or its subclasses
  - for private methods they are accessible when:
    - 1) inside method definition
    - 2) no receiver.
      - This constriction make private methods only accssible for current calling object, no receiver implies that the implied receiver is an invisible `self`, and this hidden `self` has no ambiguity inside a method definition -- it can only indicates the current object
      - No receiver also eliminate the possiblity of sending private methods to any other object other than current object since we cannot send a message to "nobody".

As we mentioned earlier, *In this post, all discussions are under the context of instance methods. Things may get different when it comes to class methods.*
