---
title: git commit 基本规范
tags:
  - git

date: 2017-06-06 21:45:18
---

> 简单介绍下git commit的规范。

我们每次进行git提交的时候，git commit都包括三个部分：
- **header**
- **body**
- **footer**

header是必须的,body和footer经常可以省略

***
#### Header

header部分就只有一行(***不超过72个字符***), 包括三个字段``type``(必须)，``scope``(可选)，``subject``(必须)
``` javascript
<type>(<scope>): <subject>
```

- **type**:
``` javascript
feat：    增加新功能（feature）
fix：     修补bug
docs：    仅仅修改了文档，比如说README,CHANGELOG等等
style：   修改了空格，格式缩进，样式格式（不改变代码逻辑的变动）
refactor：重构（即不是新增功能，也不是修改bug的代码变动）
test：    增加测试，包括单元测试等
perf：    优化相关，比如提升了性能，体验等
chore：   构建过程或辅助工具的变动
revert：  回滚到上一个版本
```
- **scope**:
scope用作说明commit的影响范围。
- **subject**:
subject是对commit的简短说明。
```
以动词开头
第一个字母小写
结尾没有句号
```