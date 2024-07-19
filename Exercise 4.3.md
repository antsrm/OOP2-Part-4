
## Exercise 4.3

a.)

Polymorphism and interfaces are used in both 'challenge1' and 'challenge2'. The latter one especially displays Java generics and type bounds.

'challenge2' uses generic type parameter 'X' with bounds. Type 'X' is constrained to be of any type that implements interfaces 'Winged' and 'Bipedal' both. So, 'X' can be of any class so long as it meets both interface requirements. This is what type bounds is about.

Type bounds are used with generics to indicate that the type parameter must meet certain conditions. Their use is best in a situation where a method operates on an object that needs to implement many interfaces or extend a class. Type bounds thus increase flexibility and reusability, while maintaining type safety.

b.)

The differences between the two are a matter of flexibility. Using generics with type bounds makes 'challenge2' more flexible and reusable:

'challenge1' can accept only instances of 'Bird' class or it's sub-classes. This limits it's reusability.


'challenge2' can accept any type that implements both 'Winged' and 'Bipedal' interfaces, instead of only 'Bird' and it's sub-classes. This makes it more reusable / flexible.

c.)

Let us introduce a new class 'Robot' which implements both 'Winged' and 'Bipedal' interfaces. This fictional robot has wings and two legs. It can fly and walk:

```Java
interface Winged {
    default void fly() {
        System.out.println(this + " flies!");
    }
}

interface Bipedal {
    default void walk() {
        System.out.println(this + " walks!");
    }
}

abstract class Bird implements Winged, Bipedal {
    
}

class Crow extends Bird {
    private static int idx = 0;
    private int id = ++idx;

    @Override
    public String toString() {
        return "Varis " + id;
    }
}

class Robot implements Winged, Bipedal {
    private static int idx = 0;
    private int id = ++idx;

    @Override
    public String toString() {
        return "Robot " + id;
    }
}

public class Exercise3 {
    public Exercise3() {
        System.out.println("Exercise 3");

        challenge1(new Crow());
        challenge2(new Crow());

        // Can't call challenge1 with a Robot instance since Robot is not a Bird

        // challenge1(new Robot()); // This woould make a compile-time error

        // Can call challenge2 with a Robot instance since Robot implements Winged and Bipedal
        challenge2(new Robot());
    }

    void challenge1(Bird b) {
        System.out.println("In this challenge, we fly and then we walk!");

        b.fly();
        b.walk();
    }

    <X extends Winged & Bipedal> void challenge2(X b) {
        System.out.println("In this challenge, we fly and then we walk!");

        b.fly();
        b.walk();
    }

    public static void main(String[] args) {
        new Exercise3();
    }
}
```
The new class 'Robot' was introduced: it implements both 'Winged' and 'Bipedal' interfaces.

'challenge1' can't accept 'Robot' because this is not a subclass of 'Bird'.

However, 'challenge2' can accept 'Robot', since it accepts any object that implements both 'Winged' and 'Bipedal' interfaces.

In this example, it is demonstrated that 'challenge2' is more flexible, enabling the same behaviors to be applied to a greater range of objects. This demonstrates clearly the advantage of using generics with type bounds over a method restricted to using a specific class hierarchy. The method 'challenge2' is reusable for future classes that may implement the required interfaces, thus making it more adaptable to changes and extensions.
