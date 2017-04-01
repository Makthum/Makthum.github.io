---
layout: post
title: Immutable Integers
description: Immutable Integers
headline: Immutable Integers
categories:
  - Softwaredevelopment

tags: 
  - Java
  - Immutable Objects
  - Integers
  - Collections
  - Autoboxing 
comments: true
mathjax: null
featured: true 
published: true
---

I have been using Integer for almost 5 years now and it dint fail to surprise me even after 5 years. I have mostly used Integer objects as keys and wrapper objects until recently i found out Integer objects are immutable similar to String objects. Strings are immutable this one is classic and you might have heard many say it.

### Integers are immutable

This is like a revealation to me. Next moment these questions struck my mind 

1. so how do you increment value contained within Integer object 
2. is there a method or way to set or modify the value ?(might sound redundant since I already mentioned Integers are mutable)

Answer to the above questions is No. Yes that is correct you cannot modify or set value of the Integer object once it is initialized. It seemed like a bad design to me at the begining becausing i was wondering will it not create thousand of Integer objects say if you run a simple of for loop with iteration count of 100000. I dont't feel good about these many objects as there is considerable overhead involved in creating a object comparated to primitive data type.

What initially occured as bad design to me, now sounds like a good design when i suddenly realized Integers are wrapper classes and they need to be used only when primitives cannot be used. Always keep this mind when choosing between Integer and primitive int. And then there is also boxing & unboxing overhead involved when using int & Integer interchangeably.

#### Can you use Increment operator with Integers ?

This again raises a valid question so how in the world is increment operator working with Integer objects. Increment operator is supported starting from Java 7 and before that it is not supported 

``` java

public class Example {
	public static void main(String args[]){
		Integer i = new Integer(4);
		System.out.println(i++);
	}
}

```

Output 
``` java
5
```

If Integers are immutable then why & how increment operator is working with Integers. There are couple of concepts involved here What is said in the begining about Integers still holds true but in this scenario behind the scenes 

1. Integer is unboxed to primitive int 
2. primitive int is incremented 
3. New Integer is created or new value is boxed again and returned.

Now, Lets try another example 

``` java
public class Example {
	public static void main(String args[]) {
		Integer val = 10;
		Integer val1 = val;
		System.out.println("Integer value :" + val.intValue());
		System.out.println("Integer value :" + ++val);
		System.out.println("Integer value :" + --val);
		System.out.println("Integer value :" + val1);
		System.out.println(val1 == val);

	}
}
```

With above explanation any number of increment or decrement will create new object so object reference should be different and last print statement shoud print false. Surprisinly that is not the case and program output will be 

``` java 
Integer value :10
Integer value :11
Integer value :10
Integer value :10
true
```
 Now lets look another example 

``` java
 	public static void main(String args[]) {
		Integer val = new Integer(10);
		Integer val1 = val;
		System.out.println("Integer value :" + val.intValue());
		System.out.println("Integer value :" + ++val);
		System.out.println("Integer value :" + --val);
		System.out.println("Integer value :" + val1);
		System.out.println(val1 == val);

	}
}

```

output is 

``` java 
Integer value :10
Integer value :11
Integer value :10
Integer value :10
false
``` 

This is because Integer objects are cached only when created from primitives and not using constructor. Looking at the source code of Integer class can help you understand better 

``` java 
public static Integer valueOf(int i) {
        assert IntegerCache.high >= 127;
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
    }
```
