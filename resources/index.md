# 精选资料与核验记录

[返回首页](../README.md) · [学习路线](../learning/roadmap.md)

整理日期：2026-10-23。本页优先帮助找到当前学习材料，不追求链接数量。

## 当前入口

| 资料 | 用途 | 核验范围 |
| --- | --- | --- |
| [第7—12课教材](../learning/lessons/index.md) | 讲解、算例、Python、自测与参考答案 | 6个代码示例已运行；个人学习待记录 |
| [风险与仓位笔记](../learning/risk-and-position.md) | 复习已学概念、做计算题 | 本轮编写并复核算例 |
| [实验 001](../research/experiment-001.md) | 把仓位、波动率与回撤连起来 | 人工教学数据，已复算 |
| [NumPy 标准差说明](https://numpy.org/doc/stable/reference/generated/numpy.std.html) | 区分 ddof=0 与 ddof=1 | 2026-10-23 查阅官方文档 |
| [Investor.gov：费用如何影响投资组合](https://www.investor.gov/introduction-investing/general-resources/news-alerts/alerts-bulletins/investor-bulletins/updated) | 理解为何记录成本和净收益 | 2026-10-23 查阅官方资料 |

## 后续课程参考

| 官方资料 | 对应课程 |
| --- | --- |
| [pandas CSV与表格读写](https://pandas.pydata.org/docs/getting_started/intro_tutorials/02_read_write.html) | 第8课 |
| [pandas pct_change](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.pct_change.html) | 第8课，相对变化与缺失值 |
| [pandas shift](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.shift.html) / [rolling](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.rolling.html) | 第9、11课，历史窗口与信号时序 |
| [时间序列交叉验证](https://scikit-learn.org/stable/modules/cross_validation.html#time-series-split) | 第12课，按时间检验 |

以上资料于2026-10-23查阅。后续教材承接既有《量化交易学习手册》第7课与第8课安排；旧第6课卖出示例日期的校正见[第11课](../learning/lessons/11-minimal-backtest.md)。本轮不修改已有HTML手册。

## 原有书籍：按学习用途索引

下列用途是阅读规划；本轮仅核对文件存在，未逐本阅读或重新验证 PDF 内容。使用前先确认具体版本。

| 仓库文件 | 可对应的学习主题 | 当前状态 |
| --- | --- | --- |
| [《一本书读懂财报（全新修订版）》](../%E3%80%8A%E4%B8%80%E6%9C%AC%E4%B9%A6%E8%AF%BB%E6%87%82%E8%B4%A2%E6%8A%A5%EF%BC%88%E5%85%A8%E6%96%B0%E4%BF%AE%E8%AE%A2%E7%89%88%EF%BC%89%E3%80%8B.pdf) | 财务报表与基本面概念 | 文件存在，内容未复核 |
| [《投资最重要的事》](../%E3%80%8A%E6%8A%95%E8%B5%84%E6%9C%80%E9%87%8D%E8%A6%81%E7%9A%84%E4%BA%8B%E3%80%8B.pdf) | 风险与投资判断 | 文件存在，内容未复核 |
| [《股票作手回忆录》](../%E3%80%8A%E8%82%A1%E7%A5%A8%E4%BD%9C%E6%89%8B%E5%9B%9E%E5%BF%86%E5%BD%95%E3%80%8B.pdf) | 交易过程与心理观察 | 文件存在，内容未复核 |
| [《资产配置的艺术》](../%E3%80%8A%E8%B5%84%E4%BA%A7%E9%85%8D%E7%BD%AE%E7%9A%84%E8%89%BA%E6%9C%AF(%E9%AB%98%E6%B8%85)%E3%80%8B.pdf) | 资产组合与风险分配 | 文件存在，内容未复核 |

先按学习问题选一个章节或主题，形成个人笔记；书籍观点本身不等于经过回测的交易规则。

## 版本关系待核对

| 文件组合 | 本轮处理 | 后续动作 |
| --- | --- | --- |
| 《怎样选择成长股》.pdf / 《怎样选择成长股最新全译版》.pdf | 保留两个文件 | 核对作者、译者、版次与内容后再决定常用入口 |
| 《纳瓦尔宝典》.pdf / 《纳瓦尔宝典财富和幸福指南》.pdf | 保留两个文件 | 核对版本关系，不能仅凭名称判为重复 |

## 历史清单中的待处理项

原文集中保存在首页的折叠区，保留上游来源。本轮没有把下列问题当作已修复。

| 项目 | 已观察到的问题 | 下一步 |
| --- | --- | --- |
| UNIQ | 原链接写为 `https.uniq.cx/`，不是规范 HTTPS 地址 | 核实站点身份后再替换，不能只猜协议 |
| BTC 生态教程 | 使用 `notion://` 协议 | 核实可公开访问的网页入口 |
| 部分交易所及工具 | 存在 ref、inviteCode 或邀请路径 | 如纳入精选入口，核实官方地址并标注推广关系或使用普通入口 |
| 频道介绍与评分 | 存在“最近”“几个月”等无日期评价 | 重新查看具体内容，再记录评价时间与依据 |
| 书籍 PDF 链接 | 历史正文指向上游 main | 本页精选入口使用当前仓库相对链接；原文保留 |
| 17 份 PDF | 全部位于根目录，缺少版本说明 | 优先补索引；本轮不搬动文件 |

其余外链未逐一访问，不能据此判断全部有效或全部失效。

## 新资料收录模板

每条新增资料只需记录：

- 标题、作者或机构、原始链接。
- 发布日期（未知则注明未知）与最后核验日期。
- 解决哪个学习问题，读完准备产出什么。
- 状态：待阅读 / 已阅读 / 已形成笔记 / 已进入实验。
- 我的判断与原作者观点分别记录；不把生成摘要当作本人已经认可的结论。
