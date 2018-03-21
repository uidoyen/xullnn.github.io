---
title:  "What's the difference between unit, integration, and functional tests"
categories: [Test ℗]
tags: [Rails Tests]
---

[测试](https://en.wikipedia.org/wiki/Software_testing)可以分为：单元测试，集成测试，功能测试... 这些术语读慢一点就会发现相互之间有语义重叠。

[Rails guides 中关于测试的章节导言中提到:](http://guides.rubyonrails.org/testing.html)

> How to write unit, functional, integration, and system tests for your application.

如何区分这类术语？

先看了下Wikipedia上各个术语的定义:

- 1.[Unit testing](https://en.wikipedia.org/wiki/Unit_testing)
- 2.[Integration testing](https://en.wikipedia.org/wiki/Integration_testing)
- 3.[Functional testing](https://en.wikipedia.org/wiki/Functional_testing)

说的相对宽泛，且又牵涉到更多的测试术语，不好理解。找到两个stackoverflow上的高分回答：

1.[回答一(+1220票)](https://stackoverflow.com/questions/4904096/whats-the-difference-between-unit-functional-acceptance-and-integration-test)
2.[回答二(+441票)](https://stackoverflow.com/questions/5357601/whats-the-difference-between-unit-tests-and-integration-tests)

综合一下

### 1 Unit testing

Unit Testing 单元测试即测试程式中最小功能单位的测试，比如一个method。它应该
- 针对具体的功能点
- 测试过程在内存中就可以完成不会动到数据库
- 不需要找其他开发者配合
- 不需要接入实际网络连接
- 不会动到文件系统
- 不会新开线程
- 等等...

核心原则是尽量小，容易debug，可靠性高（因为排除了外部因素干扰），执行速度快，能在你将小功能整合之前测试程式中的最小单元是否正确工作。

单元测试的缺点(或说使用范围的限制)是，当这些独立的小测试单独测试时一切正常，但被测试到的小功能串起来的时候就可能出问题。这就引出了集成测试，Integration testing。

### 2 Integration testing

Integration testing 集成测试即测试多个功能单元联合工作时是否正常，或说对app中重要的workflow的测试。

集成测试和单元测试的一个重要区别在于测试时的环境，前面提到单元测试要尽量小，除了自身不动用其他资源。但集成测试则要求尽量模拟真实的 production 环境，应该尽量用到在生产环境中会用到的资源，比如：

- 线程
- 进入数据库读写
- 用到(实际会需要的)硬盘，或者其他硬件

核心原则是模拟尽量贴近生产环境的测试条件。

集成测试的优点是可以测试到单元测试不能测试到的内容，单元测试聚焦于点，集成测试可以覆盖到点与点之间的联系与互动，也能测试到单元测试不能测试到的environment存在的问题。集成测试的缺点是代码会比较冗长，'集成'带来的可靠性下降，以及不容易debug，也不那么好维护。

### 3 Functional testing

Functional testing 功能测试通常是针对特定功能的测试，测试范围介于单元测试和集成测试之间。但重要的区别是，功能测试只看重结果，即特定的输入是否能得到想要的结果，而过程怎么样则不关心。

From wikipedia:

> Functional testing is a quality assurance (QA) process and a type of **black-box** testing that bases its test cases on the specifications of the software component under test. Functions are tested by feeding them input and examining the output, and internal program structure is rarely considered (unlike white-box testing). Functional testing usually describes **what the system does**.

类似有一个复杂的算法 algorithm_x(a,b)，我只关心(测试)传入数据后是否能拿到想要的结果，置于内部实现多复杂则不管。

#### 另外还提到 Acceptance testing 验收测试

先看wiki上滤去编程背景的定义:

> In engineering and its various subdisciplines, acceptance testing is a test conducted to determine if the requirements of a specification or contract are met. It may involve chemical tests, physical tests, or performance tests.

在工程学以及其不同的子学科中，验收测试是用来验证一项工作(工程)是否符合约定规格或合同要求的侧四。可能包含化学、物理测试，或者表现(性能)测试。

验收测试应该有一个 **验收标准**，对于商业合同，这个标准可能相对明确。但对于程序开发，标准可能更侧重于过程约束，以及对预期结果的一个定义。这类验收涉及到的人员可能就不止该项目的开发人员，可能还包括其他开发人员，以及参与项目的其他非开发人员。验收测试还可能基于user story来模拟使用场景，跟着用户行为动线使用app，看是否符合当初story想要的结果。当然由于验收主体不同，可能使用到的方法不同。
