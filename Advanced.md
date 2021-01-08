## Advanced
  * delegates
  ```
  // => Transformer t= new Transformer(Square);
  Transformer t = Square;
  
  // => t.Invoke(3);
  t(3);
  ```
  * multicast delegates:
    * delegates are invoked in the order they are added
    * if a multicast delegates has a nonvoid return type, the called receives the return value from the last method to be invoked, the preceding methods are still called, but their return value are discarded
  * generic delegates
  ```
  delegate TResult Func<out TResult>();
  delegate TResult Func<in T, out TResult>(T arg);
  delegate TResult Func<in T1, in T2, out TResult>(T arg1, T arg2);
  ... and so on, up to T16
  
  delegate void Action();
  delegate void Action<in T>(T arg);
  delegate void Action<in T1, in T2>(T arg1, T arg2);
  ... and so on, up to T16
  ```
  * delegate vesus interface: a delegate design maybe a better choice than an interface design if one or more these conditions are true:
    * the interface defines only a single method
    * multicast capability is needed
    * the subscriber needs to implement the interface multiple times
  * delegate compatibility
  ```
  delegate void D1();
  delegate void D2();
  ...
  
  D1 d1 = method;
  D2 d2 = d1;   // compile-time error
  
  D2 d2 = new D2(d1);  // legal
  
  delegate void D();
  D d1 = method1;
  D d2 = method1;
  Console.WriteLine(d1 == d2);  // True
  ```
  * generic deletagte type parameters: covariance and contravariance
  * events: broadcast and subscriber
    * the broadcast is a type that contains a delegate field. the broadcast decides when to broadcast, by invoking the delegate
    * the subscriber are the method target recipients. a subscriber decides when to start and stop listening by calling += and -= on the carodcaster's delegate. a subscriber dose not know about, or interfere with other subscribers
    * events are language feature that formalizes this pattern. An event is a construct that exposes just the subset of delegate feature required for tha broadcast and subscriber model. **the mian purpose of events is to prevent subscribers from interfere with one another.**
    ```
    public class Broadcaster
    {
      public event PriceChangedHandler PriceChanged;
    }
    
    =>
    PriceChangedHandler priceChanged; // private delegate
    public event PriceChangedHandler PriceChanged
    {
      add {priceChanged += value;}
      remove {priceChanged -= value;}
    }
    
    => the compiler looks within the broadcaster class for references to PriceChanged that 
    perform operation other than += and -=, and redirects them to the underlying 
    priceChanged delegate field
    
    => translate += and -= operation on the event to calls to the event's add and remove accessor
    ```
    * standard event pattern: System.EventArgs, a predefined framework class with no members
    ```
    public class PriceChangedEventArgs: System.EventArgs
    ```
    * with an EventArgs subclass in place, the next step is to choose or define a delegate for event, there are 3 ruls: first, it must have a void return type; second, it must accept two arguments object and EventArgs; thrid, it name must end with EventHandler
    ```
    public delegate void EventHandler<TEventArgs>(object source, TEventArgs e) where TEventArgs: EventArgs
    ```
    * before generic existed in language, we would jave to instead write a custom delegate as follows
    ```
    public delegates void PriceChangedHandler(object sender, PriceChangedEventArgs e);
    ```
  * with explicit event accessor, you can apply more complex strategories to storage and access of the underlying delegate
  * event modifires
  ```
  public class Foo
  {
    public static event EventHandler<EventArgs> StaticEvent;
    public virtual event Eventhandler<EventArgs> VirtualEvent;
  }
  ```
  * Lambda
    * captured variables have their lifetimes extended to that of the delegate.
    ```
    static Func<int> Natural()
    {
      int seed = 0;
      return () => seed++;
    }
    static void Main()
    {
      Func<int> natural = Natural();
      Console.WriteLine(natural);  // 0
      Console.WriteLine(natural);  // 1
    }
    ```
    * when you capture the iteration variables of a for loop
    ```
    Action[] actions = new Action[3];
    for (int i = 0; i < 3; ++i)
    {
      actions[i] = () => Console.WriteLine(i);
    }
    foreach (Action a in actions) a();   // 333
    ```
    * in C# 5.0, foreach loops would in the same way above. This cause considerable confusion and its been fixed since C# 5.0.
    * lambda expression versus local method: local method can be recursive, can avoid the clutter of specifying a delegate type, and they incur lightly less overhead; lambda use when call higher-order functions
  * Anonymous methods: an anonymous method is like a lambda expression, but it lacks the following feature: implicitly typed parameters, expression syntax, the ability to compile to an expression tree.
  ```
  delegate int transformer (int i);  
  transformer sqr = delegate (int x) { return x * x; };
  
  public event EventHandler Clicked = delegate {};
  Clicked += delegate { Console.WriteLine("clicked"); };
  ```
  * when exception is thrown, the CLR performs a test: is execution currently within a try statement that can catch the exception:
    * if so, execution is passed to the compatible catch block, and if present, executing the finally block first
    * if not, execution jumps back the caller of the function, and the test is repeat
    * if no function takes the responsibility for the exception, an error dialog box is displayed to the user, and the program terminates
  * exception filter
  ```
  catch (WebException ex) when (ex.Status == WebExceptionStatus.Timeout)
  { ... }
  catch (WebException ex) when (ex.Status == WebExceptionStatus.SendFallure)
  ```
  * IEnumerator and IEnumerable are define in System.Collections. IEnumerator\<T\> and IEnumerable\<T\> are defined in System.Collection.Generic
  ```
  class IEnumerator
  {
    public IteratorVariableType Current { get {...}}
    public bool MoveNext() {...}
  }
  class IEnumerable
  {
    public Enumerator GetEnumerator() {...}
  }
  ```
  * Dictionary initialize
  ```
  var dict = new Dictionary<int, string>()
  {
    {5, "five"},
    {10, "ten"}
  }
  var dict = new Dictionary<int, string>()
  {
    [3] = "three",
    [10] = "ten"
  }
  ```
  * On each yield statement, control is returned to the caller, but the caller's state is maintained so that the method can continue executing as soon as the caller enumerates the next element. The compiler converts iterator methods into private classes that implement IEnumeable\<T\> and/or IEnumerator\<T\>.
  ```
  static IEnumerable<int> Fibs(int fibCount)
  {
    for (int i = 0, prevFib = 1, curFib = 1; i < fibCount; ++i)
    {
      yield return prevFib;
      int newFib = prevFib + curFib;
      preFib = curFib;
      curFib = newFib;
    }
  }
  static IEnumerable<string> Foo()
  {
    yield return "One";
    yield return "Two";
    
    if (breakEarly)
      yield break;
    
    yield return "Three";
  }
  ```
  * A yield return statement cannot appear in a try block that has a catch cluase. You can, however, yield within a try block that has a finally block. the code in the finally block executes when the consuming enumerator reaches the end of the sequence or is disposed.
  ```
  IEnumerable<string> Foo()
  {
    try { yield return "One";}  // Illegal
    catch { ... }
  }
  
  IEnumerable<string> Foo()
  {
    try { yield return "One";}  // OK
    finally { ... }
  }
  ```
  * Nullable Types/Nullable\<T\> Struct, 
  ```
  int? i = null;
  
  // code translate
  public struct Nullable<T> where T: struct
  {
    public T value {get;}
    public bool HasValue {get;}
    public T GetValueOrDefault();
    public T GetValueOrDefault(T defaultValue);
    ...
  }
  
  // operator lifting
  int? x= 5;
  int? y = 10;
  boolb = x < y;  // bool b = (x.HasValue && y.HasValue) ? (x.Value < y.Value) : false;
  
  null == null  // true
  
  // bool? with & and | operator
  bool? as an unknow value, so null | true is true
  
  // null operators
  int? x = null;
  int y = x ?? 5;  // y is 5
  ```
  * Extension methods: allow an existing type to be extended with new methods wihtout alerting the defination of the original type. an extension method is a static method of a static class, where the this modifier is applied to the first parameter. the type of the first parameter will be the type that is extended.
  ```
  public static class StringHelper
  {
   public static bool IsCapitalized(this string s)
   {
     if (string.IsNullOrEmpty(s))
     {
       return false;
     }
     else
     {
       return char.IsUpper(s[0]);
     }
   }
  }
  
  Console.WriteLine("Perth".IsCapitalized());  // Console.WriteLine(StringHelper.IsCapitalized("Perth"));
  ```
  * Anonymous Types: is a simple class created by the compiler on the fly to store a set of value. To create an anonymous type, use the new keyword followed by an object initializer, specifying the propertise and values the type will contain
  ```
  var dude = new {Name = "Bob", Age = 23};
  
  int Age = 23;
  var dude = new {Name = "Bob", Age, Age.ToString().Length};
  
  var a1 = new {X = 2, Y = 4};
  var a2 = new {X = 2, Y = 4};
  Console.WriteLine(a1.GetType() == a2.GetType());  // true
  
  Console.WriteLine(a1 == a2);  // false
  Console.WriteLine(a1.equals(a2));  // true
  
  // A method cannot return an anonymously typed object, because it is illegal to write a method whose return type is var
  // instead you must use object or dynamic and then whoever calls Foo has to reply on dynamic binding, with loss of static type safety
  var Foo() => new {Name = "Bob", Age = 30};  // Illegal
  dynamic Foo() => new {Name = "Bob", Age = 30};  // No static type safety
  ```
  * Tuple: to safely return multiple values from a method without resorting to out parameters
  ```
  // create a tuple with unnamed elements, which refer to as Item1, Item2
  var bob = ("Bob", 23);
  
  // Naming Tuple elements
  var tuple = (Name: "Bob", Age: 23);
  
  // specifying tuple types
  static (string Name, int age) GetPerson() => ("Bob", 23);
  static void Main()
  {
    var person = GetPerson();
    Console.WriteLine(person.Name);
    Console.WriteLine(person.Age);
  }
  
  // type erasure
  public struct ValueTuple<T1>
  public struct ValueTuple<T1, T2>
  public struct ValueTuple<T1, T2, T3>
  
  // factory method
  ValueTuple<string, int> bob1 = ValueTuple.Create("Bob", 23);
  (string, int) bob2 = ValueTuple.Create("Bob", 23);
  
  // destructing tuple
  var bob = ("Bob", 23);
  (string name, int age) = bob;
  ```
  * System.Tuple classes: you will find another family of generic types in the System namespace called Tuple (rather than ValueTuple). These were introduced in .Net Framework 4.0, and are classes. Defining tuple as classes was in retrospect considered a mistake.
  * Attributes: an extensible machanism for adding custom information to code element (assemblies, types, members, return values, parameters, and generic type). This extensibility is useful for services that integrate deeply into the type system, without requiring special keywords or constructs in the C# language
  ```
  [ObsoleteAttribute]
  public class Foo {...}
  public sealed class ObsoleteAttribute: Attribute {...}
  
  //Named and Positional attribute parameters
  //first is positional parameter, correspond to the parameters of the attribute type's public constructors
  //second is name parameter, correspond to public fields or public properties on the attribute type
  [XmlElement("Customer", Namespace="http://oreilly.com")]
  public class CustomerEntity {...}
  
  //attribute targets: you can also attach attributes to an assembly, this requires that you explicitly specify the attribute's target
  [asssembly:CLSComliant(true)]
  
  //Multiple attribute
  [Seriazble, Obsolete, CLSCompliant(false)]
  public class Bar {...}
  [Seriazble] [Obsolete] [CLSCompliant(false)]
  public class Bar {...}
  [Seriazble]
  [Obsolete]
  [CLSCompliant(false)]
  public class Bar {...}
  ```
  * Caller info attributes: you can tag optional parameters with one the three caller info attributes, which instruct the compiler to fedd information obtained from the caller's source code into the parameter's default value. **Useful for logging**
  ```
  [CallerMemeberName]  applies the caller's member name
  [CallerFilePath]     applies the path to caller's source code file
  [CallerLineNumber]   applies the line number in the caller's source code file
  ```
  * Dynamic Binding: the process of resolving types, memebers, and operators, from compile time to runtime: Dynamic binding is useful when at compile time you know that a certain function, member, or operation exists, but the compiler does not. **This commonly occurs when you are interoperating with dynamic languages (such as IronPython) and COM and in scenarios when you might otherwise use reflection**. A dynamic types tells the compiler to relax. We expect the runtime type of d to has a Quack method
  ```
  dynamic d = GetSomeObject();
  d.Quack()
  
  // Custom binding: occurs when a dynamic object implements IDynamicMetaObject provider (IDMOP) 
  
  // Runtime representation of dynamic
  typeof(dynamic) == typeof(object)
  typeof(List<dynamic>) == typeof(List<object>)
  typeof(dynamic[]) == typeof(object[])
  
  //dynamic conversions
  int i = 7;
  dynamic d = i;
  long j = d;     // No cast required (implicit conversions)
  short j = d;    // throw RuntimeBinderException
  
  //var Versus dynamic
  
  //dynamic calls without dynamic receiver, you can call statically known functions with dynamic arguments, such calls are subject to dynamic overload resolution
  //and can include:
  //    static methods
  //    instance constructors
  //    instance methods on receivers with a statically known type
  dynamic x = ....
  x.Foo();
  
  //Uncallable functions:can't be called dynamically
  //    extension methods
  //    members of an interface, if you need to cast to that interface to do so
  //    base members hidden by a subclass
  //dynamic binding requires two pieces of information: the name of the function to call, and the object upon which to call the funtion. However,
  //in each of the uncallable scenarios, an additional type is enevolved, which is knwon only at compile time
  ```
  * Operator overloading
  ```
  //indirecly overloaded
  //the compound assignment operator (e.g., +=, /=) are implicitly override by overriding the noncompound operator (e.g., +, /)
  //the conditional operators && and || are implicitly overriden by overriding the bitwise operators & and |
  
  //example
  public struct Note
  {
      int value;
      public Note(int senitonesFromA)
      {
          value = semitonesFromA;
      }
      public static Note operator+(Note x, int semitones)
      {
          return new Note(x.value + semitones);
      }
  }
  Note B = new Note(2);
  Note CSharp = B + 2;
  CSharp += 2;
  
  //overriding equality and comparision operators
  //pairing: (==, !=), (<, >), (<=, >=)
  //Equals and GetHashCode:if you override (== and !=)
  //IComparable and IComparble<T>: if you override (<, > and <=, >=)
  
  //custom implicit and explicit conversions
  //write a constructor that has a parameter of the type to convert from
  //write ToXXX and static FromXXX methods to convert between types
  public static implicit operator double(Note x) =>
      440 * Math.Pow(2, (double)x.value / 12);
  public static explicit operator Note(double x) =>
      new Note((int)(0.5 + 12 * (Math.Log(x / 440) / Math.Log(2))));
  Note n = (Note)554.32;    //explicit conversion
  double x = n;             //implicit conversion    
  ```
  * Unsafe code and pointers: C# support direct memory manipulation via pointers within blocks of code marked unsafe and compiled with the /unsafe compiler option
  ```
  //fixed statement:required to pin a managed object, such as the bitmap, during the execution of a program, many objects are allocated and deallocated from heap
  //in order to avoid unnecessary waste of fragmentation of memory, the gabage collector moves objects around.
  //pointing to an object is futile if its address cound change while referencing it, so the fixed statement tells the gabage collector to "pin" the object
  unsafe void BlueFilter(int [,]bitmap)
  {
      int length = bitmap.Length;
      fixed (int *b = bitmap)
      {
          int *p = b;
          for (int i = 0; i < length; ++i)
          {
              *p++ &= 0xFF;
          }
      }
  }
  
  //the pointer to member operator
  
  //stackalloc keyword: since it is allocated on the stack, its lifetime is limited to the execution of the method, just as other local variable
  int *a = stackalloc int[10];
  
  //fixed-size buffers: create fixed-size buffers within structs
  unsafe struct UnsafeUnicodeString
  {
      public short Length;
      public fixed byte Buffer[30];
  }
  ```
  * Preprocessor directives
  ```
  #define
  #undef
  #if
  #else
  #elif
  #endif
  #warning
  #error
  #pragma warning
  #line
  #region
  #endregion
  ```
  
  
  
  
  













