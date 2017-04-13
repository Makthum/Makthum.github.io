---
layout: post
title: Enum's in Java
description: Enum's in Java
headline: Enum's in Java
categories:
  - Softwaredevelopment

tags: 
  - Java
  - Enum
  - Singleton Pattern
  - Collections
  - Autoboxing 
comments: true
mathjax: null
featured: true 
published: true
---

Java Enums are special data types mainly used as set of predefined constants.Enum offers few perks inherently compared to other data types such as synchronized singleton, type safety and immutability. In this order we discuss some of the features of Java Enum data type 

### Enum Implementation

All Enum data types in java implicity extend java.lang.Enum class. This inheritance results in a restriction where Java Enum's cannot extend any other class or Enum since Java doesn't support multiple inheritance.

java.lang.Enum also implements comparable & Serializable interface this ensures all Enums can be serialized and natural ordering is based on their ordinal i.e order in which they are added.

``` java
/**
 * This is the common base class of all Java language enumeration types.
 *
 * More information about enums, including descriptions of the
 * implicitly declared methods synthesized by the compiler, can be
 * found in section 8.9 of
 * <cite>The Java&trade; Language Specification</cite>.
 *
 * <p> Note that when using an enumeration type as the type of a set
 * or as the type of the keys in a map, specialized and efficient
 * {@linkplain java.util.EnumSet set} and {@linkplain
 * java.util.EnumMap map} implementations are available.
 *
 * @param <E> The enum type subclass
 * @author  Josh Bloch
 * @author  Neal Gafter
 * @see     Class#getEnumConstants()
 * @see     java.util.EnumSet
 * @see     java.util.EnumMap
 * @since   1.5
 */
public abstract class Enum<E extends Enum<E>>
        implements Comparable<E>, Serializable {
    
```

Though it implements comparable interface compareTo method is final and it stops clients from overriding it. I believe this was intentionally made to preserve ordering across all Enums.


``` java 
/**
     * Compares this enum with the specified object for order.  Returns a
     * negative integer, zero, or a positive integer as this object is less
     * than, equal to, or greater than the specified object.
     *
     * Enum constants are only comparable to other enum constants of the
     * same enum type.  The natural order implemented by this
     * method is the order in which the constants are declared.
     */
    public final int compareTo(E o) {
        Enum other = (Enum)o;
        Enum self = this;
        if (self.getClass() != other.getClass() && // optimization
            self.getDeclaringClass() != other.getDeclaringClass())
            throw new ClassCastException();
        return self.ordinal - other.ordinal;
    }
```

### Enum's are type-safe

Enum of one type cannot be cast or assigned to Enum of another type. This is useful when Enum are part of method arguments as Enum are more restrictive & specific compared to other data types. All values of Enum are static & final by default hence it cannot changed once created.

Enum's defined within a class are static be default and can be accessed OuterClass.EnumType.variable
and Enum declared in its own class should not be marked static, abstract , final or protected. 


### Enum's are singleton 

Most popularly designed singletons involves using Enum's since Enum's are singletons by default. They cannot be created using new keyword that is the reason for singletons being created with Enums. Enums can have constructors without access modifiers. If no modifier is specified it is considered as private where as public or protected modifiers are not allowed.

Similar to any other class Enum's can have methods and it is also possible to declare abstract methods within a Enum type. 


``` java
package com.ibm.epi.enums;

public enum Days {
	
	MONDAY {
		@Override
		public void shout() {
			// TODO Auto-generated method stub
			
		}
	},TUESDAY {
		@Override
		public void shout() {
			// TODO Auto-generated method stub
			
		}
	},WEDNESDAY {
		@Override
		public void shout() {
			// TODO Auto-generated method stub
			
		}
	},THURSDAY {
		@Override
		public void shout() {
			// TODO Auto-generated method stub
			
		}
	},FRIDAY {
		@Override
		public void shout() {
			// TODO Auto-generated method stub
			
		}
	},SATURDAY {
		@Override
		public void shout() {
			// TODO Auto-generated method stub
			
		}
	},SUNDAY {
		@Override
		public void shout() {
			// TODO Auto-generated method stub
			
		}
	};
	
	 private Days(){
		
	}
	 
	 public abstract void shout();

}
```

Another advantage of using Enum for singletons is thread safe. Enum inherently ensures only one object is instantiated even when accessed by multiple threads. It is guaranted by JVM that only one object is created.


Another notable usage of Enums are enums can be used in switch statement similar to string or integer literals. Enum can also be compared using "==" or equals operator.

Enum are maintained as Singletons without any additional requirement to override readObject method even though Enums are serializable. When Java Objects are serialized and deserialized extra care has to be taken to maintain Singleton property. Enum offers this guarantee out of the box.

### Using Enum

name() returns name of the Enum
ordinal() returns the index of the variable within an Enum Type
CompareTo() used to compare two Enum types based on their order of insertion
values() method returns array of Enum values
 

Apart from these methods there are two data structures available to manipulate Enums they are EnumSet & EnumMap types are available 

``` java
	EnumSet<Days> set= EnumSet.of(Days.FRIDAY,Days.MONDAY);
		EnumMap<Days, String> map = new EnumMap<>(Days.class);
		map.put(Days.FRIDAY, Days.FRIDAY.name());
```



