# API Book

This guide version is **1.0.0**

![npm](https://img.shields.io/npm/v/trading-vue-js.svg?color=brightgreen&label=Current%20lib%20version)

### DataCube API is [here](https://coldarra.coding.net/public/trading-vue-js/trading-vue-js/git/files/master/docs/datacube/README.zh.md)
### Inline Shaders API is [here](https://coldarra.coding.net/public/trading-vue-js/trading-vue-js/git/files/master/docs/api/SHADERS.zh.md)
### Index-Based mode API is [there](https://coldarra.coding.net/public/trading-vue-js/trading-vue-js/git/files/master/docs/api/IB.zh.md)
### Std-lib for scripts API is [over there](https://coldarra.coding.net/public/trading-vue-js/trading-vue-js/git/files/master/docs/api/STDLIB.zh.md)

## Props

| Prop                                           | Type    | Description                                 |
| ---------------------------------------------- | ------- | ------------------------------------------- |
| titleTxt                                       | String  | Chart title text                            |
| id                                             | String  | HTML id of root div                         |
| width                                          | Number  | Chart width (px)                            |
| height                                         | Number  | Chart height (px)                           |
| colorTitle                                     | String  | Title text color                            |
| colorBack                                      | String  | Background color                            |
| colorGrid                                      | String  | Grid color                                  |
| colorText                                      | String  | Text (labels) color                         |
| colorTextHL                                    | String  | Highlighted text color                      |
| colorScale                                     | String  | Scales border color                         |
| colorCross                                     | String  | Crosshair color                             |
| colorCandleUp                                  | String  | Green candle color                          |
| colorCandleDw                                  | String  | Red candle color                            |
| colorWickUp                                    | String  | Green wick color                            |
| colorWickDw                                    | String  | Red wick color                              |
| colorWickSm                                    | String  | Scaled down wick color                      |
| colorVolUp                                     | String  | Green volume color                          |
| colorVolDw                                     | String  | Red volume color                            |
| colorPanel                                     | String  | Value bars color                            |
| colors                                         | Object  | All-colors object, has a lower priority     |
| font                                           | String  | Full font string, e.g. "11px Arial..."      |
| data                                           | Object  | Data object                                 |
| overlays                                       | Array   | List of custom overlay classes              |
| chartConfig                                    | Object  | Overwrites chart config values              |
| legendButtons                                  | Array   | Array of legend buttons ids                 |
| toolbar                                        | Boolean | Show toolbar (works with DataCube)          |
| colorTbBack                                    | String  | Toolbar background color                    |
| colorTbBorder                                  | String  | Toolbar border color                        |
| indexBased                                     | Boolean | Index-based rendering mode (global setting) |
| extensions                                     | Array   | Array of extensions                         |
| skin                                           | String  | Skin ID (should be in the extensions)       |
| timezone  <sup style="color:#14b32a">new</sup> | Number  | Shift from UTC, hours                       |


### Legend Button Types

| add                             | remove                          | display                         | up                              | down                            | settings                        |
| ------------------------------- | ------------------------------- | ------------------------------- | ------------------------------- | ------------------------------- | ------------------------------- |
| ![](assets/README-0487d08a.png) | ![](assets/README-6ce736c2.png) | ![](assets/README-cbe4028c.png) | ![](assets/README-bc4359ae.png) | ![](assets/README-ed2c9def.png) | ![](assets/README-b0d2d797.png) |

Pass the following object to define a custom button:
传递以下对象以定义自定义按钮：

```js
{
    name: '<button-id>',
    icon: '<Icon>' // Base64 URL
}
```

# Methods 方法

### resetChart(...) 

Resets the chart to the default state. Use it if you need to reset the time range.
将图表重置为默认状态。如果需要重置时间范围，请使用该方法。

* **Arguments**: resetRange (Boolean, default = **true**) Reset current time range

*Example:*

```HTML
<template>
    <trading-vue ref="tradingVue"></trading-vue>
</template>
<script>
export default {
    mounted() {
        this.$refs.tradingVue.resetChart()
    }
}
</script>
```

### setRange(t1, t2)

Sets custom time range.
设置自定义时间范围。

* **Arguments**: t1 (Number) Left-bound of the range
* **Arguments**: t2 (Number) Right-bound of the range

*Example:*

```HTML
<template>
    <trading-vue ref="tradingVue"></trading-vue>
</template>
<script>
export default {
    mounted() {
        // Wait until chart is fully loaded
        this.$nextTick(() =>
            this.$refs.tradingVue.setRange(t1, t2)
        )
    }
}
</script>
```

### getRange()

Gets current timerange.
获取当前时间范围。

* **Returns**: [t1, t2] (Array) Current timerange

### goto(t)

Goto to a specific timestamp
转到特定时间戳

* **Arguments**: t (Number) Target timestamp

### getCursor()

Gets the cursor [object](https://coldarra.coding.net/public/trading-vue-js/trading-vue-js/git/files/master/docs/api/README.zh.md#cursor-data)

* **Returns**: Object

### showTheTip()

Shows a pop-up with specified text.
显示带有指定文本的弹出窗口。

* **Arguments**: text (String) Text
* **Arguments**: color (String) Color

### $refs.chart.hooks(...args)

Set hooks to the internal events. You'll get events like this `?<hook name>`
设置内部事件hook。你会得到`?<hook name>`这样的事件

* **Arguments**: ...args (Strings) hook names

Currently supports: `xchanged, xlocked, update, resize, data`
目前支持: `xchanged, xlocked, update, resize, data`

## Events

### legend-button-click

Emitted when user clicks on the legend button. Event format:
当用户单击图例按钮时发出。事件格式：

| Prop      | Type   | Description                          |
| --------- | ------ | ------------------------------------ |
| button    | String | Button type, e.g. 'display'          |
| dataIndex | Number | Data index in onchart/offchart array |
| grid      | Number | Grid id (0 is the main grid)         |
| overlay   | String | Overlay id, e.g. EMA_0               |
| type      | String | Indicator type (onchart/offchart)    |

*Example:*

```html
<trading-vue
    :legend-buttons="['display']"
    v-on:legend-button-click="on_button_click">
</trading-vue>
<script>
export default {
    methods: {
        on_button_click(event) {
            // ...
        }
    }
}
</script>
```

### custom-event 自定义事件

Can be emitted from overlay or **trading-vue** itself. Propagates all the way up to the root component; must have a unique name. Some names are reserved:
可以从叠加图层或**vue**本身发出。一直传播到根组件；必须具有唯一的名称。保留字：
```
register-tools, tool-selected, grid-mousedown, drawing-mode-off, change-settings,
scroll-lock, object-selected, remove-tool, before-destroy, new-shader, remove-shaders,
remove-layer-meta, data-len-changed
```

*Example:*

Somewhere in overlay:
```js
this.$emit('adios-aloha', x1, x2)
```

Event listener:

```html
<trading-vue @adios-aloha="on_arrival">
<script>
    on_arrival(x1, x2) { ... }
</script>
```

## Data structure 数据结构

Data structure v1.3

IMPORTANT: All data must be sorted by time (in ascending order). The main OHLCV must not contain duplicate timestamps.
重要提示：所有数据必须按时间（升序）排序。主OHLCV不能包含重复的时间戳。

```js
{
    "chart": {   // 强制的
        "type": "<Candles|Spline>",
        "indexBased": <true|false>, // 基于索引的渲染模式
        "data": [
            [timestamp, open, high, low, close, volume],
            ...
        ],
        "tf": <Number|'1s'...'1Y'>, // 强制时间表
        "grid": { // 栅格设置（可选）
            "logScale": <true|false>,
            "height": <Number>, // 栅格高度（宽度）
            "id": // New grid id (merge w/another, offchart only) 栅格id（与另一个合并，仅在图表外）
        }
        "settings": { } // Settings (depending on "type")取决于“type”
    },
    "onchart": [ // Displayed ON the chart 显示在图表上
        {
            "name": "<Indicator name>", // Or template, e.g. "RSI, $length"
            "type": "<e.g. EMA, SMA>",
            "data": [
                [timestamp, ... ], // Arbitrary length
                ...
            ],
            "settings": { }, // Arbitrary settings format 任意设置格式
            "grid": { }, // The same as in "chart",
            "tf": <Number|'1s'...'1Y'>, // Forced timeframe
            "loading": <true|false>, // Show loading animation
            "script": { } // See 'script settings',
            "scripts": <true|false> // Enable/disable scripts
        },
        ...
    ],
    "offchart": [ // Displayed BELOW the chart
        // The same as "onchart"
    ],
    "datasets": [ // Hidden data (used by scripts)
        {
            "type": "<Dataset Type>",
            "id": "<Dataset Id>",
            "data": [ ... ] // Initial data
        },
        ...
    ],
    /* The Properties below are automatically created by DataCube,
     * but can be defined / changed manually, for example,
     * when you need to set a default color for a tool. 
     下面的属性由DataCube自动创建，但可以手动定义/更改，例如，当您需要为工具设置默认颜色时。*/
    "tools": [ // Tool presets (colors, data, icons, see LineTool.vue)
        {
            "type": "<Tool type, e.g. LineTool:Segment>",
            "group": "<Tool Group>", // WIP
            "hint": "Tool Hint", // WIP
            "settings": {
                "color": "#35c460",
                ...
            },
            "icon": "Data Url",
            "data": [ ... ] // Initial data
        },
        ...
    ],
    "tool": "<Tool type, e.g. Cursor>", // Current tool
    "selected": "<Selected object id>",
    "drawingMode": <true|false>
}
```

### Script Settings

| Prop         | Type           | Description                                             |
| ------------ | -------------- | ------------------------------------------------------- |
| execOnRange  | Boolean        | Exec script on 'range-changed', default=false           |
| execInterval | Number         | Exec-on-range interval, default=0 (immediatly)          |
| output       | Boolean/String | Timeseries output: `true, false, 'range'`, default=true |


## Overlay api

Data for building overlays. Defined in `mixins/overlay.js`, accessed through overlay's `this.$props`.

*All properties reactive*

| Prop     | Type   | Description                                          |
| -------- | ------ | ---------------------------------------------------- |
| id       | String | Overlay unique id (within current grid) ,e.g 'EMA_1' |
| num      | Number | Overlay unique num (within current grid)             |
| interval | Number | Candlestick interval, ms (e.g. 1 min = 60000 )       |
| cursor*  | Object | Crosshair position and selected values, see below    |
| colors   | Object | All colors from `TradingVue.vue` combined            |
| layout** | Object | Layout API object, see below                         |
| sub      | Array  | Current subset of candlestick data                   |
| data     | Array  | Current subset of indicator data                     |
| settings | Object | Indicator's settings, defined in `data.json`         |
| grid_id  | Number | Current grid id                                      |
| config   | Object | Chart config, see 'constants.js'                     |
| meta     | Object | Contains the last price and other info               |
| i0       | Number | The first global index of the current subset         |

### Cursor data*

| Field       | Type    | Description                                   |
| ----------- | ------- | --------------------------------------------- |
| x           | Number  | Current x position (px)                       |
| y           | Number  | Current y position (px)                       |
| t           | Number  | Current timestamp (ms)                        |
| y$          | Number  | Current price level                           |
| grid_id     | Number  | Current grid id                               |
| locked      | Boolean | *true* during scrolling, *false* otherwise    |
| values      | Object  | Current indicator values in a specific format |
| scroll_lock | Boolean | True when scrolling is locked (drawing mode)  |

#### Values format

```js
values: {
    "<grid_id>": {
        "ohlcv": [ ... ] | undefined,
        "<overlay_id>": [ ... ],
        ...
    },
    ...
}
```

*Example:*

```js
{
    0: {
        ohlcv: [1553378400000,4051.3,4063.9,4051.3,4063.8,259.61602371],
        EMA_0: [1553378400000,4054.9315737970815],
        EMA_1: [1553378400000,4057.3817565994727]
    },
    1: {
        ohlcv: undefined,
        RSI_0: [1553378400000,53.148999912870806]
    }
}
```

### Layout API**

#### Layout object

Defined in `layout.js`, accessed through overlay's `this.$props.layout`.

| Field   | Type   | Description                                                                                                                                              |
| ------- | ------ | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| $_hi    | Number | Upper bound of price-range                                                                                                                               |
| $_lo    | Number | Lower bound of price-range                                                                                                                               |
| $_step  | Number | Grid price step                                                                                                                                          |
| $_mult  | Number | Grid price multipler ([log-scale mode](https://coldarra.coding.net/public/trading-vue-js/trading-vue-js/git/files/master/docs/faq/README.zh.md#log-scale-no))                       |
| t_step  | Number | Grid time step                                                                                                                                           |
| A       | Number | Scale transform coefficient                                                                                                                              |
| B       | Number | Offset transform coefficient                                                                                                                             |
| id      | Number | Grid id                                                                                                                                                  |
| prec    | Number | Sidebar precision (decimals after point)                                                                                                                 |
| height  | Number | Grid height (px)                                                                                                                                         |
| width   | Number | Grid width (without sidebar, px)                                                                                                                         |
| offset  | Number | Grid offset from the top (px)                                                                                                                            |
| px_step | Number | Candlestick step (px)                                                                                                                                    |
| sb      | Number | Sidebar width                                                                                                                                            |
| spacex  | Number | Drawing area width (px)                                                                                                                                  |
| startx  | Number | First candle position (px)                                                                                                                               |
| ti_map  | Object | Time-index mapping for [IB mode](https://coldarra.coding.net/public/trading-vue-js/trading-vue-js/git/files/master/docs/faq/README.zh.md#what-is-the-index-based-ib-rendering-mode) |
| candles | Array  | Candles subset                                                                                                                                           |
| volume  | Array  | Volume bars positions and sizes                                                                                                                          |
| xs      | Array  | vertical grid lines `[[x, candle], ...]`                                                                                                                 |
| ys      | Array  | horizontal grid lines `[[y, price], ...]`                                                                                                                |

#### Mapping functions

Defined in `layout_fn.js`, accessed through overlay's `this.$props.layout`.
在`layout_fn.js`中定义，通过覆盖图层`this.$props.layout`访问`.

#### $2screen($)

Returns y-coordinate for given price
返回给定价格的y坐标

* **Arguments**: price (Number)
* **Returns**: pixels (Number)

#### t2screen(t)

Returns x-coordinate for given timestamp
返回给定时间戳的x坐标

* **Arguments**: time (Number)
* **Returns**: pixels (Number)

#### screen2$(y)

Returns price for given y-coordinate
返回给定y坐标的价格

* **Arguments**: y (Number)
* **Returns**: price (Number)

#### screen2t(x)

Returns time for given x-coordinate
返回给定x坐标的时间

* **Arguments**: x (Number)
* **Returns**: time (Number)

#### t_magnet(t)

Returns x-coordinate of nearest candle for given time
返回离给定时间最近的K线柱子的x坐标

* **Arguments**: time (Number)
* **Returns**: pixels (Number)

#### c_magnet(t)

Returns nearest candle for given time
返回给定时间内最近的K线柱子

* **Arguments**: time (Number)
* **Returns**: candle (Object)

```js
Candle = {
    x,  // c-coordinate (px)
    w,  // width (px)
    o,  // open (px)
    h,  // high (px)
    l,  // low (px)
    c,  // close (px)
    raw // Candle data e.g. [1553378400000, ...]
}
```
<br>

*Example:*

```js
const layout = this.$props.layout

ctx.beginPath()

for (var p of this.$props.data) {

    let x = layout.t2screen(p[0])
    let y = layout.$2screen(p[1])

    ctx.lineTo(x, y)

}

ctx.stroke()
```

### Overlay meta-methods

To change the default behaviour of an overlay, override this methods.
若要更改覆盖图层的默认行为，请重写此方法。

#### init()

Called when overlay is mounted.
覆盖图层准备好时调用init()。

#### meta_info()

Defines plugin version and other useful information. *Optional*, required for publishing.
定义插件版本和其他有用信息。*可选*，对外发布时需要。

```js
meta_info() {
    return {
        author: 'Satoshi Smith',
        version: '1.0.0',
        contact: '<email>',      // (opt)
        github: '<GitHub Page>'  // (opt)
    }
})
```

#### draw(ctx)

Your custom drawing function. **Required**
自定义绘图功能。**必需的**

* **Arguments**: ctx (Canvas context)

```js
draw(ctx) {
    ...
}
```

#### pre_draw(ctx), post_draw(ctx)

Override if you need them.
如果需要可以重载。

* **Arguments**: ctx (Canvas context)


#### use_for()

* **Returns**: list of indicator types which this overlay can draw. **Required**
* **返回**：此覆盖图层可以绘制多个指标。**必需的**
The best practice is to include a generic type first and then a specific, e.g.
最好是先包含泛型类型，然后包含特定类型，例如。

```js
use_for() {
    return ['Spline', 'EMA', 'SMA']
}
```

#### data_colors()

* **Returns**: List of data colors in the same order as corresponding values. *Optional*
* **返回值**：与相应值顺序相同的数据颜色列表。*可选*

```js
data_colors() {
    return ['red', '#ff0000', '#ff000055']
}
```

#### y_range(hi, lo)

Custom y-range. *Optional*
自定义y范围。*可选*

* **Arguments**:
    - hi (Number) Upper bound of calculated y-range
    - lo (Number) Lower bound of calculated y-range
* **Returns**: New or transformed y-range. Use when you need to change the scaling behavior of y-axis, e.g.:
* **返回值**：新的或转换的y坐标范围。当需要更改y轴的缩放行为时使用，例如：

```js
y_range(hi, lo) {
    return [
        Math.max(hi, 70),
        Math.min(lo, 30)
    ]
}
```

#### legend(values) 图例

Custom legend formatter. *Optional*
自定义图例格式化程序。*可选*

* **Arguments**: values (Array) Current cursor values
* **参数**：当前光标值/数组
* **Returns**: Formated values array, e.g.
* **返回值**：格式化的值数组，例如。


```js
legend(values) {
    return [
        {
            value: values[1].toFixed(0),
            color: 'green'
        },
        {
            value: values[2]
        }
    ]
}
```

#### tool()

* **Returns:** Tool descriptor. See `LineTool.vue`:
* **返回值**工具描述符。见`LineTool.vue`:

```js
tool() {
    return {
        // Descriptor for the LineTool
        group: 'Lines', // Tool group (WIP)
        icon: Icons['segment.png'], // Data URL (png 25x25 or svg)
        type: 'Segment', // Tool type (in addition to overlay type)
        hint: 'This hint will be shown on hover', // WIP
        data: [],     // Default data
        settings: {}, // Default settings
        // Modifications
        mods: {
            'Extended': {
                // Rewrites the default settings field
                settings: { extended: true },
                icon: Icons['extended.png']
            }
        }
    }
}
```

<br>

#### mousemove(event)
*Optional*
* **Arguments**: event (Object) Vue mouse event

#### mouseout(event)
*Optional*
* **Arguments**: event (Object) Vue mouse event

#### mouseup(event)
*Optional*
* **Arguments**: event (Object) Vue mouse event

#### mousedown(event)
*Optional*
* **Arguments**: event (Object) Vue mouse event

#### keyup(event)
*Optional*
* **Arguments**: event (Object) Native keyup event

#### keydown(event)
*Optional*
* **Arguments**: event (Object) Native keydown event

#### keypress(event)
*Optional*
* **Arguments**: event (Object) Native keypress event

### Overlay I/O objects

#### mouse

Allows you to add multiple mouse listeners.

| Prop/Method | Type     | Description                                                                   |
| ----------- | -------- | ----------------------------------------------------------------------------- |
| pressed     | Boolean  | Left mouse button status                                                      |
| listeners   | Number   | Listener count                                                                |
| on          | Function | Registers a listener: `function on(event, callback, queuePosition="unshift")` |
| x           | Number   | Current screen position                                                       |
| y           | Number   | Current screen position                                                       |
| t           | Number   | Current timestamp                                                             |
| y$          | Number   | Current price level                                                           |


#### keys
*(Available for Tool overlays)*
*(可用于工具覆盖)*
Allows you to add multiple keyboard listeners.
允许添加多个键盘侦听器。

| Prop/Method | Type     | Description                                                         |
| ----------- | -------- | ------------------------------------------------------------------- |
| pressed     | Function | Returns `true` if button is pressed: `function pressed(buttonName)` |
| listeners   | Number   | Listener count                                                      |
| on          | Function | Registers a listener: `function on(buttonName, callback)`           |
