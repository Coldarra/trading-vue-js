# DataCube API

This guide version is **0.10.0**

![npm](https://img.shields.io/npm/v/trading-vue-js.svg?color=brightgreen&label=Current%20lib%20version)

**DataCube** [WIP] is a helper class designed for data manipulation. `Trading-vue` component provides only rendering functionality, but with the help of DC it also enables features such as real-time chart updates, indicator calculations and drawing tools (and much more).
**DataCube** [WIP] 是一个为数据操作而设计的助手类。`Trading vue`组件只提供渲染功能，但在DataCube的帮助下，它还支持实时图表更新、指标计算和绘图工具等功能（等等更多功能）。

Use DC to wrap your data object:
使用DataCube包装数据对象：

```html
<template>
    <trading-vue :data="chart"></trading-vue>
</template>
<script>

import { TradingVue, DataCube } from 'trading-vue-js'

export default {
    components: { TradingVue },
    data() {
        return {
            chart: new DataCube({
                chart: {}, onchart: [], offchart: []
            })
        }
    }
}
</script>

```

TVJS automatically detects that you are a smart person who wants to use this lib like a PRO.
希望您像专业人士一样使用这个库。

*Note: During initialization the DC performs some data modifications, so your data structure may change. With best intentions only.*
*注意：在初始化过程中，DataCube会执行一些数据修改，因此您的数据结构可能会更改。这只是出于好意*

## Properties

| Prop | Type | Description |
|---|---|---|
| data | Object | Original chart data. Use it for direct access 原始图表数据。可以直接使用。  |
| tv | Component | Reference to `trading-vue` component 对`trading-vue`组件的引用 |
| sett  | Object  | DC Settins object |
| se_state | Object | Script Engine state |
| ww  | Object  |  Web-worker interface |


## Settings

```js
new DataCube({
    // Data
}, {
    // Settings
})
```

| Sett | Type | Description |
|---|---|---|
| aggregation | Number | Update aggregation interval, default = 100  |
| script_depth | Number | 0 === Exec on all data, default = 0 |
| auto_scroll | Boolean  | Auto scroll to a new candle, default = true |
| scripts | Boolean | Enable overlays scripts, default = true |
| node_url | String | Use node.js instead of WW |
| shift_measure | String  | Shift+click measurment, default = true |
| ww_ram_limit <sup style="color:#14b32a">new</sup> | Number  | WebWorker RAM limit (MB), default = 0 |


## Query system

All overlays, settings and candlestick data can be accessed with a simple query language. For example, if you want to get all overlays chilling in DC, call this:
所有的覆盖图层、设置和K线数据可以请求一个简单的查询语句。例如，如果要在DC中获取所有覆盖图层，请调用以下命令：

```html
<trading-vue :data="dc"></trading-vue>
<script>
...
    data() {
        return {
            // Data - see Data Structure v1.1
            dc: new DataCube(Data),
        }
    }
...
</script>
```

```js
// 'dc' is shortcut for 'this.dc'

dc.get('.') // -> [{id: "onchart.Spline0", settings: {...}}, ...]
```


If you need only the main chart:
如果只需要主图表：

```js
dc.get('chart') // -> [{id: "chart.Candles", data: [...]}]
dc.get('chart.data') // -> [Array(96)]
```

If you have a bunch of overlays with unique names/types, you can use only one keyword:
如果有一组具有唯一名称/类型的覆盖图层，则只能使用一个关键字：

```js
dc.get('EMA') // -> [{id: "onchart.Spline0", name: "EMA", ...}]
dc.get('Spline') // -> [{id: "onchart.Spline0", name: "EMA", ...}]
```

But you can also be more specific:
但你也可以更具体一些：

```js
dc.get('onchart.Spline')  // -> [{id: "onchart.Spline0", name: "EMA", ...}]
dc.get('onchart.EMA.data')  // -> [Array(80)]
dc.get('offchart.EMA')  // -> Nope
```

For all the methods below - if you see `query` in arguments you simply use the query lang!
对于下面的所有方法-如果在参数中看到`query`，只需使用query lang！

```js
dc.merge('.settings', {color: 'green'}) // -> Makes everything green
dc.hide('.') // -> Hides all overlays
```

## Methods

### add (side, overlay)

Adds a new overlay to the selected array reactively.
添加新的覆盖图层到可选列表中。

* **Arguments**:
    - side (String) "onchart" or "offchart"
    - overlay (Object) Overlay descriptor
* **Returns**: Overlay datacube id


*Example:*

```js
dc.add('onchart', {
    type: 'Spline',
    name: 'EMA 25',
    data: [ ... ],
    settings: {
        lineColor: 'green'
    }
}) // -> "onchart.Spline0"
```

### get (query)

Gets all objects matching the query.
获取查询到的所有对象。

* **Arguments**: query (String)
* **Returns**: Array of objects

```js
dc.get('onchart.Spline')  // -> [{id: "onchart.Spline0", name: "EMA", ...}]
```

### get_one (query)

Gets first object matching the query.
获取查询到的第一个对象。

* **Arguments**: query (String)
* **Returns**: Array of objects

```js
dc.get_one('chart') // -> Chart object
dc.get_one('chart.data') // -> ohlcv data [ ... ]
dc.get_one('onchart.Spline')  // -> {id: "onchart.Spline0", name: "EMA", ...}
dc.get_one('onchart.Spline.data')  // -> [ ... ]
```

### set (query, data)

Changes values of selected objects.
更改选定对象的值。

* **Arguments**:
    - query (String)
    - data (Object|Array) New value

*Examples:*

```js
// Reset candles
dc.set('chart.data', ohlcv)

 // Change the entire chart object
dc.set('chart', {
    type: "Candles",
    data: [ ... ],
    settings: {}
})

// Apply new settings to all splines
dc.set('onchart.Spline.settings', {
    lineWidth: 2,
    color: 'green'
})

// Change the data of a specific overlay
dc.set('onchart.EMA0.data', [ ... ])
```

### merge (query, data)

Merges objects pulled by query with new data. Objects can be of type `Object` or `Array`.
If the type is `Array`, DC will first consider the data as time series and try to combine them by timestamp.
将查询提取的对象与新数据合并。对象可以是`Object`或`Array`类型。
如果类型为`Array`，DC将首先将数据视为时间序列，并尝试按时间戳组合它们。

* **Arguments**:
    - query (String)
    - data (Object|Array) New value

*Note: time series must be sorted before merging*
*注意：合并前必须对时间序列进行排序*

*Examples:*

```js
// Merge new settings with existing ones
// (old properties will be overridden)
dc.merge('onchart.Spline.settings', {
    lineWidth: 2,
    color: 'green'
})

// Merge as time series
dc.get('chart.data') // -> [[10001, 1, 1, 1, 1 ], [10002, 1, 1, 1, 1 ]]
dc.merge('chart.data', [
    [10002, 2, 2, 2, 2 ], [10003, 3, 3, 3, 3 ]
])
dc.get('chart.data') // ->
// [[10001, 1, 1, 1, 1 ], [10002, 2, 2, 2, 2 ], [10003, 3, 3, 3, 3 ]]

```

### del (query)

Removes all overlays matching query.
删除与查询到的所有覆盖图层。

* **Arguments**:
    - query (String)

```js
dc.del('.') // Remove everything (except the main chart)
dc.del('Spline') // Remove all overlays with id/name 'Spline'
```

### update (data)

Updates/appends a data point, depending on the timestamp (or current time).
根据时间戳（或当前时间）更新/追加数据点。

* **Arguments**:
    - data (Object) Specifies an update, see examples below 指定更新，请参见下面的示例
* **Returns**: (Boolean) **true** if a new candle is formed

```js
// Update with a trade stream:
dc.update({
    price: 8800,
    volume: 22,
    'EMA': 8576, // query => value
    'BB': [8955, 8522] // query => [value, value, ...]
})
// Update with full candlestick:
dc.update({
    candle: [1573231698000, 8750, 8900, 8700, 8800, 1688],
    'EMA': 8576, // query => value
    'BB': [8955, 8522] // query => [value, value, ...]
})
// Update with ohlcv (auto time):
dc.update({
    candle: [8750, 8900, 8700, 8800, 1688],
    'EMA': 8576, // query => value
    'BB': [8955, 8522] // query => [value, value, ...]
})
```

### lock (query)

Excludes specific query from results (for all query-based methods).
从结果中去除特定查询（对于所有基于查询的方法）。

* **Arguments**:
    - query (String)

```js
dc.lock('onchart.Spline')
dc.get('onchart.Spline')  // -> []
```

### unlock (query)

Enables the query back.
恢复被去除的特定对象。

* **Arguments**:
    - query (String)

```js
dc.unlock('onchart.Spline')
dc.get('onchart.Spline')  // -> [{id: "onchart.Spline0", name: "EMA", ...}]
```

### show (query), hide (query)

Show/hide all overlays by query.
按查询显示/隐藏所有覆盖图层。

* **Arguments**:
    - query (String)

```js
dc.hide('.')
dc.show('onchart.Spline')
```
