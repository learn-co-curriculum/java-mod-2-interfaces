# Interface

## Learning Goals

- Explain an interface
- Use interfaces in Java

## Java Interface

In Java, an `interface` is like an `abstract` class, except:

- An `interface` does not implement any default behavior
- An `interface` is defined with the `interface` keyword instead of the `class`
  keyword
- Since an `interface` does not have any behavior of its own, you do not
  "extend" it as with the `extend` keyword, but instead "implement" it with the
  `implements` keyword

Let's look at an example that builds on our previous examples. We're going to
turn the `Animal` class into an `interface`:

```java
public interface  Animal {
    void takeBreath();
    void makeSound();
}
```

Note: all methods of an `interface` are public, so there is no need to add the
`public` keyboard to the interface definition

Now our `Animal` class isn't a class anymore, it's an interface that has one 2
methods that must be implemented by any class that implements the interface.
Here what this does to the `Cat` class:

```java
public class Cat implements Animal {
    public boolean isDeclawed;

    public void useLitter() {
        System.out.println("I just used the litter - I'm a clean cat!");
    }

    public void makeSound() {
        System.out.println("Meow");
    }

    public void takeBreath() {
        System.out.println("Cat has taken a breath");
    }
}
```

The `Cat` class is still a `class` like it was before, but instead of
"extending" the `Animal` class with the `extends` keyword, it "implements" the
`Animal` interface with the `implements` keyword.

Abstract classes allow us to implement cases where a base class has some default
behavior but it doesn't want to implement all default behavior. We saw that with
our example of not being able to come up with a "default sound" for an animal,
for instance. Interfaces, on the other hand, allow us to define that a
"behavior" must be implemented, without specifying how it must be implemented.

There are 2 fundamental applications of interfaces in Java:

1. By defining a set of behaviors (the "what") without defining any
   implementation for those behaviors (the "how"), interfaces allow users of
   their implementations to choose the specific implementation that best suits
   their needs. We will see this in action in our section about the `ArrayList`
   implementation of the `List` interface
2. In Java, a class can only have one subclass - in other words, you can only
   extend one class. But a class can implement multiple interfaces. So defining
   behavior with an interface allows an extending class to implement that
   interface and either also implement other interfaces or extend a single other
   class.

Note: some programming languages provide the ability to extend multiple classes
(it's called "multiple inheritance") - the discussion as to why Java chose not
to implement multiple inheritance is beyond the scope of this module, but it is
a choice that makes interfaces very valuable.

To continue with our animal-themed example, we can create interfaces that take
better advantage of the unique properties of interfaces in Java. For example,
let's say that we know that not all animals can fly and not all animals can
swim. We can create the following 2 interfaces

```java
public interface CanSwim {
    void swim();
}
```

```java
public interface CanFly {
    void fly();
}
```

Now we can easily define animals that can fly, animals that can swim and animals
that can do both.

Let's look at a class to represent a Pigeon, which is an animal that can fly:

```java
public class Pigeon implements CanFly, Animal {
    public void fly() {
        System.out.println("I'm a pigeon and I can fly up to 90 miles an hour!");
    }

    public void takeBreath() {
        System.out.println("Pigeon has taken a breath");
    }

    public void makeSound() {
        System.out.println("Pigeon goes coo, coo");
    }
}
```

Let's look at a class to represent a ClownFish, which is an animal that can
swim:

```java
public class ClownFish implements Animal, CanSwim {
    public void swim() {
        System.out.println("I'm a clown fish and I'm swimming!");
    }

    public void takeBreath() {
        System.out.println("ClownFish takes a breath under water, a wreath if you will ;-)");
    }

    public void makeSound() {
        System.out.println("ClownFish goes blup, blup");
    }
}
```

And now we can even create a class to represent an animal that can both fly and
swim:

```java
public class Puffin implements Animal, CanSwim, CanFly {
        public void fly() {
            System.out.println("I'm puffin and I can fly up to 55 miles an hour!");
        }

        public void swim() {
            System.out.println("I'm a puffin and I can swim down to 200 feet deep");
        }

        public void takeBreath() {
            System.out.println("Puffin took a breath");
        }

        public void makeSound() {
            System.out.println("Puffin goes brau, brau");
        }
}
```

Now imagine that you're implementing a game that has all these animals (and
more) in it. You want to have some logic that involves making birds fly, but you
don't know in advance what kind of bird the player will have selected or earned.

Using traditional programming with only the exact types, we would have to have a
different variable for each type of bird, and then choose between the two for
our business logic based on the type of bird the player selected. It would lead
to messy code like this:

```java
Pigeon playerPigeon;
Puffin playerPuffin;

// logic to initialize either the Pigeon or the Puffin object based on the player's actions

if (playerPigeon != null) {
    playerPigeon.fly();
} else if (playerPuffin != null) {
    playerPuffin.fly();
}
```

This leads to the following problems:

1. We need to declare, know about and maintain multiple variables that are all
   used conditionally, but for the same purpose, i.e. to call methods that all
   "birds" implement
2. Every time we call one of those methods, it has to be called on the object of
   the right type, which means we always have to test which object was actually
   initialized and then call the method on that object. This is repetitive code
   that provides no benefits
3. Every time we want one of the birds to `fly()`, we are making the exact same
   call to the `fly()` method, which is also repetitive code that provides no
   additional value

The alternative is to take advantage of the interfaces and declare our bird
object to be of the interface we're interested. It can then be initialized to
the proper actual type based on the player's activity, but used generically in
the rest of the program.

```java
Bird playerBird;

// code to retrieve the player's specific kind of bird

playerBird.fly();
```

In this case, the code to make the player's bird fly can be run on the
`playerBird` object, regardless of its actual type at runtime. We have converted
7 lines of code to 2 lines of code, and more importantly, we've reduced code
duplication, and even more important than that, we have now made it irrelevant
for our code to need to know the type of bird the player has - all our code
needs to know is that the player has an object that "can fly" and then it "makes
it fly". The underlying details of the exact type of object and how that object
chooses to fly are not relevant.

Note that the code that retrieves the player's specific kind of bird must know
its type, which makes sense, since it essentially creates that object, but then
that type is abstracted for the code that uses it. To give a simplified version
of this mechanism, we can simply create an instance of one of the
implementations of `CanSwim`

```java
Bird playerBird;

playerBird = new Puffin();

playerBird.fly(); // this is the same code whether the bird if a pigeon or a puffin
```

Or we can create an instance of a different implementation of the `CanSwim`
interface, and the rest of the code does not have to change:

```java
Bird playerBird;

playerBird = new Pigeon();

playerBird.fly(); // this is the same code whether the bird if a pigeon or a puffin
```
