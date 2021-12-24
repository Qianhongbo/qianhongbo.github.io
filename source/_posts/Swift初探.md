---
title: Swift初探
date: 2021-12-10 20:12:53
categories: 
- iOS
tags: 
- Swift
---

# Variables and Types 

## Hello world!

```swift
var question = "Hello world!"
print(question)
```

## Comments

```swift
// this is a comment

/* this is a 
   multiline comment */

/*
print("neither will this")
print("or this")
*/
```

## Data types

| Type      | Description                                                  | Example Values                          |
| --------- | :----------------------------------------------------------- | --------------------------------------- |
| Int       | whole number values                                          | 0, 2, -2, 100, ...                      |
| Float     | floating-point (decimal) numbers that require no more than 6 decimal digits (32-bit) | 3.14, 5.693, -12.321, ...               |
| Double    | floating-point (decimal) numbers that require more precision—guaranteed 15 decimal digits (64-bit) | 3.14, 5.693, -12.321, ...               |
| Bool      | a Boolean truth value                                        | true, false                             |
| Character | a single letter, digit, symbol, or code                      | "a", "+", "q", ...                      |
| String    | combinations of characters                                   | "swift", "I Love Swift", " ", ":)", ... |

## Creating variables

```swift
var numberOfWheels: Int = 4
var centimetersOfRainfall: Float = 5.5
var pi: Double = 3.14159265359
var letterOfTheDay: Character = "z"
var myFavoriteAnimal: String  = "octopus"
var rainingOutside: Bool  = true
```

The code below is also okay, because Siwft can infer what is the type of the variable.

```Swift
var firstName = "Aisha"
```

Change the value of the variable, but we can't change the type of the vcariable.

```swift
// initial declaration of petsAge
var petsAge = 12

petsAge = 13
```

Constant.

```swift
let encouragement = "You can do it!"
var customizedEncouragement = "You can do it, Stephanie!"
customizedEncouragement = "You can do it, Ryder!"
```

String.

```swift
let emailSignature = "Aliyah Smith\nVP, Sales\n(555) - 555 - 5555"
```

| **Character** | **Usage**                                                    |
| :------------ | :----------------------------------------------------------- |
| \n            | Newline / line feed                                          |
| \t            | Horizontal tab                                               |
| \"            | Double quotation mark, using `"` alone signifies the end of a string |
| \'            | Single quotation mark                                        |
| \             | Backslash, using "\" alone escapes the next character        |

```swift
var a = "Happy birthday, \("Maverick")!"
print(a)
```

# Operators and Expressions

```swift
5 / 3 // The result is 1

5 / 3.0 // The result is 1.66666667

var hello = "Hello"
var world = "world!"
let greeting = hello + " " + world
```

# Ternary Operators

**Ternary operator** : an operator that is applied to three operands

Ternary operators are much less common than binary operators, but one of the most common ternary operators is the **ternary conditional operator**. It is used to choose between two values based on a truth (Boolean) value, and it uses the following syntax:

```swift
trueOrFalseValue ? useThisIfTrue : useThisIfFalse
```

# Control Flow

```swift
if onGuestList && age >= 21 {
    print("\(name), come party with us!")
}

if !onGuestList || age < 21 {
    print("Sorry, \(name), maybe you can go play Bingo with the Android team.")
}


if breakfast {
    print("Good morning!")
} else if lunch {
    print("Good afternoon!")
} else if dinner {
    print("Good evening!")
} else {
    print("Hello!")
}

switch month {
case 1: print(“January”)
case 2: print(“February”)
// ...
}

let meal = "breakfast"

switch meal
{
    case "breakfast": print("Good morning!")
    case "lunch": print("Good afternoon!")
    case "dinner": print("Good evening!")
    default: print("none")
}
```

```swift
1...10 // 1, 2, 3, 4, 5, 6, 7, 8, 9, 10.
1..<10 // 1, 2, 3, 4, 5, 6, 7, 8, 9.

for i in 1...10{
    print(i)
}

var i = 1

while (i<1001){
    if (i % 2) == 0{
        print(i)
    }
    i += 1
}

// repeat-while loop
var i = 1
repeat {
    print("Hello, world!")
    i += 1
} while i < 0
```

# Functions

```swift
func printLove(s: String){
    print("I love \(s)")
}

printLove(s: "Swift")

func printLove(s: String){
    print("I love" + s)
}

printLove(s: "Swift")

func areaOfRectangle(length: Int, width: Int){
    print(length * width)
}

areaOfRectangle(length: 2, width: 5)

func calculateTip(priceOfMeal: Double) -> Double {
    return priceOfMeal * 0.15
}

func isPastBedtime(hour: Int) -> Bool {
    if hour > 9 {
        return true
    } else {
        return false
    }
}

func addValues(value1 x: Int, value2 y: Int) -> Int {
    // internally, use `x` and `y`
    return x + y
}

// externally, use `value1` and `value2`
addValues(value1: 5, value2: 10)

func addExcitementToString(_ string: String) -> String {
    return string + "!"
}

addExcitementToString("Swift")
```

# Structures and Enums

```swift
struct Student {
    let name: String
    var age: Int
    var school: String
}

var ayush = Student(name: "Ayush Saraswat", age: 19, school: "Udacity")

struct Beaker {
    var volumeMilliliters: Double

    func canContainContents(otherBeaker: Beaker) -> Bool {
        return volumeMilliliters >= otherBeaker.volumeMilliliters
    }
}

let beaker1 = Beaker(volumeMilliliters: 500)
let beaker2 = Beaker(volumeMilliliters: 1000)
let canContainContents: Bool = beaker1.canContainContents(otherBeaker: beaker2)

// If, however, a method tries to modify (or mutate) one of the struct's properties, then the mutating keyword must be added before func. For example
mutating func increaseCapacity() {
    volumeMilliliters += 500
}

struct Beaker {
    let volumeMilliliters: Double
    static var madeIn = "China"

    // These are US ounces
    var volumeOunces: Double {
        return volumeMilliliters * 0.033814
    }

    func canContainContents(otherBeaker: Beaker) -> Bool {
        return volumeMilliliters >= otherBeaker.volumeMilliliters
    }
}
// In fact, when we change the value of a static property with dot notation, we reference the type name and not a specific instance (Beaker.madeIn).
```

```swift
enum PrimaryColor {
    case red
    case blue
    case yellow
}

enum Aunties {
    case aime, billie, diane, gail, janie, pam
}

enum DrinkSize: Int {
    case small = 12
    case medium = 16
    case large = 20
}

// rawValue
Var sugar = "A \(DrinkSize.small.rawValue) oz Coke has 33 g of sugar"
// "A 12 oz Coke has 33 g of sugar"

// Using 'enum' with 'switch'
enum Finger: Int {
    case thumb = 1
    case index = 2
    case middle = 3
    case ring = 4
    case pinky = 5
}

var destination = Finger.thumb
var test = Finger(rawValue: 3)

// switch destination
switch test {
    case .thumb:
        print("thumb")
    case .index:
        print("index")
    case .middle:
        print("middle")
    case .ring:
        print("ring")
    case .pinky:
        print("pinky")
  // We need to use none for 'test'
    case .none:
        print("None!")
}
```

