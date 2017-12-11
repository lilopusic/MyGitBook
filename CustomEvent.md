自定义事件使用
-------
最近做了一个功能，大概是这个样子的，有两层堆叠在一起的div元素，下面那一层有拖拽的功能，但是上面的一层div元素遮盖住了下方的div，造成的现象是当拖拽到上层div所在的区域之时，下方的mousemove/mousedown/mouseup等事件无法触发。

所以要做的事情很简单，在上层的div区域，手动触发下方的div，这里便可以用自定义事件来解决。

贴一下代码：
```js

let contents = document.body.querySelectorAll('.needPreventDefault');
contents = Array.prototype.slice.call(content);
for(let i=0;i<contents.length;i++) {
    bindEvent(contents[i], ['mousemove', 'mousedown', 'mouseup']);
}
//绑定所有传入的事件
function bindEvent(dom, typeList) {
    for(let i=0; i<typeList.length; i++) {
        dom.addEventListener(typeList[i], function(){
            event.stopPropagation();
            document.getElementById(targetId).dispatchEvent(new MouseEvent(event.type,event));
        }, false);
    }
}
//在指定ID的dom上触发一个事件
function dispatchCustomEvent(event, targetId) {
    let eventOption = {};
    for(let key in event) {
        if(/(x|y)/i.test(key)) {
            eventOption[key] = event[key];
        }
    }
    document.getElementById(targetId).dispatchEvent(new MouseEvent(event.type,eventOption));
}

```
这样，当鼠标在具有*needPreventDefault*类的dom上触发的mousemove/mousedown/mouseup事件，会被阻止冒泡并且手动触发到id为*targetId*的dom元素上。

一般来说这样就可以了，最后的这一个函数在使用某些浏览器的时候可能会有用。当时发生的原因记不清楚了之后也没有复现出来，现象是控制台报错，错误信息是事件已经在dom上执行了，所以加上这个hack的办法，只取出来事件的位置信息，其他都不要，再传过去。
