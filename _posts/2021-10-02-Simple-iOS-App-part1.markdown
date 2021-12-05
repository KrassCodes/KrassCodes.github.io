---
layout: single
title:  "Part 1: Simple iOS App with SwiftUI + API Calls + Unit Tests"
date:   2021-10-02 20:06:33 +0100
categories: swift iOS
toc: true
toc_label: " In This Post"
toc_icon: "list"
toc_sticky: true
gallery:
  - url: /assets/20211003-1.png
    image_path: /assets/20211003-1.png
    alt: "First screen"
  - url: /assets/20211003-1.png
    image_path: /assets/20211003-2.png
    alt: "Second screen"
  - url: /assets/20211003-1.png
    image_path: /assets/20211003-3.png
    alt: "Third screen"
gallery2:
  - url: /assets/20211003-4.png
    image_path: /assets/20211003-4.png
    alt: "Postman API check"
gallery3:
  - url: /assets/20211003-5.png
    image_path: /assets/20211003-5.png
    alt: "Convert JSON response to Swift"
---
Prefer videos? [This post is also a video on YouTube](https://www.youtube.com/channel/UCSMxuZP6KUb_i9F-K1LAtrw){:target="_blank"}{:rel="noopener noreferrer"} 
### Intro

I recently decided to create a simple iOS app which combines a few of the most common requirements of moderns apps: **Call an API**, **Use unit tests**, and **Leverage SwiftUI** to build a decent looking user interface. 

This is **Part 1 - API Calls**


### The Goal

Airplanes have been an interest of mine since I was little and I always find myself looking up at the sky when one is passing. I decided to build a very simple app that can tell you which airplane is above you. Of course there are plenty of professional apps that can do this, but I think it's a good opportunity to learn a few of the basics of making apps 😀

This is what the result looks like:



### Part 1 - Find a (free!) API and Implement It

Googling for a bit yields about a dozen flight data providers which you can use to get information about current and historical flights. After testing a couple of them, I chose [The OpenSky Network](https://opensky-network.org/){:target="_blank"}{:rel="noopener noreferrer"} . 

Registration is relatively straightforward and once you have your API key saved, we're ready to start diving into the code. 

> **_WARNING:_**  Never hardcode credentials in code! Especially if they can get committed to an online repo. In this example I have used the `username` and `password` fields only as a demo for learning and NOT for a production project. 
{: .notice--danger}


<font size="3">
{% highlight swift %}
public class FlightDataFetcher {
    
    private let username = ""  /* Your API username */
    private let password = ""  /* Your API password */
    
        /* NOTE: The coordinates here are roughly for the Heathrow airport 
         which is typically very busy and would guarantee that at 
         least one airplane is around. Using other locations would 
         make it hard to test the API as it would be unpredictable 
         when an airplane is around. */
    private var url: URL {
        URL(string: "http://" + username + ":" + password +
         "@opensky-network.org/api/states/all?lamin=51.44&lomin=-0.55&lamax=51.51&lomax=-0.35")!
    }
    
    public init() {}
    
    /* This is the function which is called when the the main button us pressed */
    func getFlightInfo(completionHandler: @escaping (FlightInfo) -> Void) {
        
        /* The API can be slow so we create a custom 
        URLSessionConfiguration with 10 sec timeout */
        let sessionConfig = URLSessionConfiguration.default
        sessionConfig.timeoutIntervalForResource = 10
        let session = URLSession(configuration: sessionConfig)
        
        /* Data task makes the call to the API */
        let task = session.dataTask(with: url) {(data, response, error) in
            
            /* Only continue if there is no error and if we received data */
            guard let data = data, error != nil else {
                return
            }
            
            /* Try to decode the data we receive from the API into a FlightInfo object */
            do {
                let flight = try JSONDecoder().decode(FlightInfo.self, from: data)
                DispatchQueue.main.async {
                /* If decoding was successfull call the completion 
                handler and pass it the FlightInfo object */
                    completionHandler(flight)
                }
            } catch {
            /* Catch any errors. In this case we just print an error. 
            In a more professional app you may want to show it as a message
            to the user */ 
                print ("Error to handle")
            }
        }
        task.resume()
    }
}
{% endhighlight %}
</font>



In order for the above code to work we also need to create the **FlightInfo** object which is used to decode the API response. I've found the best way to do something like this is to use the an app like [Postman](https://www.postman.com){:target="_blank"}{:rel="noopener noreferrer"} to call the API you plan to call, copy the response you get and then use a website like [app.quicktype.io](https://app.quicktype.io){:target="_blank"}{:rel="noopener noreferrer"} to convert it into objects that Swift will understand. 

Step 1: Use Postman to call the API and get the response. We use the same URL that we have in our code:
**http://username:password@opensky-network.org/api/states/all?lamin=51.44&lomin=-0.55&lamax=51.51&lomax=-0.35**

Step 2: Use [app.quicktype.io](https://app.quicktype.io){:target="_blank"}{:rel="noopener noreferrer"} to convert the JSON API response into Swift objects. 

Then all we have left to do is rename the objects so they are more appropriate for our app.

<font size="3">
{% highlight swift %}

struct FlightInfo: Codable {
    let time: Int
    var states: [[FlightState]]
}

enum FlightState: Codable {
    case bool(b: Bool)
    case double(d: Double)
    case string(s: String)
    case null
    
    var rawValue: String {
          get {
              switch self {
              case .double(d: let d):
                  return String(describing: d)
              case .bool(b: let b):
                  return String(describing: b)
              case .string(s: let s):
                  return String(describing: s)
              case .null:
                  return "N/A"
              }
          }
      }
    
    init(from decoder: Decoder) throws {
        let container = try decoder.singleValueContainer()
        if let x = try? container.decode(Bool.self) {
            self = .bool(b:x)
            return
        }
        if let x = try? container.decode(Double.self) {
            self = .double(d:x)
            return
        }
        if let x = try? container.decode(String.self) {
            self = .string(s:x)
            return
        }
        if container.decodeNil() {
            self = .null
            return
        }
        throw DecodingError.typeMismatch(FlightState.self, DecodingError.Context(codingPath: decoder.codingPath, debugDescription: "Wrong type for State"))
    }
    
    func encode(to encoder: Encoder) throws {
        var container = encoder.singleValueContainer()
        switch self {
        case .bool(let x):
            try container.encode(x)
        case .double(let x):
            try container.encode(x)
        case .string(let x):
            try container.encode(x)
        case .null:
            try container.encodeNil()
        }
    }
}

}
{% endhighlight %}
</font>

And that's it! 

In **Part 2** we will create a unit test to make sure that our **FlightDataFetcher** class performs as expected. 
