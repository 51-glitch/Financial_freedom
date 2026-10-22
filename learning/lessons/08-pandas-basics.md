# 第 8 课｜pandas 表格数据与时间序列基础

[课程目录](index.md) · [学习路线](../roadmap.md) · [上一课](07-week-one-review.md) · [下一课](09-data-quality.md)

整理日期：2026-10-23。状态：预备教材，待学习；准备好内容不代表已经完成课程。

建议 40—60 分钟。先修：完成第 7 课复习；会读变量、列表和 for 循环。目标是能够看懂并整理一张行情表。

## 1. 用表格理解 pandas

- **DataFrame** 是整张表，像带列名的电子表格。
- **Series** 是一列数据，例如收盘价。
- **index** 是行标签，使用日期索引后，可按日期取数。
- `df["close"]` 取一列；`df[["open", "close"]]` 取两列。
- `loc` 按标签选择，`iloc` 按行位置选择。日期看起来是字符串，不代表它已经是日期类型。

学习时间序列的第一件事不是计算指标，而是确认顺序、重复日期和缺失值。读完后应该能说清每行代表什么、每列是什么单位。

## 2. 从 CSV 到日期索引

以下 CSV 是教学数据，日期仅作排序标签，不表示真实交易日历或行情。把完整代码复制到一个 Python 文件运行；本节不连接网络，也不要求券商账户。需要 pandas，首次可用 `python -m pip install pandas` 安装到自己的练习环境。

```python
from io import StringIO
import pandas as pd

csv_text = """date,open,close,volume
2026-01-05,102,103,1300
2026-01-02,100,101,1000
2026-01-03,101,102,1100
2026-01-03,101,102,1100
2026-01-04,102,,1200
"""
raw = pd.read_csv(StringIO(csv_text), parse_dates=["date"])
print("原始行数:", len(raw))
print(raw.dtypes)

# 仅删除完全相同的整行；同日期但数值不同的记录要停下来核对。
clean = raw.drop_duplicates().sort_values("date").set_index("date")
assert clean.index.is_unique, "存在冲突日期，请核对来源"
clean["return"] = clean["close"].pct_change(fill_method=None)
print(clean)
print("缺失数量:", clean.isna().sum().to_dict())
print("按日期取一行:", clean.loc["2026-01-03", "close"])
print("按位置取第一行:", clean.iloc[0]["close"])
print("收盘价高于101的行:")
print(clean.loc[clean["close"] > 101, ["close", "volume"]])

assert len(raw) == 5 and len(clean) == 4
assert pd.isna(clean.loc["2026-01-04", "close"])
assert abs(clean.loc["2026-01-03", "return"] - (102/101 - 1)) < 1e-12
assert pd.isna(clean.loc["2026-01-05", "return"])
```

## 3. 逐步解释输出

原始 5 行，去除一条完全相同的重复行后剩 4 行。第一天没有上一天价格，收益率是 NaN，表示缺少值，不是 0%。

1 月 3 日收益约为 **0.9901%**。1 月 4 日收盘价缺失，所以当天收益未知；1 月 5 日也无法直接计算相邻一行的收益。代码没有填充，因此这两行收益仍是 NaN。

`pct_change` 返回的是比例，0.01 表示 1%；显示时可以格式化为百分数，计算时继续用小数。不要把 1 当作 1%。

## 4. 为什么不立刻补齐缺失值？

把 1 月 4 日缺失价格填成 102，会人为制造“当天没涨没跌”的事实。把它填成 1 月 5 日的 103，则让过去知道了未来。

本节选择保留并显示缺失。进入回测前，应追查是数据错误、停牌还是日期问题；后续处理必须与交易和估值规则匹配。

## 5. 动手修改

- 将重复行的 close 从 102 改为 999。此时两行不再完全相同，唯一日期检查应失败；不要靠随便保留最后一行掩盖冲突。
- 将 1 月 4 日 close 补成教学假设值 100，重新计算 1 月 4 日和 1 月 5 日收益，分别应为约 -1.9608% 和 3%。
- 在内存中把 clean 表用 `to_csv` 导出字符串，再用 `StringIO` 读回，核对日期索引。需要写文件时再明确目标路径。

## 6. 自测

1. Series 与 DataFrame 有什么区别？
2. 为什么先排序，再计算相邻收益？
3. 缺失值能否无条件改成 0？
4. 同一个日期有两种收盘价，应直接保留最后一条吗？

<details>
<summary>展开答案</summary>

1. 一列带索引的数据与多列表格。
2. 相邻行必须对应正确的时间先后，否则收益区间错位。
3. 不能；0 元价格或 0% 收益都是具体数值，缺失表示不知道。
4. 不能；先检查来源、版本与业务含义，再定义取舍规则。

</details>

完成标准：解释代码中每一步，成功触发冲突检查，并手算两个收益。学习记录保持待完成，不能因断言通过就自动打卡。

资料：[pandas 表格读写教程](https://pandas.pydata.org/docs/getting_started/intro_tutorials/02_read_write.html)、[pct_change 官方说明](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.pct_change.html)，查阅日期 2026-10-23。
