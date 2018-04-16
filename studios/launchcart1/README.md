---
title: "Studio: LaunchCart Part 1"
---

This activity gives you practice writing integration tests in Spring.

## Getting Ready

Fork the [LaunchCodeTraining/launchcart repo](https://gitlab.com/LaunchCodeTraining/launchcart) and clone it to your laptop. For this project we will start with the `master` branch. After forking and cloning, it's a good idea to make a *feature branch* to contain your work. Example: `git checkout -b day3-studio` creates 
a branch named *day3-studio* and checks it out at the same time.

## Your Tasks

Import the project into IntelliJ and make sure all of the tests pass.

This application is a very basic shopping cart. It has items that can be added to a cart. We can also currently add new items. There are no users, there is only one universal cart, and other basic functionality is missing.

You will add features, writing integration and unit tests as you go, and using a TDD approach.

### Add Price To Items Listing

The page at `/item` that lists all items does not currently list the price of each item. Add this feature.

Before you add code to the application, write a test to verify that item prices are displayed.

After you have written your test and the corresponding code passes, run all of the tests and commit.

### Add Total To Cart

The `Cart` class currently does not have the ability to produce a total cost of all items within a cart.

Add a `computeTotal` method to `Cart`, writing a unit test to verify its behavior before adding any code to the class itself.

Display the cart total at `/cart`. Before adding code to the controller and/or view, write an integration test to verify that the total is displayed properly.

After you have written your test(s) and the corresponding code passes, run all of the tests and commit.

<aside class="aside-note" markdown="1">
Don't worry about formatting the total as currency (i.e. with exactly 2 decimal places) when displaying it in the view.
</aside>

### Add Ability To Remove Items From The Cart

It is currently not possible to remove an item from the cart. Add this ability. There are different ways that you might do this, and we leave the implementation up to you.

As always, write your tests before writing any application code. As before, you'll have both unit and integration tests, since you'll need to add modify the `Cart` model class (to add a `removeItem` method) as well as add functionality to the web app.

After you have written your tests and they pass, run *all* of the tests and commit.

At this point, you may [turn in your work](#turning-in-your-work) before continuing on to the Bonus Mission.

### Implement Unit Test for ItemMemoryRepositoryTests
The previous programmer left a `TODO` in the unit test `/launchcart/src/test/java/org/launchcode/launchcart/data/ItemMemoryRepositoryTests.java.testAddItems()`. Please write this test so that 
we can be sure our repository class correctly saves items.

<aside class="aside-note" markdown="1">
This version of LaunchCart does not use an SQL database. Use this test as a way to get familair with how Items and Carts are persisted for this project.
</aside>

## Bonus Missions
1. How long is the data persisted? What makes the data disappear?
2. Don’t allow Add to Cart button to be clicked unless an item is selected
3. Don’t let two items with the same name be stored

## Turning In Your Work

If you don't complete each of the tasks, turn in as much as you have completed by the end of the day. *You should make sure you have working code committed to your fork.*

- Commit and push your work to GitLab
- Notify the instructor that you are done