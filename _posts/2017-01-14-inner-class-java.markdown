---
title:  "06 - Inner Class in Java"
date:   2017-01-14 11:30:23
categories: [Java]
tags: [programming]
reading_time: 10 min
---
Nested class are of two types

1. Non static nested class or inner class
2. Static Nested Class

Let's start with inner class

Inner class are of 3 types:

1. Member Inner class
2. Anonymous Inner class
3. Local Inner Class

**Member Inner class -1**

A "regular" inner class is declared inside the curly braces of another class, but outside any method or other code block.

```java
class ParentInnerMemberClass {
	private int data = 30;

	class Inner {
		void msg() {
			System.out.println("data is " + data);
		}
	}

	void display() {
		Inner in = new Inner(); //member class is invoked inside the class
		in.msg();
	}

	public static void main(String args[]) {
		ParentInnerMemberClass obj = new ParentInnerMemberClass();
		obj.display();
	}
}
```

**Member Inner class - 2**

```java
class ParentInnerMemberOutsideClass {
	private int data = 30;

	class Inner {
		void msg() {
			System.out.println("data is" + data);
		}
	}
}

class Test {
	public static void main(String args[]) {
		ParentInnerMemberOutsideClass obj = new ParentInnerMemberOutsideClass();
		ParentInnerMemberOutsideClass.Inner in = obj.new Inner(); //member class is invoked outside the class
		in.msg();
	}
}


```

**Few Points:**

1. An inner class is a full fledged member of the enclosing (outer) class, o it can be marked with an access modifier as well as the "abstract" or "final" modifiers
  2. From code within the inner class, the keyword "this" holds a reference to the inner class instance. To reference the outer this, use like as follows"ParentInnerMemberOutsideClass.this()"

**Method-local inner class**

It is defined within a method of the enclosing class.

```java
class ParentLocalInnerClass {
	private int data = 30;// instance variable

	void display() {
		//if there is local variable defined here, even not parameters if any
		//it must be final
		class Local { // Can be abstract or final
			void msg() {
				System.out.println(data);
			}
		}
		Local l = new Local(); //Must be within the same method, but after the class definition
		l.msg();
	}

	public static void main(String args[]) {
		ParentLocalInnerClass obj = new ParentLocalInnerClass();
		obj.display();
	}
}
```

**Anonymous Inner Class - 1**

Anonymous inner classes have no name, and their type must be either a subclass of named type or an implement if the named interface

Anonymous inner class can extend an abstract class or can implement an interface, but can't do both at a time like a normal class.

```java
abstract class ParentAnnonymousClass {
	abstract void eat();
}

class Emp {
	public static void main(String args[]) {
		ParentAnnonymousClass p = new ParentAnnonymousClass() {
			void eat() {
				System.out.println("nice fruits");
			}
		};
		/*
		 * Behind the screen - A class is created but its name 
		 * is decided by the
		 * compiler which extends the Person class and provides
		 * the implementation of the eat() method.
		 */

		p.eat();
	}
}
```

**Anonymous Inner Class - 2**

```java
interface Eatable {
	void eat();
}

class ParentAnnonymousInterface {
	public static void main(String args[]) {

		Eatable e = new Eatable() {
			public void eat() {
				System.out.println("nice fruits");
			}
		};

		/*
		 * Behind the screen - A class is created but its name is decided by the
		 * compiler which implements the Eatable interface and provides the
		 * implementation of the eat() method.
		 */
		e.eat();
	}
}
```

**Static Nested Class**

```java
/**
 * @author jyotipatt
 * 
 *         A static class that is created inside a class is known as static
 *         nested class. It cannot access the non-static members. 1) It can
 *         access static data members of outer class including private. 2)static
 *         nested class cannot access non-static (instance) data member or
 *         method.
 */
class ParentStaticNestedClass {
	static int data = 30;

	static class Inner {
		void msg() {
			System.out.println("data is " + data);
		}
	}

	/*
	 * In this example, you need to create the instance of static nested class
	 * because it has instance method msg(). But you don't need to create the
	 * object of Outer class because nested class is static and static
	 * properties, methods or classes can be accessed without object.
	 */
	public static void main(String args[]) {
		ParentStaticNestedClass.Inner obj = new ParentStaticNestedClass.Inner();
		obj.msg();
	}
}
```

**Static Nested Class - 2**

```java
class ParentStaticNestedClassTwo {
	static int data = 30;

	static class Inner {
		static void msg() {
			System.out.println("data is " + data);
		}
	}

	public static void main(String args[]) {
		ParentStaticNestedClassTwo.Inner.msg();// no need to create the instance of static nested class
	}
}
```

