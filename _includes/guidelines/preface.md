
{% assign checkSlice = page.url | slice: 0, page.url.size | downcase %}
# {% if checkSlice == '/about/' %}About{% else %}Preface{% endif %} #
{% include dedicated-guide-page-link.html
    pageLinkFileName='About'
%}

Swift is a language whose goal is to be easy to write and read, perform well, and let the compiler do a lot of the work for you. These guidelines attempt to assist in achieving that goal.

If you have not already, read [**The Swift Programming Language**](https://swift.org/documentation/#the-swift-programming-language) (also available as a book!). That introduces concepts and patterns in a way that's necessary before reading this guide.

If that book doesn't appeal to you, to can also look at [**Apple's Swift Language Guide**](https://developer.apple.com/library/content/documentation/Swift/Conceptual/Swift_Programming_Language/TheBasics.html#//apple_ref/doc/uid/TP40014097-CH5-ID309), which is in a more traditional documentation format, including lexical structure.

Overall, embrace Swift. **Swift-y patterns in `.swift` files are preferred over patterns used in other languages.** Following this simple rule helps the compiler create better assembly, avoids bugs, and makes the code more maintainable.

If you disagree with any of these or have something new to add, you are encouraged to create a pull request or issue using the links at the bottom.


## Compatibility ##

These guidelines are relevant and accurate as of Swift 5.1


## Deferral ##

Primarily, if anything is not explicitly defined here, defer to the below list (prefer items higher on the list). If you still can't find anything in this or those guides, then what you are trying to do is freeform; do it however you prefer.

With very few exceptions, if Xcode's build-in code completion/formatting does it some way, that is the way we want it done. If the compiler issues a warning about it, address that warning. **Don't fight the IDE.**

1.  [**The Swift.org API Design Guidelines**](https://swift.org/documentation/api-design-guidelines/)
2.  <span class="lozenge lozenge-good">HIGHLY ENCOURAGED!</span> **WWDC 2016 Session 403**: [Official](https://developer.apple.com/videos/play/wwdc2016/403/) \| [Unofficial YouTube mirror](https://www.youtube.com/watch?v=UU2fNq35xkM)
3.  [**Google's Swift Style Guide**](https://google.github.io/swift/)
4.  The style already present in the file you're editing
