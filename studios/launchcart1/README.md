---
title: "Studio: LaunchCart Part 1"
---

This activity gives you practice writing integration tests in Spring.

## Getting Ready

Fork the [LaunchCodeTraining/launchcart repo](https://gitlab.com/LaunchCodeTraining/launchcart) and clone it to your laptop. Import the project into IntelliJ and make sure all of the tests pass.

## Your Tasks

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

## Bonus Mission

Another basic feature that is missing is the ability to add some quantity of items (greater than 1) to the cart. To do this, we need to introduce a new model class, `CartItem`.

<aside class="aside-pro-tip" markdown="1">
Before embarking on this mission, create a feature branch to isolate your work. Aside from being a best-practice, this will also keep your `master` branch nice and clean in the event you don't finish the mission. This will be helpful since we'll continue working on this app in a future studio.
</aside>

Create a `CartItem` class with fields `item` and `quantity`, and refactor `Cart` to store a collection of `CartItem` objects instead of `Item` objects. As `Cart` and `Item` have done, you should extend `AbstractEntity` to get the common identifier configuration contained in that base class.

Start by refactoring the model:
- Add the new model class
- Re/write tests in `TestCart` as necessary
- Refactor `Cart` to use `CartItem`

Once you have a working model that uses `CartItem`, run *all* of your tests, including the integration tests. You'll have some work to do here, since changing the model will break parts of the controller and view.

<aside class="aside-hint" markdown="1">
You may experience a situation where your integration tests fail because a new item that is seemingly added to the cart isn't actually there when viewing `/cart`. If this is the case, it's likely that Hibernate isn't persisting your new `CartItem` instances. These objects are never handled directly by the controller, and thus never saved via a repository.

You can force Hibernate to save new `CartItem` objects in all situations by adding a cascade property to the `@OneToMany` collection storing `CartItem` objects:
```java
@OneToMany(cascade = { CascadeType.ALL })
```
</aside>

After your model has been refactored, and all of your tests pass, you'll need to refactor the controller and view layers heavily to get this to work. This will include adding functionality that allows the user to enter a quantity when adding an item to the cart.

As always, write your tests first!

## Turning In Your Work

If you don't complete each of the tasks, turn in as much as you have completed by the end of the day. *You should make sure you have working code committed to your fork.*

- Commit and push your work to GitLab
- Notify the instructor that you are done