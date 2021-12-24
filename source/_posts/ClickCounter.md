---
title: ClickCounter
date: 2021-12-21 21:44:04
categories: 
- iOS
---

## Use code to generate UI components

```swift
let label = UILabel()
// Use "CGRect" to set the position of the label.
label.frame = CGRect(x: 150, y: 150, width: 60, height: 60) // position
label.text = "0"
// show the label in the view
view.addSubview(label)
```

## Use code to connect label with view

```swift
var label: UILabel! // have to add "!", because it is nil before the compiling.
self.label = label
```

## Combine the click action with the incrementCount function

```swift
// We use this function
button.addTarget(self, action: #selector(ViewController.incrementCount), for: UIControl.Event.touchUpInside)

// have to add @obj
@objc func incrementCount() {
  self.count += 1
  self.label.text = "\(self.count)"
  self.label2.text = self.label.text
  changeBackgroundColr() // change the background color randomly
}
```

## Generate random color

```swift
func changeBackgroundColr() {
  let R = CGFloat(arc4random()%256)/255.0
  let G = CGFloat(arc4random()%256)/255.0
  let B = CGFloat(arc4random()%256)/255.0
  self.view.backgroundColor = UIColor(red: R, green: G, blue: B, alpha: 1)
}
```

