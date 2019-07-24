# Naming #
{% include dedicated-guide-page-link.html
    pageLinkFileName='Naming'
%}

Names should be in **American English**.

See also:

-   Swift.org's naming guidelines: [Swift.org - API Design Guidelines § Naming](https://swift.org/documentation/api-design-guidelines/#naming)

If this is difficult for you to understand at the call-site, then you may have chosen the wrong words. If all else fails, simply use Quick Help (by <kbd>⌥</kbd>-clicking the symbol or by selecting it and using the Quick Help inspector) and the resulting documentation will describe its declaration for you.


## Capitalization ##

This one is simple. **Types** (`class`es, `protocol`s, `typealias`es, `struct`s, `enum`s, etc.) **are always CapitalizedCamelCase**. **Instances** (`let`s, `var`s, `case`s, etc.) **and functions** (`func`s, dynamic `var`s, non-anonymous closures, etc.) **are always uncapitalizedCamelCase**.

When an acronym or initialism like HTTP or JSON is a part of a name, treat it like an English word rather than an abbreviation, even if it's not pronounceable like a word.

{% include code-example-table/combined-start.html %}
```swift
fileprivate class Foo {
    let bar = Bar()
    private var baz: Baz? = nil

    let httpSample: HttpSample? = nil

    func qux(myUrl: URL) {
        bar.hoge(myUrl)
    }
}

enum Fruit {
    case apple
    case blackberry
    case cherry(let isPitted: Bool)

    static let defaultIsPitted = true
}

let globalConstant = "c"

typealias Bar = Norf
```
{% include code-example-table/combined-cell-split.html %}
```swift
fileprivate class _Foo {
    let Bar = bar()
    // Underscores before private fields are not a strict "no", but they are discouraged
    private var _baz: Baz? = nil

    let hTTPSample: HTTPSample? = nil

    func Qux(myURL: URL) {
        Bar.Hoge(myURL)
    }
}

enum fruit {
    case Apple
    case Blackberry
    case Cherry(let is_pitted: Bool)

    static let DEFAULT_IS_PITTED = true
}

let kGlobalConstant = "c"

typealias bar = Norf
```
{% include code-example-table/combined-end.html %}

> <span class="text-bad">**Do not use Systems Hungarian notation!**</span>
>
> Knowing the type, implementation, etc. in something's name is not important. If, however, you find that such information is necessary, you may append it as full words to the end of a name, like "`nutritionString`", "`widthInt`", or "`base64ContentWideCString`"

<!-- Spacing on-purpose -->

> <span class="text-bad">**Do not use Apps Hungarian notation!**</span>
>
> Knowing the purpose of something in its name is very important. However, you should prepend it as full words (not short letter abbreviations), like "`contentLocalCoordinates`", "`unsanitizedUserInput`", or "`rowMaximumCount`"


## Grammar ##

Names should be descriptive and self-documenting (however, that does not mean you should forego [documentation](#documentation-comments)). The name of a function, for instance, should describe everything that function does. If that name seems too unwieldy, it's probably because that function does too much. In that case, split it up into multiple functions, each also with descriptive names.

**If your instance, function, or type name is only a few characters long, it probably needs a longer name.**

Here are some good **rules** from [Swift.org's design guidelines](https://swift.org/documentation/api-design-guidelines/#fundamentals):

> <span class="lozenge lozenge-good">RULE-OF-THUMB</span> **Clarity at the point of use is your most important goal.** Entities such as methods and properties are declared only once but used repeatedly. Design APIs to make those uses clear and concise. When evaluating a design, reading a declaration is seldom sufficient; always examine a use case to make sure it looks clear in context.

<!-- Spacing on-purpose -->

> <span class="lozenge lozenge-good">RULE-OF-THUMB</span> **Clarity is more important than brevity.** Although Swift code can be compact, it is a non-goal to enable the smallest possible code with the fewest characters. Brevity in Swift code, where it occurs, is a side-effect of the strong type system and features that naturally reduce boilerplate.

<!-- Spacing on-purpose -->

> <span class="lozenge lozenge-good">RULE-OF-THUMB</span> **Write a documentation comment for every declaration.** Insights gained by writing documentation can have a profound impact on your design, so don’t put it off.

<!-- Spacing on-purpose -->

> <span class="lozenge lozenge-amazing">GOLDEN RULE</span> <span class="good">**If you are having trouble describing your API’s functionality in simple terms, you may have designed the wrong API.**</span>

It's important to note that this page uses placeholder names like foo, bar, baz, etc. to name things when the name is unimportant to the example. It's **strongly discouraged** to use such obscure andor short names in production code!


### Functions ###

-   **Getters** are named **<u>the same as fields</u>**; that is to say they are simply the **<u>noun</u>** or noun phrase which describes the thing they get. If properties need to be passed in, they are named parameters, not part of the actual function name.
    -   Boolean (or other very restricted sets of values) getters are named as **<u>assertions about something</u>**. They might be prefixed with is/does/etc. or be a present-tense **<u>verb</u>** or verb phrase.
-   **Setters** take the **<u>same syntax to getters</u>**, but with the word "**<u><code>set</code></u>**" prefixing them. **Note that it's preferred to use a var rather than a func to get & set a single value.** The incoming parameter which holds the new value should not have a label (use `_`).
-   Non-Setter **Mutators** are **<u>verbs</u>** or phrases describing the mutation they perform
-   **Copiers** (functions which otherwise act like mutators, but instead of mutating return a new object with the changed data) are **<u>present- or past-participle verbs</u>** or verb phrases (ending in "ing", "ed", etc.) describing the mutation they perform


{% include code-example-table/combined-start.html %}
```swift
protocol Funcs {

    // MARK: Getters

    func font(style: FontStyle) -> Font

    func data(for provider: Provider) -> Data

    func element(_ callback: (Element) -> Void)

    func isDataValid(_ data: Data) -> Bool

    var resizes: Bool { get }

    func doesMatch(_ other: Funcs) -> Bool

    func listMatches(in other: Funcs) -> [Match]


    // MARK: Setters

    mutating func setFont(_ font: Font, style: FontStyle)

    mutating func setData(_ data: Data, for provider: Provider)

    func setAsynchronously(with setter: () -> Element)


    // MARK: Mutators

    mutating func resetData(for provider: Provider)

    mutating func increaseFontSize()


    // MARK: Copiers

    func resettingData(for provider: Provider) -> Self

    func increasedFontSize() -> Self
}
```

{% include code-example-table/combined-cell-split.html %}

```swift
protocol Funcs {

    // MARK: Getters

    func getFont(style: FontStyle) -> Font // Don't use "get" for getters

    func FetchDataForProvider(_ provider: Provider) -> Data // "fetch" implementation detail is unnecessary in the signature; function is improperly capitalized

    func get(_ callback: (Element) -> Void) // Get what?

    func validate(_ data: Data) -> Bool // Sounds like it might mutate the data to make it valid

    var resize: Bool { get } // Sounds like it resizes something, possibly returning a success

    func matches(_ other: Funcs) -> Bool // Unclear; are you asserting that this matches the other, or asking for the list of matches?

    func matches(_ other: Funcs) -> [Match] // Same problem as above. Also doesn't compile.


    // MARK: Setters

    mutating func newFont(_ font: Font, style: FontStyle) // Sounds like it returns a new font instead of setting one

    mutating func data(_ data: Data, for provider: Provider) // Sounds like it returns data

    func set(with setter: () -> Element) // Doesn't describe critical behavior (it's asynchronous)


    // MARK: Mutators

    mutating func reset(for provider: Provider) // Doesn't clarify it only resets data

    mutating func inc() // Increment? Incorporate? Increase (Increase what?)?


    // MARK: Copiers

    func resetData(for provider: Provider) -> Self // Sounds like it mutates

    func inc_font() -> Self // Same problem as above and in inc(), uses an underscore, and is unclear whether it mutates
}
```
{% include code-example-table/combined-end.html %}


### Types ###

Types should not be prefixed; this was a Cocoa pattern used until Swift as a primitive form of namespacing, but since Swift is modular, no prefix is needed.

-   **Classes** and **structs** have varied purposes, so they are named in a way that reflects their purpose, using **<u>nouns</u>** or noun phrases.
-   **Protocols** define a contract, so there are two approaches:
    -   Protocols which define **what something is** are named **<u>exactly like the classes</u>** which might implement them; nouns or noun phrases.
    -   Protocols which define **what something can do** are named using **<u>present-participle verbs</u>** or verb phrases (end in `ing`, `albe`, `ible`, etc.).
-   **Enums** are named as the [**<u>singular noun</u>**](#plurality) which describes what their cases represent.
-   **Typealiases** are named **<u>just like the type</u>** for which they are an alias.
    -   **Associated types** are named **<u>just like typealiases</u>**.


{% include code-example-table/combined-start.html %}
```swift
class BasicDataAccessor: DataAccessor { }
class MainView: NSView { }
class DynamicEncryptionPolicyHandler { }

protocol DataAccessor { }
protocol Encryptable {
    associatedtype Encrypted
}

struct RegistrationWorksheet { }
struct EncryptableFile: Encryptable { }

enum LogSeverity { }
enum ServerErrorCode { }

typealias Label = String
typealias HttpErrorCode = UInt16
typealias Encrypting = Encryptable
```

{% include code-example-table/combined-cell-split.html %}

```swift
class DataAccessImpl: DataAccessProtocol { }
class BHSMainView: NSView { }
class EncPolicy { }

protocol DataAccessProtocol { }
protocol Encrypt {
    associatedtype T
}

struct BHSReg { }
struct EncFile: Encrypt { }

enum LogSeverities { }
enum ErrorCode { }

typealias label = String
typealias HTTPErr = UInt16
typealias Encryptor = Encrypt
```
{% include code-example-table/combined-end.html %}



### Instances ###

Anything that holds an instance/value (stored fields, `enum`s' `case`s, etc.) are named as **<u>nouns</u>**. An exception can be made for Booleans, which are named as **<u>asserting verbs</u>** or verb phrases like [their function counterparts](#functions).

Note that [fancy `var`s](#fancy-vars-get-set-willset-and-didset) may be named like functions.

> All instances have the same naming scheme: `uncapitalizedCamelCase`.
>
> Do not use `ALL_CAPS_NAMES`, `m` prefixes, etc.
>
> `_underscorePrefixes` should only be used if absolutely necessary.



{% include code-example-table/combined-start.html %}
```swift
func performAction(sender: Any) { ... }

struct WellDefinedStructure {

    static let shared = WellDefinedStructure(values: ["A", "2", "III"])

    var values: [String]
    private var isBusy = false


    internal init(values: [String]) {
        self.values = values
    }


    func isEqual(to other: WellDefinedStructure) -> Bool { ... }


    var jsonValue: String {
        isBusy = true
        defer { isBusy = false }

        var jsonValue = "{\"values\":["

        jsonValue += values.map { "\"\($0)\"" } .joined(separator: ",")

        return jsonValue + "]}"
    }
}

let wellDefinedStructure = WellDefinedStructure.shared
```

{% include code-example-table/combined-cell-split.html %}

```swift
func performAction(_ sender: Any) { ... }

struct WellDefinedStructure {

    static let SHARED = WellDefinedStructure(vals: ["A", "2", "III"])

    var vals: [String]
    private var m_busy = false


    internal init(vals: [String]) {
        self.vals = vals
    }


    func isEqual(to obj: WellDefinedStructure) -> Bool { ... }


    var json: String {
        m_busy = true
        defer { m_busy = false }

        var str = "{\"values\":["

        str += vals.map { "\"\($0)\"" } .joined(separator: ",")

        return str + "]}"
    }
}

let wds = WellDefinedStructure.SHARED
```
{% include code-example-table/combined-end.html %}



### Plurality ###

Only collection types (and instances of those types) should be plural (for instance, `OptionSet`s and `Array`s). Everything else should be **singular**.


{% include code-example-table/combined-start.html %}
```swift
struct Pizza {
    let size: Size
    let toppings: Toppings


    static let defaultSize = Size.medium
    static let defaultToppings: Toppings = [.cheese, .pepperoni]



    enum Size {
        case small
        case medium
        case large
    }


    struct Toppings: OptionSet {
        let rawValue: UInt8

        static let cheese = Toppings(1 << 0)
        static let pepperoni = Toppings(1 << 1)
        static let bellPeppers = Toppings(1 << 2)
        static let sausage = Toppings(1 << 3)
        static let spinach = Toppings(1 << 4)
    }
}
```
{% include code-example-table/combined-cell-split.html %}
```swift
struct Pizza {
    let size: Sizes
    let toppings: Topping


    static let defaultSize = Sizes.small
    static let defaultToppings: Topping = [.cheese, .pepperoni]



    enum Sizes {
        case small
        case medium
        case large
    }


    struct Topping: OptionSet {
        let rawValue: UInt8

        static let cheese = Topping(1 << 0)
        static let pepperoni = Topping(1 << 1)
        static let bellPeppers = Topping(1 << 2)
        static let sausage = Topping(1 << 3)
        static let spinach = Topping(1 << 4)
    }
}
```
{% include code-example-table/combined-end.html %}
