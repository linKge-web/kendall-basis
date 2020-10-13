执行上下文是评估JavaScript代码的环境变量的抽象概念，每当JavaScript代码在执行的时候，它都是在执行上下文中运行。

## 执行上下文的类型
- **全局执行上下文**：这是默认的上下文，在浏览器中全局对象就是window对象，并且设置this执行这个全局对象。此外，一个程序中只有一个全局执行上下文。
- **函数执行上下文**：每当一个函数被**调用**的时候，就会为该函数创建新的执行上下文。每个函数都有自己的执行上下文，函数执行上下文可以有无数个。
- **Eval函数执行上下文**：执行在`eval`函数中的代码也会有自己的执行上下文。但是这个不经常使用。

## 执行栈
执行栈也叫调用栈，是一种 LIFO（后进先出）数据结构的栈。被用来存储代码在运行时的所有上下文。

首次调用`JavaScript`代码时，会创建一个全局执行上下文并压入到当前的执行栈中。每次发生函数调用，JavaScript引擎会为该函数创建一个新的执行上下文并压入到执行栈的栈顶。      
JS引擎会首先执行执行栈中位于栈顶的执行上下文，当函数执行结束时，该函数的执行上下文也会从栈中跳出，上下文控制权将移动到当前栈的下一个执行上下文。
```js
var a = 'Hello World!';

function first() {  
  console.log('Inside first function');  
  second();  
  console.log('Again inside first function');  
}

function second() {  
  console.log('Inside second function');  
}

first();  
console.log('Inside Global Execution Context');
```
结果：
```
Inside first function
Inside second function
Again inside first function
Inside Global Execution Context
```
![](./img/JS函数执行上下文01.jpg)

当上述代码在浏览器中加载时，JavaScript引擎会创建一个全局执行上下文，并把它压入当前的执行栈中。当遇到`first() `函数调用时，JavaScript引擎会创建一个新的函数执行上下文并压入当前执行栈的顶部。    
当从 `first()` 函数内部调用 `second()` 函数时，JavaScript引擎会创建一个新的执行上下文并压入当前执行栈的栈顶。当 `second()` 函数执行完毕，它的执行上下文会从当前栈弹出，并且控制流程到达下一个执行上下文，即 `first()` 函数的执行上下文。      
当 `first() `执行完毕，它的执行上下文从栈弹出，控制流程到达全局执行上下文。一旦所有代码执行完毕，JavaScript 引擎从当前栈中移除全局执行上下文。

## 执行上下文的创建

创建执行上下文分为两个阶段

- 创建阶段
- 执行阶段

### 创建阶段
在JavaScript代码执行之前，执行山下文将会经历创建阶段，创建阶段会进行的三件事：
- 1.确定this的值，也被称为this绑定(**This Binding**)
- 2.创建词法环境组件
- 3.创建变量环境组件

通过伪代码来理解
```js
ExecutionContext = {  
  ThisBinding = <this value>,     // 确定this 
  LexicalEnvironment = { ... },   // 词法环境
  VariableEnvironment = { ... },  // 变量环境
}
```
#### 1.This绑定
在全局执行上下文中，this的值指向全局对象（在浏览器中，this指向window对象；而在`nodejs`中指向这个文件的`module`对象。）

在函数执行上下文中，this取决于这个函数怎么被调用，如果它被一个引用对象调用，那么this将会被设置成这个对象，否则this会被设置成全局对象或者`undefined`（严格模式下）
```js
let foo = {
  Fun: function() {
    console.log("this is fun");
  }
}
foo.Fun();  //this是foo，因为被foo对象调用

let fun = foo.Fun;
fun();  // this是全局对象，因为没有指定引用对象
```

#### 2.词法环境

词法环境是一种持有**标识符和变量**映射的结构。（这里的标识符指的是变量/函数的名字，而变量是对应实际对象[包含函数类型对象]或原始数据的引用）。

**词法环境有两部分组成；**

- 环境记录：存储变量和函数声明的实际位置
    * 声明式环境记录器：存储变量、函数和参数
    * 对象环境记录器：用来定义出现在全局上下文中的变量和函数的关系
- 对外部环境的引用：可以访问外部其他词法环境

**词法环境的两种类型：**
- **全局环境**：是一个没有外部环境的词法环境，其外部环境引用为 `null`。拥有一个全局对象（window 对象）及其关联的方法和属性（例如数组方法）以及任何用户自定义的全局变量，`this` 的值指向这个全局对象。

- **函数环境**：用户在函数中定义的变量被存储在环境记录中，**声明式环境记录器**还包含了一个传递给函数的 `arguments` 对象（此对象存储索引和参数的映射）和传递给函数的参数的 `length`。对外部环境的引用可以是全局环境，也可以是包含内部函数的外部函数环境。

通过伪代码理解：
```js
GlobalExectionContext = {  // 全局执行上下文
  LexicalEnvironment: {    	  // 词法环境
    EnvironmentRecord: {   		// 环境记录
      Type: "Object",      		   // 全局环境
      // 标识符绑定在这里 
      outer: <null>  	   		   // 对外部环境的引用
  }  
}

FunctionExectionContext = { // 函数执行上下文
  LexicalEnvironment: {  	  // 词法环境
    EnvironmentRecord: {  		// 环境记录
      Type: "Declarative",  	   // 函数环境
      // 标识符绑定在这里 			  // 对外部环境的引用
      outer: <Global or outer function environment reference>  
  }  
}
```
#### 3.变量环境

变量环境也是一个词法环境，所以它有着上面定义的词法环境的所有属性。

在ES6中，**词法**环境和**变量**环境的区别在于：     
**词法环境**被用来存储 函数声明和变量（`let` 和 `const`）变量绑定。     
**变量环境**从来存储`var`变量绑定。

使用例子进行介绍：

```js
let a = 20;  
const b = 30;  
var c;

function multiply(e, f) {  
 var g = 20;  
 return e * f * g;  
}

c = multiply(20, 30);
```
执行上下文如下所示：
```js
GlobalExectionContext = {

  ThisBinding: <Global Object>,

  LexicalEnvironment: {  
    EnvironmentRecord: {  
      Type: "Object",  
      // 标识符绑定在这里  
      a: < uninitialized >,  
      b: < uninitialized >,  
      multiply: < func >  
    }  
    outer: <null>  
  },

  VariableEnvironment: {  
    EnvironmentRecord: {  
      Type: "Object",  
      // 标识符绑定在这里  
      c: undefined,  
    }  
    outer: <null>  
  }  
}

FunctionExectionContext = {  
   
  ThisBinding: <Global Object>,

  LexicalEnvironment: {  
    EnvironmentRecord: {  
      Type: "Declarative",  
      // 标识符绑定在这里  
      Arguments: {0: 20, 1: 30, length: 2},  
    },  
    outer: <GlobalLexicalEnvironment>  
  },

  VariableEnvironment: {  
    EnvironmentRecord: {  
      Type: "Declarative",  
      // 标识符绑定在这里  
      g: undefined  
    },  
    outer: <GlobalLexicalEnvironment>  
  }  
}

```
**变量提升**的原因：在创建阶段，函数声明存储在环境中，而变量会被设置为 `undefined`（在 `var` 的情况下）或保持未初始化（在 `let` 和 `const` 的情况下）。所以这就是为什么可以在声明之前访问 var 定义的变量（尽管是 `undefined` ），但如果在声明之前访问 let 和 const 定义的变量就会提示引用错误的原因。这就是所谓的变量提升。

### 执行阶段

这是整篇文章中最简单的部分。在此阶段，完成对所有这些变量的分配，最后执行代码。

> 如果 `Javascript` 引擎在源代码中声明的实际位置找不到 `let` 变量的值，那么将为其分配 `undefined` 值。