---
layout: single
title:  "Part 3: Simple iOS App with SwiftUI + API Calls + Unit Tests"
date:   2021-10-02 20:06:33 +0100
categories: swift iOS
toc: true
toc_label: "Table of Contents"
toc_icon: "list"
---
Prefer videos? [This post is also a video on YouTube](https://www.youtube.com/channel/UCSMxuZP6KUb_i9F-K1LAtrw){:target="_blank"}{:rel="noopener noreferrer"} 
### Intro

I recently decided to create a simple iOS app which combines a few of the most common requirements of moderns apps: **Call an API**, **Use unit tests**, and **Leverage SwiftUI** to build a decent looking user interface. 

This is **Part 3 - Leverage SwiftUI**

### The Goal

Create a user interface that allows the user to scan which airplane is above a certain geographical area. 

### Part 1 - Create the landing page

The landing page is a simple button with an icon which indicates to the user that if they press it, the scanning will begin. 

<font size="3">
{% highlight swift %}
struct ContentView: View {
    
    /* Create an instance of the FlightDataFetcher and set
     it to production or testing, depending on how you want to use it.*/
    @State var checker = FlightDataFetcher(mode: .testing)
    
    /* The object which will contain the details of a
     specific flight, if one is found by the API.*/
    @State var flightInfo: FlightInfo?
    
    /* Indication if we are currently in the process of scanning
     for flight data. In reality this means we are waiting for the
     API to respond.*/
    @State var isScanning: Bool = false
    
    /* Indication if the sheet witht the flight results has been
     displayed to the user or not.*/
    @State var showingFlightDetailsSheet = false
    
    var body: some View {
        ZStack {
            /* The main button */
            Button(action: {
                /* When button is pressed, toggle the scanning status */
                isScanning.toggle()
                /* Call the API */
                checker.getFlightInfo { result in
                    /* If we get a result from the API, store the object */
                    flightInfo = result
                    /* If we don't get a valid response reset the states */
                    if (flightInfo != nil) {
                        showingFlightDetailsSheet = true
                        isScanning = false
                    }
                }
            }) {
                /* If we are in the process of scanning, shopw the RadarView (aka scanning view) */
                if isScanning {
                    RadarView(isScanning: $isScanning)
                }
                /* If we are not scanning, show the landing page elements */
                else {
                    VStack {
                        Image(systemName: "airplane.circle")
                            .resizable()
                            .frame(width: 150, height: 150)
                            .rotationEffect(.degrees(-45))
                        Text("Tap to scan")
                            .foregroundColor(.gray)
                            .padding(6)
                            .overlay(
                                Capsule(style: .continuous).stroke(.gray)
                            )
                    }
                }
            }
        }
        /* The sheet which presents the flight details*/
        .sheet(isPresented: $showingFlightDetailsSheet) {
            if let flightInfo = flightInfo {
                FlightDetailsView(flightInfo: flightInfo)
            }
        }
    }
}
{% endhighlight %}
</font>




### Part 2 - Create the scanning page

The scanning page (aka Radar page) is a "fancy" animation that is supposed to represent a radar scanning for flights 😀. 

<font size="3">
{% highlight swift %}
struct RadarView : View {
    
    /* Indication of the animation state */
    @State private var isAnimated = false
    
    /* Indication of the scanning state */
    @Binding var isScanning: Bool
    
    var body: some View {
        /* A combination of images and animations which, hopefully, look like a radar screen... */
        VStack{
            ZStack {
                Image(systemName: "aqi.low")
                    .resizable()
                    .frame(width: 120, height: 120)
                    .opacity(isAnimated ? 1 : 0)
                    .animation(isAnimated ? .linear(duration: 1).repeatForever(autoreverses: true) : nil)
                Image(systemName: "target")
                    .resizable()
                    .frame(width: 150, height: 150)
                Image(systemName: "line.diagonal")
                    .resizable()
                    .frame(width: 50, height: 50)
                    .rotationEffect(Angle.degrees(isAnimated ? 360 : 0), anchor: .bottomLeading)
                    .animation(isAnimated ? .linear(duration: 1.5).repeatForever(autoreverses: false) : nil)
                    .offset(x: 25, y: -25)
                    .onAppear() {
                        isAnimated = true
                    }
            }
            .foregroundColor(.green)
            HStack{
                Image(systemName: "xmark.circle.fill")
                Text(NSLocalizedString("Scanning...", comment: "sjdf"))
            }
            .foregroundColor(.gray)
            .padding(6)
            .overlay(
                Capsule(style: .continuous).stroke(.gray)
            )
        }
    }
}

{% endhighlight %}
</font>



### Part 3 - Create the results page

Finally the result page is a very simple table with the properties of the **FlightInfo** object. 


<font size="3">
{% highlight swift %}
struct FlightDetailsView: View {
    let flightInfo: FlightInfo
    
    var body: some View {
        List {
            /* We pull out only the data we want to display from the array.
             Importantly, here we only look at the first ([0]) object even if
             there are more than one. This means we'll only see data for
             one flight.*/
            Text("Callsign: \(flightInfo.states[0][1].rawValue)")
            Text("Country: \(flightInfo.states[0][2].rawValue)")
            Text("On ground: \(flightInfo.states[0][8].rawValue)")
            Text("Velocity: \(flightInfo.states[0][9].rawValue)")
            Text("Altitude: \(flightInfo.states[0][7].rawValue)")
        }
    }
}
{% endhighlight %}
</font>

And that's it! 
