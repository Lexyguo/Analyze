# Web前端超实用Animation动画插件 -- laxxx
[alexfoxy/laxxx](https://github.com/alexfoxy/laxxx)插件是一款集轻巧（压缩后只有3kb）、平滑、精美于一身的动画插件,可在滚动时创建平滑且精美的动画,体验最直观的交互。

## 预备知识

#### · transform 属性
transform 属性向元素应用 2D 或 3D 转换，允许对指定元素进行旋转，缩放，倾斜或平移等操作。

#### · transform 语法
```bash
transform: none|transform-functions;
```
<table>
<tr>
<th style="width:25%;">值</th>
<th>描述</th>
</tr>
<tr>
<td>none</td>
<td>定义不进行转换。</td>
</tr>
<tr>
<td>matrix(<i>n</i>,<i>n</i>,<i>n</i>,<i>n</i>,<i>n</i>,<i>n</i>)</td>
<td>定义 2D 转换，使用六个值的矩阵。</td>
</tr>
<tr>
<td>matrix3d(<i>n</i>,<i>n</i>,<i>n</i>,<i>n</i>,<i>n</i>,<i>n</i>,<i>n</i>,<i>n</i>,<i>n</i>,<i>n</i>,<i>n</i>,<i>n</i>,<i>n</i>,<i>n</i>,<i>n</i>,<i>n</i>)</td>
<td>定义 3D 转换，使用 16 个值的 4x4 矩阵。</td>
</tr>
<tr>
<td>translate(<i>x</i>,<i>y</i>)</td>
<td>定义 2D 转换。</td>
</tr>
<tr>
<td>translate3d(<i>x</i>,<i>y</i>,<i>z</i>)</td>
<td>定义 3D 转换。</td>
</tr>
<tr>
<td>translateX(<i>x</i>)</td>
<td>定义转换，只是用 X 轴的值。</td>
</tr>
<tr>
<td>translateY(<i>y</i>)</td>
<td>定义转换，只是用 Y 轴的值。</td>
</tr>
<tr>
<td>translateZ(<i>z</i>)</td>
<td>定义 3D 转换，只是用 Z 轴的值。</td>
</tr>
<tr>
<td>scale(<i>x</i>,<i>y</i>)</td>
<td>定义 2D 缩放转换。</td>
</tr>
<tr>
<td>scale3d(<i>x</i>,<i>y</i>,<i>z</i>)</td>
<td>定义 3D 缩放转换。</td>
</tr>
<tr>
<td>scaleX(<i>x</i>)</td>
<td>通过设置 X 轴的值来定义缩放转换。</td>
</tr>
<tr>
<td>scaleY(<i>y</i>)</td>
<td>通过设置 Y 轴的值来定义缩放转换。</td>
</tr>
<tr>
<td>scaleZ(<i>z</i>)</td>
<td>通过设置 Z 轴的值来定义 3D 缩放转换。</td>
</tr>
<tr>
<td>rotate(<i>angle</i>)</td>
<td>定义 2D 旋转，在参数中规定角度。</td>
</tr>
<tr>
<td>rotate3d(<i>x</i>,<i>y</i>,<i>z</i>,<i>angle</i>)</td>
<td>定义 3D 旋转。</td>
</tr>
<tr>
<td>rotateX(<i>angle</i>)</td>
<td>定义沿着 X 轴的 3D 旋转。</td>
</tr>
<tr>
<td>rotateY(<i>angle</i>)</td>
<td>定义沿着 Y 轴的 3D 旋转。</td>
</tr>
<tr>
<td>rotateZ(<i>angle</i>)</td>
<td>定义沿着 Z 轴的 3D 旋转。</td>
</tr>
<tr>
<td>skew(<i>x-angle</i>,<i>y-angle</i>)</td>
<td>定义沿着 X 和 Y 轴的 2D 倾斜转换。</td>
</tr>
<tr>
<td>skewX(<i>angle</i>)</td>
<td>定义沿着 X 轴的 2D 倾斜转换。</td>
</tr>
<tr>
<td>skewY(<i>angle</i>)</td>
<td>定义沿着 Y 轴的 2D 倾斜转换。</td>
</tr>
<tr>
<td>perspective(<i>n</i>)</td>
<td>为 3D 转换元素定义透视视图。</td>
</tr>
</table>

#### · transform-function 数据类型
用于对元素的显示做变换。通常，这种变换可以由矩阵表示，并且可以使用每个点上的矩阵乘法来确定所得到的图像。
目前有多种用来描述转换坐标模型，最常用的是 笛卡尔坐标系统 和 齐次坐标。

#### filter 属性
将模糊或颜色偏移等图形效果应用于元素。滤镜通常用于调整图像，背景和边框的渲染。

#### background-position 属性
为每一个背景图片设置初始位置。 这个位置是相对于由 background-origin 定义的位置图层的。
## 基础方法

``` bash
    var transformFns = {
      // 修改透明度
      "data-lax-opacity": function dataLaxOpacity(style, v) {
        style.opacity = v;
      },
      // 元素在平面平移
      "data-lax-translate": function dataLaxTranslate(style, v) {
        style.transform += " translate(".concat(v, "px, ").concat(v, "px)");
      },
      // 元素在X轴（水平轴）平移
      "data-lax-translate-x": function dataLaxTranslateX(style, v) {
        style.transform += " translateX(".concat(v, "px)");
      },
      // 元素在Y轴（垂直轴）平移
      "data-lax-translate-y": function dataLaxTranslateY(style, v) {
        style.transform += " translateY(".concat(v, "px)");
      },
      // 元素以矢量大小等比例缩放
      "data-lax-scale": function dataLaxScale(style, v) {
        style.transform += " scale(".concat(v, ")");
      },
      // 元素以矢量大小缩放横坐标，scaleX(-1) 表示通过原点的垂直轴定义轴对称
      "data-lax-scale-x": function dataLaxScaleX(style, v) {
        style.transform += " scaleX(".concat(v, ")");
      },
      // 元素以矢量大小缩放纵坐标，scaleX(-1) 表示通过原点的水平轴定义轴对称
      "data-lax-scale-y": function dataLaxScaleY(style, v) {
        style.transform += " scaleY(".concat(v, ")");
      },
      // 拉伸，或者说是平移，该函数会使得在每个方向上扭曲元素上的每个点以一定角度。这是通过将每个坐标增加一个与指定角度成比例的值和到原点的距离来完成的。离原点越远，拉伸的值就越大。
      "data-lax-skew": function dataLaxSkew(style, v) {
        style.transform += " skew(".concat(v, "deg, ").concat(v, "deg)");
      },
      // 水平拉伸，它将元素的每个点在水平方向上扭曲一定角度。这是通过将横坐标增加一个与指定角度成比例的值以及到原点的距离来完成的。离原点越远，拉伸的值就越大。
      "data-lax-skew-x": function dataLaxSkewX(style, v) {
        style.transform += " skewX(".concat(v, "deg)");
      },
      // 垂直拉伸，它将元素的每个点在垂直方向上扭曲一定角度。这是通过将纵坐标增加一个与指定角度成比例的值以及到原点的距离来完成的。离原点越远，拉伸的值就越大。
      "data-lax-skew-y": function dataLaxSkewY(style, v) {
        style.transform += " skewY(".concat(v, "deg)");
      },
      // 将元素在不变形的情况下旋转到不动点周围，移动量由指定角度定义;如果为正值，则运动将为顺时针，如果为负值，则为逆时针 。 180°的旋转称为点反射 (point reflection)。
      "data-lax-rotate": function dataLaxRotate(style, v) {
        style.transform += " rotate(".concat(v, "deg)");
      },
      // 将元素在横坐标上旋转而不使其变形的方法。 其运动的程度由指定的角度来定义；如果是正的，则为顺时针旋转，如果是负的，则是逆时针旋转。
      "data-lax-rotate-x": function dataLaxRotateX(style, v) {
        style.transform += " rotateX(".concat(v, "deg)");
      },
      // 将元素在纵坐标上旋转而不使其变形的方法。 其运动的程度由指定的角度来定义；如果是正的，则为顺时针旋转，如果是负的，则是逆时针旋转。
      "data-lax-rotate-y": function dataLaxRotateY(style, v) {
        style.transform += " rotateY(".concat(v, "deg)");
      },
      // 让图像更明亮或更暗淡
      "data-lax-brightness": function dataLaxBrightness(style, v) {
        style.filter += " brightness(".concat(v, "%)");
      },
      // 增加或减少图像的对比度
      "data-lax-contrast": function dataLaxContrast(style, v) {
        style.filter += " contrast(".concat(v, "%)");
      },
      // 改变图像的整体色调
      "data-lax-hue-rotate": function dataLaxHueRotate(style, v) {
        style.filter += " hue-rotate(".concat(v, "deg)");
      },
      // 模糊图像
      "data-lax-blur": function dataLaxBlur(style, v) {
        style.filter += " blur(".concat(v, "px)");
      },
      // 反转图像颜色
      "data-lax-invert": function dataLaxInvert(style, v) {
        style.filter += "  invert(".concat(v, "%)");
      },
      // 超饱和或去饱和输入的图像
      "data-lax-saturate": function dataLaxSaturate(style, v) {
        style.filter += "  saturate(".concat(v, "%)");
      },
      // 将图像转为灰度图
      "data-lax-grayscale": function dataLaxGrayscale(style, v) {
        style.filter += "  grayscale(".concat(v, "%)");
      },
      // 修改元素背景定位
      "data-lax-bg-pos": function dataLaxBgPos(style, v) {
        style.backgroundPosition = "".concat(v, "px ").concat(v, "px");
      },
      // 修改元素背景X轴定位
      "data-lax-bg-pos-x": function dataLaxBgPosX(style, v) {
        style.backgroundPositionX = "".concat(v, "px");
      },
      // 修改元素背景Y轴定位
      "data-lax-bg-pos-y": function dataLaxBgPosY(style, v) {
        style.backgroundPositionY = "".concat(v, "px");
      }
    };
```
