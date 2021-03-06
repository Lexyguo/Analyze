# Web前端Canvas绘图项目实例 -- Shape Shifter
[kennethcachia/shape-shifter](https://github.com/kennethcachia/shape-shifter)项目是一个用canvas绘制粒子，以Grunt为基础构建工具，通过对粒子的指定组合来实现不同形状渲染的画布实验项目，还支持文本、倒数、时间和图标的模式。

## 预备知识

#### Canvas坐标系
默认情况下，Canvas 的坐标系以Canvas 元素的左上角为坐标原点(0, 0)。水平方向为x轴，并向右增长；垂直方向为y轴，并向下增长

画布上每一个点的坐标都直接映射到一个CSS像素上，点可以使用浮点数来指定坐标，但它不会自动转换为整型值。需要注意的是，此时，所绘制的内容并不会立即显示出来。因为这里只是定义一条不可见的路径，并未在画布上绘制任何图形。稍后，可以调用stroke()或fill()方法，来执行绘制动作，使其可见。


#### Canvas绘制文本
```bash
fillText(text, x, y, [maxWidth])
strokeText(text, x, y, [maxWidth])
````
如上方法，表示在(x,y)的位置，绘制text的内容。可选参数maxWidth为文本的最大宽度，单位为像素。如果设置了该属性，当文本内容宽度超过该参数值，则会自动按比例缩小字体，使文本的内容全部可见；未超过时，则以实际宽度显示。如果未设置该属性，当文本内容宽度超过画布宽度时，超出的内容将被隐藏。调用一次fillText()或strokeText()方法，只能绘制一行文本，如果要绘制多行文本，需要调用多次。

还可以通过设置如下表中属性，来保证文本在各浏览器下显示一致。

| 属性          | 描述           | 
|:------------- |:------------- | 
| font          | 文本字体，可设置字体样式的任何值，如font style，font weight，font size，font face等，该属性的值是CSS3字体字符串 |
| textAlign     | 文本参照X轴坐标如何进行水平对齐。取值：start， end，left，right，center，默认值为start|
| textBaseline  | 文本参照Y轴坐标如何进行垂直对齐。取值：top，middle，alphabetic，ideographic，bottom，默认值：alphabetic |


#### Canvas操作图像像素 **(Canvas通过getImageData方法获取图像像素)**
```bash
var data = context.getImageData(sx, sy, sWidth, sHeight)
````
sx、sy为所选区域的坐标，sWidth、sHeight为所选区域的宽度和高度。该方法返回一个指向ImageData对象的引用，其内容为所选区域中的原始像素信息，该对象包括以下三个属性：
  * width：所选区域中的每行有多少个像素。

  * height：所选区域中的每列有多少个像素。

  * data：像素数值的一维数组，保存所选区域中每个像素的RGBA（红、绿、蓝、alpha）值，按该区域从左到右、从上到下按RGBA格式依次保存，如[r1,g1,b1,a1,r2,g2,b2,a2...] 。因此，所选区域中的每个像素，在这个数组中就变成了四个整数值。
  

## 项目框架

```bash
├── /release            # Grunt构建后映射文件存放的文件夹
│ ├── /font-awesome     # font-awesome-icon的静态图片资源
│ ├── index.html        # 项目入口
│ ├── shape-shifter.js  # 映射后的js功能文件
│ └── site.min.css      # cssmin压缩style文件夹下的全部样式文件后的样式文件
├── /script             # 实现粒子绘制和动画js功能文件
│ ├── color.js          # 颜色
│ ├── dot.js            # 粒子
│ ├── drawing.js        # 绘制功能
│ ├── point.js          # 点
│ ├── shape-builder.js  # 粒子样式构建具体功能（文本、倒数、时间和图标）
│ ├── shape-shifter.js  # 粒子样式构建入口
│ ├── shape.js          # 粒子基本构建功能（打乱、组合）
│ ├── ui-tabs.js        # 弹出部分的分页栏功能
│ └── ui.js             # 项目主页面和弹出页的功能
├── /styles             # 样式的定义
├── Gruntfile.json      # Grunt配置文件
├── package.json        # 依赖包
└── README.md           # 项目文档
```
## 基础功能
>打乱粒子的位置
```bash
    shuffleIdle: function () {
      var a = S.Drawing.getArea();

      for (var d = 0; d < dots.length; d++) {
        if (!dots[d].s) {
          dots[d].move({
            x: Math.random() * a.w,
            y: Math.random() * a.h
          });
        }
      }
    },
```
>组合：根据由n传入所需绘制的样式，随机放置粒子。
```bash
    switchShape: function (n, fast) {
      var size,
          a = S.Drawing.getArea(),
          d = 0,
          i = 0;

      width = n.w;
      height = n.h;

      compensate();

      if (n.dots.length > dots.length) {
        size = n.dots.length - dots.length;
        for (d = 1; d <= size; d++) {
          dots.push(new S.Dot(a.w / 2, a.h / 2));
        }
      }

      d = 0;

      while (n.dots.length > 0) {
        i = Math.floor(Math.random() * n.dots.length);
        dots[d].e = fast ? 0.25 : (dots[d].s ? 0.14 : 0.11);

        if (dots[d].s) {
          dots[d].move(new S.Point({
            z: Math.random() * 20 + 10,
            a: Math.random(),
            h: 18
          }));
        } else {
          dots[d].move(new S.Point({
            z: Math.random() * 5 + 5,
            h: fast ? 18 : 30
          }));
        }

        dots[d].s = true;
        dots[d].move(new S.Point({
          x: n.dots[i].x + cx,
          y: n.dots[i].y + cy,
          a: 1,
          z: 5,
          h: 0
        }));

        n.dots = n.dots.slice(0, i).concat(n.dots.slice(i + 1));
        d++;
      }

      for (i = d; i < dots.length; i++) {
        if (dots[i].s) {
          dots[i].move(new S.Point({
            z: Math.random() * 20 + 10,
            a: Math.random(),
            h: 20
          }));

          dots[i].s = false;
          dots[i].e = 0.04;
          dots[i].move(new S.Point({ 
            x: Math.random() * a.w,
            y: Math.random() * a.h,
            a: 0.3, //.4
            z: Math.random() * 4,
            h: 0
          }));
        }
      }
    },
```
>移动
```bash
  _moveTowards: function (n) {
    var details = this.distanceTo(n, true),
        dx = details[0],
        dy = details[1],
        d = details[2],
        e = this.e * d;

    if (this.p.h === -1) {
      this.p.x = n.x;
      this.p.y = n.y;
      return true;
    }

    if (d > 1) {
      this.p.x -= ((dx / d) * e);
      this.p.y -= ((dy / d) * e);
    } else {
      if (this.p.h > 0) {
        this.p.h--;
      } else {
        return true;
      }
    }

    return false;
  }
  
  distanceTo: function (n, details) {
    var dx = this.p.x - n.x,
        dy = this.p.y - n.y,
        d = Math.sqrt(dx * dx + dy * dy); // 获取点位置x，y差值的平方和的平方根

    return details ? [dx, dy, d] : d;
  }
```
>更新：位置是以原坐标x，y减去随机值的正弦值；透明度是在0.1和原透明度与基准点之间的差值的5%之间的最大值；尺寸大小是在0.1和原尺寸与基准点之间的差值的5%之间的最大值。
```bash
 _update: function () {
    var p,
        d;

    if (this._moveTowards(this.t)) {
      p = this.q.shift(); // 获取点数组的第一个元素，并从数组中删去

      if (p) {
        this.t.x = p.x || this.p.x;
        this.t.y = p.y || this.p.y;
        this.t.z = p.z || this.p.z;
        this.t.a = p.a || this.p.a;
        this.p.h = p.h || 0;
      } else {
        if (this.s) {
          this.p.x -= Math.sin(Math.random() * 3.142);
          this.p.y -= Math.sin(Math.random() * 3.142);
        } else {
          this.move(new S.Point({
            x: this.p.x + (Math.random() * 50) - 25,
            y: this.p.y + (Math.random() * 50) - 25,
          }));
        }
      }
    }

    d = this.p.a - this.t.a;
    this.p.a = Math.max(0.1, this.p.a - (d * 0.05));
    d = this.p.z - this.t.z;
    this.p.z = Math.max(1, this.p.z - (d * 0.05));
  }
```
## 核心功能
>shapeCanvas 项目中用来绘制图形的HTML元素 

>shapeContext 绘图元素上的一个 CanvasRenderingContext2D 二维渲染

##### 处理画布
```bash
 function processCanvas() {
     // 获取指定区域内图像RGBA数据
    var pixels = shapeContext.getImageData(0, 0, shapeCanvas.width, shapeCanvas.height).data, 
        dots = [],
        x = 0,
        y = 0,
        fx = shapeCanvas.width,
        fy = shapeCanvas.height,
        w = 0,
        h = 0;
        
    // 由于每个像素点存储了RGBA四个数据，所以为了获得图像每个像素的信息，则需要以四为进制单位遍历
    for (var p = 0; p < pixels.length; p += (4 * gap)) {
    // 由于本项目粒子拼凑图像时均为白色，所以不需要每个像素点前三位RGB颜色的数据，只要通过指定位置像素的透明度来判断该位置是否有像素
      if (pixels[p + 3] > 0) {
        dots.push(new S.Point({
          x: x,
          y: y
        }));

        w = x > w ? x : w;
        h = y > h ? y : h;
        fx = x < fx ? x : fx;
        fy = y < fy ? y : fy;
      }

     // 重新描绘图像的默认直径值为gap变量的值，所以在设置重绘白点时以13为单位
      x += gap;

     // 一行结束开启新一行的遍历
      if (x >= shapeCanvas.width) {
        x = 0;
        y += gap;
        p += gap * 4 * shapeCanvas.width;
      }
    }

    return { dots: dots, w: w + fx, h: h + fy };
  }
```

##### font-awesome icon文件绘制
1. 图片加载：成功=>加载图片，失败=>加载文字“What？”。
2. 清空当前Canvas内容
3. 绘制宽、高为画布60%的图片
```bash
    imageFile: function (url, callback) {
      var image = new Image(),
          a = S.Drawing.getArea();

      image.onload = function () {
        shapeContext.clearRect(0, 0, shapeCanvas.width, shapeCanvas.height);
        shapeContext.drawImage(this, 0, 0, a.h * 0.6, a.h * 0.6);  // 绘制图片
        callback(processCanvas());
      };

      image.onerror = function () {
        callback(S.ShapeBuilder.letter('What?'));
      };

      image.src = url;
    },
```
##### 绘制圆：
1. 在Canvas内清除指定的像素
2. 绘制指定半径的圆，并用像素点填充
```bash
    circle: function (d) {
      var r = Math.max(0, d) / 2;
      shapeContext.clearRect(0, 0, shapeCanvas.width, shapeCanvas.height);
      shapeContext.beginPath();
      shapeContext.arc(r * gap, r * gap, r * gap, 0, 2 * Math.PI, false); // 画半径为r的路径
      shapeContext.fill(); // 填充圆路径里的内容
      shapeContext.closePath();

      return processCanvas();
    },
```
##### 绘制文本
1. 设置文本字体大小
2. 根据文本字长，比较文本长度和Canvas的宽度的80%
3. 清空Canvas内容
4. 	在画布上绘制“被填充的”文本
```bash
    letter: function (l) {
      var s = 0;

      setFontSize(fontSize);
      s = Math.min(fontSize,
                  (shapeCanvas.width / shapeContext.measureText(l).width) * 0.8 * fontSize, 
                  (shapeCanvas.height / fontSize) * (isNumber(l) ? 1 : 0.45) * fontSize);
      setFontSize(s);

      shapeContext.clearRect(0, 0, shapeCanvas.width, shapeCanvas.height);
      shapeContext.fillText(l, shapeCanvas.width / 2, shapeCanvas.height / 2);  // 填充文字

      return processCanvas();
    },
```
##### 绘制矩形
1. 从0-height（矩形高度），0-width（矩形宽度）双循环，获取矩形每个像素点的位置。
2. 根据获得像素位置数组，在数组对应位置画上像素圆点。
```bash
    rectangle: function (w, h) {
      var dots = [],
          width = gap * w,
          height = gap * h;

      for (var y = 0; y < height; y += gap) {
        for (var x = 0; x < width; x += gap) {
          dots.push(new S.Point({
            x: x,
            y: y,
          }));
        }
      }

      return { dots: dots, w: width, h: height };
    }
```
## 指令功能
>current是操作指令数组，action是操作指令的样式，value是操作指令的值

绘制文字指令
```bash
    S.Shape.switchShape(S.ShapeBuilder.letter(current[0] === cmd ? 'What?' : current));
```
倒计时指令：timedAction控制变化时间间隔为1秒
```bash
    value = parseInt(value, 10) || 10;
    value = value > 0 ? value : 10;

    timedAction(function (index) {
      if (index === 0) {
        if (sequence.length === 0) {
          S.Shape.switchShape(S.ShapeBuilder.letter(''));
        } else {
          performAction(sequence);
        }
      } else {
        S.Shape.switchShape(S.ShapeBuilder.letter(index), true);
      }
    }, 1000, value, true);
```
绘制矩形指令：value 是矩形长x宽字符串，限制了最大可展示矩形长宽为均30
```bash
    value = value && value.split('x');
    value = (value && value.length === 2) ? value : [maxShapeSize, maxShapeSize / 2];

    S.Shape.switchShape(S.ShapeBuilder.rectangle(Math.min(maxShapeSize, parseInt(value[0], 10)), Math.min(maxShapeSize, parseInt(value[1], 10))));
```
绘制圆指令：value 是圆的半径，限制了最大可展示圆的半径为30
```bash
    value = parseInt(value, 10) || maxShapeSize;
    value = Math.min(value, maxShapeSize);
    S.Shape.switchShape(S.ShapeBuilder.circle(value));
```
绘制时间指令：timedAction控制变化时间间隔为1秒，当前时间（时间格式MM-dd）值不一致的时候会更新Canvas画布。
```bash
    var t = formatTime(new Date());

    if (sequence.length > 0) {
      S.Shape.switchShape(S.ShapeBuilder.letter(t));
    } else {
      timedAction(function () {
        t = formatTime(new Date());
        if (t !== time) {
          time = t;
          S.Shape.switchShape(S.ShapeBuilder.letter(time));
        }
      }, 1000);
    }
```
绘制图标指令 ：value 是所需绘制的font-awesome icon 图片名称
```bash
    S.ShapeBuilder.imageFile('font-awesome/' + value + '.png', function (obj) {
      S.Shape.switchShape(obj);
    });
```

# 总结
此项目本质上是用白点代表像素点，并通过Canvas画布组合成每个时间状态下的图像，动态图则是通过在每个时间帧重绘了该时间点的图像，以达到类似倒计时的动效。
