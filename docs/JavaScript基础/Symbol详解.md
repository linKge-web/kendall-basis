## Symbol 概述
在MDN文档中，关于Symbol的说明是这样的：

Symbol 是一种特殊的、不可变的数据类型，可以作为对象属性的标识符使用。Symbol 对象是一个 symbol primitive data type 的隐式对象包装器。 

**简单的说：**
- Symbol用于**防止属性命名冲突**而产生的，比如向第三方对象中添加属性时。
- Symbol 的值是唯一的，独一无二的不会重复的

## Symbol 的应用场景
为了说明Symbol的作用，我们先来描述一个使用场景。我们在做一个游戏程序，用户需要选择角色的种族。
```js
var race = {
  protoss: 'protoss', // 神族
  terran: 'terran', // 人族
  zerg: 'zerg' // 虫族
}

function createRole(type){
  if(type === race.protoss){创建神族角色}
  else if(type === race.terran){创建人族角色}
  else if(type === race.zerg){创建虫族角色}
}
```
那么用户选择种族后，就需要调用 createRole 来创建角色：
```js
// 传入字符串
createRole('zerg') 
// 或者传入变量
createRole(race.zerg)
```
一般情况下不建议传入的字符串，更多的是使用`createRole(race.zerg)`。但是存在一个问题，如果使用 `createRole(race.zerg)`，那么`race.protoss`、`race.terran`、`race.zerg` 的值为多少并不重要。    
改成下面的写法对`createRole(race.zerg)`也不会造成影响
```js
var race = {
  protoss: 'askdjaslkfjas;lfkjas;flkj', // 神族
  terran: ';lkfalksjfl;askjfsfal;skfj', // 人族
  zerg: 'qwieqwoirqwoiruoiwqoisrqwroiu' // 虫族
}
```
**也就是说：**

`race.zerg` 的值是多少无所谓，只要它的值跟 `race.protoss` 和 `race.terran` 的值不一样就行。

`Symbol` 的用途就是如此：`Symbol` 可以创建一个独一无二的值（但并不是字符串）。

**用 Symbol 来改写上面的 race：**
```js
var race = {
  protoss: Symbol(), //神族
  terran: Symbol(),  //人族
  zerg: Symbol()   //虫族
}
console.log(race.protoss !== race.terran); //true
console.log(race.protoss !== race.zerg);   //true
```
你也可以给每个 Symbol 起一个名字：
```js
var race = {
  protoss: Symbol('protoss'), //神族
  terran: Symbol('terran'),  //人族
  zerg: Symbol('zerg')   //虫族
}
```
不过这个值与Symbol无关，可以理解为只是这个名字的注释：
```js
var name1 = Symbol('a');
var name2 = Symbol('a');
console.log(name1 == name2);  //flase
```

## Symbol 转换
**请看下面的四个例子，对应这四点：**

- Symbol 值可以显式转为字符串
- Symbol 值也可以转为布尔值
- Symbol 值**不能**转为数值，且不能做数值运算
- Symbol 值**不能**与其他类型的值进行运算
  
```js
// 转成字符串
let name = Symbol("kendall");
let nameStr1 = String(name); 
let nameStr2 = name.toString();

console.log(typeof name);     //symbol
console.log(typeof nameStr1); //string
console.log(typeof nameStr2); //string
```
```js
//转成boolean类型
let nameBool = Boolean(name);
console.log(typeof nameBool); //boolean
console.log(nameBool);        //true
```
```js
let num = Symbol(123);
Number(num);    //TypeError
num + 1;        //TypeError
'hello' + name; //TypeError
```

## symbol作为属性名

`Symbol` 是独一无二的所以可以保证对象属性的唯一。

`Symbol` 声明和访问使用 `[]`（变量）形式操作

也不能使用 `.` 语法因为 `.`语法是操作字符串属性的。

下面写法是错误的，会将`symbol` 当成字符串`symbol`处理
```js
let symbol = Symbol("kendall");
let obj = {
  symbol: "linkge-web.gitee.io"
}
console.log(obj);
//{ symbol: 'linkge-web.gitee.io' }
```
正确写法是以`[]`变量形式声明和访问
```js
let symbol = Symbol("kendall");
let obj = {
  [symbol]: "linkge-web.gitee.io"
}
console.log(obj[symbol]);  //linkge-web.gitee.io
```
## Symbol.for()
若想使用同一个Symbol值，就用`Symbol.for()`方法
```js
let ken = Symbol.for('kendall');
let linkge = Symbol.for('kendall');
console.log(ken === linkge); // true

let ken = Symbol.for();
let linkge = Symbol.for();
console.log(ken == linkge); //true

let ken = Symbol.for('a');
let kkk = Symbol.for('b')
console.log(ken == kkk);  //false
```
从上面的例子，请注意，当参数两个需要重用的值参数要不然都不写，要不然一定要一样，否则二者不会相等。

## Symbol.keyFor()
`Symbol.keyFor()`方法返回一个已登记的 `Symbol` 类型值的`key`
```js
// Symbol.for 有参数
let ken = Symbol.for('kendall');
console.log(Symbol.keyFor(ken)); //kendall

// Symbol.for 无参数
let ken = Symbol().for();;
console.log(Symbol.keyFor(ken));  //undefined

// 无 Symbol.for 表明未登记
let ken = Symbol();
console.log(Symbol.keyFor(ken)); // undefined
```

> 更多`Symbol`详解请参考：[Symbol](https://es6.ruanyifeng.com/#docs/symbol)


