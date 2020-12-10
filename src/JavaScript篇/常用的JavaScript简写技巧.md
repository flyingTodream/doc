## 常用的JavaScript简写技巧

### 1.声明变量

```javascript
//Longhand 
let x; 
let y = 20; 
//Shorthand 
let x, y = 20;
```

### 2.给多个变量赋值 

我们可以使用数组解构来在一行中给多个变量赋值。

```javascript
//Longhand 
let a, b, c; 
a = 5; 
b = 8; 
c = 12;

//Shorthand 
let [a, b, c] = [5, 8, 12];
```

### 3. 三元运算符 

我们可以使用三元（条件）运算符在这里节省 5 行代码。

```javascript
//Longhand 
let marks = 26; 
let result; 
if(marks >= 30){
 result = 'Pass'; 
}else{ 
 result = 'Fail'; 
} 
//Shorthand 
let result = marks >= 30 ? 'Pass' : 'Fail';
```

### 4. 赋默认值 

我们可以使用 OR(||) 短路运算来给一个变量赋默认值，如果预期值不正确的情况下。

```javascript
//Longhand 
let imagePath; 
let path = getImagePath(); 
if(path !== null && path !== undefined && path !== '') { 
  imagePath = path; 
} else { 
  imagePath = 'default.jpg'; 
} 
//Shorthand 
let imagePath = getImagePath() || 'default.jpg';
```

### 5. 与 (&&) 短路运算 

如果你只有当某个变量为 true 时调用一个函数，那么你可以使用`与 (&&)`短路形式书写。

```javascript
//Longhand 
if (isLoggedin) {
 goToHomepage(); 
} 
//Shorthand 
isLoggedin && goToHomepage();
```

当你在 React 中想要有条件地渲染某个组件时，这个`与 (&&)`短路写法比较有用。例如：

```javascript
<div> { this.state.isLoading && <Loading /> } </div>
```

### 6. 交换两个变量 

为了交换两个变量，我们通常使用第三个变量。我们可以使用数组解构赋值来交换两个变量。

```javascript
let x = 'Hello', y = 55; 
//Longhand 
const temp = x; 
x = y; 
y = temp; 
//Shorthand 
[x, y] = [y, x];
```

### 7. 箭头函数

```javascript
//Longhand 
function add(num1, num2) { 
   return num1 + num2; 
} 
//Shorthand 
const add = (num1, num2) => num1 + num2;
```

参考:JavaScript Arrow function

https://jscurious.com/javascript-arrow-function/

### 8. 模板字符串

我们一般使用 + 运算符来连接字符串变量。使用 ES6 的模板字符串，我们可以用一种更简单的方法实现这一点。

```javascript
//Longhand 
console.log('You got a missed call from ' + number + ' at ' + time); 
//Shorthand 
console.log(`You got a missed call from ${number} at ${time}`);
```

### 9. 多行字符串 

对于多行字符串，我们一般使用 + 运算符以及一个新行转义字符（\n）。我们可以使用 (`) 以一种更简单的方式实现。

```javascript
//Longhand 
console.log('JavaScript, often abbreviated as JS, is a\n' + 'programming language that conforms to the \n' + 
'ECMAScript specification. JavaScript is high-level,\n' + 
'often just-in-time compiled, and multi-paradigm.' ); 
//Shorthand 
console.log(`JavaScript, often abbreviated as JS, is a programming language that conforms to the ECMAScript specification. JavaScript is high-level, often just-in-time compiled, and multi-paradigm.`);
```

### 10. 多条件检查 

对于多个值匹配，我们可以将所有的值放到数组中，然后使用`indexOf()`或`includes()`方法。

```javascript
//Longhand 
if (value === 1 || value === 'one' || value === 2 || value === 'two') { 
     // Execute some code 
} 
// Shorthand 1
if ([1, 'one', 2, 'two'].indexOf(value) >= 0) { 
    // Execute some code 
}
// Shorthand 2
if ([1, 'one', 2, 'two'].includes(value)) { 
    // Execute some code 
}
```

### 11. 对象属性复制 

如果变量名和对象的属性名相同，那么我们只需要在对象语句中声明变量名，而不是同时声明键和值。JavaScript 会自动将键作为变量的名，将值作为变量的值。

```javascript
let firstname = 'Amitav'; 
let lastname = 'Mishra'; 
//Longhand 
let obj = {firstname: firstname, lastname: lastname}; 
//Shorthand 
let obj = {firstname, lastname};
```

### 12. 字符串转成数字 

有一些内置的方法，例如`parseInt`和`parseFloat`可以用来将字符串转为数字。我们还可以简单地在字符串前提供一个一元运算符 (+) 来实现这一点。

```javascript
//Longhand 
let total = parseInt('453'); 
let average = parseFloat('42.6'); 
//Shorthand 
let total = +'453'; 
let average = +'42.6';
```

### 13. 重复一个字符串多次

为了重复一个字符串 N 次，你可以使用`for`循环。但是使用`repeat()`方法，我们可以一行代码就搞定。

```javascript
//Longhand 
let str = ''; 
for(let i = 0; i < 5; i ++) { 
  str += 'Hello '; 
} 
console.log(str); // Hello Hello Hello Hello Hello 
// Shorthand 
'Hello '.repeat(5);
```

**提示：** 想要给某人发 100 遍“sorry”来道歉吗？用 repeat() 方法试试吧。如果你想要每次在新的一行重复字符串，可以在字符串后面加一个 \n 。

```
'sorry\n'.repeat(100);
```



### 14. 指数幂

我们可以使用`Math.pow()`方法来得到一个数字的幂。有一种更短的语法来实现，即双星号 (**)。

```javascript
//Longhand 
const power = Math.pow(4, 3); // 64 
// Shorthand 
const power = 4**3; // 64
```

### 15. 双非位运算符 (~~) 

双非位运算符是`Math.floor()`方法的缩写。

```javascript
//Longhand 
const floor = Math.floor(6.8); // 6 
// Shorthand 
const floor = ~~6.8; // 6
```

> **来自 Caleb 的评论的改进：** 双非位运算符只对 32 位整数有效，例如 (2**31)-1 = 2147483647。所以对于任何大于 2147483647 的数字，双非位运算符 (~~) 都会给出错误的结果，这种情况下推荐使用 Math.floor() 方法。

### 16. 找出数组中的最大和最小数字

我们可以使用 for 循环来遍历数组中的每一个值，然后找出最大或最小值。我们还可以使用 Array.reduce() 方法来找出数组中的最大和最小数字。

但是使用扩展符号，我们一行就可以实现。

```javascript
// Shorthand 
const arr = [2, 8, 15, 4]; 
Math.max(...arr); // 15 
Math.min(...arr); // 2
```

### 17. For 循环 

为了遍历一个数组，我们一般使用传统的`for`循环。我们可以使用`for...of`来遍历数组。为了获取每个值的索引，我们可以使用`for...in`循环。

```javascript
let arr = [10, 20, 30, 40]; 
//Longhand 
for (let i = 0; i < arr.length; i++) { 
  console.log(arr[i]); 
} 
//Shorthand 
//for of loop 
for (const val of arr) { 
  console.log(val); 
} 
//for in loop 
for (const index in arr) { 
  console.log(arr[index]); 
}
```

我们还可以使用`for...in`循环来遍历对象属性。

```javascript
let obj = {x: 20, y: 50}; 
for (const key in obj) { 
  console.log(obj[key]); 
}
```

**参考：**JavaScript 中遍历对象和数组的不同方法

https://jscurious.com/different-ways-to-iterate-through-objects-and-arrays-in-javascript/

### 18. 合并数组

```javascript
let arr1 = [20, 30]; 
//Longhand 
let arr2 = arr1.concat([60, 80]); 
// [20, 30, 60, 80] 
//Shorthand 
let arr2 = [...arr1, 60, 80]; 
// [20, 30, 60, 80]
```

### 19. 深拷贝多级对象 

为了深拷贝一个多级对象，我们要遍历每一个属性并检查当前属性是否包含一个对象。如果当前属性包含一个对象，然后要将当前属性值作为参数递归调用相同的方法（例如，嵌套的对象）。

我们可以使用`JSON.stringify()`和`JSON.parse()`，如果我们的对象不包含函数、undefined、NaN 或日期值的话。

如果有一个单级对象，例如没有嵌套的对象，那么我们也可以使用扩展符来实现深拷贝。

```javascript
let obj = {x: 20, y: {z: 30}}; 
//Longhand 
const makeDeepClone = (obj) => { 
  let newObject = {}; 
  Object.keys(obj).map(key => { 
    if(typeof obj[key] === 'object'){ 
      newObject[key] = makeDeepClone(obj[key]); 
    } else { 
      newObject[key] = obj[key]; 
    } 
  }); 
 return newObject; 
} 
const cloneObj = makeDeepClone(obj); 
//Shorthand 
const cloneObj = JSON.parse(JSON.stringify(obj));
//Shorthand for single level object
let obj = {x: 20, y: 'hello'};
const cloneObj = {...obj};
```

> **来自评论的改进**：如果你的对象包含 function, undefined or NaN 值的话，JSON.parse(JSON.stringify(obj)) 就不会有效。因为当你 JSON.stringify 对象的时候，包含 function, undefined or NaN 值的属性会从对象中移除。因此，当你的对象只包含字符串和数字值时，可以使用`JSON.parse(JSON.stringify(obj))`。

参考：JSON.parse() 和 JSON.stringify()

https://jscurious.com/difference-between-json-parse-and-json-stringify/

### 20. 获取字符串中的字符

```javascript
let str = 'jscurious.com'; 
//Longhand 
str.charAt(2); // c 
//Shorthand 
str[2]; // c
```

