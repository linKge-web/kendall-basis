## 1. JS中的变量和类型
### 1.1 基本数据类型

JS中有6中基本数据类型，分别是：
- boolean
- number
> number中有一个特殊的NaN，代表不都是一个有效数字，但是是属于number。
- string
- null
- undefined
- symbol

### 1.2 null是OBject吗？
虽然`typeof null`会输出object，但是这只是JS中存在的一个悠久的bug，在JS的最初版本中使用的是32位系统，为了性能考虑使用低位存储变量的类型信息，000开头代表对象，然而null表示全为零，所以将它错误的判断为object，虽然现在的内部类型判断已经改变了，但是对于这个Bug却是一直流传下来。

### 1.3 原始值和引用值的区别
> 原始值: null, undefined, string, number, boolean, symbol   
引用值：Object,Array,Function,RegExp,Date

- 存储位置的不同    
(1)原始类型存储的是值，引用类型存储的是地址。
原始类型的空间是固定，存储在较小的内存域——栈中，便于快速查找变量值。   
(2)引用类型存储在堆中，这个对象在堆中的地址存储在栈中。

- 赋值的方式不同    
(1)原始值赋值：拷贝值的副本给新的变量，两个变量相互独立，改变其中一个值不会影响另一个值   
(2)引用值赋值：将引用传递给新的变量，实际上就是将地址引用赋值给新的变量，两个变量指向同一个地址，也就是指向堆中的同一个区域，改变其中一个值，另一个值也会随着改变。

### 1.4 如何判断数据类型
判断数据类型的方式有：  
`typeof, instanceof, constructor, Object.prototype.toString.call()`    
#### 1.4.1 typeof
typeof对于原始类型来说，除了null会显示成object，其余的都可以正确的类型typeof，对于对象类型来说，只有函数都会显示object。所以typeof不能准确判断变量到底是什么类型。  

**那么null类型可以怎么才判断呢？**  
可以使用`===`来判断 
```js
var a = null;
console.log(a, typeof a , a===null);  //null "object" true
```
#### 1.4.2 instanceof
instanceof是用于判断【引用类型】属于哪个【构造函数】的方法，用来检测函数的prototype属性是否出现在某个实例的原型链上，但是注意**不能判断null和undefined**。

#### 1.4.3 construtor
返回一个指向创建对象的数组函数的引用（也就是地址），值得注意的是这个属性的值是那个函数本身，而不是一个包含函数名的字符串。  
用constructor判断变量类型有一个致命的缺陷，就是当检测null或者undefined类型的constructor属性时，JS会报错，所以在利用变量的constructor属性来判断变量类型时，必须要先保证变量不是null或者undefined。

#### 1.4.4 Object.prototype.toString.call()
以数组的形式返回一个字符串，数组的第二个参数就是变量类型。
```js
var a = null;
console.log( Object.prototype.toString.call(a) );  //[object Null]
var ary = [1,2,3];
console.log( Object.prototype.toString.call(ary) ); //[object Array]
var bol = true;
console.log( Object.prototype.toString.call(bol) );  //[object Boolean]
```

### 1.5 如何判断一个对象的数据类型是数组
- 根据构造函数来判断 `xxx instanceof Array`
- 根据calss属性来判断 `Object.prototype.toString.call(obj) ==="[object Array]"` 
- 直接用isArray判断 
```js
var ary = [1,2,3];
console.log(Array.isArray(ary));  //true
```

### 1.6 类数组与数组的区别与转换
类数组：通常把数值length属性和对应非数值属性的对象看成一种类型的数组，不能直接调用Array所具有的方法
常见的数组有：argument和DOM返回的元素筛选`（getElementsByTagName,getElementsByClassName）`  

**类数组转成数组的方法：**
```js
//类数组对象length属性，并且可以遍历，两则缺一不可
 var arr = {0:'a',a:'b',2:'c',length:3};
console.log(arr);
 
// 方法一：最原始的方法，借用一个空数组，遍历
var res = []
for(let i =0;i < values.length;i++){
    res.push(values[i])
}
 
// 方法二：用Array.prototype.slice.call(value);
 var res = Array.prototype.slice.call(value);
 
//  方法三：Array.from() (有length属性，是可遍历对象，缺一不可)
var res = Array.from(value)
 
// 方法四：Array.prototype.map.call()
var res = Array.prototype.map.call(value, val=> {
    return val;
});
 
// 方法五：扩展运算符[...value])(有iterator接口的对象才可以有)
var res1 = [...value];
console.log(res1);
```

### 1.7 数据类型的转换

#### 1.7.1 转Boolean
在条件判断时，除了 `undefined， null， false， NaN， ''， 0， -0`，其他所有值都转为 true，包括所有对象。

#### 1.7.2 对象转基本类型
对象在转换基本类型时，会调用valueOf， 需要转成字符类型时调用toString。  
```js
var a = {
  valueOf() {
    return 0;
  },
  toString() {
    return "1";
  },
};

1 + a; // 1   ==> 这里调用了valueof
"1".concat(a); //"11"   ==> 这里调用了tostring
```
> 函数的转换算法逻辑大致如下：  
> *  如果已经是原始类型了，那就不需要转换了
> * 如果需要转字符串类型就调用 x.toString()，转换为基础类型的话就返回转换的值。不是字符串类型的话就先调用 valueOf，结果不是基础类型的话再调用 toString
> * 调用 x.valueOf()，如果转换为基础类型，就返回转换的值
> * 如果都没有返回原始类型，就会报错


也可以重写 Symbol.toPrimitive ，该方法在转基本类型时调用优先级最高。 *Symbol.toPrimitive 指将被调用的指定函数值的属性转换为相对应的原始值*。
```js
let a = {
  valueOf() {
    return 0;
  },
  toString() {
    return '1';
  },
  [Symbol.toPrimitive]() {
    return 2;
  }
}
1 + a // => 3   ==> 这里调用的是Symbol.toPrimitive
'1' + a // => '12'  ==> 这里调用的是Symbol.toPrimitive
```
> **如果需要深入JS中数据类型的转换，可以 [点击这里-再深入理解一次JavaScript中数据类型的转换](https://juejin.im/post/6858806721588068359)**

### 1.8 四则运算
只有当加法运算时，其中一方是字符串类型，就会把另一个也转为字符串类型。  
其他运算只要其中一方是数字，那么另一方就转为数字。  
并且加法运算会触发三种类型转换：将值转换为原始值，转换为数字，转换为字符串。
```js
console.log('1' + 1);   // '11';
// 加法运算，有一个是字符串就会把另一个也转成字符串
//其他运算有一方数字，就会把另一个也转成数字
console.log(2 * '2');  // 4
console.log([1,2] + [2,1]);  //'1,22,1'   //转成字符串

console.log([1,2].toString()); //'1,2'
console.log(2 + [1,2]);  '21,2'  //所以将数组通过 toString 转为字符串'1,2'

console.log(true + true);  //2  将true转成数字
```
另外对于加法还需要注意这个表达式 'a' + + 'b'
```js
'a' + + 'b' // -> "aNaN"
```
***除了加法的其他运算有一方数字，就会把另一个也转成数字***

### 1.9 JS中 ”==“ 和 ”===“ 区别
- "==="不会进行类型转换，如果类型不同，直接返回false，也就是说"==="只有在两个运算类型一致且拥有相同的值的时候才会返回true。
- ”==“ 会在进行比较之前，将两个操作数转换成相同的类型
```js
console.log(''==0);  //true
console.log(''===0);  //false
console.log(0 == '0'); //true
console.log(0 === '0'); //false
```

 **对于”==“操作符来说，其判断流程大致是这样的**

- (1)首先会判断两者类型是否相同，相同的话就是比较大小了。
- (2)类型不相同的话，那么就会进行类型转换
- (3)会先判断是否在对比null和undefined，是的话就会返回true  
  *null没有valueof和toString，除了undefined谁跟它比较都是false*

- (4)判断两者类型是否为string和number，是的话就将string转换为number。
```js
1 == '1'
      ↓
1 ==  1 
===> true
```

- (5)判断其中一方是否为boolean，是的话就会把boolean转为number在进行判断。
```js
'1' == true
        ↓
'1' ==  1
 ↓
 1  ==  1 
===> true
```
 - (6)判断其中一方是否为object且另一方为string，number 或者 symbol，是的话就会把boolean转为与那时类型在进行判断。
 ```js
'1' == {a:'b'}
          ↓
'1' == '[object object]'
===> false
 ```
- (7)两边都是对象的话，那么只要不是同一对象的不同引用，都为 false。   

*注意：只要分出现NaN。就一定为false，因为就连NaN自己都不等于NaN，对于NaN的判断方法是使用全局函数isNaN。*

**“===”操作符**
不需要转型，直接判断类型和值是否相同，但是`NaN===NaN`还是false。








