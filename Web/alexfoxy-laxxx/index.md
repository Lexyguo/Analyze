# Web前端超实用Animation动画插件 -- laxxx
[alexfoxy/laxxx](https://github.com/alexfoxy/laxxx)插件是一款集轻巧（压缩后只有3kb）、平滑、精美于一身的动画插件,可在滚动时创建平滑且精美的动画,体验最直观的交互。

## 一、预备知识

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
## 二、基础方法

``` bash

    const transformFns = {
      // 修改透明度
      "data-lax-opacity": (style, v) => { style.opacity = v },
      // 元素在平面平移
      "data-lax-translate": (style, v) => { style.transform += ` translate(${v}px, ${v}px)` },
      // 元素在X轴（水平轴）平移
      "data-lax-translate-x": (style, v) => { style.transform += ` translateX(${v}px)` },
      // 元素在Y轴（垂直轴）平移
      "data-lax-translate-y": (style, v) => { style.transform += ` translateY(${v}px)` },
      // 元素以矢量大小等比例缩放
      "data-lax-scale": (style, v) => { style.transform += ` scale(${v})` },
      // 元素以矢量大小缩放横坐标，scaleX(-1) 表示通过原点的垂直轴定义轴对称
      "data-lax-scale-x": (style, v) => { style.transform += ` scaleX(${v})` },
      // 元素以矢量大小缩放纵坐标，scaleY(-1) 表示通过原点的水平轴定义轴对称
      "data-lax-scale-y": (style, v) => { style.transform += ` scaleY(${v})` },
      // 拉伸，或者说是平移，该函数会使得在每个方向上扭曲元素上的每个点以一定角度。这是通过将每个坐标增加一个与指定角度成比例的值和到原点的距离来完成的。离原点越远，拉伸的值就越大。
      "data-lax-skew": (style, v) => { style.transform += ` skew(${v}deg, ${v}deg)` },
      // 水平拉伸，它将元素的每个点在水平方向上扭曲一定角度。这是通过将横坐标增加一个与指定角度成比例的值以及到原点的距离来完成的。离原点越远，拉伸的值就越大。
      "data-lax-skew-x": (style, v) => { style.transform += ` skewX(${v}deg)` },
      // 垂直拉伸，它将元素的每个点在垂直方向上扭曲一定角度。这是通过将纵坐标增加一个与指定角度成比例的值以及到原点的距离来完成的。离原点越远，拉伸的值就越大。
      "data-lax-skew-y": (style, v) => { style.transform += ` skewY(${v}deg)` },
      // 将元素在不变形的情况下旋转到不动点周围，移动量由指定角度定义;如果为正值，则运动将为顺时针，如果为负值，则为逆时针 。 180°的旋转称为点反射 (point reflection)。
      "data-lax-rotate": (style, v) => { style.transform += ` rotate(${v}deg)` },
      // 将元素在横坐标上旋转而不使其变形的方法。 其运动的程度由指定的角度来定义；如果是正的，则为顺时针旋转，如果是负的，则是逆时针旋转。
      "data-lax-rotate-x": (style, v) => { style.transform += ` rotateX(${v}deg)` },
      // 将元素在纵坐标上旋转而不使其变形的方法。 其运动的程度由指定的角度来定义；如果是正的，则为顺时针旋转，如果是负的，则是逆时针旋转。
      "data-lax-rotate-y": (style, v) => { style.transform += ` rotateY(${v}deg)` },
      // 让图像更明亮或更暗淡
      "data-lax-brightness": (style, v) => { style.filter += ` brightness(${v}%)` },
      // 增加或减少图像的对比度
      "data-lax-contrast": (style, v) => { style.filter += ` contrast(${v}%)` },
      // 改变图像的整体色调
      "data-lax-hue-rotate": (style, v) => { style.filter += ` hue-rotate(${v}deg)` },
      // 模糊图像
      "data-lax-blur": (style, v) => { style.filter += ` blur(${v}px)` },
      // 反转图像颜色
      "data-lax-invert": (style, v) => { style.filter += `  invert(${v}%)` },
      // 超饱和或去饱和输入的图像
      "data-lax-saturate": (style, v) => { style.filter += `  saturate(${v}%)` },
      // 将图像转为灰度图
      "data-lax-grayscale": (style, v) => { style.filter += `  grayscale(${v}%)` },
      // 修改元素背景定位
      "data-lax-bg-pos": (style, v) => { style.backgroundPosition = `${v}px ${v}px` },
      // 修改元素背景X轴定位
      "data-lax-bg-pos-x": (style, v) => { style.backgroundPositionX = `${v}px` },
      // 修改元素背景Y轴定位
      "data-lax-bg-pos-y": (style, v) => { style.backgroundPositionY = `${v}px` }
    }
```
## 三、关键函数
### 1、计算当前位置的变化
```bash
    lax.calcTransforms = (o) => {
      const { el } = o

      // 找到当前元素变化属性
      const presets = []
      for(let i=0; i<el.attributes.length; i++) {
          const a = el.attributes[i]
          if(a.name.indexOf("data-lax-preset") > -1)  {
            presets.push(a);
          }
      }

      // 由于每个节点可以同时拥有多个运动模式，所以需要先用 split 获取到所有的运动模式，然后再对每个运动模式单独处理。
      for(let i=0; i<presets.length; i++) {
          const a = presets[i]
          const b = a.name.split(breakpointsSeparator)
          const breakpoint = b[1] ? `${breakpointsSeparator}${b[1]}` : ''
          a.value.split(" ").forEach((p) => {
              const bits = p.split("-")
              const fn = lax.presets[bits[0]]
              if(!fn) {
                  console.log(`lax error: preset ${bits[0]} is not defined`)
              } else {
                  const d = fn(bits[1])
                  for(let k in d) {
                      el.setAttribute(`${k}${breakpoint}`, d[k])
                  }
              }
          })
	  
          const currentAnchor = el.getAttribute("data-lax-anchor")
          if(!currentAnchor || currentAnchor === "") el.setAttribute("data-lax-anchor", "self")
          el.attributes.removeNamedItem(a.name)

      }

      // backface-visibility、-webkit-backface-visibility是指定当元素背面朝向观察者时是否可见。元素的背面总是透明的，当其朝向观察者时，显示正面的镜像

      // 获取data-lax-use-gpu属性，如果 value 为 false，则不使用backface-visibility、-webkit-backface-visibility，否则则使用。然后删去data-lax-optimize属性
      const useGpu = !(el.attributes["data-lax-use-gpu"] && el.attributes["data-lax-use-gpu"].value === 'false')
      if(useGpu) {
        el.style["backface-visibility"] = "hidden"
        el.style["-webkit-backface-visibility"] = "hidden"
      }
      if(el.attributes["data-lax-use-gpu"]) el.attributes.removeNamedItem("data-lax-use-gpu")

      // 由于当用户不设置opacity（透明度）时，就无法优化在滚动中更新了坐标的元素，所以在optimize的时候对opacity进行了相关操作。
      o.optimise = false 
      if(el.attributes["data-lax-optimize"] && el.attributes["data-lax-optimize"].value === 'true') {
        o.optimise = true
        const bounds = el.getBoundingClientRect()
        el.setAttribute("data-lax-opacity", `${-bounds.height-1} 0, ${-bounds.height} 1, ${window.innerHeight} 1, ${window.innerHeight+1} 0`)
        el.attributes.removeNamedItem("data-lax-optimize")
      }

      // 由于以上操作的参数操作完之后都会被remove，所以在进行接下来其他操作的处理时不会在被遍历到。
      // 先通过执行a.name.split(breakpointsSeparator)排除暂停点，获得设置lax组字符串；再通过执行b[0].split("-")，获得到设置lax的参数，如果 a.name 是 data-lax-anchor 的话，我们会判断 a.value 是不是 self，从而获取元素 o 的参照物【为元素(参照物)的 scrollY】是谁。以此分隔完各个关键帧状态之后就用 map 依次来处理其中的数据，最后将获取到的值赋值给o.transforms[a.name]。
      for(let i=0; i<el.attributes.length; i++) {
        const a = el.attributes[i]
        if(a.name.indexOf("data-lax") < 0) continue

        const b = a.name.split(breakpointsSeparator)
        const bits = b[0].split("-")
        const breakpoint = b[1] || "default"
        if(bits[1] === "lax") {
          if(a.name === "data-lax-anchor") {
            o["data-lax-anchor"] = a.value === "self" ? el : document.querySelector(a.value)
            const rect = o["data-lax-anchor"].getBoundingClientRect()
            o.anchorTop = Math.floor(rect.top) + window.scrollY
          } else {
            // 替换vw=>浏览器视口宽度；vh=>浏览器视口高度；elh=>元素内部的高度；elw=>元素的内部宽度
            const tString = a.value
              .replace(/vw/g, window.innerWidth)
              .replace(/vh/g, window.innerHeight)
              .replace(/elh/g, el.clientHeight)
              .replace(/elw/g, el.clientWidth)
              .replace(/\s+/g," ")

            const [arrString, optionString] = tString.split("|")
            const options = {}

            if(optionString) {
              optionString.split(" ").forEach((o) => {
                const [key, val] = o.split("=")
                options[key] = key && fnOrVal(val)
              }) 
            }

            const name = b[0]
            const valueMap = arrString.split(",").map((x) => { 
                return x.trim().split(" ").map(fnOrVal)
              }).sort((a,b) => {
                return a[0] - b[0]  
              })

            if(!o.transforms[name]) {
              o.transforms[name] = {}
            }
            
            o.transforms[name][breakpoint] = { valueMap, options } 
          }
        }
      }

      // 如果data-lax-sprite-data存在，则会通过el.attributes["data-lax-sprite-data"]获取相应的属性值，以修改元素的宽、高、背景图。
      const spriteData = el.attributes["data-lax-sprite-data"] && el.attributes["data-lax-sprite-data"].value
      if(spriteData) {
        o.spriteData = spriteData.split(",").map(x => parseInt(x))
        el.style.height = o.spriteData[1] + "px"
        el.style.width = o.spriteData[0] + "px"

        const spriteImage = el.attributes["data-lax-sprite-image"] && el.attributes["data-lax-sprite-image"].value
        if(spriteImage) {
          el.style.backgroundImage = `url(${spriteImage})`
        }
      }

      return o
    }
```
### 2、updateElement(更新元素信息)
```bash

    lax.updateElement = (o) => {
      const { originalStyle, anchorTop, transforms, spriteData, el } = o

      // 通过判断是否有anchorTop属性来决定r值，如果有，就会计算元素相对于屏幕顶部的距离(因为页面向下滚动了，那么这个参照物相对于屏幕顶部也就减少了那么多的滚动距离)，如果没有则直接取 window.scrollY。
      let r = anchorTop ? anchorTop-lastY : lastY

      const style = {
        transform: originalStyle.transform,
        filter: originalStyle.filter
      }
      
      // 遍历transforms的属性并调用 intrp 函数，最后执行 t(style, v)。
      for(let i in transforms) {
        const transformData = transforms[i][currentBreakpoint] || transforms[i]["default"]

        if(!transformData) {
          // console.log(`lax error: there is no setting for key ${i} and screen size ${currentBreakpoint}. Try adding a default value!`)
          continue
        }

        let _r = r
        if(transformData.options.offset) _r = _r+transformData.options.offset
        if(transformData.options.speed) _r = _r*transformData.options.speed
        if(transformData.options.loop) _r = _r%transformData.options.loop

        const t = transformFns[i]
        const v = intrp(transformData.valueMap, _r)

        if(!t) {
          // console.info(`lax: error ${i} is not supported`)
          continue
        }

        t(style, v)
      }

      // 如果元素存在背景相关信息，会根据滚动的相对位置更新背景图信息
      if(spriteData) {
        const [frameW,frameH,numFrames,cols,scrollStep] = spriteData
        const frameNo = Math.floor(lastY/scrollStep) % numFrames
        const framePosX = frameNo%cols
        const framePosY = Math.floor(frameNo/cols)
        style.backgroundPosition = `-${framePosX*frameW}px -${framePosY*frameH}px`
      }

      // 由于为了解决电脑性能不高的问题，监听 scroll 事件还会做节流与防抖，所以我们在计算并更新元素的时候需要优化部分不在屏幕中的元素。
      if(style.opacity === 0) { 
        // if opacity 0 don‘t update
        el.style.opacity = 0 
      } else {
        for(let k in style) {
          el.style[k] = style[k]
        }
      }
    }

```
### 3、getCurrentBreakPoint(获取当前暂停滚动的坐标位置)
```bash
    lax.getCurrentBreakPoint = () => {
      let b = 'default'
      const w = window.innerWidth

      for(let i in lax.breakpoints) {
        const px = parseFloat(lax.breakpoints[i])
        if(px <= w) {
          b = i
        } else {
          break
        }
      }

      return b
    }
```
通过遍历lax中的breakpoints坐标信息与获取到浏览器视口（viewport）宽度（如果存在垂直滚动条则包括它）比较，直到获取到的breakpoints坐标信息大于视口宽度，则停止遍历。
### 4、populateElements(收集所有应用lax的节点)
```bash

    lax.populateElements = () => {
      lax.elements = []
      document.querySelectorAll(lax.selector).forEach(lax.addElement)
      currentBreakpoint = lax.getCurrentBreakPoint()
    }

```
  populateElements置空了lax.elements（用来保存页面中应用lax的元素节点的数组），通过遍历节点添加键值selector选择器中，然后获得操作键值字符串。接着直接使用 document.querySelectorAll 来获取所有的节点并调用addElement函数。
### 5、addElement(初始化节点)
```bash

    lax.addElement = (el) => {
      const o = lax.calcTransforms(lax.createLaxObject(el))
      lax.elements.push(o)
      lax.updateElement(o)
    }

```
添加新节点信息进lax.elements数组中，更新节点元素信息。
#### 6、updateElements(更新节点信息)
```bash

    lax.updateElements = () => {
      lax.elements.forEach((o) => {
        lax.calcTransforms(o)
        lax.updateElement(o)
      }) 

      currentBreakpoint = lax.getCurrentBreakPoint()
    }

```
遍历节点数组中的元素，根据参照物计算并重新绘制节点元素信息，更新当前暂停坐标。
获取新节点元素，并更新节点元素。
## 四、lax整合的一些综合操作
```bash

    lax.presets = {
      // 变速滚动
      linger: (v) => {
        return { "data-lax-translate-y": `(vh*0.7) 0, 0 200, -500 0` }
      },
      // 急速上升至指定位置（默认为元素顶部）
      lazy: (v=100) => {
        return { "data-lax-translate-y": `(vh) 0, (-elh) ${v}` }
      },
      // 急速下降至指定位置（默认为元素底部）
      eager: (v=100) => {
        return { "data-lax-translate-y": `(vh) 0, (-elh) -${v}` }
      },
      // 回旋
      slalom: (v=50) => {
        return { "data-lax-translate-x": `vh ${v}, (vh*0.8) ${-v}, (vh*0.6) ${v}, (vh*0.4) ${-v}, (vh*0.2) ${v}, (vh*0) ${-v}, (-elh) ${v}` }
      },
      // 根据指定值改变整体色调
      crazy: (v) => {
        return { "data-lax-hue-rotate": `${crazy} | loop=20` }
      },
      // 顺时针旋转指定角度（默认360度）
      spin: (v=360) => {
        return { "data-lax-rotate": `(vh) 0, (-elh) ${v}` }
      },
      // 逆时针旋转指定角度（默认360度）
      spinRev: (v=360) => {
        return { "data-lax-rotate": `(vh) 0, (-elh) ${-v}` }
      },
      // 旋转指定角度（默认360度）进入
      spinIn: (v=360) => {
        return { "data-lax-rotate": `vh ${v}, (vh*0.5) 0` }
      },
      // 旋转指定角度（默认360度）离开
      spinOut: (v=360) => {
        return { "data-lax-rotate": `(vh*0.5) 0, -elh ${v}` }
      },
      // 模糊进入模糊离开
      blurInOut: (v=40) => {
        return { "data-lax-blur": `(vh) ${v}, (vh*0.8) 0, (vh*0.2) 0, 0 ${v}` }
      },
      // 模糊进入
      blurIn: (v=40) => {
        return { "data-lax-blur": `(vh) ${v}, (vh*0.7) 0` }
      },
      // 模糊离开
      blurOut: (v=40) => {
        return { "data-lax-blur": `(vh*0.3) 0, 0 ${v}` }
      },
      // 淡入淡出
      fadeInOut: () => {
        return { "data-lax-opacity": "(vh) 0, (vh*0.8) 1, (vh*0.2) 1, 0 0" }
      },
      // 淡入
      fadeIn: () => {
        return { "data-lax-opacity": "(vh) 0, (vh*0.7) 1" }
      },
      // 淡出
      fadeOut: () => {
        return { "data-lax-opacity": "(vh*0.3) 1, 0 0" }
      },
      // 向左漂移指定区间（默认从 100 至 -100）
      driftLeft: (v=100) => {
        return { "data-lax-translate-x": `vh ${v}, -elh ${-v}` }
      },
      // 向右漂移指定区间（默认从 -100 至 100）
      driftRight: (v=100) => {
        return { "data-lax-translate-x": `vh ${-v}, -elh ${v}` }
      },
      // 从左移至右指定距离（默认移动距离为视口宽100%）
      leftToRight: (v=1) => {
        return { "data-lax-translate-x": `vh 0, -elh (vw*${v})` }
      },
      // 从右移至左指定距离（默认移动距离为视口宽100%）
      rightToLeft: (v=1) => {
        return { "data-lax-translate-x": `vh 0, -elh (vw*${-v})` }
      },
      // 从指定值放大到原来大小的0.8，再缩小到0.2，最后还原至原来大小
      zoomInOut: (v=0.2) => {
        return { "data-lax-scale": `(vh) ${v}, (vh*0.8) 1, (vh*0.2) 1, -elh ${v}` }
      },
      // 从指定值放大到原来大小的0.7
      zoomIn: (v=0.2) => {
        return { "data-lax-scale": `(vh) ${v}, (vh*0.7) 1` }
      },
      // 从原来大小的0.3缩小到指定值
      zoomOut: (v=0.2) => {
        return { "data-lax-scale": `(vh*0.3) 1, -elh ${v}` }
      },
      // 水平倾斜指定范围（默认为30度至-30度）
      speedy: (v=30) => {
        return { "data-lax-skew-x": `(vh) ${v}, -elh ${-v}` }
      },
      // 垂直倾斜指定范围（默认为30度至-30度）
      swing: (v=30) => {
        return { "data-lax-skew-y": `(vh) ${v}, -elh ${-v}` }
      }
    }

```