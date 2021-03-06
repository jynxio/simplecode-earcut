# 概述

[earcut](https://github.com/mapbox/earcut) 是一个三角剖分库，它基于一系列的坐标来进行剖分，它既可以剖分二维图形，也可以剖分维度更高的图形，earcut 剖分三维及更高维度的图形的原理是：将三维及更高维度的坐标投影至二维平面，然后基于二维的坐标来进行剖分。

earcut 在大多数情况下都可以正确的剖分三维及更高维度的图形，但有时候也会失灵，失灵时的剖分结果是 `[]`，失灵的原因是：三维及更高维度的坐标被投影至二维平面后，所有的二维坐标都位于一条直线上，显然 earcut 无法对一条直线上的点进行三角剖分，因此便返回 `[]`。关于该 bug 的细节，请参见 [#151](https://github.com/mapbox/earcut/issues/151)。

earcut 不仅可以剖分普通的图形（即没有孔洞的图形），还可以剖分具有一至多个孔洞的图形。本文的主要目的是记录如何剖分具有多个孔洞的模型，若你想要学习其他知识，[earcut's homepage](https://github.com/mapbox/earcut/issues/151) 一定能帮到你。

本文使用 [three.js](https://github.com/mrdoob/three.js) 来可视化剖分的结果。此外，在你准备学习如何剖分孔洞图形之前，你应当充分阅读 [The GeoJSON Format](https://datatracker.ietf.org/doc/html/rfc7946)，因为它详述了孔洞图形的数据格式（即孔洞图形是如何记录和组织数据的），不过出于向后兼容的考虑，许多库（包括 earcut）并没有完全遵循 GeoJSON 的要求，至此你至少应当知道：

- `Polygon` 类型的 `coordinates` 是一个拥有零至多个 `linear ring` 的数组。
- `Polygon` 类型的 `coordinates` 的首个 `linear ring` 代表图形的外轮廓，且其内的顶点以逆时针方向排序，其余的 `linear ring` 代表图形的孔洞，且期内的顶点以顺时针方向排序。
- `linear ring` 是一个至少四个 `position` 的数组，且首尾的 `position` 必须相同。
- `position` 是一个坐标数组，比如 `[ 103, 20 ]`。

<br />

# 测试

依次执行下述命令，即可运行示例 demo ：

```
npm install
```

```
npm run start
```

![](./image-hosting/earcut-simplecode.png)

<br />

# 剖分没有孔洞的图形

首先，假设图形的顶点数据为：

```js
const vertices = [
    - 50, + 50, 0, // 轮廓：左上
    - 50, - 50, 0, // 轮廓：左下
    + 50, - 50, 0, // 轮廓：右下
    + 50, + 50, 0, // 轮廓：右上
];
```

然后，进行三角剖分：

```js
earcut( vertices, null, 3 );
```

最后，生成多边形：

![](./image-hosting/polygon-without-hole.png)

<br />

# 剖分只有一个孔洞的图形

首先，假设图形的顶点数据为：

```js
const vertices = [
    - 50, + 50, 0, // 轮廓：左上
    - 50, - 50, 0, // 轮廓：左下
    + 50, - 50, 0, // 轮廓：右下
    + 50, + 50, 0, // 轮廓：右上

    - 30, + 30, 0, // 孔：左上
    + 30, + 30, 0, // 孔：右上
    + 30, - 30, 0, // 孔：右下
    - 30, - 30, 0, // 孔：左下
];
```

然后，进行三角剖分：

```js
earcut( vertices, [ 4 ], 3 );
```

最后，生成多边形：

![](./image-hosting/polygon-with-a-hole.png)

<br />

# 剖分具有多个孔洞的图形

首先，假设图形的顶点数据为：

```js
const vertices = [
    - 50, + 50, 0, // 轮廓：左上
    - 50, - 50, 0, // 轮廓：左下
    + 50, - 50, 0, // 轮廓：右下
    + 50, + 50, 0, // 轮廓：右上

    - 15, + 15, 0, // 孔：左上
    -  5, + 15, 0, // 孔：右上
    -  5, +  5, 0, // 孔：右下
    - 15, +  5, 0, // 孔：左下

    +  5, + 15, 0, // 孔：左上
    + 15, + 15, 0, // 孔：右上
    + 15, +  5, 0, // 孔：右下
    +  5, +  5, 0, // 孔：左下

    +  5, -  5, 0, // 孔：左上
    + 15, -  5, 0, // 孔：右上
    + 15, - 15, 0, // 孔：右下
    +  5, - 15, 0, // 孔：左下

    - 15, -  5, 0, // 孔：左上
    -  5, -  5, 0, // 孔：右上
    -  5, - 15, 0, // 孔：右下
    - 15, - 15, 0, // 孔：左下
];
```

然后，进行三角剖分：

```js
earcut( vertices, [ 4, 8, 12, 16 ], 3 );
```

最后，生成多边形：

![](./image-hosting/polygon-with-four-hole.png)

<br />

# 运行

依次执行下述命令，来运行这个项目：

1. `npm install`
2. `npm run start`

<br />

# 许可

本项目遵循 [MIT](https://github.com/jynxio/simplecode-earcut/blob/main/license) 协议。
