## In the Framework for collections can be divided into the following categories
  * Interface that define standard collection protocols
  * Ready-to-use collections classes (lists, directories, etc)
  * Base classes for writing application-specific collections
  ```
  System.Collections                          // Nongeneric collection classes and interfaces
  System.Collections.Specialized              // Strongly typed nongeneric collection classes
  System.Collections.Generic                  // Generic collection classes and interfaces
  System.Collections.ObjectModel              // Proxies and bases for custom collections
  System.Collections.Concurrent               // Thread-safe collections
  ```

## When to use nongeneric interfaces
  * in the case of IEnumerable, you must implement this interface in conjunction with IEnumetable<T>, because the latter drivers from the former.
  * in nearly all cases, you can manage entirely with the generic interfaces, the nongeneric interfaces are still occasionally useful, though, in their ability to provide type unification for collections across all element types

## Implement IEnumeable/IEnumerable<T>
  * if the class is "wrapping" another collection, by returning the wrapped collection's enumerator
  * via an iterator using yield return
  * by instantiating your own IEnumerator/IEnumerator<T> implementation
  ```
  public class MyIntList: IEnumerable
  {
    int []data = {1, 2, 3};
  
    public IEnumerator GetEnumerator()
    {
      return new Enumerator(this);
    }
  }
  
  class Enumerator: IEnumrator
  {
    MyIntList collection;
    int currentIndex = -1;
    
    
    public Enumerator
  }
  ```
