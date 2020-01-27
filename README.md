# stock-ai
Self Learning AI system for automatic trading

## 开发思路
基于基因进化算法
将操盘策略的变量编码为基因对，分别对应上边界和下边界

### 条件表达组
条件组用稀疏数据表达
基础DNA序列 48种分组
环境条件
- 250趋势   [0,1]
- 10日趋势  [0,1]
- 5日趋势   [0,1]
- 250日价格分位 [0,1,2,3,4,5]

可扩展序列
触发条件
- 连续N日下跌 [1,2,3]
- 之前连续N日上涨
- 下跌幅度
- 5日的日均振幅

### 变量组
- 最大持仓天数 [1,7]
- 止赢点    [min: 0.5%,  max: 5.5%, step: 0.5%]
- 止损点    [min:-2.0%,  max: 0.0%, step: 0.5%]
- 回落卖出  [min:-1.0%,  max:-3%,   step:0.5%]
- 连续红卖出 [1,2,3,4]

### 评估
- 样本命中次数
- 胜率
- 收益率平均数
- 收益率中位数
- 损失中位数 （这个参数用于决定补仓点位）

## 问题难度
### 如果动态扩展基因序列的长度
根据信息熵决定

### 怎么学习，这么多序列用枚举么？
先考虑用枚举，即在每种情况下的最佳操盘策略

### 怎么分组呢？
按目标样本的计划数量，随机从股票中找到用于训练的样本数量，建议每种策略至少测试300+组随机样本

## 机器学习流程
* 随机选择几组股票
* 对选择出来的股票提取特征
* 分别比较同一个策略在不同样本集合组中的表现，

保存进度/继续学习 累加经验

## 智能学习框架
### 目标
回合胜率最大化
### 则时
通过基因算法推算出根据随机股票的环境策略最优解
### 选股
- 按历史回测胜率排序 稀疏分位数值
- 按股价的价位排序
### 调仓逻辑
根据损失中位数决定每次触发时候的仓位设置

### 估算压力位和支撑位并配合力度
其实还是判断每一个下一日的涨跌概率
如果持仓且有盈利则持仓观望
回落止损

## 代码结构
lib.KnowledgeBase
  - load
  - save

lib.DataSource
  - preprocess
  - loadDataforGALearner
  - loadDataforBacktest

lib.DynamicPositionProcessor

lib.GALearner

2% 严格止损
试探仓小于5% 主力仓15% 准主力仓 备用仓
50%以上才加仓
