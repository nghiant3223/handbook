# Examples

## Liskov Substitution Principle

- Violation Example

```java
public class Bird {
    public void fly(){}
}
public class Duck extends Bird {}
```

The duck can fly because it is a bird, but what about this:

```java
public class Ostrich extends Bird {}
```

Ostrich is a bird, but it can't fly, Ostrich class is a subtype of class Bird, but it shouldn't be able to use the fly method, that means we are breaking the LSP principle.

- Adhere Example

```java
public class Bird {}
public class FlyingBirds extends Bird {
    public void fly() {}
}
public class Duck extends FlyingBirds {}
public class Ostrich extends Bird {} 
```
