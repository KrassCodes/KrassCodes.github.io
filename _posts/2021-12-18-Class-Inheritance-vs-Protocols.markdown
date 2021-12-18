---
layout: single
title:  "Class Inheritance vs Protocols"
date:   2021-12-18 15:06:33 +0100
categories: swift
toc: false
---
Prefer videos? [This post is also a video on YouTube](https://youtu.be/xKxQ7LRiVOY){:target="_blank"}{:rel="noopener noreferrer"} 

<iframe width="560" height="315" src="https://www.youtube.com/embed/xKxQ7LRiVOY" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>


### Class Inheritance

Protocols are a powerful tool in Swift. Unfortunately, many people begin learning programming with the Object Oriented Programming paradigm where Class Inheritance is the default structure of defining object capabilities. 

For a quick refresher, let's take a look at what a very simple Class-Inheritance looks like. In the below code we create a City class and then we create a subclass called London. 


<font size="3">
{% highlight swift %}
class City {
    var population: Int
    var continent: String
    
    init(population: Int, continent: String) {
        self.population = population
        self.continent = continent
    }
}
class London: City {
    var hasSubway: Bool = true
}
let london = London(population: 10000000, continent: "Europe")
print(london.population)
~> // 10000000
{% endhighlight %}
</font>

This is a very typical way to pass capabilities. In this case the London class needs to do everything the City class can do PLUS it's own more detailed things. For this example we've added a hasSubway property which is specific to London and not all Cities. 

This works, but there's a better way. 


### Swift Protocols 

With a Protocol we can define a bundle of functionality (a blueprint). That then any class (or Struct!) can comply to. Furthermore, compliance to many protocols is as easy as adding them one after the other or through an Extension. 

The below example does the same thing as the Class Inheritance above, but we've also added additional functionality by having the London class also comply to another Protocol called Changeable. 

You can see how by complying to additional protocols we can add and remove functionality to a class like puzzle pieces. 

<font size="3">
{% highlight swift %}
protocol City {
    var population: Int { get set }
    var continent: String { get }
}
protocol Changeable {
    func grow(amount: Int)
    func shrink(amount: Int)
}
class London: City {
    var population: Int 
    var continent: String
    
    init(population: Int, continent: String) {
        self.continent = continent
        self.population = population
    }
}
extension London: Changeable {
    func grow(amount: Int) {
        self.population += amount
    }
    func shrink(amount: Int) {
        self.population -= amount
    }
}
let london = London(population: 10000000, continent: "Europe")
print(london.population)
~> // 10000000
london.grow(amount: 5000000)
print(london.population)
~> // 15000000

{% endhighlight %}
</font>


There are many more benefits of using protocols, especially when dealing with unit tests, but that is an example for another time. 

And that's it! 
