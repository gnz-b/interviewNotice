## 获取窗口宽高几个变量的区别

- window.innerWidth 浏览器内部宽度大小
- window.outerWidth 整个浏览器的宽度大小
- screen.width 显示器的宽度大小

## 快速判断日期是否是同一天

可以使用toDateString()方法来判断

例如：

``` javascript
new Date('2019/1/1 12:30').toDateString(); // "Tue Jan 01 2019"
```

注意在new Date(dateString)中的参数 日期分割符号要用’/‘，不要用’-‘ 会有兼容性问题
