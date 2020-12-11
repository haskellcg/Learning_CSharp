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
  
