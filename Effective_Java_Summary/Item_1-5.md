# Item 1 to 5 Summary


## Item 1: Consider static factory methods instead of constructors

#### A class can provide a public static factory method, which is simply a static method that returns an instance of the class. Here’s a simple example from Boolean (the boxed primitive class for boolean). This method translates a boolean primitive value into a Boolean object reference:


```java
public static Boolean valueOf(boolean b) {
       return b ? Boolean.TRUE : Boolean.FALSE;
}
```

Providing a static factory method instead of a public constructor has both advantages and disadvantages.

Advantages

* One advantage of static factory methods is that, unlike constructors, they have names.
* A second advantage of static factory methods is that, unlike constructors, they are not required to create a new object each time they’re invoked.
* A third advantage of static factory methods is that, unlike constructors, they can return an object of any subtype of their return type. 
This gives you great flexibility in choosing the class of the returned object.
* A fourth advantage of static factories is that the class of the returned object can vary from call to call as a function of the input parameters. Any sub- type of the declared return type is permissible. The class of the returned object can also vary from release to release.
*A fifth advantage of static factories is that the class of the returned object need not exist when the class containing the method is written. Such flexible static factory methods form the basis of service provider frameworks, like the Java Database Connectivity API (JDBC). A service provider framework is a system in which providers implement a service, and the system makes the implementations available to clients, decoupling the clients from the implementations.

Disadvantages

* The main limitation of providing only static factory methods is that classes without public or protected constructors cannot be subclassed.
* A second shortcoming of static factory methods is that they are hard for programmers to find.

The Javadoc tool may someday draw attention to static factory methods. In the meantime, you can reduce this problem by drawing attention to static factories in class or interface documentation and by adhering to common naming conventions.

** from—A type-conversion method that takes a single parameter and returns a corresponding instance of this type, for example:
     Date d = Date.from(instant);
** of—An aggregation method that takes multiple parameters and returns an in- stance of this type that incorporates them, for example:
     Set<Rank> faceCards = EnumSet.of(JACK, QUEEN, KING);
** valueOf—A more verbose alternative to from and of, for example:
     BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);
** instance or getInstance—Returns an instance that is described by its pa- rameters (if any) but cannot be said to have the same value, for example:
     StackWalker luke = StackWalker.getInstance(options);
** create or newInstance—Like instance or getInstance, except that the
method guarantees that each call returns a new instance, for example:
     Object newArray = Array.newInstance(classObject, arrayLen);
** getType—Like getInstance, but used if the factory method is in a different
class. Type is the type of object returned by the factory method, for example: FileStore fs = Files.getFileStore(path);
** newType—Like newInstance, but used if the factory method is in a different class. Type is the type of object returned by the factory method, for example:
     BufferedReader br = Files.newBufferedReader(path);
** type—A concise alternative to getType and newType, for example:
     List<Complaint> litany = Collections.list(legacyLitany);