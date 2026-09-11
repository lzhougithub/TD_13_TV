# TD DeMark Pine Scripts

This repository contains three TradingView Pine indicators:

- [TD_3.pine](TD_3.pine): the current Pine v6 version, with independent overlapping Combo counts and text-only C10 markers. Its chart name is **LZH3**.
- [TD_2.pine](TD_2.pine): the Pine v2 LZH version with stricter TD Combo rules and additional C10 markers.
- [TD.pine](TD.pine): the original Pine v2 indicator with simplified TD Combo counting.

## TD_3: independent Combo counts

Each buy or sell setup gets a provisional Combo from setup bar 1. If the setup fails before bar 9, its provisional Combo is discarded. If it completes, that same Combo continues with its own count, last qualifying close, and deadline. An older Combo completing C13 cannot reset a newer Combo.

C1–C10 retain TD_2's four strict conditions. C11–C13 only require improvement over that sequence's last qualifying close. A candle can qualify for more than one active sequence, but can advance each sequence only once.

The 24 non-Combo equations are preserved, apart from the declarations, reassignment syntax, and colour namespaces required by Pine v6. The two B/S display calls are commented out, so capital B and S are hidden while their internal calculations remain. The other 48 non-Combo plot calls are preserved, including setups, Sequential/aggressive countdowns, and levels.

### Install in TradingView

1. Open [TD_3.pine](TD_3.pine) and copy its full contents into TradingView's Pine Editor.
2. Save the script as **TD_3**.
3. Remove the old **LZH** indicator from the chart, then add **TD_3**. It appears as **LZH3**.
4. Save the chart layout. When editing an installed copy, use **Update on chart**.

The full indicator title is **LZH_3- DeMark Time Indicator Independent Combo**.

### Chart markers

- **C10:** text only, with no arrow, for both buy and sell Combos.
- **C13:** a green upward arrow for buy Combos and a native red `plotshape(..., style=shape.arrowdown)` for sell Combos, using the same default size as Sell Setup 9 and Sell Countdown 13. C13 captions remain separate labels at the same font size as C10.
- Each sequence gets a separate C10/C13 label, including same-bar and consecutive-bar completions. Marker tooltips show the originating setup and qualifying-candle trail.
- Simultaneous sell C13s share one native arrow on the candle while retaining their separate captions, tooltips, and counts. The arrow uses the first sell C13 caption's price position and resets on each bar.
- Up to 450 drawing labels are retained. Native sell C13 arrows and the four Data Window fields for per-bar buy/sell C10/C13 event totals remain available over the loaded history, beyond that label limit.

### Overlap and cancellation policy

- The interval is counted in chart bars between consecutive completed setups **of the same direction**, never from an opposite-direction setup.
- With the default overlap window of 30, a gap of 29 bars permits coexistence. A gap of 30 bars or more retires older same-direction Combos in favour of the newly completed setup. Setting the window to 0 disables Combo overlap.
- Each confirmed Combo has a deadline at its setup-9 bar plus the countdown limit (48 by default). The deadline bar is eligible; the next bar expires it. A later setup does not extend that deadline.
- An opposite setup 9 cancels the affected Combos before that bar can count.
- Completed Combos stop at 13. New candidates originate only at setup 1.
- These are this project's custom overlap rules; DeMark setup-range recycling has not been added.

### Inspect an originating setup

For the USO daily sell setup completed on 20 August 2026:

1. Open LZH3's Inputs and enable **Show Combo audit**.
2. Set **Direction** to **Sell**.
3. Enter **2026-08-20** in **Setup 9 date (YYYY-MM-DD)**.

The table lists the setup's starting date, completion date, status, Combo count, and the date/close of every qualifying candle. **Include setup date on Combo markers** adds the originating date directly to C10/C13 labels. **Write selected setup audit to Pine Logs** also logs the selected sequence on the last historical bar and each live update.

A blank audit date selects the latest completed setup of the chosen direction. The audit retains the latest 100 retired sequences; an older date may no longer be available. Chart history must include the setup's first candle. Use the same symbol/feed and adjustment settings when comparing charts.

### Live candles

Live values are provisional. Ordinary Pine `var` state rolls back on each tick, so repeated updates cannot accumulate multiple counts on one candle. A live C13 can disappear if the latest price stops qualifying; the closing tick determines the historical result.

### Validation recorded on 10 September 2026

The production script compiled and ran in TradingView. A local Pine regression harness using the exact production Combo engine passed **46 assertions**, covering both directions, qualification conditions, independent references, overlap boundaries, simultaneous/consecutive completions, expiry, cancellation, and snapshot isolation. The test harness and detailed evidence remain in the development workspace and are not included in this repository.

Before hiding B/S, local source checks confirmed preservation of the 24 non-Combo equations and 50 non-Combo plot calls. This was a source comparison, not an exhaustive historical comparison of every non-Combo output. The later C10 text-only display update also compiled and was visually checked in TradingView. The source copied back from Pine Editor matched the local file after normalising line endings.

The B/S display update only comments out those two plot calls; all other Pine source lines remain unchanged. TradingView saved and applied this update as TD_3 version 8 on 11 September 2026. Editor readback confirmed that only lines 320 and 321 changed. The USO daily chart was visually checked: the 24 August S is absent, while setup numbers, countdowns, C10/C13 labels, and arrows remain.

On 11 September 2026, the native sell C13 arrow update compiled and was saved as TD_3 version 11. The 10 September C13 arrow was visually checked on USO daily against the 20 August Setup 9 and 12 August Countdown 13 arrows: they use the same native symbol and default size. The C13 caption font is unchanged. Editor readback verified the updated source; local checks confirmed that the Combo engine and all 24 legacy equations remain unchanged.

On the USO daily chart, the sell setup starting **10 August** and completing **20 August** reached C10 on **3 September**, C11 on **8 September**, C12 on **9 September**, and **C13 on 10 September**. The last candle was still live during validation, so that day's result was provisional.

## Earlier versions: TD.pine and TD_2.pine

The comparison below describes the two Pine v2 reference versions.

### Core Difference

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

### Strict C1-C10 Rules

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

### C11-C13 Rules

For Buy Combo C11-C13, each qualifying close only needs to be lower than the previous qualifying Combo close.

For Sell Combo C11-C13, each qualifying close only needs to be higher than the previous qualifying Combo close.

The other C1-C10 tests are not applied to C11-C13 in `TD_2.pine`.

### Chart Markers

| Marker | `TD.pine` | `TD_2.pine` |
| --- | --- | --- |
| Buy Combo C10 | Not plotted | Green upward marker labelled `C10` |
| Sell Combo C10 | Not plotted | Red downward marker labelled `C10` |
| Buy Combo C13 | Plotted | Plotted |
| Sell Combo C13 | Plotted | Plotted |

### Indicator Name

| File | Full title | Short title |
| --- | --- | --- |
| `TD.pine` | Thaisignal - DeMark Time Indicator | TBX-DI |
| `TD_2.pine` | LZH- DeMark Time Indicator Strict Combo | LZH |

### Unchanged Logic

The following behavior is the same in both files:

- Buy and Sell Setup counts 1-9
- Traditional TD Sequential countdown
- Aggressive countdown
- Green `B` and red `S` reversal signals
- Setup trend lines
- Countdown time limits
- Nested setup and countdown handling

Because `TD_2.pine` applies more conditions to C1-C10, it may reject bars accepted by `TD.pine`. Its Combo count can therefore progress more slowly or reach C10 and C13 on different dates.
