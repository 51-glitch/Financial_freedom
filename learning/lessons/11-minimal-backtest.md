# 第 11 课｜从交易规则到最小回测

[课程目录](index.md) · [上一课](10-expectancy-and-costs.md) · [下一课](12-out-of-sample.md)

整理日期：2026-10-23。状态：预备教材，待学习。建议 50—70 分钟，可以拆为两次。先修第 8—10 课。

## 1. 这次要多做哪一步？

第 6 课用过去 3 日收盘高低点产生买卖记录。这节沿用同一组人工开盘价和收盘价，补上现金、份额、费用、滑点、逐日净值和基准。目标是把每一元盈亏追溯到一笔账。

本课例子只为学习账本与时间顺序，不宣称突破策略有效。

## 2. 在运行之前固定规则

| 项目 | 本课约定 |
| --- | --- |
| 入场信号 | 当日收盘价严格高于之前3个收盘价的最大值，且当前空仓 |
| 退出信号 | 当日收盘价严格低于之前3个收盘价的最小值，且当前持仓 |
| 成交 | 信号次行开盘；最后一行信号没有下一行，保持未执行 |
| 资金 | 初始20,000元，每次买100股；现金不足就不买；不借钱、不做空 |
| 滑点 | 买入价=开盘价×1.001；卖出价=开盘价×0.999 |
| 手续费 | 每个方向成交额×0.0005；不设最低收费，暂不模拟其他税费 |
| 估值 | 每日收盘按现金＋持仓股数×收盘价，包含初始净值 |
| 基准 | 第5行开盘买100股后持有，与策略首次入场同起点、同资金、同买入成本 |
| 末期 | 两组都按市值计价，不额外强制清仓；基准持仓浮盈不算已实现利润 |

所有费率和股数均为教学参数。允许按下一行开盘成交也只是简化；真实成交还受交易制度、报价、流动性和停牌等约束。

## 3. 逐步理解账本

买入时：`现金减少 = 股数 × 买入成交价 + 买入费`。

卖出时：`现金增加 = 股数 × 卖出成交价 - 卖出费`。

没有买卖的日子，现金不变，但持仓市值会变。不能只记录平仓后的资金，否则可能漏掉中间浮亏。最大回撤只观察收盘净值，因此也不包含日内更深的回撤。

这组数据中第 4 行收盘确认入场，第 5 行开盘买；第 9 行收盘确认退出，第 10 行开盘卖。第 12 行收盘再次产生入场信号，但无法在样本内成交。

## 4. 可直接运行的完整例子

```python
import pandas as pd

# 人工数据，日期只是标签，不对应真实行情或交易日历。
df = pd.DataFrame({
    "date": pd.date_range("2026-01-01", periods=12, freq="D"),
    "open": [100,100.8,101.8,102.5,104.8,103.5,105.2,107.2,105.5,103.8,102.6,106],
    "close": [100,101,102,104,103,105,107,106,104,103,105,108],
})
high = df["close"].shift(1).rolling(3, min_periods=3).max()
low = df["close"].shift(1).rolling(3, min_periods=3).min()
entry = df["close"].gt(high) & high.notna()
exit_signal = df["close"].lt(low) & low.notna()

initial = 20000.0
quantity = 100
fee_rate = 0.0005
slippage = 0.001

def run(signals=True):
    cash, shares = initial, 0
    nav, trades = [initial], []
    pending = None
    for i in range(len(df)):
        # 只执行上一行收盘后发出的指令。
        order = pending
        pending = None
        # 基准：同样100股，在策略首次可执行的第5行开盘买入后持有。
        if not signals and i == 4:
            order = "BUY"

        if order == "BUY" and shares == 0:
            price = float(df.loc[i, "open"]) * (1 + slippage)
            notional = quantity * price
            fee = notional * fee_rate
            if cash >= notional + fee:
                cash -= notional + fee
                shares = quantity
                trades.append((i, "BUY", price, fee))
        elif order == "SELL" and shares > 0:
            price = float(df.loc[i, "open"]) * (1 - slippage)
            notional = shares * price
            fee = notional * fee_rate
            cash += notional - fee
            trades.append((i, "SELL", price, fee))
            shares = 0

        nav.append(cash + shares * float(df.loc[i, "close"]))
        if signals:
            if shares == 0 and bool(entry.iloc[i]):
                pending = "BUY"
            elif shares > 0 and bool(exit_signal.iloc[i]):
                pending = "SELL"

    peak, mdd = nav[0], 0.0
    for value in nav:
        peak = max(peak, value)
        mdd = max(mdd, 1 - value/peak)
    return {"nav": nav, "trades": trades, "shares": shares,
            "pending": pending, "return": nav[-1]/initial-1, "mdd": mdd}

strategy, baseline = run(), run(False)
for label, result in [("策略", strategy), ("同起点100股持有基准", baseline)]:
    print(label)
    for i, action, price, fee in result["trades"]:
        print(df.loc[i, "date"].date(), action, f"成交价 {price:.4f}", f"费用 {fee:.5f}")
    print(f"期末权益 {result['nav'][-1]:.5f}, 收益 {result['return']:.4%}, "
          f"最大回撤 {result['mdd']:.4%}, 期末股数 {result['shares']}")
print("样本末尾未执行信号:", strategy["pending"])
assert [(t[0], t[1]) for t in strategy["trades"]] == [(4,"BUY"), (9,"SELL")]
assert strategy["shares"] == 0 and strategy["pending"] == "BUY"
```

默认参数的核对结果：

| 项目 | 策略 | 同起点100股持有基准 |
| --- | --- | --- |
| 买入 | 1月5日，104.9048元/股 | 相同 |
| 卖出 | 1月10日，103.6962元/股 | 未卖出 |
| 期末权益 | 19,868.70995元 | 20,304.27476元 |
| 累计收益 | -0.6565% | +1.5214% |
| 收盘净值最大回撤 | 1.6609% | 1.9798% |

**与旧手册的校正：**旧第6课文字示例写成1月10日发出退出信号、1月11日以102.6卖出。按同一份代码与价格序列复算，1月9日收盘104已经低于此前三个收盘价105、107、106的最低值105，因此应在1月10日开盘103.8执行。本页按规则和代码校正，未据此改变个人学习完成状态。

## 5. 为什么基准也要写清楚？

本例基准是“同起点、同样100股、剩余现金不计息”，并非全仓市场指数。这个定义用于比较这段样本中退出规则的效果。若改用全仓持有，会同时改变风险暴露；应明确说明，不能看到结果再换成更容易战胜的基准。

末期基准仍持有100股，未扣未来卖出费用；策略已卖出，费用已扣。报告中必须保留这个区别。如果要比较全部变现后的结果，应同时给两组定义同一可执行的退出时点。

## 6. 动手练习

1. 先把手续费和滑点都设为0。策略的买卖价应为104.8与103.8，毛亏损100元，期末权益19,900元。
2. 恢复默认成本，手算两次成交价和费用，核对程序期末现金。
3. 把最后一行 close 改成90，观察先前交易是否不变、最后未执行信号是否改变。
4. 打印每日现金、份额和净值，自己选一天复算；不要只看最后一个数字。

进阶：再加一个下一行，观察最后未执行指令如何成交。新增行不是实际行情，不要把人为修改后的表现称为样本外验证。

## 7. 自测与验收

1. 为什么不能用第4行最终收盘价确认信号，又直接按它买入？
2. 一次往返交易是几条成交记录？本例是什么？
3. 滑点已经体现在成交价中，是否还要扣同一笔滑点费用？
4. 本例基准比策略好，是否足以否定该规则在所有市场的作用？

<details>
<summary>展开答案</summary>

1. 计算最终收盘信号所需的信息，在该收盘价可成交之前不一定已经可得；本例明确使用次行开盘。
2. 两条，一买一卖；程序输出2条成交，对应1次完整往返，末尾信号不是成交。
3. 不应重复扣除。
4. 不足以，样本只有12行人工数据；这里只验证执行与记账逻辑。

</details>

- [ ] 能逐笔复算现金与手续费。
- [ ] 区分信号、指令、成交、已实现盈亏和浮盈。
- [ ] 能解释基准定义和末期未执行信号。

资料：[pandas rolling](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.rolling.html)、[shift](https://pandas.pydata.org/docs/reference/api/pandas.DataFrame.shift.html)，查阅日期 2026-10-23。
