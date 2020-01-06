---
title:  "04 - Singleton Design Pattern in Java"
date:   2016-11-11 11:30:23
categories: [Java]
tags: [programming]
reading_time: 5 min
---
Singleton is simply a class which is instatiated once per JVM initialisation.There are many ways to achieve it.

**Eager initialisation**

```java
public class EagerSingleton {
	private static volatile EagerSingleton instance = new EagerSingleton();

	// private constructor
	private EagerSingleton() {
	}

	public static EagerSingleton getInstance() {
		return instance;
	}
}
```

Drawback:

Instance is created irrespective of it is required in runtime or not

**Lazy Initialization**

```java
public final class LazySingleton {
	private static volatile LazySingleton instance = null;

	// private constructor
	private LazySingleton() {
	}

	public static LazySingleton getInstance() {
		if (instance == null) {
			synchronized (LazySingleton.class) {
				instance = new LazySingleton();
			}
		}
		return instance;
	}
}
```

Drawback:

Suppose there are two threads T1 and T2. Both comes to create instance and execute instance=null, 
now both threads have identified instance variable to null thus assume they must create an instance. 
They sequentially goes to synchronized block and create the instances. At the end, 
we have two instances in our application.

**Double Checked Locking**

```java
public class EagerSingleton {
	private static volatile EagerSingleton instance = null;

	// private constructor
	private EagerSingleton() {
	}

	public static EagerSingleton getInstance() {
		if (instance == null) {
			synchronized (EagerSingleton.class) {
				// Double check
				if (instance == null) {
					instance = new EagerSingleton();
				}
			}
		}
		return instance;
	}
}
```

Please ensure to use **Volatile** keyword with instance variable otherwise you can run into out of order write error scenario, where reference of instance is returned before actually the object is constructed i.e. JVM has only allocated the memory and constructor code is still not executed. In this case, your other thread, which refer to uninitialized object may throw null pointer exception and can even crash the whole application.

**Bill pugh Solution**

```java
public class BillPughSingleton {
	private BillPughSingleton() {
	}

	private static class LazyHolder {
		private static final BillPughSingleton INSTANCE = new BillPughSingleton();
	}

	public static BillPughSingletongetInstance() {
		return LazyHolder.INSTANCE;
	}
}
```

As you can see, until we need an instance, the LazyHolder class will not be initialized until required and you can still use other static members of BillPughSingleton class

**Enum**

provide implicit support for thread safety and only one instance is guaranteed.Recommended in Effective Java

```java
public enum EnumSingleton {
	INSTANCE;
	public void someMethod(String param) {
		// some class member
	}
}
```

**Final way**

```java
public class DemoSingleton implements Serializable {
	private static final long serialVersionUID = 1L;

	private DemoSingleton() {
		// private constructor
	}

	private static class DemoSingletonHolder {
		public static final DemoSingleton INSTANCE = new DemoSingleton();
	}

	public static DemoSingleton getInstance() {
		return DemoSingletonHolder.INSTANCE;
	}

	protected Object readResolve() {
		return getInstance();
	}
}
```
