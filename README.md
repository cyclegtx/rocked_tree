用Canvas制作可以根据手势摆动的树
===========
根据工作的需要，制作一个摆动的树做为页面的背景。为了增加页面的交互性，我又为背景中的树增加了鼠标（触控）事件，使他能够根据鼠标（触控）做出相应的动作，当手指做上下或者左右滑动的时候树会跟着摆动。先看看最终效果。
![最终效果](https://raw.githubusercontent.com/cyclegtx/rocked_tree/master/images/1.gif)

####Step1.完成HTML页面，新建一个Tree类  

完成HTML页面后新建一个Tree类用来记录树的各个属性。其中```x,y```为树根部的坐标值，```branchLen,branchWidth```分别是树枝的长度与宽度，```depth```为树枝的层数，```canvas```用来接页面中的canvas元素（默认是ID为canvas的元素）。
```javascript
function Tree(x,y,branchLen,branchWidth,depth,canvas){
	this.canvas = canvas || document.getElementById('canvas');
	this.ctx = this.canvas.getContext('2d');
	this.x = x||0;
	this.y = y||0;
	this.branchLen = branchLen||0;
	this.branchWidth = branchWidth||0;
	var depth = depth || 5;
}
```
<a href="https://github.com/cyclegtx/rocked_tree/tree/9cd00a685bd310b6824b1f006822b590b315ed5f" target="_blank">点击查看历史代码</a>

####Step2.添加drawRoot方法，用来绘制树干

首先在drawRoot中画第一个枝干。drawRoot的参数意义同上。并且在Tree类的构造函数中运行drawRoot并把Tree接受到的参数传入。最后new一个Tree类，使树根位于屏幕的底部正中心，树枝长100px，树枝宽度为8px，树枝层数为8层（暂时用不上）。```var atree = new Tree(canvas.width/2-4,canvas.height,100,8,8,canvas);```  

在drawRoot中我们需要用```lineTo()```画出树枝。树枝的起始的坐标值```(x,y)```已经给出，结束的坐标值```(toX,toY)```需要进行计算。第一个画的是树干，由于树干垂直于地面所以结束坐标```toX```等于初始坐标```x```,而结束坐标```toY```等于初始```y```减去树干长度```branchLen```(注意坐标的0,0点在canvas的左上角)。```var toX = x;var toY = y-branchLen;```
```javascript
function Tree(x,y,branchLen,branchWidth,depth,canvas){
	this.canvas = canvas || document.getElementById('canvas');
	this.ctx = this.canvas.getContext('2d');
	this.x = x||0;
	this.y = y||0;
	this.branchLen = branchLen||0;
	this.branchWidth = branchWidth||0;
	var depth = depth || 5;
	this.drawRoot(this.x,this.y,this.branchLen,this.branchWidth);
}
Tree.prototype.drawRoot = function(x,y,branchLen,branchWidth){
	var toX = x;
	var toY = y-branchLen;
	this.ctx.save();
	this.ctx.strokeStyle="rgba(37, 141, 194, 0.93)";
	this.ctx.beginPath();
	this.ctx.lineCap = "butt";
	this.ctx.lineJoin="round";
	this.ctx.lineWidth = branchWidth;
	this.ctx.moveTo(x,y);
	this.ctx.lineTo(toX,toY);
	this.ctx.closePath();
	this.ctx.stroke();
	this.ctx.restore();
}
var atree = new Tree(canvas.width/2-4,canvas.height,100,8,8,canvas);
```
运行代码：  
![效果图](https://raw.githubusercontent.com/cyclegtx/rocked_tree/master/images/2.jpg)   
<a href="https://github.com/cyclegtx/rocked_tree/tree/dba5140fd14365d228147e936d97573364ab23fa" target="_blank">点击查看历史代码</a>

####Step3.添加drawBranch方法，用来绘制树枝  
drawBranch同样是根据初始与结束坐标画出一条直线代表树枝。与树干不同的是树枝不再是垂直与地面而是与树干保持一定的角度。所以在drawBranch中我们加入新参数```angle```用来表示树枝与树干的垂直夹角α，这样就可以根据α算出toX与toY。请看图。  
![效果图](https://raw.githubusercontent.com/cyclegtx/rocked_tree/master/images/3-1.jpg)   
这样我们在画完树干后再分别画两个不同角度的树枝，一个是```30°```一个```-30°```。并将传给树枝的宽度branchWidth减小一个像素，使其与树干粗细不同。

```javascript
Tree.prototype.drawRoot = function(x,y,branchLen,branchWidth){
	var toX = x;
	var toY = y-branchLen;
	this.ctx.save();
	this.ctx.strokeStyle="rgba(37, 141, 194, 0.93)";
	this.ctx.beginPath();
	this.ctx.lineCap = "butt";
	this.ctx.lineJoin="round";
	this.ctx.lineWidth = branchWidth;
	this.ctx.moveTo(x,y);
	this.ctx.lineTo(toX,toY);
	this.ctx.closePath();
	this.ctx.stroke();
	this.ctx.restore();
	this.drawBranch(toX,toY,branchLen,branchWidth-1,30);
	this.drawBranch(toX,toY,branchLen,branchWidth-1,-30);
}
Tree.prototype.drawBranch = function(x,y,branchLen,branchWidth,angle){
	var angle = angle || 0;
	var radian = (90-angle)*(Math.PI/180);
	var toX = x+Math.cos(radian)*branchLen;
	var toY = y-Math.sin(radian)*branchLen;
	this.ctx.save();
	this.ctx.strokeStyle="rgba(37, 141, 194, 0.93)";
	this.ctx.beginPath();
	this.ctx.lineCap = "butt";
	this.ctx.lineJoin="round";
	this.ctx.lineWidth = branchWidth;
	this.ctx.moveTo(x,y);
	this.ctx.lineTo(toX,toY);
	this.ctx.closePath();
	this.ctx.stroke();
	this.ctx.restore();
}
```

运行代码：  
![效果图](https://raw.githubusercontent.com/cyclegtx/rocked_tree/master/images/3.jpg) 