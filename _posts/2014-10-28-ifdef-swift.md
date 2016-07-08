---
title: Swift Conditional Compilation
permalink: swift-conditional-compilation
---

Check for the presence of a flag for conditional compilation in Swift using `#if` `#else` `#endif`. 

Set the symbol in the Xcode Project Build Settings under

`Swift Compiler - Custom Flags - Other Swift Flags`. 

Usage:
For example, using `-D DEBUG`

```swift
#if DEBUG
    println("debug build")
#else
    println("not a debug build")
#end
```

To set multiple symbols:
For example, `DEBUG` and `STAGING` can both be set with `-D DEBUG -D LEGACY`.
Both symbols must be prefaced with the `-D` option.

Found [here](http://stackoverflow.com/a/24152730/919790) 

