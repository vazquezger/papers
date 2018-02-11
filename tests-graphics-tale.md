# Unit, Integration and End2End tests: A Graphical Tale.

**Unit tests**.  
Tests logical units of work. You get a logical unit of work (a class, a function, a module, a file), mock all dependencies and write tests that covers most branches of code. Unit tests run in a super controlled environment.

**Integration tests**.   
ITs exercise the system under test with all its modules combined and tested as a group.
Downstream dependencies are mocked.
Owned dependencies are not mocked, whenever you can. If available you can hit the downstream dependency using a docker or a driver that mimics the actual service like miniredis, or sqllite.

**End to end tests**.   
E2Es exercise the system hitting actual dependencies, nothing is mocked.

## Numbers of tests per type
![](https://github.com/vazquezger/papers/blob/master/figures/testsgraphicstale_numoftests.png)

## Test value and diminishing returns
Diminishing returns is observed on a per type basis (not as an agregation of all test types)
![](https://github.com/vazquezger/papers/blob/master/figures/testsgraphicstale_testvalue.png)

## Test coverage per type
![](https://github.com/vazquezger/papers/blob/master/figures/testsgraphicstale_codecoverage.png)

## Aggregated coverage
![](https://github.com/vazquezger/papers/blob/master/figures/testsgraphicstale_agegatedcodecoverage.png)


