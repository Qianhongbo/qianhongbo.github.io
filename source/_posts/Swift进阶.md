---
title: Swift进阶
date: 2021-12-11 20:09:37
categories: 
- 学习笔记
tags: 
- Swift
---

# Optionals

## Nil value

The Nil value is the same as null in some other languages.

```Swift
var z: Int?
var s: String
s = "123"
z = Int(s)

var zee: Int?

var string: String
let randomNumber = Int(arc4random() % 2)
if randomNumber == 1 {
    string = "123"
} else { // always 0
    string = "ABC"
}

zee = Int(string)

if let intValue = zee {
  intValue * 2
} else {
  "No value"
}
```

## Another Example With Nested Structures

Safely unwrapping optionals is a fundamental Swift skill and one can’t have too much practice. Let’s walk through another example using the following enum and structures.

```swift
enum Genre: String {
    case country, blues, folk
}

struct Artist {
    let name: String
    var primaryGenre: Genre?
}

struct Song {
    let title: String
    let released: Int
    let artist: Artist?
}
```

The `Song` struct contains a property `artist` of optional type `Artist?`. Then, the `Artist` struct as a `primaryGenre` property of optional type `Genre?`. `Genre` is a simple enumeration with only three cases for simplicity.

Now imagine we have the following instances of the `Artist` and `Song` structs. Here, `johnny` is assigned to the `Artist` property of `walkTheLine`.

```swift
var johnny = Artist(name: "Johnny Cash", primaryGenre: Genre.country)
var walkTheLine = Song(title: "I Walk the Line", released: 1956, artist: johnny)
```

If we only had access to the the `Song` instance `walkTheLine` and we wanted to determine the `primaryGenre` of the song's artist, we'd need some `if let`s.

First, we'd need to get the artist.

```swift
if let artist = walkTheLine.artist {
  print(artist.name)
} else {
    print("Artist unknown")
}
```

And within the first `if let`, we'd use another to get the `primaryGenre`.

```swift
if let artist = walkTheLine.artist {
    if let genre = artist.primaryGenre {
      // If there is no value for enum, it wll be "country"
        print("Primary genre: \(genre.rawValue)") 
    } else {
        print("Primary genre unknown")
    }
} else {
    print("Artist unknown")
}
```

If the `genre` property of `artist` is not `nil`, we know it has a value and can safely print out the genre.

## Example: Animals With and Without Tails

Here's another example: let’s say we have a struct called Animal. The Animal struct has a tail property and the tail property is an optional — not all animals have tails.

```swift
struct Tail {
    var length: String

    init(length: Int) {
        self.length = "\(length)"
    }
}

struct Animal {
    var name:String
    var species: String = "homo sapiens"
    var tail: Tail?

    init(name: String, species: String, tailLength:Int?) {
        self.name = name
        self.species = species
        if let tailLength = tailLength {
            self.tail = Tail(length: tailLength)
        } else {
            self.tail = nil
        }
    }
}
```

```swift
var animal = Animal(name: "Lenny", species: "lemur", tailLength: 12)

if let tailLength = animal.tail?.length{
  print("\(animal.name)'s tail is \(tailLength) cm long")
} else {
  print("\(animal.name) doesn't have a tail.")
}
// "Lenny's tail is 12 cm long\n"
```

```swift
var animal = Animal(name: "Gilbert", species: "Gorilla", tailLength: nil)

if let tailLength = animal.tail?.length{
  print("\(animal.name)'s tail is \(tailLength) cm long")
} else {
  print("\(animal.name) doesn't have a tail.")
}
// "Gilbert doesn't have a tail.\n"
```

## Nil Coalescing Operator

We already used optional chaining to avoid nesting `if let`s, but what if all we want to do is provide `tailLength` with a default value if the optional chaining fails? Swift provides a useful operator, for situations like these, the **nil coalescing operator**, `??`.

```
<optional expression> ?? <default case>
```

To use the operator, simply place `??` after the optional chaining expression, and then put a default value after the `??`.

```swift
let tailLength = animal.tail?.length ?? "0"
print("tail length: \(tailLength)")
// tail length: 0
```

## Your Turn

Assume the following structs and enum are defined.

```swift
enum Genre: String {
    case country, blues, folk
}

struct Artist {
    let name: String
    var primaryGenre: Genre?
}

struct Song {
    let title: String
    let released: Int
    var artist: Artist?
}
```

Using the enums and structures, implement the `getArtistGenre()` function. Given a `Song` as input, return the raw value of the artist's primary genre. If either the `artist` is `nil` or the `primaryGenre` is `nil`, then return an empty string. You must use optional chaining and the nil coalescing operator (`??`).

```swift
enum Genre: String {
    case country, blues, folk
}

struct Artist {
    let name: String
    var primaryGenre: Genre?
}

struct Song {
    let title: String
    let released: Int
    var artist: Artist?
}

func getArtistGenre(song: Song) -> String {
    if let songGenre = song.artist?.primaryGenre {
        return songGenre.rawValue
    } else {
        let songGenre = song.artist?.primaryGenre?.rawValue ?? ""
        return songGenre
    }
}

var artisTest = Artist(name: "name", primaryGenre: nil)

var test = Song(title: "title", released: 2021, artist: artisTest)

print(getArtistGenre(song: test))

```

## Example: Revisiting the `Int()` Initializer Method

Let’s say we’re using the `Int()` initializer method and invoking it on a variable that we are confident can be converted to an Int.

```swift
let w = Int("123")
```

We know this method is going to return the Int, 123, so in this case, unwrapping with the exclamation point is appropriate. We know there is little risk of a fatal error being thrown. We can place the exclamation point after w, like this:

```swift
w! * 2
```

Or we could unwrap the return value directly, like this:

```swift
let w = Int("123")!
w * 2
```

In this second case the value assigned to w will be a plain old Int.

## Implicitly Unwrapped Optionals: Revisiting the Villain struct

What about declaring implicitly unwrapped optionals? When is an appropriate time to be so bold? Well, remember the `evilScheme` property of the `Villain` struct? We wanted the ability to create instances of `Villain` with the option of not providing an `evilScheme` at first. However, once we know that the `evilScheme` has been set, we can access the property as if it were a regular non-optional type.

```swift
struct Villain {
    let name: String
    // exclamation point means implicitly unwrapped
    var evilScheme: String!

    func performScheme() {
        // no unwrapping necessary
        print("And now, I will \(evilScheme!)")
    }
}

var villain = Villain(name: "Billy", evilScheme: nil)
villain.evilScheme = "steal from the cookie jar"
// we know for sure the scheme has been set
villain.performScheme()
```

There are a few things to note in the example above.

- The `evilScheme` property is now declared as an **implicitly unwrapped optional**. It's type is written as `String!` instead of `String?`.
- In the `performScheme()` method, we reference `evilScheme` as any other property, without unwrapping.
- We call `performScheme()` once we know for a fact that `evilScheme` was set. The function assumes that `evilScheme` is not `nil`. If we treat an implicitly unwrapped optional that contains nil as if it contained a value, bad things can happen - including crashes!

## Difference between upwrapping an with `?`, with `!` and with implicit unwrapping?

Unwrapping with `?` is useful if you're accessing nested properties or want some default behavior to occur if a value is nil.

Using `!` does not require providing a default value and is useful if you know that a value won't be `nil`. However, if it is `nil`, the program will crash.

Implicitly unwrapping optionals is also useful when you know a value won't be `nil`, but you can use the same syntax as a normal property. However, unintended behavior and crashes can occur if it is `nil`.

# Strings

## A String is a Struct

You just saw how a string can be initialized without a string literal.

```swift
let stringWithPotential = String()
```

## Counting Characters

Let's start with something straightforward. If you want to know how many characters are in a string, you use the count property.

```swift
var str = "Meet me in St. Louis!"
"The string is \(str.count) characters long"
```

Many real apps rely on counting characters - for example, a Twitter client that ensures a user is restricted to typing 140 or fewer characters.

## Accessing specific characters

We can also conveniently access the characters at the beginning and end of a string.

```swift
let firstLetter = str.first
let isExcited = str.last == "!"
```

## Checking for Substrings

A **substring** is the term programmers use for any string that occurs within another string. We can check for the presence of a substring in different cases as shown below.

```swift
str.contains("Lou")
str.hasPrefix("Me")
str.hasSuffix("in")
```

`contains()` simply checks if the substring occurs anywhere in `str`. `hasPrefix()` and `hasSuffix()` are more specialized and check if the substring occurs at the beginning and end of the string respectively.

## Creating Variations of Strings

We can also use some string methods to generate different strings.

```swift
str.lowercased()
str.uppercased()
String(str.reversed())
```

Converting strings to all-lowercase or all-uppercase is great when you want user input to be case-insensitive. And reversing a string is useful if you want to see if two strings are [palindromes](https://en.wikipedia.org/wiki/Palindrome).

Note in the last example, we need to use `str.reversed()` with a `String` initializer. If you look closely at the [documentation for reversed()](https://developer.apple.com/documentation/swift/string/2893808-reversed), you'll see that it actually returns a `ReversedCollection` instead of a `String`. You won't need to use this type directly, but just know that Swift uses it "under the hood" for efficiency. You can simply use the `String()` initializer to convert the result into a usable string.

## Appending Two Strings

Similar to concatenation, `String` also has an `append()` method that takes a string as an argument and attaches it to the string you're mutating.

```swift
var hello = "hello "
let world = "world!"
hello.append(world)
```

Is the equivalent of

```swift
hello = hello + world
```

or

```swift
hello += world
```

With `append()` you don't need to reassign `hello` a new value, and you'll find the code is much more readable.

## Trimming characters

In addition to `append()`, we can also remove characters from the end of a string. For example, you may want to remove all whitespace characters from the end of a user's input.

```swift
var message = "   Be back later!        "

while message.last == " " {
    message.removeLast()
}
```

And the same can be done at the beginning.

```swift
while message.first == " " {
    message.removeFirst()
}
```

## Replacing Substrings

We can also modify the middle of strings. A common manipulation method is `replacingOccurrences(of: String, with: String)`, which allows us to swap out one substring with another.

```swift
let verbose = "We hope you are very excited to learn Swift. It's a very fun and very useful skill. Soon enough, you'll be writing your very own app!"
let better = verbose.replacingOccurrences(of: "very ", with: "")
```

This method might come in handy for implementing a Find and Replace feature, a profanity filter, and many other cases, and is great to have in your string manipulation bag of tricks.

**Note:** To use the `replaceOccurrences()` method, you will need to add `import Foundation` to the top of your file. `Foundation` is a **framework**, which is just a name for a collection of commonly used code. Apple provides frameworks like Foundation so that developers, like you, don't need to reinvent the wheel for common tasks like string manipulation. We've already imported Foundation in the playground, so you can just follow along with the examples.

# Array

## Initialize

 ```swift
 //: ## Initializing Arrays
 //: The verbose way
 var numbers = Array<Double>()
 //: More often you will see ...
 var moreNumbers = [Double]()
 moreNumbers = [85.0, 90.0, 95.0]
 //: Array literal syntax
 let differentNumbers = [97.5, 98.5, 99.0]
 
 //: Array concatenation is super convenient in Swift
 moreNumbers += differentNumbers
 
 //: ## Swift Arrays have types
 //: An array can hold any type of object
 struct LightSwitch {
     var on: Bool = true
 }
 
 var circuit = [LightSwitch]()
 
 var livingRoomSwitch = LightSwitch()
 var kitchenSwitch = LightSwitch()
 var bathroomSwitch = LightSwitch()
 
 circuit = [livingRoomSwitch, kitchenSwitch, bathroomSwitch]
 ```

**Append**

Here is an array of type `String`, listing musicians we want to play in the car on our upcoming road trip. To add an item to the end of an array we can use the append method, like this:

```swift
var musicians = ["Neil Young", "Kendrick Lamar", "Flo Rida", "Fetty Wap"]
musicians.append("Rae Sremmurd")
```

The array now looks like this:

```swift
["Neil Young", "Kendrick Lamar", "Flo Rida", "Fetty Wap", "Rae Sremmurd"]
```

**Insert**

To add an item to a specific place in an array we can use the method, *insert(at:)*. So, to add in “Dej Loaf” at index 2 we would write the following:

```swift
musicians.insert("Dej Loaf", at: 2)
```

The array now looks like this:

```swift
["Neil Young", "Kendrick Lamar", "Dej Loaf", "Flo Rida", "Fetty Wap", "Rae Sremmurd"]
```

**Remove**

We can use a method called `remove(at:)` to remove an item at a particular index. If we remove the item at index 3, you can see that “Flo Rida” is removed:

```swift
musicians.remove(at: 3)
```

**Subscripting**

Finally, if you want to retrieve an item at a particular index, you can use subscript syntax. Simply put the index in-between brackets after the array name, like so:

```swift
let musician = musician[2]
```

# Dictionary

## Initialize

```swift
// Initializer syntax
var groupDict = [String:String]()

// Dictionary literal
var animalGroupDict = ["whales":"pod", "geese":"flock", "lions": "pride"]
```

  ```swift
  //: ## Dictionary operations
  //: insert, count, remove, update, retrieve
  var animalGroupsDict = ["whales":"pod", "geese":"flock", "lions": "pride"]
  //: Adding items to a dictionary
  animalGroupsDict["crows"] = "murder"
  animalGroupsDict["monkeys"] = "troop"
  //: The count method is available to all collections.
  animalGroupsDict.count // 5
  print(animalGroupsDict)
  //: Removing items from a dictionary
  animalGroupsDict["crows"] = nil
  animalGroupsDict
  //: Updating a value
  animalGroupsDict["monkeys"] = "barrel"
  var group = animalGroupsDict.updateValue("gaggle", forKey: "geese")
  type(of: group)
  
  animalGroupsDict.updateValue("crash", forKey: "rhinoceroses")
  print(animalGroupsDict)
  //: ## Example
  //: Retrieving the value for a particular key
  let groupOfWhales = animalGroupsDict["whales"]
  //: Why would the code below return an optional?
  //:
  //:     animalGroupsDict["whales"]
  //: We unwrap a value returned from a dictionary just like we would unwrap any other optional.
  if let groupOfWhales = animalGroupsDict["whales"] {
      print("We saw a \(groupOfWhales) of whales from the boat.")
  } else {
      print("No value found for that key.")
  }
  //: What happens if the key isn't found?
  if let groupOfSasquatches = animalGroupsDict["Sasquatches"] {
      print("We saw a \(groupOfSasquatches) of Sasquatches on our hike.")
  } else {
      print("No value found for that key.")
  }
  ```

Here we use the `removeValueForKey()` method to remove the value for the string, “George Bush”. Since `removeValueForKey()` returns the value removed, we can reset the value of the new key, “George H. W. Bush” to be the previously returned value, “Miss Beazley.”

```swift
var oldValue = presidentialPetsDict.removeValueForKey("George Bush")
presidentialPetsDict["George W. Bush"] = oldValue
```

We unwrap the retrieved value using `if let` and then use string interpolation to insert “Bo” into the string. The string is printed out as a part of the if block.

```swift
if let dog = presidentialDogs["Barack Obama"] {
    print("Michele Obama walks \(dog) evey morning.")
} else {
    print("No value found.")
}
```

# Sets

```swift
//: ## Sets
//: Literal syntax
var cutlery: Set = ["fork", "knife", "spoon"]
var flowers:Set<Character> = ["🌷","🌹","🌸"]
//: Initializer syntax
var utensils = Set<String>()
var trees = Set<Character>()
//: ## Set operations
//: Insert, Remove, Count
trees.insert("🌲")
trees.insert("🌳")
trees.insert("🌵")

trees.remove("🌵")
trees.count
```

# Class

```swift
class Movie {
    let title: String
    let director: String
    let releaseYear: Int
    
    init(title: String, director: String, releaseYear: Int) {
        self.title = title
        self.director = director
        self.releaseYear = releaseYear
    }
}

class MovieArchive {
    var movies: [Movie]
    
    func filterByYear(year: Int) -> [Movie] {
        var filteredArray = [Movie]()
        for movie in self.movies {
            if movie.releaseYear == year {
                filteredArray.append(movie)
            }
        }
        return filteredArray
    }
    
    init(movies: [Movie]) {
        self.movies = movies
    }
}

let movie1 = Movie(title: "Bride of Frankenstein", director: "James Whale", releaseYear: 1935)
let movie2 = Movie(title: "The Night Walker", director: "William Castle", releaseYear: 1964)

let archive = MovieArchive(movies: [movie1, movie2])
archive.filterByYear(year: 1935)
archive.movies
```

One important difference between structs and classes in Swift is how they're handled in the computer or phone's memory. Structs are **passed by value** while classes are **passed by reference**. Let's see what this means with a simple example.

```swift
class Musician {
    var instrument: Instrument
    
    init(instrument: Instrument) {
        self.instrument = instrument
    }
}
let duo = [Musician(instrument: .fiddle), Musician(instrument: .banjo)]

//: Alternately, we could define a struct
struct MusicianStruct {
    var instrument: Instrument
}
let structDuo = [MusicianStruct(instrument: .fiddle), MusicianStruct(instrument: .banjo)]

func prepareForDuelingBanjos(musician: Musician) {
    //var musicianCopy = musician
    //musicianCopy.instrument = .banjo
    musician.instrument = .banjo
}

func prepareForDuelingBanjos(musician: MusicianStruct) {
    var musicianCopy = musician
    musicianCopy.instrument = .banjo
}

//: instances of classes are passed by reference
let fiddler = duo[0]
prepareForDuelingBanjos(musician: fiddler)
fiddler.instrument // banjo
//: instances of structs are passed by value
let fiddlerStruct = structDuo[0]
prepareForDuelingBanjos(musician: fiddlerStruct)
fiddlerStruct.instrument // fiddle
```



## Class Inheritance

One powerful feature of classes not available to structs is inheritance. Inheritance allows one class to take on the properties and methods of another class, as well as add its own functionality.

To start, let's say we have a `Guitar` class defined as the following.

```swift
class Guitar {
    let strings: Int
    let frets: Int

    init(strings: Int, frets: Int) {
        self.strings = strings
        self.frets = frets
    }

    func pluckString() {
        print("twang")
    }
}
```

This is a great start, but what if we then wanted to model an electric guitar. The electric guitar is a "kind of" guitar, but with a few differences. For example, the electric guitar has electronics for modifying the volume and tone of its output (check out the knobs on the lower body). We could define a new `ElectricGuitar` class by copy-pasting the `GuitarClass` and adding these different properties, but we can do much better. Instead, if we place a colon after the the class name and then specify the class we want to inherit from, the `ElectricGuitar` automatically has all the properties and methods of a guitar.

```swift
class ElectricGuitar: Guitar {
    var volumeLevel: Float = 1.0
    var toneLevel: Float = 1.0
}
```

Now we have a new class, `ElectricGuitar` that has all the properties of `Guitar` with the additional properties for volume and tone.

|              |                                                              |
| :----------- | :----------------------------------------------------------- |
| superclass   | A class that is inherited from                               |
| subclass     | A class that inherits from another class (the superclass)    |
| parent class | A class's superclass                                         |
| child class  | A class that descended (inherited directly) from a parent class. |
| descendant   | A class that inherited from an ancestor                      |
| ancestor     | A class that has child or descendent classes                 |
| root class   | A class with no superclass                                   |
| leaf class   | A class with no subclasses                                   |
| hierarchy    | A tree of inheritance relationships                          |

## Overriding Methods

Inheritance doesn't just let you add to a class - you can also override its behavior. For example, since an electric guitar's volume affects its sound, we can provide some custom functionality to the `pluck()` method.

```swift
override func pluckString() {
    if volumeLevel > 0.7 {
        print("🎸🎸🎸 DRAOWWW")
    } else if volumeLevel > 0 {
        print("🎸 twang")
    } else { // volumeLevel is 0
        // Guitar's implementation of pluckString()
        super.pluckString()
    }
}
```

Overriding a method is as simple as redeclaring it and adding the `override` keyword before `func`. And if you ever need to fall back to the original functionality, as we do in the case when the volume is 0, the old implementation can be called `super` keyword. `super` just refers to the superclass (the `Guitar` class we're inheriting from) so `super.pluck()` will call the `pluck() method of the`Guitar` class which will print "twang".

## Polymorphism

Inheritance, or the ability of one class to take on the properties and methods of a superclass, leads us to the next topic - **polymorphism**.

Polymorphism means “many forms.” It is a programming language feature by which a type can be substituted for another type. For example, since it inherits from a `Guitar`, an `ElectricGuitar` can be substituted anywhere that a `Guitar` would be used. In other words, an `ElectricGuitar` is a form of `Guitar`.

Polymorphism allows for simpler code that avoids unnecessary specificity, and code that is flexible and expandable because it's applicable to more than one type. If we were to add additional subclasses of `Guitar`, we could still use any of these types where a `Guitar` is used.

`override` is used to indicate that a subclass's method implementation will be substituted for a superclass's method implementation. This is an aspect of polymorphism through inheritance.

We saw an example of polymorphism previously when we used `override` to refer to a single `Guitar` type, and produced different behavior by substituting in an `ElectricGuitar` instead of a `Guitar`.

We can see an example of polymorphism in action below. Because `anyGuitar`'s type is `Guitar` we can assign any subclass of `Guitar`, including an `ElectricGuitar` to that variable.

```swift
var anyGuitar: Guitar = Guitar(strings: 6, frets: 20)
anyGuitar.pluckString()

anyGuitar = ElectricGuitar(strings: 6, frets: 20)
anyGuitar.pluckString()
```

Even though `anyGuitar()`'s type is still `Guitar`, calling `pluckString()` will call the custom method we defined for `ElectricGuitar`. As a result, we can keep our code involving `Guitar` instances reusable, without sacrificing the functionality of any subclasses that happen to override a method.

## Type Casting

Finally, if we know that `anyGuitar` is assigned an `ElectricGuitar` and we want to access the `toneLevel` property, we can convert the types with a special keyword: `as`. When using the `as` keyword the variable you want to convert goes on the left and the type you're converting to goes on the right.

```
result = variableToConvert as NewType
// forcibly convert the types with "as!"
let electricGuitar = anyGuitar as! ElectricGuitar
electricGuitar.toneLevel
// or safely convert with "if let" with "as?"
if let electricGuitar = anyGuitar as? ElectricGuitar {
    electricGuitar.toneLevel
}
```

You'll also notice the use of `as!` and `as?` in this example. This may seem complicated but it's really the same concept you learned about unwrapping optionals. Since there's no guarantee that `anyGuitar` will actually be an `ElectricGuitar`, casting to an `ElectricGuitar` returns an optional of type `Guitar?`. To forcibly unwrap the result, we use `as!` and to conditionally unwrap with `if let`, we use `as?`.

## Defining Terms

There's some terminology to cover, and then we'll describe what happens when a method is called on a type that is defined as a `class`.

| Term     | Example                         | Definition                                                   |
| :------- | :------------------------------ | :----------------------------------------------------------- |
| instance | Guitar()                        | A class or type that we have initialized. Can be referred to with a constant or variable, and refers to its own instance with `self` |
| override | override func pluckString() { } | Used to indicate that this method implementation should supersede the superclass's method implementation |
| super    | super.someMethod()              | Used to retrieve and invoke a method implementation that has been overridden |

# Protocols

```swift
// Init
protocol Souschef {
    func chop(vegetable: String) -> String
    func rinse(vegetable: String) -> String
}

class Roommate: Souschef, Equatable {
    var hungry = true
    var name: String
    
    init(hungry: Bool, name: String) {
        self.hungry = hungry
        self.name = name
    }
    
    func chop(vegetable: String) -> String {
        return "She's choppin' \(vegetable)!"
    }
    
    func rinse(vegetable: String) -> String {
        return "The \(vegetable) is so fresh and so clean"
    }
}

// implete the function outside the class
func ==(lhs: Roommate, rhs: Roommate) -> Bool {
    return lhs.name == rhs.name && lhs.hungry == rhs.hungry
}

var roomie = Roommate(hungry: true, name: "Jennifer")
var theBestRoomie = Roommate(hungry: true, name: "Jennifer")

roomie == theBestRoomie
//: ## A protocol is also a type, any class that adopts this protocol
class DinnerCrew {
    var members: [Souschef]
    
    init(members: [Souschef]) {
        self.members = members
    }
}

class RandomPasserby: Souschef {
    var name: String
    
    init(name: String) {
        self.name = name
    }
    
    func chop(vegetable: String) -> String {
        return "She's choppin' \(vegetable)!"
    }
    
    func rinse(vegetable: String) -> String {
        return "The \(vegetable) is so fresh and so clean"
    }
}

var newFriend = RandomPasserby(name: "Dave")
var motleyDinnerCrew = DinnerCrew(members: [newFriend, roomie])
```

## Extensions

One more powerful feature of Swift types is the ability to add new methods and properties through **extensions**. You'll see extensions in action as you start building the Pitch Perfect app, so let's take a look at an example before wrapping up with object oriented programming.

To extend a class, simply use the `extension` keyword, followed by the type name.

```swift
extension SomeClass { ... }
```

Additionally, you can also add a colon after the type name followed by any number of protocols you'd like the type to conform to. Let's rewrite the `Roommate` example so that it uses an extension to conform to `Souschef`.

```swift
extension Roommate: Souschef {
    func chop(vegetable: String) -> String {
        return "She's choppin' \(vegetable)!"
    }

    func rinse(vegetable: String) -> String {
        return "The \(vegetable) is so fresh and so clean"
    }
}
```

Isn't this the same as the previous example? It is, but with extensions, you don't even need access to the original class definition for `Roommate`. As you begin your journey into iOS development, you'll be working with many classes provided by Apple in which you do not have direct access to the code. However, with extensions you can add functionality to these types without ever modifying the original class definition.

# Review

Something that is passed by value such as an instance of a struct is copied when it's assigned to a variable or passed to a function. Changing a property on the instance will only apply to the copy, and the original instance will remain unchanged.

Values that are passed by reference, such as instances of classes, are not copied. Changing a value on a copy will also modify the original instance, since the copy is simply referencing the original.
