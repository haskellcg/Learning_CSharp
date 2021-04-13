# Disposal and Garbage COllection
  * disposal: some objects require explicit teardown code to release resources such as open files, locks, operating system handlers, and unmanaged objects. This is supported by the IDisposable interface
  * the managed memory occupied by unused objects must also be reclaimed at some point, this function is known as Gabage Collection, and is performed by the CLR

## Standard Disposal Semantics
  * the framework follows a de facto set of rules in its disposal logic:
    * Once disposed, an object is beyond redemption. It cannot be reactivated, and calling its methods or peoperties (other than Dispose) throws an ObjectDisposedException
    * Calling an object's Dispose method repeatly cause no error
    * If disposable object x "owns" disposable object y, x's Dispose method automatically calls y's Dispose method -- unless instructed otherwise
  * Some types define a method called Close in addition to Dispose. The framework is not completely consistent on the semantics of a Close method, although in nearly all cases it's either: functionally identical to Dispose, or A functional subset to Dispose
