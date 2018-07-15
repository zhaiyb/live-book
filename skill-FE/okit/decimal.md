###数字精度{#decimal}
整体思路：只有小数才会触发转换二进制时无限循环的问题。所以想办法先变为整数（不能直接num \* 10 ** x）
可参考[okit](http://gitlab.okcoin-inc.com/okfe/util/okit/blob/master/packages/calc/src/index.js)
实现方式，以整数进行运算。

```js
/**
 * 把小数转成整数，支持科学计数法。如果是小数则放大成整数
 */
function float2Integer(num) {
  if (!num.toString().match(/[eE]/)) {
    return Number(num.toString().replace('.', ''));
  }
  const dLen = digitLength(num);
  return dLen > 0 ? num * (10 ** dLen) : num;
}
/**
 * 0.07 * 100 = 0.7000000000000001
 * 精确乘法
 */
function calcMul(num1, num2, ...others) {
  if (others.length > 0) {
    return calcMul(calcMul(num1, num2), others[0], ...others.slice(1));
  }
  const num1Changed = float2Integer(num1);
  const num2Changed = float2Integer(num2);
  const baseNum = digitLength(num1) + digitLength(num2);
  const leftValue = num1Changed * num2Changed;
  //checkBoundary(leftValue);
  return leftValue / (10 ** baseNum);
}
```
> 参考链接

[JavaScript 浮点数陷阱及解法](https://github.com/camsong/blog/issues/9)

[二进制和十进制之间的相互转换](https://blog.csdn.net/maoguiyou/article/details/50461137)