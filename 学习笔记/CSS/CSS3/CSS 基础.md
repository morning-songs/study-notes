# `CSS` 基础

### 单位

​		`CSS` 有几个不同的单位来表示长度。

​		许多 `CSS` 属性采用“长度”值，例如 `width`、`margin`、`padding`、`font-size` 等。

​		长度是一个数字，后跟一个**长度**单位，如 `10px`、`2em` 等。

​		例如，使用 `px`（像素）设置不同的长度值：

```css
h1 {
  	font-size: 60px;
}

p {
  	font-size: 25px;
  	line-height: 50px;
}
```

**注意：**数字和单位之间不能出现空格。但是，如果值为 `0`，则可以省略该单位。对于某些 `CSS` 属性，允许使用负长度。有两种类型的长度单位：**绝对长度单位** 和 **相对长度单位**。

#### 绝对单位

​		绝对长度单位是固定的，以其中任何一个表示的长度都将显示为该大小。

​		不建议在屏幕上使用绝对长度单位，因为屏幕尺寸差异很大。 但是，如果输出介质已知，则可以使用它们，例如对于打印布局。

| 单位 |             描述             |
| :--: | :--------------------------: |
| `cm` |         centimeters          |
| `mm` |         millimeters          |
| `in` | inches (1in = 96px = 2.54cm) |
| `px` | pixels (1px = 1/96th of 1in) |
| `pt` |  points (1pt = 1/72 of 1in)  |
| `pc` |     picas (1pc = 12 pt)      |

​		像素 （`px`） 相对于查看设备。对于低 `dpi` 设备，`1px` 是显示器的一个设备像素（点）。适用于打印机和高分辨率 屏幕 `1px` 表示多个设备像素。

#### 相对单位

​		相对长度单位指定相对于另一个长度属性的长度。 相对长度单位在不同的渲染介质之间缩放得更好。

|  单位  | 描述                                                         |
| :----: | :----------------------------------------------------------- |
|  `em`  | Relative to the font-size of the element (2em means 2 times the size of the current font) |
|  `ex`  | Relative to the x-height of the current font (rarely used)   |
|  `ch`  | Relative to the width of the "0" (zero)                      |
| `rem`  | Relative to font-size of the root element                    |
|  `vw`  | Relative to 1% of the width of the viewport                  |
|  `vh`  | Relative to 1% of the height of the viewport                 |
| `vmin` | Relative to 1% of viewport's smaller dimension               |
| `vmax` | Relative to 1% of viewport's larger dimension                |
|  `%`   | Relative to the parent element                               |

如果当前默认字体大小为 `16px`，则有 `1em = 16px`；若默认大小为 `14px`，则 `1em = 14px`。

**提示：**`em` 和 `rem` 单元在完美创建可扩展的布局方面非常实用！视口 = 浏览器窗口大小。如果视口为 50 厘米宽，`1vw` = 0.5 厘米。

#### 浏览器支持

|              长度单位               | `Chrome` | `Edge` | `Firefox` | `Safari` | `Opera` |
| :---------------------------------: | :------: | :----: | :-------: | :------: | :-----: |
| `em, ex, %, px, cm, mm, in, pt, pc` |   1.0    |  3.0   |    1.0    |   1.0    |   3.5   |
|                `ch`                 |   27.0   |  9.0   |    1.0    |   7.0    |  20.0   |
|                `rem`                |   4.0    |  9.0   |    3.6    |   4.1    |  11.6   |
|              `vh, vw`               |   20.0   |  9.0   |   19.0    |   6.0    |  20.0   |
|               `vmin`                |   20.0   |  12.0  |   19.0    |   6.0    |  20.0   |
|               `vmax`                |   26.0   |  16.0  |   19.0    |   7.0    |  20.0   |



### 颜色

`CSS` 中的颜色可以通过以下方法指定：

- 十六进制颜色
- 具有透明度的十六进制颜色
- `RGB` 颜色
- `RGBA` 颜色
- `HSL` 颜色
- `HSLA` 颜色
- 预定义/跨浏览器颜色名称，如 `red`、`blue` 等
- 关键字 `currentcolor`



### 字体

没有 100% 完全安全的网页字体。总有可能 字体未找到或未正确安装。因此，它非常重要 以始终使用回退字体。

这意味着您应该在 字体系列属性。如果第一种字体不起作用，浏览器将尝试 下一个，下一个，依此类推。始终以通用字体结束列表 姓。

以下是一些常用的后备字体，按 5 个通用字体系列组织：

- **衬线**
- **无衬线**
- **等宽**
- **草书**
- **幻想**

#### 回退字体

#### 网络安全字体



### 函数

`CSS` 函数用作各种 `CSS` 属性的值。

|                             函数                             | 描述                                                         |
| :----------------------------------------------------------: | :----------------------------------------------------------- |
|  [`attr()`](https://www.w3schools.com/cssref/func_attr.php)  | Returns the value of an attribute of the selected element    |
|  [`calc()`](https://www.w3schools.com/cssref/func_calc.php)  | Allows you to perform calculations to determine CSS property values |
| [`conic-gradient()`](https://www.w3schools.com/cssref/func_conic-gradient.php) | Creates a conic gradient                                     |
| [`counter()`](https://www.w3schools.com/cssref/func_counter.php) | Returns the current value of the named counter               |
| [`cubic-bezier()`](https://www.w3schools.com/cssref/func_cubic-bezier.php) | Defines a Cubic Bezier curve                                 |
|   [`hsl()`](https://www.w3schools.com/cssref/func_hsl.php)   | Defines colors using the Hue-Saturation-Lightness model (HSL) |
|  [`hsla()`](https://www.w3schools.com/cssref/func_hsla.php)  | Defines colors using the Hue-Saturation-Lightness-Alpha model (HSLA) |
| [`linear-gradient()`](https://www.w3schools.com/cssref/func_linear-gradient.php) | Creates a linear gradient                                    |
|   [`max()`](https://www.w3schools.com/cssref/func_max.php)   | Uses the largest value, from a comma-separated list of values, as the property value |
|   [`min()`](https://www.w3schools.com/cssref/func_min.php)   | Uses the smallest value, from a comma-separated list of values, as the property value |
| [`radial-gradient()`](https://www.w3schools.com/cssref/func_radial-gradient.php) | Creates a radial gradient                                    |
| [`repeating-conic-gradient()`](https://www.w3schools.com/cssref/func_repeating-conic-gradient.php) | Repeats a conic gradient                                     |
| [`repeating-linear-gradient()`](https://www.w3schools.com/cssref/func_repeating-linear-gradient.php) | Repeats a linear gradient                                    |
| [`repeating-radial-gradient()`](https://www.w3schools.com/cssref/func_repeating-radial-gradient.php) | Repeats a radial gradient                                    |
|   [`rgb()`](https://www.w3schools.com/cssref/func_rgb.php)   | Defines colors using the Red-Green-Blue model (RGB)          |
|  [`rgba()`](https://www.w3schools.com/cssref/func_rgba.php)  | Defines colors using the Red-Green-Blue-Alpha model (RGBA)   |
|   [`var()`](https://www.w3schools.com/cssref/func_var.php)   | Inserts the value of a custom property                       |



### 变量







