---
layout: post
title: C# Best practices
---

- **Do not rename parameters.** Be careful not to rename parameter variables because any callers who are passing arguments by parameter name will have to modify their code.

- **Do not change parameter's default value.** Be aware that changing a parameter’s default value is potentially dangerous if the method is
called from outside the module.

- **Parameter type should be weak, return value should be strong type.** When declaring a method’s parameter types, you should specify the weakest type possible, preferring interfaces over base classes. For example, if you are writing a method that manipulates a collection of items, it would be best to declare the method’s parameter by using an interface such as
`IEnumerable<T>` rather than using a strong data type such as `List<T>` or even a stronger interface
type such as `ICollection<T>` or `IList<T>`. On the flip side, it is usually best to declare a method’s return type by using the strongest type possible. In the cases in which you want to leave yourself some flexibility to change what your method returns, choose a weaker return type.

- **Use finally Blocks Liberally.**

- **Don’t Catch Everything.** When you catch an exception, you’re
stating that you expected this exception, you understand why it occurred, and you know how to deal with it. In other words, you’re defining a policy for the application.

  ```javascript
  try {
   // try to execute code that the programmer knows might fail...
  }
  catch (Exception) {
   ...
  }
  ```

  This code indicates that it was expecting any and all exceptions and knows how to recover from any and all situations. How can this possibly be? It is OK to catch System.Exception and execute some code inside the catch block’s braces as long as you re-throw the exception at the bottom of that code. Catching System.Exception and swallowing the exception (not re-throwing it) should never be done because it hides failures that allow the application to run with unpredictable results and potential security vulnerabilities.

- **Class library developers should not even think about unhandled exceptions. Only application developers need to concern themselves with unhandled exceptions**, and the application should have a policy in place for dealing with unhandled exceptions. Microsoft actually recommends that application developers just accept the CLR’s default policy. That is, when an application gets an unhandled exception, Windows writes an entry to the system’s event log.

- **Use Reliability Monitor.** To start Reliability Monitor, open the Windows Control Panel and search for “reliability history”

  ![_config.yml]({{ site.baseurl }}/images/2019-11-19/1.png)

- **Design: Base Class or Interface?**
  - IS-A vs. CAN-DO relationship A type can inherit only one implementation. If the derived type can’t claim an IS-A relationship with the base type, don’t use a base type; use an interface. Interfaces imply a CAN-DO relationship.

  - Ease of use It’s generally easier for you as a developer to define a new type derived from a base type than to implement all of the methods of an interface.

  - Consistent implementation No matter how well an interface contract is documented, it’s very unlikely that everyone will implement the contract 100 percent correctly. By providing a base type with a good default implementation, you start off using a type that works and is well tested; you can then modify parts that need modification.
  - Versioning If you add a method to the base type, the derived type inherits the new method, you start off using a type that works, and the user’s source code doesn’t even have to be recompiled. Adding a new member to an interface forces the inheritor of the interface to change its source code and recompile.

  You can actually do both: define an interface and provide a
base class that implements the interface. For example, the FCL defines the `IComparer<in T>` interface and any type can choose to implement this interface. In addition, the FCL provides an abstract base class, `Comparer<T>`, which implements this interface and provides a default implementation for the non-generic `IComparer`’s Compare method. Having both an interface definition and a base class offers great flexibility because developers can now choose whichever they prefer.

*inspired by CLR via C#...*
