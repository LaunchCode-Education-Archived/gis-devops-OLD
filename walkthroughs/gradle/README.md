---
title: Gradle Walkthrough
---

In this activity, we'll create and customize a simple Gradle Java project using only the command line.

## Getting Familiar With Gradle

Create a project directory:

```nohighlight
$ mkdir gradle-walkthrough
$ cd gradle-walkthrough
```

Initialize the project as a Gradle project using the Java application template:

```nohighlight
$ gradle init --type java-application
```

Open the project in an editor and have a look around to see what Gradle created for you:

```nohighlight
$ code .
```

**Answer these questions:**

- What does the project directory structure look like?
- What classes are part of your project?
- Which plugins is your project using?
- What are your project's dependencies?
- Where will these dependencies be resolved from?
- What tasks are available to your project?

One of the tasks we have available is the `test` task. Let's run it:

```nohighlight
$ gradle test
```

Assuming that the tests pass, let's build the project:

```nohighlight
$ gradle build
```

When the build has successfully finished, locate the resulting `.class` files and the bundled JAR file.

<aside class="aside-hint" markdown="1">
The `build/` directory would be a smart place to begin your search.
</aside>

Let's run the app:

```nohighlight
$ gradle run
```

And now clean up all of the built files:

```nohighlight
$ gradle clean
```

**What was removed from the project?**

## Customizing Your Project

Let's carry out some basic customization of our simple Java project.

We want to be able to view our project tasks as a tree. We can do a similar thing with dependencies using `gradle dependencies` but no such task exists to display task relationships in tree form. Thankfully, somebody has written a plugin to do that!

Find the `com.dorongold.task-tree` plugin and enable it in your project. Then run `gradle tasks` to determine which task the plugin has added to allow us to view the task tree.

Once you've found the task, run it to see the task relationships for your project.

**Which tasks are executed when running the `build` task?**

Let's customize the way in which the JAR is built for our project. Suppose we wanted to be able to build all of the class files without bundling them into a JAR.

We can do this by adding a configuration block to the `jar` task. Add this to your `build.gradle` file:

```groovy
jar {
    onlyIf {
        System.properties['makeJar'] == 'true'
    }
}
```

Now, a JAR will be built only when we add the `-DmakeJar=true` flag to our Gradle command, like so:

```nohighlight
$ gradle -DmakeJar=true build
```

Try running `gradle build` without the flag and see that the class files were built, but not the JAR. Then run it with the flag and see that you now have a JAR. Note the name of the resulting JAR. What is it?

Finally, we can customize the name of our JAR by adding these properties to the `jar` configuration block:

```groovy
baseName = 'app'
version = '0.0.1-SNAPSHOT'
```

## Gradle in Sprint Boot Projects

With your newfound Gradle skills, go to the project root of one of your Spring Boot projects in the Terminal. Test, build, and run the app from the command line using Gradle.