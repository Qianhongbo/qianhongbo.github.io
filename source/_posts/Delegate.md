---
title: Delegate
date: 2021-12-20 17:37:39
categories: 
- iOS
---

## Defination

The delegate is an object(Control Object) that executes a group of methods on behalf of another object.

> Control object is designed to pass user input to a data model.

We want view classes to be used as is, and then we want control and model classes to have the freedom to customize those views. The delegate pattern allows for this.

The key to the delegate pattern, is that the view establishes the questions that it needs answered and encodes them in a protocol.

> A protocol is a list of methods that a delegate must implement.
>
> Any object that fulfills the protocol can become a delegate.

## `UITextFieldDelegate` protocol

Let's take a closer look at the protocol we'll implement next, the `UITextFieldDelegate` protocol. We've listed its methods below. For each of the seven methods, consult the documentation and provide an example implementation. Post one or two of your examples to the discussion forum.

*Editing lifecycle methods:*
[`textFieldShouldBeginEditing(_:)`](https://developer.apple.com/reference/uikit/uitextfielddelegate/1619601-textfieldshouldbeginediting)
[`textFieldDidBeginEditing(_:)`](https://developer.apple.com/reference/uikit/uitextfielddelegate/1619590-textfielddidbeginediting)
[`textFieldShouldEndEditing(_:)`](https://developer.apple.com/reference/uikit/uitextfielddelegate/1619592-textfieldshouldendediting)
[`textFieldDidEndEditing(_:)`](https://developer.apple.com/reference/uikit/uitextfielddelegate/1619591-textfielddidendediting)

> [`textFieldShouldBeginEditing(_:)`](https://developer.apple.com/reference/uikit/uitextfielddelegate/1619601-textfieldshouldbeginediting)
>
> You could use this method to prevent the user from editing the text field’s contents more than once.

> [`textFieldDidBeginEditing(_:)`](https://developer.apple.com/reference/uikit/uitextfielddelegate/1619590-textfielddidbeginediting)
>
> You might use this method to show overlay views that are visible only while editing.

> [`textFieldShouldEndEditing(_:)`](https://developer.apple.com/reference/uikit/uitextfielddelegate/1619592-textfieldshouldendediting)
>
> The text field calls this method when it is asked to resign the first responder status. This can happen when the user selects another control or when you call the text field’s [`resignFirstResponder()`](https://developer.apple.com/documentation/uikit/uiresponder/1621097-resignfirstresponder) method. Before the focus change occurs, however, the text field calls this method and gives you a chance to prevent the change from happening. 
>
> Normally, you would return `true` from this method to allow the text field to resign the first responder status. You might return `false`, however, in cases where your delegate detects invalid contents in the text field. Returning `false` prevents the user from switching to another control until the text field contains a valid value. 
>
> If you use this method to validate the contents of the text field, you might also want to use an overlay view to provide feedback to that effect. For example, you might display a small icon indicating the text is invalid.

> [`textFieldDidEndEditing(_:)`](https://developer.apple.com/reference/uikit/uitextfielddelegate/1619591-textfielddidendediting)
>
> This method is called after the text field resigns its first responder status. You can use this method to update your delegate’s state information. For example, you might use this method to hide overlay views that should be visible only while editing.

*The primary method for controlling text:*
[`textField(_:shouldChangeCharactersIn:replacementString:)`](https://developer.apple.com/reference/uikit/uitextfielddelegate/1619599-textfield)

>- `textField`
>
> The text field containing the text.
>
>- `range`
>
> The range of characters to be replaced.
>
>- `string`
>
> The replacement string for the specified range. During typing, this parameter normally contains only the single new character that was typed, but it may contain more characters if the user is pasting text. When the user deletes one or more characters, the replacement string is empty.
>
>- The text field calls this method whenever user actions cause its text to change. Use this method to validate text as it is typed by the user. For example, you could use this method to prevent the user from entering anything but numerical values.

*The clear button (the ‘x’ on the right side of some text fields):*
[`textFieldShouldClear(_:)`](https://developer.apple.com/reference/uikit/uitextfielddelegate/1619594-textfieldshouldclear)

> The text field calls this method in response to the user pressing the built-in clear button. (This button is not shown by default but can be enabled by changing the value in the [`clearButtonMode`](https://developer.apple.com/documentation/uikit/uitextfield/1619622-clearbuttonmode) property of the text field.) This method is also called when editing begins and the [`clearsOnBeginEditing`](https://developer.apple.com/documentation/uikit/uitextfield/1619639-clearsonbeginediting) property of the text field is set to `true`.
>
> If you do not implement this method, the text field clears the text as if the method had returned `true`.

*The return key in the keyboard:*
[`textFieldShouldReturn(_:)`](https://developer.apple.com/reference/uikit/uitextfielddelegate/1619603-textfieldshouldreturn)

> The default implementation returns `true`, resigning first responder status. You can override this method in your custom responders to update your object's state or perform other actions, such as removing the highlight from a selection. You can also return `false`, refusing to relinquish first responder status. If you override this method, you must call `super` (the superclass implementation) at some point in your code.

## Control Flow Walk-trough

1. User taps keyboard

2. Textfield realizes text will change

3. Textfield invokes ...

   > [`textField(_:shouldChangeCharactersIn:replacementString:)`](https://developer.apple.com/reference/uikit/uitextfielddelegate/1619599-textfield)

4. VC receives invocation

5. VC assembles the new text

6. VC updates the label

7. VC returns "true" to allow change

```swift
// MARK: Text Field Delegate Methods

// Step 4
func textField(_ textField: UITextField, shouldChangeCharactersIn range: NSRange, replacementString string: String) -> Bool {
		
  	// Step 5 
    // Figure out what the new text will be, if we return true
    var newText = textField.text! as NSString
    newText = newText.replacingCharacters(in: range, with: string) as NSString

    // hide the label if the newText will be an empty string
    self.characterCountLabel.isHidden = (newText.length == 0)
		
  	// Step 6
    // Write the length of newText into the label
    self.characterCountLabel.text = String(newText.length)

  	// Step 7
    // returning true gives the text field permission to change its text
    return true;
}
```

> We can't see the first three steps.

## Emoji Textfield

```swift
func textField(_ textField: UITextField, shouldChangeCharactersIn range: NSRange, replacementString string: String) -> Bool {
        
        var replacedAnEmoji = false
        var emojiStringRange: NSRange
        
        // Construct the text that will be in the field if this change is accepted
        var newText = textField.text! as NSString
        newText = newText.replacingCharacters(in: range, with: string) as NSString

        // For each dictionary entry in translations, pull out a string to 
        // search for an emoji to replace it with
        
        for (emojiString, emoji) in translations {
          
            // Search for all occurances of key (ie. "dog"), 
            // and replace with emoji (ie. 🐶)
            // repeat-while loop
            repeat {
                emojiStringRange = newText.range(of: emojiString, options: .caseInsensitive)
            
                // found one
                if emojiStringRange.location != NSNotFound {
                    newText = newText.replacingCharacters(in: emojiStringRange, with: emoji) as NSString
                    replacedAnEmoji = true
                }
                
            } while emojiStringRange.location != NSNotFound
        }

        // If we have replaced an emoji, then we directly edit the text field
        // Otherwise we allow the proposed edit.
        if replacedAnEmoji {
            textField.text = newText as String
            return false
        } else {
            return true
        }
        
    }
```

