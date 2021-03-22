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
  
## Defered Execution
  * an important feature of most query operator is that they execute not when constructured, but when enumerated
  * called defered or lazy execution
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
  
## How Defered Execution works
  * Query operators provide defered execution by returning decorator sequences
  * a decorator sequence has no backing structure of its own to store elements. Instead, it wraps another sequence that you supply at runtime

## Composition strategies
  * Progressive query construction: chain functions
  * Using the into keyword: lets you "continue" a query after a projection and is shortcut for progressive querying, the only place you can use into is after a select or group clause
  ```
  IEnumerable<string> query =
    from n in names
    select n.Replace("a", "").n.Replace("e", "").n.Replace("i", "")
            .Replace("o", "").n.Replace("u", "")
    into noVowel
    where noVowel.Length > 2 orderBy noVowel select noVowel;
  ```
  * Wrapping queries:
  ```
  var finalQuery = from ... in (tempQueryExpr)
  
  IEnumerable<string> query =
    from n1 in
    (
      from n2 in names
      select n2.Replace("a", "").n.Replace("e", "").n.Replace("i", "")
               .Replace("o", "").n.Replace("u", "")
    )
    where n1.Length > 2 orderBy n1 select n1
  ```
  
## Projection strategies
  * Object initializers:
  ```
  class TempProjectionItem
  {
    public string Origin;
    public string Vowelless;
  }
  
  string []names = {"Tom", "Dick", "Harry", "Mary", "Jay"};
  IEnumerable<TempProjectionItem> temp =
    from n in names
    select new TempProjectionItem
    {
      Origin = n,
      Vowelless = n.Replace("a", "").n.Replace("e", "").n.Replace("i", "")
                   .Replace("o", "").n.Replace("u", "")
    };
    
  // use temp later
  IEnumerable<string> query =
    from item in temp
    where item.Vowelless.Length > 2
    select item.Origin
  ```
  * Anonymous Types: allow you construct intermediate results without writing special classes
  ```
  var intermediate =
    from n in names
    select new
    {
      Origin = n,
      Vowelless = n.Replace("a", "").n.Replace("e", "").n.Replace("i", "")
                   .Replace("o", "").n.Replace("u", "")
    };
    
  // use intermediate later
  IEnumerable<string> query =
    from item in intermediate
    where item.Vowelless.Length > 2
    select item.Origin
  ```
  * the let Keyword: introducing a new variable alongside the range variable
  ```
  IEnemurable<string> query =
    from n in names
    let vowelless = n.Replace("a", "").n.Replace("e", "").n.Replace("i", "")
                     .Replace("o", "").n.Replace("u", "")
    where vowelless.Length > 2
    orderBy vowelless
    select n;
  ```
  
## Interpreted Queries  
  * LINQ provides two parallel architeture: local query for local collections and interpreted queries for remote data sources
  * local queries: operate over collections implementing IEnumerable<T>
  * interpreted queries: are decriptive, they operate over sequences that implement IQueryable<T>, and resolve to the query operators in the Queryable class
  * there are two IQueryable<T> implementations in the .NET Framework: LINQ to SQL, Entity Framework (EF)

### How interpreted Queries work
  * first, the compiler converts query syntax to fluent syntax
  * next, the compiler resolves the query operator methods
  
### Execution
  * followed defered execution model
 
### Combine Interpreted and Local Queries
  * a query can include both interpreted and local operators
  ```
  public static IEnumerable<string> Pair(this IEnumerable<string> source)
  {
    string firstHalf = null;
    foreach (string element in source)
    {
      if (null == firstHalf)
      {
        firstHalf = element;
      }
      else
      {
        yield return firstHalf + "," + element;
        firstHalf = null;
      }
    }
  }
  
  DataContext dataContext = new DataContext("connect string");
  Table<Customer> customers = dataCOntext.GetTable<>(Customer);
  IEnumerable<string> q =
    customers.Select(c => c.Name.ToUpper())
             .OrderBy(n => n)
             .Pair()              // local from this point on
             .Select((n, i) => "Pair " + i.ToString() + " = " + n);
  ```
  
### AsEnumerable
  * Enumerable.AsEnumerable is the simplest of all query operators, its purpose is to cast an IQueryable<T> sequence to IEnumerable<T>, forcing subsequence query operators to bind to Enumerable operators instead of Queryable operators
  ```
  Regex wordCounter = new Regex(@"\b(\w|[-'])+\b");
  var query = 
    dataConext.MedicalArticles.Where(article =>
                                     article.Topic == "influenza" &&
                                     wordCounter.Matches(article.Abstract).Count < 100);
  
  // SQL server doesn't supprt regular expression, use LINQ to SQL
  Regex wordCounter = new Regex(@"\b(\w|[-'])+\b");
  IEnumerable<MedicalArticles> sqlQuery = 
    dataContext.MedicalArticles.Where(article =>
                                      article.Topic == "influenza");
  IEnumerable<MedicalArticles> localQuery =
    sqlQuery.Where(article =>
                   wordCounter.Matches(article.Abstract).Count < 100);

  // or use AsEnumerable
  Regex wordCounter = new Regex(@"\b(\w|[-'])+\b");
  var sqlQuery = 
    dataContext.MedicalArticles.Where(article =>
                                      article.Topic == "influenza")
                               .AsEnumerable()
                               .Where(article =>
                                      wordCounter.Matches(article.Abstract).Count < 100);
  ```
  
## LINQ to SQL and Entity Framework
  * L2S: LINQ to SQL
  * EF: Entity Framework
  * both are LINQ-enabled object-relational mappers
  * EF allows for stronger decoupling between the database schema and the classes that you query, Entity Data Model
  * L2S was written by the C# team and was released with Frameword 3.5
  * EF was written by the ADO.NET team and was released later as part of Service Pack 1

### L2S Classes
  * 
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
