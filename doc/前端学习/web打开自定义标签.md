## web页打开新的标签页-自定义设置

### 自定义设置窗口
在web前端页面，当想在新的标签页窗口打开展示的内容，以及设置打开窗口的位置，以及大小。可以参照以下设置：
```js
window.open(url, '_blank', 'toolbar=yes,scrollbars=yes,resizable=yes,top=500,left=500,width=400,height=400');
```
### 在原窗口重复打开

按照上述代码，可以每次新打开一个标签页窗口。针对如果下次触发点击事件，不是再新打开，而是在原来的基础上加载，可以是参照以下设置：（以下设置是：打开新的标签页窗口之前关闭已打开的标签页窗口）
```js
  function openAndClosePrevious(url) {
        // 关闭之前的窗口
        if (previousWindow && !previousWindow.closed) {
            previousWindow.close();
        }
        // 在新窗口中打开链接
        previousWindow = window.open(url, '_blank', 'toolbar=yes,scrollbars=yes,resizable=yes,top=500,left=500,width=400,height=400');
    }
```