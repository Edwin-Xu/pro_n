# Problems And Solutions

## 编程常见问题

### 圈复杂度

衡量代码复杂度, 体现在

- 分支数量
- 行数
- 单元测试数量

  构建流程图，圈复杂度 = 边 + 2 - 顶点，但是计算太复杂。

  于是简化为计算关键字：if while do for switch case || && ?: catch的数量：

  圈复杂度 = 关键字出现次数 + 1；



于是引入 认知复杂度



### 无效代码

也许没有人知道他的意义，但是不甘删，于是造成冗余并累计

- 无效的import
- 变量、属性 

### 异常处理

- 不要抛出通用异常
- 不要打印异常栈

 ### 对象比较

==、!=，如果equals可以使用则不用



### 魔术数字

整理为常量，使用命令说明

### 分页查询



### Final Class

如果构造函数全部是私有的，那么意味着不可能被继承，类也应该定义为final

### 日志打印

打印本身有不小的性能消耗，高QPS只打印必要的信息



### 代码坏味道

- 重复代码
- 过长方法
- 过大类
- 过长参数列表
- 发散式变化
- 散弹式修改
- 依恋情结
- 数据泥团
- 基本类型偏执
- switch惊悚现身
- 平行继承体系
- 过多的注释
- 被拒绝的馈赠
- 相似的类
- 中间人
- 过度耦合的消息链
- ......



如何避免：

- 人工检查
- 系统发现
  - IDEA
  - Sonar Check
  - Find bug & Checkstyle
  - junit
- 流程制度约束 
  - DEV自测代码CASE
  - DEV TL Code REVIEW
  - QA Code diff 和质量检测
  - 发布过程观察监控和日志

## Java Web开发常见问题

### XSS

Cross-site scripting

跨站脚本攻击



### CSRF

Cross-site request forgery

跨站请求伪造



### 注入安全

#### SQL注入



#### EL表达式注入





















