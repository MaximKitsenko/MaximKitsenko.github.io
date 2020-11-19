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

- **Reliability Monitor.** To start Reliability Monitor, open the Windows Control Panel and search for “reliability history”

![_config.yml]({{ site.baseurl }}/images/2019-11-19/1.png)

*inspired by CLR via C#...*
