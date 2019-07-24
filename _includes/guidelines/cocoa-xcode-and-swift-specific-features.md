# Cocoa-, Xcode-, and Swift-Specific Features #
{% include dedicated-guide-page-link.html
    pageLinkFileName='Cocoa-Xcode-and-Swift-Specific-Features'
%}

## Selecting a kind of type ##

**Prefer using Structs, Protocols, and Extensions more than classes and subclasses**; Swift will behave more predictably and optimize better this way. It also allows for some patterns which are impossible with subclassing.




## Documentation Comments ##

> <span class="lozenge lozenge-amazing">GOLDEN RULE</span> <span class="good">**If you are having trouble describing your API&rsquo;s functionality in simple terms, you may have designed the wrong API.**</span>

Documentation comments are highly encouraged. We do not have any preferences of its style, so long as the doc comment is provided. You can view them in the Quick Help sidebar on the right of Xcode when the typing cursor is on the name, or by <kbd>⌥</kbd>-clicking the name.

For reference, here are a couple examples of documentation comments:



{% include code-example-table/combined-start.html
    left='Acceptable'
    left-class='good'
    left-comment='Sparse Doc Comment'
    right='Good'
    right-comment='Detailed Doc Comment'
 %}
```swift
/// English comment. **Markdown** is _supported!_
func foo(x: Bar) -> Baz

/// English comment. **Markdown** is _supported!_
var qux: Qux
```
{% include code-example-table/combined-cell-split.html %}
```swift
/// Fulltext English comment. **Markdown** is _supported!_
///
/// - Parameter x: Description of parameter
/// - Return: Description of return
func foo(x: Bar) -> Baz

/// Fulltext English comment. **Markdown** is _supported!_
///
/// - Note: Some subtle functionality/usage note
/// - Attention: Some important functionality/usage note
var qux: Qux
```
{% include code-example-table/combined-end.html %}


Note that documentation comments are _not_ to explain what something does in a way its name cannot, but instead to elaborate on what its name already says, including intended uses, parameter limits, possible return values, related code, external documentation, etc. **Do not use documentation comments as an excuse to poorly name anything.**

Also, be sure you know what something actually does before documenting it.

> <span class="lozenge lozenge-good">RULE-OF-THUMB</span> **Less documentation is better than incorrect documentation.**


{% include code-example-table/combined-start.html %}
```swift
/// Called when the encrypt dialog is about to close
func encryptDialogWillClose(notification: Notification)
```
{% include code-example-table/combined-cell-split.html %}
```swift
/// This function is called when the encrypt dialog is closed
func myWindowWillClose(notification: Notification)
```
<span class="lozenge lozenge-info lozenge-hollow">ACTUAL EXAMPLE FROM CODEBASE 😱</span>
{% include code-example-table/combined-end.html %}

### See Also ###
Here's a couple starting guides on how to write awesome documentation:
-   [NSHipster - Swift Documentation](http://nshipster.com/swift-documentation/)
-   [Apple Developer Docs: Markup Formatting Reference](https://developer.apple.com/library/content/documentation/Xcode/Reference/xcode_markup_formatting_ref/)



## Numbers ##
By default, use `Int` for integers and `CGFloat` for fractions (or `Length` if you have access to [SwiftUI](https://developer.apple.com/documentation/swiftui)), as they automatically scale to the current processor architecture. If a specific task _requires_ a specific number type like `UInt8` or `NSDecimalNumber`, then that's OK, but for common operations, use those aforementioned types.



## Optionals ##
In Swift, "optional" means "able to be `nil`". The great thing is that, in Swift, _anything_ can be optional, even primitives like `Int`s! This is a very powerful feature, and such power comes with a lot of responsibility.


### Do Not Use Exclamation Points<sup>[*](#exclamation-caveat-1)[†](#exclamation-caveat-2)</sup> ###
"`!`" means _"I know this might be `nil`, but I'm smart enough to know it isn't `nil` right now. It's okay to crash if I'm wrong."_ The problem here is that there is still a chance for crashing[*](#exclamation-caveat-1), whereas there's almost always another approach which does not provide a chance of crashing.

The following are preferred ways to unwrap an optional:

1.   The **`?.`**, **`?(`**, and **`?[`** operators. These somewhat mirror Objective-C's behavior, meaning "If it's `nil`, stop evaluating and return `nil` if anything expects a value. Either way, continue as normal."
2.   If the question-mark syntax does not accomplish the goal, or would be overly verbose for this context, **`if let`** (or `if var`) is the next best option. This creates a new sub-context in which the existence of the value is guaranteed, but not before nor after.
3.   If one or more values are required for a context (usually a function), or if the if let nesting is getting too deep, **`guard let`** (or `guard var`) is preferred. This is much like an inverted `if let`, in that it creates a sub-context in which the nonexistence of the value is guaranteed, the difference being that after that context, its existence is guaranteed.
    * These should go at the top of a context which requires the value, rather than farther down.
4.   If you have a backup value handy, use **`??`** to use it as an alternative in case the ideal value doesn't exist.


{% include code-example-table/combined-start.html %}
```swift
func foo() -> Foo? {
    guard let bar = self.bar else {
        assertionFailure("foo() requires bar")
        return nil
    }

    let success = bar.baz()

    if success,
        let hoge = bar.qux?.hoge {
        hoge.norf()
    }

    return bar.foo ?? self
}
```
{% include code-example-table/combined-cell-split.html %}
```swift
func foo() -> Foo? {
    if bar == nil {
        assertionFailure("foo() requires bar")
        return nil
    }

    let success = bar!.baz() // bar may have become nil by now!

    if success,
        bar!.qux != nil {
        bar!.qux!.hoge.norf() // bar andor qux may have become nil by now!
    }

    if let foo = bar!.foo { // bar may have become nil by now!
        return foo
    }
    else {
        return self
    }
}
```
{% include code-example-table/combined-end.html %}

> <a name="exclamation-caveat-1" id="exclamation-caveat-1"></a><sup>\*</sup> There is one exception where exclamation points are acceptable: On nib-initialized fields which are guaranteed to be available after load. **If you are using SwiftUI, this exception does not apply.**

> <a name="exclamation-caveat-2" id="exclamation-caveat-2"></a><sup>†</sup> Obviously, using them to mean "Boolean invert" / "not" is okay (`!condition`).


### Throwing functions ###

Another way to write a function that may or may not return a value is to declare it with the `throws` keyword. This is perfectly acceptable, and in fact encouraged when it would lead to a more clear API. However, **never** return an optional value from a throwing function. Instead, where you would `return nil`, throw a new, custom, descriptive error that tells _why_ you couldn't return a value. This is because, once the error has been handled, the API caller shouldn't have to then deal with a `nil` value, as from their perspective all errors that would cause that have been handled. Additionally, that approach wouldn't play well with the `try?` keyword.


{% include code-example-table/combined-start.html %}
```swift
struct NoBarWhenRequired: Error {}

func foo() throws -> Foo {
    guard let bar = self.bar else {
        assertionFailure("foo() requires bar")

        throw NoBarWhenRequired()
    }

    return try bar.baz() ?? self
}


func usage() {
    try {
        let result = try foo()
        result.sideEffect()
    }
    catch let error as NoBarWhenRequired {
        print("ERROR: Function called prematurely; `bar` was not initialized")
    }
    catch let error {
        print("ERROR: Unknown error occurred:", error)
    }
}
```
{% include code-example-table/combined-cell-split.html %}
```swift
func foo() throws -> Foo? {
    guard let bar = self.bar else {
        assertionFailure("foo() requires bar")
        return nil
    }

    return try bar.baz() // Might result in a `Foo??`
}


func usage() {
    try {
        if let result = try foo() {
            result.sideEffect()
        }
        else {
            print("INFO: Could not get `foo`, but no error thrown. Check logs for why this happened")
        }
    }
    catch let error {
        print("ERROR: Unknown error occurred:", error)
    }
}
```
{% include code-example-table/combined-end.html %}



## `if` and `guard` ##
`if` should never be used in place of `guard`, and vice-versa. As its name implies, `guard` should be used to guard against a bad state; if something is required, it acts as a guard that everything is OK before proceeding.

When entering a `guard`'s `else` block constitutes a bad state, **use [`assertionFailure`](#assertions) (or at least log a message) when a `guard`'s `else` block was entered.**

**Do not use `guard` or `if` in place of each other!** `guard` is meant to be used as a guard against a bad state, not as an `unless`-style statement.



## `extension` ##

Extensions are the bread and butter of Swift and Cocoa. **_Always_** prefer creating an extension function over a utility function. **_Always_** prefer creating an extension over subclassing. It's OK to make `private extension`s if something is only needed in the current context and nowhere else.

> <span class="lozenge lozenge-amazing">GOLDEN RULE</span> Always prefer extensions over subclassing.


{% include code-example-table/combined-start.html %}
```swift
extension String {
    func reversed() -> String {
        //...
    }
}
```
{% include code-example-table/combined-cell-split.html %}
```swift
// Anywhere:
func reversedString(_ string: String) -> String {
    // ...
}
```

{% include code-example-table/combined-row-split.html %}

```swift
extension Dictionary where Key: Bar, Value: Baz {
    convenience init(foo: Foo) {
        // ...
    }
}
```
{% include code-example-table/combined-cell-split.html %}
```swift
func createDictionary(from foo: Foo) -> [Bar : Baz] {
    // ...
}
```

{% include code-example-table/combined-row-split.html %}

```swift
extension Foo {
    convenience init(bar: Bar) {
        self.init(baz: bar.baz)
    }

    func qux() {
        // ...
    }
}
```
{% include code-example-table/combined-cell-split.html %}
```swift
// in some class Bar:
    var fooValue: Foo {
        return Foo(baz: self.baz)
    }



class QuxFoo: Foo {
    func qux() {
        // ...
    }
}
```

{% include code-example-table/combined-row-split.html %}

```swift
// Use examples:
let racecar = "racecar".reversed()
var foo = Foo(bar: Bar())
foo.qux()
let barBazDict = [Bar : Baz](foo: foo)
```
{% include code-example-table/combined-cell-split.html %}
```swift
// Use examples:
let racecar = reversedString("racecar")
var foo = Bar().fooValue
foo.qux() // ERROR! foo is Foo, not QuxFoo
(foo as! QuxFoo).qux() // DANGER! foo might not be QuxFoo, so this might crash
let barBazDict = createDictionary(from: foo)
```

{% include code-example-table/combined-row-split.html %}
```swift
extension URL {
    static var desktopOutput: URL {
        return URL(fileURLWithPath: "\(NSHomeDirectory())/Desktop/Output/")
    }
}

// Use example:
let outputUrl = URL.desktopOuput
writeFile(to: .desktopOutput)
```
This is more natural. "I'm looking for a common URL; I will look in the URL class."
{% include code-example-table/combined-cell-split.html %}
```swift
class AppPaths {
    class var desktopOutput: URL {
        return URL(fileURLWithPath: "\(NSHomeDirectory())/Desktop/Output/")
    }
}

// Use example:
let outputUrl = AppPaths.desktopOuput
writeFile(to: AppPaths.desktopOutput)
```
This is less natural. "I'm looking for a common URL; I will look in one of possibly several utility classes."

Equally bad is placing the constant in global scope, since it's still not obvious how to find it, and it overpopulates autocomplete.
{% include code-example-table/combined-end.html %}



## `// MARK:` ##

Separate logical sections of code with `// MARK:` and `// MARK: -` messages. Use `// MARK: -` more sparingly than `// MARK:`.

When implementing a protocol (or subclassing) outside an `extension`, make sure you place a `// MARK:` message before the variables and functions you've implemented, making sure the mark's message is the name of the protocol.

See also: [Blank Lines](#blank-lines)

{% include code-example-table/combined-start.html
    right-name='Acceptable'
    right-class='good'
 %}
```swift
struct Square {
    var sideLength: CGFloat = 0.0
}



// MARK: ExpressibleByIntegerLiteral

extension Square: ExpressibleByIntegerLiteral {
    init(integerLiteral: Int) {
        self.init(sideLength: integerLiteral)
    }
}



// MARK: CustomStringConvertible

extension Square: CustomStringConvertible {
    var description: String {
        return "A \(sideLength)×\(sideLength) square"
    }
}
```
{% include code-example-table/combined-cell-split.html %}
```swift
struct Square: ExpressibleByIntegerLiteral, CustomStringConvertible {

    var sideLength: CGFloat = 0.0


    // MARK: ExpressibleByIntegerLiteral

    init(integerLiteral: Int) {
        self.init(sideLength: integerLiteral)
    }


    // MARK: CustomStringConvertible

    var description: String {
        return "A \(sideLength)×\(sideLength) square"
    }
}
```
{% include code-example-table/combined-end.html %}



## Fancy `var`s: `get`, `set`, `willSet`, and `didSet` ##

In case you don't know or need a refresher, **here are the basics:**

> Swift `var`s are very powerful, much moreso than allowing a value to be re-set. There are three ways to use a `var`:
>
> 1.   As a normal (unobserved[*](#fancy-vars-caveat-1)) instance, whose value can be set and gotten. The scope of these operations can be set individually, like `public private(set)`.
> 2.   As an observed[*](#fancy-vars-caveat-1) instance whose changes are monitored just before andor just after the value is changed
> 3.   As a dynamic[†](#fancy-vars-caveat-2) value, whose setter and getter are specified by the developer. In this mode, it can also be read-only.
>
> These are mutually exclusive; an unobserved instance cannot[*](#fancy-vars-caveat-1) report when it changes, an observed instance cannot have a dynamic[†](#fancy-vars-caveat-2) getter and setter, and a dynamic[†](#fancy-vars-caveat-2) value does not have a backing stored value.
>
> It's also important to know that a `var` in a `struct` inherits the mutability and observation status of the instance in which it's held. That is to say, `let parentStruct`'s fields won't be changeable, regardless of whether they're `let`s or `var`s. However, `var parentStruct`'s fields will be changeable if (and only if) they are `var`s, but not if they're `let`s. Even better, if `parentStruct` has a `willSet` or a `didSet`, it will be notified if any of its fields changes.
>
> Do note that this behavior does _**not**_ exist in `class` instances, even if those instances are inside a `struct` (the best immutability those will get is not being able to change the reference; its own fields' mutability won't change). **This is one reason why [you should avoid using `class`es](#selecting-a-kind-of-type) as much as possible.**

**With the basics over, here's the guidelines:**

*   Normal, unobserved[*](#fancy-vars-caveat-1) `var`s should be used only for values that need to change rapidly, but whose changes aren't important.
*   Observed[*](#fancy-vars-caveat-1) `var`s should be used for important values, which should propagate other changes or send notifications when they change.
*   Dynamic[†](#fancy-vars-caveat-2) `var`s should be used in place of functions which only get or set a single value



{% include code-example-table/combined-start.html
    exclude-right='true'
 %}
```swift
struct Square {
    var sideLength: CGFloat = 0.0

    var name: String = "Square" {
        willSet {
            print("Renaming \(self) to \(newValue)")
        }
    }


    var perimeter: CGFloat {
        get {
            return sideLength * 4.0
        }
        set {
            sideLength = newValue / 4.0
        }
    }
}
```
{% include code-example-table/combined-end.html %}


> <a name="fancy-vars-caveat-1" id="fancy-vars-caveat-1"></a><sup>\*</sup> Here, "unobserved" refers to lacking the built-in `willSet` andor `didSet` blocks; these can still be observed using key-value approaches. Conversely, "observed" refers to the presence of these blocks and does not imply KVO.

> <a name="fancy-vars-caveat-2" id="fancy-vars-caveat-2"></a><sup>†</sup> Here, "dynamic" refers to returning a generated, abstracted, translated, or conditional value (like a function). It does _not_ refer to using the `dynamic` keyword for KVO.



## Function Overloading and Default Parameters ##

In Objective-C, it was necessary to create multiple methods when you wanted one parameter to be optional. With Swift's default parameters, this is no longer necessary.

Of course, overloading is okay if it is necessary for the current task.


{% include code-example-table/combined-start.html %}
```swift
func foo(_ bar: Bar, for baz: Baz = Baz.shared) {
    // ...
}
```
{% include code-example-table/combined-cell-split.html %}
```swift
func foo(_ bar: Bar, for baz: Baz) {
    // ...
}


func foo(_ bar: Bar) {
    foo(bar, for: Baz.shared)
}
```

{% include code-example-table/combined-row-split.html %}
```swift
func stretch(_ square: Square) -> Rectangle {
    // ...
}

func stretch(_ muscle: Muscle) -> Exercise.Result {
    // ...
}
```
{% include code-example-table/combined-cell-split.html %}
```swift
func stretch(_ squareOrMuscle: Any) -> Any {
    if let square = squareOrMuscle as? Square {
        // ...
    }
    else if let muscle = squareOrMuscle as? Muscle {
        // ...
    }
    else {
        // ...
    }
}
```
{% include code-example-table/combined-end.html %}



## Function parameter labels ##

Sometimes it's a good idea to label function parameters, and sometimes it's not.

1.   Parameter labels may be omitted when the parameter reflects the main purpose of the function. For instance, the string message of a log function, the new value of a setter, or the two items in a two-item comparison function. **This overrides the following guidelines.**
2.   Boolean parameters **must always** have labels
3.   Unlike Objective-C, the first parameter can have a label separate from the method name. When possible, prefer this.
4.   When the value being passed in has a specific purpose other than just providing required data, try to give it a separate, more-English label than its variable name (not simple ones like `for` or `with`)
5.   **Do not** provide a label for parameters whose name is in the function name
6.   When calling an external API which does not label its parameters, but leaves their purpose confusing, add "label" block-comments

> <span class="lozenge lozenge-good">RULE-OF-THUMB</span> When in doubt, use a label.


{% include code-example-table/combined-start.html %}
```swift
func setFoo(_ foo: Foo, // 1 & 5
            with barProvider: (Foo) -> Bar, // 4
            bazzing: Bool) { // 2
    // ...
}
// Call-site example:
setFoo(Foo(), with: BarProvider.default, bazzing: true)



func qux(for hoge: Hoge) -> Qux { // 3
    // ...
}
// Call-site example:
_ = qux(for: Hoge())


let x = cApiCall(descriptiveValueA,
                 descriptiveValueB,
                 /* silently: */ true) // 2 & 6
```
{% include code-example-table/combined-cell-split.html %}
```swift
func setFoo(foo: Foo, // 1 & 5
            barProvider: (Foo) -> Bar, // 4
            _ bazzing: Bool) { // 2
    // ...
}
// Call-site example:
setFoo(foo: Foo(), barProvider: .default, true)



func quxForHoge(_ hoge: Hoge) -> Qux { // 3
    // ...
}
// Call-site example:
_ = quxForHoge(Hoge())


let x = cApiCall(descriptiveValueA,
                 descriptiveValueB,
                 true) // 2 & 6
```
{% include code-example-table/combined-end.html %}



## Mutability ##

Swift was designed around less-mutating, more-functional approaches first. In general, **use approaches that create changed immutable copies**, rather than ones that mutate an existing value. This may sound inefficient, but remember that the Swift compiler and runtime expect this to be the default approach, and will optimize for it.

If you run tests and find that using a non-mutating approach is too slow, then it's okay to delicately choose which parts of your approach to convert to mutating. **Use a scalpel, not a mallet** when deciding what needs to be mutated. If you're using Swift's functional collection functions like `map` and `filter`, first try preceding these with `.lazy` to turn it into a `LazySequence`, whose performance is improved by waiting until the last moment to perform the operations. **This can be even faster than using mutation.**

This is much more expressive, safer, and often even faster than mutating.


{% include code-example-table/combined-start.html %}
```swift
extension Sequence where Iterator.Element == String {

    func sum() -> CGFloat.NativeType {
        return self
            .lazy
            .compactMap(CGFloat.NativeType.init)
            .reduce(into: 0, +=)
    }
}


// Usage:
["1", "2.0", "3.5", "4e1", "-4.5"].sum() // 42.0
```

In my tests, this takes an average of **~82%** as long as the mutating approach.

{% include code-example-table/combined-cell-split.html %}
```swift
extension Sequence where Iterator.Element == String {

    func sum() -> CGFloat.NativeType {

        var sum: CGFloat.NativeType = 0

        self.forEach { numberString in
            if let floatNumber = CGFloat.NativeType(numberString) {
                sum += floatNumber
            }
        }

        return sum
    }
}


// Usage:
["1", "2.0", "3.5", "4e1", "-4.5"].sum() // 42.0
```
In my tests, this takes an average of **~122%** as long as the functional approach.
{% include code-example-table/combined-end.html %}



## Control Flow ##

One of the beautiful things Swift does is decrease the amount of control flow that's needed by turning common patterns into production-ready functions in the standard library, called **higher-order functions**. When at all possible, **prefer these functions and paradigms over traditional control flow.**



{% include code-example-table/combined-start.html %}
```swift
extension Array where Element == CGFloat {
    func positives() -> [CGFloat] {
        return self.filter { $0 >= 0.0 }
    }
}
```
{% include code-example-table/combined-cell-split.html %}
```swift
extension Array where Element == CGFloat {
    func positives() -> [CGFloat] {
        var positives = [CGFloat]()
        for number in self {
            if number >= 0 {
                positives += number
            }
        }
        return positives
    }
}
```

{% include code-example-table/combined-row-split.html %}

```swift
extension Array where Element == CGFloat {
    func sum() -> CGFloat {
        return self.reduce(into: 0.0, +=)
    }
}
```
{% include code-example-table/combined-cell-split.html %}
```swift
extension Array where Element == CGFloat {
    func sum() -> CGFloat {
        var sum = 0.0
        for number in self {
            sum += number
        }
        return sum
    }
}
```

{% include code-example-table/combined-row-split.html %}

```swift
extension Array where Element == CGRect {
    func union() -> CGRect? {
        return self.first.map { firstRectangle in
            self.reduce(into: firstRectangle) { grandUnion, rect in
                grandUnion = grandUnion.union(rect)
            }
        }
    }
}
```
{% include code-example-table/combined-cell-split.html %}
```swift
extension Array where Element == CGRect {
    func union() -> CGRect? {
        guard var grandUnion = self.first else {
            return nil
        }

        for rect in self {
            grandUnion = grandUnion.union(rect)
        }

        return grandUnion
    }
}
```

{% include code-example-table/combined-row-split.html %}

```swift
extension Sequence where Iterator.Element == CGRect {
    func nonEmptyIntersections(_ otherRectangles: [CGRect]) -> [CGRect] {
        return zip(self, otherRectangles)
            .map { $0.0.intersection($0.1) }
            .filter { !$0.isEmpty }
    }
}
```
{% include code-example-table/combined-cell-split.html %}
```swift
extension Sequence where Iterator.Element == CGRect {
    func nonEmptyIntersections(_ otherRectangles: [CGRect]) -> [CGRect] {
        var nonEmptyIntersections = [CGRect]()
        for i in 0..<min(self.count, otherRectangles.count) {
            let lhs = self[i]
            let rhs = otherRectangles[i]
            let intersection = lhs.intersection(rhs)
            if !intersection.isEmpty {
                nonEmptyIntersections += intersection
            }
        }
        return nonEmptyIntersections
    }
}
```

{% include code-example-table/combined-row-split.html %}

```swift
extension Array where Element == Int {
    func asFloats() -> [CGFloat] {
        return map(CGFloat.init)
    }
```
{% include code-example-table/combined-cell-split.html %}
```swift
extension Array where Element == Int {
    func asFloats() -> [CGFloat] {
        var floats = [CGFloat]()
        for foo in foos {
            floats += CGFloat(foo)
        }
        return floats
    }
}
```

{% include code-example-table/combined-row-split.html %}

```swift
struct OptionalIntCollection {
    var optionalInts: [Int?]

    func nonOptionalInts() -> [Int] {
        return optionalInts.compactMap { $0 }
    }


    func nonOptionalStrings() -> [String] {
        return optionalInts.compactMap { optionalInt in
            return optionalInt.flatMap { $0.description }
        }
    }
}
```
{% include code-example-table/combined-cell-split.html %}
```swift
struct OptionalIntCollection {
    var optionalInts: [Int?]

    func nonOptionalInts() -> [Int] {
        var nonOptionalInts = [Int]()
        for optionalInt in optionalInts {
            if let actualInt = optionalInt {
                nonOptionalInts += actualInt
            }
        }
        return nonOptionalInts
    }


    func nonOptionalStrings() -> [String] {
        var nonOptionalStrings = [String]()
        for optionalInt in optionalInts {
            if let actualInt = optionalInt {
                nonOptionalStrings += actualInt.description
            }
        }
        return nonOptionalStrings
    }
}
```
{% include code-example-table/combined-end.html %}



## Enum Case Associated Values ##

See Also:
-    [Associated Values - Enumerations - Swift Programming Language Documentation](https://docs.swift.org/swift-book/LanguageGuide/Enumerations.html#ID148)


Associated values are a very powerful Swift paradigm, but there are many ways to use them. In our codebase, we prefer this approach:

1.    Always label the associated values
2.    When reading the values in a switch case, always give the reader variable the same name as the label
3.    When pattern-matching in a switch case, always use the label
4.    When pattern-matching in a switch case and ignoring the associated values, always write the label followed by an underscore, rather than just an underscore or omitting the associated values entirely


{% include code-example-table/combined-start.html %}
```swift
enum Result {
    case asExpected
    case needTweaks(firstItemIndex: Int, secondItemIndex: Int, reverse: Bool) // 1
    case didIncrement(incrementedIndex: Int) // 1
    case errorOccurred(fullError: Error) // 1
}
```

```swift
switch result {
    case asExpected:
        return


    case needTweaks(firstItemIndex: 123, secondItemIndex: _, reverse: true): // 3 & 4
        foo()

    case needTweaks(firstItemIndex: _, secondItemIndex: 456, reverse: false): // 3 & 4
        bar()

    case needTweaks(firstItemIndex: _, secondItemIndex: _, reverse: _): // 3 & 4
        baz()


    case didIncrement(incrementedIndex: _): // 4
        return


    case errorOccurred(let fullError) // 3
        throw fullError
}
```

{% include code-example-table/combined-cell-split.html %}

```swift
enum Result {
    case asExpected
    case needTweaks(Int, Int, reverse: Bool) // 1
    case didIncrement(Int) // 1
    case errorOccurred(Error) // 1
}
```

```swift
switch result {
    case asExpected:
        return


    case needTweaks(123, _, true): // 3 & 4
        foo()

    case needTweaks(_, 456, false): // 3 & 4
        bar()

    case needTweaks(_, _, _): // 3 & 4
        baz()

    case didIncrement: // 4
        return


    case errorOccurred(let cause) // 3
        throw cause
}
```
{% include code-example-table/combined-end.html %}



## `NSCache` ##

`NSCache` is very useful **when applied correctly**. It cannot be used for all caching needs.
This is because `NSCache` can and will evict things **even if everything is under the limits**.

In case you're unfamiliar with `NSCache`, it is a Cocoa class which allows you to give limits on both total number of items in that cache, as well as total cost (with each item optionally given a cost when it is cached). As previously mentioned, `NSCache` only loosely respects these guidelines. For performance reasons, it might evict items even if no limits have been reached. Similarly, it might not yet have evicted items well after its limits have been passed.

**Never use `NSCache` to cache values which cannot be recreated** (for instance, items with UUIDs)!

If your requirements are okay with this looseness, then `NSCache` might be the right tool for the job. Otherwise, you should probably write your own cache which is specialized to fit those requirements.
