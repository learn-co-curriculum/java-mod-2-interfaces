# Interface

## Learning Goals

- Explain an interface
- Use interfaces in Java

## Introduction

Now that we have an `Animal` class, let's say we want to keep one of these
animals as a pet! This is great since both our `Cat` and `Parrot` could be
considered a pet. So maybe we can just add some pet behaviors in our `Animal`
class.

Wait... isn't a lion an animal too? And so is a tiger and a bear! Oh, my! We
don't want to pet any of those animals - we might get eaten by one of them! Let
us remove that method from our `Animal` class then.

Well, we still want the option to keep our `Cat` or `Parrot` as a pet. Maybe we
can have each of the child classes implement their own `petAnimal()` method.
But then won't we run into the same issue of the child classes deciding whether
to have a `petAnimal()` method? We want to force our `Cat` and `Parrot` and
other type of pets to have a `petAnimal()` method. It sounds like we need two
parent classes - an `Animal` and a `Pet`!

There's just one issue with having two parent classes... Java won't let us do
that! So now what?

## Java Interface

Turns out, Java has something called an interface. **Interfaces** are groups of
related methods that tell us how to do something rather than what to do. In
fact, their behavior is a lot like an abstract class, except:

- An interface does not implement any default behavior.
- An interface is defined with the `interface` keyword instead of the `class`
  keyword.
- Since an interface does not have any behavior of its own, we do not
  extend it as with the `extend` keyword, but instead **implement** it with the
  `implements` keyword.

Let's create a `Pet` interface!

```java
public interface Pet {
    void petAnimal();
}
```

Note: all methods of an `interface` are public, so there is no need to add the
`public` keyboard to the interface definition.

We can now implement our `Pet` interface!

```java
package com.flatiron.animal;

// Notice here that we can still have the Cat inherit from the Animal class
// but also implement the Pet interface!
public class Cat extends Animal implements Pet {
   private boolean isDeclawed;

   public Cat() {
      super();
      this.isDeclawed = false;
   }

   public boolean getIsDeclawed() {
       return isDeclawed; 
   }
   public void setIsDeclawed(boolean isDeclawed) { 
       this.isDeclawed = isDeclawed;
   }

   public void useLitter() {
      System.out.println("I just used the litter - I'm a clean cat!");
   }

   @Override
   public void eat() {
      super.eat();
      System.out.println("I like to eat cat food!");
   }

   // We had to override this method from the Animal class because it
   // was an abstract method.
   @Override
   public void makeSound() {
      System.out.println("Meow!");
   }

   // We have to override this method from the Pet interface because
   // the pet interface did not define what this method would do.
   @Override
   public void petAnimal() {
      System.out.println("I'm a happy cat! I love pets!");
   }
}

```

The `Cat` class is still a `class` like it was before, but now it extends the
the `Animal` class **and** implements the `Pet` interface!

Abstract classes allow us to implement cases where a base class has some default
behavior, but it doesn't want to implement all default behavior. We saw that with
our example of not being able to come up with a "default sound" for an animal,
for instance. Interfaces, on the other hand, allow us to define that a
"behavior" must be implemented, without specifying how it must be implemented.

There are 2 fundamental applications of interfaces in Java:

1. By defining a set of behaviors (the "what") without defining any
   implementation for those behaviors (the "how"), interfaces allow users of
   their implementations to choose the specific implementation that best suits
   their needs.
2. In Java, a class can only have one parent class - in other words, we can only
   extend one class. But a class can implement multiple interfaces. So defining
   behavior with an interface allows an extending class to implement that
   interface and either also implement other interfaces or extend a single other
   class. This is how we were able to pass `Pet` specific methods along to the
   `Cat` class!

Note: some programming languages provide the ability to extend multiple classes
(it's called "multiple inheritance") - the discussion as to why Java chose not
to implement multiple inheritance is beyond the scope of this module, but it is
a choice that makes interfaces very valuable.

## Multiple Interfaces

To continue with our animal-themed example, we can create interfaces that take
better advantage of the unique properties of interfaces in Java. For example,
let's say that we know that not all animals can fly. We can create the following
interface:

```java
public interface CanFly {
    void fly();
}
```

Since our `Parrot` is considered a pet and can fly, let's see how we can
implement multiple interfaces!

```java
package com.flatiron.animal;

public class Parrot extends Animal implements Pet, CanFly {
   private int numWings;
   private boolean inFlight;

   public Parrot() {
      super();
      this.numWings = 2;
      this.inFlight = false;
   }

   public int getNumWings() { 
       return numWings; 
   }
   public void setNumWings(int numWings) { 
       this.numWings = numWings; 
   }

   public void startFlying() {
      System.out.println("I'm flying - weeee!!!");
      inFlight = true;
   }

   public void stopFlying() {
      System.out.println("Coming down for a landing!");
      inFlight = false;
   }

   // This method is overriding the abstract method defined in
   // the Animal class
   @Override
   public void makeSound() {
      System.out.println("Squawk!");
   }

   // This method is overriding the method defined in
   // the Pet interface
   @Override
   public void petAnimal() {
      System.out.println("I'm a parrot that enjoys being pet!");
   }
   
   // This method is overriding the method defined in
   // the CanFly interface
   @Override
   public void fly() {
       System.out.println("I'm a parrot and I can fly between 81 and 200 miles per hour!");
   }
}
```

Now imagine that we are going into a pet store, and we know we want to adopt a
pet, but we aren't sure what type of animal we are going to adopt! We want to
have some logic that involves petting our new pet, but we don't know in advance
what kind of pet we will be getting!

Using traditional programming with only the exact types, we would have to have a
different variable for each type of pet, and then choose between the two for
our business logic based on the type of pet we adopt. It would lead to messy
code like this:

```java
Cat petCat;
Parrot petParrot;

// logic to initialize either the Cat or the Parrot object

if (petCat != null) {
    petCat.petAnimal();
} else if (petParrot != null) {
    petParrot.petAnimal();
}
```

This leads to the following problems:

1. We need to declare, know about, and maintain multiple variables that are all
   used conditionally, but for the same purpose, i.e. to call methods that all
   "pets" implement. This defeats the purpose of abstraction, which is one of
   the four pillars of object-oriented programming.
2. Every time we call one of those methods, it has to be called on the object of
   the right type, which means we always have to test which object was actually
   initialized and then call the method on that object. This is repetitive code
   that provides no benefits.

The alternative is to take advantage of the interfaces and declare our `Pet`
object to be of the interface we're interested. It can then be initialized to
the proper actual type based on the animal we choose to adopt!

```java
Pet myNewPet;

// Code to retrieve the what kind of pet we adopt
// We will have to initialize myNewPet before we can
// call the petAnimal() method

myNewPet.petAnimal();
```

In this case, the code to pet our new pet can be run on the `myNewPet` object
regardless of its actual type at runtime. We have converted 7 lines of code
to 2 lines of code, reduced code duplication, and more importantly, we have now
made it irrelevant for our code to need to know the type of pet we adopt - all
our code needs to know is that we adopted a pet that can be petted. The
underlying details of the exact type of object is no longer relevant.
