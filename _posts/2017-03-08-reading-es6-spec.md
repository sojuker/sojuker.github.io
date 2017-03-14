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

A Lexical Environment is a specification type used to define the association of Identifiers to specific variables and functions based upon the lexical nesting structure of ECMAScript code. A Lexical Environment consists of an Environment Record and a possibly null reference to an outer Lexical Environment. Usually a Lexical Environment is associated with some specific syntactic structure of ECMAScript code such as a FunctionDeclaration, a BlockStatement, or a Catch clause of a TryStatement and a new Lexical Environment is created each time such code is evaluated.

An Environment Record records the identifier bindings that are created within the scope of its associated Lexical Environment. It is referred to as the Lexical Environment’s EnvironmentRecord

The outer environment reference is used to model the logical nesting of Lexical Environment values. The outer reference of a (inner) Lexical Environment is a reference to the Lexical Environment that logically surrounds the inner Lexical Environment. An outer Lexical Environment may, of course, have its own outer Lexical Environment. A Lexical Environment may serve as the outer environment for multiple inner Lexical Environments. For example, if a FunctionDeclaration contains two nested FunctionDeclarations then the Lexical Environments of each of the nested functions will have as their outer Lexical Environment the Lexical Environment of the current evaluation of the surrounding function.

A global environment is a Lexical Environment which does not have an outer environment. The global environment’s outer environment reference is null. A global environment’s EnvironmentRecord may be prepopulated with identifier bindings and includes an associated global object whose properties provide some of the global environment’s identifier bindings. This global object is the value of a global environment’s this binding. As ECMAScript code is executed, additional properties may be added to the global object and the initial properties may be modified.

A module environment is a Lexical Environment that contains the bindings for the top level declarations of a Module. It also contains the bindings that are explicitly imported by the Module. The outer environment of a module environment is a global environment.

A function environment is a Lexical Environment that corresponds to the invocation of an ECMAScript function object. A function environment may establish a new this binding. A function environment also captures the state necessary to support super method invocations.

Lexical Environments and Environment Record values are purely specification mechanisms and need not correspond to any specific artefact of an ECMAScript implementation. It is impossible for an ECMAScript program to directly access or manipulate such values.

### 8.1.1 Environment Records

There are two primary kinds of Environment Record values used in this specification: declarative Environment Records and object Environment Records. Declarative Environment Records are used to define the effect of ECMAScript language syntactic elements such as FunctionDeclarations, VariableDeclarations, and Catch clauses that directly associate identifier bindings with ECMAScript language values. Object Environment Records are used to define the effect of ECMAScript elements such as WithStatement that associate identifier bindings with the properties of some object. Global Environment Records and function Environment Records are specializations that are used for specifically for Script global declarations and for top-level declarations within functions.

For specification purposes Environment Record values are values of the Record specification type and can be thought of as existing in a simple object-oriented hierarchy where Environment Record is an abstract class with three concrete subclasses, declarative Environment Record, object Environment Record, and global Environment Record. Function Environment Records and module Environment Records are subclasses of declarative Environment Record. The abstract class includes the abstract specification methods defined in Table 15. These abstract methods have distinct concrete algorithms for each of the concrete subclasses.

Abstract Methods of Environment Records

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














