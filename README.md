用Canvas制作可以根据手势摆动的树
===========  
![最终效果](https://raw.githubusercontent.com/cyclegtx/rocked_tree/master/images/1.gif)  
根据工作的需要，制作一个摆动的树做为页面的背景。为了增加页面的交互性，我又为背景中的树增加了鼠标（触控）事件，使他能够根据鼠标（触控）做出相应的动作，当手指做上下或者左右滑动的时候树会跟着摆动。  


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
drawBranch同样是根据初始与结束坐标画出一条直线代表树枝。与树干不同的是树枝不再是垂直与地面而是与树干保持一定的角度，而且树枝的初始值是树干的结束点```(toX,toY)```。所以在drawBranch中我们加入新参数```angle```用来表示树枝与树干的垂直夹角α，这样就可以根据α算出toX与toY。请看图。  
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
<a href="https://github.com/cyclegtx/rocked_tree/tree/355ea5357df9b516950a3c368c440434d728bda1" target="_blank">点击查看历史代码</a>  

####Step4.修改drawBranch函数，重复画树枝    
在drawBranch函数的最后再次调用两次drawBranch  
```javascript
this.drawBranch(toX,toY,branchLen,branchWidth-1,angle+30);
this.drawBranch(toX,toY,branchLen,branchWidth-1,angle-30);
```
使其调用自己完成递归,注意这里传入的角度是在之前的角度的基础上在增加或者减少30度。  
为了使递归停下来我们需要一个停止条件，就是之前一直没有用到的```depth```参数。我们在每次画下一层之前使其减1表示已经完成了一层树枝的绘制，直至depth减小到0表示绘制完所有的层数。  
```javascript
function Tree(x,y,branchLen,branchWidth,depth,canvas){
    this.canvas = canvas || document.getElementById('canvas');
    this.ctx = this.canvas.getContext('2d');
    this.x = x||0;
    this.y = y||0;
    this.branchLen = branchLen||0;
    this.branchWidth = branchWidth||0;
    var depth = depth || 5;
    this.drawRoot(this.x,this.y,this.branchLen,this.branchWidth,depth);
}
Tree.prototype.drawRoot = function(x,y,branchLen,branchWidth,depth){
    var toX = x;
    var toY = y-branchLen;
    var depth = depth||5;
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
    depth--;
    if(depth>0){
      this.drawBranch(toX,toY,branchLen,branchWidth-1,30,depth);
      this.drawBranch(toX,toY,branchLen,branchWidth-1,-30,depth);
    }
}
Tree.prototype.drawBranch = function(x,y,branchLen,branchWidth,angle,depth){
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
    depth--;
    if(depth>0){
      this.drawBranch(toX,toY,branchLen,branchWidth-1,angle+30,depth);
      this.drawBranch(toX,toY,branchLen,branchWidth-1,angle-30,depth);
    }
}
```  
运行代码：  
![效果图](https://raw.githubusercontent.com/cyclegtx/rocked_tree/master/images/4-1.jpg)  
由于树之间角度过大，而且所有树枝长度都相等，看起来并不像一棵树。所以我们需要在Tree的构造函数中加入几个参数用来调整树的姿态。   
```javasript
function Tree(x,y,branchLen,branchWidth,depth,canvas){
    ......
    this.branchLenFactor = 0.8;
    this.rootLenFactor = 1.2;
    this.branchAngle = 20;
    ......
}
```  


branchLenFactor:画每一层树枝的时候乘在branchLen上面，用来控制树枝长度。
rootLenFactor:画树根的时候乘在branchLen上面，用来控制树根长度。
branchAngle: 用来控制树枝之间的角度。   


```javascript
  Tree.prototype.drawRoot = function(x,y,branchLen,branchWidth,depth){
    var toX = x;
    var toY = y-branchLen*this.rootLenFactor;
    var depth = depth||5;
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
    depth--;
    if(depth>0){
      this.drawBranch(toX,toY,branchLen*this.branchLenFactor,branchWidth-1,this.branchAngle,depth);
      this.drawBranch(toX,toY,branchLen*this.branchLenFactor,branchWidth-1,-this.branchAngle,depth);
    }
  }
  Tree.prototype.drawBranch = function(x,y,branchLen,branchWidth,angle,depth){
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
    depth--;
    if(depth>0){
      this.drawBranch(toX,toY,branchLen*this.branchLenFactor,branchWidth-1,angle+this.branchAngle,depth);
      this.drawBranch(toX,toY,branchLen*this.branchLenFactor,branchWidth-1,angle-this.branchAngle,depth);
    }
  }
```  
运行代码：  
![效果图](https://raw.githubusercontent.com/cyclegtx/rocked_tree/master/images/4-2.jpg)    
<a href="https://github.com/cyclegtx/rocked_tree/tree/a450d7faedbc6dfff2f78773ce2c8299a971178b" target="_blank">点击查看历史代码</a>  

####Step5.使树枝晃动起来

为了使树枝有摇晃的效果，我们只需要改变树枝之间的角度branchAngle就可以了。我需要在Tree的构造函数中增加三个新属性：```oBranchAngle```用来记录初始角度；```branchAngleFactor```用来控制角度随时间变化的变化量；```swingAngle```:随时间增加用来记录摇动的角度。  
同时修改下drawRoot函数使其不用接受参数。调用更加方便。

```javascript
function Tree(x,y,branchLen,branchWidth,depth,canvas){
    ......
    this.branchAngle = 20;
    this.oBranchAngle = this.branchAngle;
    this.branchAngleFactor = 5;
    this.swingAngle = 0;
    ......
    this.drawRoot();
}

Tree.prototype.drawRoot = function(){
    var x = this.x,y=this.y,branchLen = this.branchLen,depth = this.depth,branchWidth = this.branchWidth;
    var toX = x;
    var toY = y-branchLen*this.rootLenFactor;
    var depth = depth||5;
    this.ctx.save();
    this.ctx.strokeStyle="rgba(37, 141, 194, 0.93)";
    this.ctx.beginPath();
    this.ctx.lineCap = "butt";
    this.ctx.lineJoin="round";
    this.ctx.lineWidth = this.branchWidth;
    this.ctx.moveTo(x,y);
    this.ctx.lineTo(toX,toY);
    this.ctx.closePath();
    this.ctx.stroke();
    this.ctx.restore();
    depth--;
    if(depth>0){
      this.drawBranch(toX,toY,branchLen*this.branchLenFactor,branchWidth-1,this.branchAngle,depth);
      this.drawBranch(toX,toY,branchLen*this.branchLenFactor,branchWidth-1,-this.branchAngle,depth);
    }
  }
```

增加循环函数，在循环函数中重绘整个树，并且每次重绘都要修改branchAngle值，使大树摇动起来。```atree.swingAngle++;```使摇动角度随时间变化。这里使用```Math.sin(atree.swingAngle*(Math.PI/180))```可以获得一个-1至1之间的连续变化值。```atree.branchAngle = Math.sin(atree.swingAngle*(Math.PI/180))*atree.branchAngleFactor+atree.oBranchAngle;```乘以系数并加在原角度上。


```javascript
function loop(time){
    ctx.clearRect(0,0,canvas.width,canvas.height);
    atree.branchAngle = Math.sin(atree.swingAngle*(Math.PI/180))*atree.branchAngleFactor+atree.oBranchAngle;
    atree.drawRoot()
    requestAnimFrame(loop);
  }
  loop(0);
```

运行代码：  
![效果图](https://raw.githubusercontent.com/cyclegtx/rocked_tree/master/images/5.gif)    
<a href="https://github.com/cyclegtx/rocked_tree/tree/1fc535215e0fdc86f95329518d4de8aae73b8c61" target="_blank">点击查看历史代码</a>    


####Step6.添加手势  
这里为了省事只添加了touch事件，mouse事件与touch事件的处理方法大体一致。  
首先为Tree新加一个属性```swingSwitch = true```用来表示大树是否摆动。当手指触控到屏幕的时候摆动停止，离开屏幕的时候摆动继续。  
添加```strengthX```,```strengthY```两个属性;分别表示树在x轴与y轴因受到的力而移动的距离。  
添加```strengthXFactor```,```strengthYFactor```；分别用来表示再一次滑动中x轴与y轴移动的最大距离。  




```javascript
function Tree(x,y,branchLen,branchWidth,depth,canvas){
    ......
    this.swingSwitch = true;
    ......
    this.strengthX = 0;
    this.strengthY = 0;
    ......
  }
```  
```javascript
//记录触控开始时的信息
var touchStart = {x:0,y:0,strengthX:0,strengthY:0};
document.addEventListener('touchstart',function(e){
    //让树停止摆动
    atree.swingSwitch = false;
    touchStart.x = e.touches[0].clientX;
    touchStart.y = e.touches[0].clientY;
    //记录触控开始时,原strength的值
    touchStart.strengthX = atree.strengthX;
    touchStart.strengthY = atree.strengthY;
});
document.addEventListener('touchmove',function(e){
    //阻止浏览器默认动作
    e.preventDefault();
    //(touchStart.x-e.touches[0].clientX)/canvas.width可以根据滑动距离获得一个0-1的值
    atree.strengthX = touchStart.strengthX-(touchStart.x-e.touches[0].clientX)/canvas.width*atree.strengthXFactor;
    atree.strengthY = touchStart.strengthY-(touchStart.y-e.touches[0].clientY)/canvas.height*atree.strengthYFactor;
});
document.addEventListener('touchend',function(e){
    //恢复摆动
    atree.swingSwitch = true;
});
```  

修改drawBranch将strength的变化添加到角度与toX，toY的计算中，详情见注释。

```javascript
Tree.prototype.drawBranch = function(x,y,branchLen,branchWidth,angle,depth){
    
    var angle = angle || 0;
    //用strengthX乘以(depth/this.depth)使得树枝末梢对角度的变化不敏感
    angle += this.strengthX*(depth/this.depth)/this.strengthXFactor*this.branchAngle;
    var radian = (90-angle)*(Math.PI/180);
    //用strengthX乘以(1-depth/this.depth)使得树枝末梢对角度的变化敏感
    var toX = x+Math.cos(radian)*branchLen+this.strengthX*(1-depth/this.depth);
    var toY = y-Math.sin(radian)*branchLen+this.strengthY*(1-depth/this.depth);
    ......
}
```

在动画循环中添加恢复代码，使strengthX，strengthY恢复为0，并增加swingSwitch的判断。
```javascript
function loop(time){
    ......
    //当swingSwitch开启时开始摆动
    if(atree.swingSwitch){
        //将strength恢复到0
      if(atree.strengthX >0){
        atree.strengthX -= 1;
      }
      if(atree.strengthX <0){
        atree.strengthX += 1;
      }
      if(atree.strengthY >0){
        atree.strengthY -= 1;
      }
      if(atree.strengthY <0){
        atree.strengthY += 1;
      }
      atree.swingAngle++;
      atree.branchAngle = Math.sin(atree.swingAngle*(Math.PI/180))*atree.branchAngleFactor+atree.oBranchAngle;
    }
    ......
}
loop(0);
```

运行代码：  
![效果图](https://raw.githubusercontent.com/cyclegtx/rocked_tree/master/images/6.gif)    
<a href="https://github.com/cyclegtx/rocked_tree/tree/fc396c3490ad873c061145baed773ad83057e1b5" target="_blank">点击查看历史代码</a>  

####Step7.添加缓动效果  
Step6中的恢复strengthX,strengthY的代码过于简单，动画匀速恢复到0，显得过于突兀。比较真实的情况应该是由快变慢的恢复，所以我们要为恢复代码加上缓动。首先在Tree中添加```recoverStartTime = 0```用来记录恢复开始的时间，在手指离开屏幕的时候(touchend)将其赋为0，同时用```oStrengthX```,```oStrengthY```记录下来strengthX与strengthY的目标值。


```javascript
function Tree(x,y,branchLen,branchWidth,depth,canvas){
    ......
    this.recoverStartTime = 0;
    ......
}
function loop(time){
    ......
    if(atree.swingSwitch){
      if(atree.strengthX > 0){
        if(atree.recoverStartTime == 0){
          atree.recoverStartTime = time;
        }
        var t = time-atree.recoverStartTime;
        //五次方的缓动
        atree.strengthX =  Math.max(atree.oStrengthX-atree.oStrengthX*((t=t/2000-1)*t*t*t*t + 1)+0,0);
      }
      if(atree.strengthX < 0){
        if(atree.recoverStartTime == 0){
          atree.recoverStartTime = time;
        }
        var t = time-atree.recoverStartTime;
        //五次方的缓动
        atree.strengthX =  Math.min(atree.oStrengthX-atree.oStrengthX*((t=t/2000-1)*t*t*t*t + 1)+0,0);
      }
      if(atree.strengthY > 0){
        if(atree.recoverStartTime == 0){
          atree.recoverStartTime = time;
        }
        var t = time-atree.recoverStartTime;
        //五次方的缓动
        atree.strengthY =  Math.max(atree.oStrengthY-atree.oStrengthY*((t=t/2000-1)*t*t*t*t + 1)+0,0);
      }
      if(atree.strengthY < 0){
        if(atree.recoverStartTime == 0){
          atree.recoverStartTime = time;
        }
        var t = time-atree.recoverStartTime;
        //五次方的缓动
        atree.strengthY =  Math.min(atree.oStrengthY-atree.oStrengthY*((t=t/2000-1)*t*t*t*t + 1)+0,0);
      }
    }
    ......
}
document.addEventListener('touchend',function(e){
  atree.recoverStartTime = 0;
  atree.oStrengthX = atree.strengthX;
  atree.oStrengthY = atree.strengthY;
  ......
});
```   

运行代码：  
![效果图](https://raw.githubusercontent.com/cyclegtx/rocked_tree/master/images/7.gif)
<a href="https://github.com/cyclegtx/rocked_tree/tree/ed015c82fe1dd310058bf07a1ea98314583c73a7" target="_blank">点击查看历史代码</a>      


####Step7.使树干摇动并移至屏幕左边
修改drawRoot使树干也可以晃动，并修改```var atree = new Tree(10,canvas.height,100,8,8,canvas);```使其移至左边。


```javascript
Tree.prototype.drawRoot = function(){
    ......
    //增加strength
    var angle = 0;
    angle += this.strengthX/this.strengthXFactor*this.branchAngle;
    var radian = (90-angle)*(Math.PI/180);
    var toX = x+Math.cos(radian)*branchLen*this.rootLenFactor;
    var toY = y-Math.sin(radian)*branchLen*this.rootLenFactor;
    ......
}
var atree = new Tree(10,canvas.height,100,8,8,canvas);
```
运行代码：  
![效果图](https://raw.githubusercontent.com/cyclegtx/rocked_tree/master/images/1.gif)
<a href="https://github.com/cyclegtx/rocked_tree/tree/ed015c82fe1dd310058bf07a1ea98314583c73a7" target="_blank">点击查看历史代码</a>     

####Step8.   
将动画循环中处理角度的部分添加到Tree的swing()中。   
```javascript
Tree.prototype.swing = function(time){
    this.ctx.clearRect(0,0,this.canvas.width,this.canvas.height);
    if(this.swingSwitch){
      if(this.strengthX > 0){
        if(this.recoverStartTime == 0){
          this.recoverStartTime = time;
        }
        var t = time-this.recoverStartTime;
        this.strengthX =  Math.max(this.oStrengthX-this.oStrengthX*((t=t/2000-1)*t*t*t*t + 1)+0,0);
      }
      if(this.strengthX < 0){
        if(this.recoverStartTime == 0){
          this.recoverStartTime = time;
        }
        var t = time-this.recoverStartTime;
        this.strengthX =  Math.min(this.oStrengthX-this.oStrengthX*((t=t/2000-1)*t*t*t*t + 1)+0,0);
      }
      if(this.strengthY > 0){
        if(this.recoverStartTime == 0){
          this.recoverStartTime = time;
        }
        var t = time-this.recoverStartTime;
        this.strengthY =  Math.max(this.oStrengthY-this.oStrengthY*((t=t/2000-1)*t*t*t*t + 1)+0,0);
      }
      if(this.strengthY < 0){
        if(this.recoverStartTime == 0){
          this.recoverStartTime = time;
        }
        var t = time-this.recoverStartTime;
        this.strengthY =  Math.min(this.oStrengthY-this.oStrengthY*((t=t/2000-1)*t*t*t*t + 1)+0,0);
      }
      this.swingAngle++;
      this.branchAngle = Math.sin(this.swingAngle*(Math.PI/180))*this.branchAngleFactor+this.oBranchAngle;
    }
    this.drawRoot();
}
var atree = new Tree(10,canvas.height,100,8,8,canvas);
function loop(time){
    atree.swing(time);
    requestAnimFrame(loop);
}
loop(0);
```
运行代码：  
![效果图](https://raw.githubusercontent.com/cyclegtx/rocked_tree/master/images/1.gif)


更多教程请访问：[ued.sexy](http://ued.sexy)  