## 一、JavaScript 基础

### 1. 手写 Object.create

> 思路：将传入的对象作为原型
>
> ```javascript
> function create(obj) {
>   if(typeof obj !== 'object')  
>     throw new TypeError("proto必须为对象或者null");
>   function F() {}  //创建一个构造函数
>   F.prototype = obj
>   return new F()  //返回构造的实例
> }
> //返回一个构造函数
> //当创建构造函数的一个实例后，就将这个实例的原型赋值为构造函数的prototype属性
> ```

> ```js
> let animal = {
>   eats: true
> };
> // Object.create的使用
> // 创建一个以 animal 为原型的新对象
> let rabbit = Object.create(animal);
> ```

### 2. 手写 instanceof 方法

> instanceof 运算符用于判断构造函数的 prototype 属性是否出现在对象的原型链中的任何位置。
>
> `instanceof`**只能正确判断引用数据类型**，而不能判断基本数据类型。`instanceof` 运算符可以用来测试一个对象在其原型链中是否存在一个构造函数的 `prototype` 属性。
>
> ```js
> console.log('str' instanceof String);  // false 
> console.log([] instanceof Array); //true
> ```
>
> 实现步骤：
>
> 1. 首先获取类型的原型
> 2. 然后获得对象的原型
> 3. 然后一直循环判断对象的原型是否等于类型的原型，直到对象原型为 `null`，因为原型链最终为 `null`
>
> 具体实现：
>
> ```javascript
> function myInstanceof(left, right) {
>   let proto = Object.getPrototypeOf(left), // 获取对象的原型
>       prototype = right.prototype; // 获取构造函数的 prototype 对象
> 
>   // 判断构造函数的 prototype 对象是否在对象的原型链上
>   while (true) {
>     if (!proto) return false;
>     if (proto === prototype) return true;
> 
>     proto = Object.getPrototypeOf(proto);
>   }
> }
> ```

### 3. 手写 new 操作符

> ```js
> function User(name) {
>   // this = {};（隐式创建）
>   this.name = name;
>   this.isAdmin = false;
>   // return this;（隐式返回)
> }
> 
> let user = new User("Jack");
> alert(user.name); // Jack
> ```
>
> 当一个函数被使用 `new` 操作符执行时，它按照以下步骤：
>
> 1. 一个新的空对象被创建并分配给 `this`。
> 2. 函数体执行。通常它会修改 `this`，为其添加新的属性。
> 3. 返回 `this` 的值。

> 在调用 `new` 的过程中会发生以上四件事情：
>
> （1）首先创建了一个新的空对象
>
> （2）设置原型，将对象的原型设置为函数的 prototype 对象。
>
> （3）让函数的 this 指向这个对象，执行构造函数的代码（为这个新对象添加属性）
>
> （4）如果无返回值或者返回一个非对象值，则将 obj 返回作为新对象；如果返回值是一个新对象的话那么直接直接返回该对象。
>
> ```javascript
> function myNew(fn, ...args) {
>     
>   // 新建一个空对象，对象的原型为构造函数的 prototype 对象  
>   let obj = Object.create(fn.prototype);
>   // 将 this 指向新建对象，并执行函数
>   let res = fn.call(obj, ...args);
>   // 将 this 指向新建对象，并执行函数
>   if (res && (typeof res === "object" || typeof res === "function")) {
>     return res;
>   }
>   return obj;
> }
> 用法如下：
> // // function Person(name, age) {
> // //   this.name = name;
> // //   this.age = age;
> // // }
> // // Person.prototype.say = function() {
> // //   console.log(this.age);
> // // };
> // // let p1 = myNew(Person, "lihua", 18);
> // // console.log(p1.name);
> // // console.log(p1);
> // // p1.say();
> ```

### 4.手写Promise

```js
class MyPromise {
  constructor(executor) {
    this.status = 'pending' // 初始状态为等待
    this.value = null // 成功的值
    this.reason = null // 失败的原因
    this.onFulfilledCallbacks = [] // 成功的回调函数存放的数组
    this.onRejectedCallbacks = [] // 失败的回调函数存放的数组
    let resolve = value => {
      if (this.status === 'pending') {
        this.status = 'fulfilled'
        this.value = value;
        this.onFulfilledCallbacks.forEach(fn => fn()) // 调用成功的回调函数
      }
    }
    let reject = reason => {
      if (this.status === 'pending') {
        this.status = 'rejected'
        this.reason = reason
        this.onRejectedCallbacks.forEach(fn => fn()) // 调用失败的回调函数
      }
    };
    try {
      executor(resolve, reject)
    } catch (err) {
      reject(err)
    }
  }
    
  // 实例方法 then
  then(onFulfilled, onRejected) {
    // 判断回调是否是函数
    // onFulfilled如果不是函数，则修改为函数，直接返回value
    onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : value => value
    // onRejected如果不是函数，则修改为函数，直接抛出错误
    onRejected = typeof onRejected === 'function' ? onRejected : err => { throw err }
    
    // 为了保持链式调用  继续返回promise
    return new MyPromise((resolve, reject) => {

     	// 将回调注册到onFulfilledCallbacks事件集合里面去
        this.onFulfilledCallbacks.push(() => { // 将成功的回调函数放入成功数组
         try {
            let x = onFulfilled(this.value)
             // 如果回调函数结果是普通值 那么就resolve出去给下一个then链式调用  如果是一个promise对象（代表又是一个异步） 那么调用x的then方法 将resolve和reject传进去 等到x内部的异步 执行完毕的时候（状态完成）就会自动执行传入的resolve 这样就控制了链式调用的顺序
            x instanceof MyPromise ? x.then(resolve, reject) : resolve(x)
          }catch(error{
            reject(error);
          }
        })//push
        
        this.onRejectedCallbacks.push(() => { // 将失败的回调函数放入失败数组
          try{
            let x = onRejected(this.reason)
            x instanceof MyPromise ? x.then(resolve, reject) : resolve(x)
          }catch(error{
            reject(error);
          }
        })//push
      
    })//
  }
}
// 测试
function p1() {
  return new MyPromise((resolve, reject) => {
    setTimeout(resolve, 1000, 1)
  })
}
function p2() {
  return new MyPromise((resolve, reject) => {
    setTimeout(resolve, 1000, 2)
  })
}
p1().then(res => {
  console.log(res) // 1
  return p2()
}).then(ret => {
  console.log(ret) // 2
})
```

> ```js
>   //静态方法
>   static all(promiseArr) {
>     let result = [];
>     //声明一个计数器 每一个promise返回就加一
>     let count = 0;
>     return new Mypromise((resolve, reject) => {
>       for (let i = 0; i < promiseArr.length; i++) {
>       //这里用 Promise.resolve包装一下 防止不是Promise类型传进来
>         Promise.resolve(promiseArr[i]).then(
>           (res) => {
>             //这里不能直接push数组  因为要控制顺序一一对应(感谢评论区指正)
>             result[i] = res;
>             count++;
>             //只有全部的promise执行成功之后才resolve出去
>             if (count === promiseArr.length) {
>               resolve(result);
>             }
>           },
>           (err) => {
>             reject(err);
>           }
>         );
>       }
>     });
>   }
>   //静态方法
>   static race(promiseArr) {
>     return new Mypromise((resolve, reject) => {
>       for (let i = 0; i < promiseArr.length; i++) {
>         Promise.resolve(promiseArr[i]).then(
>           (res) => {
>             //promise数组只要有任何一个promise 状态变更  就可以返回
>             resolve(res);
>           },
>           (err) => {
>             reject(err);
>           }
>         );
>       }
>     });
>   }
> }
> ```

### 5.防抖

```js
// 函数防抖： 在事件被触发 n 秒后再执行回调，如果在这 n 秒内事件又被触发，则重新计时。
// 这可以使用在一些点击请求的事件上，避免因为用户的多次点击向后端发送多次请求。
//个人理解 函数防抖就是法师发技能的时候要读条，技能读条没完再按技能就会重新读条
//防抖步骤
/*
声明定时器
返回函数
一定时间间隔，执行回调函数
回调函数
已执行：清空定时器
未执行：重置定时器
*/
//这里fn是要执行的函数
/*应用
1.search搜索联想，用户在不断输入值时，用防抖来节约请求资源。
2.window触发resize的时候，不断的调整浏览器窗口大小会不断的触发这个事件，用防抖来让其只触发一次
*/
function debounce(fn, delay) {
  let timer
  return function (...args) {
    if (timer) {
      clearTimeout(timer)
        timer=null
    }
    timer = setTimeout(() => {
      fn.apply(this, args)
    }, delay)
  }
}

// 测试
function task() {
  console.log('run task')
}
const debounceTask = debounce(task, 1000)
window.addEventListener('scroll', debounceTask)


// 函数防抖的实现
function debounce(fn, wait) {
  var timer = null;

  return function() {
    var context = this,
      args = arguments;

    // 如果此时存在定时器的话，则取消之前的定时器重新记时
    if (timer) {
      clearTimeout(timer);
      timer = null;
    }

    // 设置定时器，使事件间隔指定事件后执行
    timer = setTimeout(() => {
      fn.apply(context, args);
    }, wait);
  };
}
```

### 6.节流

```js
// 函数节流：规定一个单位时间，在这个单位时间内，只能有一次触发事件的回调函数执行，如果在同一个单位时间内某事件被触发多次，只有一次能生效。
//节流可以使用在 scroll 函数的事件监听上，通过事件节流来降低事件调用的频率。
//个人理解 函数节流就是fps游戏的射速，就算一直按着鼠标射击，也只会在规定射速内射出子弹。
/*应用
1.鼠标不断点击触发，mousedown(单位时间内只触发一次)
2.监听滚动事件，比如是否滑到底部自动加载更多，用throttle来判断
*/
function throttle(fn, delay) {
  let last = Date.now();// 上次触发时间
  return (...args) => {
    const now = Date.now()
    if (now - last >= delay) {
      last = Date.now();
      fn.apply(this, args)
    }
  }
}

// 测试
function task() {
  console.log('run task')
}
const throttleTask = throttle(task, 1000)
window.addEventListener('scroll', throttleTask)

// 函数节流的实现;
function throttle(fn, delay) {
  var preTime = Date.now();

  return function() {
    var context = this,
      args = arguments,
      nowTime = Date.now();

    // 如果两次时间间隔超过了指定时间，则执行函数。
    if (nowTime - preTime >= delay) {
      preTime = Date.now();
      return fn.apply(context, args);
    }
  };
}
```

### 7.手写类型判断函数

> ```js
> function getType(value) {
>   // 判断数据是 null 的情况
>   if (value === null) {
>     return value + "";
>   }
>   // 判断数据是引用类型的情况
>   if (typeof value === "object") {
>     let valueClass = Object.prototype.toString.call(value),
>     type = valueClass.split(" ")[1].split("");
>     type.pop();
>     return type.join("").toLowerCase();
>   } else {
>     // 判断数据是基本数据类型的情况和函数的情况
>     return typeof value;
>   }
> }
> ```

### 8.call apply bind

> > **（1）call 函数的实现步骤：**
> >
> > - 判断调用对象是否为函数，即使是定义在函数的原型上的，但是可能出现使用 call 等方式调用的情况。
> > - 判断传入上下文对象是否存在，如果不存在，则设置为 window 。
> > - 处理传入的参数，截取第一个参数后的所有参数。
> > - 将函数作为上下文对象的一个属性。
> > - 使用上下文对象来调用这个方法，并保存返回结果。
> > - 删除刚才新增的属性。
> > - 返回结果。
> >
> > ```javascript
> > Function.prototype.myCall = function(context) {
> >   // 判断调用对象
> >   if (typeof this !== "function") {
> >     console.error("type error");
> >   }
> >   // 获取参数
> >   let args = [...arguments].slice(1),
> >     result = null;
> >   // 判断 context 是否传入，如果未传入则设置为 window
> >   context = context || window;
> >   // 将调用函数设为对象的方法
> >   context.fn = this;
> >   // 调用函数
> >   result = context.fn(...args);
> >   // 将属性删除
> >   delete context.fn;
> >   return result;
> > };
> > 
> > Function.prototype.myCall=function(context,...args){
> >   // 判断调用对象
> >   if (typeof this !== "function") {
> >     console.error("type error");
> >   }
> >   context=context||window;
> >   context.fn=this
> >   let res=context.fn(...args)
> >   delete context.fn
> >   return res
> > }
> > ```
> >
> > **（2）apply 函数的实现步骤：**
> >
> > - 判断调用对象是否为函数，即使是定义在函数的原型上的，但是可能出现使用 call 等方式调用的情况。
> > - 判断传入上下文对象是否存在，如果不存在，则设置为 window 。
> > - 将函数作为上下文对象的一个属性。
> > - 判断参数值是否传入
> > - 使用上下文对象来调用这个方法，并保存返回结果。
> > - 删除刚才新增的属性
> > - 返回结果
> >
> > ```javascript
> > Function.prototype.myApply = function(context) {
> >   // 判断调用对象是否为函数
> >   if (typeof this !== "function") {
> >     throw new TypeError("Error");
> >   }
> >   let result = null;
> >   // 判断 context 是否存在，如果未传入则为 window
> >   context = context || window;
> >   // 将函数设为对象的方法
> >   context.fn = this;
> >   // 调用方法
> >   if (arguments[1]) {
> >     result = context.fn(...arguments[1]);
> >   } else {
> >     result = context.fn();
> >   }
> >   // 将属性删除
> >   delete context.fn;
> >   return result;
> > };
> > 
> > Function.prototype.myApply=function(context,arr=[]){
> >    if(typeof this !=='function') {
> >        throw new Error('Error')
> >    }
> >    context=context||window;
> >    context.fn=this
> >    let res=context.fn(...arr)
> >    delete context.fn
> >    return res
> > }
> > ```
> >
> > **（3）bind 函数的实现步骤：**
> >
> > - 判断调用对象是否为函数，即使是定义在函数的原型上的，但是可能出现使用 call 等方式调用的情况。
> > - 保存当前函数的引用，获取其余传入参数值。
> > - 创建一个函数返回
> > - 函数内部使用 apply 来绑定函数调用，需要判断函数作为构造函数的情况，这个时候需要传入当前函数的 this 给 apply 调用，其余情况都传入指定的上下文对象。
> >
> > ```javascript
> > Function.prototype.myBind = function(context) {
> >   // 判断调用对象是否为函数
> >   if (typeof this !== "function") {
> >     throw new TypeError("Error");
> >   }
> >   // 获取参数
> >   var args = [...arguments].slice(1),
> >     fn = this;
> >   return function Fn() {
> >     // 根据调用方式，传入不同绑定值
> >     return fn.apply(
> >       this instanceof Fn ? this : context,
> >       args.concat(...arguments)
> >     );
> >   };
> > };
> > ```

### 9.函数柯里化

> 函数柯里化指的是一种将使用多个参数的一个函数转换成一系列使用一个参数的函数的技术。
>
> 柯里化（Currying），又称部分求值（Partial Evaluation），是把接受多个参数的函数变换成接受一个单一参数（最初函数的第一个参数）的函数，并且返回接受余下的参数而且返回结果的新函数的技术。核心思想是把多参数传入的函数拆成单参数（或部分）函数，内部再返回调用下一个单参数（或部分）函数，依次处理剩余的参数。

> ```js
> function curry(fn, ...args) {
>   // 获取函数需要的参数长度
>   let length = fn.length;
>   let args = [...args] || [];
>   return function() {
>     let subArgs = args.slice(0);
> 
>     // 拼接得到现有的所有参数
>     for (let i = 0; i < arguments.length; i++) {
>       subArgs.push(arguments[i]);
>     }
> 
>     // 判断参数的长度是否已经满足函数所需参数的长度
>     if (subArgs.length >= length) {
>       // 如果满足，执行函数
>       return fn.apply(this, subArgs);
>     } else {
>       // 如果不满足，递归返回科里化的函数，等待参数的传入
>       return curry.call(this, fn, subArgs);
>     }
>   };
> }
> 
> // es6 实现
> function curry(fn, ...args) {
>   return fn.length <= args.length ? fn(...args) : curry.bind(null, fn, ...args);
> }
> 
> ```

### 10. 实现AJAX请求

> AJAX是 Asynchronous JavaScript and XML 的缩写，指的是通过 JavaScript 的 异步通信，从服务器获取 XML 文档从中提取数据，再更新当前网页的对应部分，而不用刷新整个网页。
>
> 创建AJAX请求的步骤：
>
> - **创建一个 XMLHttpRequest 对象。**
> - 在这个对象上**使用 open 方法创建一个 HTTP 请求**，open 方法所需要的参数是请求的方法、请求的地址、是否异步和用户的认证信息。
> - 在发起请求前，可以为这个对象**添加一些信息和监听函数**。比如说可以通过 setRequestHeader 方法来为请求添加头信息。还可以为这个对象添加一个状态监听函数。一个 XMLHttpRequest 对象一共有 5 个状态，当它的状态变化时会触发onreadystatechange 事件，可以通过设置监听函数，来处理请求成功后的结果。当对象的 readyState 变为 4 的时候，代表服务器返回的数据接收完成，这个时候可以通过判断请求的状态，如果状态是 2xx 或者 304 的话则代表返回正常。这个时候就可以通过 response 中的数据来对页面进行更新了。
> - 当对象的属性和监听函数设置完成后，最后调**用 sent 方法来向服务器发起请求**，可以传入参数作为发送的数据体。
>
> ```javascript
> const SERVER_URL = "/server";
> let xhr = new XMLHttpRequest();
> // 创建 Http 请求
> xhr.open("GET", SERVER_URL, true);
> // 设置状态监听函数
> xhr.onreadystatechange = function() {
>   if (this.readyState !== 4) return;
>   // 当请求成功时
>   if (this.status === 200) {
>     handle(this.response);
>   } else {
>     console.error(this.statusText);
>   }
> };
> // 设置请求失败时的监听函数
> xhr.onerror = function() {
>   console.error(this.statusText);
> };
> // 设置请求头信息
> xhr.responseType = "json";
> xhr.setRequestHeader("Accept", "application/json");
> // 发送 Http 请求
> xhr.send(null);
> ```
>
> 使用Promise封装AJAX请求
>
> ```js
> // promise 封装实现：
> function getJSON(url) {
>   // 创建一个 promise 对象
>   let promise = new Promise(function(resolve, reject) {
>     let xhr = new XMLHttpRequest();
>     // 新建一个 http 请求
>     xhr.open("GET", url, true);
>     // 设置状态的监听函数
>     xhr.onreadystatechange = function() {
>       if (this.readyState !== 4) return;
>       // 当请求成功或失败时，改变 promise 的状态
>       if (this.status === 200) {
>         resolve(this.response);
>       } else {
>         reject(new Error(this.statusText));
>       }
>     };
>     // 设置错误监听函数
>     xhr.onerror = function() {
>       reject(new Error(this.statusText));
>     };
>     // 设置响应的数据类型
>     xhr.responseType = "json";
>     // 设置请求头信息
>     xhr.setRequestHeader("Accept", "application/json");
>     // 发送 http 请求
>     xhr.send(null);
>   });
>   return promise;
> }
> ```

### 11.深拷贝

**浅拷贝实现**

> 浅拷贝是指，一个新的对象对原始对象的属性值进行精确地拷贝，如果拷贝的是基本数据类型，拷贝的就是基本数据类型的值，如果是引用数据类型，拷贝的就是内存地址。如果其中一个对象的引用内存地址发生改变，另一个对象也会发生变化。
>
> #### （1）Object.assign()
>
> `Object.assign()`是ES6中对象的拷贝方法，接受的第一个参数是目标对象，其余参数是源对象，用法：`Object.assign(target, source_1, ···)`，该方法可以实现浅拷贝，也可以实现一维对象的深拷贝。
>
> **注意：**
>
> - 如果目标对象和源对象有同名属性，或者多个源对象有同名属性，则后面的属性会覆盖前面的属性。
> - 如果该函数只有一个参数，当参数为对象时，直接返回该对象；当参数不是对象时，会先将参数转为对象然后返回。
> - 因为`null` 和 `undefined` 不能转化为对象，所以第一个参数不能为`null`或 `undefined`，会报错。
>
> ```javascript
> let target = {a: 1};
> let object2 = {b: 2};
> let object3 = {c: 3};
> Object.assign(target,object2,object3);  
> console.log(target);  // {a: 1, b: 2, c: 3}
> ```
>
> #### （2）扩展运算符
>
> 使用扩展运算符可以在构造字面量对象的时候，进行属性的拷贝。语法：`let cloneObj = { ...obj };`
>
> ```javascript
> let obj1 = {a:1,b:{c:1}}
> let obj2 = {...obj1};
> obj1.a = 2;
> console.log(obj1); //{a:2,b:{c:1}}
> console.log(obj2); //{a:1,b:{c:1}}
> obj1.b.c = 2;
> console.log(obj1); //{a:2,b:{c:2}}
> console.log(obj2); //{a:1,b:{c:2}}
> ```
>
> #### （3）数组方法实现数组浅拷贝
>
> ###### **1）Array.prototype.slice**
>
> - `slice()`方法是JavaScript数组的一个方法，这个方法可以从已有数组中返回选定的元素：用法：`array.slice(start, end)`，该方法不会改变原始数组。
> - 该方法有两个参数，两个参数都可选，如果两个参数都不写，就可以实现一个数组的浅拷贝。
>
> ```javascript
> let arr = [1,2,3,4];
> console.log(arr.slice()); // [1,2,3,4]
> console.log(arr.slice() === arr); //false
> ```
>
> ###### **2）Array.prototype.concat**
>
> - `concat()` 方法用于合并两个或多个数组。此方法不会更改现有数组，而是返回一个新数组。
> - 该方法有两个参数，两个参数都可选，如果两个参数都不写，就可以实现一个数组的浅拷贝。
>
> ```javascript
> let arr = [1,2,3,4];
> console.log(arr.concat()); // [1,2,3,4]
> console.log(arr.concat() === arr); //false
> ```
>
> #### （4）手写实现浅拷贝
>
> ```js
> // 浅拷贝的实现;
> function shallowCopy(object) {
>   // 只拷贝对象
>   if (!object || typeof object !== "object") return;
> 
>   // 根据 object 的类型判断是新建一个数组还是对象
>   let newObject = Array.isArray(object) ? [] : {};
> 
>   // 遍历 object，并且判断是 object 的属性才拷贝
>   for (let key in object) {
>     if (object.hasOwnProperty(key)) {
>       newObject[key] = object[key];
>     }
>   }
>   return newObject;
> }
> 
> function shallowClone(obj) {
>   let cloneObj = {};
>   
>   for (let i in obj) {
>     cloneObj[i] = obj[i];
>   }
>   
>   return cloneObj;
> }
> ```

**深拷贝**

> **浅拷贝：** 浅拷贝指的是将一个对象的属性值复制到另一个对象，如果有的属性的值为引用类型的话，那么会将这个引用的地址复制给对象，因此两个对象会有同一个引用类型的引用。浅拷贝可以使用  Object.assign 和展开运算符来实现。
>
> **深拷贝：** 深拷贝相对浅拷贝而言，如果遇到属性值为引用类型的时候，它新建一个引用类型并将对应的值复制给它，因此对象获得的一个新的引用类型而不是一个原有类型的引用。深拷贝对于一些对象可以使用 JSON 的两个函数来实现，但是由于 JSON 的对象格式比 js 的对象格式更加严格，所以如果属性值里边出现函数或者 Symbol 类型的值时，会转换失败

> #### （1）JSON.stringify()
>
> - `JSON.parse(JSON.stringify(obj))`是目前比较常用的深拷贝方法之一，它的原理就是利用`JSON.stringify` 将`js`对象序列化（JSON字符串），再使用`JSON.parse`来反序列化(还原)`js`对象。
> - 这个方法可以简单粗暴的实现深拷贝，但是还存在问题，拷贝的对象中如果有函数，undefined，symbol，当使用过`JSON.stringify()`进行处理之后，都会消失。
>
> ```javascript
> let obj1 = {  a: 0,
>               b: {
>                  c: 0
>                  }
>             };
> let obj2 = JSON.parse(JSON.stringify(obj1));
> obj1.a = 1;
> obj1.b.c = 1;
> console.log(obj1); // {a: 1, b: {c: 1}}
> console.log(obj2); // {a: 0, b: {c: 0}}
> ```
>
> #### （2）函数库lodash的_.cloneDeep方法
>
> 该函数库也有提供_.cloneDeep用来做 Deep Copy
>
> ```javascript
> var _ = require('lodash');
> var obj1 = {
>     a: 1,
>     b: { f: { g: 1 } },
>     c: [1, 2, 3]
> };
> var obj2 = _.cloneDeep(obj1);
> console.log(obj1.b.f === obj2.b.f);// false
> ```
>
> #### （3）手写实现
>
> ```js
> // 深拷贝的实现
> function deepCopy(object) {
>   if (!object || typeof object !== "object") return;
> 
>   let newObject = Array.isArray(object) ? [] : {};
> 
>   for (let key in object) {
>     if (object.hasOwnProperty(key)) {
>       newObject[key] =
>         typeof object[key] === "object" ? deepCopy(object[key]) : object[key];
>     }
>   }
> 
>   return newObject;
> }
> ```
>
> ```js
> //深拷贝，循环引用问题
> //使用Map函数
> //使用Map的key可以是对象的特性。把要拷贝的目标对象，当做Key存起来，value是深拷贝后的对象（在Map中有这样一个键值对）。
> function deepCopy(obj,map = new Map()){
>     if (typeof obj != 'object') return;
>     var newObj = Array.isArray(obj)?[]:{}
>     if(map.get(obj)){ 
>       return map.get(obj); 
>     } 
>     map.set(obj,newObj);
>     for(var key in obj){
>         if (obj.hasOwnProperty(key)) {
>             if (typeof obj[key] == 'object') {
>                 newObj[key] = deepCopy(obj[key],map);
>             } else {
>                 newObj[key] = obj[key];
>             }
>         }
>     }
>     return newObj;
> }
> ```

### 12.异步控制并发数

```js
function limitRequest(urls = [], limit = 3) {
  return new Promise((resolve, reject) => {
    const len = urls.length
    let count = 0

    // 同时启动limit个任务
    while (limit > 0) {
      start()
      limit -= 1
    }

    function start() {
      const url = urls.shift() // 从数组中拿取第一个任务
      if (url) {
        axios.post(url).then(res => {
          // todo
        }).catch(err => {
          // todo
        }).finally(() => {
          if (count == len - 1) {
            // 最后一个任务完成
            resolve()
          } else {
            // 完成之后，启动下一个任务
            count++
            start()
          }
        })
      }
    }

  })
}

// 测试
limitRequest(['http://xxa', 'http://xxb', 'http://xxc', 'http://xxd', 'http://xxe'])
```

### 13.继承

**ES5继承（寄生组合继承）**

```js
function Parent(name) {
  this.name = name
}
Parent.prototype.eat = function () {
  console.log(this.name + ' is eating')
}

function Child(name, age) {
  Parent.call(this, name)
  this.age = age
}
Child.prototype = Object.create(Parent.prototype)
Child.prototype.contructor = Child

// 测试
let xm = new Child('xiaoming', 12) 
console.log(xm.name) // xiaoming
console.log(xm.age) // 12
xm.eat() // xiaoming is eating
```

**ES6继承**

```js
class Parent {
  constructor(name) {
    this.name = name
  }
  eat() {
    console.log(this.name + ' is eating')
  }
}

class Child extends Parent {
  constructor(name, age) {
    super(name)
    this.age = age
  }
}

// 测试
let xm = new Child('xiaoming', 12) 
console.log(xm.name) // xiaoming
console.log(xm.age) // 12
xm.eat() // xiaoming is eating
```

### 14.数组排序

```js
//冒泡排序
function bubbleSort(arr) {
  let len = arr.length
  for (let i = 0; i < len - 1; i++) {
    // 从第一个元素开始，比较相邻的两个元素，前者大就交换位置
    for (let j = 0; j < len - 1 - i; j++) {
      if (arr[j] > arr[j + 1]) {
        let num = arr[j]
        arr[j] = arr[j + 1]
        arr[j + 1] = num
      }
    }
    // 每次遍历结束，都能找到一个最大值，放在数组最后
  }
  return arr
}

//测试
console.log(bubbleSort([2, 3, 1, 5, 4])) // [1, 2, 3, 4, 5]
```

### 15.事件总线，发布订阅模式

> 发布/订阅模式：基于一个主题/事件通道，希望接收通知的对象（称为subscriber）通过自定义事件订阅主题，被激活事件的对象（称为publisher）通过发布主题事件的方式被通知。
>
> 发布订阅模式中有三个角色，发布者 `Publisher` ，事件调度中心 `Event Channel` ，订阅者 `Subscriber` 。

```js
class EventEmitter {
  constructor() {
    this.events = {}//定义事件容器，用来装事件数组
  }

  //实现订阅
  on(name, fn) {  //事件名 事件方法
    if (this.events[name]) {     //如果该事件已经存在
      this.events[name].push(fn) //那么加入新的方法
    } else {
      this.events[name] = [fn]
    }
  }

  //删除订阅
  off(name, fn) {
    if (!this.events[name]) return;//若不存在要删除的事件
    this.events[name] = this.events[name].filter((item) => {
      //从该事件的方法数组中删除对应方法
      return item !== fn;
    });
  }

   // 只执行一次订阅事件
  once(name, fn) {
    function temp() {
      fn();
      this.off(type, temp);
    }
    this.on(type, temp);
  }
       
  // 触发事件
  //触发事件，参数：事件名 事件参数
  emit(name, ...rest) {
    this.events[name] &&
      this.events[name].forEach((fn) => fn.apply(this, 		rest));
  }
}

// 测试
const eventBus = new EventEmitter()
const task1 = () => { console.log('task1'); }
const task2 = () => { console.log('task2'); }

eventBus.on('task', task1)
eventBus.on('task', task2)
eventBus.off('task', task1)
setTimeout(() => {
  eventBus.emit('task') // task2
}, 1000)
```

```js
class EventEmitter {
  constructor() {
    this.events = {};
  }

  on(event, callback) {
    let callbacks = this.events[event] || [];
    callbacks.push(callback);
    this.events[event] = callbacks;

    return this;
  }

  off(event, callback) {
    let callbacks = this.events[event];
    this.events[event] = callbacks && callbacks.filter(fn => fn !== callback);

    return this;
  }

  emit(event, ...args) {
    let callbacks = this.events[event];
    callbacks.forEach(fn => {
      fn(...args);
    });

    return this;
  }

  once(event, callback) {
    let wrapFun = (...args) => {
      callback(...args);

      this.off(event, wrapFun);
    };
    this.on(event, wrapFun);

    return this;
  }
}
```

### 16.插入文本

> ```js
> // 解法2 JS实现 修改给定的HTML代码，定位body并用带p标签的文本代替原文本
> let body = document.getElementsByTagName("body")[0];
> body.innerHTML = body.innerText.replace("牛客网是一个专注于程序员的学习和成长的专业平台。","<p>牛客网是一个专注于程序员的学习和成长的专业平台。</p>");
>  
> // 解法3 JS实现 获取给定HTML代码中的文本，删除原子节点，给获取的文本加上p标签，然后插入页面
> const pInnerText = document.body.childNodes[0].textContent.trim();
> document.body.removeChild(document.body.childNodes[0]);
> const p = document.createElement('p');
> p.innerHTML = pInnerText;
> document.body.appendChild(p);
>  
> // 解法4 JS实现 创建一个p标签，嵌入到body中，缺点是没有删除给定的不带p标签的文本
> let p = document.createElement("p");
> p.innerText = "牛客网是一个专注于程序员的学习和成长的专业平台。";
> document.body.append(p);
> // document.querySelector("body").append(p);
> // body不需要selector，直接document.body就可以了
>  
> // 解法5 JS使用document.write，缺点是没有删除给定的不带p标签的文本
> document.write('<p>牛客网是一个专注于程序员的学习和成长的专业平台。</p>');
> ```

### 17.事件委托

> 要理解`DOM`相关事件，我们先要理解`“事件流”`这个概念，事件流描述的是从页面中接收事件的顺序。
>
> 事件冒泡：事件开始由最具体的元素接收，然后逐级向上传播到较为不具体的节点或文档。
>
> 事件捕获：事件开始由不太具体的节点接收，然后逐级向下传播到最具体的节点。它与事件冒泡是个相反的过程。
>
> `DOM2` 级事件规定的事件流包括三个阶段：事件捕获、目标阶段、事件冒泡。
>
> ### 事件委托
>
> 事件委托，通俗的说就是将元素的事件委托给它的父级或者更外级的元素处理，它的实现机制就是事件冒泡。
>
> **事件委托的优点**
>
> - 只需要将同类元素的事件委托给父级或者更外级的元素，不需要给所有的元素都绑定事件，减少内存占用空间，提升性能。
> - 动态新增的元素无需重新绑定事件
>
> **需要注意的点**
>
> - 事件委托的实现依靠的冒泡，因此不支持事件冒泡的事件就不适合使用事件委托。
> - 不是所有的事件绑定都适合使用事件委托，不恰当使用反而可能导致不需要绑定事件的元素也被绑定上了事件。

### 18.Object.freeze

> Object.freeze() 方法可以冻结一个对象。一个被冻结的对象再也不能被修改；冻结了一个对象则不能向这个对象添加新的属性，不能删除已有属性，不能修改该对象已有属性的可枚举性、可配置性、可写性，以及不能修改已有属性的值。此外，冻结一个对象后该对象的原型也不能被修改。freeze() 返回和传入的参数相同的对象
>
> ### 用法
>
> ```js
> const objectExample = {
>   prop1: 20,
>   prop2: "羊先生"
> };
> 
> // 默认情况下，我们可以根据需要修改对象的属性
> objectExample.prop1 = 100;
> console.log(objectExample.prop1)
> 
> // 冻结对象
> Object.freeze(objectExample);
> 
> objectExample.prop2 = "Alice" // 如果在严格模式会抛出失败，在非严格模式下只会抛出异常
> 
> console.log(objectExample.prop2);
> ```
>
> ### 结果
>
> #### 非严格模式
>
> ![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/b7a0758637b74c5598f5011456ae7ce7~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)image.png
>
> #### 添加严格模式
>
> ```js
> "use strict";
> ```
>
> ![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/43374c492d0e45099530d787b6ab18ab~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)image.png
>
> 抛出异常提示该属性是只读的

### 19.观察者模式

>  **什么是观察者模式那？**当对象之间存在一对多的依赖关系时，其中一个对象的状态发生改变，所有依赖它的对象都会收到通知，这就是观察者模式。
>
> 观察者模式与发布订阅模式相比，耦合度更高，通常用来实现一些响应式的效果。在观察者模式中，只有两个主体，分别是目标对象`Subject`，观察者`Observer`。
>
> - 观察者需`Observer`要实现`update`方法，供目标对象调用。`update`方法中可以执行自定义的业务代码。
> - 目标对象`Subject`也通常被叫做被观察者或主题，它的职能很单一，可以理解为，它只管理一种事件。`Subject`需要维护自身的观察者数组`observerList`，当自身发生变化时，通过调用自身的`notify`方法，依次通知每一个观察者执行`update`方法。
>
> ```js
> // 观察者
> class Observer {
>     /**
>      * 构造器
>      * @param {Function} cb 回调函数，收到目标对象通知时执行
>      */
>     constructor(cb){
>         if (typeof cb === 'function') {
>             this.cb = cb
>         } else {
>             throw new Error('Observer构造器必须传入函数类型！')
>         }
>     }
>     /**
>      * 被目标对象通知时执行
>      */
>     update() {
>         this.cb()
>     }
> }
> 
> // 目标对象
> class Subject {
>     constructor() {
>         // 维护观察者列表
>         this.observerList = []
>     }
>     /**
>      * 添加一个观察者
>      * @param {Observer} observer Observer实例
>      */
>     addObserver(observer) {
>         this.observerList.push(observer)
>     }
>     /**
>      * 通知所有的观察者
>      */
>     notify() {
>         this.observerList.forEach(observer => {
>             observer.update()
>         })
>     }
> }
> 
> const observerCallback = function() {
>     console.log('我被通知了')
> }
> const observer = new Observer(observerCallback)
> 
> const subject = new Subject();
> subject.addObserver(observer);
> subject.notify();
> ```

## 二、数据处理

### 1. 实现日期格式化函数

> 输入：
>
> ```javascript
> dateFormat(new Date('2020-12-01'), 'yyyy/MM/dd') // 2020/12/01
> dateFormat(new Date('2020-04-01'), 'yyyy/MM/dd') // 2020/04/01
> dateFormat(new Date('2020-04-01'), 'yyyy年MM月dd日') // 2020年04月01日
> 
> const dateFormat = (dateInput, format)=>{
>     var day = dateInput.getDate() 
>     var month = dateInput.getMonth() + 1  
>     var year = dateInput.getFullYear()   
>     format = format.replace(/yyyy/, year)
>     format = format.replace(/MM/,month)
>     format = format.replace(/dd/,day)
>     return format
> }
> ```

### 2. 交换a,b的值，不能用临时变量

> 巧妙的利用两个数的和、差：
>
> ```javascript
> a = a + b
> b = a - b
> a = a - b
> //
> [a,b]=[b,a]
> ```

### 3. 实现数组的乱序输出

> 主要的实现思路就是：
>
> - 取出数组的第一个元素，随机产生一个索引值，将该第一个元素和这个索引对应的元素进行交换。
> - 第二次取出数据数组第二个元素，随机产生一个除了索引为1的之外的索引值，并将第二个元素与该索引值对应的元素进行交换
> - 按照上面的规律执行，直到遍历完成
>
> ```javascript
> var arr = [1,2,3,4,5,6,7,8,9,10];
> for (var i = 0; i < arr.length; i++) {
>   const randomIndex = Math.round(Math.random() * (arr.length - 1 - i)) + i;
>   [arr[i], arr[randomIndex]] = [arr[randomIndex], arr[i]];
> }
> console.log(arr)
> ```
>
> 还有一方法就是倒序遍历：
>
> ```javascript
> var arr = [1,2,3,4,5,6,7,8,9,10];
> let length = arr.length,
>     randomIndex,
>     temp;
>   while (length) {
>     randomIndex = Math.floor(Math.random() * length--);
>     temp = arr[length];
>     arr[length] = arr[randomIndex];
>     arr[randomIndex] = temp;
>   }
> console.log(arr)
> ```

### 4. 实现数组元素求和

> - arr=[1,2,3,4,5,6,7,8,9,10]，求和
>
> ```javascript
> let arr=[1,2,3,4,5,6,7,8,9,10]
> //reduce()的第一个参数是一个函数，数组每个成员都会依次执行这个函数。
> //该函数包含四个可选变量：
> //  a,   累积变量，必须；默认为数组的第一个成员
> //  b,   当前变量，必须；默认为数组的第二个成员
> //  i,   当前位置，可选；表示第二个参数的位置，默认为1
> //  arr  原数组，可选
> //当指定reduce的第二个参数作为初始值时，b从数组的第一个成员开始遍历
> let sum = arr.reduce( (total,i) => total += i,0);
> console.log(sum);
> ```
>
> - arr=[1,2,3,[[4,5],6],7,8,9]，求和
>
> ```javascript
> arr=[1,2,3,[[4,5],6],7,8,9]
> //arr.toString()//'1,2,3,4,5,6,7,8,9'
> arr= arr.toString().split(',').reduce( (total,i) => total += Number(i),0);
> console.log(arr);
> ```
>
> 递归实现：
>
> ```javascript
> let arr = [1, 2, 3, 4, 5, 6] 
> function add(arr) {
>     if (arr.length == 1) return arr[0] 
>     return arr[0] + add(arr.slice(1)) 
> }
> console.log(add(arr)) // 21
> ```

### 5. 实现数组的扁平化

> **（1）递归实现**
>
> 普通的递归思路很容易理解，就是通过循环递归的方式，一项一项地去遍历，如果每一项还是一个数组，那么就继续往下遍历，利用递归程序的方法，来实现数组的每一项的连接：
>
> ```javascript
> let arr = [1, [2, [3, 4, 5]]];
> function flatten(arr) {
>   let result = [];
> 
>   for(let i = 0; i < arr.length; i++) {
>     if(Array.isArray(arr[i])) {
>       result = result.concat(flatten(arr[i]));
>     } else {
>       result.push(arr[i]);
>     }
>   }
>   return result;
> }
> flatten(arr);  //  [1, 2, 3, 4，5]
> ```
>
> **（2）reduce 函数迭代**
>
> 从上面普通的递归函数中可以看出，其实就是对数组的每一项进行处理，那么其实也可以用reduce 来实现数组的拼接，从而简化第一种方法的代码，改造后的代码如下所示：
>
> ```javascript
> let arr = [1, [2, [3, 4,5]]];
> function flatten(arr) {
>     return arr.reduce(function(prev, next){
>         return prev.concat(Array.isArray(next) ? flatten(next) : next)
>     }, [])
> }
> console.log(flatten(arr));//  [1, 2, 3, 4，5]
> ```
>
> **（3）扩展运算符实现**
>
> 这个方法的实现，采用了扩展运算符和 some 的方法，两者共同使用，达到数组扁平化的目的：
>
> ```javascript
> let arr = [1, [2, [3, 4, 5]]];
> function flatten(arr) {
>     while (arr.some(item => Array.isArray(item))) {
>         arr = [].concat(...arr);
>     }
>     return arr;
> }
> console.log(flatten(arr)); //  [1, 2, 3, 4，5]
> ```
>
> **（4）split 和 toString**
>
> 可以通过 split 和 toString 两个方法来共同实现数组扁平化，由于数组会默认带一个 toString 的方法，所以可以把数组直接转换成逗号分隔的字符串，然后再用 split 方法把字符串重新转换为数组，如下面的代码所示：
>
> ```javascript
> let arr = [1, [2, [3, 4, 5]]];
> function flatten(arr) {
>     return arr.toString().split(',');
> }
> console.log(flatten(arr)); //  [1, 2, 3, 4，5]
> ```
>
> 通过这两个方法可以将多维数组直接转换成逗号连接的字符串，然后再重新分隔成数组。
>
> **（5）ES6 中的 flat**
>
> 我们还可以直接调用 ES6 中的 flat 方法来实现数组扁平化。flat 方法的语法：`arr.flat([depth])`
>
> 其中 depth 是 flat 的参数，depth 是可以传递数组的展开深度（默认不填、数值是 1），即展开一层数组。如果层数不确定，参数可以传进 Infinity，代表不论多少层都要展开：
>
> ```javascript
> let arr = [1, [2, [3, 4]]];
> function flatten(arr) {
>   return arr.flat(Infinity);
> }
> console.log(flatten(arr)); //  [1, 2, 3, 4，5]
> ```
>
> 可以看出，一个嵌套了两层的数组，通过将 flat 方法的参数设置为 Infinity，达到了我们预期的效果。其实同样也可以设置成 2，也能实现这样的效果。在编程过程中，如果数组的嵌套层数不确定，最好直接使用 Infinity，可以达到扁平化。 
>
> **（6）正则和 JSON 方法** 在第4种方法中已经使用 toString 方法，其中仍然采用了将 JSON.stringify 的方法先转换为字符串，然后通过正则表达式过滤掉字符串中的数组的方括号，最后再利用 JSON.parse 把它转换成数组：
>
> ```javascript
> let arr = [1, [2, [3, [4, 5]]], 6];
> function flatten(arr) {
>   let str = JSON.stringify(arr);
>   str = str.replace(/(\[|\])/g, '');
>   str = '[' + str + ']';
>   return JSON.parse(str); 
> }
> console.log(flatten(arr)); //  [1, 2, 3, 4，5]
> ```

### 6.数组去重

```js
//Set去重
const newArr = [...new Set(arr)]
// 或
const newArr = Array.from(new Set(arr))

//index去重
function resetArr(arr) {
  let res = []
  arr.forEach(item => {
    if (res.indexOf(item) === -1) {
      res.push(item)
    }
  })
  return res
}

// 测试
const arr = [1, 1, 2, 3, 3]
console.log(resetArr(arr)) // [1, 2, 3]
```

### 7. 实现数组的flat方法

> ```js
> function _flat(arr, depth) {
>   if(!Array.isArray(arr) || depth <= 0) {
>     return arr;
>   }
>   return arr.reduce((prev, cur) => {
>     if (Array.isArray(cur)) {
>       return prev.concat(_flat(cur, depth - 1))
>     } else {
>       return prev.concat(cur);
>     }
>   }, []);
> }
> ```

### 8.获取URL参数

```js
//URLSearchParams 方法
// 创建一个URLSearchParams实例
const urlSearchParams = new URLSearchParams(window.location.search);
// 把键值对列表转换为一个对象
const params = Object.fromEntries(urlSearchParams.entries());

//split 方法
function getParams(url) {
  const res = {}
  if (url.includes('?')) {
    const str = url.split('?')[1]
    const arr = str.split('&')
    arr.forEach(item => {
      const key = item.split('=')[0]
      const val = item.split('=')[1]
      res[key] = decodeURIComponent(val) // 解码
    })
  }
  return res
}

// 测试
const user = getParams('http://www.baidu.com?user=%E9%98%BF%E9%A3%9E&age=16')
console.log(user) // { user: '阿飞', age: '16' }
```

## 三.排序

### 1. 冒泡排序--时间复杂度 n^2

> 思路：
>
> - 俩俩交换，大的放在后面，第一次排序后最大值已在数组末尾。(相邻的数依次比较，每一轮结束都有一个数字在最终位置上)
> - 因为俩俩交换，需要`n-1`趟排序，比如10个数，需要9趟排序
>
> 代码实现要点：
>
> - 两个for循环，外层循环控制排序的趟数，内层循环控制比较的次数
>   - **每趟过后，比较的次数都应该要减1**
> - 优化：如果一趟排序后也没有交换位置，那么该数组已有序～

> ```javascript
> function bubbleSort(arr) {
> // 缓存数组长度
> const len = arr.length;
> // 外层循环用于控制从头到尾的比较+交换到底有多少轮
> for (let i = 0; i < len-1; i++) {
>  // 内层循环用于完成每一轮遍历过程中的重复比较+交换
>  //内层-i,因为每一轮都有i个数已经排好了
>  for (let j = 0; j < len -i- 1; j++) {
>    // 若相邻元素前面的数比后面的大//从小到大排序
>    if (arr[j] > arr[j + 1]) {
>      // 交换两者
>      [arr[j], arr[j + 1]] = [arr[j + 1], arr[j]];
>    }
>  }
> }
> // 返回数组
> return arr;
> }
> // console.log(bubbleSort([3, 6, 2, 4, 1]));
> 3, 6, 2, 4, 1
> 3,2,4,1,6
> 2,3,1,4,6
> 2,1,3,4,6
> 1,2,3,4,6
> ```

### 2. 选择排序--时间复杂度 n^2

> 选择排序的思想是：
>
> - 在序列中找到最小（大）元素，放到序列的起始位置作为已排序序列；(开始时，已排序序列为空)
> - 再从剩余未排序元素中继续寻找最小（大）元素，放到已排序序列的末尾。
>
> 思路：
>
> - 找到数组中最大的元素，与数组最后一位元素交换
> - 当只有一个数时，则不需要选择了，因此需要`n-1`趟排序，比如10个数，需要9趟排序
>
> 代码实现要点：
>
> - **两个for循环，外层循环控制排序的趟数，内层循环找到当前趟数的最大值，随后与当前趟数组最后的一位元素交换**

> ```javascript
> function selectSort(arr) {
> // 缓存数组长度
> const len = arr.length;
> // 定义 minIndex，缓存当前区间最小值的索引，注意是索引
> let minIndex;
> // i 是当前排序区间的起点
> for (let i = 0; i < len - 1; i++) {
>  // 初始化 minIndex 为当前区间第一个元素
>  minIndex = i;
>  // i、j分别定义当前区间的上下界，i是左边界，j是右边界
>  for (let j = i; j < len; j++) {
>  // 若 j 处的数据项比当前最小值还要小，则更新最小值索引为 j
>    if (arr[j] < arr[minIndex]) {
>      minIndex = j;
>    }
>  }
>  // 如果 minIndex 对应元素不是目前的头部元素，则交换两者
>  if (minIndex !== i) {
>    [arr[i], arr[minIndex]] = [arr[minIndex],arr[i]];
>  }
> }
> return arr;
> }
> // console.log(selectSort([3, 6, 2, 4, 1]));
> //[] 3,6,2,4,1
> //[1] 6,2,4,3
> //[1,2] 6,4,3
> //[1,2,3] 4,6
> //[1,2,3,4] 6
> ```

### 3. 插入排序--时间复杂度 n^2

> 思路：
>
> - 将一个元素插入到已有序的数组中，在初始时未知是否存在有序的数据，因此将元素**第一个元素看成是有序的**
> - 与有序的数组进行比较，**比它大则直接放入，比它小则移动数组元素的位置，找到个合适的位置插入**
> - 当只有一个数时，则不需要插入了，因此需要`n-1`趟排序，比如10个数，需要9趟排序
>
> 代码实现：
>
> - 一个for循环内嵌一个while循环实现，外层for循环控制需要排序的趟数，while循环找到合适的插入位置(并且插入的位置不能小于0)

> ```javascript
> function insertSort(arr) {
> for (let i = 1; i < arr.length; i++) {
>  let j = i;
>  let target = arr[j];
> //如果前一位(已排序的数据)比当前数据要大，那么就进入循环比较
>  while (j > 0 && arr[j - 1] > target) {
>     //往后退一个位置，让当前数据与之前前位进行比较
>    arr[j] = arr[j - 1];
>    j--;
>  }
> //退出了循环说明找到了合适的位置了，将当前数据插入合适的位置中
>  arr[j] = target;
> }
> return arr;
> }
> // console.log(insertSort([3, 6, 2, 4, 1]));
> //[3] 6,2,4,1
> //[3,6] 2,4,1
> //[2,3,6] 4,1
> //[2,3,4,6] 1
> //[1,2,3,4,6]
> ```

### 4. 快排--时间复杂度 nlogn~ n^2 之间

> 思路：
>
> - 在数组中找一个元素(节点)，比它小的放在节点的左边，比它大的放在节点右边。一趟下来，比节点小的在左边，比节点大的在右边。
> - 不断执行这个操作....
>
> 代码实现：
>
> - 快速排序用递归比较好写。
>   - 支点取中间，使用L和R表示数组的最小和最大位置
>   - 不断进行比较，直到找到比支点小(大)的数，随后交换，不断减小范围～
> - 递归L到支点前一个元素(j)(执行相同的操作,同上)
> - 递归支点后一个元素(i)到R元素(执行相同的操作,同上)

> ```javascript
>function quickSort(arr) {
> if (arr.length < 2) {
> return arr;
> }
> const cur = arr[arr.length - 1];//基准
>   const left = arr.filter((v, i) => v <= cur && i !== arr.length - 1);//从左找第一个小于等于
>    const right = arr.filter((v) => v > cur);
>   return [...quickSort(left), cur, ...quickSort(right)];
>   }
>   // console.log(quickSort([3, 6, 2, 4, 1]));
>   ```

### 5. 归并排序--时间复杂度 nlog(n)

> 思路：
>
> - 将两个已排好序的数组合并成一个有序的数组。
>   - **将元素分隔开来，看成是有序的数组，进行比较合并**
>   - 不断拆分和合并，直到只有一个元素
>
> 代码实现：
>
> - 在第一趟排序时实质是两个元素(看成是两个已有序的数组)来进行合并，不断执行这样的操作，最终数组有序
> - 拆分左边，右边，合并...

> ```javascript
>function merge(left, right) {
> let res = [];
>let i = 0;
> let j = 0;
> while (i < left.length && j < right.length) {
>    if (left[i] < right[j]) {
>      res.push(left[i]);
>      i++;
>    } else {
>       res.push(right[j]);
>       j++;
>     }
>    }
>    if (i < left.length) {
>     res.push(...left.slice(i));
>    } else {
>    res.push(...right.slice(j));
>   }
>    return res;
>   }
>    
>   function mergeSort(arr) {
>   if (arr.length < 2) {
>  return arr;
> }
> const mid = Math.floor(arr.length / 2);
>   
>    const left = mergeSort(arr.slice(0, mid));
>   const right = mergeSort(arr.slice(mid));
>   return merge(left, right);
> }
>   // console.log(mergeSort([3, 6, 2, 4, 1]));
>   //3,6,2,4,1
>   
> ```

### 6.希尔排序

>##### 希尔排序须知：
>
>希尔排序是插入排序的一种更高效率的实现。它与插入排序的不同之处在于，它会优先比较距离较远的元素。希尔排序的核心在于间隔序列的设定。既可以提前设定好间隔序列，也可以动态的定义间隔序列。动态定义间隔序列的算法是《算法（第4版》的合著者Robert Sedgewick提出的。在这里，我就使用了这种方法。
>
>##### 希尔排序JavaScript代码实现：
>
>```cpp
>function shellSort(arr) {
>    var len = arr.length,
>        temp,
>        gap = 1;
>    while(gap < len/3) {          //动态定义间隔序列
>        gap =gap*3+1;
>    }
>    for (gap; gap > 0; gap = Math.floor(gap/3)) {
>        for (var i = gap; i < len; i++) {
>            temp = arr[i];
>            for (var j = i-gap; j >= 0 && arr[j] > temp; j-=gap) {
>                arr[j+gap] = arr[j];
>            }
>            arr[j+gap] = temp;
>        }
>    }
>    return arr;
>}
>```



### 6.总结

> ![image-20220320164533194](C:\Users\H\AppData\Roaming\Typora\typora-user-images\image-20220320164533194.png)

> 不稳定的排序：快希选一堆（快排，希尔，选择，堆排序）

> 在一趟结束后，就选出一个元素在其最终的位置上的排序是否就只有 ：
> **简单选择排序**、**快速排序**、**冒泡排序**、**堆排序**
