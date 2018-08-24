---
layout: post
title:  "From My Notebook: Object Oriented Programming vs Functional Programming"
date:   2018-08-23 22:43:00 -0700
categories: From My Notebook
---

Like most recently graduated programmers, I've spent most of my time learning and working within the object oriented programming paradigm. Classes I took at UCI touched on concepts of functional programming, but at the end of the day it has always come back to OOP. In my mind, OOP was just "regular" programming, and functional programming was some odd-ball topic to be explored another day. Well, that day is today.
<!--break-->

I finally took the time to sit down, get Googling, and read up on the topic until I felt like I had a good basic understanding of it. What follows is a transcription of my hand-written notes as well as some examples that may help clarify the concepts. I can't take credit for this -- besides the pure/impure function examples, it's all taken from various sources I personally found useful. As I learned about functional programming, I found that there isn't a single definition. It's a broad paradigm, so different people have different ways of talking about it. Because of all thse varying perspectives, I thought it would be valuable to have a compilation of information I found. At first it was just stored for my own sanity, but I figured I'd make it public in case it's useful to somebody else down the line. Sources are linked at the bottom.

## Paradigm Comparison

<table>
    <tr>
        <th></th>
        <th>Object Oriented Programming</th>
        <th>Functional Programming</th>
    </tr>
    <tr>
        <td>Philosophy</td>
        <td>Having data and its behavior in a single location makes it easier to understand how a program works (encapsulation)</td>
        <td>Data and behavior are distinctly different things and should be kept separate for clarity</td>
    </tr>
    <tr>
        <td>What is it?</td>
        <td>
            <ul>
                <li>Everything is built upon objects, which...</li>
                <ul>
                    <li>Contain data (attributes)</li>
                    <li>Contain code (methods) that can access and modify data</li>
                    <li>Have a notion of self (this)</li>
                </ul>
                <li>Seeks to mimic real life</li>
                <li>Imperative: tell the computer <em>how</em> to do something</li>
            </ul>
        </td>
        <td>
            <ul>
                <li>Complete separation of data and behvior</li>
                <li>Objects are immutable</li>
                <li>Shared state is avoided</li>
                <li>Uses only pure functions</li>
                <ul>
                    <li>Return value varies based only on input</li>
                    <li>No side effects</li>
                    <li>Does not alter data it is given -- only generates output based on the input</li>
                </ul>
                <li>Declarative: tell the computer <em>what</em> you want to be done</li>
            </ul>
        </td>
    </tr>
    <tr>
        <td>When to use it</td>
        <td>
            <ul>
                <li>You have a fixed set of operations on various things</li>
                <li>As your code evolves, you will mostly be adding new things, not new operations</li>
                <ul>
                    <li>This can be easily done with classes</li>
                </ul>
            </ul>
        </td>
        <td>
            <ul>
                <li>When you have various operations working on a fixed set of things</li>
                <li>As your code evolves you will mostly be adding new operations on existing things</li>
                <ul>
                    <li>Can be easily added with (pure) functions</li>
                </ul>
            </ul>
        </td>
    </tr>
    <tr>
        <td>Pros</td>
        <td>
            <ul>
                <li>Can be easier to build a mental model for, since it more closely mirrors real life</li>
                <li>Code can be read and analyzed as step-by-step instructions</li>
                <li>Encapsulating data and the operations that work on that data can make it more clear what the operations are doing</li>
            </ul>
        </td>
        <td>
            <ul>
                <li>Eliminates "clutter" from loops, indices, etc.</li>
                <ul>
                    <li>Makes code more concise and easier to digest quickly (provided you understand the syntax)</li>
                    <li>Less likely to introduce bugs from simple coding errors</li>
                </ul>
                <li>Avoids side effects and mutable state</li>
                <ul>
                    <li>Easier to keep track of state in very large applications</li>
                    <li>Makes parallel processes  easier to manage, since they do not have to worry about values being changed by another process (avoids race conditions)</li>
                    <li>Can refactor/optimize with confidence, since only output matters</li>
                </ul>
            </ul>
        </td>
    </tr>
    <tr>
        <td>Cons</td>
        <td>
            <ul>
                <li>If you ever need to add new functionality, you will likely need to change many objects</li>
                <li>Race conditions can be a pain to handle because of shared state</li>
                <li>Can lead to verbose, tiring to read code</li>
            </ul>
        </td>
        <td>
            <ul>
                <li>If  you ever need to add new objects, you will need to modify many functions to account for the new case</li>
                <li>Can be memory intensive because objects are constantly being created whenever a state change is needed</li>
                <li>Because it is not read as line-by-line instructions, it can actually decrease readability for those not familiar with the syntax</li>
            </ul>
        </td>
    </tr>

</table>

## Examples

### Pure vs Impure Functions

#### Pure
For a given argument, a pure function will always return the same value. It does not depend on anything that can change (i.e. anything that is mutable). A pure function also does not have any side effects -- even thing as simple as logging to the console.
~~~
function multiplyByTwo(a) {
    return a * 2;
}
~~~

#### Impure
The following (admittedly quite contrived) function is impure for two reasons. The value that gets returned depends on an external mutable value, and it also has the side effect of logging to the console. It would also be impure if it changed the value of someNumber.
~~~
var someNumber = 2;
function multiplyGlobalAndLog(b) {
    console.log("Multiplying: " + b + " and " + someNumber)
    return b * someNumber;
}
~~~

### Imperative vs Declarative

#### Imperative
Using an imperative approach, your code describes *how to do something*. (i.e. "Loop through the length of this array, and for each item at a particular index multiply it by 3 and add it to this array.")

~~~
// triple the value of every element in a given array
const triple = (arr) => {
  let results = []
  for (let i = 0; i < arr.length; i++){
    results.push(arr[i] * 3)
  }
  return results
}

// sum all the elements in a given array
const sum = (arr) => {
  let result = 0
  for (let i = 0; i < arr.length; i++){
    result += arr[i]
  }
  return result
}
~~~
<span style="font-size: 0.8em;">Source: [Javascript and Functional Programming: An Introduction](https://hackernoon.com/javascript-and-functional-programming-an-introduction-286aa625e26d)</span>

#### Declarative
Using a declarative approach, your code describes *what you want to do*. (i.e. "Multiply each item in this array by 3.")

~~~
// triple the value of every item in a given array
const triple = (arr) => arr.map((currentItem) => currentItem * 3)

// sum all the elements in a given array
const sum = (arr) => arr.reduce((prev, current) => prev + current, 0)
~~~
<span style="font-size: 0.8em;">Source: [Javascript and Functional Programming: An Introduction](https://hackernoon.com/javascript-and-functional-programming-an-introduction-286aa625e26d)</span>

## Only the Surface
This is only a very shallow overview of a complex topic. Just on this topic alone, there's still a ton of information that I did not get to touch on -- and this is before I even properly try my hand at functional programming! As I learn more I'll update this post, or make a new one if the topic is deep enough to merit it.

## Sources

[Norman Ramsey, Green Harry (Stack Overflow)](https://stackoverflow.com/questions/2078978/functional-programming-vs-object-oriented-programming)

[Sho Miyata (Medium)](https://medium.com/@sho.miyata.1/the-object-oriented-programming-vs-functional-programming-debate-in-a-beginner-friendly-nutshell-24fb6f8625cc)

[Omer Goldberg (Hacker Noon)](https://hackernoon.com/javascript-and-functional-programming-an-introduction-286aa625e26d)

[Alex Holderness (Hacker Noon)](https://hackernoon.com/functional-programming-what-language-should-you-be-talking-313dd8bc379b)

[Chris Hokamp (Quora)](https://www.quora.com/What-are-the-advantages-of-functional-programming-over-object-oriented-programming-What-are-some-languages-that-are-mainly-functional)

[Darrick Mckirnan (Medium)](https://medium.com/@darrickmckirnan/object-oriented-programming-oop-functional-programming-what-are-they-the-pros-and-cons-11f98a971e38)