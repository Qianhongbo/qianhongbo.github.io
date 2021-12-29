---
title: Associated Value
date: 2021-12-28 14:27:30
categories: 
- iOS
---

# Review about "enum"

Enums can have raw values and associated values. A raw value is a string, character, or number (integer or floating-point) that can represent an enum case.

```swift
enum Position: Int {
    case first = 1
    case second = 2
    case third = 3
    case fourth = 4
}
```

The example above defines a `Position` enum and uses type annotation to specify that all cases must have a raw integer value.

```swift
// ": Int" is the type annotation
enum Position: Int {
```

Then, for each case, a raw integer value is explicitly defined after the case name.

```swift
    case first = 1
    case second = 2
    case third = 3
    case fourth = 4
```

# Raw Value to Enum

Raw values enable developers to convert values into enum cases, and vice versa. Let’s keep working with the `Position` enum.

To convert an enum case into its raw value, use its `rawValue` property:

```swift
// convert a Position into a raw integer value
let myPosition = Position.first
let myPositionNumber = myPosition.rawValue
print(myPositionNumber) // prints "1"
```

To convert a raw value to its respective enum case, use the initializer that takes a `rawValue` argument.

```swift
// convert an integer into a Position
let rivalPositionNumber = 4
let rivalPosition = Position(rawValue: rivalPositionNumber)
print(rivalPosition == .fourth) // prints "true"
```

Since there is no guarantee that a raw value has a corresponding case, any attempt to create an enum from a raw value will return an optional. To be safe, use `if let` to create enums from raw values.

```swift
var randomNumber = 5

// safely create enums from raw values
if let newPosition = Position(rawValue: randomNumber) {
    print("created \(newPosition) from raw value")
} else {
    print("cannot create position from raw value")
}
```

# Implicit Raw Values

In certain situations, the Swift compiler can infer raw values for enum cases.

If an enum uses raw string values, then each case is implicitly assigned a raw string value equal to the case's name.

```swift
enum AudioRate: String {
    case slow, normal, fast, custom
}

print(AudioRate.slow.rawValue) // prints "slow"
print(AudioRate.custom.rawValue) // prints "custom"
```

If an enum uses raw integer values, then the compiler assigns zero to the first case, and increases the raw value for subsequent cases by one.

```swift
enum Endurance: Int {
    case worst, abysmal, poor, medium, strong
}

print(Endurance.poor.rawValue) // prints "2"
print(Endurance.strong.rawValue) // prints "4"
```

It is also possible to define an enum with implicitly and explicitly defined raw values; all implicitly defined raw integer values will be one greater than the previous raw integer value. If no previous raw integer value exists, then the compiler will assign zero to the enum case.

```swift
enum SkewedEndurance: Int {
    case worst, abysmal = 10, poor, medium, strong = 30
}

print(SkewedEndurance.worst.rawValue) // prints "0"
print(SkewedEndurance.poor.rawValue) // prints "11"
print(SkewedEndurance.medium.rawValue) // prints "12"
print(SkewedEndurance.strong.rawValue) // prints "30"
```

You can mix and match implicit and explicit raw string values too:

```swift
enum Axis: String {
    case x
    case y
    case z = "zed"
}

print(Axis.y.rawValue) // prints "y"
print(Axis.z.rawValue) // prints "zed"
```

# Associated values

```swift
//: Associated values are defined alongside enum cases. Associated values are not required; some enum cases may have an associated value while others do not. In the example below, `LibraryFee` has three cases with associated values and a case without an associated value.
enum LibraryFee {
    case overdueBook(Int)
    case lostBook(Double)
    case lostLibraryCard(Int)
    case annualDues
}

let fee = LibraryFee.overdueBook(4)

//: It can be very helpful to name associated values so that their intent is easily understood.
enum DescriptiveLibraryFee {
    case overdueBook(days: Int)
    case lostBook(price: Double)
    case lostLibraryCard(timesLost: Int)
    case annualDues
}

let weekLateFee = DescriptiveLibraryFee.overdueBook(days: 7)

//: Associated values are actually tuples. Therefore, an associated value can contain mutliple values.
import UIKit

enum ImageFilter {
    case sepia
    case verticalGradient(from: UIColor, to: UIColor)
    case horizontalGradient(from: UIColor, to: UIColor)
    case sketch(penThickness: Double?)
}

let fadeToBlack = ImageFilter.horizontalGradient(from: .gray, to: .black)
```

```swift
//: - Callout(Watch Out!):
//: If all enum cases have an associated value of the same type, and it is static, then you might consider using a raw value instead.
enum AudioRateAssociated {
    case slow(value: Int)
    case normal(value: Int)
    case fast(value: Int)
    case custom(value: Int)
}

// the associated values for `AudioRateAssociated` should be raw values
enum AudioRateRaw: Int {
    case slow, normal, fast, custom
}
```

# Exact the associated value with switch

```swift
import UIKit

enum ImageFilter {
    case sepia
    case verticalGradient(from: UIColor, to: UIColor)
    case horizontalGradient(from: UIColor, to: UIColor)
    case sketch(penThickness: Double?)
}

let filter1 = ImageFilter.horizontalGradient(from: .gray, to: .black)
let filter2 = ImageFilter.horizontalGradient(from: .white, to: .black)
```

## Switch

```swift
//: Most often, associated values are extracted in a switch block. For cases that have an associated value, the `let` keyword followed by a name will extract each  value from an associated value.
switch filter1 {
case .sepia:
    print("sepia")
case .verticalGradient(let color1, let color2):
    print("vertical gradient with \(color1) and \(color2)")
case .horizontalGradient(let color1, let color2):
    print("horizontal gradient with \(color1) and \(color2)")
case .sketch(let penThickness):
    if let thickness = penThickness {
        print("sketch using \(thickness) thickness")
    } else {
        print("sketch using default thickness")
    }
}

//: Associated values can also be extracted as variables using the `var` keyword. Values extracted as variables are only available in the case where they are declared.

switch filter1 {
case .horizontalGradient(var color1, let color2):
    color1 = .blue
    print("horizontal gradient with \(color1) and \(color2)")
default:
    break
}
//: To extract all values from an associated value as constants, use the `case let` syntax.

switch filter1 {
case let .horizontalGradient(color1, color2):
    print("horizontal gradient with \(color1) and \(color2)")
default:
    break
}
//: To extract all values from an associated value as variables, use the `case var` syntax.

switch filter1 {
case var .horizontalGradient(color1, color2):
    color1 = .red
    color2 = .blue
    print("horizontal gradient with \(color1) and \(color2)")
default:
    break
}
```

## Watch out!

```swift
//: - Callout(Watch Out!):
//: Remember, switch statements must be exhaustive or Xcode will complain.
// uncomment the switch statement below to see Xcode complain about a non-exhaustive switch statement.
/*
 switch filter1 {
 case .horizontalGradient(var color1, let color2):
 color1 = .blue
 print("horizontal gradient with \(color1) and \(color2)")
 }
 */

//: If it is possible to treat two cases exactly the same, even when they have associated values, then you can combine cases. Note, the associated values must be the same type.
switch filter1 {
case .verticalGradient(let color1, let color2), .horizontalGradient(let color1, let color2):
    print("a gradient from \(color1) to \(color2)")
default:
    break
}

//: - Callout(Watch Out!):
//: If two cases are specified with a single case statement, then the tuple element names must match.
 switch filter1 {
    // uncomment the case statement below to see Xcode complain that `color2` and `color3` must be used in every pattern (i.e. the tuple element names don't match)
    /*
    case .verticalGradient(let color1, let color3), .horizontalGradient(let color1, let color2):
        print("a gradient from \(color1) to \(color2)")
     */
 default:
    break
 }

//: If associated values are not needed for computation, then they can be ignored. The example below ignores the associated value for the horizontal gradient.
switch filter1 {
case .horizontalGradient:
    print("the filter is a horizontal gradient!")
default:
    break
}

//: It is also possible to partially ignore values in an associated value, while extracting others.
switch filter1 {
case .horizontalGradient(let color1, _):
    print("the horizontal gradient's first color is \(color1)")
default:
    break
}
```

## "where" keyword

```swift
//: Associated values may also be extracted based on conditions specified using the `where` keyword. If all conditions are held, then the values are extracted and the case statement is executed.
switch filter1 {
case .horizontalGradient(let color1, _) where color1 == .white:
    print("the horizontal gradient's first color is white")
case .horizontalGradient(_, let color2) where color2 == .black:
    print("the horizontal gradient's second color is black")
default:
    break
}
```

## Computed properties

```swift
//: Associated values can be extracted using computed properties. By using a computed property, you may avoid duplicate switch statements.
extension ImageFilter {
    var colors: (from: UIColor, to: UIColor)? {
        switch self {
        case .verticalGradient(let from, let to),
             .horizontalGradient(let from, let to):
            return (from, to)
        default:
            return nil
        }
    }
    
    var penThickness: Double? {
        switch self {
        case .sketch(let penThickness):
            return penThickness
        default:
            return nil
        }
    }
}

let filter3 = ImageFilter.sketch(penThickness: 4.0)

filter1.colors
filter1.colors?.from
filter1.colors?.to
filter3.colors // nil 

filter1.penThickness // nil
filter3.penThickness // 4
```

# Extract with if

```swift
import UIKit

enum ImageFilter {
    case sepia
    case verticalGradient(from: UIColor, to: UIColor)
    case horizontalGradient(from: UIColor, to: UIColor)
    case sketch(penThickness: Double?)
}

let filter1 = ImageFilter.horizontalGradient(from: .gray, to: .black)
let filter2 = ImageFilter.horizontalGradient(from: .white, to: .black)
```

```swift
//: `if case` works alongside the equals operator (=) to check and extract an associated value from an enum. While the syntax may look strange, the equals operator still behaves in an intuitive way; that is, the associated value on the left-hand side (containing `color1` and `color2`) is set equal to the associated value for the enum on the right-hand side (`filter1`).
if case ImageFilter.horizontalGradient(let color1, var color2) = filter1 {
    color2 = .red
    print("horizontal gradient with \(color1) and \(color2)")
}

//: To extract all values as constants use `if case let`.
if case let ImageFilter.horizontalGradient(color1, color2) = filter1 {
    print("horizontal gradient with \(color1) and \(color2)")
}

//: Complex conditionals can be formed using `if case` and conditional statements separated by commas. If the conditional statements are held true, then the values are extracted and usable from within the `if case` block.
if case let ImageFilter.horizontalGradient(_, color2) = filter1, color2 == .black {
    print("the horizontal gradient's second color is \(color2)")
}

//: A single associated value can also be extracted as a computed property. By using a computed property, you may avoid duplicate `if case` statements.
extension ImageFilter {
    var hasHeavyPenThickness: Bool {
        if case let ImageFilter.sketch(penThickness) = self, let thickness = penThickness, thickness > 5.0 {
            return true
        } else {
            return false
        }
    }
}

filter1.hasHeavyPenThickness
```

# Enums with Enums

```swift
enum ShirtSize {
    case extraSmall
    case small
    case medium
    case large
    case extraLarge
}

enum Search {
    case forShirts(sizes: [ShirtSize])
    case forName(name: String)
}

let searchForBigShirts = Search.forShirts(sizes: [.large, .extraLarge])
let searchForHenleys = Search.forName(name: "henley")
```

