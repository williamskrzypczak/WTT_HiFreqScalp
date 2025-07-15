# WTT_HiFreqScalp Alert Conditions Cheat Sheet

## Overview
This cheat sheet documents all alert conditions in the WTT_HiFreqScalp indicator and their underlying logic.

---

## 1. Syzygy Pivot Long (Trending Up)
**Alert Title**: "Syzygy Pivot Long (Trending Up)"

### Logic Requirements (ALL must be true):
- **EMA Crossover**: `ema3` (fast EMA) crosses above `middleEMA` (average of fast and slow EMAs)
- **Signal Spacing**: At least 2 bars since last long signal (`bar_index - lastLongSignalBar >= minBarsBetweenSignals`)
- **Volume Confirmation**: Volume > average volume × 1.5 (if volume filter enabled)
- **Market Structure**: Market must be "TRENDING"
- **Price Direction**: Price change must be positive (trending up)
- **Alerts Enabled**: Global alerts must be enabled

### Core Signal Logic:
```pine
longSignal = ta.crossover(ema3, middleEMA)
longSignalActive = longSignal AND spacing_check AND volume_check AND market_structure == "TRENDING" AND price_change > 0
```

---

## 2. Syzygy Pivot Short (Trending Down)
**Alert Title**: "Syzygy Pivot Short (Trending Down)"

### Logic Requirements (ALL must be true):
- **EMA Crossunder**: `ema3` (fast EMA) crosses below `middleEMA` (average of fast and slow EMAs)
- **Signal Spacing**: At least 2 bars since last short signal (`bar_index - lastShortSignalBar >= minBarsBetweenSignals`)
- **Volume Confirmation**: Volume > average volume × 1.5 (if volume filter enabled)
- **Market Structure**: Market must be "TRENDING"
- **Price Direction**: Price change must be negative (trending down)
- **Alerts Enabled**: Global alerts must be enabled

### Core Signal Logic:
```pine
shortSignal = ta.crossunder(ema3, middleEMA)
shortSignalActive = shortSignal AND spacing_check AND volume_check AND market_structure == "TRENDING" AND price_change < 0
```

---

## 3. Syzygy Momentum Peak
**Alert Title**: "Syzygy Momentum Peak"

### Logic Requirements (ANY of the following):
- **Strict Long Momentum Peak**: 
  - RSI ≤ oversold level (25 for scalping, 30 for day trading, 35 for swing)
  - ADX ≥ threshold (25 for scalping, 30 for day trading, 25 for swing)
  - Volume > average volume × 1.5

- **Strict Short Momentum Peak**:
  - RSI ≥ overbought level (75 for scalping, 70 for day trading, 65 for swing)
  - ADX ≥ threshold (25 for scalping, 30 for day trading, 25 for swing)
  - Volume > average volume × 1.5

- **Potential Long Momentum Peak**:
  - RSI ≤ oversold level × 0.85 (less strict)
  - ADX ≥ threshold × 0.8 (less strict)
  - Volume > average volume × 1.2 (less strict)

- **Potential Short Momentum Peak**:
  - RSI ≥ overbought level × 0.85 (less strict)
  - ADX ≥ threshold × 0.8 (less strict)
  - Volume > average volume × 1.2 (less strict)

### Additional Requirements:
- **Alerts Enabled**: Global alerts must be enabled
- **SMP Alerts Enabled**: SMP-specific alerts must be enabled

### Core Signal Logic:
```pine
syzygy_momentum_peak_signal = strict_momentum_peak_long OR strict_momentum_peak_short OR potential_momentum_peak_long OR potential_momentum_peak_short
```

---

## 4. Long Pivot Bounce Detected
**Alert Title**: "Long Pivot Bounce Detected"

### Logic Requirements (ALL must be true):
- **Bounce Detection**: Price bounces off pullback level
- **First Detection**: Bounce detected on this bar but not previous bar (`long_bounce_detected AND NOT long_bounce_detected[1]`)
- **Pullback Conditions**:
  - Pullback detection enabled
  - Long pullback detected
  - Pullback low level > 0
  - Close price > pullback low level
  - Pivot not invalidated by EMA
- **Alerts Enabled**: Global alerts must be enabled
- **Bounce Alerts Enabled**: Bounce-specific alerts must be enabled

### Core Signal Logic:
```pine
long_bounce_alert_ready = long_bounce_detected AND NOT long_bounce_detected[1] AND alerts_enabled AND enable_bounce_alerts
```

---

## 5. Short Pivot Bounce Detected
**Alert Title**: "Short Pivot Bounce Detected"

### Logic Requirements (ALL must be true):
- **Bounce Detection**: Price bounces off pullback level
- **First Detection**: Bounce detected on this bar but not previous bar (`short_bounce_detected AND NOT short_bounce_detected[1]`)
- **Pullback Conditions**:
  - Pullback detection enabled
  - Short pullback detected
  - Pullback high level > 0
  - Close price < pullback high level
  - Pivot not invalidated by EMA
- **Alerts Enabled**: Global alerts must be enabled
- **Bounce Alerts Enabled**: Bounce-specific alerts must be enabled

### Core Signal Logic:
```pine
short_bounce_alert_ready = short_bounce_detected AND NOT short_bounce_detected[1] AND alerts_enabled AND enable_bounce_alerts
```

---

## 6. Syzygy Range Breakout Bullish
**Alert Title**: "Syzygy Range Breakout Bullish"

### Logic Requirements (ALL must be true):
- **Keltner Breakout**: Close price crosses above upper Keltner Channel
- **Alerts Enabled**: Global alerts must be enabled

### Core Signal Logic:
```pine
keltner_upper_cross_up = ta.crossover(close, keltner_upper)
```

---

## 7. Syzygy Range Breakout Bearish
**Alert Title**: "Syzygy Range Breakout Bearish"

### Logic Requirements (ALL must be true):
- **Keltner Breakdown**: Close price crosses below lower Keltner Channel
- **Alerts Enabled**: Global alerts must be enabled

### Core Signal Logic:
```pine
keltner_lower_cross_down = ta.crossunder(close, keltner_lower)
```

---

## Alert Configuration Settings

### Global Alert Controls:
- **Enable Alerts**: Master switch for all alerts
- **Enable SMP Alerts**: Toggle for Syzygy Momentum Peak alerts
- **Enable Bounce Alerts**: Toggle for bounce detection alerts

### Signal Filtering:
- **Min Bars Between Signals**: Minimum 2 bars between signals (prevents spam)
- **Volume Filter**: Requires above-average volume for pivot signals
- **Volume Threshold Multiplier**: 1.5x average volume required

### Market Structure Detection:
- **Trend vs Range**: Determines if market is trending, ranging, or mixed
- **Trend Threshold**: 0.45 (45% of bars must show consistent direction)
- **Range Threshold**: 0.35 (35% of bars must show sideways movement)

### Pullback Detection:
- **Enable Pullback Detection**: Master switch for pullback/bounce logic
- **Pullback Percentage**: 0.15% (configurable 0.01-10.0%)
- **Pullback Lookback**: 50 bars maximum to look back for pullback

---

## Trading Style Presets

### Scalping (Default):
- RSI Oversold: 25, Overbought: 75
- ADX Threshold: 25
- EMA Lengths: 21, 50, 8
- Volume MA: 20

### Day Trading:
- RSI Oversold: 30, Overbought: 70
- ADX Threshold: 30
- EMA Lengths: 21, 50, 9
- Volume MA: 20

### Swing Trading:
- RSI Oversold: 35, Overbought: 65
- ADX Threshold: 25
- EMA Lengths: 50, 200, 20
- Volume MA: 30

---

## Visual Indicators

### Chart Shapes:
- **Pivot Long**: Lime green circle below bar
- **Pivot Short**: White circle above bar
- **Momentum Peak Long**: Yellow circle below bar (strong), purple circle below bar (weak)
- **Momentum Peak Short**: Yellow circle above bar (strong), purple circle above bar (weak)
- **Bounce Long**: Lime green triangle pointing up below bar
- **Bounce Short**: Orange triangle pointing down above bar
- **Keltner Breakout**: Green square above bar (bullish), red square below bar (bearish)

### Table Highlighting:
- **Active Signals**: Background color changes when signal is active
- **Rank 1**: Orange background with large text
- **Rank 2**: Gray text
- **Insufficient Data**: Lime green dash ("-")

---

## Performance Tracking

### Three Bar Stop Strategy:
- **Lookback**: 50 signals (configurable 10-200)
- **Ranking**: Based on win percentage (not pip capture)
- **Minimum Data**: 2 trades required for ranking
- **Display**: Shows average pips, total trades, win percentage, and rank

### Success Metrics:
- **Win Percentage**: Percentage of profitable trades
- **Average Pips**: Average pip movement over 3 bars
- **Total Trades**: Number of completed trades
- **Rank**: Performance ranking (1-4) based on win percentage 