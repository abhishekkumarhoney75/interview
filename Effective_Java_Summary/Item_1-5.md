# Item 1 to 5 Summary

## Item 1: Consider static factory methods instead of constructors

### A class can provide a public static factory method, which is simply a static method that returns an instance of the class. Here’s a simple example from Boolean (the boxed primitive class for boolean). This method translates a boolean primitive value into a Boolean object reference:

```java
public static Boolean valueOf(boolean b) {
       return b ? Boolean.TRUE : Boolean.FALSE;
}
```

Providing a static factory method instead of a public constructor has both advantages and disadvantages.

#### Advantages

* One advantage of static factory methods is that, unlike constructors, **they have names.**
* A second advantage of static factory methods is that, unlike constructors, **they are not required to create a new object each time they’re invoked.**
* A third advantage of static factory methods is that, unlike constructors, **they can return an object of any subtype of their return type.**
This gives you great flexibility in choosing the class of the returned object.
* A fourth advantage of static factories is that the **class of the returned object can vary from call to call as a function of the input parameters. Any sub- type of the declared return type is permissible.** The class of the returned object can also vary from release to release.
* A fifth advantage of static factories is that **the class of the returned object need not exist when the class containing the method is written.** 

#### Disadvantages

* The main limitation of providing only static factory methods is that **classes without public or protected constructors cannot be subclassed.**
* A second shortcoming of static factory methods is that **they are hard for programmers to find.**

#### The **Javadoc** tool may someday draw attention to static factory methods. In the meantime, you can reduce this problem by drawing attention to static factories in class or interface documentation and by adhering to common naming conventions.

* **from**—A type-conversion method that takes a single parameter and returns a corresponding instance of this type, for example:
     Date d = Date.from(instant);
* **of**—An aggregation method that takes multiple parameters and returns an in- stance of this type that incorporates them, for example:
     Set<Rank> faceCards = EnumSet.of(JACK, QUEEN, KING);
* **valueOf**—A more verbose alternative to from and of, for example:
     BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE);
* **instance** or **getInstance**—Returns an instance that is described by its pa- rameters (if any) but cannot be said to have the same value, for example:
     StackWalker luke = StackWalker.getInstance(options);
* **create** or **newInstance**—Like instance or getInstance, except that the
method guarantees that each call returns a new instance, for example:
     Object newArray = Array.newInstance(classObject, arrayLen);
* **getType**—Like getInstance, but used if the factory method is in a different
class. Type is the type of object returned by the factory method, for example: FileStore fs = Files.getFileStore(path);
* **newType**—Like newInstance, but used if the factory method is in a different class. Type is the type of object returned by the factory method, for example:
     BufferedReader br = Files.newBufferedReader(path);
* **type**—A concise alternative to getType and newType, for example:
     List<Complaint> litany = Collections.list(legacyLitany);

## Item 2: Consider a builder when faced with many constructor parameters.

### Static factories and constructors share a limitation: they do not scale well to large numbers of optional parameters.

Consider the case of a class representing the Nutrition Facts label that appears on packaged foods. These labels have a few required fields—serving size, servings per container, and calories per serving— and over twenty optional fields—total fat, saturated fat, trans fat, cholesterol, sodium, and so on.

```java

// Telescoping constructor pattern - does not scale well!
public class NutritionFacts {
     
private final int servingSize; 
private final int servings; 
private final int calories; private final int fat;
private final int sodium; 
private final int carbohydrate;

public NutritionFacts(int servingSize, int servings) {
    this(servingSize, servings, 0);
}
public NutritionFacts(int servingSize, int servings,
        int calories) {
    this(servingSize, servings, calories, 0);
}...


NutritionFacts cocaCola =
       new NutritionFacts(240, 8, 100, 0, 35, 27);

```

So lets see one more example.

``` java
// Server Side Code 
final class Student { 
  
    // final instance fields 
    private final int id; 
    private final String name; 
    private final String address; 
}
```



#### In short, the telescoping constructor pattern works, but it is hard to write client code when there are many parameters, and harder still to read it.


```java


// JavaBeans Pattern - allows inconsistency, mandates mutability
public class NutritionFacts {
// Parameters initialized to default values (if any) 
private int servingSize = -1;
private int servings = -1
private int calories = 0;
//Public Constructor...
// Setters...
NutritionFacts cocaCola = new NutritionFacts();
cocaCola.setServingSize(240);
cocaCola.setServings(8);
cocaCola.setCalories(100);
```

### JavaBean may be in an inconsistent state partway through its construction.

JavaBeans pattern pre- cludes the possibility of making a class immutable (Item 15), and requires added effort on the part of the programmer to ensure thread safety.

### Builder Pattern

``` java
// Java code to demonstrate Builder Pattern 
  
// Server Side Code 
final class Student { 
  
    // final instance fields 
    private final int id; 
    private final String name; 
    private final String address; 
  
    private Student(Builder builder) 
    { 
        this.id = builder.id; 
        this.name = builder.name; 
        this.address = builder.address; 
    } 
  
    // Static class Builder 
    public static class Builder { 
  
        /// instance fields 
        private int id; 
        private String name; 
        private String address; 
  
        public static Builder newInstance() 
        { 
            return new Builder(); 
        } 
  
        private Builder() {} 
  
        // Setter methods 
        public Builder setId(int id) 
        { 
            this.id = id; 
            return this; 
        } 
        public Builder setName(String name) 
        { 
            this.name = name; 
            return this; 
        } 
        public Builder setAddress(String address) 
        { 
            this.address = address; 
            return this; 
        } 
  
        // build method to deal with outer class 
        // to return outer instance 
        public Student build() 
        { 
            return new Student(this); 
        } 
    } 
  
    @Override
    public String toString() 
    { 
        return "id = " + this.id + ", name = " + this.name +  
                               ", address = " + this.address; 
    } 
} 
  
// Client Side Code 
class StudentReceiver { 
  
    // volatile student instance to ensure visibility 
    // of shared reference to immutable objects 
    private volatile Student student; 
  
    public StudentReceiver() 
    { 
  
        Thread t1 = new Thread(new Runnable() { 
            @Override
            public void run() 
            { 
                student = Student.Builder.newInstance() 
                              .setId(1) 
                              .setName("Ram") 
                              .setAddress("Noida") 
                              .build(); 
            } 
        }); 
  
        Thread t2 = new Thread(new Runnable() { 
            @Override
            public void run() 
            { 
                student = Student.Builder.newInstance() 
                              .setId(2) 
                              .setName("Shyam") 
                              .setAddress("Delhi") 
                              .build(); 
            } 
        }); 
  
        t1.start(); 
        t2.start(); 
    } 
  
    public Student getStudent() 
    { 
        return student; 
    } 
} 
  
// Driver class 
public class BuilderDemo { 
    public static void main(String args[]) 
    { 
        StudentReceiver sr = new StudentReceiver(); 
        System.out.println(sr.getStudent()); 
    } 
} 

```
This client code is easy to write and, more importantly, to read. The Builder pat- tern simulates named optional parameters as found in Ada and Python.

#### In summary, the Builder pattern is a good choice when designing classes whose constructors or static factories would have more than a handful of parameters, especially if most of those parameters are optional. Client code is much easier to read and write with builders than with the traditional telescoping constructor pattern, and builders are much safer than JavaBeans.


























