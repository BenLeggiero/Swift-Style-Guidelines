
# Punctuation #
{% include dedicated-guide-page-link.html
    pageLinkFileName='Punctuation'
%}

## Semicolons ##

Don't use semicolons. If you think you need to use semicolons, try to find a different way to do it.

### Sole Exception ###

There is actually one case where semicolons are acceptable in the code: When a scope opens and you are logging both the entrance and exit of that scope. In this case, you may place both these log lines and the defer block on the same line of code.

{% include code-example-table/combined-start.html
    left='Good'
    right-class='good'
    right-name='Acceptable'
 %}
```swift
func importantFunction() {
    logFunctionEntry()
    defer {
        logFunctionExit()
    }
}
```
{% include code-example-table/combined-cell-split.html %}
```swift
func importantFunction() {
    logFunctionEntry(); defer { logFunctionExit() }
}
```
{% include code-example-table/combined-end.html %}


## Braces ##

Along with Apple's guidelines, curly braces (`{` and `}`) open on the same line and close on a new line. Curly braces that are the first non-whitespace character on a line must be indented to the same level as the code around its block, not inside it.

The main difference between our guidelines and Apple's is the `else` statement; we place them on the line _following_ their `if`'s closing brace, whereas Apple places them on the _same_ line.

Braces are required, by all guidelines and by the compiler, for all `if`, `else`, `guard`, `switch`, `while`, and `for` statements.

<div class="wide-table-holder code-example-table-holder">
<table class="code-example-table"><thead>
<th class="good">Good</th> <th class="bad">Bad</th>
</thead><tbody><tr>
<td markdown="block">
```swift
class Foo {
    func bar() {
        if baz {
            qux.hoge()
        }
        else {
            qux.norf()
        }
    }
}
```
</td>
<td markdown="block">
```swift
class Foo
{ // Opening curly braces belong on the same line
    func bar()
        { // Indent standalone braces along with the code AROUND the block, not inside
        if baz {
            qux.hoge()
            } // Indent standalone braces along with the code AROUND the block
        else {
            qux.norf()} // This obfuscates the end of the block
    }
}
```
</td>

</tr><tr>

<td markdown="block">
```swift
if foo {
    bar()
}
else if let baz = self.baz {
    baz.qux()
}
else {
    hoge()
}
```
</td>
<td markdown="block">
```swift
if foo {
    bar()
} else if let baz = self.baz { baz.qux() }
else
{
    hoge()
}
```
</td>
</tr></tbody></table>
</div>


## Parentheses ##

> <span class="lozenge lozenge-good">RULE-OF-THUMB</span> When in doubt, more parentheses is better.

`if`, `else`, `guard`, `switch`, `while`, and `for` statements **should not** use parentheses unless necessary for compiling, or if their logic is so complex that parentheses are required for clear human andor compiler understanding. If this logic is very complex, consider moving it into its own function.

**Always strive for clarity**; when using multiple operators (mathematical, Boolean, or otherwise), use parentheses to explicitly group each pair. This ensures it does what you think it does, and future readers know what you intended it to do.

{% include code-example-table/combined-start.html %}
```swift
if foo {
    bar()
}
else if baz, qux {
    hoge()
}
else if norf {
    foobar = ((2 + (6 / foobar)) * 3)
}

// ...

var norf: Bool {
    return (!foo && baz)
            || (qux && !baz)
}
```
{% include code-example-table/combined-cell-split.html %}
```swift
if (foo) {
    bar()
}
else if (baz && qux) {
    hoge()
}
else if !foo && baz || qux && !baz {
    foobar = 2 + 6 / foobar * 3
}
```
{% include code-example-table/combined-end.html %}
