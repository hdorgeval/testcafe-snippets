# Flacky or not Flacky? Say goodbye to flackyness

This is an opiniated summary on how to write tests that are not flacky.
In other words say goodbye to the flakyness.

## Current situation

A test is said to be flacky when it does not behave consistently across platform or even across executions on the same machine.

I frequently here that kind of things:

```text
This test is flaky: it runs on my machine but it randomly fails on the CI or in the docker container 
```

This means that once you have written an e2e test, this test has a self owned bipolar personality: green on the good days, red on the bad days.

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


[This is a work in progress ... obviously this page is not finished]