# Flacky or not Flacky? Say goodbye to flackyness

This is an opiniated summary on how to write tests that are not flacky.
In other words say goodbye to the flakyness.

## Current situation

A test is said to be flacky when it does not behave consistently across platform or even across executions on the same machine.

I frequently here that kind of things:

```text
This test is flaky: it runs on my machine but it randomly fails on the CI or in the docker container 
```

This means that once you have written an e2e test, this test has a self owned bipolar personality: green on the good days, red on the bad days. And, like Doctor House, you are trying to figure out what is happening.

I myself faced such situation where the e2e tests were green on my machine but randomly red on the CI environment.

It took me a lot of time to have stable tests being always green on all CI environments.


## The recipe

Given a flacky test, I discover that more code has always to be added to make the test pass and say goodby to flakyness.

Let's start with a trivial example:

```js
await t
    .typetext('#developer-name',"John Doe")
```

Believe me or not, if you think the above line is enough to enter text in an input field, you will have to struggle all your life with the flackyness of your tests.

Do you think a user is able to enter some text in a specific input field just by directly typing the text on the keyboard once he has entered the url in the browser?   
Just try it. You may succeed, but you will fail very often.

Now, try to figure out what a real user has to do in order to type some text in a field. 

A typical behavior is:

* Do I see this field on the screen? (page is loaded?)
* Can I type something in this field (field is disabled?)
* If the field is disabled, wait until the field is enabled (how much time should I wait?)
* Move the mouse over the field (access to the field via the mouse, but it could be via the keyboard)
* Click in the field (a pre-requisite in order to be able to start typing)
* Is there existing content? (could the field be already populated?)
* Remove existing content
* Type the text
* Last but not least: validate the typed content by navigating to another field or by clicking on a submit button (by using the same above strategy).

Writing e2e tests is not about coding, it's only about simulating the real behavior of your users. 

If you code your tests by using technical shortcuts your tests will always be flacky at one time or another. 

Typing a text in a field should always be as boilerplate as the following sequence:

```js
await ensure_form_is_loaded() // custom method specific to your App
const selector = Selector('input#developer-name[type="text"]') // be the most specific possible to increase the search speed  
    .with({visibilityCheck: true}) // explicitely search for a visible selector
    .nth(0); // a Selector is always a collection of objects. Be explicit and always take the first found
await t
    .expect(selector.exists).ok({timeout: 5000}) // ensure the input field is visible on the screen
    .hover(selector) // access to the field via the mouse
    .expect(selector.hasAttribute("disabled")).notOk({timeout: 5000}) // ensure the field is enabled
    .click(selector) // give focus to the field
    .setTestSpeed(0.7) // set the user's speed: start at 0.7, should be 1 on well coded page
    .typeText(input, "john doe", {replace: true}) // replace existing content if any
    .pressKey("tab") // navigate to next input field in order to automatically trigger any validation mechanism
```

In short, your test code should never do what a real user cannot do: a user will never be able to type text in a field in just one operation.

Your test should always simulate the real behavior of a real user. 

Any time you will take a technical shortcut to make your code more brilliant you will introduce flakyness.


## Asserting a property selector with ok(), notOk(), eql(), etc ...

Look at this code:

```js
await t.expect(selector.exists).ok(); // check that selector exists
await t.expect(selector.value).eql('some value'); // check that selector has an expected value
await t.expect(selector.count).eql(N); // check that selector is a collection of N items
```

All those assertions are flaky. 

They will work on your dev machine, but there is a good chance they will fail at one time or another when executed in a container or on a remote agent.

TestCafe has a great [assertion mechanism](http://devexpress.github.io/testcafe/documentation/test-api/assertions/#smart-assertion-query-mechanism).

But if you do not provide explicitely a timeout in the `ok()`, `eql()`, `notOk()`, it uses the timeout specified using the runner.run API method or the assertion-timeout command line option. 

If you do nothing, the default is three seconds.

These 3 seconds are enough on a powerfull dev machine, but definitely not when running on CI or in a container because the CI or the container may be under high-cpu pressure/low-memory pressure/slow-network pressure either because it is a shared ressource with other tenants or because the machine is not scaled enough for its memory, its cpu or its network bandwith.

The above assertions should always be rewritten like this:

```js
const longTimeout = 20000; // 20 seconds
await t.expect(selector.exists).ok({timeout: longTimeout}); // check that selector exists
await t.expect(selector.value).eql('some value', {timeout: longTimeout}); // check that selector has an expected value
await t.expect(selector.count).eql(N, {timeout: longTimeout}); // check that selector is a collection of N items
```

[This is a work in progress ... obviously this page is not finished]