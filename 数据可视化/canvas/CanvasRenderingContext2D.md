## canvasRender
* [2d的api]("https://developer.mozilla.org/zh-CN/docs/Web/API/CanvasRenderingContext2D")
* CanvasRenderingContext2D是canvas api的一部分，为canvas绘图提供2d渲染上下文。`可以用于绘制图像，文本，形状和其他对象`

## 获取2d渲染上下文
* 首先需要有一个canvas元素，然后使用getContext方法，注意传递的参数是2d
* 然后我们首先做一个最简单的例子：画一条线
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <canvas id="canvas" width="600" height="600"></canvas>
    <script>
        let canvas = document.getElementById("canvas");
        let ctx = canvas.getContext("2d");
        ctx.beginPath()
        ctx.moveTo(50,100)
        ctx.lineTo(100,200)
        ctx.closePath()
        ctx.stroke()
    </script>
</body>
</html>
```

### 绘制矩形
1. fillRect(x,y,width,height):填充矩形,`会直接在画布上填充，不会修改当前路径`
* 矩形的起点是(x,y),填充的宽度是width,高度是height
* 绘制一个黑色填充的矩形
```html
    <canvas id="canvas" width="600" height="600"></canvas>
    <script>
        let canvas = document.getElementById("canvas");
        let ctx = canvas.getContext("2d");
        ctx.fillRect(50,50,100,200)
    </script>
```
2. strokeRect(x,y,width,height):`描边矩形`，直接在画布上绘制，不会修改当前路径
* 矩形的起点是(x,y),宽度是width,高度是height
* 绘制一个黑色描边的矩形`中间透明，边框宽度和颜色都是默认值`
```html
    <canvas id="canvas" width="600" height="600"></canvas>
    <script>
        let canvas = document.getElementById("canvas");
        let ctx = canvas.getContext("2d");
        ctx.strokeRect(50,50,100,200)
    </script>
```
3. clearRect(x,y,width,height):`把矩形范围内的像素设置为透明以达到擦除矩形的效果`
* `如果没有按照绘制路径的步骤，那么可能导致意想不到的效果。`
* `确保在调用clearRect之后，在绘制新内容之前，调用beginPath`
* 擦除的矩形起点是(x,y),宽度是width,高度是height
```html
<canvas id="canvas" width="600" height="600"></canvas>
    <script>
        let canvas = document.getElementById("canvas");
        let ctx = canvas.getContext("2d");
        ctx.lineWidth = 1;
        ctx.strokeRect(1.5,1.5,100,200)
        // 中线: 1.5 - 101.5
        // 实际区域 x:1-102,y:1-202
        // 注意起点和宽高
        // 清除 x:1-102,y:1-102
        ctx.clearRect(1,1,101,101)
    </script>
```
* `从这里可以引申出一个1px像素问题，lineWidth的绘制方式？看下文`
* [参考]("https://developer.mozilla.org/zh-CN/docs/Web/API/Canvas_API/Tutorial/Applying_styles_and_colors")

### 绘制文本
1. fillText(text,x,y,[maxWidth]):是一个用来在(x,y)位置填充文本的api
* `如果设置了最大宽度，文本会进行缩放以适应最大宽度`
```html
 <canvas id="canvas" width="600" height="600"></canvas>
    <script>
      let canvas = document.getElementById("canvas");
      let ctx = canvas.getContext("2d");
      ctx.fillText("hello world", 100, 50);
      ctx.fillText("hello world", 100, 100, 40);

    </script>
```
* 可以看到设置了最大宽度，那么文本会被整体缩放，`高度也会被缩放`
* 并且发现一个问题，`在坐标为(0,0)的时候，文本绘制无法显示，所以canvas文本定位是怎么处理的？`








