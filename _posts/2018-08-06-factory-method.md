---
layout: post
title:  "Factory Method"
date:   2018-08-06
categories: csharp creationpatterns designpatterns
---
So, in order to test posting of GitHub Pages, I have imported my blog post from my Weebly blog (I'm changing to GitHub Pages): 

So I thought I would start off this blog by talking about the design patterns described by the well-known “Gang of Four.”  The first patterns we’ll talk about is the Factory Method (if you didn’t read the title of this post). This pattern is easier to understand than the Abstract Factory, so we are starting with this Factory pattern first.
​
The Factory Method patterns involves many different components including an Interface for factories and an interface for products.  This probably didn’t make sense, so let’s break it down. If you don’t know what an interface you should look it up since I haven’t wrote about it yet, but put simply: interfaces define a contract that implementing classes must follow.

The main purpose of the Factory Method is to move the initialization responsibility of an object from the user to another Factory object.  Instead of explicitly calling a class's constructor, the factory will instantiate the class for you (class instantiation is deferred to another class).

These are the different “players” involved in the Abstract Factory:
- Interface: IFactory
  - The interface that all factories must implement
- Class: Factory
  - A concrete implementation of a Factory
- Interface: IProduct
  - The interface that all products must implement
- Class: Product
  - Concrete implementation for the product your factory produces​

Here's an implementation of the pattern in C#:

```
// Interfaces

interface IFactory {
	IProduct Create();
}

interface IProduct {
	string Name { get; }
	void Hello();
}
```

```
// Concrete Implementations

class Factory : IFactory {
	public IProduct Create() {
		return new Product();
    }
}

class Product : IProduct {
	public string Hello { get; private set; }
	
	public Product() {
		Hello = “Hello, I am a product.”;
    }

    public void Hello() {
	    Console.WriteLine(Hello);
    }
}
```

The user should only be able to interact with the classes as if they are an IFactory or IProduct; they only have access to what they need and don’t know about the underlying implementation.

The user invokes the factory’s create method to get an object that implements IProduct.  The user can not change the Name property of the products, but can read it and call the Name method of the product.

This is how it would look like in a main method:

```
public static void Main() {
	IFactory factory = new Factory();
	
	IProduct product = factory.Create();
	
	product.Hello();
}
```

```
// Expected Output
Hello, I am a product.
```

There are other ways to extend this pattern, such as having multiple product types from a single factory or 1 product per factory.  You could implement this with enums or multiple create methods for each product. Multiple methods is probably better because your if/switch statement will become rather long/unruly the more product enums you have.  The following implementation will probably be very similar to the Abstract Factory pattern, but we will go over that pattern later.  Let's see some examples of these approaches.

### Multiple Products in a Single Factory - Enum Approach

One way of implementing multiple products is by having a single Create method with an enum parameter.  So let's extend our previous, basic example:

```
// Enum and Concrete Product Implementations

public enum ProductType { ProductA, ProductB }

public ProductA : IProduct {
    public string Hello { get; private set; }
	
	public ProductA() {
		Hello = “Hello, I am product A.”;
    }

    public void Hello() {
	    Console.WriteLine(Hello);
    }
}

public ProductB : IProduct {
    public string Hello { get; private set; }
	
	public ProductA() {
		Hello = “Hello, I am product B.”;
    }

    public void Hello() {
	    Console.WriteLine(Hello);
    }
}
```

```
// Concrete Factory Implementation and Interface Update

interface IFactory {
	IProduct Create(ProductType type);
}

class Factory : IFactory {
	public IProduct Create(ProductType type) {
		switch (type) {
		    case ProductType.ProductA:
		        return new ProductA();
		    case ProductType.ProductB:
		        return new ProductB();
		}
		return null;
    }
}
```

The user supplies an enum value to the factory's Create method to receive a product in return.  As before, the user can only interact with the methods defined in the interfaces.

The main method will remain similar along with the expected output:

```
public static void Main() {
	IFactory factory = new Factory();
	
	IProduct productA = factory.Create(ProductType.ProductA);
	IProduct productB = factory.Create(ProductType.ProductB)
	
	productA.Hello();
	productB.Hello();
}
```

```
// Expected Output
Hello, I am product A.
Hello, I am product B.
```

### Multiple Products in a Single Factory - Method Approach

The difference from this approach and the previous one is using multiple methods instead of a single method.  The output is the same.

```
// Updated Interface and Concrete Factory Implementation

interface IFactory {
	IProduct CreateProductA();
	IProduct CreateProductB();
}

class Factory : IFactory {
    //These are examples of Expression body definitions.
    //This is what a one-liner function can be simplified to.
    //All that's happening here is that a new object is being returned.
	public IProduct CreateProductA() => new ProductA();
	public IProduct CreateProductB() => new ProductB();
}
```

```
public static void Main() {
	IFactory factory = new Factory();
	
	IProduct productA = factory.CreateProductA();
	IProduct productB = factory.CreateProductB();
	
	productA.Hello();
	productB.Hello();
}
```

### One Factory for Each Product

The last way we will describe here is using a factory for each product.  Each factory will have a create method that returns 1 implementation of IProduct.  This will require extra code as shown below, but the output is the same.  IFactory will be the original version consisting of a single Create method with no parameters.

```
// Two Concrete Factory Implementations

class FactoryA : IFactory {
	public IProduct Create() {
		return new ProductA();
    }
}

class FactoryB : IFactory {
	public IProduct Create() {
		return new ProductB();
    }
}
```

```
public static void Main() {
	IFactory factoryA = new FactoryA();
	IFactory factoryB = new FactoryB();
	
	IProduct productA = factoryA.Create();
	IProduct productB = factoryB.Create();
	
	productA.Hello();
	productB.Hello();
}
```

Note that in these examples we were all explicitly choosing what version of Product we wanted, but that doesn't have to be the case all the time.  A factory could randomly give the user 1 product from a selection of 10, for example.