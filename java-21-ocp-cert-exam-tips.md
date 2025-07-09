# Java 21 OCP Certification Exam Tips
* **Author**: Shea Riley
* **GitHub profile**: https://github.com/sheariley
* **Description**: First, this is not meant to be an exhaustive list of topics covered by the Java 21 OCP exam. This is a categorized list of helpful tips that I found helpful; mostly consisting of nuances that were common pitfalls for me when I was taking practice tests. I found it very helpful to read through this list before taking each test to remind myself of these common pitfalls and nuances. One could also use this list to help them become familiar with the quirks of the Java language and avoid its pitfalls. Feel free to submit pull requests, if you feel that something should be corrected, added, or augmented. Fair warning though: I may or may not spend time looking at these pull requests. We'll see how it goes.

## General Test Taking Tips
* Read the **WHOLE** problem. Don't skip over things, as that is often how one arrives at the wrong answer.
* Read every answer and compare them.  If 2 look similar, that is a clue that one is trying to trick you.
* Look for trick questions where strings are trying to be assigned to variables declared as `char` or a `double` is trying to be assigned to a `float` without casting.
* Pay careful attention to method return types and parameter types.

---
## Control Flow
* You can use a constant value of `false` as the conditional expression of an `if-statement` and it will **NOT** cause an "unreachable code" compilation error. However, you **CANNOT** do the same with `while-statements`. This is intended to permit the equivalent of _conditional compilation_.
  ```java
  if (false) { // VALID
    System.out.println("Debugging");
  }

  while (false) { // INVALID
    System.out.println("Debugging");
  }
  ```
* The parts of a for-loop statement are called initialization, condition, and update.
* Each of the three parts within the parenthesis is optional, but the semicolons are required. If omitted, the condition is assumed to be `true` (endless loop).
* Before each iteration of a for-loop, the condition expression is evaluated to determine if the iteration is to be executed (including the first iteration).
* If present, the condition expression **MUST** evaluate to a boolean value or compilation will fail.
* After the each iteration of a for-loop, the update expression is executed
  ```java
  for (var i = 0; i < 10; i++) {
    // ...body...
  }
  /* the update expression i++ is executed after the body of the for-loop is executed */
  ```

---
## Switch Statements and Expressions
* Variables declared in switch-case blocks are available within the rest of the case blocks below the one that declares the variable(s); even if the one that declares the variable(s) is not executed.
* Switch **expressions** must be exhaustive, the `default` case must be last, and `case null` is allowed (only in switch expressions). Also, the `default` case does not cover null. In other words, the `case null` and `default` cases should always be implemented for switch expressions operating on reference types. Finally, the `case null` and `default` cases can be combined in a switch with pattern matching (ex: `case null, default -> "invalid input";`). 
* The when-clauses of the case label statements must be taken into account when analyzing a switch expression to determine if it is exhaustive. Therefore, it is probably recommended that a `default` case always be specified if when-clauses are used; unless one is sure that the switch expression will always remain exhaustive.
* The `default` case of switch statements and expressions is permitted to be unreachable to accommodate new values being introduced, such as when new values are added to enums.

---
## Polymorphism and Inheritance
* Accessibility modifiers are ordered as follows, from least to most accessible: `private`, `package-private` (default), `protected`, `public`.
* A method override is chosen by the compiler by using the instance (or object) type of a variable; and **NOT** the reference type.
* When referring to a field that is hidden/shadowed, the field that is dereferenced by the compiler is chosen using the reference type (opposite of method override selection); and **NOT** the instance type. So, a hidden field may be dereferenced by casting an object to the appropriate super type.
* To access a super type's overridden method, the `super` keyword can be used. Example: `super.do_something();`
* A method on a super type that is more than one level deep in the object hierarchy **CANNOT** be accessed via the `super` keyword. The following is invalid: `super.super.do_something();`.
* Static methods on interfaces must be invoked using the interface name; and cannot be invoked using a reference variable. Doing so will cause compilation to fail.
* Look for `static` methods trying to access instance members without creating a new instance.
* Interfaces and their methods can be declared `abstract`, although it is not necessary; as they are implicitly `abstract`.
* Sealed classes can omit the permits clause if the subclasses are in the same scope/file.
* Subclasses of sealed classes must be declared either `sealed`, `non-sealed`, or `final`.
* Pay careful attention to the modifier keywords on constructors. They are not inherited from the class definition. (i.e. Subclasses from different packages cannot access super-class ctors with no modifier specified, because the default modifier is `package-private` and thus cannot be extended by the aforementioned subclasses).
* To determine if a method is a valid overload (and not an override), the params must be different (type(s) and/or number of params). It cannot only differ in return type.
* Method overloads are chosen by the compiler using the reference/declaration type(s) of the variable(s) being passed as argument(s); not the variables' object type(s). This gives the coder the ability to choose the method being called by casting the variable(s) to different compatible types and then using them to call specific overloads.
* Method overrides can return the same type or a sub-type of the overridden method (AKA covariant return types).
* Method override parameters must either have the same generic type spec or no generic type spec (type erasure).
* All fields of an interface are always `public`, `final`, and `static`; even if no modifiers are specified. So they must be explicitly initialized with a value.
* Interface fields can be accessed via the interface's name or via the name of instance variables of classes that implement the interface (unlike `static` methods on interfaces which must be accessed explicitly via the interface name)
* Only instance methods of an interface can be `private`. See previous statement about fields.
* The `var` keyword cannot be used to declare a class member. It can only be used to declare a local variable.
* You cannot override a `static` method of a class with a non-static method (and vice-versa) in a subclass. However, you can override a `static` method of an interface with a non-static method in a subclass or sub-interface, because interface's `static` methods must be referenced using the name of the interface in which it is declared.
* Look for incorrect answers that try to reuse local variable names for lambda expression arguments. Lambda expressions do not create a new scope.
* Pay careful attention to the declared type of variables and the accessibility of the fields/methods that are attempting to be accessed in the subsequent code. (ex: Given `Book o1 = new Encyclopedia()` only the members on the `Book` class are accessible without explicit casting)
* Fields are not overridden. They are hidden or shadowed and it is ok for a subclass to hide a field defined in its ancestors.
* Instance members declared in subclasses are initialized **AFTER** the super-class' ctor is executed, unless they are final and initialized upon declaration.
* If a class implements multiple interfaces that declare the same signature for a `default` method, it **MUST** override the default methods from the interfaces. Otherwise, a compilation error will occur.

---
## Variable Types, Initialization, Primitives/Wrappers, and Casting
* Signed numeric type ranges usually extend further below zero than above. For example, the value range for `byte` is `-128` to `127`.
* Caching ranges for primitive wrappers
  * All `Boolean` values (`true` and `false`)
  * All `Byte` values (`-128` to `127`)
  * All `Character` values from `\u0000` to `\u007f` (i.e. `0` to `127` in decimal)
  * All `Short` and `Integer` values from `-128` to `127`
* You cannot compare (using `==`) operands of different types of primitive wrappers. (ex: `Byte b = 1; Integer i = 1; b == i;` will not compile because operands are of different reference types)
* Promotion of numeric primitives is not used automatically when boxing/unboxing. For example, you cannot box an `int` into a `Double` wrapper object; even though an `int` can be assigned to a `double` primitive variable.
* Implicit narrowing never happens for long and double primitives.
* Implicit narrowing is only allowed when assigning **constant literal** values (i.e. no variables; even effectively final).
* The only target types for implicit narrowing are `byte`, `short`, or `char`.
* Implicit narrowing does **NOT** apply when converting from floating-point types (`float` or `double`) to integer types (`byte`, `short`, `char`, `int`, or `long`).
* Implicit narrowing is **NOT** allowed when converting from `long` to `byte`, `short`, or `char`.
* Implicit narrowing only applies to primitive types. It does **NOT** apply to objects or wrapper classes.
* Implicit narrowing does NOT happen during method invocation (overload selection). This includes constructors. For example, `new Short(9)` will **NOT** compile, but `new Short((short)9)` will compile.
* Operands of math operators are **ALWAYS** promoted to at least `int`. (ex: `byte * byte` will result in an `int`)
* All compound assignment operators (ex: `+=`, `*=`, etc...) internally do an explicit cast. (i.e. `s += i` equal to `s = (short)s + (short)i` where `s` is a `short` and `i` is an `int`)
* The `instanceof` operator only works on reference types; not primitives.
* The result of an expression involving **ONLY** values of type `int`, will always be an `int` (including division). Therefore, the resulting value can be stored in a variable of type `int`, `long`, `float`, or `double`; as those types can all accommodate values of type `int` via implicit widening.
* `Math.round` returns `int` or `long` when given a `float` or `double` respectively.
* Auto-boxing will potentially be used when the compiler is determining which method overload to call. For example, if no method is more specific, a primitive `double` variable can be passed to a method that takes a `Number` argument; because the wrapper class `Double` extends `Number`. Additionally, if no method is more specific, it could be auto-boxed and passed to a method that takes an argument typed as `Object`.
* The `AtomicInteger` class (and others like it) provide `incrementAndGet` and `addAndGet` methods, for manipulating the underlying value in a thread-safe way. Furthermore, the standard increment/decrement operators are not thread-safe, as they are **NOT** considered to be atomic in nature (i.e. they are actually 2 operations; a get and a set).

---
## Functional
* The `Function` interface takes 2 type params (arg and return value). The `BiFunction` interface takes 3 type params (2 arg types and return value).
* The `UnaryOperator` interface takes 1 type param (arg and return value are same type).
* The `BinaryOperator` interface also takes 1 type param (2 args and return value are same type).
* The `Runnnable` interface represents a single parameterless method that returns no value. (`void run()`)
* The `Callable` interface takes 1 type param (return value) and represents a single parameterless method that returns a value. (`V call() throws Exception`)

---
## Strings and StringBuilder
* `String` and `StringBuilder` are final classes and cannot be inherited/extended
* `String` does **NOT** have a method named `append`.  `StringBuilder` and `StringBuffer` do, however. Additionally, `String` does have a `concat` method which returns a new `String` with the provided `String` argument appended to the original.

---
## Localization/Internationalization
* To format values using a locale other than the default system locale, the `Locale` value must be provided when initializing the formatter (i.e. provided to the factory methods or ctors).  It cannot be set (or passed as an argument to any of the formatter's methods) after initialization of the formatter.
* The `DateFormat` class is abstract and has factory methods for obtaining instances of various specific implementations, such as `SimpleDateFormat`.  The specific implementations are concrete classes and thus have constructors. However, it is recommended to use the factory methods on `DateFormat`.
* The various factory methods provided by `DateFormat` (ex: `getTimeInstance`, `getDateInstance` or `getDateTimeInstance`) sometimes take a `style` argument.  This argument is used to specify the formatting style to be used. (ex: `DateFormat.SHORT` for "M/d/yy" in the US locale)
* The `Locale` class does have ctors (ex: `Locale(String language)` and `Locale(String language, String country)`). However, they are deprecated and it is recommended to use the `static` factory methods, such as `Locale.getDefault` and `Locale.of`.
* Resource bundles are chosen based soley on the values of the provided `Locale` (or the default `Locale` if none is explicitly provided). Values (language or country) are **NOT** inferred from the default if not provided by the specified `Locale`. For example, if the specified `Locale` doesn't have a country value, the resource bundle that is chosen will be either the default resource bundle, or the resource bundle with only the language (and no country) in the name. In other words, the country value from the default `Locale` will not be used to fill in that blank when the JVM selects the resource bundle file.
* If a `Locale` is provided by to the `ResourceBundle.getBundle` method, and then a resource is requested that does **NOT** exist in a matching resource file, it will attempt to load the resource from the default resource file without a language or country code in the name. It will **NOT** attempt to load the resource from the file with the language and/or country code of the default `Locale`.
* The `parse` method of the various formatters are declared as throwing `java.text.ParseException`, which is a checked exception. So, code that calls it must handle that exception properly or compilation will fail.
* The `Float.parseFloat` method will correctly parse the `String` values of `"Infinity"` and `"-Infinity"` and return the values of `Float.INFINITY` and `Float.NEGATIVE_INFINITY` respectively.
* The `Float.parseFloat` method throws `NumberFormatException`, which is an unchecked exception; **UNLIKE** the `parse` methods of `NumberFormat` and its subtypes.

---
## Enums
* An enum ctor is always implicitly `private`. You cannot make it `public` or `protected`.
* Enum value constants must always be declared before anything else.
* Enums are final (i.e. cannot be inherited) and also cannot inherit from other types, because they implicitly inherit from the `Enum` abstract class. However, they can implement any number of interfaces.

---
## Records
* If declared, the compact constructor does not have parenthesis in its declaration.
  ```java
  record MyRecord(int foo, float bar) {
    /* VALID compact record constructor syntax */
    public MyRecord {
      // ...initialization code...
    }
  }
  ```
* You cannot declare instance fields on `Record` classes. This will cause compilation to fail. However, you can declare `static` fields and `static`/instance methods.
* Instances of `Record` classes are considered immutable. However, any classes used as types for the record class' fields may still be mutable.
* The implicit getter methods on a `Record` class are named the same as the fields listed in the `Record` class' declaration signature.
  ```java
  record Foo(String bar) {} // will have a `bar()` getter method
  ```

---
## Optional
* Calling the `get` method (or any of its variants like `OptionaDouble.getAsDouble`) on an empty `Optional`, will throw a `NoSuchElementException`.
* Use `Optional.ofNullable` to accommodate potentially `null` values. Calling `Optional.of` with a value of `null` will throw an exception at run time.

---
## Exception Handling
* Checked exceptions are exceptions that are caused by things external to the program (ex: user input, network/IO errors, etc...) and thus must be expected and handled explicitly.
* Unchecked exceptions are exceptions that are caused by things internal to the program (ex: invalid code assumptions like improper type casting). They are not required to be handled explicitly.
* Multiple exceptions can be specified in a catch clause by using a single pipe char to separate them before declaring the variable name.
  ```java
  try {
   // ...code that throws...
  } catch (java.io.FileNotFoundException | java.io.InvalidClassException ex) { // multi-catch
   // ...exception handling logic...
  }
  ```
* When using a multi-exception catch clause, you **CANNOT** include exceptions are are subclasses of other exceptions in the list.
  ```java
  try {
    // ...code...
  } catch (IOException | FileNotFoundException ex) { // invalid because FileNotFoundException extends IOException
    // ...exception handling logic...
  }
  ```
* The exception parameter in a multi-catch clause is implicitly final. Therefore, it **CANNOT** be reassigned within the catch block.
* Look for situations where checked exceptions are caught, but there is no code in the `try-block` that throws the caught exception type(s) or their subclasses. This will cause compilation to fail. The only exception to this rule is the `Exception` class or its super-types.
* A `return` statement in a `finally` block supersedes any `return` statement in the `try` or `catch` blocks.  If fact, it supersedes a `return` statement that would be executed after the `try-catch-finally` scope and thus the subsequent `return` statement would be unreachable; causing a compilation error, unless the `try-catch-finaly` block is wrapped in a conditional and the subsequent `return` statement is not inside the scope of said conditional block.
* A regular `try-catch` block requires either a `catch` or `finally` clause (or both). A `try-with-resources` block does **NOT** require a `catch` or `finally` clause.
* Be sure that classes that are initialized in a `try-with-resources` initialization clause implement `AutoCloseable`.
* Resources initialized via a `try-with-resources` block must be effectively `final`, if using a previously declared variable for the specified resource.  In other words, you cannot initialize a previously declared variable within the `try-with-resources` initialization clause. It must be initialized before the `try-with-resources` block.  To initialize the resource without previously declaring it, it must be declared within the `try-with-resources` initializer clause.
* Resources initialized in a `try-with-resources` block are closed before the `catch` or `finally` blocks are executed; and any exceptions thrown during their closing are suppressed and available via the `getSuppressed()` method on the caught exception (inherited from `Throwable`).
* When overriding a method, the method can only declare no exceptions to be thrown **OR** declare that the same exceptions as, or any subclasses of, the overridden method's exceptions are thrown.
* When defining a ctor of a subclass, the ctor **MUST** declare that it throws the same (or superclasses of) exceptions of the superclass' ctor. If it declares the aforementioned exceptions in its throws-clause, it can declare any other exceptions.  The reason for this is that the ctor of a subclass is usually called explicitly, instead of through a virtual call on a variable instance declared as a supertype of a set of other subclasses. For example, in `Book b = new Encyclopedia(); b.getName();`, the ctor for `Encyclopedia` is being called explicitly, whereas the `getName` method might be overridden in `Encyclopedia` and thus could be a virtual call. In this example, the ctor of `Encyclopedia` must call the ctor of `Book` and thus it must declare the same (or supers of) exceptions as the `Book` ctor. On the other hand, to facilitate polymorphism, the rule for methods is the opposite (same exceptions or subclasses of overridden method); because it enables consumers to reference the super types while still maintaining exceptional determinism.
* The `java.lang.OutOfMemoryError` class is a subclass of `java.lang.Error`; and **NOT** a subclass of `RuntimeException` or even `Exception`.
* If the expression given to a `throw` statement results in `null`, it will throw a `java.lang.NullPointerException`.

---
## Arrays and Collections
* Collections can only contain reference types; not primitives. (ex: `List<Integer>` is valid, but `List<int>` is **NOT** valid)
* Collections typically have a `size()` method (**NOT** `length()`)
* The `indexOf` method on Collections typically takes a parameter of type `Object`. So any type can be passed to it. The same goes for the `contains` method.
* Array has a `length` **property** (**NOT** a `length()` method or `size()` method).
* You cannot use brackets `[]` alongside the `var` keyword in variable declaration.
  ```java
  var i[] = new int[]; // INVALID
  var i = new int[]; // VALID
  ```
* You cannot declare an array using the `var` keyword on the left side of the declaration if the type cannot be inferred from the initialization (right side).
  ```java
  var i = {1, 2, 3} // INVALID
  var i = new int[] {1, 2, 3} // VALID
  ```
* Array size can only be pre-defined in the initializer (right side); not on the variable declaration (left side). Also, you cannot use an initializer block when the size is pre-defined.
  ```java
  String [3] sa = new String[]; // INVALID
  String [] sa = new String[3]; // VALID
  /* The following is invalid and will NOT compile, because the size is specified and an initializer is used: */
  String[] sa = new String[3]{ "a", "b", "c"}; 
  ```
* `Arrays.compare` returns zero if the given arrays are equal and contain the same elements in the same order; a value less than zero if the first array is lexicographically less than the second array; and a value greater than zero if the first array is lexicographically greater than the second array.
* `Arrays.mismatch` finds and returns the index of the first mismatch between two int arrays, otherwise returns `-1` if no mismatch is found.
* When you split a `Spliterator`, the first half of the elements goes to the resulting (new) `Spliterator` while the remaining half remains with the origininal one.
* The `Spliterator` class has a `forEachRemaining` method; which iterates over the remaining elements referenced by the `Spliterator`.
* In order to sort collections, the element type must implement the `Comparable` interface.  Calling the `sort` method on collections of elements whose type doesn't implement `Comparable` will throw a `ClassCastException` at run time.
* Both `List.of` and `List.copyOf` methods return an unmodifiable `List`. Calling modification methods, such as `sort` will throw an `java.lang.UnsupportedOperationException` at runtime.
* The `List.sort` method requires that a comparator be specified via its first param. There is NO parameterless overload of this method.
* The `ArrayList` class has a `removeIf` method that takes a predicate which removes all elements that satisfy the predicate provided via its first argument.
* Queue & Deque (double-ended queue) Common Methods:
  * `addFirst`, `addLast`: Adds element to front and end of queue respectively; throwing exception if full.
  * `offerFirst`, `offerLast`: Same as `addFirst`, `addLast`; except returns a `boolean` if the element was added instead of throwing exception.
  * `removeFirst`, `removeLast`: Removes and returns the first or last element; throwing exception if empty.
  * `pollFirst`, `pollLast`: Same as `removeFirst`, `removeLast`; except returns `null` instead of throwing exception if empty.
  * `getFirst`, `getLast`: Same as `removeFirst`, `removeLast`; except doesn't remove the element (throws if empty).
  * `peekFirst`, `peekLast`: Same as `getFirst`, `getLast`; except returns `null` instead of throwing.
  * Other common `Collection` methods...

---
## Streams
* `Stream.count()` returns a `long` primitive value. The same can be said for `Collectors.counting`.
* Execution of intermediate operations on streams is controlled by the terminal operation. For example, the `allMatch` terminal operation is a short-circuiting operation and thus it determines how many iterations of the stream operations execute. If a false result is produced by the predicate passed to `allMatch`, it stops execution.
* Look for trick questions with streams that don't end in a terminal operation; asking what the output will be. There will be **NO** output, because a terminal operation is required to execute the stream and any of its intermediate operations.
* The `reduce` operation that takes an _identity_ value returns the final result instead of an optional containing the result; because it will use the _identity_ value as the default value if no values result from the source or previous operations.
* In parallel streams, the identity value provided to a `reduce` operation may be used an unknown number of times as part of the calculation, because a parallel stream may be split into unknown number of sub-streams and executed independently and thus the number of times the identity value is used is dependent on how many sub-streams are spun up.
* There exists a `Collectors.joining` method that takes zero parameters, which will attempt to concatenate the string values resulting from a stream.
  ```java
  values.stream().collect(Collectors.joining());
  ```
  There is also a flavor of `Collectors.joining` that takes a single `String` argument that is used as a delimiter between the values.
* The specialized primitive streams (ex: `IntStream`) have a `mapToObj` method, which can be used to access some generic `Stream` methods that aren't available on the original, specialized stream. They also have a `boxed` method that essentially does the same, but without specifying a mapping function.
* The specialized primitive streams do **NOT** have a `collect` method that takes a `Collector`. Use the `mapToObj` or `boxed` methods to gain access to the `collect` method.
* The specialized primitive streams have reducing methods, such as `average`, which return an `Optional`. (ex: `IntStream.average` returns an `OptionalDouble`). Therefore, to get the resulting primitive value, one must call the `getAsDouble` method on the returned `OptionalDouble` value.
  ```java
  double average = nums.parallel().mapToDouble(i->i).average().getAsDouble();  // VALID
  OptionalDouble average = nums.parallel().mapToDouble(i->i).average(); // ALSO VALID
  double average = nums.parallel().mapToDouble(i->i).average(); // INVALID, will NOT compile
  ```
* The specialized primitive streams do **NOT** have a `parallelStream` method. They **DO** have a `parallel` method that converts the stream to a parallel stream.
* Calling `sorted()` on unlimited streams cause an `OutOfMemoryError` to be thrown, because it will accumulate an infinite call stack.
  ```java
  /* OutOfMemoryError will be thrown when the below statement is executed. */
  LongStream.generate(() -> ThreadLocalRandom.current().nextLong() % 100).sorted();
  ```
* Both index params for IntStream.rangeClosed are **INCLUSIVE**; whereas the ending index param for IntStream.range (default impl) is **EXCLUSIVE**.
* Look for trick questions where a "reader" stream is being passed to a "writer" constructor.
  ```java
  var foo = new PrintWriter(new FileReader(...)); // INVALID, will NOT compile
  ```

---
## Generics
* When accessing collections with wildcard type specs, if the type spec is for super-types (ex: `Collection<? super Number>`), it is typically being used for output (i.e. adding to the collection).  If the type spec is for sub-types, (ex: `Collection<? extends Number>`), it is typically used for input (i.e. reading from the collection).  The reason is that you could only use the type `Object` for a variable receiving a value from the collection using a super-type wildcard spec; because the compiler can't know the exact type referenced by the type-spec (it could be `Object` or any other type in the lineage). However, if the type-spec is for sub-types (`extends`), the compiler can be sure that the type referenced by the type-spec is at least the type being extended; and thus a value from the collection can be referenced into a variable of the type being extended.

---
## Date and Time
* `Period` is used to manipulate dates in terms of days, months, and years; while `Duration` is used to manipulate dates in terms of hours, minutes, and seconds. Therefore, `Period` doesn't affect the time component of the date while `Duration` may change the time component if the date is close to the DST boundary.
* When using a `DateTimeFormatter` and a custom format string, to get the calendar year, use lower case `y`.  Upper case `Y` is used to output the week year.
* Pay close attention to invalid formatters being used when formatting date-type objects.  For example, you cannot use `DateTimeFormatter.ISO_DATE_TIME` to format a `LocalDate` instance, because `LocalDate` does not contain a time component.
* Applying a zone or offset to a date/time does not change the time component. It only sets the zone ID or offset of the date/time value. For example, the result of `LocalDateTime.atZone(ZoneId.of("America/New_York"))` will only set the timezone. The time (i.e. the hour/minute values) will remain the same. So, a date/time with an offset of `-4` applied will come before that same date/time with an offset of `-5` applied (ex: 6AM at `-4` is before 6AM at `-5`).

---
## Threads
* Thread states are: `NEW`, `RUNNABLE`, `BLOCKED`, `WAITING`, `TIMED_WAITING`, `TERMINATED`
* If a thread is interrupted while it is in either of the waiting states, an `InterruptedException` is thrown. If the thread is in `RUNNABLE` state, no exception is thrown, but its interrupted status property is set to true (accessible via the `interrupted()` method); and its state doesn't change (still `RUNNABLE`) until the thread finishes.
* A virtual thread always has the same priority and it is set to `Thread.NORM_PRIORITY`. A call to change the priority using the `setPriority` method is ignored (no exception is thrown).
* The `ExecutorService` (created via the methods provided by the factory class `Executors`) has a `submit` method that can take either a `Runnable` or `Callable`; and returns a `Future<?>` or `Future<T>` respectively.
* The `ExecutorService.shutdown` method initiates an orderly shutdown where previously submitted tasks are executed, but no new tasks will be accepted.
* The `ExecutorService.shutdownNow` method attempts to stop all actively executing tasks, halts the processing of waiting tasks, and returns a list of tasks that were awaiting execution.
* Implementations may be instantiated via the following factory methods on the `Executors` class:
  * `newFixedThreadPool(int nThreads)`: Creates a thread pool with a fixed number of threads.
  * `newCachedThreadPool()`: Creates a thread pool that creates new threads as needed, but reuses previously constructed threads when they are available. Threads that have not been used for 60 seconds are terminated and removed from the cache.
  * `newSingleThreadExecutor()`: Creates an `Executor` that uses a single worker thread operating off an unbounded queue.
  * `newWorkStealingPool()`: Creates a thread pool that maintains enough threads to support the specified parallelism level, and may use multiple queues to reduce contention.
  * `newVirtualThreadPerTaskExecutor()`: Creates an `Executor` that starts a new virtual thread for each task.
  * `newScheduledThreadPool(int corePoolSize)`: Creates a thread pool that can schedule commands to run after a given delay, or to execute periodically.
* The `ReentrantLock.lock` method can be called multiple times. However, the `unlock` method must also be called the same number of times to fully release the lock.
* The `ReentrantLock.lock` method blocks the current thread, if another thread has already acquired the lock, until the lock has been released by the other thread.
* The `ReentrantLock.tryLock` method does **NOT** block the current thread; and returns a `boolean` indicating whether the lock was successfully acquired by the current thread.

---
## IO, Files, and Paths
* The `Console` singleton is meant to interact with the user, typically via a terminal window and keyboard. Thus, it only provides methods for reading and writing character data; and **NOT** binary data.
* To obtain an instance of the `Path` class, use the static factory method `get` on the `Paths` class (ex: `var myPath = Paths.get("/temp");`); or the various instance methods on other related classes such as the `File` class' `toPath` method.
* `Path.getRoot()` returns the slash (because on unix based systems, an empty string would be returned if it didn't include the slash)
  ```java
  // On Windows:
  var foo = Paths.get("C:\\temp");
  System.out.println(foo.getRoot()); // outputs `C:\`

  // On Nix:
  var bar = Paths.get("/temp");
  System.out.println(bar.getRoot()); // outputs `/`
  ```
* The `File` class is immutable.
* To obtain an instance of the Console object, call `System.console()`. Remember that `java.io.Console` is a singleton class and all of its constructors are `private`.
* `PrintWriter` does **NOT** have `write[Primitive]` methods such as `writeInt`, `writeBoolean`, and `writeLong`. It has overloaded `print` methods for writing various primitives.
* The `PrintWriter.write` methods are used to write character values (single, array, or `String` forms) to files, using the specified or default encoding. The `PrintWriter.print` methods are used to write string representations (i.e. the result of `String.valueOf(valueToBeWritten)`) of primitives or objects, using the specified or default encoding.
* Remember that the `PrintWriter`'s `print` and `write` methods do **NOT** throw I/O exceptions (although some of its constructors may). This is unlike other streams, where you need to include exception handling (i.e. a try/catch or throws clause) when you use the stream. It does have `checkError`, `clearError`, and `setError` methods for general error handling purposes.
* `OutputStream` has `write` methods that write bytes to the output. It also has a `flush` method and a `close` method.
* The `DataOutputStream` class provides methods such as `writeInt`, `writeChar`, and `writeDouble`, for writing binary-serialized values of primitives to a file. It also has read method counterparts, such as `readInt`.
* Glob pattern syntax
  * `*.java` : Matches a path that represents a file name ending in `.java`
  * `*.*` : Matches file names containing a dot
  * `*.{java,class}` : Matches file names ending with `.java` or `.class` (curly braces define a list of literals, separated by commas)
  * `foo.?` : Matches file names starting with `foo.` and a single character extension
* Creating an instance of the `RandomAccessFile` class while specifying access mode containing the character `w`, will attempt to create the specified file if it does not exist. The permitted values for the access mode are as follows:
  * `r`: Readonly access. Invoking any of the write methods on an instance of the class will throw an `IOException`.
  * `rw`: Read/write access. If the file does not already exist an attempt will be made to create it.
  * `rws`: Read/write access; and updates to the file's content or metadata will be written synchronously.
  * `rwd` Read/write access; and updates to the file's content will be written synchronously.

---
## Java Modules
* Module directories are **NOT** exploded. In other words, the output of compiling a module named `x.y` will be in a folder called `x.y` (ex: `C:\temp\x.y\Main.class`). The same goes for compilation input paths.
* A module **CANNOT** specify more than one `provides` directive, in its module declaration, that specifies the **SAME** service. In other words, it can only provide a **SINGLE** implementation for any service via the module declaration.
* You **CANNOT** have multiple `exports` directive specifying the same package.
* The `java.util` package(s) (among others) are provided by the standard module `java.base`.
* The `java.base` module is always listed as a dependency of all classes/modules by the various standard Java command line tools.
* For a type to be used as a _provider_, in a module's `provides` directive, it must adhere to the following rules:
  * If the type itself is intended to be instantiated, it must provide a parameterless constructor (or declare no constructor), **AND** must be a subtype of the _service_ specified in the `provides` directive. Otherwise a compile-time error occurs.
  * If the type is intended to be used as a factory-style provider, it must declare a `public static` method called `provider` with no formal parameters. This is called the _provider method_. The _provider method_ must declare a return type that is a subtype of the _service_ specified in the `provides` directive. The return type must also be accessible to the code in the module from which the _provider_ is being provided. It can be declared in another module, as long as it meets the aforementioned code accessibility requirements.
  * A _provider_ that is being specified by a `provides` directive must be declared in the current module being defined. However, the return type of its _provider method_ may be declared in another module.

---
## Command Line Tools
* The `jdeps --list-deps moduleA.jar` command lists the module dependencies and pkg names of JDK internal APIs (if referenced). If `moduleA` requires any other application module, and those modules aren't specified via the `--module-path` param, an error will be thrown.
* The `jmod` tool is used to package module files into jmod archives. (Knowledge thereof not required for exam)

---
## Area Specific (Database connections, etc...)
* Indexing of DB columns in a `ResultSet` starts with `1` (not zero). So calling getter methods like this `getString(0)` will always throw an `SQLException` at runtime.