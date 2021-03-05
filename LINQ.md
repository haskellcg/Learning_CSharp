## LINQ
  * LINQ, Language Integrated Query, is a set of language and framework feartures for writing structured type-safe queries over local collections and remote data sources. LINQ is introduced in C# 3.0 and Framework 3.5
  * LINQ enables you to query any collection impementing IEnumerable<T>. LINQ offers the benefits of both compile-time type checking and dynamic query composition
  * define in System.Linq and System.Linq.Expressions

## Fluent Syntax
  * fluent syntax is the most flexible and fundamental.
  ```
  IEnumerable<string> filteredNames = from n in names
                                      where n.Contains("a")
                                      select n;
                                      
  public static IEnumerable<TSource> Where<TSource>(this IEnumerable<TSource> source, Func<TSource, bool> predicate);
  public static IEnumerable<TSource> OrderBy<TSource, TKey>(this IEnumerable<TSource> source, Func<TSource, TKey> keySelector);
  public static IEnumerable<TResult> Select<TSource, TResult>(this IEnumerable<TSource> source, Func<TSource, TResult> selector);
  ```
  
## Query Expressions
  * syntactix shortcut for writing LINQ queries.
  * Contrary to popular belief, a query expression is not a means of embedding SQL into C#. In fact, the design a query expression was inspired primarily by list comprehensions from functional programming languages such as LISP and Haskell
  ```
  class LinqDemo
  {
    static void Main()
    {
      string []names = {"Tom", "Dick", "Harry", "Mary", "Jay"};
      
      IEnumerable<string> query =
        from n in names
        where n.Contains("a")
        orderby n.Length
        select n.ToUpper();
        
      foreach (string name in query)
      {
        Console.WriteLine(name);
      }
    }
  }
  
  // compiler processes a query expression by translating it into fluent syntax
  IEnumerable<string> query =
    names.Where(n => n.Contains("a"))
         .OrderBy(n => n.Length)
         .Select(n => n.ToUpper())
  ```
  * vs SQL syntax: query syntax follows standard C# rules, a subquery in LINQ is just another C# expression and so requires no special syntax
  * vs Fluent syntax: query syntax is simpler, involed let clause for introducting a new variable, selectMany, Join, GroupJoin
  * Mixed syntax query: the only restriction is that query syntax component must be complete
  ```
  int matches = (from n in names where n.Contains("a") select n).Count();
  string first = (from n in names orderby n select n).FIrst();
  ```
  
## Range Vairable
  * the identifier immediately following the from keyword syntax is called the range variable, a range variable refers to the current element in the sequence that the operations is to be performed on
  
## Deffered Execution
  * an important feature of most query operator is that they execute not when constructured, but when enumerated
  * called deffered or lazy execution
  * all standard query operators provide deffered execution, with following exceptions:
    * Operators that returns a single element or scale value, such as First, Count
    * The following conversion operators: ToArray, ToList, ToDictionary, ToLookUp

## Reevaluation
  * deffered execution has another consequence -- a deffered execution query is reevaluate when you re-enumerated
  * you can defeat reevaluation by calling a conversion operator, such as ToArray...

## Captured Variables
  * query's lambda expression capture outer variable
  ```
  int []numbers = {1, 2};
  int factor = 10;
  IEnumerable<int> query = numbers.Select(n => n * factor);
  factor = 20;
  foreach (int n in query)
  {
    Console.Write(n + "|");
  }
  // 20|40|
  ``` 
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
