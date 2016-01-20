
{{target: series-treemap}}

# series.treemap(Object)

[Treemap](https://en.wikipedia.org/wiki/Treemapping) 是一种常见的表达『层级数据』『树状数据』的可视化形式。它主要用面积的方式，便于突出展现出『树』的各层级中重要的节点。

**示例：**

~[700x580](${galleryViewPath}treemap-obama&edit=1&reset=1)


<br>
**视觉映射：**

treemap 首先是把数值映射到『面积』这种视觉元素上。

此外，也支持对数据的其他维度进行视觉映射，例如映射到颜色、颜色明暗度上。

{{ use: partial-treemap-visual-detial }}


<br>
<br>
<br>
注：treemap 的配置项 和 ECharts2 相比有一些变化，一些不太成熟的配置方式不再支持或不再兼容：

+ `center/size` 方式的定位不再支持，而是统一使用 `left/top/bottom/right/width/height` 方式定位。

+ `breadcrumb` 的配置被移动到了 `itemStyle.normal/itemStyle.emphasis` 外部，和 `itemStyle` 平级。

+ `root` 的设置暂时不支持。目前可以使用 `zoom` 的方式来查看树更下层次的细节。

+ `label` 的配置被移动到了 `itemStyle.normal/itemStyle.emphasis` 外部，和 `itemStyle` 平级。

+ `itemStyle.normal.childBorderWidth`、`itemStyle.normal.childBorderColor`不再支持（因为这个配置方式只能定义两层的treemap）。统一使用 [series-treemap.levels](~series-treemap.levels) 来进行各层级的定义。

<br>
<br>

## type(string) = 'treemap'

{{ use: partial-rect-layout-width-height(
    componentName='treemap ',
    defaultLeft: 'center',
    defaultRight: null,
    defaultTop: 'middle',
    defaultBottom: null,
    defaultWidth: '80%',
    defaultHeight: '80%'
) }}

## squareRatio(number)

期望矩形长宽比率。布局计算时会尽量向这个比率靠近。

默认为黄金比：`0.5 * (1 + Math.sqrt(5))`。


## roam(boolean|string) = true

是否开启拖拽漫游（移动和缩放）。可取值有：

+ `false`：关闭。
+ `'scale'` 或 `'zoom'`：只能够缩放。
+ `'move'`：只能够平移。
+ `true`：缩放和平移均可。


## nodeClick(boolean|string) = 'zoomToNode'

可取值为：

+ `false`：节点点击无反应。
+ `'zoomToNode'`：点击节点后缩放到节点。
+ `'link'`：如果节点数据中有 `link` 点击节点后会进行链接跳转。


## zoomToNodeRatio(number) = 0.32*0.32

点击某个节点，会自动放大那个节点到合适的比例（节点占可视区域的面积比例），这个配置项就是这个比例。


## levels(Array) = []

**多层配置**

treemap 中采用『series』--『每个层级』--『每个节点』这样的『三级配置』。
即我们可以对每个节点进行配置，也可以对树的每个层级进行配置，也可以 series 上设置全局配置。

最常用的是『每个层级进行配置』，`levels` 配置项就是每个层级的配置。例如：

```javascript
levels: [
    {...}, // 顶层配置
    {...}, // 下一层配置
    {...}, // 再下一层配置
    ...
]
```

<br>
**视觉映射的规则**

treemap中首要关注的是如何在视觉上较好得区分『不同层级』、『同层级中不同类别』。这需要合理得设置不同层级的『矩形颜色』、『边界粗细』、『边界颜色』甚至『矩形颜色饱和度』等。

参见这个[例子](${galleryEditorPath}treemap-disk&edit=1&reset=1)，最顶层级用颜色区分，分成了『红』『绿』『蓝』等大块。每个颜色块中是下一个层级，使用颜色的饱和度来区分（参见 `colorSaturation`）。最外层的矩形边界是『白色』，下层级的矩形边界是当前区块颜色加上饱和度处理（参见 `borderColorSaturation`）。

treemap是通过这样的规则来支持这种配置的：每个层级计算用户配置的 `color`、`colorSaturation`、`borderColor`、`colorSaturation`等视觉信息（在levels中配置），然后传递给子节点（子层级）。如果子节点没有配置，则继承父的配置，否则使用自己的配置）。

这样，可以做到：父层级配置 `color` 列表，子层级配置 `colorSaturation`。父层级的每个节点会从 `color` 列表中得到一个颜色，子层级的节点会从 `colorSaturation` 中得到一个颜色，并且继承父节点得到的颜色，合成得到自己的最终颜色。


<br>
**维度与『额外的视觉映射』**

例子：每一个 `value` 字段是一个 Array，其中每个项对应一个维度（dimension）。

```javascript
[
    {
        value: [434, 6969, 8382],
        children: [
            {
                value: [1212, 4943, 5453],
                id: 'someid-1',
                name: 'description of this node',
                children: [...]
            },
            {
                value: [4545, 192, 439],
                id: 'someid-2',
                name: 'description of this node',
                children: [...]
            },
            ...
        ]
    },
    {
        value: [23, 59, 12],
        children: [...]
    },
    ...
]
```

treemap 默认把第一个维度（Array 第一项）映射到『面积』上。而如果想表达更多信息，用户可以把其他的某一个维度（[series-treemap.visualDimension](~series-treemap.viusalDimension)），映射到其他的『视觉元素』上，比如颜色明暗等。参见[例子](${galleryEditorPath}treemap-obama&edit=1&reset=1)中，legend选择 `Growth`时的状态。

<br>
{{ use: partial-treemap-borderColor-setting(galleryEditorPath=${galleryEditorPath}) }}

{{use: partial-treemap-level-props(
    prefix="##",
    galleryEditorPath=${galleryEditorPath},
    galleryViewPath=${galleryViewPath}
)}}



{{use: partial-treemap-level-props(
    prefix="#",
    galleryEditorPath=${galleryEditorPath},
    galleryViewPath=${galleryViewPath}
)}}


## breadcrumb(Object)

面包屑，能够显示当前节点的路径。


### show(boolean) = true

是否显示面包屑。


{{ use: partial-rect-layout(
    componentName="asdf ",
    prefix="##",
    noZ=true,
    defaultLeft="'center'",
    defaultBottom=0
) }}


### height(number) = 22

面包屑的高度。.asdf `series-treemap.breadcrumb`撒地方撒地方阿斯顿发斯蒂芬阿萨德f分


### emptyItemWidth(number) = 25

当面包屑没有内容时候，设个最小宽度。


### itemStyle(Object)

{{ use: partial-item-style-desc }}


#### normal

{{use: partial-item-style(
    prefix="####",
    name="boxplot",
    defaultColor="rgba(0,0,0,0.7)",
    defaultBorderColor="rgba(255,255,255,0.7)",
    defaultBorderWidth=1,
    defaultShadowColor='rgba(150,150,150,1)',
    defaultShadowBlur=3,
    defaultShadowOffsetX=0,
    defaultShadowOffsetY=0
)}}


##### textStyle(Object)

{{use: partial-text-style(
    prefix="#####",
    defaultColor="#fff"
)}}


#### emphasis

{{use: partial-item-style(
    prefix="####",
    name="boxplot",
    defaultColor="rgba(0,0,0,0.7)",
    defaultBorderColor="rgba(255,255,255,0.7)",
    defaultBorderWidth=1,
    defaultShadowColor='rgba(150,150,150,1)',
    defaultShadowBlur=3,
    defaultShadowOffsetX=0,
    defaultShadowOffsetY=0
)}}


##### textStyle(Object)

{{use: partial-text-style(
    prefix="#####",
    defaultColor="#fff"
)}}



## data(Array)

[series-treemap.data](~series-treemap.data) 的数据格式是树状的，例如：

```javascript
[ // 注意，最外层是一个数组，而非从某个根节点开始。
    {
        value: 1212,
        children: [
            {
                value: 2323,    // value字段的值，对应到面积大小。
                                // 也可以是数组，如 [2323, 43, 55]，则数组第一项对应到面积大小。
                                // 数组用于进行额外的视觉映射，详情参见 series-treemp.levels。
                id: 'someid-1', // id 不是必须设置的。
                                // 但是如果想使用 API 来改变某个节点，需要用 id 来定位。
                name: 'description of this node', // 显示在矩形中的描述文字。
                children: [...],
                label: {        // 此节点特殊的 label 定义（如果需要的话）。
                    ...         // label的格式参见 series-treemap.label。
                },
                itemStyle: {    // 此节点特殊的 itemStyle 定义（如果需要的话）。
                    ...         // label的格式参见 series-treemap.itemStyle。
                }
            },
            {
                value: 4545,
                id: 'someid-2',
                name: 'description of this node',
                children: [
                    {
                        value: 5656,
                        id: 'someid-3',
                        name: 'description of this node',
                        children: [...]
                    },
                    ...
                ]
            }
        ]
    },
    {
        value: [23, 59, 12]
        // 如果没有children，可以不写
    },
    ...
]
```

### value(number|Array)

每个树节点的值，对应到面积大小。可以是number，也可以是数组，如 `[2323, 43, 55]`，则数组第一项对应到面积大小。

### id(string)

每个树节点的id。id 不是必须设置的。但是如果想使用 API 来改变某个节点，需要用 id 来定位。

### name(string)

显示在矩形中的描述文字。


{{use: partial-treemap-level-props(
    prefix="##",
    galleryEditorPath=${galleryEditorPath},
    galleryViewPath=${galleryViewPath}
)}}

### children(Array)

子节点，递归定义，格式同 [series-treemap.data](~series-treemap.data)。



{{use: partial-animation-init(
    prefix="#",
    defaultAnimationEasing='quinticInOut',
    defaultAnimationDuration=1500
)}}







{{target: partial-treemap-level-props}}

#${prefix} visualDimension(number) = 0

`treemap` 中支持对数据其他维度进行视觉映射。

首先，treemap的数据格式（参见 [series-treemap.data](~series-treemap.data)）中，每个节点的 `value` 都可以是数组。数组每项是一个『维度』（dimension）。`visualDimension` 指定了额外的『视觉映射』使用的是数组的哪一项。默认为第 `0` 项。

{{ use: partial-treemap-visual-detial }}
{{use: partial-treemap-prop-location-desc(name="visualDimension")}}


#${prefix} color(Array)

表示同一层级的节点的 颜色 选取列表。默认为空时，选取系统color列表。

{{ use: partial-treemap-visual-detial }}
{{use: partial-treemap-prop-location-desc(name="color")}}


#${prefix} colorAlpha(Array) = null

表示同一层级的节点的 颜色透明度 选取范围。数值范围 0 ~ 1。

{{ use: partial-treemap-visual-detial }}
{{use: partial-treemap-prop-location-desc(name="colorAlpha")}}


#${prefix} colorSaturation(number) = null

表示同一层级的节点的 颜色饱和度 选取范围。数值范围 0 ~ 1。

{{ use: partial-treemap-visual-detial }}
{{use: partial-treemap-prop-location-desc(name="colorSaturation")}}


#${prefix} colorMappingBy(string) = 'index'

表示同一层级节点，在颜色列表中（参见 `color` 属性）选择时，按照什么来选择。可选值：

* `'value'`：

将节点的值（即 [series-treemap.data.value](~series-treemap.data.value)）映射到颜色列表中。这样得到的颜色，反应了节点值的大小。
可以使用 `visualDimension` 属性来设置，用 `data` 中那个纬度的值来映射。

* `'index'`：

将节点的 `index`（序号）映射到颜色列表中。即同一层级中，第一个节点取颜色列表中第一个颜色，第二个节点取第二个。这样得到的颜色，便于区分相邻节点。

* `'id'`：

将节点的 `id`（即 [series-treemap.data.id](~series-treemap.data.id)）映射到颜色列表中。`id` 是用户指定的，这样能够使得，在treemap 通过 setOption 变化数值时，同一 `id` 映射到同一颜色，保持一致性。参见 [例子](${galleryEditorPath}treemap-obama&edit=1&reset=1)。

{{ use: partial-treemap-visual-detial }}
{{use: partial-treemap-prop-location-desc(name="colorMappingBy")}}


#${prefix} visibleMin(number) = 10

如果某个节点的矩形的面积，小于这个数值（单位：px平方），这个节点就不显示。

如果不加这个限制，很小的节点会影响显示效果。

{{ use: partial-treemap-visual-detial }}
{{use: partial-treemap-prop-location-desc(name="visibleMin")}}


#${prefix} childrenVisibleMin(number) = null

如果某个节点的矩形面积，小于这个数值（单位：px平方），则不显示这个节点的子节点。

这能够在矩形面积不足够大时候，隐藏节点的细节。当用户用鼠标缩放节点时，如果面积大于此阈值，又会显示子节点。

{{ use: partial-treemap-visual-detial }}
{{use: partial-treemap-prop-location-desc(name="childrenVisibleMin")}}


#${prefix} label(Object)

`label` 描述了每个矩形中，文本标签的样式。

{{use: partial-treemap-prop-location-desc(name="label")}}

<br>

##${prefix} normal(Object)

{{use:partial-treemap-label(prefix=${prefix} + "##")}}


##${prefix} emphasis(Object)

{{use:partial-treemap-label(prefix=${prefix} + "##")}}



#${prefix} itemStyle(Object)

{{use: partial-treemap-prop-location-desc(name="itemStyle")}}

<br>

##${prefix} normal(Object)

{{use: partial-treemap-item-style(
    prefix=${prefix} + "##",
    galleryEditorPath=${galleryEditorPath}
)}}

##${prefix} emphasis(Object)

{{use: partial-treemap-item-style(
    prefix=${prefix} + "##",
    galleryEditorPath=${galleryEditorPath}
)}}










{{target: partial-treemap-prop-location-desc}}
<br>
> 注：treemap中 `${name}` 属性可能在多处地方存在：

> * 可以存在于 [sereis-treemap](~series-treemap) 根下，表示本系列全局的统一设置。

> * 可以存在于 [series-treemap.levels](~series-treemap.levels) 的每个数组元素中，表示树每个层级的统一设置。

> * 存在于 [series-treemap.data](~series-treemap.data) 的每个节点中，表示每个节点的特定设置。





{{target: partial-treemap-visual-detial}}

关于视觉设置，详见 [series-treemap.levels](~series-treemap.levels)。






{{target: partial-treemap-item-style}}

#${prefix} color(Color) =  null

矩形的颜色。默认从全局调色盘 [option.color](~color) 获取颜色。


#${prefix} colorAlpha(number) = null

矩形颜色的透明度。取值范围是 0 ~ 1 之间的浮点数。


#${prefix} colorSaturation(number) = null

矩形颜色的饱和度。取值范围是 0 ~ 1 之间的浮点数。


#${prefix} borderWidth(number) = 0

矩形边框线宽。为 0 时无边框。


#${prefix} gapWidth(number) = 0

矩形内部子矩形的间隔距离。


#${prefix} borderColor(Color) = '#fff',

矩形边框的颜色。支持的格式同`color`。


#${prefix} borderColorSaturation(Color) = null

矩形边框的颜色的饱和度。取值范围是 0 ~ 1 之间的浮点数。

注意：

如果设置此属性，则 `borderColor` 的设置无效，而是：取当前节点计算出的颜色（比如从父节点遗传而来），在此颜色值上设置 `borderColorSaturation` 得到最终颜色。这种方式，能够做出『不同区块有不同色调的矩形间隔线』的效果，能够便于区分层级。

<br>
{{ use: partial-treemap-borderColor-setting(galleryEditorPath=${galleryEditorPath}) }}









{{ target: partial-treemap-borderColor-setting }}
**矩形边框/缝隙设置如何避免混淆**

如果统一用一种颜色设置矩形的缝隙，那么当不同层级的矩形同时展示时可能会出现混淆。

参见 [例子](${galleryEditorPath}doc-example/treemap-borderColor&edit=1&reset=1)，注意其中红色的区块中的子矩形其实是更深层级，和其他用白色缝隙区分的矩形不是在一个层级。所以，红色区块中矩形的分割线的颜色，我们在 `borderColorSaturation` 中设置为『加了饱和度变化的红颜色』，以示区别。







{{ target: partial-treemap-label }}

#${prefix} show(boolean) = true

是否显示文本标签。


#${prefix} position(string|Array) = ['50%', '50%']

{{ use:partial-label-position }}



#${prefix} textStyle(Object)

##${prefix} ellipsis(boolean) = true

当文字超出矩形边界的时候，是否超出部分替换为省略号。


{{use:partial-text-style(
    prefix=${prefix} + '#',
    defaultColor="'#fff'"
)}}


##${prefix} align(string) = 'center'

文字水平对齐方式，可选值 `'center'`、`'right`、`'left'`


##${prefix} baseline(string) = 'middle'

文字竖直对齐方式，可选值 `'middle'`、`'right`、`'left'`




