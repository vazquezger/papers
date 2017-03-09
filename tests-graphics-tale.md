# Unit, Integration and End2End tests: a graphical tale.

## Unit tests
Tests logical units of work.
You get a logical unit of work (a class, a function, a module, a file), mock all dependencies and write tests that covers most branches of code.
Unit tests run in a super controlled environment.

## Integration tests
ITs exercise the system under test with all its modules combined and tested as a group.
Downstream dependencies are mocked.
Owned dependencies are not mocked.

## End to end tests
E2Es exercise the system in combination with eternal dependencies. 
Nothing is mocked.

_Figure 1_ Numbers of tests per type and test coverage
![](https://github.com/vazquezger/papers/blob/master/figures/tests-num%20of%20tests.png)

_Figure 2_ Aggregated code coverage and test complexity
![](https://github.com/vazquezger/papers/blob/master/figures/tests-code%20coverage.png)

_Figure 3_ Test value and diminishong returns
![](https://github.com/vazquezger/papers/blob/master/figures/tests-diminishing-value.png)


