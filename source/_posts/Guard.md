---
title: Guard
date: 2021-12-29 08:52:53
categories: 
- iOS
---

# Guard statement

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

# Guard versus If

The `guard` and `if` keywords can serve similar purposes in Swift, but they should not be used interchangeably. Instead, use the following guidelines to decide when to use guard versus if:

- `guard` should be used to check for preconditions and early exit
- `if` should be used to change execution path based on some condition(s)

> Guard: whether the credit card is validand whether the order is scheduled
>
> If: wether the user enter a discount code

# Guard with Optionals

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

