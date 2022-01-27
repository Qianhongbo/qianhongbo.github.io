---
title: GCD
date: 2022-01-22 21:51:58
categories: 
- iOS
---

## What is GCD

GCD - ==Grand Central Dispatch==

UIKit can only be used on main queue.

Core data can be used both on main queue and background queue. But the core data can only be used in the same queue it was created.

Or we will meet ==concurrency bugs==. 并发错误

## async and sync

- Dispatch_async(): doesn't wait for the closure to finish, and moves on.
- Dispatch_sync(): waits until the closure is done. Could easily get stalled.

## UserDefaults

**`UserDefaults`**: a Dictionary that periodically saves its contents to your device’s permanent storage (SSD).

- Great for storing user preferences and other simple things.
- Saves data in a plist file (property list file).
- Can only store the following data types: `Data`, `String`, `Number`, `Date`, `Array`, and `Dictionary`.
- When storing (writing) the file or accessing (reading) the file, `UserDefaults` does it all at once, possibly creating long/unnecessary I/O time.
- It’s a good idea to keep `UserDefaults` under 1MB.

## Optional

The difference between Swift optionals and Core Data optional attributes is subtle. Swift optionals can be nil during runtime, and Core Data optional attributes can be nil at save time.