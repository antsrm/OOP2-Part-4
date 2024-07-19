
## Exercise 4.1

a.)

'Zipper' class construct is an abstract class.

'Handler' class construct is an abstract static nested class.

'TestZipper' is a concrete class, a subclass of 'Zipper'.

There is an anonymous inner class, a subclass of 'Handler' that is inside the 'createHandler' method of 'TestZipper'.

'Exercise1' is a concrete class.

b.)

For 'Zipper', abstract class is used because it contains both implemented methods like 'close', 'unzip', 'run', and also abstract methods 'createHandler' that need to be defined by subclasses. This way the code can be reused and a contract is enforced for subclasses. 'Zipper' implements 'AutoCloseable' so that the temporary directory is cleaned as it should be.

For 'Handler', abstract static nested class is used because it is to be used as blueprint for file handlers that operate within 'Zipper', but don't need access to the outer class. Because 'Handler' is static, it can be instantiated without reference to instance of the enclosing class. Abstract imposes on subclasses the need to implement the 'handle' method.

'TestZipper' concrete class extends 'Zipper' and gives specific implementations for abstract methods. This way unzipping and file handling can be tested.

The anonymous inner class as subclass of 'Handler' is used to provide one-off implementation of 'Handler' inside the 'createHandler' method of 'TestZipper'. This works fine, as it is specific to the current context without need for separate named class. This class encapsulates the specific file handling logic in a way convenient to the context.

'Exercise1' concrete class demonstrates use of 'TestZipper'. It is the access point to the app. It has the main logic for running the program and handling exceptions.

c.)

The concept of temporary directory lifespan relates to the 'AutoCloseable' interface implemented by 'Zipper' class. The temporary directory is created as instance of 'Zipper' is instantiated and it is intended to exist for lifespan of the 'Zipper' object. When the object is closed, the 'close' metod is called, and it deletes the temporary directory and all of it's contents. In this way, the temporary files don't exist longer than would be necessary.

d.)

'Protected' restricts the visibility of 'Handler' class to the package and 'Zipper' subclasses. So, 'Handler' is meant only to be used within the 'Zipper' hierarchy and related classes.

'Abstract' means that 'Handler' is a base class to be extended into subclasses. It contains at least one abstract method that must be implemented by subclasses.

'Static' is fine here, because 'Handler' doesn't need an instance of 'Zipper' class to be instantiated or used. So, 'Handler' can be instantiated and used independent of an instance of 'Zipper'.

In this way, 'Handler' can be part of the 'Zipper' class structure, with enforced relationship, but still be flexible enough to be used independently where needed without an instance of 'Zipper'.
