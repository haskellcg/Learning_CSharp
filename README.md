# Learning_CSharp
## Basic
  * string is a reference type. Its equal operator, however, follow value-type semantics
  ```
  string a = "test"
  string b = "test"
  Console.Write(a == b) // true
  ```
  * in string, the & and | operators also test for and and or conditions. The difference is that they **do not** short-circuit.
  * expression evaluated at compile time are always overflow-checked, unless you apply the unchecked operator
  ```
  int x = int.MaxValue + 1; // compile-time error
  int y = unchecked(int.MaxValue + 1) // no errors
  ```
  * following error result in compile-time error
  ```
  static void Main()
  {
    int i;
    Console.WriteLine(i); // compile-time error
  }
  ```
  * ref modifier, pass by reference
  ```
  class Test
  {
    static void Foo(ref int p)
    {
      p = p + 1;
      Console.WriteLine(p);
    }
    
    static void Main()
    {
      int x = 8;
      Foo(ref x);
      Console.WriteLine(x); // x is 9
    }
  }
  ```
  * out modifier, it is like a ref argument, except it:
    * need not be assigned before going into the function
    * must be assigned before it comes out of the function
    * most used to get multiple return values back from method
  ```
  class Test
  {
    static void Split(string name, out string fileNames, out string lastName)
    {
      int i = name.LastIndexOf('');
      firstNames = name.SubString(0, i);
      lastName = name.SubString(i + 1);
    }
    
    static void Main()
    {
      string a, b;
      Split("Stevie Ray Vaughan", out a, out b);
      Console.WriteLine(a);
      Console.WriteLine(b);
    }
  }
  ```
  * params modifier, be used on the last parameters of the a method so that the method accepts any number of arguments of a particular type. The parameter type must be declared as an array
  ```
  class Test
  {
    static int Sum(params int []ints)
    {
      int sum = 0;
      for (int i = 0; i < ints.Length; ++i)
      {
        sum += ints[i];
      }
      return sum;
    }
    
    static void Main()
    {
      int total = Sum(1, 2, 3, 4);
      Console.WriteLine(total);
    }
  }
  ```
  * operator precedence and associativity(left associativity, right associativity)
  * operator table
  * Null operator
    * Null coalescing operator: if operand is non-null, give it to me, otherwise, give me default value
    ```
    string s1 = null;
    string s2 = s1 ?? "nothing"
    ```
    * Null-conditional operator
    ```
    System.Text.StringBuilder sb = null;
    string s = sb?.ToString(); // == string s = (sb == null ? null : sb.ToString());
    x?.y?.z
    ```
  * switch statement with patterms
  ```
  switch (x)
  {
    case int i:
      break;
    case string s:
      break;
    default:
      break;
  }
  ```
  * Jump statement: break, continue, goto, return, throw
    * a jump out of try block always execute the try's finally block before reaching the target of the jump
    * a jump cannot be made from the inside to outside of a finally block (except via throw)
  * application cannot compile because Widge is ambiguous. Extern alias can resolve the ambiguity in our application
  ```
  // csc /r:W1=Widgets1.dll /r:W2=Widgets2.dll application.cs
  extern alias W1;
  extern alias W2;
  
  class Test
  {
    static void Main()
    {
      W1.Widgets.Widget w1 = new W1.Widgets.Widget();
      W2.Widgets.Widget w2 = new W2.Widgets.Widget();
    }
  }
  ```
  * overloading constructors,
  * implicit parameterless constructors: C# compiler automatically generates a parameterless public constructor if and only if you do not define any constructor. However, as soon as you define at least one constructor, the parameters constructor is no longer automatically generated.
  * deconstruction method must be called Deconstruct, and have one or more out parameters
  ```
  class Rectangle
  {
    public void Deconstruct(out float width, out float height)
    {
      width = Width;
      height = Height;
    }
  }
  
  vec rect = new Rectangle(3, 4);
  (float width, float height) = rect; // Deconstruct
  ```
  * virtual function members: a function marked as **virtual** can be **overrided** by subclass wanting to providing a specialed implementation  
  * Abstract classes and abstract members
  ```
  public abstract class Asset
  {
    public abstract decimal NetValue { get; }
  }
  
  public class Stock: Asset
  {
    public long ShareOwned;
    public decimal CurrentPrice;
    public override decimal NetValue => CurrentPrice * SharesOwned;
  }
  ```
  * Hiding inherited members
  ```
  public class A {public int Counter = 1;}
  public class B: A {public new int Counter = 2;} // the new modifire comunicates you intent to 
                                                  // compiler that duplicate member is not an accident
  ```
  * Sealing functions and classes: to prevent it from being overridden by further subclasses
  ```
  public sealed override decimal Liability { get { return Mortgage; } }
  ```
  * when an overload is called, the most specific type has precedence
  * boxing and unboxing: act of converting a value-type to a reference-type
  * Structs is similar to a class, with the following key differences:
    * A struct is value type, whereas a class is reference type
    * a struct does not support inheritance
    * a struct does not have parameterless constructor
    * a struct does not have field initializers
    * a struct does not have virtual or protected members
  * construction semantics of a struct are as follows:
    * a parametrless constructor that you cant override implicitly exists. This performs a bitwise-zeroing of its fields
    * when you define a struct constructor, you must explicitly assign every field
  * internal: accessible only within the containing assembly or friend assemblies. This is the default accessibility for non-nested types
  * friendly assemblies
  ```
  [assembly: InternalVisibleTo("Friend")]
  ```
  * Interface members are always **implicitly public** and cannot declare an access modifire
  ```
  internal class CountDown: IEnumerator
  {
    int count = 11;
    public bool MoveNext() => count-- > 0;
    public object Current => count;
    public void Reset() { throw new NotSupportedException();}
  }
  ```
  * Flags enum
  ```
  [Flags]
  public enum BorderSlides {None = 0, Left = 1, Right = 2, Top = 4, Bottom = 8}
  BorderSlides leftRight = BorderSlides.Left | BorderSlides.Right;
  string formatted = leftRight.ToString(); // "Left, Right"
  ```
  * Generics  
    * unbound generic types
    ```
    class A<T> {}
    class A<T1, T2> {}
    
    Type a1 = typeof(A<>);
    Type a2 = typeof(A<,>);
    ```
    * generic constraints
    ```
    where T: base-class  // must subclass a perticular class 
    where T: interface   // must implement that interface
    where T: class       // 
    where T: struct
    where T: new()
    where U: T
    ```
    * self referenceing generic declarations
    * type parameters and conversions
    ```
    // Numberic conversion
    // Reference conversion
    // Boxing/unboxing conversion
    // Custom conversion
    
    StringBuilder Foo<T> (T arg)
    {
      if (arg is StringBuilder)
        return (StringBuilder)arg;    // will not compile
    }
    
    StringBuilder Foo<T> (T arg)
    {
      StringBuilder sb = arg as StringBuilder;
      if (null != sb) return sb;
    }
    ```
    * Covariance: assume A is convertible to B, X has a covariant type parameter if X\<A\> is convertible to X\<B\>, **covariance is not automatic**
    ```
    public interface IPoppable<out T> {T Pop();}
    
    var bears = new Stack<Bear>();
    bears.push(new Bear());
    // Bears implements IPoppable<Bear>, we can convert to IPoppable<Animal>
    IPoppable<Animal> animals = bears;
    Animal a = animals.pop();
    ```
    * Contravariance:reverse convert compare to Convariance
    ```
    public interface IPushable<in T> {void push(T obj);}
    
    IPushable<Animal> animals = new Stack<Animals>();
    IPushable<Bear> bears = animals;
    bears.push(new Bear());
    ```
  * C\# Generics Versus C\+\+ Template:in C\#, producer type can be compiled into a library, because the synthesis between the producer and the costumer that produces closed types doesn't actually happen until runtime; in C\+\+, it performed at compile time
