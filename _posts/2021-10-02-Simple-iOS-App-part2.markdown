---
layout: single
title:  "Part 2: Simple iOS App with SwiftUI + API Calls + Unit Tests"
date:   2021-10-02 20:06:33 +0100
categories: swift iOS
toc: true
toc_label: "Table of Contents"
toc_icon: "list"
gallery:
  - url: /assets/20211003-6.png
    image_path: /assets/20211003-6.png
    alt: "Create Unit Testing target"
gallery2:
  - url: /assets/20211003-7.png
    image_path: /assets/20211003-7.png
    alt: "Failed unit test"
gallery3:
  - url: /assets/20211003-8.png
    image_path: /assets/20211003-8.png
    alt: "Successful unit test"
---
Prefer videos? [This post is also a video on YouTube](https://www.youtube.com/channel/UCSMxuZP6KUb_i9F-K1LAtrw){:target="_blank"}{:rel="noopener noreferrer"} 
### Intro

I recently decided to create a simple iOS app which combines a few of the most common requirements of moderns apps: **Call an API**, **Use unit tests**, and **Leverage SwiftUI** to build a decent looking user interface. 

This is **Part 2 - Unit Tests**

### The Goal

Create unit tests that will test the **FlightDataFetcher** class which we are using to connect to the OpenSky Network API. 

### Part 1 - Create a Testing target in the Xcode project

In order to add Unit Tests to our project we need to create a new Unit Testing target. We can do this by going to the project settings and adding a new target from the **+** button as shown below. 

After the new target has been created we will have a new folder in the project navigator. The boilerplate text looks like this:

<font size="3">
{% highlight swift %}
class ARFligthTrackerTests: XCTestCase {

    override func setUpWithError() throws {
        // Put setup code here. This method is called before the invocation of each test method in the class.
    }

    override func tearDownWithError() throws {
        // Put teardown code here. This method is called after the invocation of each test method in the class.
    }

    func testExample() throws {
        // This is an example of a functional test case.
        // Use XCTAssert and related functions to verify your tests produce the correct results.
    }

    func testPerformanceExample() throws {
        // This is an example of a performance test case.
        measure {
            // Put the code you want to measure the time of here.
        }
    }

}

{% endhighlight %}
</font>

Let's simplify this code a bit as we don't need all of these cases for this demo. Below is a very simple test we can use to start with. It just calls the Class's **getFlightInfo** method and we wait to hear back with an answer. If that answer is not **nil** we suceed.  


<font size="3">
{% highlight swift %}
import XCTest
import ARFligthTracker

class ARFligthTrackerTests: XCTestCase {
    
    func testFlightDataFetcher() throws {
        let dataFetcher = FlightDataFetcher()
        var flightInfo: FlightInfo?
        let expectation = self.expectation(description: "API Call complete")
        dataFetcher.getFlightInfo { result in
            flightInfo = result
            expectation.fulfill()
        }
        waitForExpectations(timeout: 10, handler: nil)
        XCTAssertNotNil(flightInfo)
    }
}
{% endhighlight %}
</font>
 
Unfortunately, if you are like me, you may get unlucky and the test will fail :( . 

Why? Well there's a lot of different reasons why we may not get a response. The simplest explanation is that there weren't any airplanes in the area we defined, but it could also be other problems that are unrelated to our code. For example the API could be slow that day and take longer than our timeout. 

So, to get around all of these other reasons the test could fail that have nothing to do with our code, we can use a mock response. 

### Part 2 - Create a Mock API response

Let's start by creating a string which is a direct copy of an API response. 

<font size="3">
{% highlight swift %}
struct MockAPIResponse {
    let mockResponse = """
    {
        "time": 1627589855,
        "states": [
            [
                "44cdc9",
                "BEL4UU",
                "Belgium",
                1627589854,
                1627589854,
                -0.1788,
                51.4783,
                982.98,
                false,
                102.89,
                270,
                -5.2,
                null,
                1028.7,
                "4417",
                false,
                0
            ]
        ]
    }
    """
}
{% endhighlight %}
</font>

Now we need to find a way to give this mocked response to our **FlightDataFetcher** class. That way when we are testing **FlightDataFetcher** we don't have to wait for the real API to send us a response. 

In order to do this we'll need to make a few changes to the **FlightDataFetcher** class. 

1. Create a new property **mode** which will be set at initialisation and be set to either **testing** or **production**
2. Require the new property be set in the **init** 
3. In the **getFlightInfo** function we create a short **if** statement which checks if we are in **testing** mode. If so, we immediately retunr the mocked response without going forward to call the real API.

<font size="3">
{% highlight swift %}
    
    /* 1. Determines if the class is in Testing mode or Production mode. Set when the class is initialised. */
    var mode: FlightDataFetcherMode
    
    public init(mode: FlightDataFetcherMode) {
        /* 2. When the class is initialised we need to say if we want it to be used for testing or production. */
        self.mode = mode
    }
    
    /* This is the function which is called when the the main button us pressed */
    public func getFlightInfo(completionHandler: @escaping (FlightInfo) -> Void) {
        
        /* 3. If we are in testing mode, we want to return a mocked Object instead of waiting for the real API. */
        if mode == .testing {
            do {
                let flight = try JSONDecoder().decode(FlightInfo.self, from:MockAPIResponse().mockResponse.data(using: .utf8)!)
                completionHandler(flight)
            } catch {
                print ("Should not happen!")
            }
        }
{% endhighlight %}
</font>


### Part 3 - Test the **FlightDataFetcher** class using the mock API response

Now we can finally go back to our unit tests and initialise our **FlightDataFetcher** in testign mode. 

<font size="3">
{% highlight swift %}
import XCTest
import ARFligthTracker

class ARFligthTrackerTests: XCTestCase {
    
    func testFlightDataFetcher() throws {
        let dataFetcher = FlightDataFetcher(mode: .testing) /* NEW: initialised in testing mode */
        var flightInfo: FlightInfo?
        let expectation = self.expectation(description: "API Call complete")
        dataFetcher.getFlightInfo { result in
            flightInfo = result
            expectation.fulfill()
        }
        waitForExpectations(timeout: 10, handler: nil)
        XCTAssertNotNil(flightInfo)
    }
}
{% endhighlight %}
</font>



Once we do that, we should be able to run our test and be successful. 

And that's it! 

In **Part 3** we will create a UI that will be able to use this API data and display it to the user. 
