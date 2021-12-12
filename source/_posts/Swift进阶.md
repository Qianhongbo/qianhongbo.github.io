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
