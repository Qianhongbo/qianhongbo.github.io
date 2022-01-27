---
title: Swift knowledge supplement
date: 2021-12-28 14:27:30
categories: 
- iOS
---

# Associated Value

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

## Raw Value to Enum

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

## Implicit Raw Values

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

## Associated values

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

## Extract the associated value with switch

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

### Switch

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

### Watch out!

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

### "where" keyword

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

### Computed properties

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

## Extract with if

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

## Enums with Enums

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

# Guard

## Guard statement

```swift
func takeOff(passengersSeated: Bool, crewReady: Bool, runwayClear: Bool) {
    guard passengersSeated else { return }
    guard crewReady else { return }
    guard runwayClear else { return }
    print("✈️ Lifts off runway")
}

// the runway isn't clear, the airplane cannot take off
takeOff(passengersSeated: true, crewReady: true, runwayClear: false)

// The same
func takeOffCombineGuards(passengersSeated: Bool, crewReady: Bool, runwayClear: Bool) {
    guard passengersSeated, crewReady, runwayClear else { return }
    print("✈️ Lifts off runway")
}

// Add sth
func takeOffGuardWithCode(passengersSeated: Bool, crewReady: Bool, runwayClear: Bool) {
    guard passengersSeated, crewReady, runwayClear else {
        print("tell passengers there will be a delay")
        return
    }
    print("✈️ Lifts off runway")
}
```

## Guard versus If

The `guard` and `if` keywords can serve similar purposes in Swift, but they should not be used interchangeably. Instead, use the following guidelines to decide when to use guard versus if:

- `guard` should be used to check for preconditions and early exit
- `if` should be used to change execution path based on some condition(s)

> Guard: whether the credit card is validate and whether the order is scheduled
>
> If: wether the user enter a discount code

## Guard with Optionals

```swift
func takeOffGuardVar(passengersSeated: Bool, crewReady: Bool, runwayClear: Bool, crewLeader: String?) {
    guard passengersSeated, crewReady, runwayClear else { return }
    guard var crewLeaderVariable = crewLeader else { return }
    
    // modify the crew leader
    crewLeaderVariable = crewLeaderVariable.uppercased()
    
    print("\(crewLeaderVariable): \"Takeoff checks complete!\"")
    print("✈️ Lifts off runway")
}

takeOffGuardVar(passengersSeated: true, crewReady: true, runwayClear: true, crewLeader: "👩🏻‍✈️ Natalie")
```

### Example 1

```swift
struct Student {
    var name: String
    var grades: [Double]
    var average: Double {
        return grades.reduce(0) { return $0 + $1 } / Double(grades.count)
    }
}

func passStudent(_ student: Student?) {
    guard let student = student, student.average >= 75 else { return }
    print("\(student.name) passed!")
}

passStudent(Student(name: "Sam", grades: [70, 90, 84, 62, 88]))
```

#### The reduce funtion

```swift
let numbers = [1, 2, 3, 4]
let numberSum = numbers.reduce(0, { x, y in
x + y
})
// numberSum == 10
```

[One website.](https://developer.apple.com/documentation/swift/array/2298686-reduce).

 [Another website](https://stackoverflow.com/questions/56034602/what-does-the-reduce-function-do-in-swift).

#### Swift `in` keyword. 

```swift
{ /parameters and type/ in /function body/ }
```

#### `$0` and `$1`

`$0` is the first parameter passed into the closure. `$1` is the second parameter, etc. 

```swift
add = { arg1, arg2 in
arg1 + arg2
}

add = {
$0 + $1
}

let result = add(20, 20) // 40
```

### Example 2

```swift
struct Student {
    var name: String
    var grades: [Double]
    var topPassingGrade: Double? {
        let passingGrades = grades.filter { return $0 >= 75 }
        return passingGrades.max() // It can be "nil".
    }
}

func notifyUser(_ student: Student) {
    guard let _ = student.topPassingGrade else { return }
    print("you've passed!")
}

notifyUser(Student(name: "Ulysses", grades: [70, 60, 74, 76, 63]))
```

> [The filter function](https://developer.apple.com/documentation/swift/sequence/3018365-filter)
>
> ```swift
> let cast = ["Vivien", "Marlon", "Kim", "Karl"]
> let shortNames = cast.filter { $0.count < 5 }
> print(shortNames)
> // Prints "["Kim", "Karl"]"
> ```

## Guard let Versus If let

- When optional values are used with `guard let` (or `guard var`) they are bound as non-optional values and available in the rest of the scope where the guard statement appears.
- This differs from how optionals work with `if let`. With `if let`, optionals are bound as non-optional constants, and they are only available in the body of the `if let` statement.

> After the 'guard let' judgement, the optional is transformed to a variable or constant.

# Errors

The errors referred to by "error handling" are not like warnings or issues raised by Xcode and the compiler. They are also not software bugs. Instead, these errors are either intentionally caused or generated in situations where correct behavior cannot be fully guaranteed.

- a compile-time error (or warning) can be generated by the compiler, before code is run

- a run-time error (sometimes called a logic error) is caused by code that is syntactically correct, but logically incorrect or causes a crash when code is running

These kinds of errors can be fixed.

The errors in "error handling" can happen even when code is syntactically correct.

```swift
// When a catch block is executed, it automatically defines a constant called "error". 
// This constant is of type `Error` and it has a localized description property with helpful information about the cause of the problem.
if let fileURL = Bundle.main.url(forResource: "swift", withExtension: "png") {
    do {
        let content = try String(contentsOf: fileURL, encoding: .utf8)
        print(content)
    } catch {
        print(error) // notice, error is implicitly defined
        print(error.localizedDescription)
    }
}

//: For readability, the "error" constant can be explicitly defined using `catch let error`.
if let fileURL = Bundle.main.url(forResource: "swift", withExtension: "png") {
    do {
        let content = try String(contentsOf: fileURL, encoding: .utf8)
        print(content)
    } catch let error {
        print(error) // error is defined and available for use in the catch block
    }
}
```

## Types of error

We can use `do catch` to handle different type of the error. 

```swift
if let fileURL = Bundle.main.url(forResource: "swift", withExtension: "png") {
    // create a url that does not exist
    let erroneousURL = fileURL.appendingPathComponent("unknown-path")
    
    do {
        let content = try String(contentsOf: erroneousURL, encoding: .utf8)
        print(content)
    } catch CocoaError.fileReadInapplicableStringEncoding {
        print("cannot read file into a string")
    } catch CocoaError.fileReadUnknown {
        print("file unknown, cannot read")
    } catch {
        print("\(error)")
    }
}
```

> How can we get the error type?
>
> Use the [website](https://osstatus.com/) to search the error code.

### Catch is

Alternatively, errors can be caught by type using the `catch is` syntax. As seen before, all errors implement the `Error` protocol. Additionally, all errors extend from `NSError` which provides more information for troubleshooting like a string property called "domain" that classifies the origin of an error. An error's domain may also suggest if the error can be casted into a more specific type like `CocoaError`, `POSIXError`, or `MachError`. See [Apple's documentation on error objects, domains, and codes](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/ErrorHandlingCocoa/ErrorObjectsDomains/ErrorObjectsDomains.html) to learn more.

```swift
if let fileURL = Bundle.main.url(forResource: "swift", withExtension: "png") {
    // create a url that does not exist
    let erroneousURL = fileURL.appendingPathComponent("unknown-path")
    
    do {
        let content = try String(contentsOf: erroneousURL, encoding: .utf8)
        print(content)
    } catch is CocoaError {
        print("this error is a CocoaError")
    } catch {
        print("\(error)")
    }
}
```

### Catch let ... as ...

To catch and cast an error by type, combine `catch let error` with a type cast. In the example below, an error is casted into `CocoaError` which exposes common error codes generated by core Apple frameworks.

```swift
if let fileURL = Bundle.main.url(forResource: "swift", withExtension: "png") {
    // create a url that does not exist
    let erroneousURL = fileURL.appendingPathComponent("unknown-path")
    
    do {
        let content = try String(contentsOf: erroneousURL, encoding: .utf8)
        print(content)
    } catch let error as CocoaError { /* handle if error can be casted into a `CocoaError` */
        switch error.errorCode {
        case CocoaError.fileReadInapplicableStringEncoding.rawValue:
            print("cannot read file into a string")
        case CocoaError.fileReadUnknown.rawValue:
            print("file unknown, cannot read")
        default:
            print("cocoa error with code: \(error.errorCode)")
        }
    } catch {
        print("\(error)")
    }
}
```

## Propogate error

To propagate all possible errors to the calling site, start by removing the `do`-`catch` statement and adding `throws` to the signature of the function which calls the error-prone code.

```swift
// add "trows" to the function
func readFileIntoStringWithThrows(fileName: String, fileExtension: String) throws {
    if let fileURL = Bundle.main.url(forResource: fileName, withExtension: fileExtension) {
        let content = try String(contentsOf: fileURL, encoding: .utf8)
        print(content)
    }
}

// Now, any code that calls `readFileIntoStringWithThrows` is responsible for handling any errors it generates.
do {
    try readFileIntoStringWithThrows(fileName: "swift", fileExtension: "png")
} catch {
    print("the error was propagated to me, and I handled it!")
} 
```

Sometimes it is appropriate to handle some errors immediately while propagating others for further processing. To propagate a specific error, use the `throw` keyword.

```swift
func readFileIntoStringHandleAndThrow(fileName: String, fileExtension: String) throws {
    if let fileURL = Bundle.main.url(forResource: fileName, withExtension: fileExtension) {
        do {
            let content = try String(contentsOf: fileURL, encoding: .utf8)
            print(content)
        } catch CocoaError.fileReadUnknown {
            print("file unknown, cannot read")
        } catch let error {
            throw error /* throw (propagate) this error to the call site */
        }
    }
}

do {
    try readFileIntoStringHandleAndThrow(fileName: "swift", fileExtension: "png")
} catch let error {
    print("handling error: \(error.localizedDescription)")
}
```

## `try?` and `try!`

Try comes in two other forms: `try?` and `try!`. `try?` executes error-prone code, and if any error is generated, then it is converted into an optional where the underlying value has the same type as the error-prone function or intializer's return type. This can simplify code, but the ability to analyze errors is lost.

```swift
func printFileContentsTry() {
    if let fileURL = Bundle.main.url(forResource: "swift", withExtension: "png") {
        let content = try? String(contentsOf: fileURL, encoding: .utf8)
        print(content ?? "content is nil")
        
        // preferably, combine `if let` with `try?`; it's easier to read
        if let content = try? String(contentsOf: fileURL, encoding: .utf8) {
            print(content)
        } else {
            print("could not read contents of file into string")
        }
    }
}
```

`try!` executes error-prone code while foregoing any opportunity to safely handle an error. If an error-prone function is called using `try!` and it fails, then the entire app or playground crashes.

```swift
// try! should only be used if there is no risk of error; generally, this is not advised
if let fileURL = Bundle.main.url(forResource: "swift", withExtension: "txt") {
     let content = try! String(contentsOf: fileURL, encoding: .utf8)
     print(content)
}
```

## Error handle summary

There are four ways to handle the error.

1. Handle Error with `Do`-`Catch`
2. Convert Error to Optional with `try?`
3. Ignore Error with `try!`
4. Propagate Error

## Create custom error

To create a custom error, define a type that inherits from the `Error` protocol. Often, enums are used for this purpose.

```swift
enum SimplePurchaseError: Error {
    case invalidAddress
    case cardRejected
    case cartWeightLimitExceeded
    case insufficientStock
}

func makeBadPurchase() throws {
    throw SimplePurchaseError.cardRejected // throw a custom error
}

do {
    try makeBadPurchase()
} catch {
    print(error) // Result: cardRejected
}
```

When using an enum to define a custom error, use associated values to add helpful debugging information.

```swift
enum ComplexPurchaseError: Error {
    case invalidAddress
    case cardRejected
    case cartWeightLimitExceeded(Double)
    case insufficientStock(String)
}

func attemptPurchase(withWeight weight: Double) throws {
    if weight > 100 {
        // throw a custom error with an associated value
        throw ComplexPurchaseError.cartWeightLimitExceeded(-1 * (100 - weight))
    } else {
        print("purchase succeeded!")
    }
}

do {
    try attemptPurchase(withWeight: 125.6)
} catch let error as ComplexPurchaseError {
    switch error {
    case let .cartWeightLimitExceeded(weight): /* extract the associated value */
        print("purchase failed. weight exceeds limit by: \(weight)")
      	// Result: purchase failed. weight exceeds limit by: 25.599
    default:
        print(error)
        break
    }
} catch {
    print(error)
}
```

## Describe custom error

You can add extra debugging information to a custom error by also implementing the `LocalizedError` and `CustomNSError` protocols.

### `LocalizedError`

`LocalizedError` includes properties which should be used to provide localized information about an error.

```swift
/// Describes an error that provides localized messages describing why
/// an error occurred and provides more information about the error.
public protocol LocalizedError : Error {

    /// A localized message describing what error occurred.
    public var errorDescription: String? { get }

    /// A localized message describing the reason for the failure.
    public var failureReason: String? { get }

    /// A localized message describing how one might recover from the failure.
    public var recoverySuggestion: String? { get }

    /// A localized message providing "help" text if the user requests help.
    public var helpAnchor: String? { get }
}
```

The following example describes the `failureReason` for a `PurchaseError`. For brevity, the other properties have been excluded.

```swift
extension PurchaseError: LocalizedError {   
    var failureReason: String? {
        switch self {
        case .invalidAddress:
            return NSLocalizedString("address contained invalid or empty fields", comment: "")
        case .cardRejected:
            return NSLocalizedString("card number or csv code is invalid", comment: "")
        case let .cartWeightLimitExceeded(amount):
            return NSLocalizedString("weight limit was by exceeded \(amount)", comment: "")
        case .insufficientStock:
            return NSLocalizedString("insufficient stock", comment: "")
        }
    }
}

do {
    try makeBadPurchase() // a fictitious function that might throw an error
} catch let error as LocalizedError { // cast error to `LocalizedError`
    if let description = error.errorDescription { print(description) }
    if let failureReason = error.failureReason { print(failureReason) }
    if let recoverySuggestion = error.recoverySuggestion { print(recoverySuggestion) }
    if let helpAnchor = error.helpAnchor { print(helpAnchor) }
} catch {
    print(error)
}
```

> **Note**: Comment is left blank for each `NSLocalizedString`. Again, this is for brevity. In practice, you should use comments that provide sufficient context about a localized string so that translators can produce more accurate translations. Failure to do so may result in vastly different translations.

### `CustomNSError`

Recall, all errors extend from `NSError`. By default, the properties of `NSError`, like domain, have inferred values. To provide more accurate values for `NSError` properties, implement `CustomNSError`.

```swift
/// Describes an error type that specifically provides a domain, code,
/// and user-info dictionary.
public protocol CustomNSError : Error {

    /// The domain of the error.
    public static var errorDomain: String { get }

    /// The error code within the given domain.
    public var errorCode: Int { get }

    /// The user-info dictionary.
    public var errorUserInfo: [String : Any] { get }
}
```

If an error implements `CustomNSError`, then when casted to an `NSError`, it will contain the values provided by the protocol implementation.

```swift
extension PurchaseError: CustomNSError {
    // domain and error code...

    /// The user-info dictionary.
    var errorUserInfo: [String: Any] {
        switch self {
        case let .cartWeightLimitExceeded(amount):
            return [
                "weightLimit": 100.0,
                "weightExceeded": amount
            ]
        case let .insufficientStock(items):
            return [
                "itemsOutOfStock": items
            ]
        default:
            return [:]
        }
    }
}

do {
    try attemptPurchase(withWeight: 125.6) // a fictitious function that might throw an error
} catch let error as NSError { // cast error to `NSError`
    print(error.domain)
    print(error.code)
    print(error.userInfo) // prints "["weightExceeded": 25.599999999999994, "weightLimit": 100.0]"
} catch {
    print(error)
}
```

## `defer`

The last topic related to errors is the `defer` keyword. While uncommon, you may find it coupled with error handling because it can be used to execute a block of code before an error-generating scope is exited.

Typically, a defer block is provided near the top of a function. It cannot contain any code that changes the flow of control, like a break or return statement, or throwing an error. Also, multiple defer blocks may be defined, but they are executed in reverse order of when they appear.

```swift
func attemptPurchaseWithMultipleDefers() {
    // before this function exits, execute this defer block
    defer { print("then, close the secure purchase session") }
    defer { print("first, clear order") }

    do {
        try processOrder()
    } catch {
        print("\(error)")
    }
}

attemptPurchaseWithMultipleDefers()

// before `attemptPurchaseWithMultipleDefers` exits, it prints…
// "first, clear order"
// "then, close the secure purchase session"
```

# Generics

## Array are Generic

Upon first glance, many do not realize that Swift arrays use generics. Specifically, the type that a Swift array stores is generic — it can be anything. When declaring an array using its more longhand syntax, this becomes apparent.

```swift
var intArray = Array<Int>()
var stringArray: Array<String> = ["one", "two", "three"]
```

The type specified in the brackets (ex. "<String>") is called a "concrete type". When specified, the concrete type takes the place of a generic type. Because Swift arrays use generics, they behave the same, regardless of the concrete type.

## Generic Function

Generics can be applied to functions and types. To write a generic function, specify a generic type after the function name using the bracket notation (ex. `func myFunction<Type>`). Then, for any arguments that should be generic, use the generic type instead of a concrete type. Below, the generic type is called `Type`; `Type` can represent any type.

```swift
func printTypeWithNamedGenericType<Type>(_ argument: Type) {
    print(type(of: argument))
}

printTypeWithNamedGenericType(4) // Int 
printTypeWithNamedGenericType("udacity") // String
```

> Now, the type is for any type. We can't implement some functions for this value.
>
> For example, we can't use "Plus" function, so we should specify a type for it.

A generic type can be bound or constrained such that it can only represent concrete types which adhere to some protocol or inherit from a certain class. In the example below, the generic type is constrained such that it can only represent types which implement the `UnsignedInteger` protocol — essentially, non-negative integers.

```swift
func printUIntTypes<Type: UnsignedInteger>(_ argument: Type) {
    print(type(of: argument))
}

let unsignedInt: UInt = 4
let unsignedInt8: UInt8 = 4
let unsignedInt16: UInt16 = 4

printUIntTypes(unsignedInt)
printUIntTypes(unsignedInt8)
printUIntTypes(unsignedInt16)
// When a generic type is constrained, any concrete types that do not adhere to the constraint will cause Xcode to complain.
//printUIntTypes(4) /* `Int` is not unsigned because it can store negative values */
//printUIntTypes(-4)
//printUIntTypes("abc")
```

## Multiple generic function

Generic functions and types can specify multiple generic types, simply use a comma to separate them.

```swift
func combineUInt<Type1: UnsignedInteger, Type2>(_ int: Type1, withString string: Type2) -> String {
    return "\(int) \(string)"
}

let unsignedInt: UInt = 4
print(combineUInt(unsignedInt, withString: "zebras"))
```

## Generic Type

Generics can be applied to an object's properties to create what is known as a generic type. Similar to generic functions, a generic type is created by specifying the object's name followed by a generic type using the bracket notation (ex. `struct MyType<Type>`). Then, for any properties that should be generic, use the generic type instead of a concrete type.

```swift
struct TypeAnalyzer<T> {
    let value: T
    
    // represents the sub-structure of the generic type
    var mirror: Mirror {
        return Mirror(reflecting: value)
    }
    
    // print information about the type
    func analyze() {
        print("Type: \(type(of: value))")
        print("Value: \(value)")
        if let superClassMirror = mirror.superclassMirror {
            print("Superclass: \(superClassMirror.subjectType)")
        }
    }
}

let x = TypeAnalyzer(value: 2)
x.analyze()
// Type: Int
// Value: 2

let view = TypeAnalyzer<UIView>(value: UIView(frame: CGRect.zero))
view.analyze()
// Type: UIView
// Value: <UIView: 0x7f82c0a0dfc0; frame = (0 0; 0 0); layer = <CALayer: 0x6000035e6140>>
// Superclass: UIResponder
```

Like generic functions, the generic type can be named and constained. In the example below, `ZooExhibit` is defined where its animals property is generic and constrained to any type that implements the `Animal` protocol.

```swift
protocol Animal {
    var name: String { get }
    static var commonName: String { get }
    static var emoji: String { get }
}

struct Whale: Animal {
    let name: String
    static let commonName = "Whale"
    static let emoji = "🐳"
}

struct Dolphin: Animal {
    let name: String
    static let commonName = "Dolphin"
    static let emoji = "🐬"
}

struct ZooExhibit<AnimalType: Animal> {
    let animals: [AnimalType]

    func tourTheExhibit() {
        print("Welcome to the \(AnimalType.commonName) Exhibit \(AnimalType.emoji)!")
        for animal in animals {
            print("Say hello to \(animal.name) \(AnimalType.emoji).")
        }
    }
}

let exhibit1 = ZooExhibit(animals: [Whale(name: "Wendy"), Whale(name: "Wu")])
exhibit1.tourTheExhibit()
// Welcome to the Whale Exhibit 🐳!
// Say hello to Wendy 🐳.
// Say hello to Wu 🐳.

// the longhand syntax can be used to specify the concrete type
let exhibit2 = ZooExhibit<Dolphin>(animals: [Dolphin(name: "Dilbert"), Dolphin(name: "Dezeri")])
exhibit2.tourTheExhibit()
// Welcome to the Dolphin Exhibit 🐬!
// Say hello to Dilbert 🐬.
// Say hello to Dezeri 🐬.
```

A single generic type can only be substituted with one concrete type. If Xcode is unable to determine the concrete type that should be substituted for a generic type, it will complain.

```swift
// let exhibit3 = ZooExhibit(animals: [Whale(name: "Wilber"), Dolphin(name: "Daphnie")])
```

Extensions can be combined with generics for truly powerful effects. With an extension, it is possible to specify functionality that should only apply to a generic type when the concrete type meets inherits from a specific protocol.

```swift
protocol Feedable {
    static var favoriteFood: String { get }
}

extension Dolphin: Feedable {
    static let favoriteFood = "🐟"
}

extension ZooExhibit where AnimalType: Feedable {
    func feedTheAnimals() {
        for animal in animals {
            print("You feed \(animal.name) \(AnimalType.emoji) some \(AnimalType.favoriteFood).")
        }
    }
}

exhibit2.feedTheAnimals()

// because `Whale` is not `Feedable`, the `feedTheAnimals()` function doesn't exist for the whale exhibit
//exhibit1.feedTheAnimals()
```

## Subclass a Generic Type

A generic type can be subclassed, assuming it is a class and not a struct. To demonstrate an example of subclassing a generic type, the `Animal`, `Whale`, and `ZooExhibit` types are defined:

```swift
protocol Animal {
    var name: String { get }
    static var commonName: String { get }
}

struct Whale: Animal {
    let name: String
    static let commonName = "Whale"
}

class ZooExhibit<AnimalType: Animal> {
    let animals: [AnimalType]

    init(animals: [AnimalType]) {
        self.animals = animals
    }

    func tourTheExhibit() {
        print("Welcome to the \(AnimalType.commonName) Exhibit!")
        for animal in animals {
            print("Say hello to \(animal.name).")
        }
    }
}
```

To subclass the generic type `ZooExhibit`, one must define a new class with a generic type that can be substituted for `AnimalType` (any type that implements the `Animal` protocol). In the subclass below, the generic type `A` is constrained such that it must implement the `Animal` protocol. Hence, when `ZooExhibit` is specified as the superclass, the type `A` can be used without error.

```swift
class TravelingExhibit<A: Animal>: ZooExhibit<A> {
    var location: String

    init(location: String, animals: [A]) {
        self.location = location
        super.init(animals: animals)
    }

    override func tourTheExhibit() {
        print("Welcome to the \(A.commonName) Exhibit at \(location)!")
        for animal in animals {
            print("Say hello to \(animal.name).")
        }
    }
}
```

But, if a subclass tries to specify a generic type that does not satisfy the constraints from the parent class, then Xcode will generate an error.

```swift
// this generates an error because `B` does not conform to `Animal` protocol
class TankExhibit<B>: ZooExhibit<B> { 
    let volume: Double

    init(volume: Double, animals: [B]) {
        self.volume = volume
        super.init(animals: animals)
    }
}
```

Once a generic type is subclassed, it can be created and used in a manner similar to the base class.

```swift
// exhibit is of type `TravelingExhibit<Whale>`
let exhibit = TravelingExhibit(location: "Oakland Zoo", animals: [Whale(name: "Watson"), Whale(name: "Wren")])
exhibit.tourTheExhibit()

// change exhibit location
exhibit.location = "San Francisco Zoo"
exhibit.tourTheExhibit()
```

# Closure

## Create a closure

```swift
let closures = [f,
               { (x:Int) -> Int in return x * 2 },
               { x in return x - 8 },
               { x in x * x },
               { $0 * 42 }]

for fn in closures {
  fn(42) // 5 results
}
```

## Closure and Function

```swift
let f = {(x: Int) -> Int
        in
        return x + 42}

f(9) // 51
f("99") // error

// this function is the same as the closure
func foo(x: Int) -> Int {
  return x + 42
}
```

## alias

```swift
typealias Integer = Int

let z: Integer = 42
let zz: Int = 42

// (Int) -> Int
typealias IntToInt = (Int) -> Int
```

```swift
typealias IntMaker = (Void) -> Int

// the function returns another function/closure
func makeCounter() -> IntMaker {
  var n = 0
  
  func adder() -> Int {
    n = n + 1
    return n
  }
  
  return adder
}

let counter1 = makeCounter() // counter 1 is equal to the adder function
let counter2 = makeCounter()

counter1() // 1
counter1() // 2
counter1() // 3

// the "n" is not the same for difference instance
counter2() // 1
```

