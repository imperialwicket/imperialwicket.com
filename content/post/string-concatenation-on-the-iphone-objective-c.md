{
  "title": "String concatenation on the iPhone (Objective-C)",
  "description": "String concatenation on the iPhone (Objective-C)",
  "date": "2011-05-06",
  "url": "string-concatenation-on-the-iphone-objective-c/",
  "type": "post",
  "tags": [
    "objective-c",
    "iphone"
  ]
}
I always have to look this up. This is my attempt at remembering, as well as offering a single, easy to find location for these techniques.

Depending on how you phrase your search, you will likely end up at one of the following stackoverflow questions, each of which is worthwhile.  The information presented here is drawn from those questions, a couple of random macrumors.com forum posts, and the NSString, NSMutableString, and NSArray class reference pages from apple.

*   [StackOverflow: concatenate strings in objective-c](http://stackoverflow.com/questions/510269/how-do-i-concatenate-strings-in-objective-c)
*   [StackOverflow: concatenate strings in objective-c (iphone)](http://stackoverflow.com/questions/1158860/how-to-concatenate-string-in-objective-c-iphone)
*   [StackOverflow: concatenate strings and ints for iphone](http://stackoverflow.com/questions/703669/easiest-way-to-concatenate-strings-and-ints-for-iphone)
*   [NSString](http://developer.apple.com/library/mac/#documentation/Cocoa/Reference/Foundation/Classes/NSString_Class/Reference/NSString.html)
*   [NSMutableString](http://developer.apple.com/library/mac/#documentation/Cocoa/Reference/Foundation/Classes/NSMutableString_Class/Reference/Reference.html)
*   [NSArray](http://developer.apple.com/library/mac/#documentation/Cocoa/Reference/Foundation/Classes/NSArray_Class/NSArray.html)

I will preface this with the disclosure that I am coming from the Java side of things, and I have to admit that I find Java's string manipulation to be a lot more straight-forward:

<pre>
String myJavaString = "My string";
myJavaString += " needs";
String stringToAppend = "more characters";
myJavaString = myJavaString + " " + stringToAppend;
</pre>

After pointing that out, I will also admit two points:

A.  I am among the hordes of Java developers who despise memory management, mostly because I am not good at it.  So if anyone has concerns about these techniques (particular in the context of memory management considerations), or has any additional options that I should consider, please don't hestitate to add them in the comments.

B.  My objective-c skills are lacking; this is my first post on the topic, and the official start of my journey to get better at objective-c.  

Without further delay, string concatenation in objective-c:

#### NSString -stringByAppendingString:

stringByAppendingString is the go to method appending to a given string in order to build a new string.  The key consideration here is 'in order to build a new string.'  Those of us coming from the Java side of things are probably equating "String" with an NSMutableString, and should be careful to avoid things like:

<pre>
// DO NOT DO THIS
NSString *notAJavaString = [[NSString alloc] initWithString:@"Of course I can "];
notAJavaString = [notAJavaString stringByAppendingString:@"modify my NSString ";
// DO NOT DO THIS
</pre>

Instead, you likely want something like the following:
<pre>
NSString *myString = @"My string";
NSString *test = [myString stringByAppendingString:@" needs more characters"];
</pre>

I find that stringByAppendingString quickly becomes a burden when concatenating many strings.  In cases where multiple strings are present, but not vast numbers of strings to manage, stringWithFormat is a good option. 

#### NSString -stringWithFormat:

When dealing with multiple strings (but not very large numbers), or many variables of various types, stringWithFormat can help make much more concise concatenation.  A distinct benefit to stringWithFormat is the ability to include integer, float, string, etc. values in a single method.

<pre>
NSString *four = [[NSString alloc] initWithString:@"My string"];
NSString *three = [[NSString alloc] initWithString:@" needs "];
int two = 42;
NSString *one = [[NSString alloc] initWithString:@" more characters"];

NSString *myString = [NSString stringWithFormat:@"%@/%@/%d/%@", three, two, one];
</pre>

For many of my string concatenation needs in objective-c, stringWithFormat is a perfectly reasonable solution.

#### NSMutableString -appendString:

When dealing with a string that is programmatically generated, it is good to repeat (for the Java crowd - or maybe just for me) that a String is probably equivalent to an NSMutableString.  So start with that, and then use the appendString method to grow your string of characters.

<pre>
NSMutableString *aString = [[NSMutableString alloc] initWithString:@"My string"];
[aString appendString:@" needs more characters"];
</pre>

#### NSArray -componentsJoinedByString:

When you have really large numbers of strings to concatenate, build an array (if you don't have one already), and use the componentsJoinedByString method.

<pre>
NSArray *stringParts = [[NSArray alloc] initWithObjects:@"My string", @"needs", @"more characters", nil];
NSString *myString = [stringParts componentsJoinedByString:@" "];
</pre>

Hopefully I didn't miss anything obvious or include any terrible objective-c errors.  As I mentioned, do not hesitate to correct and/or offer alternatives in the comments.
