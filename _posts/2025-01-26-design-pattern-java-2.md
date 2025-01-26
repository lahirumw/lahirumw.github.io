---
layout: post
title: Mastering Design Patterns with Java - II (Behavioral Design Patterns)
subtitle: 
cover-img: 
thumbnail-img: 
share-img: /assets/img/path.jpg
tags: [Design Patterns, Java, Java Design Pattern, Software Design Patterns, Solid Principles In Java]
author: keylearn
---

Let’s continue Mastering design patterns. Please read [Mastering design patterns](https://lahirumw.github.io/2025-01-24-design-pattern-java-1/) if you haven’t already. For those who have read it, proceed with the Behavioral Pattern section.

## Behavioral Design Pattern

Behavioral design patterns are a type of design pattern that focuses on the interactions and behaviors of objects, rather than their structures. These patterns describe how objects communicate with each other, how they respond to changes, and how they manage their dependencies.

Behavioral design patterns are used to define the ways in which objects interact with each other, and how they respond to changes in their environment. They provide a way to encapsulate complex behaviors and make them reusable, flexible, and maintainable.

Some common examples of behavioral design patterns include:

**Observer Pattern**

The Observer Pattern is a behavioral design pattern that defines a one-to-many dependency between objects, so that when one object changes, all its dependents are notified. This pattern is used to notify a set of objects about changes to another object, without making them tightly coupled.

Example

Imagine a scenario where a weather station is observed by various smart devices. The weather station maintains a list of registered devices. When there’s a change in weather conditions, the weather station notifies all devices about the update. Each device, acting as a concrete observer, interprets and displays the information in its own way.

Components of Observer Design Pattern

1. Subject: maintains a list of observers (subscribers or listeners). It provides methods to register and unregister observers dynamically and defines a method to notify observers of changes in its state.

2. Observer: defines an interface with an update method that concrete observers must implement and ensures a common or consistent way for concrete observers to receive updates from the subject.

3. ConcreteSubject: specific implementations of the subject. They hold the actual state or data that observers want to track. When this state changes, concrete subjects notify their observers.

4. ConcreteObserver: implements the observer interface. They register with a concrete subject and react when notified of a state change.

Let’s consider a scenario where you have a weather monitoring system. Different parts of your application need to be updated when the weather conditions change.

~~~
// Subject interface
public interface Subject {
    void registerObserver(Observer observer);
    void removeObserver(Observer observer);
    void notifyObservers();
}

// Observer interface
public interface Observer {
    void update(String message);
}

// ConcreteSubject
public class WeatherStation implements Subject {
    private List<Observer> observers;
    private String weather;

    public WeatherStation() {
        observers = new ArrayList<>();
    }

    @Override
    public void registerObserver(Observer observer) {
        observers.add(observer);
    }

    @Override
    public void removeObserver(Observer observer) {
        observers.remove(observer);
    }

    @Override
    public void notifyObservers() {
        for (Observer observer : observers) {
            observer.update(weather);
        }
    }

    public void setWeather(String weather) {
        this.weather = weather;
        notifyObservers();
    }
}

// ConcreteObserver
public class WeatherApp implements Observer {
    private String weather;

    @Override
    public void update(String message) {
        weather = message;
        System.out.println("Weather App: " + weather);
    }
}

// Usage
public class Main {
    public static void main(String[] args) {
        WeatherStation weatherStation = new WeatherStation();
        WeatherApp weatherApp = new WeatherApp();

        weatherStation.registerObserver(weatherApp);
        weatherStation.setWeather("Sunny");
        weatherStation.setWeather("Rainy");
    }
}
~~~

When to use

1. Use the Observer pattern when there is a one-to-many relationship between objects, and changes in one object should notify multiple dependent objects.

2. This is particularly useful when changes in one object need to propagate to several other objects without making them tightly coupled.

3. Decoupling: Use the Observer pattern to achieve loose coupling between objects.

**Strategy Pattern**

The Strategy Pattern is a behavioral design pattern that defines a family of algorithms, encapsulates each one, and makes them interchangeable. This pattern allows the algorithm to vary independently from clients that use it.

Example

Imagine a scenario where you’re planning a trip and need to decide how to travel. You can choose from different transportation strategies, such as driving, flying, or taking the train. Each strategy has its own implementation, but they all share a common interface. You can switch between strategies without changing the overall trip planning process.

Components of Strategy Design Pattern

1. Strategy: defines an interface for a family of algorithms. It declares a method that concrete strategies must implement.

2. Concrete Strategy: implements the strategy interface. Each concrete strategy provides a specific implementation of the algorithm.

3. Context: uses the strategy interface to call the algorithm defined by a concrete strategy. It’s responsible for managing the lifetime of the strategy object.

Let’s consider a scenario where you have a payment processing system that needs to support different payment gateways, such as PayPal, Stripe, and Authorize.net

~~~
// Strategy interface
public interface PaymentStrategy {
    void pay(int amount);
}

// ConcreteStrategy
public class PayPalPaymentStrategy implements PaymentStrategy {
    @Override
    public void pay(int amount) {
        System.out.println("Paying $" + amount + " using PayPal");
    }
}

public class StripePaymentStrategy implements PaymentStrategy {
    @Override
    public void pay(int amount) {
        System.out.println("Paying $" + amount + " using Stripe");
    }
}

// Context
public class PaymentProcessor {
    private PaymentStrategy paymentStrategy;

    public PaymentProcessor(PaymentStrategy paymentStrategy) {
        this.paymentStrategy = paymentStrategy;
    }

    public void setPaymentStrategy(PaymentStrategy paymentStrategy) {
        this.paymentStrategy = paymentStrategy;
    }

    public void processPayment(int amount) {
        paymentStrategy.pay(amount);
    }
}

// Usage
public class Main {
    public static void main(String[] args) {
        PaymentProcessor paymentProcessor = new PaymentProcessor(new PayPalPaymentStrategy());
        paymentProcessor.processPayment(100);

        paymentProcessor.setPaymentStrategy(new StripePaymentStrategy());
        paymentProcessor.processPayment(200);
    }
}
~~~

When to use

1. Use the Strategy pattern when you need to define a family of algorithms, encapsulate each one, and make them interchangeable.

2. This pattern is beneficial when you need to decouple the algorithm from the client code that uses it.
3. It’s particularly useful when you need to support multiple algorithms or business rules that can change over time.

**Template Method Pattern**

The Template Method Pattern is a behavioral design pattern that defines the skeleton of an algorithm in the superclass but allows subclasses to customize specific steps of the algorithm without changing its structure.

Example

Imagine a scenario where you’re baking a cake. The overall process of baking a cake remains the same, but you can customize the recipe by using different ingredients or adding specific flavors. The Template Method pattern allows you to define the overall process (baking a cake) and let subclasses customize specific steps (choosing ingredients or adding flavors).

Components of Template Method Design Pattern

1. AbstractClass: defines the template method, which outlines the algorithm’s structure. It also declares abstract methods that subclasses must implement.

2. ConcreteClass: implements the abstract methods declared in the abstract class. It customizes specific steps of the algorithm.

Let’s consider a scenario where you have a game that needs to support different game modes, such as single-player and multi-player.

~~~
// AbstractClass
public abstract class Game {
    public final void play() {
        initialize();
        start();
        end();
    }

    protected abstract void initialize();
    protected abstract void start();
    protected abstract void end();
}

// ConcreteClass
public class SinglePlayerGame extends Game {
    @Override
    protected void initialize() {
        System.out.println("Initializing single-player game");
    }

    @Override
    protected void start() {
        System.out.println("Starting single-player game");
    }

    @Override
    protected void end() {
        System.out.println("Ending single-player game");
    }
}

public class MultiPlayerGame extends Game {
    @Override
    protected void initialize() {
        System.out.println("Initializing multi-player game");
    }

    @Override
    protected void start() {
        System.out.println("Starting multi-player game");
    }

    @Override
    protected void end() {
        System.out.println("Ending multi-player game");
    }
}

// Usage
public class Main {
    public static void main(String[] args) {
        Game singlePlayerGame = new SinglePlayerGame();
        singlePlayerGame.play();

        Game multiPlayerGame = new MultiPlayerGame();
        multiPlayerGame.play();
    }
}
~~~

When to use

1. Use the Template Method pattern when you need to provide a way for subclasses to customize specific steps of an algorithm without changing its overall structure.

2. This pattern is beneficial when you have a common algorithm that needs to be implemented in different ways by subclasses.

## Conclusion

Design patterns are not just about solving a specific problem; they’re about creating a shared language and set of principles that can help you communicate more effectively with other developers. By learning and applying design patterns, you can improve your coding skills, write more efficient code, and become a better software developer.

If you have any questions or need further clarification on any of the concepts discussed in this blog, feel free to ask!

Thanks for reading, and happy coding!

[Mastering Design Patterns with Java — I](https://lahirumw.github.io/2025-01-24-design-pattern-java-1/)
