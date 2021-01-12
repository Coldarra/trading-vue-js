# Datasets API

Dataset is an element of DataStructure that is completely invisible to the rendering engine, but can be accessed from scripts (the data lives in web worker):
数据集是一种数据结构，它对于渲染引擎完全不可见，你可以从脚本访问它（数据位于 web worker 中）：

```js
this.chart = new DataCube({
    // ...
    datasets: [{
        type: 'Trades',
        id: 'binance-btcusdt',
        data: [...]
    }]
})
```

When you create a new dataset, its data is sent to the web-worker and immediately deleted from the structure (we don't need Vue to sniff out its existence).
After you added the initial dataset object, the reference will appear in `dss`:
创建一个新的数据集时，数据被发送到 web worker，并立即从结构中删除（不需要 Vue 来感知数据变动）。
初始化数据集对象后，它将会被引用在“dss”中：

```js
dc.dss["binance-btcusdt"]; // => Dataset {...}
```

## Dataset Methods

### set(data, exec = true)

Sets new data array (overwrites it) in web worker.
在 web worker 中设置新的数据数组（覆盖原数据）。

- **Arguments**: data (Data array)
- **Arguments**: exec (Exec the scripts, default = true)

### update(arr)

Updates the set with an array of data points. (script `update` is not called).
使用一个数据点数组更新数据集。（未调用脚本“update”）。

- **Arguments**: arr (Data array)

Alternatively all datasets can be updated in parallel with `dc.update` method:
或者让所有数据集同时调用`update`方法：

```js
dc.update({
    'datasets.dataset-id-1': [...], // Only fresh points added,
    'datasets.dataset-id-2': [...], // similarly to merge()
    'datasets.dataset-id-3': [...]
})
```

To trigger the `update` functions of the scripts, in addition send an array of the latest `ohlcv` points:
为了触发脚本的“update”函数，需要发送最新的`ohlcv`数据：

```js
dc.ww.just('update-data', {
    'datasets.dataset-id-1': [...],
    'datasets.dataset-id-2': [...],
    'datasets.dataset-id-3': [...],
    'ohlcv': [...] // Preferably the last two candles
})
```

### merge(data, exec = true)

Sends WW a chunk to merge. The merge algo here is simpler than in DC. It just adds data at the beginning or/and the end of dataset (cuts overlaps).
向 WW 发送要合并的数据块。这里的合并算法比在 DC 中简单。它只是在数据集的开头或/和结尾添加数据（替换重叠部分）。

- **Arguments**: data (Data array)
- **Arguments**: exec (Exec the scripts, default = true)

### remove(exec = true)

Removes a dataset from existence.
删除 dataset。

- **Arguments**: exec (Exec the scripts, default = true)

## Fetching WW data 获取 WW 数据

Executing `dc.get('datasets')` will give you dataset descriptors only (w/o the data).
执行`dc.get('datasets')`将只提供数据集描述符（w/o 数据）。

If you need to fetch the data itself, you need to specify `.data` in your query, e.g.:
如果要获取数据本身，则需要在查询中指定`.data`，例如：

```js
dc.get("datasets.binance-btcusdt.data"); // => Promise of array
dc.get("datasets..data"); // All datasets, array of Promises
```

**WARNING:** if you uploaded gigabytes of data it will likely make the query pretty slow.

## Limiting RAM

Datasets allow you to keep data for multiple symbols at the same time. For example, you don't need to reload all data every time user switches between `BTC/USD` & `LTC/USD`. Instead you can keep uploading new datasets and updating them (through `merge`) for the active symbol.
数据集允许同时保留多组数据。例如，用户在`BTC/USD`和`LTC/USD`之间切换时不需要每次都重新加载数据。相反，可以继续更新 dataset（通过`merge`）。

For this reason there is a max-size limit for all datasets:
因此，所有数据集都有一个大小限制：

```js
let dc = new DataCube(
  {},
  {
    ww_ram_limit: 500 // MB, 0 for no limit
  }
);
```

Then, each time you upload new dataset, all sets will be purged by the `last_upd` time. To force this operation send an empty `upload-data` event:
每次上传新数据集时，所有集合都将在`last_upd`时清除。如果要强制清空，请发送空的`upload-data`事件：

```js
dc.ww.just("upload-data", {});
```
