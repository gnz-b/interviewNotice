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

## android中软键盘弹起会顶起fixed的底部元素
解决方法：
height不要使用100vh

使用window.innerHeight代替


如果使用React的话，注意高度的属性不要放在render中赋值，防止state更新时把高度也更新了，导致固定在底部的原素浮上来。

## 计算二进制数中1的个数

### 算法1
从低位往高位，和1按位&来计算位数

```javascript
function countOne(n) {
  let count = 0;
  while(n) {
    if (n & 1) {
      count++;
    }
    n = n>>1; // n往右移动1位
  }
  return count;
}
```

### 算法2
原理不明白

```javascript
function countOne2(n) {
  let num = n;
  let count = 0;
  while (num) {
    num = num & (num - 1);
    count++;
  }
  return count;
}
```
