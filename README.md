# Standard Variation Indicator for Price Range Analysis:<br> Data Automation in Financial Markets

![.](nasdaq.png)

## Introduction
This project showcases a technical solution developed to streamline a manual daily volatility analysis workflow. Originally conducted using pen and paper on a trading floor, the process was first automated in Excel and later evolved into a fully visualized script built on TradingView using Pine Script (TradingView’s internal scripting language).

The script dynamically calculates historical price ranges and visualizes them to support data-driven trading decisions. It demonstrates the ability to identify repetitive manual tasks and replace them with automated, scalable solutions tailored for high-speed financial environments.

## Table of Contents
- [Business Context & Problem](#business-context--problem)
- [Solution Overview](#solution-overview)
- [Technical Skills Demonstrated](#technical-skills-demonstrated)
- [Business & Analytical Skills Demonstrated](#business--analytical-skills-demonstrated)
- [Sample Output](#sample-output)

## Business Context & Problem
During my role as a Fixed Income Sales Trader, **I manually calculated the prior day's price ranges and statistical deviations to identify high-probability support and resistance levels. This was a time-consuming, repetitive, and error-prone process — often under time pressure during market open.**

Business needs addressed:
1. Reduce manual workload and human error.
2. Improve consistency and accuracy in technical analysis.
3. Enable data-backed trading decisions grounded in historical price behavior.

**This indicator resolves these pain points by automating the creation and visualization of volatility-based zones, supporting faster and more reliable trading decisions.**

## Solution Overview
The script runs at the start of each trading day and performs the following tasks:
1. Calculates the previous day’s high, low, and midpoint
2. Computes average daily range over multiple lookback periods: 2, 4, 8, 16, 32, 64, 128 days
3. Dynamically draws horizontal lines based on range multipliers:
- Half-range (0.5x) → green dotted lines
- Full-range (1.0x) → yellow dashed lines
- One-and-a-half range (1.5x) → red solid lines

All levels are calculated from the midpoint and automatically adjust based on historical data.

This setup allows traders to:
1. Instantly visualize historical volatility zones.
2. See how current price interacts with key statistical thresholds.
3. Incorporate quantitative levels into decision-making frameworks.

## Technical Skills Demonstrated
1. **Pine Script (TradingView)** - built a fully custom indicator with efficient use of arrays, line plotting, and performance-aware architecture.
2. **Time Series Analysis** - applied statistical aggregation techniques across different temporal windows.
3. **Data Automation** - eliminated manual recalculations through time-based triggers and persistent variables (var).
4. **Clean Code Practices** - designed for readability and daily reusability without code edits.
5. **Statistical Thinking** - modeled volatility zones aligned with real-world trading logic.

## Business & Analytical Skills Demonstrated
1. **Problem Solving in Real-Time Settings** - converted a manual, risk-sensitive workflow into a repeatable, automated model.
2. **Financial Markets Expertise** - incorporated volatility logic grounded in professional fixed income and technical trading strategies.
3. **Insight Generation** - designed to reveal actionable relationships between price and statistical range behavior.
4. **Efficiency-First Mindset** - replaced a daily task with a zero-maintenance script.
5. **Scalable Thinking** - the solution is generic enough to be applied to other asset classes or integrated into multi-script toolkits.

## Sample Output

[https://github.com/user-attachments/assets/2265144e-2d27-49d8-8350-4f5629c45b91](https://github.com/user-attachments/assets/90a01c92-5889-4e52-bbf5-1d7d9796d21d)

    //@version=6
    indicator("Standard Deviations", overlay=true)

    // Time control: check if it's a new day
    t = time('D')
    is_new_day = ta.change(time('D')) != 0

    // Switch to daily timeframe data
    [prev_high, prev_low] = request.security(syminfo.tickerid, 'D', [high[1], low[1]], lookahead=barmerge.lookahead_off)

    // Calculate daily range
    daily_range = prev_high - prev_low

    // Calculate average range for different periods
    avg_range_128 = ta.sma(daily_range, 128)
    avg_range_64 = ta.sma(daily_range, 64)
    avg_range_32 = ta.sma(daily_range, 32)
    avg_range_16 = ta.sma(daily_range, 16)
    avg_range_8 = ta.sma(daily_range, 8)
    avg_range_4 = ta.sma(daily_range, 4)
    avg_range_2 = ta.sma(daily_range, 2)

    // Calculate previous day's midpoint
    prev_midpoint = prev_low + (prev_high - prev_low) / 2

    // Clear and redraw lines
    var line curr_line = na
    var line[] range_lines = array.new_line()

    var current_day_start = 0
    var current_day_end = 0

    // When a new daily candle forms, create the lines
    if is_new_day
    current_day_start := time('D')
    current_day_end := current_day_start + 24 * 60 * 60 * 1000  // 24 hours
    
    // Calculate how many bars remain until the end of the day
    var int bars_until_end = math.round((24 * 60) / timeframe.multiplier)
    
    // Previous day's midpoint line
    curr_line := line.new(bar_index, prev_midpoint, bar_index + bars_until_end, prev_midpoint, color=color.blue, style=line.style_solid, width=2)
    
    // Previous day's high and low lines
    array.push(range_lines, line.new(bar_index, prev_high, bar_index + bars_until_end, prev_high, color=color.new(color.blue, 0), style=line.style_dashed))
    array.push(range_lines, line.new(bar_index, prev_low, bar_index + bars_until_end, prev_low, color=color.new(color.blue, 0), style=line.style_dashed))
    
    // Range lines based on different periods
    
    // 128 bar average
    array.push(range_lines, line.new(bar_index, prev_midpoint + avg_range_128/2, bar_index + bars_until_end, prev_midpoint + avg_range_128/2, color=color.green, style=line.style_dotted))
    array.push(range_lines, line.new(bar_index, prev_midpoint - avg_range_128/2, bar_index + bars_until_end, prev_midpoint - avg_range_128/2, color=color.green, style=line.style_dotted))
    array.push(range_lines, line.new(bar_index, prev_midpoint + avg_range_128, bar_index + bars_until_end, prev_midpoint + avg_range_128, color=color.yellow, style=line.style_dashed))
    array.push(range_lines, line.new(bar_index, prev_midpoint - avg_range_128, bar_index + bars_until_end, prev_midpoint - avg_range_128, color=color.yellow, style=line.style_dashed))
    array.push(range_lines, line.new(bar_index, prev_midpoint + avg_range_128 * 1.5, bar_index + bars_until_end, prev_midpoint + avg_range_128 * 1.5, color=color.red, style=line.style_solid))
    array.push(range_lines, line.new(bar_index, prev_midpoint - avg_range_128 * 1.5, bar_index + bars_until_end, prev_midpoint - avg_range_128 * 1.5, color=color.red, style=line.style_solid))

    // 64 bar average
    array.push(range_lines, line.new(bar_index, prev_midpoint + avg_range_64/2, bar_index + bars_until_end, prev_midpoint + avg_range_64/2, color=color.green, style=line.style_dotted))
    array.push(range_lines, line.new(bar_index, prev_midpoint - avg_range_64/2, bar_index + bars_until_end, prev_midpoint - avg_range_64/2, color=color.green, style=line.style_dotted))
    array.push(range_lines, line.new(bar_index, prev_midpoint + avg_range_64, bar_index + bars_until_end, prev_midpoint + avg_range_64, color=color.yellow, style=line.style_dashed))
    array.push(range_lines, line.new(bar_index, prev_midpoint - avg_range_64, bar_index + bars_until_end, prev_midpoint - avg_range_64, color=color.yellow, style=line.style_dashed))
    array.push(range_lines, line.new(bar_index, prev_midpoint + avg_range_64 * 1.5, bar_index + bars_until_end, prev_midpoint + avg_range_64 * 1.5, color=color.red, style=line.style_solid))
    array.push(range_lines, line.new(bar_index, prev_midpoint - avg_range_64 * 1.5, bar_index + bars_until_end, prev_midpoint - avg_range_64 * 1.5, color=color.red, style=line.style_solid))

    // 32 bar average
    array.push(range_lines, line.new(bar_index, prev_midpoint + avg_range_32/2, bar_index + bars_until_end, prev_midpoint + avg_range_32/2, color=color.green, style=line.style_dotted))
    array.push(range_lines, line.new(bar_index, prev_midpoint - avg_range_32/2, bar_index + bars_until_end, prev_midpoint - avg_range_32/2, color=color.green, style=line.style_dotted))
    array.push(range_lines, line.new(bar_index, prev_midpoint + avg_range_32, bar_index + bars_until_end, prev_midpoint + avg_range_32, color=color.yellow, style=line.style_dashed))
    array.push(range_lines, line.new(bar_index, prev_midpoint - avg_range_32, bar_index + bars_until_end, prev_midpoint - avg_range_32, color=color.yellow, style=line.style_dashed))
    array.push(range_lines, line.new(bar_index, prev_midpoint + avg_range_32 * 1.5, bar_index + bars_until_end, prev_midpoint + avg_range_32 * 1.5, color=color.red, style=line.style_solid))
    array.push(range_lines, line.new(bar_index, prev_midpoint - avg_range_32 * 1.5, bar_index + bars_until_end, prev_midpoint - avg_range_32 * 1.5, color=color.red, style=line.style_solid))

    // 16 bar average
    array.push(range_lines, line.new(bar_index, prev_midpoint + avg_range_16/2, bar_index + bars_until_end, prev_midpoint + avg_range_16/2, color=color.green, style=line.style_dotted))
    array.push(range_lines, line.new(bar_index, prev_midpoint - avg_range_16/2, bar_index + bars_until_end, prev_midpoint - avg_range_16/2, color=color.green, style=line.style_dotted))
    array.push(range_lines, line.new(bar_index, prev_midpoint + avg_range_16, bar_index + bars_until_end, prev_midpoint + avg_range_16, color=color.yellow, style=line.style_dashed))
    array.push(range_lines, line.new(bar_index, prev_midpoint - avg_range_16, bar_index + bars_until_end, prev_midpoint - avg_range_16, color=color.yellow, style=line.style_dashed))
    array.push(range_lines, line.new(bar_index, prev_midpoint + avg_range_16 * 1.5, bar_index + bars_until_end, prev_midpoint + avg_range_16 * 1.5, color=color.red, style=line.style_solid))
    array.push(range_lines, line.new(bar_index, prev_midpoint - avg_range_16 * 1.5, bar_index + bars_until_end, prev_midpoint - avg_range_16 * 1.5, color=color.red, style=line.style_solid))

    // 8 bar average
    array.push(range_lines, line.new(bar_index, prev_midpoint + avg_range_8/2, bar_index + bars_until_end, prev_midpoint + avg_range_8/2, color=color.green, style=line.style_dotted))
    array.push(range_lines, line.new(bar_index, prev_midpoint - avg_range_8/2, bar_index + bars_until_end, prev_midpoint - avg_range_8/2, color=color.green, style=line.style_dotted))
    array.push(range_lines, line.new(bar_index, prev_midpoint + avg_range_8, bar_index + bars_until_end, prev_midpoint + avg_range_8, color=color.yellow, style=line.style_dashed))
    array.push(range_lines, line.new(bar_index, prev_midpoint - avg_range_8, bar_index + bars_until_end, prev_midpoint - avg_range_8, color=color.yellow, style=line.style_dashed))
    array.push(range_lines, line.new(bar_index, prev_midpoint + avg_range_8 * 1.5, bar_index + bars_until_end, prev_midpoint + avg_range_8 * 1.5, color=color.red, style=line.style_solid))
    array.push(range_lines, line.new(bar_index, prev_midpoint - avg_range_8 * 1.5, bar_index + bars_until_end, prev_midpoint - avg_range_8 * 1.5, color=color.red, style=line.style_solid))

    // 4 bar average
    array.push(range_lines, line.new(bar_index, prev_midpoint + avg_range_4/2, bar_index + bars_until_end, prev_midpoint + avg_range_4/2, color=color.green, style=line.style_dotted))
    array.push(range_lines, line.new(bar_index, prev_midpoint - avg_range_4/2, bar_index + bars_until_end, prev_midpoint - avg_range_4/2, color=color.green, style=line.style_dotted))
    array.push(range_lines, line.new(bar_index, prev_midpoint + avg_range_4, bar_index + bars_until_end, prev_midpoint + avg_range_4, color=color.yellow, style=line.style_dashed))
    array.push(range_lines, line.new(bar_index, prev_midpoint - avg_range_4, bar_index + bars_until_end, prev_midpoint - avg_range_4, color=color.yellow, style=line.style_dashed))
    array.push(range_lines, line.new(bar_index, prev_midpoint + avg_range_4 * 1.5, bar_index + bars_until_end, prev_midpoint + avg_range_4 * 1.5, color=color.red, style=line.style_solid))
    array.push(range_lines, line.new(bar_index, prev_midpoint - avg_range_4 * 1.5, bar_index + bars_until_end, prev_midpoint - avg_range_4 * 1.5, color=color.red, style=line.style_solid))

    // 2 bar average
    array.push(range_lines, line.new(bar_index, prev_midpoint + avg_range_2/2, bar_index + bars_until_end, prev_midpoint + avg_range_2/2, color=color.green, style=line.style_dotted))
    array.push(range_lines, line.new(bar_index, prev_midpoint - avg_range_2/2, bar_index + bars_until_end, prev_midpoint - avg_range_2/2, color=color.green, style=line.style_dotted))
    array.push(range_lines, line.new(bar_index, prev_midpoint + avg_range_2, bar_index + bars_until_end, prev_midpoint + avg_range_2, color=color.yellow, style=line.style_dashed))
    array.push(range_lines, line.new(bar_index, prev_midpoint - avg_range_2, bar_index + bars_until_end, prev_midpoint - avg_range_2, color=color.yellow, style=line.style_dashed))
    array.push(range_lines, line.new(bar_index, prev_midpoint + avg_range_2 * 1.5, bar_index + bars_until_end, prev_midpoint + avg_range_2 * 1.5, color=color.red, style=line.style_solid))
    array.push(range_lines, line.new(bar_index, prev_midpoint - avg_range_2 * 1.5, bar_index + bars_until_end, prev_midpoint - avg_range_2 * 1.5, color=color.red, style=line.style_solid))
