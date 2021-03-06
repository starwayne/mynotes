我的理解：

软件产品 = 程序代码 + 软件工程

因此：

软件产品质量=程序代码质量 + 软件工程质量

上面两个维度我们开发都有参与，所以我们可以分别从这两个维度来考虑如何提升质量：

1、程序代码方面，这块我们是唯一责任人。所以需要注意以下一些问题：

（1）合理的架构设计，界面和数据业务逻辑尽可能分离，因为功能业务不容易变，而视效很容易变。否则不利于版本升级和项目移植。

（2）合理的命名规范和代码格式，我们知道源代码是给人看的，二进制才是给机器看的。为了便于团队人员理解，大家应该形成统一的命名规范和代码格式标准，其中命名规范可以通过组织培训宣贯和代码评审来保证，代码格式可以通过技术手段来保证：比如应用统一的格式化工具，在CI服务器上完成代码的自动统一格式化。

（3）不断重构也是保证代码质量的重要手段，随着代码的不断迭代，很多代码的坏味道都会冒出来，根据破窗效应，如果不持续投入重构去整理优化代码，代码质量会加速变得越来越糟糕。但由于我们缺乏下面要讲到的合适的单元测试工具，加上项目时间进度压力，导致重构做得很少。

（4）良好覆盖的单元测试用例代码既可以让我们在代码重构修改环节确保修改代码质量，也有助于促使大家设计实现低耦合的模块代码，同时也有助于其他同事对函数接口的用法理解。所以编写单元测试代码好处多多，而且目前Android平台目前已经有比较成熟完善的单元测试框架，只是我们一直因为懒惰习惯及担心拉长开发时间进度不愿意投入使用。在目前现状下，建议我们至少应对新增自研功能模块和已有关键功能模块编写单元测试。

2、软件工程方面，除了我们开发这边，还涉及到需求、视效交互设计、测试、项目管理等各个环节。根据木桶效应，其中任何一块出现短板都可能无法保证工程质量。我们开发这边在工程环节上能够做好的是：

（1）探索一些先进的组织开发方式：如测试驱动开发（先写测试代码，再写实现代码），结对编程。

（2）关键代码组织团队评审，疑难故障组织团队会诊，重点任务技术方案和时间计划团队制定。

（3）每周允许团队成员有不低于20%的时间脱离项目用于个人技能提升，如学习新语言，开发提升工作效率的小工具，并准备交流培训进行讨论分享。

（4）积极向项目管理团队反馈开发进展和寻求其它模块的协助支持，同需求及视效交互设计团队密切沟通确保代码设计实现符合预期，协助测试和构建团队参与自动化测试流程的实现。另外测试和视效走查故障定级合理，让开发能将精力优先集中到最需要处理的问题上。

世上没有免费的午餐，采取上面措施的同时肯定也会增加开发成本，特别是从短期来看更是如此。每个人的工作时间都是有限的，如果项目进度订得过于紧张，不合理需求任务压得太多，要保证这些质量提升措施能够落地就无从谈起，如何从中做好平衡取舍值得我们每个人思索。

