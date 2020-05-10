---
title: 'Introduction to Mutation Testing'
url: '/blog/introduction_to_mutation_testing'
date: Sat, 09 May 2020 19:00:00 +0000
draft: false
tags: [test-automation,quality,mutation-testing]
featured: true
toc: true
summary: "Why slaying mutants is good for measuring the quality of your tests"
---
# Mutation Testing

*Why slaying mutants is good for measuring the quality of your tests.*

## The problem: assessing test quality

Test automation of a piece of software is a well developed practice these day. But assessing the quality of those tests is not easy. One really big mistake is to rely on coverage because you can have 100% (or close to) coverage but still having really low test quality.

Say you have a beautiful function `foo` defined as

```js
/* Return the answer to life, the universe and everything */
function foo(){
	return 42;
}
```


And your test looks like:

```js
// very basic version of a test, hopefully very far from a real-life example
resultFoo = foo();
assert(true, 'result of foo should be 42');
```

If you run the test with your favourite test runner, it will pass! (obviously because we passed `true` in the `assert` function call) You get the greenlight. Even sadder, you also get a 100% coverage for this function because we called it in the test (even if we don't use the result of that call to determine if the test pass or not).

Of course this is a contrived example to put emphasis on my point above that coverage is not a reliable metric for code quality. (At the bottom of the article I link to a Kata revolving exactly around this idea of false coverage, and about fixing bad tests)

## A solution: mutation testing

One way to get more confidence into you test suite is to use Mutation testing. The way it works is by changing a tiny bit of your code, say replace `==` by `!=` in one place in your code, then run your test suite against the modified version of your code.

This new version of your code is called a *mutant*. If all your tests pass on the mutant, it means your test quality is not good enough because it did not catch this change. In this case the *mutant* is sometimes called a *zombie* because it lives (did not get 'killed' by the test suite). 

Then you repeat the process of introducing another mutation from the original code to create another *mutant* (say this time you replace `let c = new Foo()` by  `let c = null`) then you run the test suite again and you determine if the mutant has been killed.

Rinse and repeat a lot, then you count how many mutant you killed in relation to how many were produced. This ratio is called the mutation score, and should be as close to 1 as possible. Mutation testing allows to be more confident in the test you write.

## Mutation operator

The changes in the code (replacing `==` by `!=` like in the first example)  are called *mutations*, and they are defined by *mutation operator*. There are many different families of *mutation operator*.

You have operator on (non-exhaustive list):

- boolean: `==` and `!=`, `||` and `&&`, `<` with `<=`, `>` and `>=` 
- arithmetic: `*` with `+`, `-` and `/`
- statement deletion
- statement duplication
- OOP: replacing call for object constructor by `null`, changing method and field scope
- operator specialized for specific features of a programming language (like C++) [^1]
- operators for security purposes[^2]

## Pitfalls

Of course it's not all good, if it was we'd be using it in every project since it was invented (in the 70's!)

### Instability of the mutations

Because we mutate pieces of code, it happens that mutant causes a crash or an infinite loop (when say you change the condition on a loop) and that hinders the tests.

Some mutation testing framework deal with that by letting you disable some classes of mutations operator if it does not play well with your codebase. 

In most cases you get a timeout for that mutant, so you don't get a clear result. And it can slow down considerably your mutation testing run.

### Performance

Another negative thing (and, from what I understand, until recently the major drawback that prevented spreading of mutation testing) is that for even a small code base you can generate hundred or thousands of mutants. Then you need to tests every one of them. That's a huge resource sink, that could only be overcome for small projects or medium-big projects with huge resources. (Remember the idea was first devised in the 70's)

Now we have much more resources at hand so it is less a problem but still, that can quickly add time to your test pipeline (especially if your test suite is slow). To help the performance aspect, there are numerous optimizations that have been devised, like only generating mutation for line of code that are covered by at least a test, testing the mutants by running only the specifics tests that covers the line with the mutation instead of the whole test suite etc. This make using mutation testing a possibility.

Another option is to use extreme mutation (the paper 'Will My Tests Tell Me If I Break This Code?'[^3] is a good read). Basically extreme mutation will remove any code from a tested function, and replace it with a value of the type of the method.

This generates less mutant so is quicker to test. You can find [here](https://github.com/STAMP-project/pitest-descartes/blob/master/docs/performance-comparison.md) a comparison of regular mutation vs extreme mutation testing of various java library with the framework Pit using a standard generator (called Gregor) and a extreme mutation generator (called Descartes).

Below you can find the result of that comparison:


|                                                                         | Descartes |         | Gregor   |         |
|-------------------------------------------------------------------------|-----------|---------|----------|---------|
| Project                                                                 | Time      | Mutants | Time     | Mutants |
| [authzforce](https://github.com/authzforce/core.git)                    | 0:08:00   |    626  |  1:23:50 |    7296 |
| [aws-sdk-java](https://github.com/aws/aws-sdk-java)                     | 1:32:23   | 161758  |  6:11:22 | 2141689 |
| [commons-cli](https://github.com/apache/commons-cli)                    | 0:00:13   |    271  |  0:01:26 |    2560 |
| [commons-codec](https://github.com/apache/commons-codec)                | 0:02:02   |    979  |  0:07:57 |    9233 |
| [commons-collections](https://github.com/apache/commons-collections)     | 0:01:41   |   3558  |  0:05:41 |   20394 |
| [commons-io](https://github.com/apache/commons-io)                      | 0:02:16   |   1164  |  0:12:48 |    8809 |
| [commons-lang](https://github.com/apache/commons-lang)                  | 0:02:07   |   3872  |  0:21:02 |   30361 |
| [flink-core](https://github.com/apache/flink/tree/master/flink-core)    | 0:14:04   |   4935  |  2:29:45 |   43619 |
| [gson](https://github.com/google/gson)                                  | 0:01:08   |    848  |  0:05:34 |    7353 |
| [imagej-common](https://github.com/imagej/imagej-common)                | 0:08:07   |   1947  |  0:29:09 |   15592 |
| [jaxen](https://github.com/jaxen-xpath/jaxen)                           | 0:01:31   |   1252  |  0:24:40 |   12210 |
| [jfreechart](https://github.com/jfree/jfreechart)                       | 0:05:48   |   7210  |  0:41:28 |   89592 |
| [jgit](https://github.com/eclipse/jgit)                                 | 1:30:08   |   7152  | 16:02:03 |   78316 |
| [joda-time](https://github.com/JodaOrg/joda-time)                       | 0:03:39   |   4525  |  0:16:32 |   31233 |
| [jopt-simple](https://github.com/jopt-simple/jopt-simple)               | 0:00:37   |    412  |  0:01:36 |    2271 |
| [jsoup](https://github.com/jhy/jsoup)                                   | 0:02:43   |   1566  |  0:12:49 |   14054 |
| [sat4j-core](https://github.com/apache/pdfbox)                          | 0:53:09   |   2304  | 10:55:50 |   17163 |
| [pdfbox](https://gitlab.ow2.org/sat4j/sat4j/tree/master/org.sat4j.core) | 0:44:07   |   7559  |  6:20:25 |   79763 |
| [scifio](https://github.com/scifio/scifio)                              | 0:24:14   |   3627  |  3:12:11 |   62768 |
| [spoon](https://github.com/INRIA/spoon)                                 | 2:24:55   |   4713  | 56:47:57 |   43916 |
| [urbanairship](https://github.com/urbanairship/java-library)            | 0:07:25   |   3082  |  0:11:31 |   17345 |
| [xwiki-rendering](https://github.com/xwiki/xwiki-rendering)             | 0:10:56   |   5534  |  2:07:19 |  112605 |



You can see that going from extreme mutation to standard mutation testing is roughly an order of magnitude in number of mutant generated and time taken to test every mutant.

## Conclusion

Try it sometime, for me it was really fun and eye opening to catch bad tests I wrote!

And remember:

*Software testing proves the existence of bugs not their absence.*

### Bonus: resources to dig deeper

- The original paper that first described mutation testing: [R. A. DeMillo, R. J. Lipton and F. G. Sayward, "Hints on Test Data Selection: Help for the Practicing Programmer," in *Computer*, vol. 11, no. 4, pp. 34-41, April 1978, doi: 10.1109/C-M.1978.218136.](https://www.researchgate.net/publication/2957629_Hints_on_Test_Data_Selection_Help_for_the_Practicing_Programmer)
- If you are interesting to know a bit more about different techniques and different tradeoffs to implement mutation testing, the [PIT](https://pitest.org/) library (Java) has a good overview on [mutant generation](https://pitest.org/java_mutation_testing_systems/#mutant-generation), [test selection](https://pitest.org/java_mutation_testing_systems/#test-selection), [mutant insertion](https://pitest.org/java_mutation_testing_systems/#mutant-insertion) and [mutant detection](https://pitest.org/java_mutation_testing_systems/#mutant-detection).
- A [Kata](https://github.com/vmzakharov/mutate-test-kata) (using Java) to see and test yourself the fact that coverage is not a reliable metric, and practicing hand-on to fix tests.
- The [English Wikipedia page](https://en.wikipedia.org/wiki/Mutation_testing) on mutation testing
- The Descartes engine [github page](https://github.com/STAMP-project/pitest-descartes) (the engine implementing extreme mutation testing)

[^1]: [Parsai, A., Demeyer, S., De Busser, S.: C++11/14 Mutation operators Based on Common Fault Patterns(2004)](https://arxiv.org/pdf/2004.04206.pdf)

[^2]: [Dadeau, F., Héam, P-C., Kheddam, R.: Mutation-Based Test Generation from Security Protocols in HLPSL(2011) in: 2011 Fourth IEEE International Conference on Software Testing, Verification and Validation](https://ieeexplore.ieee.org/abstract/document/5770613)

[^3]:  [Niedermayr, R., Jurgens, E., Wagner, S.: Will My Tests Tell Me If I Break This Code? (2016) in: Proceedings of the International Workshop on Continuous Software Evolution and Delivery (CSED ’16)](https://arxiv.org/pdf/1611.07163.pdf)
