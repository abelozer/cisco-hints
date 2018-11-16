# Cisco load-interval command

Most of peaple are thinking that `load-interval N` is showing avegage pps/bps during last N seconds. This is not the case. Things a little bit more complicated.

Cisco devices display a value which is calculated as a [Exponentially Weighted Moving Average](https://en.wikipedia.org/wiki/Moving_average). The weights are fix, and the time range covered by the moving average depends on the configured load-interval.

If `load-interval 30` is configured, the last 30 seconds have a weight of 60%, the preceding 30 seconds are weighted around 24%, the preceding 30 secs around 9% and so on.
