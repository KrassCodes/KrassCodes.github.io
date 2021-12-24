---
layout: single
title:  "Coin Flip Game"
date:   2021-12-24 11:06:33 +0100
categories: swift iOS
toc: false
---
Prefer videos? [This post is also a video on YouTube](https://youtu.be/eK4xC1hZF9M){:target="_blank"}{:rel="noopener noreferrer"} 

<iframe width="560" height="315" src="https://www.youtube.com/embed/eK4xC1hZF9M" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


### Coin Flip Game

I wanted to make a very quick coin flipping game in SwiftUI and I contineu to be amazed at how few lines are needed for something like this. It can be better improved in many ways, but it was fun to throw together in an hour or so. 

<font size="3">
{% highlight swift %}
import SwiftUI

struct ContentView: View {
    @State var isFlipping = false
    @State var isHeads = false
    @State var degreesToFlip: Int = 0
    @State var tailsCount: Int = 0
    @State var headsCount: Int = 0
    
    var body: some View {
        VStack {
            VStack {
                Text("Heads: \(headsCount)")
                Text("Tails: \(tailsCount)")
            }
            Spacer()
            Coin(isFlipping: $isFlipping, isHeads: $isHeads)
                .rotation3DEffect(Angle(degrees: Double(degreesToFlip)), axis: (x: CGFloat(0), y: CGFloat(10), z: CGFloat(0)))
            Spacer()
            Button("Flip Coin") {
                flipCoin()
            }
        }
    }
    
    func flipCoin() {
        withAnimation {
            let randomNumber = Int.random(in: 5...6)
            if degreesToFlip > 1800000000 {
                reset()
            }
            degreesToFlip = degreesToFlip + (randomNumber * 180)
            headsOrTails()
            isFlipping.toggle()
        }
    }
    
    func headsOrTails() {
        let divisible = degreesToFlip / 180
        (divisible % 2) == 0 ? (isHeads = false) : (isHeads = true)
        isHeads == true ? (headsCount += 1) : (tailsCount += 1)
    }
    
    func reset() {
        degreesToFlip = 0
    }
}

struct Coin: View {
    @Binding var isFlipping: Bool
    @Binding var isHeads: Bool
    
    var body: some View {
        ZStack{
            Circle()
                .foregroundColor(isHeads ? .gray : .orange)
                .frame(width: 100, height: 100)
            Circle()
                .foregroundColor(isHeads ? .green : .yellow)
                .frame(width: 90, height: 90)
        }
    }
}

{% endhighlight %}
</font>

And that's it! 
