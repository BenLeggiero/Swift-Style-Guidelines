
# Scoping #
{% include dedicated-guide-page-link.html
    pageLinkFileName='Scoping'
%}

Also called "visibility" or "access level", it's important to use the proper scope to ensure your code is used properly, both by others and by Future You.

**Prefer more-private scopes** first, and only make something more public if necessary.

In case it's unclear, here are the visibility scopes as of **Swift 5**:

-   **`private`**: <small class="comment">Only the current context can see this</small>
    -   **This is our preferred default scope**
    -   Top-level in file: Only visible in this file; synonymous with `fileprivate`
    -   In `class`, `struct`, `enum`, `extension`, etc.: Only visible in this type, and only in this file. When used in a nested type, it's only visible in the nested one, not its parent.

-   **`fileprivate`**: <small class="comment">Only (and all) things in this file can see this</small>
    -   Top-level in file: **Avoid this**; synonymous with `private`
    -   In `class`, `struct`, `enum`, `extension`, etc.: **This is okay**; use this for utilities only needed in this file

-   **`internal`**: <small class="comment">Only (and all) things in this Swift module can see this</small>
    -   You may use this liberally or judicously, to your preference

-   **`public`**: <small class="comment">Everything can see this</small>
    -   **This is okay, but only if necessary.**

-   **`open`**: <small class="comment">By itself, this is public, but subclassable/overridable.</small>
    -   **If subclassing/overriding is the goal, great. Otherwise, avoid this**, or at least pair it with another scope to shield it from the public.

-   **unspecified**
    -   Assume surrounding context's visibility. **This is OK.**
    -   If top-level (no surrounding context), synonymous with `internal`. **Avoid this.**
