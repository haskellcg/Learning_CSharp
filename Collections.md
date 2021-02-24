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
    
    public Enumerator(MyIntList collection)
    {
      this.collection = collection;
    }
    
    public object Current
    {
      get
      {
        if (-1 == currentIndex)
        {
          throw new InvalidOperationException("Enumerator not started");
        }
        if (collection.data.Length == currentIndex)
        {
          throw new InvalidOperationException("Past end of list!");
        }
        return collection.data[currentIndex];
      }
    }
    
    public bool MoveNext()
    {
      if (currentIndex >= (collection.data.Length - 1))
      {
        return false;
      }
      return ++currentIndex < collection.data.Length;
    }
    
    public void Reset()
    {
      currentIndex = -1;
    }
  }
  ```
  
## ICollection and IList interfaces
  * IEnumerable<T> and IEnumarable: provide minimum functionality (enumration only)
  * ICollection<T> and ICollection: provide midium functionality (the Count property)
  * IList<T> and IList and IDictionary<K, V>: provide maximum functionality (the random access by index or key)
  * the reasons for this are mostly historical: generics came later, the generic interfaces were developed with the benefit of hindsight, leading to a different (and better) choice of members
  * An ArgumentException is thrown if you try to access a multidimentional array via IList's indexer. This is a trap when writing methods such as the following:
  ```
  public object FirstOrNull(IList list)
  {
    if (null == this || 0 == list.Count)
    {
      return null;
    }
    return list[0];
  }
  
  //multidementional
  list.GetType().IsArray && list.GetType().GetArrayRank() > 1
  ```
  * IReadOnlyList<T>:
  ```
  public interface IReadOnlyList<out T>: IEnumerable<T>, IEnumerable
  {
    int Count { get;}
    T this[int index] { get;}
  }
  ```

## Array class
  * the CLR treats array types specially upon construction, assigning them a contiguous space in memory, this makes indexing into arrays highly efficient, but prevents them from being resized later on
  * the Array does actually offer a static Resize method
  * Arrays can be duplicated with the Clone method
  ```
  // result in a shallow clone
  arrayB = arrayA.Clone();
  ```
  * GetValue and SetValue
  ```
  // create a string array 2 elements in length
  Array a = Array.CreateInstance(typeof(string), 2);
  a.SetValue("hi", 0);
  a.SetValue("there", 1);
  string s = (string)a.GetValue(0);
  
  // we can also cast to a C# array as follows
  string []cSharpArray = (string [])a;
  string s2 = cSharpArray[0];
  
  void WriteFirstValue(Array a)
  {
    Console.Write(a.Rank + "-dimentional;");
    
    int []indexers = new int[a.Rank];
    Console.WriteLine("First Value is " + a.GetValue(indexers));
  }
  
  void Demo()
  {
    int [] oneD = {1, 2, 3};
    int [,] twoD = {{5, 6}, {8, 9}};
    
    WriteFirstValue(oneD);  // 1-dimentianal; first value is 1
    WriteFirstValue(twoD);  // 2-dimentional; second value is 5
  }
  ```
  * Clear: clear content instead of affect size, ICollection's Clear is reduced to zero
  * Length and Rank: GetRank returns of dimensions in array  
  * Sort
  ```
  //base on the first array to sort second array
  int []numbers = {3, 2, 1};
  string []words = {"three", "two", "one"};
  Array.Sort(numbers, words);
  ```
  * Converting and Resizing: resize method works by creating a new array and copying over the elements
  ```
  public delegate TOutput Convert<TInput, TOutput>(TInput input);
  
  float []reals = {1.3f, 1.5f, 1.8f};
  int []wholes = Array.ConvertAll(reals, r => Convert.ToInt32(r));
  ```























