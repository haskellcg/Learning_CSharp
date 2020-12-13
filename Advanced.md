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
  














