---
layout: post
title: 开发人员应该测试自己的代码
category : skill
tags : [testing]
---

由于在上一家外包公司有专职QA，导致我写程序只是简单的做了“干净测试”，从来没有对自己的代码做过系统的测试。于是，
在这家没有QA的公司，我负责一个抽奖小活动，抱着用后弃之的态度，写完后没有仔细测试，导致放到线上出现了问题（
奖品总数限制漏洞，后台配置有临界值导致的死循环）。


之前有专职QA的时候，他们也只是测试需求中的功能是否完成，设计中的关注点是否完成，其实这应该是我们开发人员的事情。


作为一个梦想成为靠谱程序员的我，不能只coding，要测试，要对自己的代码质量负责，没人给你擦PP。


扩展阅读：[我们需要专职的QA吗？](http://coolshell.cn/articles/6994.html)


摘录：开发人员应该测试自己的代码。没什么可说的。背后的道理并不重要。这包括单元测试，全覆盖的自动化测试或手工测试或组合测试。如果你的开发人员不能/不愿意或认为这“不归我管”，那你需要更好的程序员。


##我出现过的bug##


1. while循环没有测试条件判断导致的死循环。