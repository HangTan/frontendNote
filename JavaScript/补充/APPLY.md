#  1、call()、apply()、bind()基本用法与区别

JS 中的函数是对象，因此函数也有属性和方法。每个函数都有两个**非继承而来**的方法：apply() 和 call()。用途就是在特定的作用域中调用函数，也就是设置函数体内 this 对象的值。

## 1.1 apply()

apply() 方法接收两个参数：在其中运行函数的作用域，另外一个是参数**数组**。其中，第二个参数可以是 Array 的实例，也可以是 arguments 对象。

```javascript
function sum(num1, num2) {
  console.log(num1 + num2); 
}

function applyFunc(num1, num2) {
  return sum.apply(this, arguments);    // 传入 arguments 对象
}

function applyFunc2(num1, num2) {
  return sum.apply(this, [num1, num2]);  // 传入数组
}
callFunc(1, 2); // 打印 3 
callFunc2(1, 2); // 打印 3
```

## 1.2 call()

与 apply() 方法的作用一样，区别是接收参数的方式不同。第一个参数还是 this，其他参数必须**逐个列举**出来。

```javascript
function sum(num1, num2) {
  console.log(num1 + num2); 
}

function callFunc(num1, num2) {
  return sum.call(this, num1, num2);
}
callFunc(2, 5);
```

## 1.3 bind()

bind() 方法会创建一个函数的**实例**，它的 this 值会被绑定到传给 bind() 函数的值。

```javascript
window.color = 'red';
var o = { color: 'blue' };

function sayColor() {
  console.log(this.color);
}

var objectSayColor = sayColor.bind(o);
objectSayColor();   //  blue
// objectSayColor() 函数的 this 值等于 o。
```





# 2、常见用处

apply() 和 call() 真正的强大之处是能够**扩充函数赖以运行的作用域**。

```javascript
window.color = "red";
var o = { color: "blue" };
function sayColor() { 
    alert(this.color);
} 
sayColor();              // red
sayColor.call(this);     // red
sayColor.call(window);   // red
sayColor.call(o);        // blue
```

 使用 call() 和 apply() 来扩充作用域的最大好处，就是对象不需要与方法有任何耦合关系。

## 一些其他巧妙用法

（1）Math.max 实现得到数组中最大的一项：

Math.max 不支持数组参数，但它支持 Math.max(param1, param2,...)，使用 apply 可以解决这个问题。 

```javascript
var arr = [1, 5, 6, 12];
var max = Math.max.apply(null, arr);
console.log(max); // 12
```

（2） Array.prototype.push 可以实现两个数组的合并

push 方法不接受数组参数，但提供了 push(param1, param2....paramN)，同样使用 apply：

```
var arr1 = [1, 3, 5];
var arr2 = [2, 4, 6];
console.log(Array.prototype.push.apply(arr1, arr2));
console.log(arr1);
// 6
// [ 1, 3, 5, 2, 4, 6 ]
```



# 3、模拟实现

## 实现思路：

- 改变 this 指向：将目标函数作为这个对象的一个属性
- 利用 arguments 对象实现参数的不定长问题
- 不能增加对象的属性，所以最后要使用 delete 删除

```javascript
Function.prototype.call2 = function(context) {
  var context = context || window;
  context.fn = this;

  var args = Array.prototype.slice.apply(arguments, [1]);
  var result = context.fn(...args);
  delete context.fn;
  return result;
};

var value = 2;

var obj = {
  value: 1
}

function bar(name, age) {
  console.log(this.value);
  return {
      value: this.value,
      name: name,
      age: age
  }
}

bar.call2(null); // 2

console.log(bar.call2(obj, 'name', 18));
```













