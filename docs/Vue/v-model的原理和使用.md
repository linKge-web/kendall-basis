# v-mode原理和使用

`v-model`指令在表单`<input>`,`<textarea>`,`<select>`元素上创建双向绑定，它会根据控件类型自动选取正确的方法来更新元素。

`v-model`会忽略所有表单的的`value`、`checked`、`selected`、`attribute` 的初始值,需要在`data`中声明初始值。

## v-model 实现
```vue
  <div id="app">
    <input type="text" v-model="messages">
    <h2>{{messages}}</h2>
  </div>
  
  <script>
    const obj = {
      messages: 'hello vue',
    }
    const App = new Vue({
      el:'#app',
      data: obj,
    })
  </script>
```
会发现输入框中的信息与`messages`同步更新。

### 自己实现v-model
使用`v-bind`和`input`事件可实现`v-model`。
```vue
  <div id="app">
    <!-- 使用v-bind单向绑定并结合input事件 -->
    <input type="text" v-bind:value="messages" @input="changeValue">
    <h2>{{messages}}</h2>
  </div>
  
  <script>
    const obj = {
      messages: 'hello vue',
    }
    const App = new Vue({
      el:'#app',
      data: obj,
      methods: {
        // 一旦界面产生一个事件浏览器生成一个event对象
        // 在调用方法的时候如果不传参数，而方法本身带参数的情况下会自动吧event对象传递过来
        changeValue(event) {
           this.messages = event.target.value;
        }
      }
    })
  </script>
```
**Event对象补充：**

`event`对象代表事件的状态，比如事件在其中发生的元素、键盘按键的状态、鼠标的位置、鼠标按钮的状态。

**什么时候会产生Event 对象呢?** 
- 例如: 当用户单击某个元素的时候,我们给这个元素注册的事件就会触发,该事件的本质就是一个函数,而该函数的形参接收一个event对象.
- 事件通常与函数结合使用，函数不会在事件发生前被执行！
> 属性：    
> target	返回触发此事件的元素（事件的目标节点）。    
> type	返回当前 Event 对象表示的事件的名称。   
> [了解更多属性](https://www.w3school.com.cn/jsref/dom_obj_event.asp)

## v-model的原理
- v-model其实是一个语法糖，它背后的本质包含了两个操作：
  * 1.`v-bind绑定一个``value`属性
  * 2.`v-on指令给当前元素绑定``input`事件
  
也就是说下面的代码是等同的：
```js
<input type="text" v-model="messages">
等同于
<input type="text" v-bind:value="messages" @input="messages = $event.target.value">
```

## v-model结合radio使用
当存在多个单选框的时候

```vue
  <div id="app">
    <label for="male">
      <!-- <input type="radio" id="male" name="sex" value="男" v-model="sex">男 -->
      <!-- 当使用v-model绑定同一值时可以省略name -->
      <input type="radio" id="male" value="男" v-model="sex">男
    </label>
    <label for="female">
      <input type="radio" id="female" value="女" v-model="sex">女
    </label>
    <h3>你选择的性别是：{{sex}}</h3>
  </div>
  
  <script>
    const obj = {
      messages: 'hello vue',
      sex: ''
    }
    const App = new Vue({
      el:'#app',
      data: obj,
    })
  </script>
```
![](./img/v-model结合radio.png)

## v-mode 结合 checkbox

复选框分为两种：单个勾选框 和 多个勾选框

- 单个勾选框
  * `v-model` 即为布尔值
  * 此时`input`的`value`并不影响`v-model`的值

- 多个复选框：
  * 当是多个复选框时，因为可以选中多个，所以对应的data中属性是一个数组。
  * 当选中某一个时，就会将`input`的`value`添加到数组中。

```vue
<div id="app">
    <!-- 单选框 -->
    <label for="check">
      <input type="checkbox" id="check" v-model="checked">同意协议
    </label>
    <h3>是否选中：{{checked}}</h3>
    <label>
      <input type="checkbox" v-model="hobbies" value="篮球">篮球
    </label>
    <label>
      <input type="checkbox" v-model="hobbies" value="足球">足球
    </label>
    <label>
      <input type="checkbox" v-model="hobbies" value="台球">台球
    </label>
  </div>
  
  <script>
    const obj = {
      messages: 'hello vue',
      checked: false,
      hobbies: []
    }
    const App = new Vue({
      el:'#app',
      data: obj,
    })
  </script>
```
## v-mode 结合 select
和`checkbox`一样，`select`也分单选和多选两种情况。
- 单选：只能选中一个值。
  * `v-model`绑定的是一个值。
  * 当我们选中`option`中的一个时，会将它对应的`value`赋值到`mySelect`中
- 多选：可以选中多个值。
`v-model`绑定的是一个数组。
  * 当选中多个值时，就会将选中的`option`对应的`value`添加到数组`mySelects`中

```vue
  <div id="app">
    <!-- 只能选一个 -->
    <select v-model="mySelect">
      <option value="苹果">苹果</option>
      <option value="雪梨">雪梨</option>
      <option value="香蕉">香蕉</option>
    </select>
    <h3>你选择的是：{{mySelect}}</h3>
    <!-- 可以选多个 -->
    <select v-model="mySelects" multiple>
      <option value="苹果">苹果</option>
      <option value="雪梨">雪梨</option>
      <option value="香蕉">香蕉</option>
    </select>
    <h3>你选择的是：{{mySelects}}</h3>
  </div>
  
  <script>
    const obj = {
      messages: 'hello vue',
      mySelect: '苹果',
      mySelects: []
    }
    const App = new Vue({
      el:'#app',
      data: obj,
    })
  </script>
```
