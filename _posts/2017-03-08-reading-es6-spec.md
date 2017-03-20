# Index

1. Scope
2. Conformance
3. Normative references
4. Overview
    1. Terms and definitions
5. Notational Conventions

-----

6. ECMAScript Data Types and Values
7. Abstract Operations
    1. Type Conversion
    2. Testing and Comparison Operations
    3. Operations on Objects
    4. Operations on Iterator Objects
8. Executable Code and Execution Contexts
    1. Lexical Environments
    2. Code Realms
    3. Execution Contexts
    4. Jobs and Job Queues
    5. ECMAScript Initialization()
9. Ordinary and Exotic Objects Behaviours

-----

10. ECMAScript Language: Source Code
11. ECMAScript Language: Lexical Grammar
12. ECMAScript Language: Expressions
13. ECMAScript Language: Statements and Declarations
14. ECMAScript Language: Functions and Classes
15. ECMAScript Language: Scripts and Modules
16. Error Handling and Language Extensions

-----

17. ECMAScript Standard Built-in Objects
18. The Global Object
19. Fundamental Objects
20. Numbers and Dates
21. Text Processing
22. Indexed Collections
23. Keyed Collection
24. Structured Data
25. Control Abstraction Objects
26. Reflection

----

# 4 Overview

ECMAScript is an object-oriented programming language for performing computations and manipulating computational objects within a host environment.
ECMAScript as defined here is not intended to be computationally self-sufficient;
indeed, there are no provisions in this specification for input of external data or output of computed results.

Instead, it is expected that the computational environment of an ECMAScript program will provide not only the objects and other facilities described in this specification but also certain environment-specific objects, whose description and behaviour are beyond the scope of this specification except to indicate that they may provide certain properties that can be accessed and certain functions that can be called from an ECMAScript program.

ECMAScript was originally designed to be used as a scripting language, but has become widely used as a general purpose programming language. A `scripting language` is a programming language that is used to manipulate, customize, and automate the facilities of an existing system. In such systems, useful functionality is already available through a user interface, and the scripting language is a mechanism for exposing that functionality to program control. In this way, the existing system is said to provide a host environment of objects and facilities, which completes the capabilities of the scripting language.

ECMAScript is object-based: basic language and host facilities are provided by objects, and an ECMAScript program is a cluster of communicating objects.
In ECMAScript, an object is a collection of zero or more properties each with attributes that determine how each property can be used.
Properties are containers that hold other objects, primitive values, or functions.
A primitive value is a member of one of the following built-in types: Undefined, Null, Boolean, Number, String, and Symbol;
an object is a member of the built-in type Object;
and a function is a callable object.
A function that is associated with an object via a property is called a method.

ECMAScript defines a collection of built-in objects that round out the definition of ECMAScript entities.
ECMAScript also defines a set of built-in operators.
Large ECMAScript programs are supported by modules which allow a program to be divided into multiple sequences of statements and declarations.

## 4.1 Objects

Even though ECMAScript includes syntax for class definitions, ECMAScript objects are not fundamentally class-based. Instead objects may be created in various ways including via a literal notation or via constructors which create objects and then execute code that initializes all or part of them by assigning initial values to their properties.

Each constructor is a function that has a property named "prototype" that is used to implement prototype-based inheritance and shared properties. Objects are created by using constructors in new expressions. Invoking a constructor without using new has consequences that depend on the constructor.

Every object created by a constructor has an implicit reference (called the object’s prototype) to the value of its constructor’s "prototype" property.
Furthermore, a prototype may have a non-null implicit reference to its prototype, and so on;
this is called the prototype chain.
When a reference is made to a property in an object,
that reference is to the property of that name in the first object in the prototype chain that contains a property of that name.

In a class-based object-oriented language, in general, state is carried by instances, methods are carried by classes, and inheritance is only of structure and behaviour. In ECMAScript, the state and methods are carried by objects, while structure, behaviour, and state are all inherited.

Unlike most class-based object languages, properties can be added to objects dynamically by assigning values to them.

# 5 Notational Conventions

## 5.1 Syntactic and Lexical Grammars

### 5.1.1 Context-Free Grammars

A context-free grammar consists of a number of productions.
Each production has an abstract symbol called a nonterminal as its left-hand side,
and a sequence of zero or more nonterminal and terminal symbols as its right-hand side.

For each grammar, the terminal symbols are drawn from a specified alphabet.

Starting from a sentence consisting of a single distinguished nonterminal, called the goal symbol, a given context-free grammar specifies a language, namely, the (perhaps infinite) set of possible sequences of terminal symbols.

### 5.1.2 The Lexical and RegExp Grammars

A lexical grammar for ECMAScript has Unicode code points as its terminal symbols.
It defines a set of productions that describe how sequences(为什么是复数) of such code points are translated into a sequence of input elements.

Input elements other than white space and comments form the terminal symbols for the syntactic grammar for ECMAScript and are called ECMAScript tokens. These tokens are the reserved words, identifiers, literals, and punctuators of the ECMAScript language. Moreover, line terminators, although not considered to be tokens, also become part of the stream of input elements and guide the process of automatic semicolon insertion.

A RegExp grammar for ECMAScript also has the code points as its terminal symbols.
It defines a set of productions that describe how sequences of code points are translated into regular expression patterns.

### 5.1.3 The Numeric String Grammar

### 5.1.4 The Syntactic Grammar

The syntactic grammar for ECMAScript has ECMAScript tokens defined by the lexical grammar as its terminal symbols.

It defines a set of productions, starting from two alternative goal symbols Script and Module, that describe how sequences of tokens form syntactically correct independent components of ECMAScript programs.

When a stream of code points is to be parsed as an ECMAScript Script or Module, it is first converted to a stream of input elements by repeated application(为什么是重复的) of the lexical grammar; this stream of input elements is then parsed by a single application of the syntactic grammar. The input stream is syntactically in error if the tokens in the stream of input elements cannot be parsed as a single instance of the goal nonterminal (Script or Module), with no tokens left over.

### 5.1.5 Grammar Notation

The definition of a production is introduced by the name of the nonterminal being defined followed by one or more colons. One or more alternative right-hand sides for the nonterminal then follow on succeeding lines.

- A production may be parameterized.
- References to nonterminals on the right-hand side of a production can also be parameterized.
- Prefixing a parameter name with `?` on a right-hand side nonterminal reference
- a right-hand side alternative prefixed with `[+parameter]`
- a right-hand side alternative prefixed with `[~parameter]`
- `one of`
- `[empty]` appears as the right-hand side of a production
- `[lookahead ∉ set]` appears in the right-hand side of a production
- If the set consists of a single terminal the phrase `[lookahead ≠ terminal]` may be used.
- `[no LineTerminator here]` appears in the right-hand side of a production of the syntactic grammar
- `but not`

## 5.2 Algorithm Conventions

The specification often uses a numbered list to specify steps in an algorithm.
These algorithms are used to precisely specify the required semantics of ECMAScript language constructs.

Algorithms may be explicitly parameterized.
In order to facilitate their use in multiple parts of this specification,
some algorithms, called `abstract operations`,
are named and written in parameterized functional form so that they may be referenced by name from within other algorithms.

Algorithms may be associated with productions of one of the ECMAScript grammars.
A production that has multiple alternative definitions will typically have a distinct algorithm for each alternative.
When an algorithm is associated with a grammar production, it may reference the terminal and nonterminal symbols of the production alternative as if they were parameters of the algorithm.

When an algorithm is associated with a production alternative,
the alternative is typically shown without any “[ ]” grammar annotations.
Such annotations should only affect the syntactic recognition of the alternative
and have no effect on the associated semantics for the alternative.

Mathematical operations and the mathematical functions defined later should always be understood as computing exact mathematical results on mathematical real numbers, which unless otherwise noted do not include infinities and do not include a negative zero that is distinguished from positive zero.

Algorithms in this standard that model floating-point arithmetic include explicit steps,
where necessary, to handle infinities and signed zero and to perform rounding.
If a mathematical operation or function is applied to a floating-point number,
it should be understood as being applied to the exact mathematical value represented by that floating-point number;
such a floating-point number must be finite, and if it is +0 or −0 then the corresponding mathematical value is simply 0.

# 6 ECMAScript Data Types and Values

Algorithms within this specification manipulate values each of which has an associated type.
The possible value types are exactly those defined in this clause.
Types are further subclassified into ECMAScript language types and specification types.

Within this specification, the notation “Type(x)” is used as shorthand for “the type of x” where “type” refers to the ECMAScript language and specification types defined in this clause.
When the term “empty” is used as if it was naming a value, it is equivalent to saying “no value of any type”.

## 6.1 ECMAScript Language Types

An ECMAScript language type corresponds to values that are directly manipulated by an ECMAScript programmer using the ECMAScript language.
The ECMAScript language types are Undefined, Null, Boolean, String, Symbol, Number, and Object.
An ECMAScript language value is a value that is characterized by an ECMAScript language type.

### 6.1.1 The Undefined Type

The Undefined type has exactly one value, called `undefined`.
Any variable that has not been assigned a value has the value `undefined`.

### 6.1.2 The Null Type

The Null type has exactly one value, called `null`.

### 6.1.3 The Boolean Type

The Boolean type represents a logical entity having two values, called `true` and `false`.

### 6.1.4 The String Type

The String type is the set of all ordered sequences of zero or more 16-bit unsigned integer values (“elements”) up to a maximum length of 253-1 elements.
The String type is generally used to represent textual data in a running ECMAScript program,
in which case each element in the String is treated as a `UTF-16 code unit` value.
Each element is regarded as occupying a position within the sequence.
These positions are indexed with nonnegative integers.
The length of a String is the number of elements within it.
The empty String has length zero and therefore contains no elements.

Where ECMAScript operations interpret String values,
each element is interpreted as a single UTF-16 code unit.
However, ECMAScript does not place any restrictions or requirements
on the sequence of code units in a String value,
so they may be ill-formed when interpreted as UTF-16 code unit sequences.

Operations that do not interpret String contents treat them as
sequences of undifferentiated 16-bit unsigned integers.

Some operations interpret String contents as UTF-16 encoded Unicode code points.
In that case the interpretation is:

- A code unit in the range 0 to 0xD7FF or in the range 0xE000 to 0xFFFF is interpreted as a code point with the same value.
- A sequence of two code units, where the first code unit c1 is in the range 0xD800 to 0xDBFF and the second code unit c2 is in the range 0xDC00 to 0xDFFF,is a surrogate pair and is interpreted as a code point with the value (c1 - 0xD800) × 0x400 + (c2 – 0xDC00) + 0x10000.
- A code unit that is in the range 0xD800 to 0xDFFF, but is not part of a surrogate pair, is interpreted as a code point with the same value.

### 6.1.5 The Symbol Type

The Symbol type is the set of all non-String values that may be used as the key of an Object property.
Each possible Symbol value is unique and immutable.
Each Symbol value immutably holds an associated value called [[Description]] that is either undefined or a String value.

### 6.1.6 The Number Type

The Number type has exactly 2^64−2^53+3 values,
representing the double-precision 64-bit format IEEE 754-2008 values,
except that the 2^53−2 distinct “Not-a-Number” values of the IEEE Standard are represented in ECMAScript as a single special NaN value.To ECMAScript code, all NaN values are indistinguishable from each other.

all the positive and negative integers whose magnitude is no greater than 2^53 are representable in the Number type.

There are two other special values, called positive Infinity and negative Infinity.

The other 2^64−2^53 values are called the finite numbers.
Half of these are positive numbers and half are negative numbers;
for every finite positive Number value there is a corresponding negative value having the same magnitude.

Note that there is both a positive zero and a negative zero.

The procedure `the Number value for x` corresponds exactly to the behaviour of the IEEE 754-2008 “round to nearest, ties to even” mode.

### 6.1.7 The Object Type

An Object is logically a collection of properties.
Each property is either a data property, or an accessor property:

A data property associates a key value with an ECMAScript language value and a set of Boolean attributes.
An accessor property associates a key value with one or two accessor functions, and a set of Boolean attributes.
The accessor functions are used to store or retrieve an ECMAScript language value that is associated with the property.
Properties are identified using key values.
A property key value is either an ECMAScript String value or a Symbol value.
A `property name` is a property key that is a String value.

An `integer index` is a String-valued property key that is a canonical numeric String
and whose numeric value is either +0 or a positive integer ≤ 253−1.
An array index is an `integer index` whose numeric value i is in the range +0 ≤ i < 232−1.

Property keys are used to access properties and their values.
There are two kinds of access for properties: get and set.
The properties accessible via get and set access includes both own properties and inherited properties
Each own property of an object must each have a key value that is distinct from the key values of the other own properties of that object.

All objects are logically collections of properties, but there are multiple forms of objects that differ in their semantics for accessing and manipulating their properties.
Ordinary objects are the most common form of objects and have the default object semantics.
An exotic object is any form of object whose property semantics differ in any way from the default semantics.

#### 6.1.7.1 Property Attributes

Attributes are used in this specification to define and explain the state of Object properties.

A data property associates a key value with the attributes listed below.

- [[Value]]
- [[Writable]]
- [[Enumerable]]
- [[Configurable] , if this value is false, programmers can change [[Writable]] from true to false.

An accessor property associates a key value with the attributes listed below.

- [[Get]]
- [[Set]]
- [[Enumerable]]
- [[Configurable]]

#### 6.1.7.2 Object Internal Methods and Internal Slots

The actual semantics of objects, in ECMAScript, are specified via algorithms called internal methods.
Each object in an ECMAScript engine is associated with a set of internal methods that defines its runtime behaviour.
These internal methods are not part of the ECMAScript language.
They are defined by this specification purely for expository purposes.
However, each object within an implementation of ECMAScript must behave as specified by the internal methods associated with it.
The exact manner in which this is accomplished is determined by the implementation.

Internal method names are polymorphic.
This means that different object values may perform different algorithms
when a common internal method name is invoked upon them.
That actual object upon which an internal method is invoked is the “target” of the invocation.

Internal slots correspond to internal state that is associated with objects
and used by various ECMAScript specification algorithms.
Internal slots are not object properties and they are not inherited.
Depending upon the specific internal slot specification,
such state may consist of values of any ECMAScript language type
or of specific ECMAScript specification type values.
Unless explicitly specified otherwise,
internal slots are allocated as part of the process of creating an object
and may not be dynamically added to an object.
Unless specified otherwise, the initial value of an internal slot is the value undefined.
Various algorithms within this specification create objects that have internal slots.
However, the ECMAScript language provides no direct way to associate internal slots with an object.

Internal methods and internal slots are identified within this specification using `[[ ]]`.
An internal method implicitly returns a `Completion Record`.
In addition to its parameters, an internal method always has access to the object that is the target of the method invocation.

Essential Internal Methods

- [[GetPrototypeOf]]
- [[SetPrototypeOf]]
- [[IsExtensible]]
- [[PreventExtensions]]
- [[GetOwnProperty]]
- [[HasProperty]]
- [[Get]]
- [[Set]]
- [[Delete]]
- [[DefineOwnProperty]]
- [[Enumerate]]
- [[OwnPropertyKeys]]

Additional Essential Internal Methods of Function Objects

- [[Call]], Objects that implement this internal method are callable.
- [[Construct]], Creates an object. Invoked via the new or super operators.

#### 6.1.7.3 Invariants of the Essential Internal Methods

The Internal Methods of Objects of an ECMAScript engine must conform to the list of invariants specified below.

省略部分，语义上的一些强制要求，但凡 ECMAScript engine 都必须实现且确保不能被绕过。

#### 6.1.7.4 Well-Known Intrinsic Objects

Well-known intrinsics are built-in objects
that are explicitly referenced by the algorithms of this specification
and which usually have Realm specific identities.
Unless otherwise specified
each intrinsic object actually corresponds to a set of similar objects, one per Realm.

省略 Intrinsic Objects 的表格

## 6.2 ECMAScript Specification Types

A specification type corresponds to meta-values that are used within algorithms to describe the semantics of ECMAScript language constructs and ECMAScript language types.
The specification types are `Reference`, `List`, `Completion`, `Property Descriptor`, `Lexical Environment`, `Environment Record`, and `Data Block`.
Specification type values are specification artefacts that do not necessarily correspond to any specific entity within an ECMAScript implementation.
Specification type values may be used to describe intermediate results of ECMAScript expression evaluation but such values cannot be stored as properties of objects or values of ECMAScript language variables.

### 6.2.1 The List and Record Specification Type

The List type is used to explain the evaluation of argument lists in new expressions, in function calls, and in other algorithms where a simple ordered list of values is needed.
Values of the List type are simply ordered sequences of list elements containing the individual values.

The Record type is used to describe data aggregations.
A Record type value consists of one or more named fields.
The value of each field is either an ECMAScript value or an abstract value.
Field names are always enclosed in double brackets, for example `[[value]]`.

### 6.2.2 The Completion Record Specification Type

The Completion type is a Record used to explain the runtime propagation of values
and control flow such as the behaviour of statements (break, continue, return and throw) that perform nonlocal transfers of control.

Completion Record Fields

- [[type]]
- [[value]]
- [[target]]

The term “abrupt completion” refers to any completion with a [[type]] value other than normal.

abstract operations

- NormalCompletion(argument)
- Implicit Completion Values
- Completion(completionRecord)
- Throw an Exception
- ReturnIfAbrupt
- UpdateEmpty(completionRecord, value)

### 6.2.3 The Reference Specification Type

The Reference type is used to explain the behaviour of such operators
as delete, typeof, the assignment operators, the super keyword and other language features.

A Reference is a resolved name or property binding.
A Reference consists of three components, the `base` value,
 the `referenced name` and the Boolean valued `strict reference` flag.
The `base` value is either undefined, an Object, a Boolean, a String, a Symbol, a Number, or an Environment Record.
A `base` value of undefined indicates that the Reference could not be resolved to a binding.
The `referenced name` is a String or Symbol value.

A Super Reference is a Reference that is used to represents a name binding that was expressed using the super keyword.
A Super Reference has an additional `thisValue` component and its `base` value will never be an Environment Record.

abstract operations to access the components of references:

- GetBase(V)
- GetReferencedName(V)
- IsStrictReference(V)
- HasPrimitiveBase(V)
- IsPropertyReference(V)
- IsUnresolvableReference(V)
- IsSuperReference(V)

abstract operations:

- GetValue(V)
- PutValue(V, W)
- GetThisValue(V)
- InitializeReferencedBinding(V, W)

### 6.2.4 The Property Descriptor Specification Type

The Property Descriptor type is used to explain the manipulation and reification of Object property attributes.
Values of the Property Descriptor type are Records.

Property Descriptor values may be further classified as data Property Descriptors and accessor Property Descriptors based upon the existence or use of certain fields.
A data Property Descriptor is one that includes any fields named either [[Value]] or [[Writable]].
An accessor Property Descriptor is one that includes any fields named either [[Get]] or [[Set]].
Any Property Descriptor may have fields named [[Enumerable]] and [[Configurable]].
A Property Descriptor value may not be both a data Property Descriptor and an accessor Property Descriptor; however, it may be neither.
A generic Property Descriptor is a Property Descriptor value that is neither a data Property Descriptor nor an accessor Property Descriptor.
A fully populated Property Descriptor is one that is either an accessor Property Descriptor or a data Property Descriptor and that has all of the fields that correspond to the property attributes defined in either Table 2 or Table 3.

abstract operations:

- IsAccessorDescriptor(Desc)
- IsDataDescriptor(Desc)
- IsGenericDescriptor(Desc)
- FromPropertyDescriptor(Desc)
- ToPropertyDescriptor(Obj)
- CompletePropertyDescriptor(Desc)

### 6.2.5 The Lexical Environment and Environment Record Specification Types

The Lexical Environment and Environment Record types are used to explain the behaviour of name resolution in nested functions and blocks.
These types and the operations upon them are defined in 8.1.

### 6.2.6 Data Blocks

The Data Block specification type is used to describe a distinct and mutable sequence of byte-sized (8 bit) numeric values.
A Data Block value is created with a fixed number of bytes that each have the initial value 0.

abstract operations:

- CreateByteDataBlock(size)
- CopyDataBlockBytes(toBlock, toIndex, fromBlock, fromIndex, count)

# 7 Abstract Operations

## 7.1 Type Conversion

- 7.1.1 ToPrimitive (input [, PreferredType]), `valueOf`, `toString`
- 7.1.2 ToBoolean (argument)
- 7.1.3 ToNumber (argument), Number value is not a mathematical value
- 7.1.4 ToInteger (argument)
- 7.1.5 ToInt32 (argument)
- 7.1.6 ToUint32 (argument)
- 7.1.11 ToUint8Clamp (argument) , 按照大小来
- 7.1.12 ToString (argument)
- 7.1.13 ToObject (argument)
- 7.1.14 ToPropertyKey (argument)
- 7.1.15 ToLength ( argument )
- 7.1.16 CanonicalNumericIndexString ( argument ), 命名没有看懂

## 7.2 Testing and Comparison Operations

- 7.2.1 RequireObjectCoercible ( argument )
- 7.2.2 IsArray ( argument )  `integer index` -->  `array index`
- 7.2.3 IsCallable ( argument )
- 7.2.4 IsConstructor ( argument )
- 7.2.5 IsExtensible (O)
- 7.2.6 IsInteger ( argument )
- 7.2.7 IsPropertyKey ( argument )
- 7.2.8 IsRegExp ( argument )
- 7.2.9 SameValue(x, y)
    - diffs from equal comparison
    - +0 is equal to -0 mathmatically, but not the same value in IEEE float-point Number represention.
- 7.2.10 SameValueZero(x, y)
    - means +0 and -0 are the same value in this abstract operation
- 7.2.11 Abstract Relational Comparison,`<`
    - ToPrimitive(x) and toPrimitive(y) first
    - if the result values are both String values, compare two string
    - else toNumber() both result values, and compare two number
    - notice either +0 < -0 or -0 < +0 returns false, because they are equal mathematically.
    - The comparison of Strings uses a simple lexicographic ordering on sequences of code unit values. no consideration of Unicode equivalence or Unicode code point values.
- 7.2.12 Abstract Equality Comparison    `==`
- 7.2.13 Strict Equality Comparison      `===`

## 7.3 Operations on Objects

- 7.3.1 Get (O, P)
- 7.3.2 GetV (V, P)
- 7.3.3 Set (O, P, V, Throw)
- 7.3.4 CreateDataProperty (O, P, V)
- 7.3.5 CreateMethodProperty (O, P, V)
- 7.3.6 CreateDataPropertyOrThrow (O, P, V)
- 7.3.7 DefinePropertyOrThrow (O, P, desc)
- 7.3.8 DeletePropertyOrThrow (O, P)
- 7.3.9 GetMethod (O, P)
- 7.3.10 HasProperty (O, P)
- 7.3.11 HasOwnProperty (O, P)
- 7.3.12 Call(F, V, [argumentsList])
- 7.3.13 Construct (F, [argumentsList], [newTarget])
- 7.3.14 SetIntegrityLevel (O, level)
- 7.3.15 TestIntegrityLevel (O, level)
- 7.3.16 CreateArrayFromList (elements)
- 7.3.17 CreateListFromArrayLike (obj [, elementTypes] )
- 7.3.18 Invoke(O,P, [argumentsList])
- 7.3.19 OrdinaryHasInstance (C, O)
- 7.3.20 SpeciesConstructor ( O, defaultConstructor )
- 7.3.21 EnumerableOwnNames (O)
- 7.3.22 GetFunctionRealm ( obj )

## 7.4 Operations on Iterator Objects

- 7.4.1 GetIterator ( obj, method )
- 7.4.2 IteratorNext ( iterator, value )
- 7.4.3 IteratorComplete ( iterResult )
- 7.4.4 IteratorValue ( iterResult )
- 7.4.5 IteratorStep ( iterator )
- 7.4.6 IteratorClose( iterator, completion )
- 7.4.7 CreateIterResultObject ( value, done )
- 7.4.8 CreateListIterator ( list )

# 8 Executable Code and Execution Contexts

## 8.1 Lexical Environments

A `Lexical Environment` is a specification type used to define the association of Identifiers to specific variables and functions based upon the lexical nesting structure of ECMAScript code.

A `Lexical Environment` consists of an Environment Record and a possibly null reference to an outer Lexical Environment.
Usually a Lexical Environment is associated with some specific syntactic structure such as a FunctionDeclaration, or a Catch clause of a TryStatement
and a new Lexical Environment is created each time such code is evaluated.

An Environment Record records the identifier bindings that are created within the scope of its associated Lexical Environment.
It is referred to as the Lexical Environment’s EnvironmentRecord

The outer environment reference is used to model the logical nesting of Lexical Environment values.
The outer reference of a (inner) Lexical Environment is a reference to the Lexical Environment that logically surrounds the inner Lexical Environment.
An outer Lexical Environment may, of course, have its own outer Lexical Environment.

A `global environment` is a Lexical Environment which does not have an outer environment.
The global environment’s outer environment reference is null.
A global environment’s EnvironmentRecord may be prepopulated with identifier bindings
and includes an associated global object whose properties provide some of the global environment’s identifier bindings.
This global object is the value of a global environment’s this binding.
As ECMAScript code is executed, additional properties may be added to the global object and the initial properties may be modified.

A `module environment` is a Lexical Environment that contains the bindings for the top level declarations of a Module.
It also contains the bindings that are explicitly imported by the Module.
The outer environment of a module environment is a global environment.

A `function environment` is a Lexical Environment that corresponds to the invocation of an ECMAScript function object.
A function environment may establish a new this binding.
A function environment also captures the state necessary to support super method invocations.

### 8.1.1 Environment Records

There are two primary kinds of Environment Record values used in this specification: declarative Environment Records and object Environment Records.

Declarative Environment Records are used to define the effect of ECMAScript syntactic elements such as FunctionDeclarations, VariableDeclarations, and Catch clauses that directly associate identifier bindings with ECMAScript language values.

Object Environment Records are used to define the effect of ECMAScript elements such as WithStatement that associate identifier bindings with the properties of some object.

Environment Record values can be thought of as existing in a simple object-oriented hierarchy
where Environment Record is an abstract class with three concrete subclasses,
`declarative Environment Record`, `object Environment Record`, and `global Environment Record`.
Function Environment Records and module Environment Records are subclasses of declarative Environment Record.

The abstract class includes the abstract specification methods defined in Table 15.
These abstract methods have distinct concrete algorithms for each of the concrete subclasses.

Table 15 - Abstract Methods of Environment Records

- HasBinding(N)
- CreateMutableBinding(N, D)
- CreateImmutableBinding(N, S)
- InitializeBinding(N,V)
- SetMutableBinding(N,V, S)
- GetBindingValue(N,S)
- DeleteBinding(N)
- HasThisBinding()
- HasSuperBinding()
- WithBaseObject()

#### 8.1.1.2 Object Environment Records

Each `object Environment Record` is associated with an object called its `binding object`.

An object Environment Record binds the set of string identifier names that directly correspond to the property names of its binding object.
Property keys that are not strings in the form of an `IdentifierName` are not included in the set of bound identifiers.
Both own and inherited properties are included in the set regardless of their [[Enumerable]] attribute.

Because properties can be dynamically added and deleted from objects,
the set of identifiers bound by an object Environment Record may potentially change as a side-effect of any operation that adds or deletes properties.
Any bindings that are created as a result of such a side-effect are considered to be a mutable binding
even if the Writable attribute of the corresponding property has the value `false`.
Immutable bindings do not exist for object Environment Records.

Object Environment Records created for `with` statements can provide their binding object as an implicit `this` value for use in function calls.
The capability is controlled by a `withEnvironment` Boolean value that is associated with each object Environment Record.

#### 8.1.1.3 Function Environment Records

A `function Environment Record` is a declarative Environment Record that is used to represent the top-level scope of a function and,
if the function is not an ArrowFunction, provides a `this` binding.
If a function is not an ArrowFunction function and references `super`,
its function Environment Record also contains the state that is used to perform super method invocations from within the function.

Function Environment Records have the additional state fields listed below.

- [[thisValue]]
- [[thisBindingStatus]]
- [[FunctionObject]]
- [[HomeObject]], the object that the function is bound to as a method, generally Constructor.prototype
- [[NewTarget]], the value of the [[Construct]] *newTarget* parameter

#### 8.1.1.4 Global Environment Records

A `global Environment Record` is used to represent the outer most scope
that is shared by all of the ECMAScript Script elements that are processed in a common Realm.
A global Environment Record provides the bindings for built-in globals, properties of the global object,
and for all top-level declarations that occur within a Script.

A global Environment Record is logically a single record
but it is specified as a composite encapsulating an object Environment Record and a declarative Environment Record.

The object Environment Record has as its base object the global object of the associated Realm.
This global object is the value returned by the global Environment Record’s GetThisBinding method.
The object Environment Record component of a global Environment Record contains the bindings for all built-in globals
and all bindings introduced by a FunctionDeclaration, GeneratorDeclaration, or VariableStatement contained in global code.

The bindings for all other ECMAScript declarations in global code are contained in the declarative Environment Record component of the global Environment Record.

Properties may be created directly on a global object.
Hence, the object Environment Record component of a global Environment Record may contain both bindings created explicitly by FunctionDeclaration, GeneratorDeclaration, or VariableDeclaration declarations and binding created implicitly as properties of the global object.
In order to identify which bindings were **explicitly created using declarations**,
a global Environment Record maintains a list of the names bound using its CreateGlobalVarBindings and CreateGlobalFunctionBindings methods.

Additional Fields of Global Environment Records

- [[ObjectRecord]]
- [[DeclarativeRecord]]
- [[VarNames]]

Additional Methods of Global Environment Records

- GetThisBinding()
- HasVarDeclaration(N), 包含 unctionDeclaration, GeneratorDeclaration, or VariableDeclaration
- HasLexicalDeclaration(N), created using a lexical declaration such as a LexicalDeclaration or a ClassDeclaration
- HasRestrictedGlobalProperty(N)，A global lexical binding may not be created that has the same name as a non-configurable property of the global object.
- CanDeclareGlobalVar(N)
- CanDeclareGlobalFunction(N)
- CreateGlobalVarBinding(N, D)
- CreateGlobalFunctionBinding(N, V, D)

DeclarativeRecord 是优先于 ObjectRecord 的，同名的 binding 在查询时会产生屏蔽效应。

#### 8.1.1.5 Module Environment Records

A module Environment Record is a declarative Environment Record that is used to represent the outer scope of an ECMAScript Module.
In additional to normal mutable and immutable bindings, module Environment Records also provide immutable import bindings
which are bindings that provide indirect access to a target binding that exists in another Environment Record.

In addition, module Environment Records support the methods:

- CreateImportBinding(N, M, N2)
- GetThisBinding()

### 8.1.2 Lexical Environment Operations

The following abstract operations are used in this specification to operate upon lexical environments:

#### 8.1.2.1 GetIdentifierReference(lex, name, strict)

The abstract operation GetIdentifierReference is called with a Lexical Environment lex, a String name, and a Boolean flag strict.

1. If lex is the value `null`, then
    1. Return a value of type Reference whose base value is undefined, whose referenced name is name, and whose strict reference flag is strict.
2. Let envRec be lex’s EnvironmentRecord.
3. Let exists be envRec.HasBinding(name).
4. ReturnIfAbrupt(exists).
5. If exists is `true`, then
    1. Return a value of type Reference whose base value is envRec, whose referenced name is name, and whose strict reference flag is strict.
6. Else
    1. Let `outer` be the value of lex’s outer environment reference.
    2. Return GetIdentifierReference(outer, name, strict).

#### 8.1.2.2 NewDeclarativeEnvironment(E)

When the abstract operation NewDeclarativeEnvironment is called with a Lexical Environment as argument E the following steps are performed:

1. Let env be a new Lexical Environment.
2. Let envRec be a new declarative Environment Record containing no bindings.
3. Set env’s EnvironmentRecord to be envRec.
4. Set the outer lexical environment reference of env to E.
5. Return env.

#### 8.1.2.3 NewObjectEnvironment(O, E)

省略

#### 8.1.2.4 NewFunctionEnvironment(F, newTarget)

1. Let env be a new Lexical Environment.
2. Let envRec be a new function Environment Record containing no bindings.
3. Set envRec.[[FunctionObject]] to F.
4. If F’s [[ThisMode]] internal slot is `lexical`, set envRec.[[thisBindingStatus]] to "lexical".
5. Else, Set envRec.[[thisBindingStatus]] to "uninitialized".
6. Let home be the value of F’s [[HomeObject]] internal slot.
7. Set envRec.[[HomeObject]] to home.
8. Set envRec.[[NewTarget]] to newTarget.
9. Set env’s EnvironmentRecord to be envRec.
10. Set the outer lexical environment reference of env to the value of F’s [[Environment]] internal slot.
11. Return env.

#### 8.1.2.5 NewGlobalEnvironment ( G )

省略

#### 8.1.2.6 NewModuleEnvironment (E)

省略

## 8.2 Code Realms

Before it is evaluated, all ECMAScript code must be associated with a Realm.
Conceptually, a realm consists of a set of intrinsic objects, an ECMAScript global environment,
all of the ECMAScript code that is loaded within the scope of that global environment,
and other associated state and resources.

A Realm is specified as a Record with the fields specified below:

- [[intrinsics]], Record whose field names are intrinsic keys and whose values are objects
- [[globalThis]], The global object for this Realm
- [[globalEnv]]
- [[templateMap]]

### 8.2.1 CreateRealm ( )

1. Let realmRec be a new Record.
2. Perform CreateIntrinsics(realmRec).
3. Set realmRec.[[globalThis]] to undefined.
4. Set realmRec.[[globalEnv]] to undefined.
5. Set realmRec.[[templateMap]] to a new empty List.
6. Return realmRec.

### 8.2.2 CreateIntrinsics(realmRec)

1. Let intrinsics be a new Record.
2. Set realmRec.[[intrinsics]] to intrinsics.
3. Let objProto be ObjectCreate(null).
4. Set intrinsics.[[%ObjectPrototype%]] to objProto.
5. ...
6. Return intrinsics.

### 8.2.3 SetRealmGlobalObject(realmRec, globalObj)

1. If globalObj is undefined, then
    1. Let intrinsics be realmRec.[[intrinsics]].
    2. Let globalObj be ObjectCreate(intrinsics.[[%ObjectPrototype%]]).
2. Set realmRec.[[globalThis]] to globalObj.
3. Let newGlobalEnv be NewGlobalEnvironment(globalObj).
4. Set realmRec.[[globalEnv]] to newGlobalEnv.
5. Return realmRec.

### 8.2.4 SetDefaultGlobalBindings (realmRec)

1. Let global be realmRec.[[globalThis]]
2. For each property of the Global Object specified in clause 18, do
    1. Let name be the String value of the property name.
    2. Let desc be the fully populated data property descriptor for the property.
    3. Let status be DefinePropertyOrThrow(global, name, desc).
    4. ReturnIfAbrupt(status).
3. Return global.

## 8.3 Execution Contexts

An execution context is a specification device that is used to track the runtime evaluation of code by an ECMAScript implementation.
At any point in time, there is at most one execution context that is actually executing code.
This is known as the running execution context.
A stack is used to track execution contexts.
The running execution context is always the top element of this stack.
A new execution context is created whenever control is transferred from the executable code associated with the currently running execution context
to executable code that is not associated with that execution context.
The newly created execution context is pushed onto the stack and becomes the running execution context.

An execution context contains whatever implementation specific state is necessary to track the execution progress of its associated code.
Each execution context has at least the state components listed in Table 22.

Table 22 — State Components for All Execution Contexts

- code evaluation state
- Function
- Realm
- LexicalEnvironment
- VariableEnvironment, Identifies the Lexical Environment whose EnvironmentRecord holds bindings created by VariableStatements.

Evaluation of code by the running execution context may be suspended at various points defined within this specification.
Once the running execution context has been suspended a different execution context may become the running execution context
and commence evaluating its code.
At some later time a suspended execution context may again become the running execution context
and continue evaluating its code at the point where it had previously been suspended.

The value of the Realm component of the running execution context is also called the `current Realm`.
The value of the Function component of the running execution context is also called the `active function object`.

The LexicalEnvironment and VariableEnvironment components of an execution context are always Lexical Environments.
When an execution context is created its LexicalEnvironment and VariableEnvironment components initially have the same value.

In most situations only the running execution context is directly manipulated by algorithms within this specification.
Hence when the terms “LexicalEnvironment”, and “VariableEnvironment” are used without qualification
they are in reference to those components of the running execution context.

Execution contexts representing the evaluation of generator objects have the additional state components Generator,
which is The GeneratorObject that this execution context is evaluating.

### 8.3.1 ResolveBinding(name, [env])

The ResolveBinding abstract operation is used to determine the binding of name passed as a String value.
The optional argument env can be used to explicitly provide the Lexical Environment that is to be searched for the binding.
`During execution of ECMAScript code`, ResolveBinding is performed using the following algorithm:

1. If env was not passed or if env is undefined, then
2. Let env be the running execution context’s LexicalEnvironment.
3. Assert: env is a Lexical Environment.
4. If the code is contained in strict mode code, let strict be true, else let strict be false.
5. Return GetIdentifierReference(env, name, strict ).

### 8.3.2 GetThisEnvironment()

finds the Environment Record that currently supplies the binding of the keyword `this`.

### 8.3.3 ResolveThisBinding()

determines the binding of the keyword this using the LexicalEnvironment of the running execution context.

### 8.3.4 GetNewTarget()

determines the NewTarget value using the LexicalEnvironment of the running execution context.

### 8.3.5 GetGlobalObject()

returns the global object used by the currently running execution context.

## 8.4 Jobs and Job Queues

A Job is an abstract operation that initiates an ECMAScript computation when no other ECMAScript computation is currently in progress.

Execution of a Job can be initiated only when there is no running execution context and the execution context stack is empty.
A PendingJob is a request for the future execution of a Job.
A PendingJob is an internal Record whose fields are specified in Table 25.
Once execution of a Job is initiated, the Job always executes to completion.
No other Job may be initiated until the currently running Job completes.
However, the currently running Job or external events may cause the enqueuing of additional PendingJobs.

Table 25 — PendingJob Record Fields

- [[Job]], The name of a Job abstract operation
- [[Arguments]]
- [[Realm]], The Realm for the initial execution context when this Pending Job is initiated.
- [[HostDefined]], Field reserved for use by host environments that need to associate additional information with a pending Job.

A Job Queue is a FIFO queue of PendingJob records.
Each Job Queue has a name and the full set of available Job Queues are defined by an ECMAScript implementation.
Every ECMAScript implementation has at least the Job Queues defined in Table 26.

Table 26 — Required Job Queues

- ScriptJobs, Jobs that validate and evaluate ECMAScript Script and Module source text.
- PromiseJobs, Jobs that are responses to the settlement of a Promise

A request for the future execution of a Job is made by enqueueing a PendingJob record that includes a Job abstract operation name and any necessary argument values.
When there is no running execution context and the execution context stack is empty,
the ECMAScript implementation removes the first PendingJob from a Job Queue
and uses the information contained in it to create an execution context and starts execution of the associated Job abstract operation.

The PendingJob records from a single Job Queue are always initiated in FIFO order.
This specification does not define the order in which multiple Job Queues are serviced.

NOTE:

Typically an ECMAScript implementation will have its Job Queues pre-initialized with at least one PendingJob
and one of those Jobs will be the first to be executed.

abstract operations used to create and manage Jobs and Job Queues:

- EnqueueJob(queueName, job, arguments)
    1. Let callerContext be the running execution context.
    2. Let callerRealm be callerContext’s Realm.
    3. Let pending be PendingJob{ [[Job]]: job, [[Arguments]]: arguments, [[Realm]]: callerRealm, [[HostDefined]]: undefined }.
    4. Add pending at the back of the Job Queue named by queueName.
    5. Return NormalCompletion(empty).
- NextJob result
    1. If result is an abrupt completion, perform implementation defined unhandled exception processing.
    2. Suspend the running execution context and remove it from the execution context stack.
    3. Assert: The execution context stack is now empty.
    4. Let nextPending be the next PendingJob.
    5. Let newContext be a new execution context.
    6. Set newContext’s Realm to nextPending.[[Realm]].
    7. Push newContext onto the execution context stack; newContext is now the running execution context.
    8. Perform the abstract operation named by nextPending.[[Job]] using the elements of nextPending.[[Arguments]] as its arguments.

## 8.5 ECMAScript Initialization()

An ECMAScript implementation performs the following steps prior to the execution of any Jobs or the evaluation of any ECMAScript code:

初始化 HostDefinedRealm (并在 realm 上初始化 globalObject) 和 对 source text 进行 EnqueueJob 操作

1. Let realm be CreateRealm().
2. Let newContext be a new execution context.
3. Set the Function of newContext to null.
4. Set the Realm of newContext to realm.
5. Push newContext onto the execution context stack; newContext is now the running execution context.
6. Perform InitializeHostDefinedRealm(realm).
7. Obtain the ECMAScript source texts for zero or more ECMAScript scripts and/or ECMAScript modules. For each such sourceText do,
    1. If sourceText is the source code of a script, then
        1. Perform EnqueueJob("ScriptJobs", ScriptEvaluationJob, « sourceText »).
    2. Else sourceText is the source code of a module,
        1. Perform EnqueueJob("ScriptJobs", TopLevelModuleEvaluationJob, « sourceText »).
8. NextJob NormalCompletion(undefined).

### 8.5.1 InitializeHostDefinedRealm(realm)

The abstract operation InitializeHostDefinedRealm with parameter realm performs the following steps:

1. If this implementation requires use of an exotic object to serve as realm’s global object, let global be such an object created in an implementation defined manner. Otherwise, let global be undefined indicating that an ordinary object should be created as the global object.
2. Perform SetRealmGlobalObject(realm, global).
3. Let globalObj be SetDefaultGlobalBindings(realm).
4. ReturnIfAbrupt(globalObj).
5. Create any implementation defined global object properties on globalObj.
6. Return NormalCompletion(undefined).
