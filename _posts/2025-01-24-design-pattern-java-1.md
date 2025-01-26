---
layout: post
title: Mastering Design Patterns with Java
subtitle: 
cover-img: 
thumbnail-img: 
share-img: /assets/img/path.jpg
tags: [Design Patterns, Java, Java Design Pattern, Software Design Patterns, Solid Principles In Java]
author: keylearn
---

Design patterns are reusable solutions to common problems that arise during software development. They provide a proven, standardized approach to solving a specific design problem, making your code more maintainable, flexible, and scalable. In this blog, we’ll delve into the world of Java design patterns, exploring their types, examples, and best practices.

Design patterns are templates designed to help you write code that is easy to understand, modify, and extend. They are based on the principles of object-oriented programming (OOP) and provide a way to solve common design problems. There are three main categories of design patterns: creational, structural, and behavioral.

Creational Design Patterns: These patterns deal with object creation mechanisms, trying to create objects in a manner suitable to the situation.

1. Singleton Pattern: Ensures that only one instance of a class is created.
   
2. Factory Pattern: Provides a way to create objects without specifying the exact class of object.

3. Abstract Factory Pattern: Provides a way to create families of related objects.

Structural Design Patterns: These patterns deal with the composition of objects and classes, trying to create objects in a manner suitable to the situation.

1. Adapter Pattern: Converts the interface of a class into another interface clients expect.

2. Bridge Pattern: Separates an object’s abstraction from its implementation.

3. Composite Pattern: Allows clients to treat individual objects and compositions of objects uniformly.

Behavioral Design Patterns: These patterns deal with the interactions between objects, trying to define how objects interact with each other.

1. Observer Pattern: Defines a one-to-many dependency between objects so that when one object changes state, all its dependents are notified.

2. Strategy Pattern: Defines a family of algorithms, encapsulates each one, and makes them interchangeable.

3. Template Method Pattern: Provides a way to allow subclasses to override specific steps of an algorithm without changing its structure.

Best Practices for Using Java Design Patterns:

1. Use patterns judiciously: Don’t overuse design patterns; use them only when necessary.

2. Keep it simple: Prefer simple solutions over complex ones.

3. Test thoroughly: Ensure that your code is testable and tested.

Let’s elaborate each design pattern with example.

## Singleton Pattern

Ensures that only one instance of a class is created.

*Usage*: A logging system that logs messages to a single file. The logger class can be implemented as a singleton to ensure that only one instance is created.

~~~
public class Logger {
    private static Logger instance;
    private Logger() {}
    public static Logger getInstance() {
        if (instance == null) {
            instance = new Logger();
        }
        return instance;
    }
    public void log(String message) {
        // log the message to a file
    }
}
~~~

~~~
Logger logger = Logger.getInstance(); 
logger.log("Hello, world!");
~~~

## Factory Pattern

Provides a way to create objects without specifying the exact class of object.

*Usage*: A system that needs to create different types of database connections (e.g. MySQL, Oracle, PostgreSQL). A factory class can be used to create the connections without specifying the exact class.

~~~
public abstract class DatabaseConnection {
    public abstract void connect();
}

public class MySQLConnection extends DatabaseConnection {
    public void connect() {
        // connect to MySQL database
    }
}

public class OracleConnection extends DatabaseConnection {
    public void connect() {
        // connect to Oracle database
    }
}

public class DatabaseConnectionFactory {
    public static DatabaseConnection createConnection(String type) {
        if (type.equals("mysql")) {
            return new MySQLConnection();
        } else if (type.equals("oracle")) {
            return new OracleConnection();
        } else {
            throw new UnsupportedOperationException("Unsupported database type");
        }
    }
}
~~~

~~~
DatabaseConnection connection = DatabaseConnectionFactory.createConnection("mysql"); 
connection.connect();
~~~

## Abstract Factory Pattern

Provides a way to create families of related objects.

*Usage*: A system that needs to create different types of UI components (e.g. buttons, text fields, labels) for different operating systems (e.g. Windows, macOS, Linux). An abstract factory class can be used to create the components without specifying the exact class.

~~~
public abstract class UIComponent {
    public abstract void render();
}

public class Button extends UIComponent {
    public void render() {
        // render a button
    }
}

public class TextField extends UIComponent {
    public void render() {
        // render a text field
    }
}

public class Label extends UIComponent {
    public void render() {
        // render a label
    }
}

public abstract class UIComponentFactory {
    public abstract Button createButton();
    public abstract TextField createTextField();
    public abstract Label createLabel();
}

public class WindowsUIComponentFactory extends UIComponentFactory {
    public Button createButton() {
        return new WindowsButton();
    }
    public TextField createTextField() {
        return new WindowsTextField();
    }
    public Label createLabel() {
        return new WindowsLabel();
    }
}

public class MacUIComponentFactory extends UIComponentFactory {
    public Button createButton() {
        return new MacButton();
    }
    public TextField createTextField() {
        return new MacTextField();
    }
    public Label createLabel() {
        return new MacLabel();
    }
}
~~~

~~~
UIComponentFactory factory = new WindowsUIComponentFactory(); 
Button button = factory.createButton(); 
button.render();
~~~

## Adapter Pattern

Allows two incompatible objects to work together by converting the interface of one object into an interface expected by the clients.

*Problem*: Suppose we have a Turkey interface and a Duck interface, and we want to use a Turkey object in a method that expects a Duck object.

~~~
// Target interface
interface Duck {
    void quack();
    void fly();
}

// Adaptee interface
interface Turkey {
    void gobble();
    void fly();
}

// Concrete implementation of a Duck
class MallardDuck implements Duck {
    @Override
    public void quack() {
        System.out.println("Quack");
    }

    @Override
    public void fly() {
        System.out.println("I'm flying");
    }
}

// Concrete implementation of a Turkey
class WildTurkey implements Turkey {
    @Override
    public void gobble() {
        System.out.println("Gobble gobble");
    }

    @Override
    public void fly() {
        System.out.println("I'm flying a short distance");
    }
}
~~~

Adaptor: To use a Turkey object in a method that expects a Duck object, we need to create an adaptor that implements the Duck interface and delegates the calls to a Turkey object.

~~~
// Adaptor that adapts a Turkey to a Duck
class TurkeyAdaptor implements Duck {
    private Turkey turkey;

    public TurkeyAdaptor(Turkey turkey) {
        this.turkey = turkey;
    }

    @Override
    public void quack() {
        turkey.gobble();
    }

    @Override
    public void fly() {
        for (int i = 0; i < 5; i++) {
            turkey.fly();
        }
    }
}
~~~

*Usage*: Now we can use a Turkey object in a method that expects a Duck object. In this example, the TurkeyAdaptor class adapts a Turkey object to a Duck object, allowing us to use a Turkey object in a method that expects a Duck object.

~~~
public class Main {
    public static void main(String[] args) {
        MallardDuck mallardDuck = new MallardDuck();
        WildTurkey wildTurkey = new WildTurkey();
        Duck turkeyAdaptor = new TurkeyAdaptor(wildTurkey);

        System.out.println("The Turkey says...");
        wildTurkey.gobble();
        wildTurkey.fly();

        System.out.println("\nThe Duck says...");
        testDuck(mallardDuck);

        System.out.println("\nThe TurkeyAdaptor says...");
        testDuck(turkeyAdaptor);
    }

    static void testDuck(Duck duck) {
        duck.quack();
        duck.fly();
    }
}
~~~

## Bridge design pattern

Structural design pattern that separates an object’s abstraction from its implementation, allowing them to vary independently.

*Problem*: Suppose we have a Shape interface and a DrawAPI interface, and we want to create different shapes (e.g., Circle, Rectangle) that can be drawn using different drawing APIs (e.g., DrawAPI1, DrawAPI2).

~~~
// Abstraction
interface Shape {
    void draw();
}

// Implementation
interface DrawAPI {
    void drawCircle(int radius);
    void drawRectangle(int width, int height);
}

// Concrete implementation of DrawAPI
class DrawAPI1 implements DrawAPI {
    @Override
    public void drawCircle(int radius) {
        System.out.println("Drawing a circle with radius " + radius + " using DrawAPI1");
    }

    @Override
    public void drawRectangle(int width, int height) {
        System.out.println("Drawing a rectangle with width " + width + " and height " + height + " using DrawAPI1");
    }
}

class DrawAPI2 implements DrawAPI {
    @Override
    public void drawCircle(int radius) {
        System.out.println("Drawing a circle with radius " + radius + " using DrawAPI2");
    }

    @Override
    public void drawRectangle(int width, int height) {
        System.out.println("Drawing a rectangle with width " + width + " and height " + height + " using DrawAPI2");
    }
}
~~~

Bridge: To separate the Shape abstraction from the DrawAPI implementation, we create a Shape abstract class that has a reference to a DrawAPI object.

~~~
// Refined abstraction
abstract class Shape {
    protected DrawAPI drawAPI;

    public Shape(DrawAPI drawAPI) {
        this.drawAPI = drawAPI;
    }

    public abstract void draw();
}

// Concrete implementation of Shape
class Circle extends Shape {
    private int radius;

    public Circle(int radius, DrawAPI drawAPI) {
        super(drawAPI);
        this.radius = radius;
    }

    @Override
    public void draw() {
        drawAPI.drawCircle(radius);
    }
}

class Rectangle extends Shape {
    private int width;
    private int height;

    public Rectangle(int width, int height, DrawAPI drawAPI) {
        super(drawAPI);
        this.width = width;
        this.height = height;
    }

    @Override
    public void draw() {
        drawAPI.drawRectangle(width, height);
    }
}
~~~

*Usage*: Now we can create different shapes that can be drawn using different drawing APIs.

~~~
public class Main {
    public static void main(String[] args) {
        DrawAPI drawAPI1 = new DrawAPI1();
        DrawAPI drawAPI2 = new DrawAPI2();

        Shape circle1 = new Circle(5, drawAPI1);
        Shape circle2 = new Circle(10, drawAPI2);
        Shape rectangle1 = new Rectangle(4, 6, drawAPI1);
        Shape rectangle2 = new Rectangle(8, 12, drawAPI2);

        circle1.draw();
        circle2.draw();
        rectangle1.draw();
        rectangle2.draw();
    }
}
~~~

In this example, the Shape abstract class acts as the bridge between the DrawAPI implementation and the concrete shape classes (Circle, Rectangle). This allows us to create different shapes that can be drawn using different drawing APIs, without having to change the shape classes.

## Composite design pattern

Structural design pattern that allows you to compose objects into a tree-like structure, where each node in the tree can be either a leaf node or a composite node.

*Problem*: Suppose we have a file system with files and directories. We want to be able to perform operations on both files and directories, such as calculating their size.

~~~
// Component
interface FileSystemComponent {
    void print();
    long getSize();
}

// Leaf
class File implements FileSystemComponent {
    private String name;
    private long size;

    public File(String name, long size) {
        this.name = name;
        this.size = size;
    }

    @Override
    public void print() {
        System.out.println("File: " + name + ", Size: " + size + " bytes");
    }

    @Override
    public long getSize() {
        return size;
    }
}

// Composite
class Directory implements FileSystemComponent {
    private String name;
    private List<FileSystemComponent> components = new ArrayList<>();

    public Directory(String name) {
        this.name = name;
    }

    public void add(FileSystemComponent component) {
        components.add(component);
    }

    public void remove(FileSystemComponent component) {
        components.remove(component);
    }

    @Override
    public void print() {
        System.out.println("Directory: " + name);
        for (FileSystemComponent component : components) {
            component.print();
        }
    }

    @Override
    public long getSize() {
        long size = 0;
        for (FileSystemComponent component : components) {
            size += component.getSize();
        }
        return size;
    }
}
~~~

*Usage*: Now we can create a file system with files and directories, and perform operations on them.

~~~
public class Main {
    public static void main(String[] args) {
        Directory root = new Directory("Root");
        Directory documents = new Directory("Documents");
        Directory pictures = new Directory("Pictures");

        root.add(documents);
        root.add(pictures);

        documents.add(new File("Report.docx", 1024));
        documents.add(new File("Presentation.pptx", 2048));

        pictures.add(new File("Photo1.jpg", 512));
        pictures.add(new File("Photo2.jpg", 1024));

        root.print();
        System.out.println("Total size: " + root.getSize() + " bytes");
    }
}
~~~
In this example, the FileSystemComponent interface acts as the component, the File class acts as the leaf node, and the Directory class acts as the composite node. The Directory class can contain both files and subdirectories, and can perform operations on them.

Conclusion: Java design patterns are essential for writing robust, maintainable, and scalable code. By understanding and applying these patterns, you can improve your coding skills and create software that is easier to maintain and extend. Remember to use patterns judiciously, keep it simple, follow the SOLID principles, and test thoroughly.

That’s a wrap for today! Stay tuned for our next installment, where we’ll dive into the world of Behavioral Design Patterns.

[Mastering Design Patterns with Java — II](https://lahirumw.github.io/2025-01-26-design-pattern-java-2/)
