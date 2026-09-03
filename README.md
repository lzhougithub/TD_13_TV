# TD DeMark Pine Scripts

This repository contains two TradingView Pine Script v2 indicators:

- `TD.pine`: the original indicator with simplified TD Combo counting.
- `TD_2.pine`: the LZH version with stricter TD Combo rules and additional C10 markers.

## Core Difference

The main behavioral difference is how each script qualifies TD Combo countdown bars.

`TD.pine` applies the same simplified conditions to every Combo count from C1 through C13.

For a Buy Combo, a bar qualifies when:

```text
close <= low two bars earlier
close < previous qualifying Combo close
```

For a Sell Combo, a bar qualifies when:

```text
close >= high two bars earlier
close > previous qualifying Combo close
```

`TD_2.pine` separates C1-C10 from C11-C13.

## Strict C1-C10 Rules

For Buy Combo C1-C10, all four conditions must be true:

```text
close <= low two bars earlier
low < previous bar's low
close < previous bar's close
close < previous qualifying Combo close
```

For Sell Combo C1-C10, all four conditions must be true:

```text
close >= high two bars earlier
high > previous bar's high
close > previous bar's close
close > previous qualifying Combo close
```

## C11-C13 Rules

For Buy Combo C11-C13, each qualifying close only needs to be lower than the previous qualifying Combo close.

For Sell Combo C11-C13, each qualifying close only needs to be higher than the previous qualifying Combo close.

The other C1-C10 tests are not applied to C11-C13 in `TD_2.pine`.

## Chart Markers

| Marker | `TD.pine` | `TD_2.pine` |
| --- | --- | --- |
| Buy Combo C10 | Not plotted | Green upward marker labelled `C10` |
| Sell Combo C10 | Not plotted | Red downward marker labelled `C10` |
| Buy Combo C13 | Plotted | Plotted |
| Sell Combo C13 | Plotted | Plotted |

## Indicator Name

| File | Full title | Short title |
| --- | --- | --- |
| `TD.pine` | Thaisignal - DeMark Time Indicator | TBX-DI |
| `TD_2.pine` | LZH- DeMark Time Indicator Strict Combo | LZH |

## Unchanged Logic

The following behavior is the same in both files:

- Buy and Sell Setup counts 1-9
- Traditional TD Sequential countdown
- Aggressive countdown
- Green `B` and red `S` reversal signals
- Setup trend lines
- Countdown time limits
- Nested setup and countdown handling

Because `TD_2.pine` applies more conditions to C1-C10, it may reject bars accepted by `TD.pine`. Its Combo count can therefore progress more slowly or reach C10 and C13 on different dates.
