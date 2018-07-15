###添加千分位
* 千分位正则`/(\d{1,3})(?=(\d{3})+$)/g`
* 正则的`?=`正向前瞻（即作为限制条件，但不会放到匹配结果中）
* 注意第二个子规则最后$的重要性
  ```
  //以12345678为例
  if(没有最后的$限制) {    //错误的
    此时正则为`/(\d{1,3})(?=(\d{3})+)/g`
    匹配为[123,45]
    因为123后面跟着456，符合这种规则；45后面跟着678，符合这种规则
  }
  if(存在最后的$限制) {    //正确的
    此时正则为`/(\d{1,3})(?=(\d{3})+$)/g`
    匹配为[12,345]
    因为必须以`检查3的倍数位数字*结尾*`
    所以123后面跟的45678就不能满足规则，
    而12后面跟的是345678，可以满足，
    345后面跟的是678，可以满足
  }
  ```
  **提问：**在上面错误的正则下，为什么匹配的不是[1,2,3,4,5]呢，因为他们都符合规则呀

  **回答：**非常棒！这时候就涉及到了"贪婪匹配"和"非贪婪匹配"，暂且不码字说明了，Google一下，或者查看后面的参考链接吧。

* 支持小数：将整数和小数分开，高效使用[replce](http://www.w3school.com.cn/jsref/jsref_replace.asp)函数

```js
/**
 * 添加千分位
 */
function thousandFormat(num) {
  const thousandReg = /(\d{1,3})(?=(\d{3})+$)/g; // 千分位正则
  const intFloatReg = /(\d+)((\.\d+)?)$/; // 分开整数和小数
  return num.toString().replace(intFloatReg, (s, $1, $2) => {
    // '$&' 表示与 regexp 相匹配的子串。
    // $1.replace(thousandReg, '$&,')  整数部分添加千分位
    return $1.replace(thousandReg, '$&,') + $2;
  });
}
```
> 参考链接

[javascript为数字添加千分符](https://blog.csdn.net/xuyunfei_2012/article/details/54628981)

[正则基础之——贪婪与非贪婪模式](https://blog.csdn.net/lxcnn/article/details/4756030)

[在线正则测试工具](http://tool.oschina.net/regex/)
[js中replace的用法](https://www.cnblogs.com/skywang/articles/2051052.html)