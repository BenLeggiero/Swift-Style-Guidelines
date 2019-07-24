
# Objective-C Interop #
{% include dedicated-guide-page-link.html
    pageLinkFileName='Objective-C-Interop'
%}

Sometimes it is necessary to write a Swift class or function that is accessible by Objective-C code. For this, we have a pattern that allows us to keep everything important in Swift Land, only using a thin wrapper to expose our code to Objective-C Land. This has the added benefit of keeping all code that ties us to Objective-C contained, so as we move away from it, the only changes that must be made to Swift files are deleting these thin wrappers.



{% include code-example-table/combined-start.html %}
```swift
struct MySwiftStruct {
    let foo: [String]

    var bar: Int? {
        didSet {
            baz()
        }
    }
}



@objc(MySwiftStruct)
@available(swift, deprecated: 0.0.1, message: "Available in Objective-C Only.")
class __objc__MySwiftStruct : NSObject {
    private var basis: MySwiftStruct


    var foo: NSArray { return basis.foo as NSArray }


    var bar: NSNumber? {
        get {
            return basis.bar.map(NSNumber.init(value:))
        }
        set {
            basis.bar = newValue.map { $0.intValue }
        }
    }
}
```
{% include code-example-table/combined-cell-split.html %}
```swift
@objcMembers
class MySwiftStruct : NSObject { // Sacrifices the powerful & safe value semantics
    let foo: [String] // But what if ObjC tries to put a NSURL in here?


    var bar: Int? { // Unclear how this is represented in ObjC
        didSet {
            baz()
        }
    }
}
```
{% include code-example-table/combined-end.html %}

> <span class="lozenge lozenge-good lozenge-hollow">EXCEPTION</span> Assuming their counterparts are well-documented and easy to find, it is OK to not document these thin `__objc__` wrappers.
