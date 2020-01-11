## 落寞的 table layout

`表布局`，这不是我们一直都极力避免的吗？不过我们并非讨论如何用表建立布局，而是要介绍 CSS 中表本身如何布局。

table 本身就能够确定其他元素的元素大小，在 normal flow 中，没有任何情况能够以这样的方式让不同元素相互影响大小和布局。

## 表格式化

table 元素与 table-cell 元素有很明确的界限。对于 table-cell 元素来说：
1. table-cell 生成矩形盒子，该矩形盒子没有外边距，不能通过定义外边距来定义 table-cell 之间的间隔。如果试图对 table-cell、table-row 应用 margin，浏览器会将其忽略。caption 除外。


| 盒子模型     | 外在盒子 | 容器盒子     |
| ------------ | -------- | ------------ |
| block        | 块级盒子 | 块级容器盒子 |
| inline       | 内联盒子 | 内联盒子     |
| inline-block | 内联盒子 | 块级容器盒子 |


## table

[Tables](https://www.w3.org/TR/2011/REC-CSS2-20110607/tables.html#q17.0)

[17.5 表格内容的可视化布局](https://www.w3.org/TR/2011/REC-CSS2-20110607/tables.html#q17.0)

table {display：table}
td，th {display：table-cell}
col {border-style：none solid} -- 只是用来设置表格样式，并没有实际盒子模型。

|--------------|--------------|--------------|
|                                            | -- caption
|--------------|--------------|--------------|
|              |     td       |      td      | -- thead [列标题]
|              |--------------|--------------|
|      col     |     td       |      td      | -- tr [tbody: 多行 tr] [colgroup: 多列 col]
|              |--------------|--------------|
|              |     td       |      td      | -- tfoot
|--------------|--------------|--------------|

[17.5.2 表宽度算法： 在“表格的布局” 属性](https://www.w3.org/TR/2011/REC-CSS2-20110607/tables.html#q17.0)


table-layout：表格的宽度通过表格设置宽度与列宽度之和中的较大值决定。

- table-layout：fixed
布局优先：当某列的宽度为 auto 时，某一列的宽度仅由该列首行的单元格决定。

使用 `fixed` 布局方式时，整个表格可以在其首行被下载后就被解析和渲染。这样对于 `auto 自动布局方式`来说可以加速渲染，但是其后的单元格内容并不会自适应当前列宽（可以使用 `overflow`  属性控制是否允许内容溢出）。

- table-layout：auto
内容优先：表格及单元格的宽度取决于其包含的内容。

宽度设置了百分比? - CSS世界













