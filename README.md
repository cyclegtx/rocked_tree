用Canvas制作可以根据手势摆动的树
===========
根据工作的需要，制作一个摆动的树做为页面的背景。为了增加页面的交互性，我又为背景中的树增加了鼠标（触控）事件，使他能够根据鼠标（触控）做出相应的动作。先看看最终效果。
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