# Object Oriented In Compilation

- Polymorphism can be difficult to implement

## Object Layout In Memory

- Need to ask
  - How are objects laid out in memory?
  - How are methods invoked?
- We solve these problems with the following ideas:
  - Objects are laid out in contiguous memory, with pointer pointing to that memory giving us access to the memory
  - Each instance variable is at the same place in the contiguous memory representing an object (i.e. at a fixed offset known at compile time)
  - Subclass instance variables are added 'from below'

```java
class A {
  int a = 0;
}

class B extends {
  int b = 999;
}
```

- Becomes e.g like this in memory:

```text
instance of A:

32 : Header
36 : a = 0

instance of B:

120: Header
124: a = 0
128: b = 999
```

### A Subtle Issue

- In Java you can do this:

```java
class A { int a = 0; }
class B extends A { int a = 1; }

class Main {
  public static void main(String[] args) {
    A a = new A();
    B a = new B();
    A ab = new B();

    System.out.println("a: " + a.a);
    System.out.println("b: " + b.a);
    System.out.println("ab: " + ab.a);
  }
}

/*
prints out:
a: 0
b: 1
ab: 0
*/
```

- The compiler will look for the nearest definition of a variable above the current level in the inheritance tree
- Member variables are attached at the bottom

## Methods

- Need to deal with the following two questions:
  - How do we generate code for the method body?
  - How/where to store method code to ensure dynamic dispatch works?
- We put it in the header

```text
Header
  a
  b

becomes:

Other header data
 Code for f_a()
 Code for f_b()
       a
       b
```

- Way too much duplication
- You can also store pointers to the method bodies where there would be a method table where the pointers go to
- If these were stored in the header, there would still be too much duplication
- Solution to this:
  - Only use 1 pointer that goes to the method table where all the pointers for the methods from a class live, then they point to the code for a method
  - This leads to more of a delay when finding the methods
    - Just in time compilers can resolve these issues
- Same thing for method variables and inheritance happens with methods as well
