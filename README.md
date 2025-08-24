//@version=5
indicator("Pocket Option EMA + RSI Alerts", overlay=true)

// ─── Inputs ─────────────────────────────────────
emaFastLen   = input.int(9,  "Fast EMA Length")
emaSlowLen   = input.int(21, "Slow EMA Length")
rsiLen       = input.int(14, "RSI Length")
rsiOB        = input.int(70, "RSI Overbought Level")
rsiOS        = input.int(30, "RSI Oversold Level")

showBg       = input.bool(true, "Show Background Highlight")

// ─── Indicators ─────────────────────────────────
emaFast = ta.ema(close, emaFastLen)
emaSlow = ta.ema(close, emaSlowLen)
rsi     = ta.rsi(close, rsiLen)

// ─── Conditions ─────────────────────────────────
longCond  = ta.crossover(emaFast, emaSlow) and rsi > 50 and rsi < rsiOB
shortCond = ta.crossunder(emaFast, emaSlow) and rsi < 50 and rsi > rsiOS

// ─── Plots ──────────────────────────────────────
plot(emaFast, "EMA Fast", color=color.blue, linewidth=2)
plot(emaSlow, "EMA Slow", color=color.red,  linewidth=2)

plotshape(longCond,  title="Buy",  text="CALL", location=location.belowbar,
          color=color.green, style=shape.labelup, size=size.small)

plotshape(shortCond, title="Sell", text="PUT",  location=location.abovebar,
          color=color.red,   style=shape.labeldown, size=size.small)

// ─── Background Highlight ───────────────────────
bgcolor(showBg and longCond  ? color.new(color.green, 85) : na)
bgcolor(showBg and shortCond ? color.new(color.red,   85) : na)

// ─── Alerts ─────────────────────────────────────
alertcondition(longCond,  title="CALL Alert", message="EMA Crossover + RSI confirm → CALL")
alertcondition(shortCond, title="PUT Alert",  message="EMA Crossunder + RSI confirm → PUT")
