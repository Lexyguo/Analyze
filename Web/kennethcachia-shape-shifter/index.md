# Web前端Canvas绘图项目实例 -- Shape Shifter
[kennethcachia/shape-shifter](https://github.com/kennethcachia/shape-shifter)项目是一个用canvas绘制粒子，以Grunt为基础构建工具，通过对粒子的指定组合来实现不同形状渲染的画布实验项目，还支持文本、倒数、时间和图标的模式。

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
## 核心功能
>shapeCanvas 项目中用来绘制图形的HTML元素 

>shapeContext 绘图元素上的一个 CanvasRenderingContext2D 二维渲染

font-awesome icon文件绘制
```bash
    imageFile: function (url, callback) {
      var image = new Image(),
          a = S.Drawing.getArea();

      image.onload = function () {
        shapeContext.clearRect(0, 0, shapeCanvas.width, shapeCanvas.height);
        shapeContext.drawImage(this, 0, 0, a.h * 0.6, a.h * 0.6);
        callback(processCanvas());
      };

      image.onerror = function () {
        callback(S.ShapeBuilder.letter('What?'));
      };

      image.src = url;
    },
```
绘制圆
```bash
    circle: function (d) {
      var r = Math.max(0, d) / 2;
      shapeContext.clearRect(0, 0, shapeCanvas.width, shapeCanvas.height);
      shapeContext.beginPath();
      shapeContext.arc(r * gap, r * gap, r * gap, 0, 2 * Math.PI, false);
      shapeContext.fill();
      shapeContext.closePath();

      return processCanvas();
    },
```
绘制文本
```bash
    letter: function (l) {
      var s = 0;

      setFontSize(fontSize);
      s = Math.min(fontSize,
                  (shapeCanvas.width / shapeContext.measureText(l).width) * 0.8 * fontSize, 
                  (shapeCanvas.height / fontSize) * (isNumber(l) ? 1 : 0.45) * fontSize);
      setFontSize(s);

      shapeContext.clearRect(0, 0, shapeCanvas.width, shapeCanvas.height);
      shapeContext.fillText(l, shapeCanvas.width / 2, shapeCanvas.height / 2);

      return processCanvas();
    },
```
绘制矩形
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
倒计时指令
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
绘制时间指令
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