---
layout: post
title: Method Overloading
description: Java Method Overloading
headline: Method Overloading
categories:
  - Softwaredevelopment

tags: 
  - Java
  - Tricky Questions
  - Core Java
  - Collections
  - MultiThreading 
comments: true
mathjax: null
featured: true 
published: true
---

 In Java two methods are said to be overloaded when they have same method name but they differ in method signature. Method signature doesnt include access modifier , return type and Exception thrown.

 Overloaded methods vary in number of arguments and data type of the arguments. Generally changing the order of argument creates a overloaded method but it is not recommended.

 Changing only the return type will not create overloaded method but overloaded methods can have different return types. For example 

```java
 public Class A {

   public void show(int i){

   }

   public String show(String i){

   }

 }

```


 Above methods are said to be overloaded but following example will throw duplicate method error 

```java

 public Class A {

   public void show(int i){

   }

   public String show(int i){

   }

 }
``` 

### Method selection 

Method that is most specific is selected at compile time based on number of arguments & type of arguments. When number of arguments are same then type of the arguments is used in general to select the method that will be executed. Consider following examples

 When subclass types are used as arguments overloaded methods can be confusing for example 

```java
 	class A
	{
	     
	}
	 
	class B extends A
	{
	     
	}
	 
	class C extends B
	
	     
	}
	 
	public class ExampleOne 
	{
	     private void overloadedMethod(A a)
	    {
	        System.out.println("ONE");
	    }
	     
	    protected B overloadedMethod(B b)
	    {
	        System.out.println("TWO");
	        return b;
	    }
	     
	    public static void overloadedMethod(Object obj) throws RuntimeException 
	    {
	        System.out.println("THREE");
	    }
	     
	    public static void main(String[] args)
	    {
	    	ExampleOne one = new ExampleOne();
	        C c = new C();
	         
	        one.overloadedMethod(c);
	    }
	}
``` 

 This is a legal invocation of the overloaded method and in this method with signature that is close to the formal argument or which is more specific gets called. In this case overloaded method with B is called. Above example explains several nuances of method overloading.

 As you can see one of the overloaded method has static keyword which shows static method can be overloaded or static keyword doesnt have much significance in terms of overloaded methods. 

 Another notable difference is with the return types. Second overloaded method returns an object of Type B where as other overloaded method doesn't return any value. Similar to return type Exception thrown and access modifiers will not impact the overloaded methods.

 These methods are still considered overloaded. Another example with slight modification to method invocation.

```java
public static void main(String[] args)
	    {
	    	ExampleOne one = new ExampleOne();
	        C c = new C();
	         
	        one.overloadedMethod(null);
	    }

```
In this case null is passed instead of an object and Two will be the output still. Between A , B & Object, Object is superclass of A & B and between A & B - B is more specific than A so method with B type is choosen.


One more interesting case would be terinary operator

```java
public static void main(String[] args)
	    {
	    	ExampleOne one = new ExampleOne();
	        C c = new C();
	        one.overloadedMethod((10%2==0)?null:new Object());
	    }
```

This will print output as "Three" since Java doc says 

> The type of a conditional expression is determined as follows:
> If one of the second and third operands is of the null type and the type of the other is a reference type, then the type of the conditional expression is that reference type.


There can be cases where both type are at same level and Java can't decide which one is more specifc than other in that case Compiler throws ambiguity error for example

```java
package com.ibm.epi.overloading;

public class ExampleThree {
	
	public void show(Integer i){
		
	}
	
	public void show(char[] c){
		
	}
	
	public void show(Object c){
		
	}
	
	public static void main(String args[]){
		ExampleThree three = new ExampleThree();
		three.show(null);
	}

}
```

Lastly final methods can be overloaded and it is absolutely legal to overload them except that it is not recommended since it will prevent the method from being overridden.

Let me know if there is anything i have missed. Hope this is helpful to someone.