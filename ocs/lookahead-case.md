# The Attempt-1 Case: How a 95% Backtest Became 50% Live

## What happened
A crypto futures strategy entered only on the first attempt after a golden cross.
Backtest win rate: 95%. Live win rate: about 50%.

## The cause
The counter that tracked "first attempt" was reset by a condition that looked at
the future: it used the time the previous signal hit its take-profit, which was
often after the current bar. In 156 mismatched trades, 100% had this leak.

## Why it was hard to see
The column was just an integer. No header, no timestamp told you it was
contaminated. The leak was in how the column was defined, not in the data.

## What failed afterwards
- Multivariate filters to separate real from fake signals: failed
- XGBoost out-of-sample: AUC 0.519 (a coin flip)
- 53.2% on 5 symbols became 42.1% on 552 symbols

## What I changed
1. Check every column at definition time: could this value be computed at time t?
2. Store when each value was published, not only the period it describes.
3. Count trials before testing, seal the out-of-sample set, and open it once.

Sentinel's time-travel replay is built on this lesson.
