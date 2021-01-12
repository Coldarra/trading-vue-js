# Index-based mode 基于索引的模式

By default TVJS uses time as the x-coordinate. For candlestick charts like BTCUSD this works well, because there are no time gaps & every candle has a unique timestamp. But if you try to plot stocks data or build Renko charts, you'll see the weekend gaps, or a total mess in the second case.
默认情况下，TVJS 使用时间作为 x 坐标。因为没有时间间隔，而且每根柱子都有一个唯一的时间戳，对于像 BTCUSD 这样的蜡烛图来说很有效。但是，如果你试图绘制股票数据或建立 Renko 图表，会看到周末的缺口，在第二种情况下甚至完全是一团糟。

The solution is to use the candle index instead of time, so the gaps are collapsed and Renko candles are placed at the same distance.
解决方法是使用 K 线索引替代时间索引，这样可以消除间隙保证柱子间距相同。

<br>
<center>

![](assets/IB.gif)

</center>
<br>

## In-Depth explanation

When you set `indexBased: true` in the data or in the main component props, the lib replaces time with the index internally. To make some sense of this situation a special TI mapping (time-index) is used. The `ti_map` object converts time and indices back & forth.
在数据或主组件 props 中设置`indexBased: true`时，会在内部用 K 线索引替代时间索引。使用了一种特殊的 TI 映射（时间索引），`ti_map`对象来回转换时间和索引。

If you need to access the map from outside it can be done with the following code:
如果需要从外部访问映射，可以使用以下代码：

```js
// tvjs === the main component
// Converts index => time
tvjs.$refs.chart.ti_map.i2t(index);

// You guessed it right
tvjs.$refs.chart.ti_map.t2i(time);

// If x < 4294967296 (Max array length)
//  it returns index
// else
//  it converts x to index
tvjs.$refs.chart.ti_map.smth2i(x);
```

Inside overlay you'll find it in the `layout` [object](https://coldarra.coding.net/public/trading-vue-js/trading-vue-js/git/files/master/docs/api#layout-object).

## 3 MODES of index calculation for overlays/subcharts 覆盖图层/附图的 3 种索引计算模式

There are 3 ways the library can calculate indices for overlays (form the main chart data):
可以通过 3 种方式计算覆盖图层的索引（构成主图表数据）：

```js
{
    "type": "Spline",
    "data": [...],
    "indexSrc": "<mode>"
}
```

- **map** -> uses TI mapping functions to detect the index (slowest, for stocks only. DEFAULT). It uses interpolation/extrapolation, so it can correctly calculate data points placed in-between candles. It's all good but the math is quite heavy.
  使用 TI 映射函数（速度最慢，仅适用于股票。默认）。 由于使用了插值/外推，因此可以正确计算 K 线柱子之间需要放置的数据点。这个很好，但计算量很大。

- **calc** -> calculates a shift between sub & data (faster, but overlay data should be perfectly align with the main chart, 1-1 candle/data point. Supports Renko).
  计算数据之间的偏移（更快，但覆盖图层数据应与主图 K 线柱子/数据点一一对应完全对齐。支持 Renko）。

  This method matches the timestamps between the main chart and the overlay, detecting the shift between index spaces (`*` means data timestamp):
  此方法匹配主图表和覆盖图层之间的时间戳，检测索引空间之间的偏移（`*`表示数据时间戳）：

```
Main data: [ * * * * * * * * * * * * * ]
Overlayzz: [ <shift> * * * * * * * * * ]
```

Sadly, it doesn't support scenerios like this one:
遗憾的是，它不支持这样的场景：

```
Main data: [ * * * * * * * * * * * * * ]
Overlayzz: [ <shft> * * * * * * * * * ]
```

<br>

- **data** -> overlay data should come with candle indices (fastest, supports Renko).
  覆盖数据应该带有 K 线柱子索引（最快，支持 Renko）。

Using the first two you provide the overlay data as usual with timestamps:
使用前两个选项，你可以像平常一样提供带有时间戳的覆盖图层数据：

```js
[
  [1543572000000, 4079.63],
  [1543575600000, 4076.08],
  [1543579200000, 4074.51],
  [1543582800000, 4073.33],
  [1543586400000, 4072.3]
];
```

For the third you should use the candle index instead (the index of the nearest candle to a corresponding data-point):
对于第三种情况，应使用 K 线柱子索引（距离相应数据点最近的 K 线柱子索引）：

```js
[[3, 4079.63], [4, 4076.08], [5, 4074.51], [6, 4073.33], [7, 4072.3]];
```

For almost all scenarios it's recommended to just feed a ready-to-use candle index. It requires a bit of data preprocessing, but it's the fastest method that supports all the good stuff, even fractional indices:
对于几乎所有的场景，建议只输入一个现成的 K 线柱子索引。它需要一些数据预处理，但它是支持所有情况的最快的方法，即使是带小数点的索引：

```js
[[3.5, 4079.63], [4.0, 4076.08], [4.5, 4074.51], [5.0, 4073.33], [5.5, 4072.3]];
```
