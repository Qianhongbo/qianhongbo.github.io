---
title: Network Foundations HTTP
date: 2021-12-30 19:13:37
categories: 
- iOS
---

# Networking Terms

- **Data:** (Usually in the raw form) anything that is sent over the network.
- **Client:**
- **Server:**
- **URL:** **U**niform **R**esource **L**ocator
- **HTTP:** **H**yper**t**ext **T**ransfer **P**rotocol
- **HTTPS:** HTTP **S**ecure,

# Anatomy of a URL

| Component | Name   |
| --------- | ------ |
| https://  | scheme |
| swift.org | host   |
| /about/   | path   |

| Order | Component |
| ----- | --------- |
| 1st   | scheme    |
| 2nd   | host      |
| 3rd   | port      |
| 4th   | path      |
| 5th   | query     |
| 6th   | fragment  |

# Status codes

HTTP status codes fall into 5 ranges based on the type of response.

- **100:** Informational response (request has been received by the server but is not finished yet)
- **200:** Successful response (request has been processed and completed by the server)
- **300:** Redirection (client needs to do something to complete the request)
- **400:** Unsuccessful (problem with the client's request)
- **500:** Unsuccessful (problem with the server)

A complete list of status codes can be found [here](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

| **Status Code** | **Short Description**                                        | **Category** |
| :-------------- | :----------------------------------------------------------- | :----------- |
| 100             | [Continue](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/100) | Information  |
| 200             | [OK](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/200) | Success      |
| 301             | [Moved Permanently](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/301) | Redirection  |
| 403             | [Forbidden](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/403) | Client Error |
| 404             | [Not Found](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/404) | Client Error |
| 418             | [I'm a teapot](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/418) | Client Error |
| 500             | [Internal Server Error](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/500) | Server Error |

# HTTP Verbs

CRUD:

- Create
- Read
- Update
- Delete

| Verb   | CRUD Operation  |
| ------ | --------------- |
| GET    | Read            |
| POST   | Create          |
| PUT    | Update(Replace) |
| PATCH  | Update(Modify)  |
| DELETE | Delete          |

- **GET**: Retrieves data from the server, such as getting a list of search results.
- **POST**: Submits data to be stored in the server, such as when submitting a form.
- **PUT**: Updates data on the server, replacing the old data with the new data.
- **PATCH**: Updates data on the server, by changing specific values.
- **DELETE**: Used when removing data from the server.

# URL

# string

```swift
// Task 1: create a valid URL

let website = "http://udacity.com"
let url = URL(string: website)
print(url!)

// Task 2: append an "ios" path component to the URL

url?.appendPathComponent("ios")
if let url = url {
    print("URL: \(url)")
}
```

> We can't add query and fragment.

## URLComponents

```swift
import Foundation

// create a new URLComponents
var components = URLComponents()

// set the scheme
components.scheme = "https"
// set the host
components.host = "google.com"
// set the path
components.path = "/search"

// set the query, you'll need to create a new URLQueryItem
components.queryItems = [URLQueryItem(name: "query", value: "udacity")]

print(components.url ?? "")
```

## Use struct to store URLComponents

```swift
import Foundation

let urlString = "https://itunes.apple.com/us/app/udacity/id819700933?mt=8"

let urlComponents = URLComponents(string: urlString)

if let urlComponents = urlComponents {
    print("scheme:\t\t\(String(reflecting: urlComponents.scheme))")
    print("user:\t\t\(String(reflecting: urlComponents.user))")
    print("password:\t\(String(reflecting: urlComponents.password))")
    print("host:\t\t\(String(reflecting: urlComponents.host))")
    print("port:\t\t\(String(reflecting: urlComponents.port))")
    print("path:\t\t\(String(reflecting: urlComponents.path))")
    print("query:\t\t\(String(reflecting: urlComponents.query))")
    print("fragment:\t\(String(reflecting: urlComponents.fragment))")
}

struct AppStore {
    static let scheme = "https"
    static let host = "itunes.apple.com"
    static let udacityPath = "/us/app/udacity/id819700933"
    
    enum ParameterKey: String {
        case mediaType = "mt"
    }
    
    enum MediaType: String {
        case music = "1",
        podcasts = "2",
        audiobooks = "3",
        tvShows = "4",
        musicVideos = "5",
        movies = "6",
        iPodGames = "7",
        mobileApps = "8",
        ringTones = "9",
        iTunesU = "10",
        ebooks = "11",
        desktopApps = "12"
    }

}

var udacityAppURL = URLComponents()
udacityAppURL.scheme = AppStore.scheme
udacityAppURL.host = AppStore.host
udacityAppURL.path = AppStore.udacityPath

udacityAppURL.queryItems = [URLQueryItem(name: AppStore.ParameterKey.mediaType.rawValue, value: AppStore.MediaType.mobileApps.rawValue)]
print(udacityAppURL)
```

## URLSession

"Tasks" are what `URLSession` calls network requests.

- `URLSessionDataTask`: Used for perfuming an HTTP request (GET, POST, etc.).
- `URLSessionDownloadTask`: Downloading a file from a server.
- `URLSessionUploadTask`: Uploading a file to a server
- `URLSessionStreamTask`: Getting a continuous stream of data from a server.

> After you create the task, you must start it by calling its [`resume()`](https://developer.apple.com/documentation/foundation/urlsessiontask/1411121-resume) method.
>
> Newly-initialized tasks begin in a suspended state, so you need to call this method to start the task.

###URLSession.shared.dataTask 

```swift
@IBAction func loadImage(_ sender: Any) {
        guard let url = URL(string: self.imageLocation) else {
            print("Cannot create url!")
            return
        }
        let task = URLSession.shared.dataTask(with: url) {
            (data, response, error) in
            guard let data = data else {
                print("no data")
                return
            }
            let downLoadedImage = UIImage(data: data)
          // the code of network request is working on the background
          // have to move this line of code to the main thread
            DispatchQueue.main.async {
                self.imageView.image = downLoadedImage
            }
        }
  // have to use resume() to start the task 
        task.resume()
    }
```

#### Trailing closure syntax

Many functions in iOS accept multiple parameters where the final parameter is a closure.

```swift
let task = URLSession.shared.dataTask(with: url, completionHandler: {
  
})

// trailing closure syntax
let task = URLSession.shared.dataTask(with: url) {
  
}
```

### URLSession.shared.downloadTask

```swift
@IBAction func loadImage(_ sender: Any) {
        guard let url = URL(string: self.imageLocation) else {
            print("Cannot create url!")
            return
        }
        let task = URLSession.shared.downloadTask(with: url) {
            (location, response, error) in
            guard let location = location else{
                print("location is nil!")
                return
            }
            let downLoadedImage = try! Data(contentsOf: location)
            DispatchQueue.main.async {
                self.imageView.image = UIImage(data: downLoadedImage)
            }
        }
        task.resume()
    }
```

# API

## API & Web Service

There you have it: an API is an interface that allows you to build on the data and functionality of another application, while a web service is a network-based resource that fulfills a specific task. Yes, there’s overlap between the two: all web services are APIs, but not all APIs are web services.

## DogAPI

### JSONSerialization

```swift
class DogAPI {
    enum endpoint: String {
        case randomImageFromAllDogsCollection = "https://dog.ceo/api/breeds/image/random"
        
        var url: URL {
            return URL(string: self.rawValue)!
        }
    }
}
```

```swift
let randomImageEndpoint = DogAPI.endpoint.randomImageFromAllDogsCollection.url
        
let tast = URLSession.shared.dataTask(with: randomImageEndpoint) { 
  data, response, error in
  guard let data = data else {
    return
  } do {
    let json = try JSONSerialization.jsonObject(with: data, options: []) as! [String: Any]
    let url = json["message"] as! String
    print(url)
  } catch {
    print(error)
  }
}
tast.resume()
```

## Codable

```swift
struct DogImage: Codable {
    let status: String
    let message: String
}
```

```swift
let decoder = JSONDecoder()
let imageData = try! decoder.decode(DogImage.self, from: data)
print(imageData) // a struct
```

## JSON parsing scenarios

- **Coding Keys** (a way to map differently named JSON keys to Swift property names)
- **Parsing JSON Arrays**
- **Nested** JSON objects
- **Accessing keys and values** (treating a JSON object like a dictionary when we don't know what the keys and values will be)

### Coding Keys 

```swift
import Foundation

var json = """
{
    "food_name": "Lemon",
    "taste": "sour",
    "number of calories": 17
}
""".data(using: .utf8)!

struct Food: Codable {
    let name: String
    let taste: String
    let calories: Int

    enum CodingKeys: String, CodingKey {
        case name = "food_name"
        case taste
        case calories = "number of calories"
    }
}

let decoder = JSONDecoder()
let food: Food

do {
    food = try decoder.decode(Food.self, from: json)
    print(food)
} catch {
    print(error)
}
```

### Parsing JSON Arrays

```swift
import Foundation

var json = """
[
    {
        "title": "Groundhog Day",
        "released": 1993,
        "starring": ["Bill Murray", "Andie MacDowell", "Chris Elliot"]
    },
    {
        "title": "Home Alone",
        "released": 1990,
        "starring": ["Macaulay Culkin", "Joe Pesci", "Daniel Stern", "John Heard", "Catherine O'Hara"]
    }
]
""".data(using: .utf8)!

struct Movie: Codable {
    let title: String
    let released: Int
    let starring: [String]
}

let decoder = JSONDecoder()
let comedies: [Movie]

do {
    comedies = try decoder.decode([Movie].self, from: json)
    print(comedies)
} catch {
    print(error)
}
```

### Nested JSON objects 

```swift
import Foundation

var json = """
{
    "name": "",
    "studentId: 326156,
    "academics": {
        "field": "iOS",
        "grade": "A"
    }
}
""".data(using: .utf8)!

struct Academics: Codable {
    let field: String
    let grade: String
}

struct Student: Codable {
    let name: String
    let studentId: String
    let academics: Academics
}

let decoder = JSONDecoder()
let student: Student

do {
    student = try decoder.decode(Student.self, from: json)
    print(student)
} catch {
    print(error)
}
```

### Accessing keys and values 

```swift
import Foundation

let json = """
{
    "100": {
        "type": "colonial",
        "location": "Plainville, MA",
        "bedrooms": 3,
        "bathrooms": 2.5,
        "has air conditioning": false,
        "amenities": ["basement", "garden"],
        "listing": {
            "price": 430000,
            "date": "May 2018"
        }
    },
    "101": {
        "type": "condo",
        "location": "San Francisco, CA",
        "bedrooms": 1,
        "bathrooms": 1,
        "has air conditioning": true,
        "amenities": [],
        "listing": {
            "price": 765000,
            "date": "September 2018"
        }
    }
}
""".data(using: .utf8)!

struct Listing: Codable {
    let price: Int
    let date: String
}

struct House: Codable {
    let houseType: String
    let location: String
    let beds: Int
    let baths: Float
    let hasAirConditioning: Bool
    let amenities: [String]
    let listing: Listing
    
    enum CodingKeys: String, CodingKey {
        case houseType = "type"
        case location = "location"
        case beds = "bedrooms"
        case baths = "bathrooms"
        case hasAirConditioning = "has air conditioning"
        case amenities
        case listing
    }
}

let decoder = JSONDecoder()
do {
    let houseDict = try decoder.decode([String: House].self, from: json)
    print(houseDict)
    let houses = houseDict.values.map({$0})
    let houseIds = houseDict.keys.map({$0})
    print(houses)
    print(houseIds)
} catch {
    print(error)
}
```

> We have to use [map](https://developer.apple.com/documentation/swift/array/3017522-map) to transform the data from keys `type` to `array` type.

## Post request in Swift

```swift
import Foundation
import CoreFoundation

// create a Codable struct called "POST" with the correct properties
struct Post: Codable {
    let userId: Int
    let title: String
    let body: String
}

// create an instance of the Post struct with your own values
let post = Post(userId: 1, title: "udacity", body: "udacious")

// create a URLRequest by passing in the URL
var request = URLRequest(url: URL(string: "https://jsonplaceholder.typicode.com/posts")!)
// set the HTTP method to POST
request.httpMethod = "POST"
// set the HTTP body to the encoded "Post" struct
request.httpBody = try! JSONEncoder().encode(post)
// set the appropriate HTTP header fields
request.addValue("application/json", forHTTPHeaderField: "Content-Type")
// HACK: this line allows the workspace or an Xcode playground to execute the request, but is not needed in a real app
let runLoop = CFRunLoopGetCurrent()
// task for making the request
let task = URLSession.shared.dataTask(with: request) {data, response, error in
    print(String(data: data!, encoding: .utf8))
    // also not necessary in a real app
    CFRunLoopStop(runLoop)
}
task.resume()
// not necessary
CFRunLoopRun()s
```

