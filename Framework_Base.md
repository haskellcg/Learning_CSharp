## Framework overview
  * mascorlib.dll(multi-language standard common object runtime library): built-in types, basic collection classes, types for stream processing, serialization, reflection, threading, native interoperability
  * System.dll, System.Xml.dll, System.Core.dll: XML, networking, LINQ

## ADO.NET
  * ADO.NET is the managed data access API, although the name is derived from the 1990's ADO (ActiveX Data Objects), the technology is completelt different. ADO.NET contains two major low-level components: Provider layer, Dataset layer
  * Sitting above the provider layer are three APIs that offer the ability to query databases via LINQ: Entity framwork, Entity framework Core, LINQ to SQL

## Windows workflow
  * [Workflow foundation Wiki](https://en.wikipedia.org/wiki/Windows_Workflow_Foundation)
  * a framework for modeling and managing potentially long running business process. Workflow targets a standard running library, providing consistency and interoperability.
  
## WCF - Windows Communication Foundation

## String.Format and composite format string
  * the embedded variables can be of any types, the Format simly calls ToString on them
  * the master string that includes the embedded variables is called a **composite format string**.
  ```
  string composite = "it's {0} degrees in {1} on this {2} morning";
  string s = string.Format(compiste, 35, "Perth", DataTime.Now.DayofWeek);
  ```
  * we can use interpolated string literal to the same effect, just string with $ symbol and put the expressions in braces
  ```
  string s = $"it's hot this {DataTime.Now.DayofWeek} morning"
  ```
  * format item, comma and a minimum width, colon and a format string
  ```
  string composite = "Name={0, -20} Credit Limit={1, 15:C}"
  ```
  
## StringBuilder
  * represent a mutable string. With a StringBuilder, you can Append, Insert, Remove, and Replace substrings without replacing the whole StringBuilder
  
## Text Encoding and Unicode
  * a character set is an allocation of characters, each with a numeric code or code point
  * a text encoding maos characters from their numeric code point to a binary representation. In .Net, test encodings come into play primaryly when dealing with text files or streams
  * there are two categories of test encoding in .Net
    * those map Unicode characters to another character set
    * those use standard Unicode encoding schemes
  * UTF-8 is the most space-efficient for most kind of text, it uses between 1 and 4 bytes to represent each character. The first 128 characters require only a single byte, make it compatible with ASCII. UTF-8 is the most popular encoding for text files and streams, and it's the default for stream I/O in .NET
  * UTF-16 use one or two 16-bit words to represent each characters, and is what .NET uses interbally to represent characters and string. Some programs also write files in UTF-16
  * UTF-32 is the least space-efficent, it maps each code point directly to 32 bits, so every character consume 4 bytes. It make random access very easy because every characters takes an equal number of bytes

## UTF-16 and surrogate pairs
  * some Unicode characters requires two chars to represent. This has a couple of consequences: A string's Length property may be greater than its real character count, A single char is not always enough to fully represent a Unicode character
  * Two-word characters are called surrogates. They are easy to spot because each word is in the range 0xD800 to 0xDFFF
  ```
  string ConvertFromUft32(int utf32);
  int ConvertToUtf32(char highSurrogate, char lowSurrogate);
  
  bool IsSurogate(char c);
  bool IsHighSurrogate(char c);
  bool IsLowSurrogate(char c);
  bool IsSurrogatePair(char highSurrogate, char lowSurrogate);
  ```

## DateTime and DateTimeOffset









  
  
  
  
  
  
  
