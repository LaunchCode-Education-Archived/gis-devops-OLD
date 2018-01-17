---
title: "Studio: Test-Driven Development"
---

For this studio, you will create a class to represent [URLs](https://en.wikipedia.org/wiki/URL) as Java objects. You will do this using a test-driven approach.

## Getting Ready

Create a new Java project in IntelliJ named `tdd`. Use the Gradle project template. Initialize the project as a Git repository, then create a corresponding remote repository in your GitLab account. Connect your local to the remote, commit, and push.

Review terminology for the following URL components at [Wikipedia](https://en.wikipedia.org/wiki/URL): protocol, domain, path.

## Your Tasks

Create two classes:
- `src/main/java/Url.java`
- `src/test/java/TestUrl.java`

You will follow a test-driven approach to implement the requirements below. This means that for each requirement, you should write a test *before* writing any code for that requirement. 

Use the red-green-refactor workflow: 
- Write a failing test
- Write code to get the test to pass
- Review your code to see if it can be improved, and rerun your tests after each refactor

### Requirements

- There should be only one `Url` constructor, and it should take a string (e.g. `"https://launchcode.org/learn"`) and set `domain`, `protocol`, and `path` fields. Note that protocol and domain will not contain any `/` characters, but the path may.
- The three should be immutable. In other words, they should be `final` and have getters but not setters.
- The constructor should take mixed-case strings, but set fields as lowercase strings. For example, if passed `"HTTPS://LAUNCHCODE.ORG"` then calling `getDomain()` should return `"launchcode.org"`.
- `Url` should override `toString()`, which should return a properly-formatted version of the URL (e.g. `"https://launchcode.org/learn"`)
- The string passed to the constructor should satisfy each of the following. If it doesn't, then the constructor should throw an `IllegalArgumentException` with an appropriate message.
    - The protocol is one of: ftp, http, https, and file
    - The path may be empty, but domain and protocol must both be non-empty
    - The domain may only contain letters, numbers, `.`, `-`, or `_`

<aside class="aside-note" markdown="1">
These requirements are not comprehensive of how a URL may be structured, but they encompass a large number of URLs you use on a daily basis.
</aside>

## Bonus Missions

If you implement the above requirements with time to spare, attempt to implement these as well:
- Add port, query string, and fragment fields with reasonable validation for each
- Add support for IP addresses in place of domains

## Turning In Your Work

If you don't complete each of the tasks, turn in as much as you have completed by the end of the day.

- Commit and push your work to GitLab
- Notify the instructor that you are done

## Resources
- [Java Regex Docs](https://docs.oracle.com/javase/7/docs/api/java/util/regex/Pattern.html)
<aside class="aside-note" markdown="1">
The "intentions menu", Alt + Enter, in Intellij has a "Check Regex" option that brings up a Regular Expression tester. 
</aside>
