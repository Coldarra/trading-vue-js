# Inline Shaders API 内联着色器

Inline shaders can be used to draw stuff on top of the sidebar and botbar. The inline shader is a simple object that should be emitted **once** from your overlay:
内联着色器可用于在侧边栏和底部绘制内容。内联着色器是一个简单的对象，在覆盖时有**一次**触发：

```js
class Shader {
    constructor(target, draw, name) {
        this.target = target // Where to apply ('sidebar|botbar')
        this.draw = draw  // arrow function ctx => {}
        this.name = name // optional
        this.id = null // Generated automatically
    }
}

// In you overlay:
this.$emit('new-shader', new Shader('sidebar', ctx => {...}))

// Or simply
this.$emit('new-shader', {
    target: 'sidebar',
    draw: ctx => {...}
})
```

As you can see, here we use an arrow function. This is why the shader is called _inline_, it has access to multiple contexts. For example, if you need to draw something on the bottom bar, and you also want to use your main overlay's layout, here is a way to implement this:
如你所见，这里我们使用箭头函数。这就是为什么着色器被称为* 内联*，它可以访问多个上下文。例如，如果您需要在底部栏上绘制一些内容，并且您还希望使用主覆盖的布局，下面是一种实现此功能的方法：

```js
// init() called once
init() {
    let layout = this.$props.layout
    let t = 1577836800000

    this.$emit('new-shader', {
        target: 'botbar',
        // ctx is a context of the bottom bar
        draw: ctx => {
            let x = layout.t2screen(t)
            let w = ctx.canvas.width  // botbar width
            let h = ctx.canvas.height // botbar height
            /* draw x with Canvas API */
        }
    })
}
```

It is also possible to dynamically pull data from your main overlay:
还可以从主覆盖图层中动态提取数据：

```js
init() {
    let layout = this.$props.layout

    this.$emit('new-shader', {
        target: 'botbar',
        draw: ctx => {
            let x = layout.t2screen(this.time())
            /* draw x with Canvas API */
        }
    })
},
time() {
    return 1577836800000
}
```

Both `sidebar` and `botbar` shaders updated and removed automatically by the lib.
`sidebar`和`botbar`着色器都由 lib 自动更新和删除。

## Background Shaders

Very similar to the inline type, but use `props` as a data source.
与内联着色器非常相似，但这个使用了`props`作为数据源。

```js
export default class BackShader {
  constructor() {
    this.target = "grid"; // Where to apply ('sidebar|botbar|grid')
    this.name = "BackShader";
    this.id = "BackShader";
    this.zIndex = -1; // Order
    this.owner = null; // Skin / extension (set automatically)
  }

  draw(ctx, props) {
    // props contains (for main grid):
    // { layout, range, interval tf, cursor,
    // colors, sub, font, config, meta }
    // (for sidebar & botbar):
    // { layout, cursor }
  }
}
```
