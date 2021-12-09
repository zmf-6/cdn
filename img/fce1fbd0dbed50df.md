### 一、canvas画布
Canvas是HTML5中新出的一个元素，开发者可以通过JS脚本动态绘制图像。
#### 1. 创建canvas画布
```
在页面中创建canvas标签,并设置其id和宽高 (不要通过css设置,会有bug)

<canvas id="myCanvas" width="500" height="500">
```

#### 2. 设置画布
```
// 1. 通过js设置画布宽高
var canvas = document.getElementById('myCanvas');
canvas.width = 800;
canvas.height = 600;

// 2. 获取上下文对象 （可以理解为获取画笔）
var cxt = canvas.getContext('2d');
//重置圆点
ctx.translate(x,y);
```

### 二、绘制 - 线条

方法 | 描述
---|---
beginPath() | 开启路径
moveTo(x,y) | 起始点
lineTo(x,y) | 下一个点
closePath() | 闭合路径
stroke() | 描边绘制
fill() | 填充绘制

// 画封闭图填充颜色
由线条组成的封闭图 不是完整的封闭图 填充不上颜色
由点连接形成的封闭图，可以填充颜色


属性 | 描述
---|---
strokeStyle | 描边颜色
fillStyle | 填充颜色
lineWidth | 粗细
lineCap | 设置或返回线条端点样式 <br> butt 默认,平直边缘<br> round 圆形线帽 <br> square 正方形线帽 
lineJoin | 设置或返回两条相交线的拐角<br> miter  默认,尖角<br> round 圆角 <br> bevel 斜角

### 三、绘制 - 矩形

方法 | 描述
---|---
rect(x,y,width,height) | 需配合stroke()或fill()方法绘制矩形
fillRect(x,y,width,height) | 绘制填充矩形
strokeRect(x,y,width,height) | 绘制矩形边框
clearRect(x,y,width,height) | 清除指定矩形区域

### 四、绘制 - 圆弧

==弧线==`arc(x,y,r,sAngle,eAngle,counterclockwise)`

参数 | 描述
---|---
x,y | 圆心的坐标
r | 圆的半径
sAngle| 起始弧度
eAngle | 结束弧度<br>弧度 = Math.PI/180*角度
counterclockwise | 可选。true逆时针,false顺时针
Math.PI = 数学 ∏
==两切线之间的弧线== `arcTo(x1,y1,x2,y2,r)`
参数 | 描述
---|---
x1,y1 | 弧的起点坐标
x2,y2 | 弧的终点坐标
r | 半径

==绘制扇形==
```
cxt.moveTo(x,y);
cxt.arc(x,y...);
cxt.closePath();
```

### 五、绘制 - 文本

属性 | 描述
---|---
font | 设置或返回文本的当前字体属性
textAlign | 设置或返回文本的对齐方式
textBaseline | 设置或返回文本的基线

方法 | 描述
---|---
fillText(text,x,y) | 绘制填充文本
strokeText(text,x,y) | 绘制描边文本

### 六、绘制 - 图像

```
drawImage(img,x,y,width,height)
```
参数 | 描述
---|---
img | 要使用的图像
x | 绘制的起始位置x坐标
y | 绘制的起始位置y坐标
width | 可选。宽度
height | 可选。高度

### 七、绘制 - 转换
方法|描述
---|---
scale(scalewidth,scaleheight)	|缩放当前绘图至更大或更小
scalewidth	缩放当前绘图的宽度 (1=100%, 0.5=50%, 2=200%, 依次类推)
scaleheight	缩放当前绘图的高度 (1=100%, 0.5=50%, 2=200%, 依次类推)
rotate(Math.PI/180*角度)|	旋转当前绘图
translate(x,y)|	重新设置画布原点
x 添加到水平坐标（x）上的值
y 添加到垂直坐标（y）上的值
transform()	|替换绘图的当前转换矩阵
setTransform()|	将当前转换重置为单位矩阵。然后运行 transform()

### 八、绘制 - 渐变色
###### 线性渐变
```
var lg = ctx.createLinearGradient(x, y, x1, y1);
lg.addColorStop(渐变位置,颜色);
ctx.strokeStyle = lg;
```

###### 径向渐变
```
var rg = cxt.createRadialGradient(起始圆x, 起始圆y, 半径, 结束圆x, 结束圆y, 半径);
rg.addColorStop(渐变位置,颜色);
ctx.strokeStyle = rg;
```

### 九、多图形组合方式
```
ctx.globalCompositeOperation = 
'source-over' //后画覆盖先画
'destination-over' //后画清空先画
```
### 十、保存画布
cvs.toDataURL(); 










