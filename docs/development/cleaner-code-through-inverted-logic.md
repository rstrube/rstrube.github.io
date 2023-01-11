---
title: 'Cleaner Code Through Inverted Logic'
tags: ['development', 'csharp']
date: 2023-01-10
---
# Cleaner Code Through Inverted Logic {: .primaryHeading }
<small>Last updated: 2023-01-10</small>
{: .primaryDate }

---

## Introduction
One of the best ways to improve the readability and management of your code is to invert your conditional logic.  Sometimes I refer to this as "putting your short-circuit conditions up front".  This takes a little while to get used to, as we're more inclined to think of our logic through the lens of a "happy-path" flow (e.g. what are the conditions to continue) rather than the conditions to halt executing or return an error.

It also doesn't help that many CS professors preach "You should only a single return statement" for a given method.  This single manta has caused an egregious amount of ugly code to be created!

Over the past 10 years or so, more developers are embracing logic inversion.  [Resharper](https://www.jetbrains.com/help/resharper/InvertIf.html) even has a built code inspection tool that will handle performing this refactor for you!

To better illustrate this concept lets take a look at some code examples.

!!! note

    All of the examples provided here are written in C#, but the concepts can apply to virtually any language.  I often use similar approaches for Javascript, etc.
    
First let's define a plain old class (POCO) that contains a few basic properties.  This class will be used in all the subsequent code examples:

!!! example "Example POCO used in subsequent code examples"

    ```csharp
    public class MyItem
    {
        public int Id { get; set; }
        public bool IsActive { get; set; }
        public string? Value { get; set; }
    }
    ```

Let's also define some very simple business logic that we'll be required to implement in a service.  The service should contain a method with the following specifications:

- [x] Method input arguments are a `List<MyItem>` and a string value:
    - [x] If the string value is null or empty, throw an argument exception
    - [x] If the list is null or empty return null 

- [x] Method logic should attempt to match and return a **single** item from the list that:
    - [x] Is active
    - [x] Has a value == to the string input value
    - [x] If more than one item is active and matches the string input value, throw an exception
    - [x] If no items match, return null 

!!! note

    I realize this business logic is a little ridiculous.  Don't focus too much on the business logic, but rather the way we implement it.

## Traditional "Happy-Path" Logic Flow Example

This first example shows a more traditional "happy-path" logic flow.  It follow the rule of only having a single `return`.

!!! warning

    I personally consider the readability of this code to be quite poor and difficult to follow.  I'm only presenting this an an example of the kind of code **you should avoid writing**!

!!! example "Method using traditional happy-path logic flow, please avoid at all costs"

    ```csharp
    public MyItem? GetItemNestedLogic(List<MyItem> items, string valueToMatch)
    {
        MyItem? matchedItem = null;

        if(!string.IsNullOrEmpty(valueToMatch))
        {
            if(items != null && items.Count > 0)
            {
                foreach(var item in items)
                {
                    if(item.IsActive)
                    {
                        if(item.Value == valueToMatch)
                        {
                            if(matchedItem == null)
                                matchedItem = item;

                            else
                                throw new Exception("More than one item matches value");
                        }
                    }

                    else
                    {
                        // log warning about item not being active
                        // ...
                    }
                }
            }

            return matchedItem;
        }

        else
            throw new ArgumentException("valueToMatch");
    }
    ```

Unfortunately, I still encounter code like this all the time.  There are a number of problems with this code:

* Significant amount of if/else nesting
* Can be difficult to match the error actions (in the else blocks) with the corresponding if blocks
* Heavily dependent on indentation to read the code
* Need to scroll down to the bottom of the method to see that the method actually throws an exception when `valueToMatch` is null or empty

## Inverted Logic Flow Example (without Linq)

This second example shows how to invert your logic for cleaner, more readable code.

!!! note

    I have deliberately chosen not to use Linq in this example to properly demonstrate how to invert the existing logic of the previous example!

!!! example "Method using inverted logic (without Linq)"

    ```csharp
    public MyItem? GetItemInvertedWithoutLinq(List<MyItem> items, string valueToMatch)
    {
        // put all the short-circuit conditions up front
        if(string.IsNullOrEmpty(valueToMatch))
            throw new ArgumentException("valueToMatch");

        if(items == null || items.Count < 0)
            return null;
        
        MyItem matchedItem = null;

        foreach(var item in items)
        {
            // put all the short-circuit conditions to skip iteration up front
            if(!item.IsActive)
            {
                // log warning about item not being active
                // ...
                continue;
            }
            
            if(item.Value != valueToMatch)
                continue;

            if(matchedItem != null)
                throw new Exception("More than one item matches value");

            matchedItem = item;
        }

        return matchedItem;
    }
    ```

In my opinion this method is much easier to read.  You can scan through the method and see all the situations that would cause error conditions very easily.  You also don't have the deep level of nesting that was present in the previous example.

## Inverted Logic with Linq

This example uses inverted logic for some initial short-circuit conditions, but also relies heavily upon Linq to do the iteration and matching.

!!! example "Method using inverted logic and Linq for matching"

    ```csharp
    public MyItem? GetItemInvertedLinq(List<MyItem> items, string valueToMatch)
    {
        if(string.IsNullOrEmpty(valueToMatch))
            throw new ArgumentException("valueToMatch");

        if(items == null || items.Count < 0)
            return null;
        
        return items.Where(item => item.IsActive && item.Value == valueToMatch).SingleOrDefault();
    }
    ```

    !!! info

        The `SingleOrDefault()` Linq method will return `null` if the `IEnumerable` is empty, and it will throw an exception if the `IEnumerable` contains more than one item.

!!! note

    The eagle-eyed reader would notice that this example forgoes the ability to log when an item is not active.  I've chosen to omit this for now to demonstrate how concise Linq code can be.

## Conclusion

I hope the provided examples demonstrate how inverted logic can help make your code cleaner and easier to read and maintain.  We really need to have a talk with the CS professors that are stuck twenty years in the past!