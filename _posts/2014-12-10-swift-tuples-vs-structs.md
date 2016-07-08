---
title: Swift Tuples vs Structs
layout: default
permalink: swift-tuples-vs-structs
---

[Answer](http://stackoverflow.com/a/27386550/919790) to a StackOverflow [question](http://stackoverflow.com/q/27384151/919790) concerning the differences between tuples and structs.

**Similarities Between Tuples and Structs**

- Both may have any number of members of any type, including closures
- Both can be constructed inline (see `typealias` in the code below)
- Both prevent mutation of any members if declared as constants
- If a tuple has labeled members, both structs and tuples allow member access by label


**Differences Between Tuples and Structs**

- Structs require a definition before use
- Structs do not allow pattern matching against their members
- Structs allow mutability of members declared as variables if the instance is a variable
- Tuples do not allow mutating functions or functions that refer to any of its members
- Tuples may not implement Protocols
- If a tuple has anonymous members, its members can be accessed by index, unlike structs


**Some code for a playground illustrating these differences and similarities**


```swift
// All commented code causes a compilation error. Uncomment to view error messages.

struct StructureX {
    let a: Int = 0
    var b: String = "string"
}

//
// Struct member variability
//
var structureA: StructureX = StructureX()
let structureB: StructureX = StructureX()
//structureA.a = 2              // declared as a constant, instance is variable
structureA.b = "allowed"        // declared as a variable, instance is variable
//structureB.a = 2              // declared as constant, instance is constant
//structureB.b = "not allowed"  // declared as constant, instance is constant
structureA = structureB         // these are the same type
structureA


//
// Typealias a labeled tuple and it can be constructed similarly to a struct
//
typealias StructureT = (a: Int, b: String)
var structureD: StructureT = StructureT(a: 0, b: "asdf")
structureD
var structureE: StructureX = StructureX(a: 0, b: "asdf")
//structureD = structureA       // but they are distinct types



let emptyTuple: () = ()         // philosophically, isn't this the definition of Void?
let single: (Int) = (23)
//let namedSingle: (a: Int) = (a: 42)



//
// Tuple Labeled Member Access
//
var labeledTupleA: (a: Int, b: String) = (a: 0, b: "string")
labeledTupleA.0 = 5
labeledTupleA.a
labeledTupleA

var check: (a: Int, b: String)
check = labeledTupleA           // same type
check

//
// Tuples can have functions/closures
//
let labeledTupleB: (Int, String, fun: () -> Void) = (0, "string", { () -> Void in
    println("hi")
})
labeledTupleB.1
labeledTupleB.fun()
//labeledTupleB.0 = 10          // this tuple is a constant, so all of its members are constant


//
// Tuples with members of the same type, but differet labels are not of the same type
//
var labeledTupleC: (c: Int, d: String) = (c: -1, d: "fail")
//labeledTupleC = labeledTupleA
//labeledTupleC = labeledTupleB


//
// Tuples with anonymous members matching the type pattern of a labeled member tuple are of equivalent type
//
var unlabeledTuple: (Int, String) = (0, "good")
unlabeledTuple = labeledTupleA
unlabeledTuple = labeledTupleC


//
// Tuples with closures may not refer to sibling members
//
var labeledTupleD: (de: Int, df: (Int) -> Void) = (de: 0, df: { (num: Int) -> Void in
    //de += num
    //self.de += num
    println(num)
})

labeledTupleD.de
labeledTupleD.df(1)


//
// Tuples allow pattern matching, Structs do not
//
//switch structureA {
//case (let i, let s):
//    print(i, s)
//default:
//    break
//}

switch labeledTupleD {
case (_, let closure):
    closure(123)
default:
    break
}








```