---
title: UITableView and Navigation
date: 2021-12-26 10:38:48
categories: 
- iOS
---

## Create UITabelView

> We need to link two delegates(datasource & delegate) from the tabelview in the storyboard to the ViewController.

```swift
import UIKit


class ViewController: UIViewController, UITableViewDataSource, UITableViewDelegate {
    
    // MARK: Properties
    
    // Get ahold of some villains, for the table
    // This is an array of Villain instances
    let allVillains = Villain.allVillains
    
    // MARK: Table View Data Source
    
    // The number of rows
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return self.allVillains.count
    }
    
    // The text of every row
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        
        let cell = tableView.dequeueReusableCell(withIdentifier: "VillainCell")!
        let villain = self.allVillains[(indexPath as NSIndexPath).row]
        
        // Set the name and image
        cell.textLabel?.text = villain.name
        cell.imageView?.image = UIImage(named: villain.imageName)
        
        // If the cell has a detail label, we will put the evil scheme in.
        if let detailTextLabel = cell.detailTextLabel {
            detailTextLabel.text = "Scheme: \(villain.evilScheme)"
        }
        
        return cell
    }

    func tableView(_ tableView: UITableView, canEditRowAt indexPath: IndexPath) -> Bool {
        return true
    }
    
  	// Use navigationController to show the corresponding page of each row 
    func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        let detailController = storyboard?.instantiateViewController(withIdentifier: "VillainDetailViewController") as! VillainDetailViewController
        detailController.villain = self.allVillains[indexPath.row]
        navigationController?.pushViewController(detailController, animated: true)
    }
}
```

> ### Creating and preparing the DetailViewController
>
> - Create a `VillainDetailViewController` with a 100x80 image view, and a label
> - Give the `VillainDetailViewController` a Storyboard ID
> - Give the `VillainDetailViewController` a villain property
> - Write the `viewWillAppear` method in the `VillainDetailViewController` so that it sets the image and label

## Create the Villain struct 

```swift
struct Villain {
    
    // MARK: Properties
    
    let name: String
    let evilScheme: String
    let imageName: String
    
    static let NameKey = "NameKey"
    static let EvilSchemeKey = "EvilScheme"
    static let ImageNameKey = "ImageNameKey"
    
    // MARK: Initializer
    
    // Generate a Villain from a three entry dictionary

    init(dictionary: [String : String]) {
    
        self.name = dictionary[Villain.NameKey]!
        self.evilScheme = dictionary[Villain.EvilSchemeKey]!
        self.imageName = dictionary[Villain.ImageNameKey]!
    }
}

// MARK: - Villain (All Villians)

/**
* This extension adds static variable allVillains. An array of Villain objects
*/

extension Villain {

    // Generate an array full of all of the villains in
    static var allVillains: [Villain] {
        
        var villainArray = [Villain]()
        
        for d in Villain.localVillainData() {
            villainArray.append(Villain(dictionary: d))
        }
        
        return villainArray
    }
    
    static func localVillainData() -> [[String : String]] {
        return [
            [Villain.NameKey : "Mr. Big", Villain.EvilSchemeKey : "Smuggle herion.",  Villain.ImageNameKey : "Big"],
            [Villain.NameKey : "Ernest Blofeld", Villain.EvilSchemeKey : "Many, many, schemes.",  Villain.ImageNameKey : "Blofeld"],
            [Villain.NameKey : "Sir Hugo Drax", Villain.EvilSchemeKey : "Nerve gass Earth, from the Moon.",  Villain.ImageNameKey : "Drax"],
            [Villain.NameKey : "Jaws", Villain.EvilSchemeKey : "Kill Bond with huge metal teeth.",  Villain.ImageNameKey : "Jaws"],
            [Villain.NameKey : "Rosa Klebb", Villain.EvilSchemeKey : "Humiliate MI6",  Villain.ImageNameKey : "Klebb"],
            [Villain.NameKey : "Emilio Largo", Villain.EvilSchemeKey : "Steal nuclear weapons", Villain.ImageNameKey : "EmilioLargo"],
            [Villain.NameKey : "Le Chiffre", Villain.EvilSchemeKey : "Beat bond at poker.",  Villain.ImageNameKey : "Lechiffre"],
            [Villain.NameKey : "Odd Job", Villain.EvilSchemeKey : "Kill Bond with razor hat.",  Villain.ImageNameKey : "OddJob"],
            [Villain.NameKey : "Francisco Scaramanga", Villain.EvilSchemeKey : "Kill Bond after assembling a golden gun.",  Villain.ImageNameKey : "Scaramanga"],
            [Villain.NameKey : "Raoul Silva", Villain.EvilSchemeKey : "Kill M.",  Villain.ImageNameKey : "Silva"],
            [Villain.NameKey : "Alec Trevelyan", Villain.EvilSchemeKey : "Nuke London, after killing Bond.",  Villain.ImageNameKey : "Trevelyan"],
            [Villain.NameKey : "Auric Goldfinger", Villain.EvilSchemeKey : "Nuke Fort Knox.",  Villain.ImageNameKey : "Goldfinger"],
            [Villain.NameKey : "Max Zorin", Villain.EvilSchemeKey : "Destroy Silicon Valley with an earthquake and flood.",  Villain.ImageNameKey : "Zorin"]
        ]
    }
}
```

## How to create a Navigation ViewController 

**Step 1:** Create a new single view application

**Step 2:** Delete the single view controller (You can delete this view controller by selecting the yellow icon and then tapping the delete button)

**Step 3:** Drag a `UINavigationController` into the storyboard from the object library.

**Step 4:** Delete the automatically generated root view controller and drag in a new view controller. To reset the Navigation Controller’s Root View Controller property control click on the Navigation Controller’s round yellow icon and drag a line from the root view controller triggered segue to your new view controller.

## Implement the StartOver button

```swift
class MYOAViewController: UIViewController {

    override func viewDidLoad() {
        super.viewDidLoad()
        navigationItem.rightBarButtonItem = UIBarButtonItem(
          title: "Start Over", 
          style: .plain, 
          target: self, 
          action: #selector(startOver))
    }

    func startOver() {
        if let navigationController = navigationController {
            navigationController.popToRootViewController(animated: true)
        }
    }

}
```

> The Navigation Controller is a stack data structure.

## How to Update the Rock Paper Scissors App with Navigation

**Step 1.** Drag a navigation controller into the storyboard and set its root view controller property to the `RockPaperScissorsViewController`.

**Step 2.** Change each of the three segues one at a time.

- Scissors is easiest: Just change the type to “show” instead of “modal”. Note the invocation of `prepare(for:sender:)`.
- Paper is essentially the same, but follow the invocation in the view controller and note the invocations of an IBAction, and the `performSegue(withIdentifier:sender:)` method.
- Rock is always the hard way. Read the documentation for `UINavigationController` to see how to programmatically push the results controller onto the navigation controller. Remember that the `RockPaperScissorsViewController` will have a navigation controller property that you can use in the action.

**Step 3.** Edit the `playAgainButtonPressed` method so that the “Play Again” button behaves similarly to the “Start Over” button in Make Your Own Adventure.
