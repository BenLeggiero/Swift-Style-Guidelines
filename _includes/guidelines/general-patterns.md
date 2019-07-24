
# General Patterns #
{% include dedicated-guide-page-link.html
    pageLinkFileName='General-Patterns'
%}

## Singleton ##
Singletons are often unnecessary, but if you find one necessary, name the single instance `shared` (if there can only ever be one) or `default` (if a custom instance can be created), and place it in the class' static scope.

{% include code-example-table/combined-start.html %}
```swift
class Foo {
    static let shared = Foo()

    private init() {
    }
}

// Call-site example:
let foo = Foo.shared
let bar = bar(foo: .shared)
```

```swift
class Bar {
    static let `default` = Foo()

    init() {
    }
}

// Call-site example:
let bar = Bar.default
let baz = baz(bar: .default)
```
{% include code-example-table/combined-cell-split.html %}
```swift
class Foo {
    static let sharedInstance = Foo()

    private init() {
    }
}

// Call-site example:
let foo = Foo.sharedInstance
let bar = bar(foo: .sharedInstance)
```

```swift
class Foo {
    static let foo = Foo()

    private init() {
    }
}

// Call-site example:
let foo = Foo.foo
let bar = bar(foo: .foo)
```

```swift
class Bar {
    init() {
    }
}

let defaultBar = Bar()

// Call-site example:
let bar = Bar
let baz = baz(bar: defaultBar)
```
{% include code-example-table/combined-end.html %}



## Lazy initialization ##

Thankfully, Swift makes this easy with its lazy keyword. The only matter of style is how to handle resetting the lazy value.

{% include code-example-table/combined-start.html %}
```swift
lazy var foo = Foo()
lazy private(set) var bar = Bar()
```
{% include code-example-table/combined-cell-split.html %}
```swift
private var _foo: Foo? = nil
var foo: Foo {
    get {
        if let foo = _foo {
            return foo
        }
        _foo = Foo()
        return _foo
    }

    set {
        _foo = newValue
    }
}

private var _bar: Bar? = nil
var bar: Bar {
    if let bar = _bar {
        return bar
    }
    _bar = Bar()
    return _bar
}
```

{% include code-example-table/combined-row-split.html %}

```swift
lazy private(set) var baz: Baz = {
    let baz = Baz()
    baz.qux = "qux?"
    return baz
}()
```
{% include code-example-table/combined-cell-split.html %}
```swift
private var lazyBaz: Baz? = nil
var baz: Baz {
    get {
        if let baz = lazyBaz {
            return baz
        }
        let baz = Baz()
        baz.qux = "qux?"
        lazyBaz = baz
        return baz
    }
    private set {
        lazyBaz = newValue
    }
}
```
{% include code-example-table/combined-end.html %}



### Resettable Lazy Fields ###

For resettable lazy fields, declare a private backing field as optional and a more-exposed field getter that will set it if it's `nil`. For this, you can use [Swift Lazy Patterns](https://github.com/BenLeggiero/Swift-Lazy-Patterns)' [`ResettableLazy`](https://github.com/BenLeggiero/Swift-Lazy-Patterns/blob/master/Lazy.swift#L88-L171):

{% include code-example-table/combined-start.html %}
```swift
private var _foo = ResettableLazy<Foo> { Foo() }


internal var foo: Foo! {
    get {
        return _foo.value
    }
    set {
        if let newValue = newValue {
            _foo.value = newValue
        }
        else {
            _foo.reset()
        }
    }
}


print(foo)
foo = nil
```
{% include code-example-table/combined-cell-split.html %}
```swift
internal lazy var foo: Foo!

print(foo)
foo = nil // This does not reset the lazy value! https://bugs.swift.org/browse/SR-5172
```
{% include code-example-table/combined-end.html %}



## Assertions ##

Assertions can be a powerful way to help yourself debug; if a very bad state occurs, instead of it being quietly logged, the app halts immediately and won't continue. Remember **assertions are ignored in production builds** so they won't lead to end-user crashes.

When no logic is necessary and an assertion should always be thrown, use `assertionFailure("message")` instead of `assert(false, "message")`.

{% include code-example-table/combined-start.html %}
```swift
guard let bar = self.bar else {
    assertionFailure("foo requires bar")
    return
}
```
{% include code-example-table/combined-cell-split.html %}
```swift
guard let bar = self.bar else {
    print("foo requires bar")
    return
}
```
```swift
guard let bar = self.bar else {
    return
}
```
{% include code-example-table/combined-end.html %}



## Factory Initializers ##

A common Objective-C approach to fancy initializers is to create a static function named like `arrayWithObject:` or `stringByAppendingString:`. Swift no longer needs these bodges. Instead, create another initializer (perhaps in an `extension`), whose first parameter is named like the old factory methods. These are often also annotated as `convenience` initializers.

{% include code-example-table/combined-start.html %}
```swift
class Foo {
    let bar: Bar


    required init(bar: Bar) {
        self.bar = bar
    }


    convenience init?(with baz: Baz) {
        guard let bar = baz.bar else {
            return nil
        }
        self.init(bar: bar)
    }
}

let example = Foo(with: Baz())
```
{% include code-example-table/combined-cell-split.html %}
```swift
class Foo {
    let bar: Bar


    required init(bar: Bar) {
        self.bar = bar
    }


    static func foo(withBaz baz: Baz) -> Foo? {
        guard let bar = baz.bar else {
            return nil
        }
        return Foo(bar: bar)
    }
}

let example = Foo.foo(withBaz: Baz())
```
{% include code-example-table/combined-end.html %}



## Global Constants ##

See also:

 * [`extension`](#extension)
 * [global constants' naming guidelines](#instances)

There will always be a need for global constants. However, it's better to keep them out of global _scope_. Prefer placing these constants in `struct`s, `extension`s, etc.

{% include code-example-table/combined-start.html %}
```swift
public extension Int {
    public static let answer = 42
}

public extension MyTimelyOperation {
    public static let maxDuration: TimeInterval = 0.5
}

public extension Notification.Name {
    public static let hotFolderUpdate = Notification.Name("org.bh.hotFolderUpdate")
}


// Usage:

reverseEngineerQuestion(from: .answer)

extension MyTimelyOperation {
    static var shortOperation: MyTimelyOperation {
        return MyTimelyOperation(duration: self.maxDuration / 3.0)
    }
}
let shortOperation = MyTimelyOperation.shortOperation

let notification = Notification(name: .hotFolderUpdate)
```
{% include code-example-table/combined-cell-split.html %}
```swift
public let answer = 42

public let maxTimeSensitiveOperationDuration: TimeInterval = 0.5

public let notificationNameHotFolderUpdate = Notification.Name("org.bh.hotFolderUpdate")


// Usage:

reverseEngineerQuestion(from: answer)

extension MyTimelyOperation {
    static var shortOperation: MyTimelyOperation {
        return MyTimelyOperation(duration: maxTimeSensitiveOperationDuration / 3.0)
    }
}
let shortOperation = MyTimelyOperation.shortOperation

let notification = Notification(name: notificationNameHotFolderUpdate)
```
{% include code-example-table/combined-end.html %}



## Compile-Time Warnings and Errors ##

Treat warnings as errors. Very seldom is it okay to allow code with a warning to be committed. It's up to you to make sure code that you commit is warning- and error-free.

The same goes for tests; if a test fails, treat that like a compiler error. It's up to you to ensure every build passes its tests.



## Runtime Warnings and Errors ##

It's often necessary to indicate that a function could not complete its duties. The preferred Objective-C way was to use an `NSError**`. In Swift, that is to `throw` an `Error`. Embrace this, and use it to bring more information and compile-time peace-of-mind.

{% include code-example-table/combined-start.html %}
```swift
struct Foo {

    enum JsonParseError: Error {
        case unknownSymbol(symbol: String)
        case unexpectedEnd
    }


    func parse(json: String) throws -> ParsedJson {
        ...
        guard let nextCharacter = reader.nextCharacter() else {
            throw JsonParseError.unexpectedEnd
        }
        ...
        switch symbol {
        ...
        default: throw JsonParseError.unknownSymbol(symbol: symbol)
        }
        ...
        return parsedJson
    }
}
```


**Example usage:**

```swift
func didReceive(json: String) {
    do {
        self.parsedJson = try self.parse(json: json)
    }
    catch let error: JsonParseError {
        let message: String

        switch error {
        case .unknownSymbol(let symbol):
            message = "What is this?: \"\(symbol)\""

        case .unexpectedEnd:
            message = "That EOF came out of nowhere!"
        }

        assertionFailure(message)
    }
}
```
```swift
let empty = try! Foo().parse(json: "{}")
```
```swift
if let unimportantString = self.unimportantString,
    let unimportant = try? Foo().parse(json: unimportantString) {
    ...
}
```
{% include code-example-table/combined-cell-split.html %}
```swift
struct Foo {
    var parsedJson: ParsedJson?

    enum JsonParseError: Int {
        case noError = 0 // Non-error defined in "Error" enum
        case unknownSymbol = 1
        case unexpectedEnd = 2
    }


    // An `inout` `parsedJson` would also be inappropriate here, even if it does reduce spooky action and mutability
    mutating func parse(json: String) -> JsonParseError {
        self.parsedJson = nil
        ...
        guard let nextCharacter = reader.nextCharacter() else {
            return .unexpectedEnd
        }
        ...
        switch symbol {
        ...
        default: return .unknownSymbol
        }
        ...
        self.parsedJson = parsedJson
        return .noError
    }
}
```


**Example usage:**

```swift
mutating func didReceive(json: String) {
    let error = self.parse(json: json) // spooky action: Where is the result?

    switch error {
    case .unknownSymbol
        assertionFailure("Some symbol was wrong!") // Less information

    case .unexpectedEnd:
        assertionFailure("That EOF came out of nowhere!")

    case .noError:
        break
    }
}
```
```swift
var emptyWrapper = Foo()
let error = emptyWrapper.parse(json: "{}")
guard error == .noError else {
    assertionFailure("Didn't expect \(error) when parsing {}")
    exit(error.rawValue) // Not the same effect
}
let empty = emptyWrapper.parsedJson
```
```swift
if let unimportantString = self.unimportantString {
    var unimportantWrapper = Foo()
    _ = unimportantWrapper.parse(json: unimportantString) // What is ignored? It's unclear
    if let unimportant = unimportantWrapper.parsedJson {
        ...
    }
}
```
{% include code-example-table/combined-end.html %}



### Runtime Errors in Callbacks ###

This is all well and good, but when receiving a callback from an asynchronous function, you might also want to know if any error occurred in there. Swift's standard library provides the [`Result`](https://developer.apple.com/documentation/swift/result) type for this:

{% include code-example-table/combined-start.html %}
```swift
func someAsync(then callback: @escaping (Result<String, Error>) -> Void) {
    onAnotherThread {
        do {
            callback(.success(try someDangerousStringGenerator()))
        }
        catch let error {
            callback(.failure(cause: error))
        }
    }
}
```


**Example usage:**

```swift
someAsync { result in
    switch result {
    case .failure(let cause): print("ERROR: Could not get string. Cause:", cause)
    case .success(let result): print(result)
    }
}
```
{% include code-example-table/combined-cell-split.html %}
```swift
func someAsync(then callback: @escaping (String?, Error?) -> Void) {
    onAnotherThread {
        do {
            callback(try someDangerousStringGenerator(), nil)
        }
        catch let error {
            callback(nil, error)
        }
    }
}
```


**Example usage:**

```swift
someAsync { result, error in
    if let error = error {
        print("ERROR: Could not get string. Cause:", cause)
    }
    else if let result = result {
        print(result)
    }
    else {
        preconditionFailure("Undefined state! No result, and no error!")
    }
}
```
{% include code-example-table/combined-end.html %}



## Getting Results from Functions ##

There are two ways you should get results from a function: by them it directly, or by using a callback. Do not use "return parameters" (via `inout`). Note that `inout`, itself, has valid uses in our style, for instance when the point of the function is to mutate some other object, but it should not be used to "return" a value.

If more than 1 value must be returned, use a tuple. If a great many values must be returned (like, 4 or more), create a specialized struct.

To indicate an error, instead of using an `inout Error`, use the `throws` keyword.

{% include code-example-table/combined-start.html %}
```swift
func asynchronous(callback: (Result) -> Void) { ... }

func manyReturnValues() -> (ResultA, ResultB, ResultC) { ... }

func wayTooManyReturnValues() -> SpecializedStruct { ... }

func danger() throws -> Result { ... }

func nicelySynchronous() -> Result { ... }
```

Remember, `inout` still has its place:

```swift
func prepareForDelivery(payload: inout Payload, options: PayloadDeliveryOptions) { ... }



// Usage:

prepareForDelivery(payload: &payload, options: [])
```

... but always consider using a mutating func in an extension instead:

```swift
extension Payload {
    mutating func prepareForDelivery(options: PayloadDeliveryOptions) { ... }
}



// Usage:


payload.prepareForDelivery(options: [])
```
{% include code-example-table/combined-cell-split.html %}
```swift
func asynchronous(return: inout Result?) { ... }

func manyReturnValues(returnA: inout ResultA?, returnB: inout ResultB?, returnC: inout ResultC?) { ... }

func wayTooManyReturnValues() -> (ResultA, ResultB, ResultC, ResultD, ResultE, ResultF, ResultG) { ... }

func danger(error: inout Error) -> Result? { ... }

// More discouraged than bad; sometimes signatures require this
func nicelySynchronous(callback: (Result) -> Void) { ... }
```
{% include code-example-table/combined-end.html %}
