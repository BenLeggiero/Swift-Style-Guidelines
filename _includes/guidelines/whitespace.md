
# Whitespace #
{% include dedicated-guide-page-link.html
    pageLinkFileName='Whitespace'
%}

## Indentation ##

Before lines, use **4 spaces**, not tabs (set this in your preferences). Allow the IDE to guide your indentation practices; if it automatically indents something, don't change that, but adopt it.

Always indent inside curly-brace-blocks, with the sole exception of `switch` `case`s, which appear at the same indentation level as the `switch` statement, itself.

When there are multiple parts of an `if`, `else`, `guard`, or `while` statement, chop them down and align as the IDE prefers. This makes it clear that you have one clear intent for each line.


{% include code-example-table/combined-start.html %}
```swift
if (!foo && baz)
    || (qux && baz) {
    // Content...
}

guard
    let hoge = self.hoge,
    let norf = hoge.norf,
    norf.isInitialized
    else {
        assertionFailure("hoge and norf are required")
        return
}
```
{% include code-example-table/combined-cell-split.html %}
```swift
if (!foo && baz) || (qux && baz) {
  // Content...
}

guard let hoge = self.hoge, let norf = hoge.norf, norf.isInitialized else {
	assertionFailure("hoge and norf are required")
	return
}
```
{% include code-example-table/combined-row-split.html %}
```swift
switch parseError {
case nil:
    return .success

case .undefinedKey:
    continue

case .unexpectedToken:
    return .failure
}
```
{% include code-example-table/combined-cell-split.html %}
```swift
switch parseError {
    case nil:
        return .success

    case .undefinedKey:
        continue

    case .unexpectedToken:
        return .failure
}
```
{% include code-example-table/combined-end.html %}


### Chopping-Down Functions ###
When a function has a long declaration with many parameters, it may be chopped-down at both the declaration and the call site. That is to say, hard-wrapped so that they align nicely. If you prefer to keep it all on one line, that is also OK.

Don't chop-down Objective-C style (so that colons align).


{% include code-example-table/combined-start.html %}
```swift
func foobar(_ baz: Baz,
            for qux: Qux,
            hoge: Hoge,
            norfProvider: (() -> Norf)?) -> Foobar {
    // Content...
}
```
```swift
func foobar(_ baz: Baz, for qux: Qux, hoge: Hoge, norfProvider: (() -> Norf)?) -> Foobar {
    // Content...
}
```
```swift
let x = foobar(baz,
               for: Qux(),
               hoge: baz.hoge,
               norfProvider: nil)
```
```swift
let x = foobar(baz, for: Qux(), hoge: baz.hoge, norfProvider: nil)
```
{% include code-example-table/combined-cell-split.html %}
```swift
func foobar(_ baz: Baz, for qux: Qux,
    hoge: Hoge, norfProvider: (() -> Norf)?) -> Foobar {
    // Content...
}
```
```swift
func foobar(_ baz: Baz,
          for qux: Qux,
             hoge: Hoge,
     norfProvider: (() -> Norf)?
) -> Foobar {
    // Content...
}
```
```swift
let x = foobar(baz, for: Qux(),
    hoge: baz.hoge, norfProvider: nil)
```
```swift
let x = foobar(baz,
           for: Qux(),
          hoge: baz.hoge,
  norfProvider: nil)
```
{% include code-example-table/combined-end.html %}


## Inline Padding ##

The quick summary of the below table is:

-   Colons are `X: Y` or `X : Y`
-   Put a space before opening curly braces
-   Put a space around the assignment "`=`"
-   Don't put spaces around parentheses and angle brackets unless some other rule dictates that you should
-   Keep parentheses as close to the tokens they group as possible


<div class="wide-table-holder code-example-table-holder">
<table class="code-example-table striped-rows whitespace-example-table"><thead>
<th>Place</th> <th>Number of spaces</th> <th class="good">Good</th> <th class="bad">Bad</th>
</thead><tbody>
<tr>
<td markdown="block">
Around typing colon
</td>
<td markdown="block">
-   0 or 1 before
-   1 after

Depending on preference
</td>
<td markdown="block">
```swift
class Foo: Bar {
    // Content...
}

struct Baz : Qux {
    let hoge: Hoge
    var norf : Norf
}
```
</td>
<td markdown="block">
```swift
class Foo:Bar {
    // Content...
}

struct Baz :Qux {
    let hoge:Hoge
    var norf :Norf
}
```
</td>
</tr><tr>
<td markdown="block">
Generics' angle brackets
</td>
<td markdown="block">
-   0

Keep them flush with the type they modify and the types within them.

Rules dictating more spacing override this (**do** place a space after closing angle bracket and opening curly brace).
</td>
<td markdown="block">
```swift
class Foo<Baz> {
    // Content...
}

class Bar: Qux<Hoge, Norf> {
    // Content...
}
```
</td>
<td markdown="block">
```swift
class Foo < Baz > {
    // Content...
}

class Bar: Qux <Hoge, Norf> {
    // Content...
}
```
</td>
</tr><tr>
<td markdown="block">
After type declaration

<small>Between last declaration character and opening curly brace</small>
</td>
<td markdown="block">
-   1
</td>
<td markdown="block">
```swift
struct Foo {
    // Content...
}

protocol Bar: Baz {
    // Content...
}

class Qux: Hoge<Norf> {
    // Content...
}
```
</td>
<td markdown="block">
```swift
struct Foo  {
    // Content...
}

protocol Bar: Baz{
    // Content...
}

class Qux: Hoge<Norf>{
    // Content...
}
```
</td>
</tr><tr>
<td markdown="block">
Around assignment "`=`"

Around operators
</td>
<td markdown="block">
-   1

Consecutive assignments are allowed to have more spacing for pretty alignment.

Note that Swift will not allow things like `x= y` or `x =y`.
</td>
<td markdown="block">
```swift
let foo    = 5 + 7
var bazqux = false
let hoge   = "norf"
```
```swift
let foo = 5 + 7
var bazqux = false
let hoge = "norf"
```
</td>
<td markdown="block">
```swift
let foo=5+7
var foobar  =  false
let hoge =     "norf"
```
```swift
let foo =    5 + 7
var bazqux = false
let hoge =   "norf"
```
</td>
</tr><tr>
<td markdown="block">
Functions
</td>
<td markdown="block">
-   0 between name and open parenthesis
-   0 between open parenthesis and first parameter label (or close parenthesis if no parameters)
-   0 before commas between parameters
-   1 after commas between parameters
-   0 between last parameter and close parenthesis
-   1 between close parenthesis and open brace
</td>
<td markdown="block">
```swift
func foo(bar baz: Qux, hoge: Norf) {
    // Content...
}

func foo2() -> Bool {
    return true
}
```
</td>
<td markdown="block">
```swift
func foo ( bar baz: Qux , hoge: Norf ){
    // Content...
}

func foo2( )->Bool{
    return true
}
```
</td>
</tr><tr>
<td markdown="block">
`if`, `else`, `guard`, `switch`, `while`, and `for` statements
</td>
<td markdown="block">
-   1 around (but not within) necessary parentheses
-   1 before opening brace
Spaces emerge from other rules;

First write or imagine it without parentheses. Then, place parentheses as necessary to group elements.
</td>
<td markdown="block">
```swift
guard foo else {
    assertionFailure("need foo")
    return
}

if (bar && baz) || qux {
    // Content...
}
```
</td>
<td markdown="block">
```swift
guard ( foo ) else{
    assertionFailure("need foo")
    return
}

if(bar && baz) || qux{
    // Content...
}
```
</td>
</tr>
</tbody></table>
</div>


## Blank Lines ##

When in doubt, more blank lines are okay. It's better for readability when things are spaced out rather than crammed together, and storage/bandwidth/compile time is not a concern.

The quick summary of the below table is:

-   0 or 1 blank line between fields
-   2 blank lines between functions and other such blocks
-   3 blank lines between classes and other types

<div class="wide-table-holder code-example-table-holder">
<table class="code-example-table striped-rows whitespace-example-table"><thead>
<th>Place</th> <th>Number of spaces</th> <th class="good">Good</th> <th class="bad">Bad</th>
</thead><tbody>
<tr>
<td markdown="block">
Before opening comment

(copyright, file documentation, etc.)
</td>
<td markdown="block">
-   0
</td>
<td markdown="block" rowspan="0">
{:.numbered-lines}
{% highlight swift linenos %}
//
//  MyFile.swift
//  Project Name
//
//  Created by Ben Leggiero on 2019-07-16
//  Copyright © 2019 Ben Leggiero. All rights reserved.
//

import Cocoa
import FancySDK


let defaultFooSize = 5
fileprivate var instanceCount = 0



class Foo {

    private var usageCount = 0
    let size: Int


    var intValue: Int {
        return usageCount + size
    }


    // MARK: Initialization

    init(size: Int) {
        instanceCount += 1
        self.size = size
    }


    convenience init() {
        self.init(size: defaultFooSize)
    }


    // MARK: Class-specific functions

    func bar() {
        print("Bar!")
        usageCount += 1
    }


    func handleWithBaz(text: String) {

        if let baz = FancySDK.baz {

            let hogeText = text.hoge()
            baz.handle(hogeText)
        }

        print("Did handle baz")

        usageCount += 1
    }
}



// MARK: - Extensions

extension String {
    func hoge() -> String {
        return self + " - Hoge"
    }
}
{% endhighlight %}
</td>
<td markdown="block" rowspan="0">
{% highlight swift linenos %}
//
//  MyFile.swift
//  Project Name
//
//  Created by Ben Leggiero on 2019-07-16
//  Copyright © 2019 Ben Leggiero. All rights reserved.
//
import Cocoa
import FancySDK

let defaultFooSize = 5
fileprivate var instanceCount = 0

class Foo {
    private var usageCount = 0
    let size: Int
    var intValue: Int {
        return usageCount + size
    }


    // MARK: Initialization

    init(size: Int) {
        instanceCount += 1
        self.size = size
    }
    convenience init() {
        self.init(size: defaultFooSize)
    }

    // MARK: Class-specific functions
    func bar() {
        print("Bar!")
        usageCount += 1
    }

    func handleWithBaz(text: String) {
        if let baz = FancySDK.baz {
            let hogeText = text.hoge()
            baz.handle(hogeText)
        }
        print("Did handle baz")
        usageCount += 1
    }

}
// MARK: - Extensions
extension String {
    func hoge() -> String {
        return self + " - Hoge"

    }

}
{% endhighlight %}
</td>
</tr><tr>
<td markdown="block">
Between opening comment and group of imports
</td>
<td markdown="block">
-   1
</td>
</tr><tr>
<td markdown="block">
After group of imports
</td>
<td markdown="block">
-   2 or 3

Depending on following number of variables/constants outside scope
</td>
</tr><tr>
<td markdown="block">
Around group of variables/constants outside scope
</td>
<td markdown="block">
-   2 or 3

Depending on number of variables/constants outside scope
</td>
</tr><tr>
<td markdown="block">
Around types (classes, enums, etc.)
</td>
<td markdown="block">
-   3
</td>
</tr><tr>
<td markdown="block">
At beginning of type

<small>After opening brace and before actual code</small>
</td>
<td markdown="block">
0 or 1

Depending on preference
</td>
</tr><tr>
<td markdown="block">
Around functions and other scoped blocks
</td>
<td markdown="block">
-   2
</td>
</tr><tr>
<td markdown="block">
At beginning of functions and other scoped blocks

<small>After opening brace and before actual code</small>
</td>
<td markdown="block">
-   0 or 1

Depending on preference
</td>
</tr><tr>
<td markdown="block">
At the end of functions and other scoped blocks
</td>
<td markdown="block">
-   0
</td>
</tr><tr>
<td markdown="block">
Around variables
</td>
<td markdown="block">
-   0 or 1

Depending on complexity of declaration
</td>
</tr><tr>
<td markdown="block">

Around local blocks (`if`, closures, etc.)
</td>
<td markdown="block">
-   1
</td>
</tr><tr>
<td markdown="block">
Before `// MARK:`
</td>
<td markdown="block">
-   2
</td>
</tr><tr>
<td markdown="block">
Before `// MARK: -`
</td>
<td markdown="block">
-   3
</td>
</tr><tr>
<td markdown="block">
After `// MARK:` and `// MARK: -`
</td>
<td markdown="block">
-   1
</td>
</tr>
</tbody></table>
</div> <!-- .wide-table-holder -->



## Line breaks ##

<div class="wide-table-holder code-example-table-holder">
<table class="code-example-table striped-rows whitespace-example-table"><thead>
<th>Place</th> <th>Number of spaces</th> <th class="good">Good</th> <th class="bad">Bad</th>
</thead><tbody>
<tr>
<td markdown="block">
-   After type declaration attribute
-   After function attribute
-   After member attribute
</td>
<td markdown="block">
-   1
</td>
<td markdown="block">
```swift
@objc
class Foo: NSObject {
    @objc
    @IBAction
    func bar(_ sender: Any?) {
        // Content...
    }


    @objc(baz)
    var qux: Hoge? = nil


    func norf(callback: @escaping () -> Void) {
        // Content...
    }
}
```
</td>
<td markdown="block">
```swift
@objc class Foo: NSObject {
    @objc @IBAction
    func bar(_ sender: Any?) {
        // Content...
    }


    @objc(baz)


    var qux: Hoge? = nil


    func norf(callback: @escaping
                        () -> Void) {
        // Content...
    }
}
```
</td>
</tr>
</tbody></table>
</div> <!-- .wide-table-holder -->
