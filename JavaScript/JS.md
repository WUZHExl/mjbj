## 一、数据类型

### 1. JavaScript有哪些数据类型，它们的区别？

> JavaScript共有八种数据类型，分别是 Undefined、Null、Boolean、Number、String、Object、Symbol、BigInt。
>
> 其中 Symbol 和 BigInt 是ES6 中新增的数据类型：
>
> - Symbol 代表创建后独一无二且不可变的数据类型，它主要是为了解决可能出现的全局变量冲突的问题。
> - BigInt 是一种数字类型的数据，它可以表示任意精度格式的整数，使用 BigInt 可以安全地存储和操作大整数，即使这个数已经超出了 Number 能够表示的安全整数范围。
>
> 这些数据可以分为原始数据类型和引用数据类型：
>
> - 栈：原始数据类型（Undefined、Null、Boolean、Number、String）
> - 堆：引用数据类型（对象、数组和函数）
>
> 两种类型的区别在于**存储位置的不同：**
>
> - 原始数据类型直接存储在栈（stack）中的简单数据段，占据空间小、大小固定，属于被频繁使用数据，所以放入栈中存储；
> - 引用数据类型存储在堆（heap）中的对象，占据空间大、大小不固定。如果存储在栈中，将会影响程序运行的性能；引用数据类型在栈中存储了指针，该指针指向堆中该实体的起始地址。当解释器寻找引用值时，会首先检索其在栈中的地址，取得地址后从堆中获得实体。
>
> 堆和栈的概念存在于数据结构和操作系统内存中，在数据结构中：
>
> - 在数据结构中，栈中数据的存取方式为先进后出。
> - 堆是一个优先队列，是按优先级来进行排序的，优先级可以按照大小来规定。
>
> 在操作系统中，内存被分为栈区和堆区：
>
> - 栈区内存由编译器自动分配释放，存放函数的参数值，局部变量的值等。其操作方式类似于数据结构中的栈。
> - 堆区内存一般由开发着分配释放，若开发者不释放，程序结束时可能由垃圾回收机制回收。

### 2. 数据类型检测的方式有哪些

> **（1）typeof**
>
> > typeof是判断基本类型的，基本类型有7种: `Undefined, null, Boolean, Number, String, bigint, symbol（es6）`，还能判断出function 然后其他都是object（null是object）
>
> ```javascript
> console.log(typeof 2);               // number
> console.log(typeof true);            // boolean
> console.log(typeof 'str');           // string
> console.log(typeof []);              // object    
> console.log(typeof function(){});    // function
> console.log(typeof {});              // object
> console.log(typeof undefined);       // undefined
> console.log(typeof null);            // object
> ```
>
> 其中数组、对象、null都会被判断为object，其他判断都正确。
>
> **（2）instanceof**
>
> `instanceof`可以正确判断对象的类型
>
> instanceof 运算符用于判断构造函数的 prototype 属性是否出现在对象的原型链中的任何位置
>
> ```javascript
> console.log(2 instanceof Number);                    // false
> console.log(true instanceof Boolean);                // false 
> console.log('str' instanceof String);                // false 
> 
> console.log([] instanceof Array);                    // true
> console.log(function(){} instanceof Function);       // true
> console.log({} instanceof Object);                   // true
> ```
>
> 可以看到，`instanceof`**只能正确判断引用数据类型**，而不能判断基本数据类型。`instanceof` 运算符可以用来测试一个对象在其原型链中是否存在一个构造函数的 `prototype` 属性。
>
> **（3） constructor**
>
> ```javascript
> console.log((2).constructor === Number); // true
> console.log((true).constructor === Boolean); // true
> console.log(('str').constructor === String); // true
> console.log(([]).constructor === Array); // true
> console.log((function() {}).constructor === Function); // true
> console.log(({}).constructor === Object); // true
> ```
>
> `constructor`有两个作用，一是判断数据的类型，二是对象实例通过 `constrcutor` 对象访问它的构造函数。需要注意，如果创建一个对象来改变它的原型，`constructor`就不能用来判断数据类型了：
>
> ```javascript
> function Fn(){};
> 
> Fn.prototype = new Array();
> 
> var f = new Fn();
> 
> console.log(f.constructor===Fn);    // false
> console.log(f.constructor===Array); // true
> ```
>
> **（4）Object.prototype.toString.call()**
>
> `Object.prototype.toString.call()` 使用 Object 对象的原型方法 toString 来判断数据类型：
>
> ```javascript
> var a = Object.prototype.toString;
> 
> console.log(a.call(2));            //[object Number]
> console.log(a.call(true));        //[object Boolean]
> console.log(a.call('str'));        //[object String]
> console.log(a.call([]));            //[object Array]
> console.log(a.call(function(){}))//[object Function]
> console.log(a.call({}));           //[object Object]
> console.log(a.call(undefined)); //[object Undefined]
> console.log(a.call(null));           //[object Null]
> ```
>
> 同样是检测对象obj调用toString方法，obj.toString()的结果和Object.prototype.toString.call(obj)的结果不一样，这是为什么？
>
> 这是因为toString是Object的原型方法，而Array、function等**类型作为Object的实例，都重写了toString方法**。不同的对象类型调用toString方法时，根据原型链的知识，调用的是对应的重写之后的toString方法（function类型返回内容为函数体的字符串，Array类型返回元素组成的字符串…），而不会去调用Object上原型toString方法（返回对象的具体类型），所以**采用obj.toString()不能得到其对象类型，只能将obj转换为字符串类型**；因此，在想要得到对象的具体类型时，应该调用Object原型上的toString方法。
>
> ```js
> (2).toString()  //'2'
> (true).toString() //'true'
> ```

### 3. 判断数组的方式有哪些

> - 通过Object.prototype.toString.call()做判断
>
> ```javascript
> Object.prototype.toString.call(obj).slice(8,-1) === 'Array';
> ```
>
> - 通过原型链做判断
>
> ```javascript
> obj.__proto__ === Array.prototype;
> ```
>
> - 通过ES6的Array.isArray()做判断
>
> ```javascript
> Array.isArrray(obj);
> ```
>
> - 通过instanceof做判断
>
> ```javascript
> obj instanceof Array
> ```
>
> - 通过Array.prototype.isPrototypeOf
>
> ```javascript
> Array.prototype.isPrototypeOf(obj)
> ```

### 4. null和undefined区别

> 首先 Undefined 和 Null 都是基本数据类型，这两个基本数据类型分别都只有一个值，就是 undefined 和 null。
>
> undefined 代表的含义是**未定义**，null 代表的含义是**空对象**。一般变量声明了但还没有定义的时候会返回 undefined，null主要用于赋值给一些可能会返回对象的变量，作为初始化。
>
> undefined 在 JavaScript 中不是一个保留字，这意味着可以使用 undefined 来作为一个变量名，但是这样的做法是非常危险的，它会影响对 undefined 值的判断。我们可以通过一些方法获得安全的 undefined 值，比如说 void 0。
>
> 当对这两种类型使用 typeof 进行判断时，Null 类型化会返回 “object”，这是一个历史遗留的问题。当使用双等号对两种类型的值进行比较时会返回 true，使用三个等号时会返回 false。

### 5. typeof null 的结果是什么，为什么？

> typeof null 的结果是Object。
>
> 在 JavaScript 第一个版本中，所有值都存储在 32 位的单元中，每个单元包含一个小的 **类型标签(1-3 bits)** 以及当前要存储值的真实数据。类型标签存储在每个单元的低位中，共有五种数据类型：
>
> ```javascript
> 000: object   - 当前存储的数据指向一个对象。
>   1: int      - 当前存储的数据是一个 31 位的有符号整数。
> 010: double   - 当前存储的数据指向一个双精度的浮点数。
> 100: string   - 当前存储的数据指向一个字符串。
> 110: boolean  - 当前存储的数据是布尔值。
> ```
>
> 如果最低位是 1，则类型标签标志位的长度只有一位；如果最低位是 0，则类型标签标志位的长度占三位，为存储其他四种数据类型提供了额外两个 bit 的长度。
>
> 有两种特殊数据类型：
>
> - undefined的值是 (-2)30(一个超出整数范围的数字)；
> - null 的值是机器码 NULL 指针(null 指针的值全是 0)
>
> 那也就是说null的类型标签也是000，和Object的类型标签一样，所以会被判定为Object。

### 6. intanceof 操作符的实现原理及实现

> instanceof 运算符用于判断构造函数的 prototype 属性是否出现在对象的原型链中的任何位置。
>
> ```javascript
> function myInstanceof(left, right) {
>   // 获取对象的原型
>   let proto = Object.getPrototypeOf(left)
>   // 获取构造函数的 prototype 对象
>   let prototype = right.prototype; 
>  
>   // 判断构造函数的 prototype 对象是否在对象的原型链上
>   while (true) {
>     if (!proto) return false;
>     if (proto === prototype) return true;
>     // 如果没有找到，就继续从其原型上找，Object.getPrototypeOf方法用来获取指定对象的原型
>     proto = Object.getPrototypeOf(proto);
>   }
> }
> ```

### 7. 为什么0.1+0.2 ! == 0.3，如何让其相等

>在开发过程中遇到类似这样的问题：
>
>```javascript
>let n1 = 0.1, n2 = 0.2
>console.log(n1 + n2)  // 0.30000000000000004
>```
>
>这里得到的不是想要的结果，要想等于0.3，就要把它进行转化：
>
>```javascript
>(n1 + n2).toFixed(2) // 注意，toFixed为四舍五入
>```
>
>`toFixed(num)` 方法可把 Number 四舍五入为指定小数位数的数字。那为什么会出现这样的结果呢？
>
>计算机是通过二进制的方式存储数据的，所以计算机计算0.1+0.2的时候，实际上是计算的两个数的二进制的和。0.1的二进制是`0.0001100110011001100...`（1100循环），0.2的二进制是：`0.00110011001100...`（1100循环），这两个数的二进制都是无限循环的数。那JavaScript是如何处理无限循环的二进制小数呢？
>
>一般我们认为数字包括整数和小数，但是在 JavaScript 中只有一种数字类型：Number，它的实现遵循IEEE 754标准，使用64位固定长度来表示，也就是标准的double双精度浮点数。在二进制科学表示法中，双精度浮点数的小数部分最多只能保留52位，再加上前面的1，其实就是保留53位有效数字，剩余的需要舍去，遵从“0舍1入”的原则。
>
>根据这个原则，0.1和0.2的二进制数相加，再转化为十进制数就是：`0.30000000000000004`。

### 8. 如何获取安全的 undefined 值？

> 因为 undefined 是一个标识符，所以可以被当作变量来使用和赋值，但是这样会影响 undefined 的正常判断。表达式 void ___ 没有返回值，因此返回结果是 undefined。void 并不改变表达式的结果，只是让表达式不返回值。因此可以用 void 0 来获得 undefined。

### 9. typeof NaN 的结果是什么？

> NaN 指“不是一个数字”（not a number），NaN 是一个“警戒值”（sentinel value，有特殊用途的常规值），用于指出数字类型中的错误情况，即“执行数学运算没有成功，这是失败后返回的结果”。
>
> ```javascript
> typeof NaN; // "number"
> ```
>
> NaN 是一个特殊值，它和自身不相等，是唯一一个非自反（自反，reflexive，即 x === x 不成立）的值。而 NaN !== NaN 为 true。

### 10. isNaN 和 Number.isNaN 函数的区别？

> * 函数 isNaN 接收参数后，会尝试将这个参数转换为数值，任何不能被转换为数值的的值都会返回 true，因此非数字值传入也会返回 true ，会影响 NaN 的判断。
>
> * 函数 Number.isNaN 会首先判断传入参数是否为数字，如果是数字再继续判断是否为 NaN ，不会进行数据类型的转换，这种方法对于 NaN 的判断更为准确。

### 11. == 操作符的强制类型转换规则？

> 对于 `==` 来说，如果对比双方的类型**不一样**，就会进行**类型转换**。假如对比 `x` 和 `y` 是否相同，就会进行如下判断流程：
>
> 1. 首先会判断两者类型是否**相同，**相同的话就比较两者的大小；
> 2. 类型不相同的话，就会进行类型转换；
> 3. 会先判断是否在对比 `null` 和 `undefined`，是的话就会返回 `true`
> 4. 判断两者类型是否为 `string` 和 `number`，是的话就会将字符串转换为 `number`
>
> ```javascript
> 1 == '1'
>       ↓
> 1 ==  1
> ```
>
> 5. 判断其中一方是否为 `boolean`，是的话就会把 `boolean` 转为 `number` 再进行判断
>
> ```javascript
> '1' == true
>         ↓
> '1' ==  1
>         ↓
>  1  ==  1
> ```
>
> 6. 判断其中一方是否为 `object` 且另一方为 `string`、`number` 或者 `symbol`，是的话就会把 `object` 转为原始类型再进行判断
>
> ```javascript
> '1' == { name: 'js' }        ↓'1' == '[object Object]'
> ```

### 12. 其他值到字符串的转换规则？

> * Null 和 Undefined 类型 ，null 转换为 "null"，undefined 转换为 "undefined"，
>
> * Boolean 类型，true 转换为 "true"，false 转换为 "false"。
>
> * Number 类型的值直接转换，不过那些极小和极大的数字会使用指数形式。
>
> * Symbol 类型的值直接转换，但是只允许显式强制类型转换，使用隐式强制类型转换会产生错误。
>
> * 对普通对象来说，除非自行定义 toString() 方法，否则会调用 toString()（Object.prototype.toString()）来返回内部属性 [[Class]] 的值，如"[object Object]"。如果对象有自己的 toString() 方法，字符串化时就会调用该方法并使用其返回值。

### 13. 其他值到数字值的转换规则？

> - Undefined 类型的值转换为 NaN。
> - Null 类型的值转换为 0。
> - Boolean 类型的值，true 转换为 1，false 转换为 0。
> - String 类型的值转换如同使用 Number() 函数进行转换，如果包含非数字值则转换为 NaN，空字符串为 0。
> - Symbol 类型的值不能转换为数字，会报错。
> - 对象（包括数组）会首先被转换为相应的基本类型值，如果返回的是非数字的基本类型值，则再遵循以上规则将其强制转换为数字。
>
> 为了将值转换为相应的基本类型值，抽象操作 ToPrimitive 会首先（通过内部操作 DefaultValue）检查该值是否有valueOf()方法。如果有并且返回基本类型值，就使用该值进行强制类型转换。如果没有就使用 toString() 的返回值（如果存在）来进行强制类型转换。
>
> 如果 valueOf() 和 toString() 均不返回基本类型值，会产生 TypeError 错误。

### 14. 其他值到布尔类型的值的转换规则？

> 以下这些是假值： • undefined • null • false • +0、-0 和 NaN • ""
>
> 假值的布尔强制类型转换结果为 false。从逻辑上说，假值列表以外的都应该是真值。

### 15. Object.is() 与比较操作符 `===、 ==`的区别？

> * 使用双等号（==）进行相等判断时，如果两边的类型不一致，则会进行强制类型转化后再进行比较。
>
> * 使用三等号（===）进行相等判断时，如果两边的类型不一致时，不会做强制类型准换，直接返回 false。
>
> * 使用 Object.is 来进行相等判断时，一般情况下和三等号的判断相同，它处理了一些特殊的情况，比如 -0 和 +0 不再相等，两个 NaN 是相等的。

### 16.什么是 JavaScript 中的包装类型？

> 在 JavaScript 中，基本类型是没有属性和方法的，但是为了便于操作基本类型的值，在调用基本类型的属性或方法时 JavaScript 会在后台隐式地将基本类型的值转换为对象，如：
>
> ```javascript
> const a = "abc";
> a.length; // 3
> a.toUpperCase(); // "ABC"
> ```
>
> 在访问`'abc'.length`时，JavaScript 将`'abc'`在后台转换成`String('abc')`，然后再访问其`length`属性。
>
> JavaScript也可以使用`Object`函数显式地将基本类型转换为包装类型：
>
> ```javascript
> var a = 'abc'
> Object(a) // String {"abc"}
> ```
>
> 也可以使用`valueOf`方法将包装类型倒转成基本类型：
>
> ```javascript
> var a = 'abc'
> var b = Object(a)
> var c = b.valueOf() // 'abc'
> ```
>
> 看看如下代码会打印出什么：
>
> ```javascript
> var a = new Boolean( false );
> if (!a) {
> 	console.log( "Oops" ); // never runs
> }
> ```
>
> 答案是什么都不会打印，因为虽然包裹的基本类型是`false`，但是`false`被包裹成包装类型后就成了对象，所以其非值为`false`，所以循环体中的内容不会运行。

### 17.`+` 操作符什么时候用于字符串的拼接？

> 根据 ES5 规范，如果某个操作数是字符串或者能够通过以下步骤转换为字符串的话，+ 将进行拼接操作。如果其中一个操作数是对象（包括数组），则首先对其调用 ToPrimitive 抽象操作，该抽象操作再调用 [[DefaultValue]]，以数字作为上下文。如果不能转换为字符串，则会将其转换为数字类型来进行计算。
>
> 简单来说就是，如果 + 的其中一个操作数是字符串（或者通过以上步骤最终得到字符串），则执行字符串拼接，否则执行数字加法。
>
> 那么对于除了加法的运算符来说，只要其中一方是数字，那么另一方就会被转为数字。

###  18.object.assign和扩展运算法是深拷贝还是浅拷贝，两者区别

> 扩展运算符：
>
> ```javascript
> let outObj = {
> inObj: {a: 1, b: 2}
> }
> let newObj = {...outObj}
> newObj.inObj.a = 2
> console.log(outObj) // {inObj: {a: 2, b: 2}}
> ```
>
> Object.assign():
>
> ```javascript
> let outObj = {
> inObj: {a: 1, b: 2}
> }
> let newObj = Object.assign({}, outObj)
> newObj.inObj.a = 2
> console.log(outObj) // {inObj: {a: 2, b: 2}}
> ```
>
> 可以看到，两者都是浅拷贝。
>
> - Object.assign()方法接收的第一个参数作为目标对象，后面的所有参数作为源对象。然后把所有的源对象合并到目标对象中。它会修改了一个对象，因此会触发 ES6 setter。
> - 扩展操作符（…）使用它时，数组或对象中的每一个值都会被拷贝到一个新的数组或对象中。它不复制继承的属性或类的属性，但是它会复制ES6的 symbols 属性。

### 19.JS中的变量存储机制

> JS 内存空间分为栈(stack)空间、堆(heap)空间、代码空间。其中代码空间用于存放可执行代码
>
> * 基本类型：保存在栈内存中，因为这些类型在存中分别占有固定大小的空间，通过按值来访问。
>
> * 引用类型：保存在堆内存中，因为这种值的大小不固定，因此不能把它们保存到栈内存中，但内存地址大小的固定的，因此保存在堆内存中，在栈内存中存放的只是该对象的访问地址。当查询引用类型的变量时， 先从栈中读取内存地址， 然后再通过地址找到堆中的值。对于这种，我们把它叫做按引用访问。

## 二、ES6

### 1. let、const、var的区别

> **（1）块级作用域：** 块作用域由 `{ }`包括，let和const具有块级作用域，var不存在块级作用域。块级作用域解决了ES5中的两个问题：
>
> - 内层变量可能覆盖外层变量
> - 用来计数的循环变量泄露为全局变量
>
> **（2）变量提升：** var存在变量提升，let和const不存在变量提升，即在变量只能在声明之后使用，否在会报错。
>
> **（3）给全局添加属性：** 浏览器的全局对象是window，Node的全局对象是global。var声明的变量为全局变量，并且会将该变量添加为全局对象的属性，但是let和const不会。
>
> **（4）重复声明：** var声明变量时，可以重复声明变量，后声明的同名变量会覆盖之前声明的遍历。const和let不允许重复声明变量。
>
> **（5）暂时性死区：** 在使用let、const命令声明变量之前，该变量都是不可用的。这在语法上，称为**暂时性死区**。使用var声明的变量不存在暂时性死区。
>
> **（6）初始值设置：** 在变量声明时，var 和 let 可以不用设置初始值。而const声明变量必须设置初始值。
>
> **（7）指针指向：** let和const都是ES6新增的用于创建变量的语法。 let创建的变量是可以更改指针指向（可以重新赋值）。但const声明的变量是不允许改变指针的指向。

### 2. const对象的属性可以修改吗

> const保证的并不是变量的值不能改动，而是变量指向的那个内存地址不能改动。对于基本类型的数据（数值、字符串、布尔值），其值就保存在变量指向的那个内存地址，因此等同于常量。
>
> 但对于引用类型的数据（主要是对象和数组）来说，变量指向数据的内存地址，保存的只是一个指针，const只能保证这个指针是固定不变的，至于它指向的数据结构是不是可变的，就完全不能控制了。

### 3. 如果new一个箭头函数的会怎么样

> 箭头函数是ES6中的提出来的，它没有prototype，也没有自己的this指向，更不可以使用arguments参数，所以不能New一个箭头函数。
>
> new操作符的实现步骤如下：
>
> 1. 创建一个对象
> 2. 将构造函数的作用域赋给新对象（也就是将对象的__proto__属性指向构造函数的prototype属性）
> 3. 指向构造函数中的代码，构造函数中的this指向该对象（也就是为这个对象添加属性和方法）
> 4. 返回新的对象
>
> 所以，上面的第二、三步，箭头函数都是没有办法执行的。

### 4. 箭头函数与普通函数的区别

> **（1）箭头函数比普通函数更加简洁**
>
> - 如果没有参数，就直接写一个空括号即可
> - 如果只有一个参数，可以省去参数的括号
> - 如果有多个参数，用逗号分割
> - 如果函数体的返回值只有一句，可以省略大括号
> - 如果函数体不需要返回值，且只有一句话，可以给这个语句前面加一个void关键字。最常见的就是调用一个函数：
>
> ```javascript
> let fn = () => void doesNotReturn();
> ```
>
> **（2）箭头函数没有自己的this**
>
> 箭头函数不会创建自己的this， 所以它没有自己的this，它只会在自己作用域的上一层继承this。所以箭头函数中this的指向在它在定义时已经确定了，之后不会改变。
>
> **（3）箭头函数继承来的this指向永远不会改变**
>
> ```javascript
> var id = 'GLOBAL';
> var obj = {
>   id: 'OBJ',
>   a: function(){
>     console.log(this.id);
>   },
>   b: () => {
>     console.log(this.id);
>   }
> };
> obj.a();    // 'OBJ'
> obj.b();    // 'GLOBAL'
> new obj.a()  // undefined
> new obj.b()  // Uncaught TypeError: obj.b is not a constructor
> ```
>
> 对象obj的方法b是使用箭头函数定义的，这个函数中的this就永远指向**它定义时所处的全局执行环境中的this**，即便这个函数是作为对象obj的方法调用，this依旧指向Window对象。需要注意，定义对象的大括号`{}`是无法形成一个单独的执行环境的，它依旧是处于全局执行环境中。
>
> **（4）call()、apply()、bind()等方法不能改变箭头函数中this的指向**
>
> ```javascript
> var id = 'Global';
> let fun1 = () => {
>     console.log(this.id)
> };
> fun1();                     // 'Global'
> fun1.call({id: 'Obj'});     // 'Global'
> fun1.apply({id: 'Obj'});    // 'Global'
> fun1.bind({id: 'Obj'})();   // 'Global'
> ```
>
> **（5）箭头函数不能作为构造函数使用**
>
> 构造函数在new的步骤在上面已经说过了，实际上第二步就是将函数中的this指向该对象。 但是由于箭头函数时没有自己的this的，且this指向外层的执行环境，且不能改变指向，所以不能当做构造函数使用。
>
> **（6）箭头函数没有自己的arguments**
>
> 箭头函数没有自己的arguments对象。在箭头函数中访问arguments实际上获得的是它外层函数的arguments值。
>
> **（7）箭头函数没有prototype**
>
> **（8）箭头函数不能用作Generator函数，不能使用yeild关键字**

### 5. 箭头函数的**this**指向哪⾥？

> 箭头函数不同于传统JavaScript中的函数，箭头函数并没有属于⾃⼰的this，它所谓的this是捕获其所在上下⽂的 this 值，作为⾃⼰的 this 值，并且由于没有属于⾃⼰的this，所以是不会被new调⽤的，这个所谓的this也不会被改变。
>
> 可以⽤Babel理解⼀下箭头函数:
>
> ```javascript
> // ES6 
> const obj = { 
>   getArrow() { 
>     return () => { 
>       console.log(this === obj); 
>     }; 
>   } 
> }
> ```
>
> 转化后：
>
> ```javascript
> // ES5，由 Babel 转译
> var obj = { 
>    getArrow: function getArrow() { 
>      var _this = this; 
>      return function () { 
>         console.log(_this === obj); 
>      }; 
>    } 
> };
> ```

### 6. 扩展运算符的作用及使用场景

> **（1）对象扩展运算符**
>
> 对象的扩展运算符(...)用于取出参数对象中的所有可遍历属性，拷贝到当前对象之中。
>
> ```javascript
> let bar = { a: 1, b: 2 };
> let baz = { ...bar }; // { a: 1, b: 2 }
> ```
>
> 上述方法实际上等价于:
>
> ```javascript
> let bar = { a: 1, b: 2 };
> let baz = Object.assign({}, bar); // { a: 1, b: 2 }
> ```
>
> `Object.assign`方法用于对象的合并，将源对象`（source）`的所有可枚举属性，复制到目标对象`（target）`。`Object.assign`方法的第一个参数是目标对象，后面的参数都是源对象。(**如果目标对象与源对象有同名属性，或多个源对象有同名属性，则后面的属性会覆盖前面的属性**)。
>
> 同样，如果用户自定义的属性，放在扩展运算符后面，则扩展运算符内部的同名属性会被覆盖掉。
>
> ```javascript
> let bar = {a: 1, b: 2};
> let baz = {...bar, ...{a:2, b: 4}};  // {a: 2, b: 4}
> ```
>
> 利用上述特性就可以很方便的修改对象的部分属性。在`redux`中的`reducer`函数规定必须是**一个纯函数**，`reducer`中的`state`对象要求不能直接修改，可以通过扩展运算符把修改路径的对象都复制一遍，然后产生一个新的对象返回。
>
> 需要注意：**扩展运算符对对象实例的拷贝属于浅拷贝**。
>
> **（2）数组扩展运算符**
>
> 数组的扩展运算符可以将一个数组转为用逗号分隔的参数序列，且每次只能展开一层数组。
>
> ```javascript
> console.log(...[1, 2, 3])
> // 1 2 3
> console.log(...[1, [2, 3, 4], 5])
> // 1 [2, 3, 4] 5
> ```
>
> 下面是数组的扩展运算符的应用：
>
> - **将数组转换为参数序列**
>
> ```javascript
> function add(x, y) {
>   return x + y;
> }
> const numbers = [1, 2];
> add(...numbers) // 3
> ```
>
> - **复制数组**
>
> ```javascript
> const arr1 = [1, 2];
> const arr2 = [...arr1];
> ```
>
> 要记住：**扩展运算符(…)用于取出参数对象中的所有可遍历属性，拷贝到当前对象之中**，这里参数对象是个数组，数组里面的所有对象都是基础数据类型，将所有基础数据类型重新拷贝到新的数组中。
>
> - **合并数组**
>
> 如果想在数组内合并数组，可以这样：
>
> ```javascript
> const arr1 = ['two', 'three'];const arr2 = ['one', ...arr1, 'four', 'five'];// ["one", "two", "three", "four", "five"]
> ```
>
> - **扩展运算符与解构赋值结合起来，用于生成数组**
>
> ```javascript
> const [first, ...rest] = [1, 2, 3, 4, 5];first // 1rest  // [2, 3, 4, 5]
> ```
>
> 需要注意：**如果将扩展运算符用于数组赋值，只能放在参数的最后一位，否则会报错。**
>
> ```javascript
> const [...rest, last] = [1, 2, 3, 4, 5];         // 报错const [first, ...rest, last] = [1, 2, 3, 4, 5];  // 报错
> ```
>
> - **将字符串转为真正的数组**
>
> ```javascript
> [...'hello']    // [ "h", "e", "l", "l", "o" ]
> ```
>
> - **任何 Iterator 接口的对象，都可以用扩展运算符转为真正的数组**
>
> 比较常见的应用是可以将某些数据结构转为数组：
>
> ```javascript
> // arguments对象
> function foo() {
>   const args = [...arguments];
> }
> ```
>
> 用于替换`es5`中的`Array.prototype.slice.call(arguments)`写法。
>
> - **使用**`Math`**函数获取数组中特定的值**
>
> ```javascript
> const numbers = [9, 4, 7, 1];
> Math.min(...numbers); // 1
> Math.max(...numbers); // 9
> ```

### 7. Proxy 可以实现什么功能？

> 在 Vue3.0 中通过 `Proxy` 来替换原本的 `Object.defineProperty` 来实现数据响应式。
>
> Proxy 是 ES6 中新增的功能，它可以用来自定义对象中的操作。
>
> ```javascript
> let p = new Proxy(target, handler)
> ```
>
> `target` 代表需要添加代理的对象，`handler` 用来自定义对象中的操作，比如可以用来自定义 `set` 或者 `get` 函数。
>
> 下面来通过 `Proxy` 来实现一个数据响应式：
>
> ```javascript
> let onWatch = (obj, setBind, getLogger) => {
>   let handler = {
>     get(target, property, receiver) {
>       getLogger(target, property)
>       return Reflect.get(target, property, receiver)
>     },
>     set(target, property, value, receiver) {
>       setBind(value, property)
>       return Reflect.set(target, property, value)
>     }
>   }
>   return new Proxy(obj, handler)
> }
> let obj = { a: 1 }
> let p = onWatch(
>   obj,
>   (v, property) => {
>     console.log(`监听到属性${property}改变为${v}`)
>   },
>   (target, property) => {
>     console.log(`'${property}' = ${target[property]}`)
>   }
> )
> p.a = 2 // 监听到属性a改变
> p.a // 'a' = 2
> ```
>
> 在上述代码中，通过自定义 `set` 和 `get` 函数的方式，在原本的逻辑中插入了我们的函数逻辑，实现了在对对象任何属性进行读写时发出通知。
>
> 当然这是简单版的响应式实现，如果需要实现一个 Vue 中的响应式，需要在 `get` 中收集依赖，在 `set` 派发更新，之所以 Vue3.0 要使用 `Proxy` 替换原本的 API 原因在于 `Proxy` 无需一层层递归为每个属性添加代理，一次即可完成以上操作，性能上更好，并且原本的实现有一些数据更新不能监听到，但是 `Proxy` 可以完美监听到任何方式的数据改变，唯一缺陷就是浏览器的兼容性不好。

### 8. 对对象与数组的解构的理解

> 解构是 ES6 提供的一种新的提取数据的模式，这种模式能够从对象或数组里有针对性地拿到想要的数值。
>
>  **1）数组的解构** 在解构数组时，以元素的位置为匹配条件来提取想要的数据的：
>
> ```javascript
> const [a, b, c] = [1, 2, 3]
> ```
>
> 最终，a、b、c分别被赋予了数组第0、1、2个索引位的值：
>
> 右侧的元素值，精准地被映射到了左侧的第0、1、2个变量里去，这就是数组解构的工作模式。还可以通过给左侧变量数组设置空占位的方式，实现对数组中某几个元素的精准提取：
>
> ```javascript
> const [a,,c] = [1,2,3]
> ```
>
> 通过把中间位留空，可以顺利地把数组第一位和最后一位的值赋给 a、c 两个变量。
>
> `a=1,c=3`
>
> **2）对象的解构** 对象解构比数组结构稍微复杂一些，也更显强大。在解构对象时，是以属性的名称为匹配条件，来提取想要的数据的。现在定义一个对象：
>
> ```javascript
> const stu = {
>   name: 'Bob',
>   age: 24
> }
> ```
>
> 假如想要解构它的两个自有属性，可以这样：
>
> ```javascript
> const { name, age } = stu
> ```
>
> 这样就得到了 name 和 age 两个和 stu 平级的变量
>
> `name='Bob',age=24`

### 9. **如何提取高度嵌套的对象里的指定属性？**

> 有时会遇到一些嵌套程度非常深的对象：
>
> ```javascript
> const school = {
>    classes: {
>       stu: {
>          name: 'Bob',
>          age: 24,
>       }
>    }
> }
> ```
>
> 像此处的 name 这个变量，嵌套了四层，此时如果仍然尝试老方法来提取它：
>
> ```javascript
> const { name } = school
> ```
>
> 显然是不奏效的，因为 school 这个对象本身是没有 name 这个属性的，name 位于 school 对象的“儿子的儿子”对象里面。要想把 name 提取出来，一种比较笨的方法是逐层解构：
>
> ```javascript
> const { classes } = school
> const { stu } = classes
> const { name } = stu
> name // 'Bob'
> ```
>
> 但是还有一种更标准的做法，可以用一行代码来解决这个问题：
>
> ```javascript
> const { classes: { stu: { name } }} = school
> console.log(name)  // 'Bob'
> ```
>
> 可以在解构出来的变量名右侧，通过冒号+{目标属性名}这种形式，进一步解构它，一直解构到拿到目标数据为止。

### 10. 对 rest 参数的理解

> 扩展运算符被用在函数形参上时，**它还可以把一个分离的参数序列整合成一个数组**：
>
> ```javascript
> function mutiple(...args) {
>   let result = 1;
>   for (var val of args) {
>     result *= val;
>   }
>   return result;
> }
> mutiple(1, 2, 3, 4) // 24
> ```
>
> 这里，传入 mutiple 的是四个分离的参数，但是如果在 mutiple 函数里尝试输出 args 的值，会发现它是一个数组：
>
> ```javascript
> function mutiple(...args) {
>   console.log(args)
> }
> mutiple(1, 2, 3, 4) // [1, 2, 3, 4]
> ```
>
> 这就是 … rest运算符的又一层威力了，它可以把函数的多个入参收敛进一个数组里。这一点**经常用于获取函数的多余参数，或者像上面这样处理函数参数个数不确定的情况。**

### 11. ES6中模板语法与字符串处理

> ES6 提出了“模板语法”的概念。在 ES6 以前，拼接字符串是很麻烦的事情：
>
> ```javascript
> var name = 'css'   
> var career = 'coder' 
> var hobby = ['coding', 'writing']
> var finalString = 'my name is ' + name + ', I work as a ' + career + ', I love ' + hobby[0] + ' and ' + hobby[1]
> ```
>
> 仅仅几个变量，写了这么多加号，还要时刻小心里面的空格和标点符号有没有跟错地方。但是有了模板字符串，拼接难度直线下降：
>
> ```javascript
> var name = 'css'   
> var career = 'coder' 
> var hobby = ['coding', 'writing']
> var finalString = `my name is ${name}, I work as a ${career} I love ${hobby[0]} and ${hobby[1]}`
> ```
>
> 字符串不仅更容易拼了，也更易读了，代码整体的质量都变高了。这就是模板字符串的第一个优势——允许用${}的方式嵌入变量。但这还不是问题的关键，模板字符串的关键优势有两个：
>
> - 在模板字符串中，空格、缩进、换行都会被保留
> - 模板字符串完全支持“运算”式的表达式，可以在${}里完成一些计算
>
> 基于第一点，可以在模板字符串里无障碍地直接写 html 代码：
>
> ```javascript
> let list = `
> 	<ul>
> 		<li>列表项1</li>
> 		<li>列表项2</li>
> 	</ul>
> `;
> console.log(message); // 正确输出，不存在报错
> ```
>
> 基于第二点，可以把一些简单的计算和调用丢进 ${} 来做：
>
> ```javascript
> function add(a, b) {
>   const finalString = `${a} + ${b} = ${a+b}`
>   console.log(finalString)
> }
> add(1, 2) // 输出 '1 + 2 = 3'
> ```
>
> 除了模板语法外， ES6中还新增了一系列的字符串方法用于提升开发效率：
>
> （1）**存在性判定**：在过去，当判断一个字符/字符串是否在某字符串中时，只能用 indexOf > -1 来做。现在 ES6 提供了三个方法：includes、startsWith、endsWith，它们都会返回一个布尔值来告诉你是否存在。
>
> - **includes**：判断字符串与子串的包含关系：
>
> ```javascript
> const son = 'haha' 
> const father = 'xixi haha hehe'
> father.includes(son) // true
> ```
>
> - **startsWith**：判断字符串是否以某个/某串字符开头：
>
> ```javascript
> const father = 'xixi haha hehe'
> father.startsWith('haha') // false
> father.startsWith('xixi') // true
> ```
>
> - **endsWith**：判断字符串是否以某个/某串字符结尾：
>
> ```javascript
> const father = 'xixi haha hehe'
>   father.endsWith('hehe') // true
> ```
>
> （2）**自动重复**：可以使用 repeat 方法来使同一个字符串输出多次（被连续复制多次）：
>
> ```javascript
> const sourceCode = 'repeat for 3 times;'
> const repeated = sourceCode.repeat(3) 
> console.log(repeated) // repeat for 3 times;repeat for 3 times;repeat for 3 times;
> ```

### 12.模板字符串

> #### 基础用法
>
> ```js
> let message = `Hello World`;
> console.log(message);
> ```
>
> 如果你碰巧要在字符串中使用反撇号，你可以使用反斜杠转义：
>
> ```js
> let message = `Hello \` World`;
> console.log(message);
> ```
>
> 值得一提的是，在模板字符串中，空格、缩进、换行都会被保留：
>
> ```js
> let message = `
> 	<ul>
> 		<li>1</li>
> 		<li>2</li>
> 	</ul>
> `;
> console.log(message);
> ```
>
> #### 嵌入变量
>
> 模板字符串支持嵌入变量，只需要将变量名写在 ${} 之中，其实不止变量，任意的 JavaScript 表达式都是可以的：
>
> ```js
> let x = 1, y = 2;
> let message = `<ul><li>${x}</li><li>${x + y}</li></ul>`;
> console.log(message); // <ul><li>1</li><li>3</li></ul>
> ```
>
> 值得一提的是，模板字符串支持嵌套:
>
> ```js
> let arr = [{value: 1}, {value: 2}];
> let message = `
> 	<ul>
> 		${arr.map((item) => {
> 			return `
> 				<li>${item.value}</li>
> 			`
> 		})}
> 	</ul>
> `;
> console.log(message);
> ```
>
> 打印结果如下：
>
> ![string](https://p1-jj.byteimg.com/tos-cn-i-t2oaga2asx/gold-user-assets/2018/5/30/163af643fb17bd02~tplv-t2oaga2asx-zoom-in-crop-mark:1304:0:0:0.awebp)
>
> #### 标签模板
>
> 标签模板其实不是模板，而是函数调用的一种特殊形式。“标签”指的就是函数，紧跟在后面的模板字符串就是它的参数。
>
> ```javascript
> alert`hello`
> // 等同于
> alert(['hello'])
> ```
>
> 但是，如果模板字符里面有变量，就不是简单的调用了，而是会将模板字符串先处理成多个参数，再调用函数。
>
> 模板标签是一个非常重要的能力，模板字符串可以紧跟在一个函数名后面，该函数将被调用来处理这个模板字符串，举个例子：
>
> ```js
> let x = 'Hi', y = 'Kevin';
> var res = message`${x}, I am ${y}`;
> console.log(res);
> ```
>
> 我们可以自定义 message 函数来处理返回的字符串:
>
> ```js
> // literals 文字
> // 注意在这个例子中 literals 的第一个元素和最后一个元素都是空字符串
> function message(literals, value1, value2) {
> 	console.log(literals); // [ "", ", I am ", "" ]
> 	console.log(value1); // Hi
> 	console.log(value2); // Kevin
> }
> ```
>
> 我们利用这些参数将其拼合回去：
>
> ```js
> function message(literals, ...values) {
> 	let result = '';
> 
> 	for (let i = 0; i < values.length; i++) {
> 		result += literals[i];
> 		result += values[i];
> 	}
> 
> 	result += literals[literals.length - 1];
> 
> 	return result;
> }
> ```
>
> 你也可以这样写：
>
> ```js
> function message(literals, ...values) {
> 	let result = literals.reduce((prev, next, i) => {
> 	    let value = values[i - 1];
> 	    return prev + value + next;
> 	});
> 
> 	return result;
> }
> ```

## 三、JavaScript基础

### 1. new操作符的实现原理

> **new操作符的执行过程：**
>
> （1）首先创建了一个新的空对象obj
>
> （2）设置原型，将对象obj的原型设置为函数的 prototype 对象。
>
> （3）让函数的 this 指向这个对象，执行构造函数的代码（为这个新对象添加属性）(使用call改变this指向)
>
> （4）如果无返回值或者返回一个非对象值，则将 obj 返回作为新对象；如果返回值是一个新对象的话那么直接直接返回该对象。
>
> 具体实现：
>
> ```javascript
> function objectFactory() {
>   let newObject = null;
>   let constructor = Array.prototype.shift.call(arguments);
>   let result = null;
>   // 判断参数是否是一个函数
>   if (typeof constructor !== "function") {
>     console.error("type error");
>     return;
>   }
>   // 新建一个空对象，对象的原型为构造函数的 prototype 对象
>   newObject = Object.create(constructor.prototype);
>   // 将 this 指向新建对象，并执行函数
>   result = constructor.apply(newObject, arguments);
>   // 判断返回对象
>   let flag = result && (typeof result === "object" || typeof result === "function");
>   // 判断返回结果
>   return flag ? result : newObject;
> }
> // 使用方法
> objectFactory(构造函数, 初始化参数);
> ```

### 2. map和Object的区别

![image-20220309180524976](C:\Users\H\AppData\Roaming\Typora\typora-user-images\image-20220309180524976.png)

### 3. map和weakMap的区别

> **（1）Map** map本质上就是键值对的集合，但是普通的Object中的键值对中的键只能是字符串。而ES6提供的Map数据结构类似于对象，但是它的键不限制范围，可以是任意类型，是一种更加完善的Hash结构。如果Map的键是一个原始数据类型，只要两个键严格相同，就视为是同一个键。
>
> 实际上Map是一个数组，它的每一个数据也都是一个数组，其形式如下：
>
> ```javascript
> const map = [
>      ["name","张三"],
>      ["age",18],
> ]
> ```
>
> Map数据结构有以下操作方法：
>
> - **size**： `map.size` 返回Map结构的成员总数。
> - **set(key,value)**：设置键名key对应的键值value，然后返回整个Map结构，如果key已经有值，则键值会被更新，否则就新生成该键。（因为返回的是当前Map对象，所以可以链式调用）
> - **get(key)**：该方法读取key对应的键值，如果找不到key，返回undefined。
> - **has(key)**：该方法返回一个布尔值，表示某个键是否在当前Map对象中。
> - **delete(key)**：该方法删除某个键，返回true，如果删除失败，返回false。
> - **clear()**：map.clear()清除所有成员，没有返回值。
>
> Map结构原生提供是三个遍历器生成函数和一个遍历方法
>
> - keys()：返回键名的遍历器。
> - values()：返回键值的遍历器。
> - entries()：返回所有成员的遍历器。
> - forEach()：遍历Map的所有成员。
>
> ```javascript
> const map = new Map([
>      ["foo",1],
>      ["bar",2],
> ])
> for(let key of map.keys()){
>     console.log(key);  // foo bar
> }
> for(let value of map.values()){
>      console.log(value); // 1 2
> }
> for(let items of map.entries()){
>     console.log(items);  // ["foo",1]  ["bar",2]
> }
> map.forEach( (value,key,map) => {
>      console.log(key,value); // foo 1    bar 2
> })
> ```
>
> **（2）WeakMap** WeakMap 对象也是一组键值对的集合，其中的键是弱引用的。**其键必须是对象**，原始数据类型不能作为key值，而值可以是任意的。
>
> 该对象也有以下几种方法：
>
> - **set(key,value)**：设置键名key对应的键值value，然后返回整个Map结构，如果key已经有值，则键值会被更新，否则就新生成该键。（因为返回的是当前Map对象，所以可以链式调用）
> - **get(key)**：该方法读取key对应的键值，如果找不到key，返回undefined。
> - **has(key)**：该方法返回一个布尔值，表示某个键是否在当前Map对象中。
> - **delete(key)**：该方法删除某个键，返回true，如果删除失败，返回false。
>
> 其clear()方法已经被弃用，所以可以通过创建一个空的WeakMap并替换原对象来实现清除。
>
> WeakMap的设计目的在于，有时想在某个对象上面存放一些数据，但是这会形成对于这个对象的引用。一旦不再需要这两个对象，就必须手动删除这个引用，否则垃圾回收机制就不会释放对象占用的内存。
>
> 而WeakMap的**键名所引用的对象都是弱引用**，即垃圾回收机制不将该引用考虑在内。因此，只要所引用的对象的其他引用都被清除，垃圾回收机制就会释放该对象所占用的内存。也就是说，一旦不再需要，WeakMap 里面的**键名对象和所对应的键值对会自动消失，不用手动删除引用**。
>
> **总结：**
>
> - Map 数据结构。它类似于对象，也是键值对的集合，但是“键”的范围不限于字符串，各种类型的值（包括对象）都可以当作键。
> - WeakMap 结构与 Map 结构类似，也是用于生成键值对的集合。但是 WeakMap 只接受对象作为键名（ null 除外），不接受其他类型的值作为键名。而且 WeakMap 的键名所指向的对象，不计入垃圾回收机制。

### 4. JavaScript有哪些内置对象

> 全局的对象（ global objects ）或称标准内置对象，不要和 "全局对象（global object）" 混淆。这里说的全局的对象是说在 全局作用域里的对象。全局作用域中的其他对象可以由用户的脚本创建或由宿主程序提供。
>
> **标准内置对象的分类：**
>
> （1）值属性，这些全局属性返回一个简单值，这些值没有自己的属性和方法。例如 Infinity、NaN、undefined、null 字面量
>
> （2）函数属性，全局函数可以直接调用，不需要在调用时指定所属对象，执行结束后会将结果直接返回给调用者。例如 eval()、parseFloat()、parseInt() 等
>
> （3）基本对象，基本对象是定义或使用其他对象的基础。基本对象包括一般对象、函数对象和错误对象。例如 Object、Function、Boolean、Symbol、Error 等
>
> （4）数字和日期对象，用来表示数字、日期和执行数学计算的对象。例如 Number、Math、Date
>
> （5）字符串，用来表示和操作字符串的对象。例如 String、RegExp
>
> （6）可索引的集合对象，这些对象表示按照索引值来排序的数据集合，包括数组和类型数组，以及类数组结构的对象。例如 Array
>
> （7）使用键的集合对象，这些集合对象在存储数据时会使用到键，支持按照插入顺序来迭代元素。 例如 Map、Set、WeakMap、WeakSet
>
> （8）矢量集合，SIMD 矢量集合中的数据会被组织为一个数据序列。 例如 SIMD 等
>
> （9）结构化数据，这些对象用来表示和操作结构化的缓冲区数据，或使用 JSON 编码的数据。例如 JSON 等
>
> （10）控制抽象对象 例如 Promise、Generator 等
>
> （11）反射。例如 Reflect、Proxy
>
> （12）国际化，为了支持多语言处理而加入 ECMAScript 的对象。例如 Intl、Intl.Collator 等
>
> （13）WebAssembly
>
> （14）其他。例如 arguments
>
> **总结：** js 中的内置对象主要指的是在程序执行前存在全局作用域里的由 js 定义的一些全局值属性、函数和用来实例化其他对象的构造函数对象。一般经常用到的如全局变量值 NaN、undefined，全局函数如 parseInt()、parseFloat() 用来实例化对象的构造函数如 Date、Object 等，还有提供数学计算的单体内置对象如 Math 对象。

### 5. 常用的正则表达式有哪些？

```javascript
// （1）匹配 16 进制颜色值
var regex = /#([0-9a-fA-F]{6}|[0-9a-fA-F]{3})/g;

// （2）匹配日期，如 yyyy-mm-dd 格式
var regex = /^[0-9]{4}-(0[1-9]|1[0-2])-(0[1-9]|[12][0-9]|3[01])$/;

// （3）匹配 qq 号
var regex = /^[1-9][0-9]{4,10}$/g;

// （4）手机号码正则
var regex = /^1[34578]\d{9}$/g;

// （5）用户名正则
var regex = /^[a-zA-Z\$][a-zA-Z0-9_\$]{4,16}$/;
```

### 6. 对JSON的理解

> JSON 是一种基于文本的轻量级的数据交换格式。它可以被任何的编程语言读取和作为数据格式来传递。
>
> 在项目开发中，使用 JSON 作为前后端数据交换的方式。在前端通过将一个符合 JSON 格式的数据结构序列化为 JSON 字符串，然后将它传递到后端，后端通过 JSON 格式的字符串解析后生成对应的数据结构，以此来实现前后端数据的一个传递。
>
> 因为 JSON 的语法是基于 js 的，因此很容易将 JSON 和 js 中的对象弄混，但是应该注意的是 JSON 和 js 中的对象不是一回事，JSON 中对象格式更加严格，比如说在 JSON 中属性值不能为函数，不能出现 NaN 这样的属性值等，因此大多数的 js 对象是不符合 JSON 对象的格式的。
>
> 在 js 中提供了两个函数来实现 js 数据结构和 JSON 格式的转换处理，
>
> - JSON.stringify 函数，通过传入一个符合 JSON 格式的数据结构，将其转换为一个 JSON 字符串。如果传入的数据结构不符合 JSON 格式，那么在序列化的时候会对这些值进行对应的特殊处理，使其符合规范。在前端向后端发送数据时，可以调用这个函数将数据对象转化为 JSON 格式的字符串。
> - JSON.parse() 函数，这个函数用来将 JSON 格式的字符串转换为一个 js 数据结构，如果传入的字符串不是标准的 JSON 格式的字符串的话，将会抛出错误。当从后端接收到 JSON 格式的字符串时，可以通过这个方法来将其解析为一个 js 数据结构，以此来进行数据的访问。

### 7. JavaScript脚本延迟加载的方式有哪些？

> 延迟加载就是等页面加载完成之后再加载 JavaScript 文件。 js 延迟加载有助于提高页面加载速度。
>
> 一般有以下几种方式：
>
> - **defer 属性：** 给 js 脚本添加 defer 属性，这个属性会让脚本的加载与文档的解析同步解析，然后在文档解析完成后再执行这个脚本文件，这样的话就能使页面的渲染不被阻塞。多个设置了 defer 属性的脚本按规范来说最后是顺序执行的，但是在一些浏览器中可能不是这样。
> - **async 属性：** 给 js 脚本添加 async 属性，这个属性会使脚本异步加载，不会阻塞页面的解析过程，但是当脚本加载完成后立即执行 js 脚本，这个时候如果文档没有解析完成的话同样会阻塞。多个 async 属性的脚本的执行顺序是不可预测的，一般不会按照代码的顺序依次执行。
> - **动态创建 DOM 方式：** 动态创建 DOM 标签的方式，可以对文档的加载事件进行监听，当文档加载完成后再动态的创建 script 标签来引入 js 脚本。
> - **使用 setTimeout 延迟方法：** 设置一个定时器来延迟加载js脚本文件
> - **让 JS 最后加载：** 将 js 脚本放在文档的底部，来使 js 脚本尽可能的在最后来加载执行。

### 8. JavaScript 类数组对象的定义？

> 一个拥有 length 属性和若干索引属性的对象就可以被称为类数组对象，类数组对象和数组类似，但是不能调用数组的方法。常见的类数组对象有 arguments 和 DOM 方法的返回结果，还有一个函数也可以被看作是类数组对象，因为它含有 length 属性值，代表可接收的参数个数。
>
> 常见的类数组转换为数组的方法有这样几种：
>
> （1）通过 call 调用数组的 slice 方法来实现转换
>
> ```javascript
> Array.prototype.slice.call(arrayLike);
> ```
>
> （2）通过 call 调用数组的 splice 方法来实现转换
>
> ```javascript
> Array.prototype.splice.call(arrayLike, 0);
> ```
>
> （3）通过 apply 调用数组的 concat 方法来实现转换
>
> ```javascript
> Array.prototype.concat.apply([], arrayLike);
> ```
>
> （4）通过 Array.from 方法来实现转换
>
> ```javascript
> Array.from(arrayLike);
> ```

### 9. 数组有哪些原生方法？

> * 数组和字符串的转换方法：toString()、toLocalString()、join() 其中 join() 方法可以指定转换为字符串时的分隔符。
>
> * 数组尾部操作的方法 pop() 和 push()，push 方法可以传入多个参数。
>
> * 数组首部操作的方法 shift() 和 unshift() 重排序的方法 reverse() 和 sort()，sort() 方法可以传入一个函数来进行比较，传入前后两个值，如果返回值为正数，则交换两个参数的位置。
>
> * 数组连接的方法 concat() ，返回的是拼接好的数组，不影响原数组。
>
> * 数组截取办法 slice()，用于截取数组中的一部分返回，不影响原数组。
>
> * 数组插入方法 splice()，影响原数组查找特定项的索引的方法，indexOf() 和 lastIndexOf() 迭代方法 every()、some()、filter()、map() 和 forEach() 方法
>
> * 数组归并方法 reduce() 和 reduceRight() 方法

>数组有哪些方法会改变原数组，哪些不会
>
>改变原数组的方法：push，pop，shift，unshift，join，reverse，splice，sort
>
>不改变原数组的方法：concat，slice，filter，map

### 10. 为什么函数的 arguments 参数是类数组而不是数组？如何遍历类数组?

> `arguments`是一个对象，它的属性是从 0 开始依次递增的数字，还有`callee`和`length`等属性，与数组相似；但是它却没有数组常见的方法属性，如`forEach`, `reduce`等，所以叫它们类数组。
>
> 要遍历类数组，有三个方法：
>
> （1）将数组的方法应用到类数组上，这时候就可以使用`call`和`apply`方法，如：
>
> ```javascript
> function foo(){ 
>   Array.prototype.forEach.call(arguments, a => console.log(a))
> }
> ```
>
> （2）使用Array.from方法将类数组转化成数组：‌
>
> ```javascript
> function foo(){ 
>   const arrArgs = Array.from(arguments) 
>   arrArgs.forEach(a => console.log(a))
> }
> ```
>
> （3）使用展开运算符将类数组转化成数组
>
> ```javascript
> function foo(){ 
>     const arrArgs = [...arguments] 
>     arrArgs.forEach(a => console.log(a)) 
> }
> ```

### 11. 什么是 DOM 和 BOM？

> * DOM 指的是文档对象模型，它指的是把文档当做一个对象，这个对象主要定义了处理网页内容的方法和接口。
>
> * BOM 指的是浏览器对象模型，它指的是把浏览器当做一个对象来对待，这个对象主要定义了与浏览器进行交互的法和接口。BOM的核心是 window，而 window 对象具有双重角色，它既是通过 js 访问浏览器窗口的一个接口，又是一个 Global（全局）对象。这意味着在网页中定义的任何对象，变量和函数，都作为全局对象的一个属性或者方法存在。window 对象含有 location 对象、navigator 对象、screen 对象等子对象，并且 DOM 的最根本的对象 document 对象也是 BOM 的 window 对象的子对象。

### 12. 对AJAX的理解，实现一个AJAX请求

> AJAX是 Asynchronous JavaScript and XML 的缩写，指的是通过 JavaScript 的 异步通信，从服务器获取 XML 文档从中提取数据，再更新当前网页的对应部分，而不用刷新整个网页。
>
> 创建AJAX请求的步骤：
>
> - **创建一个 XMLHttpRequest 对象。**
> - 在这个对象上**使用 open 方法创建一个 HTTP 请求**，open 方法所需要的参数是请求的方法、请求的地址、是否异步和用户的认证信息。
> - 在发起请求前，可以为这个对象**添加一些信息和监听函数**。比如说可以通过 setRequestHeader 方法来为请求添加头信息。还可以为这个对象添加一个状态监听函数。一个 XMLHttpRequest 对象一共有 5 个状态，当它的状态变化时会触发onreadystatechange 事件，可以通过设置监听函数，来处理请求成功后的结果。当对象的 readyState 变为 4 的时候，代表服务器返回的数据接收完成，这个时候可以通过判断请求的状态，如果状态是 2xx 或者 304 的话则代表返回正常。这个时候就可以通过 response 中的数据来对页面进行更新了。
> - 当对象的属性和监听函数设置完成后，最后调**用 send 方法来向服务器发起请求**，可以传入参数作为发送的数据体。
>
> ```javascript
> const SERVER_URL = "/server";
> let xhr = new XMLHttpRequest();
> // 创建 Http 请求
> xhr.open("GET", url, true);
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
> 使用Promise封装AJAX：
>
> ```javascript
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

### 17. JavaScript为什么要进行变量提升，它导致了什么问题？

> 变量提升的表现是，无论在函数中何处位置声明的变量，好像都被提升到了函数的首部，可以在变量声明前访问到而不会报错。
>
> 造成变量声明提升的**本质原因**是 js 引擎在代码执行前有一个解析的过程，创建了执行上下文，初始化了一些代码执行时需要用到的对象。当访问一个变量时，会到当前执行上下文中的作用域链中去查找，而作用域链的首端指向的是当前执行上下文的变量对象，这个变量对象是执行上下文的一个属性，它包含了函数的形参、所有的函数和变量声明，这个对象的是在代码解析的时候创建的。
>
> 首先要知道，JS在拿到一个变量或者一个函数的时候，会有两步操作，即解析和执行。
>
> - 在解析阶段
>
>   ，JS会检查语法，并对函数进行预编译。解析的时候会先创建一个全局执行上下文环境，先把代码中即将执行的变量、函数声明都拿出来，变量先赋值为undefined，函数先声明好可使用。在一个函数执行之前，也会创建一个函数执行上下文环境，跟全局执行上下文类似，不过函数执行上下文会多出this、arguments和函数的参数。
>
>   - 全局上下文：变量定义，函数声明
>   - 函数上下文：变量定义，函数声明，this，arguments
>
> - **在执行阶段**，就是按照代码的顺序依次执行。
>
> 那为什么会进行变量提升呢？主要有以下两个原因：
>
> - 提高性能
> - 容错性更好
>
> **（1）提高性能** 在JS代码执行之前，会进行语法检查和预编译，并且这一操作只进行一次。这么做就是为了提高性能，如果没有这一步，那么每次执行代码前都必须重新解析一遍该变量（函数），而这是没有必要的，因为变量（函数）的代码并不会改变，解析一遍就够了。
>
> 在解析的过程中，还会为函数生成预编译代码。在预编译时，会统计声明了哪些变量、创建了哪些函数，并对函数的代码进行压缩，去除注释、不必要的空白等。这样做的好处就是每次执行函数时都可以直接为该函数分配栈空间（不需要再解析一遍去获取代码中声明了哪些变量，创建了哪些函数），并且因为代码压缩的原因，代码执行也更快了。
>
> **（2）容错性更好**
>
> 变量提升可以在一定程度上提高JS的容错性，看下面的代码：
>
> ```javascript
> a = 1;var a;console.log(a);
> ```
>
> 如果没有变量提升，这两行代码就会报错，但是因为有了变量提升，这段代码就可以正常执行。
>
> 虽然，在可以开发过程中，可以完全避免这样写，但是有时代码很复杂的时候。可能因为疏忽而先使用后定义了，这样也不会影响正常使用。由于变量提升的存在，而会正常运行。
>
> **总结：**
>
> - 解析和预编译过程中的声明提升可以提高性能，让函数可以在执行时预先为变量分配栈空间
> - 声明提升还可以提高JS代码的容错性，使一些不规范的代码也可以正常执行
>
> 变量提升虽然有一些优点，但是他也会造成一定的问题，在ES6中提出了let、const来定义变量，它们就没有变量提升的机制。下面看一下变量提升可能会导致的问题：
>
> ```javascript
> var tmp = new Date();
> 
> function fn(){
> 	console.log(tmp);
> 	if(false){
> 		var tmp = 'hello world';
> 	}
> }
> 
> fn();  // undefined
> ```
>
> 在这个函数中，原本是要打印出外层的tmp变量，但是因为变量提升的问题，内层定义的tmp被提到函数内部的最顶部，相当于覆盖了外层的tmp，所以打印结果为undefined。
>
> ```javascript
> var tmp = 'hello world';
> 
> for (var i = 0; i < tmp.length; i++) {
> 	console.log(tmp[i]);
> }
> 
> console.log(i); // 11
> ```
>
> 由于遍历时定义的i会变量提升成为一个全局变量，在函数结束之后不会被销毁，所以打印出来11。

### 18. 什么是尾调用，使用尾调用有什么好处？

> 尾调用指的是函数的最后一步调用另一个函数。代码执行是基于执行栈的，所以当在一个函数里调用另一个函数时，会保留当前的执行上下文，然后再新建另外一个执行上下文加入栈中。使用尾调用的话，因为已经是函数的最后一步，所以这时可以不必再保留当前的执行上下文，从而节省了内存，这就是尾调用优化。但是 ES6 的尾调用优化只在严格模式下开启，正常模式是无效的。

### 19. **ES6**模块与**CommonJS**模块有什么异同？

> ES6 Module和CommonJS模块的区别：
>
> - CommonJS是对模块的浅拷⻉，ES6 Module是对模块的引⽤，即ES6 Module只存只读，不能改变其值，也就是指针指向不能变，类似const；
> - import的接⼝是read-only（只读状态），不能修改其变量值。 即不能修改其变量的指针指向，但可以改变变量内部指针指向，可以对commonJS对重新赋值（改变指针指向），但是对ES6 Module赋值会编译报错。
>
> ES6 Module和CommonJS模块的共同点：
>
> - CommonJS和ES6 Module都可以对引⼊的对象进⾏赋值，即对对象内部属性的值进⾏改变。

### 20. 常见的DOM操作有哪些

> #### 1）DOM 节点的获取
>
> DOM 节点的获取的API及使用：
>
> ```javascript
> getElementById // 按照 id 查询
> getElementsByTagName // 按照标签名查询
> getElementsByClassName // 按照类名查询
> querySelectorAll // 按照 css 选择器查询
> 
> // 按照 id 查询
> var imooc = document.getElementById('imooc') // 查询到 id 为 imooc 的元素
> // 按照标签名查询
> var pList = document.getElementsByTagName('p')  // 查询到标签为 p 的集合
> console.log(divList.length)
> console.log(divList[0])
> // 按照类名查询
> var moocList = document.getElementsByClassName('mooc') // 查询到类名为 mooc 的集合
> // 按照 css 选择器查询
> var pList = document.querySelectorAll('.mooc') // 查询到类名为 mooc 的集合
> ```
>
> #### 2）DOM 节点的创建
>
> **创建一个新节点，并把它添加到指定节点的后面。** 已知的 HTML 结构如下：
>
> ```html
> <html>
>   <head>
>     <title>DEMO</title>
>   </head>
>   <body>
>     <div id="container"> 
>       <h1 id="title">我是标题</h1>
>     </div>   
>   </body>
> </html>
> ```
>
> 要求添加一个有内容的 span 节点到 id 为 title 的节点后面，做法就是：
>
> ```javascript
> // 首先获取父节点
> var container = document.getElementById('container')
> // 创建新节点
> var targetSpan = document.createElement('span')
> // 设置 span 节点的内容
> targetSpan.innerHTML = 'hello world'
> // 把新创建的元素塞进父节点里去
> container.appendChild(targetSpan)
> ```
>
> #### 3）DOM 节点的删除
>
> **删除指定的 DOM 节点，** 已知的 HTML 结构如下：
>
> ```javascript
> <html>
>   <head>
>     <title>DEMO</title>
>   </head>
>   <body>
>     <div id="container"> 
>       <h1 id="title">我是标题</h1>
>     </div>   
>   </body>
> </html>
> ```
>
> 需要删除 id 为 title 的元素，做法是：
>
> ```javascript
> // 获取目标元素的父元素
> var container = document.getElementById('container')
> // 获取目标元素
> var targetNode = document.getElementById('title')
> // 删除目标元素
> container.removeChild(targetNode)
> ```
>
> 或者通过子节点数组来完成删除：
>
> ```javascript
> // 获取目标元素的父元素var container = document.getElementById('container')// 获取目标元素var targetNode = container.childNodes[1]// 删除目标元素container.removeChild(targetNode)
> ```
>
> #### 4）修改 DOM 元素
>
> 修改 DOM 元素这个动作可以分很多维度，比如说移动 DOM 元素的位置，修改 DOM 元素的属性等。
>
> **将指定的两个 DOM 元素交换位置，** 已知的 HTML 结构如下：
>
> ```javascript
> <html>
>   <head>
>     <title>DEMO</title>
>   </head>
>   <body>
>     <div id="container"> 
>       <h1 id="title">我是标题</h1>
>       <p id="content">我是内容</p>
>     </div>   
>   </body>
> </html>
> ```
>
> 现在需要调换 title 和 content 的位置，可以考虑 insertBefore 或者 appendChild：
>
> ```javascript
> // 获取父元素
> var container = document.getElementById('container') 
> 
> // 获取两个需要被交换的元素
> var title = document.getElementById('title')
> var content = document.getElementById('content')
> // 交换两个元素，把 content 置于 title 前面
> container.insertBefore(content, title)
> ```

### 21. use strict是什么意思 ? 使用它区别是什么？

> use strict 是一种 ECMAscript5 添加的（严格模式）运行模式，这种模式使得 Javascript 在更严格的条件下运行。设立严格模式的目的如下：
>
> - 消除 Javascript 语法的不合理、不严谨之处，减少怪异行为;
> - 消除代码运行的不安全之处，保证代码运行的安全；
> - 提高编译器效率，增加运行速度；
> - 为未来新版本的 Javascript 做好铺垫。
>
> 区别：
>
> - 禁止使用 with 语句。
> - 禁止 this 关键字指向全局对象。
> - 对象不能有重名的属性。

### 22. 如何判断一个对象是否属于某个类？

> * 第一种方式，使用 instanceof 运算符来判断构造函数的 prototype 属性是否出现在对象的原型链中的任何位置。
>
> * 第二种方式，通过对象的 constructor 属性来判断，对象的 constructor 属性指向该对象的构造函数，但是这种方式不是很安全，因为 constructor 属性可以被改写。
>
> * 第三种方式，如果需要判断的是某个内置的引用类型的话，可以使用 Object.prototype.toString() 方法来打印对象的[[Class]] 属性来进行判断。

### 23. for...in和for...of的区别

> for…of 是ES6新增的遍历方式，允许遍历一个含有iterator接口的数据结构（数组、对象等）并且返回各项的值，和ES3中的for…in的区别如下
>
> - for…of 遍历获取的是对象的键值，for…in 获取的是对象的键名；
> - for… in 会遍历对象的整个原型链，性能非常差不推荐使用，而 for … of 只遍历当前对象不会遍历原型链；
> - 对于数组的遍历，for…in 会返回数组中所有可枚举的属性(包括原型链上可枚举的属性)，for…of 只返回数组的下标对应的属性值；
>
> **总结：** for...in 循环主要是为了遍历对象而生，不适用于遍历数组；for...of 循环可以用来遍历数组、类数组对象，字符串、Set、Map 以及 Generator 对象。

### 24. 如何使用for...of遍历对象

> for…of是作为ES6新增的遍历方式，允许遍历一个含有iterator接口的数据结构（数组、对象等）并且返回各项的值，普通的对象用for..of遍历是会报错的。
>
> 如果需要遍历的对象是类数组对象，用Array.from转成数组即可。
>
> ```javascript
> var obj = {
>     0:'one',
>     1:'two',
>     length: 2
> };
> obj = Array.from(obj);
> for(var k of obj){
>     console.log(k)
> }
> ```
>
> 如果不是类数组对象，就给对象添加一个[Symbol.iterator]属性，并指向一个迭代器即可。
>
> ```javascript
> //方法一：
> x={a: 2, b: 1}
> for(let i of Object.keys(x)){console.log(i)}
> a
> b
> for(let i of Object.keys(x)){console.log(x[i])}
> 2
> 1
> // 方法二
> var obj = {
>     a:1,
>     b:2,
>     c:3
> };
> obj[Symbol.iterator] = function*(){
>     var keys = Object.keys(obj);
>     for(var k of keys){
>         yield [k,obj[k]]
>     }
> };
> 
> for(var [k,v] of obj){
>     console.log(k,v);
> }
> ```

### 27. ajax、axios、fetch的区别

> **（1）AJAX** Ajax 即“AsynchronousJavascriptAndXML”（异步 JavaScript 和 XML），是指一种创建交互式网页应用的网页开发技术。它是一种在无需重新加载整个网页的情况下，能够更新部分网页的技术。通过在后台与服务器进行少量数据交换，Ajax 可以使网页实现异步更新。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。传统的网页（不使用 Ajax）如果需要更新内容，必须重载整个网页页面。其缺点如下：
>
> - 本身是针对MVC编程，不符合前端MVVM的浪潮
> - 基于原生XHR开发，XHR本身的架构不清晰
> - 不符合关注分离（Separation of Concerns）的原则
> - 配置和调用方式非常混乱，而且基于事件的异步模型不友好。
>
> **（2）Fetch** fetch号称是AJAX的替代品，是在ES6出现的，使用了ES6中的promise对象。Fetch是基于promise设计的。Fetch的代码结构比起ajax简单多。**fetch不是ajax的进一步封装，而是原生js，没有使用XMLHttpRequest对象**。
>
> fetch的优点：
>
> - 语法简洁，更加语义化
> - 基于标准 Promise 实现，支持 async/await
> - 更加底层，提供的API丰富（request, response）
> - 脱离了XHR，是ES规范里新的实现方式
>
> fetch的缺点：
>
> - fetch只对网络请求报错，对400，500都当做成功的请求，服务器返回 400，500 错误码时并不会 reject，只有网络错误这些导致请求不能完成时，fetch 才会被 reject。
> - fetch默认不会带cookie，需要添加配置项： fetch(url, {credentials: 'include'})
> - fetch不支持abort，不支持超时控制，使用setTimeout及Promise.reject的实现的超时控制并不能阻止请求过程继续在后台运行，造成了流量的浪费
> - fetch没有办法原生监测请求的进度，而XHR可以
>
> **（3）Axios** Axios 是一种基于Promise封装的HTTP客户端，其特点如下：
>
> - 浏览器端发起XMLHttpRequests请求
> - node端发起http请求
> - 支持Promise API
> - 监听请求和返回
> - 对请求和返回进行转化
> - 取消请求
> - 自动转换json数据
> - 客户端支持抵御XSRF攻击

### 28. 数组的遍历方法有哪些

![image-20220309193435727](C:\Users\H\AppData\Roaming\Typora\typora-user-images\image-20220309193435727.png)

### 29. forEach和map方法有什么区别

> 这方法都是用来遍历数组的，两者区别如下：
>
> - forEach()方法会针对每一个元素执行提供的函数，对数据的操作会改变原数组，该方法没有返回值；
> - map()方法不会改变原数组的值，返回一个新数组，新数组中的值为原数组调用函数处理之后的；

### 30.EventLoop 事件循环

> ```
> JS`是单线程的，为了防止一个函数执行时间过长阻塞后面的代码，所以会先将同步代码压入执行栈中，依次执行，将异步代码推入异步队列，异步队列又分为宏任务队列和微任务队列，因为宏任务队列的执行时间较长，所以微任务队列要优先于宏任务队列。微任务队列的代表就是，`Promise.then`，`MutationObserver`，宏任务的话就是`setImmediate setTimeout setInterval
> ```
>
> JS运行的环境。一般为浏览器或者Node。 在浏览器环境中，有JS 引擎线程和渲染线程，且两个线程互斥。 Node环境中，只有JS 线程。 不同环境执行机制有差异，不同任务进入不同Event Queue队列。 当主程结束，先执行准备好微任务，然后再执行准备好的宏任务，一个轮询结束。
>
> #### **浏览器中的事件环（Event Loop)**
>
> 事件环的运行机制是，**先会执行栈中的内容，栈中的内容执行后执行微任务，微任务清空后再执行宏任务，**先取出一个宏任务，再去执行微任务，然后在取宏任务清微任务这样不停的循环。
>
> - eventLoop 是由JS的宿主环境（浏览器）来实现的；
>
> - 事件循环可以简单的描述为以下四个步骤:
>
>   1. 函数入栈，当Stack中执行到异步任务的时候，就将他丢给WebAPIs,接着执行同步任务,直到Stack为空；
>   2. 此期间WebAPIs完成这个事件，把回调函数放入队列中等待执行（微任务放到微任务队列，宏任务放到宏任务队列）
>   3. 执行栈为空时，Event Loop把微任务队列执行清空；
>   4. 微任务队列清空后，进入宏任务队列，取队列的第一项任务放入Stack(栈）中执行，执行完成后，查看微任务队列是否有任务，有的话，清空微任务队列。重复4，继续从宏任务中取任务执行，执行完成之后，继续清空微任务，如此反复循环，直至清空所有的任务。
>
>   <img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/342e581223d2471d9484fc48beb9f8e1~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp" alt="事件循环流程" style="zoom:50%;" />
>
> - 浏览器中的任务源(task):
>
>   - `宏任务(macrotask)`：
>      宿主环境提供的，比如浏览器
>      ajax、setTimeout、setInterval、setTmmediate(只兼容ie)、script、requestAnimationFrame、messageChannel、UI渲染、一些浏览器api
>   - `微任务(microtask)`：
>      语言本身提供的，比如promise.then
>      then、queueMicrotask(基于then)、mutationObserver(浏览器提供)、messageChannel 、mutationObersve
>
> 传送门 ☞ [# 宏任务和微任务](https://juejin.cn/post/7001881781125251086)
>
> #### **Node 环境中的事件环（Event Loop)**
>
> `Node`是基于V8引擎的运行在服务端的`JavaScript`运行环境，在处理高并发、I/O密集(文件操作、网络操作、数据库操作等)场景有明显的优势。虽然用到也是V8引擎，但由于服务目的和环境不同，导致了它的API与原生JS有些区别，其Event Loop还要处理一些I/O，比如新的网络连接等，所以Node的Event Loop(事件环机制)与浏览器的是不太一样。
>
> ![2020120317343116.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e362c1770f62428fbf3faabd99d2a64c~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp) 执行顺序如下：
>
> - `timers`: 计时器，执行setTimeout和setInterval的回调
> - `pending callbacks`: 执行延迟到下一个循环迭代的 I/O 回调
> - `idle, prepare`: 队列的移动，仅系统内部使用
> - `poll轮询`: 检索新的 I/O 事件;执行与 I/O 相关的回调。事实上除了其他几个阶段处理的事情，其他几乎所有的异步都在这个阶段处理。
> - `check`: 执行`setImmediate`回调，setImmediate在这里执行
> - `close callbacks`: 执行`close`事件的`callback`，一些关闭的回调函数，如：socket.on('close', ...)

> ```js
> console.log('script start')
> 
> async function async1() {
>   await async2()
>   console.log('async1 end')
> }
> async function async2() {
>   console.log('async2 end') 
> }
> async1()
> 
> setTimeout(function() {
>   console.log('setTimeout')
> }, 0)
> 
> new Promise(resolve => {
>   console.log('Promise')
>   resolve()
> })
>   .then(function() {
>     console.log('promise1')
>   })
>   .then(function() {
>     console.log('promise2')
>   })
> 
> console.log('script end')
> ```
>
> 这里需要先理解`async/await`。
>
> `async/await` 在底层转换成了 `promise` 和 `then` 回调函数。
>  也就是说，这是 `promise` 的语法糖。
>  每次我们使用 `await`, 解释器都创建一个 `promise` 对象，然后把剩下的 `async` 函数中的操作放到 `then` 回调函数中。
>  `async/await` 的实现，离不开 `Promise`。从字面意思来理解，`async` 是“异步”的简写，而 `await` 是 `async wait` 的简写可以认为是等待异步方法执行完成。
>
> * 首先，打印`script start`，调用`async1()`时，返回一个`Promise`，所以打印出来`async2 end`。
>
> * 每个 `await`，会新产生一个`promise`,但这个过程本身是异步的，所以该`await`后面不会立即调用。
>
> * 继续执行同步代码，打印`Promise`和`script end`，将`then`函数放入**微任务**队列中等待执行。
>
> * 同步执行完成之后，检查**微任务**队列是否为`null`，然后按照先入先出规则，依次执行。
>
> * 然后先执行打印`promise1`,此时`then`的回调函数返回`undefinde`，此时又有`then`的链式调用，又放入**微任务**队列中，再次打印`promise2`。
>
> * 再回到`await`的位置执行返回的 `Promise` 的 `resolve` 函数，这又会把 `resolve` 丢到微任务队列中，打印`async1 end`。
>
> * 当**微任务**队列为空时，执行宏任务,打印`setTimeout`。

### 31.简述MVVM

> **什么是MVVM？**
>
> `视图模型双向绑定`，是`Model-View-ViewModel`的缩写，也就是把`MVC`中的`Controller`演变成`ViewModel。Model`层代表数据模型，`View`代表UI组件，`ViewModel`是`View`和`Model`层的桥梁，数据会绑定到`viewModel`层并自动将数据渲染到页面中，视图变化的时候会通知`viewModel`层更新数据。以前是操作DOM结构更新视图，现在是`数据驱动视图`。
>
> **MVVM的优点：**
>
> 1.`低耦合`。视图（View）可以独立于Model变化和修改，一个Model可以绑定到不同的View上，当View变化的时候Model可以不变化，当Model变化的时候View也可以不变；
>  2.`可重用性`。你可以把一些视图逻辑放在一个Model里面，让很多View重用这段视图逻辑。
>  3.`独立开发`。开发人员可以专注于业务逻辑和数据的开发(ViewModel)，设计人员可以专注于页面设计。
>  4.`可测试`。

## 四、原型与原型链

### 1.原型继承

#### 1.1.[[[Prototpe\]]

> 在 JavaScript 中，对象有一个特殊的隐藏属性 `[[Prototype]]`，它**要么为 `null`，要么就是对另一个对象的引用**。该对象被称为“原型”
>
> 当我们从 `object` 中读取一个缺失的属性时，JavaScript 会自动从原型中获取该属性。在编程中，这被称为“原型继承”。
>
> 属性 `[[Prototype]]` 是内部的而且是隐藏的，但是这儿有很多设置它的方式。
>
> 其中之一就是使用特殊的名字 `__proto__`，就像这样：
>
> ```javascript
> let animal = {
>   eats: true
>   walk() {
>     alert("Animal walk");
>   }
> };
> let rabbit = {
>   jumps: true
> };
> 
> rabbit.__proto__ = animal; // 设置 rabbit.[[Prototype]] = animal
> // 现在这两个属性我们都能在 rabbit 中找到：
> alert( rabbit.eats ); // true (**)
> alert( rabbit.jumps ); // true
> rabbit.walk(); // Animal walk
> ```
>
> 现在，如果我们从 `rabbit` 中读取一个它没有的属性，JavaScript 会自动从 `animal` 中获取。
>
> 在这儿我们可以说 "`animal` 是 `rabbit` 的原型"，或者说 "`rabbit` 的原型是从 `animal` 继承而来的"。
>
> 原型链可以很长：
>
> ```js
> let longEar = {
>   earLength: 10,
>   __proto__: rabbit
> };
> // walk 是通过原型链获得的
> longEar.walk(); // Animal walk
> alert(longEar.jumps); // true（从 rabbit）
> ```
>
> 现在，如果我们从 `longEar` 中读取一些它不存在的内容，JavaScript 会先在 `rabbit` 中查找，然后在 `animal` 中查找。
>
> **这里只有两个限制**：
>
> 1. 引用不能形成闭环。如果我们试图在一个闭环中分配 `__proto__`，JavaScript 会抛出错误。
> 2. `__proto__` 的值可以是对象，也可以是 `null`。而其他的类型都会被忽略。
>
> ```js
> `__proto__ 是 [[Prototype]] 的因历史原因而留下来的 getter/setter
> __proto__ 属性有点过时了。它的存在是出于历史的原因，现代编程语言建议我们应该使用函数 Object.getPrototypeOf/Object.setPrototypeOf 来取代 __proto__ 去 get/set 原型。稍后我们将介绍这些函数。`
> ```

#### 1.2.写入不使用原型

> 原型仅用于读取属性。
>
> 对于写入/删除操作可以直接在对象上进行。
>
> 在下面的示例中，我们将为 `rabbit` 分配自己的 `walk`：

#### 1.3.this的值

> **无论在哪里找到方法：在一个对象还是在原型中。在一个方法调用中，`this` 始终是点符号 `.` 前面的对象。**

> - 1.第一种是函数调用模式，当一个函数不是一个对象的属性时，直接作为函数来调用时，this 指向全局对象。
>
> - 2.第二种是方法调用模式，如果一个函数作为一个对象的方法来调用时，this 指向这个对象。
>
> - 3.第三种是构造器调用模式，如果一个函数用 new 调用时，函数执行前会新创建一个对象，this 指向这个新创建的对象。
>
> - 4.第四种是 apply 、 call 和 bind 调用模式，这三个方法都可以显示的指定调用函数的 this 指向。其中 apply 方法接收两个参数：一个是 this 绑定的对象，一个是参数数组。call 方法接收的参数，第一个是 this 绑定的对象，后面的其余参数是传入函数执行的参数。也就是说，在使用 call() 方法时，传递给函数的参数必须逐个列举出来。bind 方法通过传入一个对象，返回一个 this 绑定了传入对象的新函数。这个函数的 this 指向**除了使用 new 时会被改变，其他情况下都不会改变**。

#### 1.4.for...in循环

> `for..in` 循环也会迭代继承的属性。
>
> ```js
> let animal = {
>   eats: true
> };
> 
> let rabbit = {
>   jumps: true,
>   __proto__: animal
> };
> 
> // Object.keys 只返回自己的 key
> alert(Object.keys(rabbit)); // jumps
> 
> // for..in 会遍历自己以及继承的键
> for(let prop in rabbit) alert(prop); // jumps，然后是 eats
> ```
>
> 如果这不是我们想要的，并且我们想排除继承的属性，那么这儿有一个内建方法 [obj.hasOwnProperty(key)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/hasOwnProperty)：如果 `obj` 具有自己的（非继承的）名为 `key` 的属性，则返回 `true`。
>
> 因此，我们可以过滤掉继承的属性（或对它们进行其他操作）：
>
> ```js
> for(let prop in rabbit) {
>   let isOwn = rabbit.hasOwnProperty(prop);
> 
>   if (isOwn) {
>     alert(`Our: ${prop}`); // Our: jumps
>   } else {
>     alert(`Inherited: ${prop}`); // Inherited: eats
>   }
> }
> ```

### 2.F.prototype

> 我们还记得，可以使用诸如 `new F()` 这样的构造函数来创建一个新对象。
>
> 如果 `F.prototype` 是一个对象，那么 `new` 操作符会使用它为新对象设置 `[[Prototype]]`。
>
> 请注意，这里的 `F.prototype` 指的是 `F` 的一个名为 `"prototype"` 的常规属性。这听起来与“原型”这个术语很类似，但这里我们实际上指的是具有该名字的**常规属性**。
>
> ```js
> let animal = {
>   eats: true
> };
> 
> function Rabbit(name) {
>   this.name = name;
> }
> 
> Rabbit.prototype = animal;
> 
> let rabbit = new Rabbit("White Rabbit"); //  rabbit.__proto__ == animal
> 
> alert( rabbit.eats ); // true
> ```
>
> 设置 `Rabbit.prototype = animal` 的字面意思是：“当创建了一个 `new Rabbit` 时，把它的 `[[Prototype]]` 赋值为 `animal`”。
>
> 当创建构造函数的一个实例后，就将这个实例的原型赋值为animal
>
> ![image-20220309221053407](C:\Users\H\AppData\Roaming\Typora\typora-user-images\image-20220309221053407.png)

> 在上图中，`"prototype"` 是一个水平箭头，表示一个常规属性，`[[Prototype]]` 是垂直的，表示 `rabbit` 继承自 `animal`。
>
> `F.prototype` 属性仅在 `new F` 被调用时使用，它为新对象的 `[[Prototype]]` 赋值。
>
> 如果在创建之后，`F.prototype` 属性有了变化（`F.prototype = <another object>`），那么通过 `new F` 创建的新对象也将随之拥有新的对象作为 `[[Prototype]]`，但已经存在的对象将保持旧有的值。

#### 2.1.默认的 F.prototype，构造器属性

>每个函数都有 `"prototype"` 属性，即使我们没有提供它。
>
>默认的 `"prototype"` 是一个只有属性 `constructor` 的对象，属性 `constructor` 指向函数自身。
>
>像这样：
>
>```javascript
>function Rabbit() {}
>
>/* default prototype
>Rabbit.prototype = { constructor: Rabbit };
>*/
>
>alert( Rabbit.prototype.constructor == Rabbit ); // true
>```
>
>通常，如果我们什么都不做，`constructor` 属性可以通过 `[[Prototype]]` 给所有 rabbits 使用：
>
>```js
>let rabbit = new Rabbit(); // inherits from {constructor: Rabbit}
>
>alert(rabbit.constructor == Rabbit); // true (from prototype)
>```
>
>关于 `"constructor"` 最重要的是……
>
>**……JavaScript 自身并不能确保正确的 `"constructor"` 函数值。**
>
>是的，它存在于函数的默认 `"prototype"` 中，但仅此而已。之后会发生什么 —— 完全取决于我们。
>
>特别是，如果我们将整个默认 prototype 替换掉，那么其中就不会有 `"constructor"` 了。
>
>例如：
>
>```javascript
>function Rabbit() {}
>Rabbit.prototype = {
>  jumps: true
>};
>
>let rabbit = new Rabbit();
>alert(rabbit.constructor === Rabbit); // false
>```
>
>因此，为了确保正确的 `"constructor"`，我们可以选择添加/删除属性到默认 `"prototype"`，而不是将其整个覆盖：
>
>```javascript
>function Rabbit() {}
>
>// 不要将 Rabbit.prototype 整个覆盖
>// 可以向其中添加内容
>Rabbit.prototype.jumps = true
>// 默认的 Rabbit.prototype.constructor 被保留了下来
>```
>
>或者，也可以手动重新创建 `constructor` 属性：
>
>```javascript
>Rabbit.prototype = {
>  jumps: true,
>  constructor: Rabbit
>};
>
>// 这样的 constructor 也是正确的，因为我们手动添加了它
>```

### 3.原生的原型

> `"prototype"` 属性在 JavaScript 自身的核心部分中被广泛地应用。所有的内建构造函数都用到了它。
>
> 首先，我们将看看原生原型的详细信息，然后学习如何使用它为内建对象添加新功能。

#### 3.1.Object.prototype

> 假如我们输出一个空对象：
>
> ```javascript
> let obj = {};
> alert( obj ); // "[object Object]" ?
> ```
>
> 生成字符串 `"[object Object]"` 的代码在哪里？那就是一个内建的 `toString` 方法，但是它在哪里呢？`obj` 是空的！
>
> ……然而简短的表达式 `obj = {}` 和 `obj = new Object()` 是一个意思，其中 `Object` 就是一个内建的对象构造函数，其自身的 `prototype` 指向一个带有 `toString` 和其他方法的一个巨大的对象。
>
> ![image-20220309223000659](C:\Users\H\AppData\Roaming\Typora\typora-user-images\image-20220309223000659.png)
>
> 当 `new Object()` 被调用（或一个字面量对象 `{...}` 被创建），按照前面章节中我们学习过的规则，这个对象的 `[[Prototype]]` 属性被设置为 `Object.prototype`：
>
> ![image-20220309223043704](C:\Users\H\AppData\Roaming\Typora\typora-user-images\image-20220309223043704.png)
>
> 所以，之后当 `obj.toString()` 被调用时，这个方法是从 `Object.prototype` 中获取的。
>
> 我们可以这样验证它：
>
> ```javascript
> let obj = {};
> 
> alert(obj.__proto__ === Object.prototype); // true
> 
> alert(obj.toString === obj.__proto__.toString); //true
> alert(obj.toString === Object.prototype.toString); //true
> ```
>
> 请注意在 `Object.prototype` 上方的链中没有更多的 `[[Prototype]]`：
>
> ```javascript
> alert(Object.prototype.__proto__); // null
> ```

#### 3.2.其他的内建原型

> 其他内建对象，像 `Array`、`Date`、`Function` 及其他，都在 prototype 上挂载了方法。
>
> 例如，当我们创建一个数组 `[1, 2, 3]`，在内部会默认使用 `new Array()` 构造器。因此 `Array.prototype` 变成了这个数组的 prototype，并为这个数组提供数组的操作方法。这样内存的存储效率是很高的。
>
> 按照规范，所有的内建原型顶端都是 `Object.prototype`。这就是为什么有人说“一切都从对象继承而来”。
>
> ![image-20220309223559000](C:\Users\H\AppData\Roaming\Typora\typora-user-images\image-20220309223559000.png)

#### 3.3.基本数据类型

> 最复杂的事情发生在字符串、数字和布尔值上。
>
> 正如我们记忆中的那样，它们并不是对象。但是如果我们试图访问它们的属性，那么临时包装器对象将会通过内建的构造器 `String`、`Number` 和 `Boolean` 被创建。它们提供给我们操作字符串、数字和布尔值的方法然后消失。
>
> 这些对象对我们来说是无形地创建出来的。大多数引擎都会对其进行优化，但是规范中描述的就是通过这种方式。这些对象的方法也驻留在它们的 prototype 中，可以通过 `String.prototype`、`Number.prototype` 和 `Boolean.prototype` 进行获取。

#### 3.4.更改原生原型

> 原生的原型是可以被修改的。例如，我们向 `String.prototype` 中添加一个方法，这个方法将对所有的字符串都是可用的：
>
> ```javascript
> String.prototype.show = function() {
>   alert(this);
> };
> 
> "BOOM!".show(); // BOOM!
> ```
>
> 在开发的过程中，我们可能会想要一些新的内建方法，并且想把它们添加到原生原型中。但这通常是一个很不好的想法。

> **在现代编程中，只有一种情况下允许修改原生原型。那就是 polyfilling。**
>
> Polyfilling 是一个术语，表示某个方法在 JavaScript 规范中已存在，但是特定的 JavaScript 引擎尚不支持该方法，那么我们可以通过手动实现它，并用以填充内建原型。
>
> 例如：
>
> ```javascript
> if (!String.prototype.repeat) { // 如果这儿没有这个方法
>   // 那就在 prototype 中添加它
> 
>   String.prototype.repeat = function(n) {
>     // 重复传入的字符串 n 次
> 
>     // 实际上，实现代码比这个要复杂一些（完整的方法可以在规范中找到）
>     // 但即使是不够完美的 polyfill 也常常被认为是足够好的
>     return new Array(n + 1).join(this);
>   };
> }
> 
> alert( "La".repeat(3) ); // LaLaLa
> ```

#### 3.5.从原型中借用

> 一些原生原型的方法通常会被借用。
>
> 例如，如果我们要创建类数组对象，则可能需要向其中复制一些 `Array` 方法。
>
> 例如：
>
> ```javascript
> let obj = {
>   0: "Hello",
>   1: "world!",
>   length: 2,
> };
> 
> obj.join = Array.prototype.join;
> 
> alert( obj.join(',') ); // Hello,world!
> ```
>
> 上面这段代码有效，是因为内建的方法 `join` 的内部算法只关心正确的索引和 `length` 属性。它不会检查这个对象是否是真正的数组。许多内建方法就是这样。
>
> 另一种方式是通过将 `obj.__proto__` 设置为 `Array.prototype`，这样 `Array` 中的所有方法都自动地可以在 `obj` 中使用了。
>
> 但是如果 `obj` 已经从另一个对象进行了继承，那么这种方法就不可行了（译注：因为这样会覆盖掉已有的继承。此处 `obj` 其实已经从 `Object` 进行了继承，但是 `Array` 也继承自 `Object`，所以此处的方法借用不会影响 `obj` 对原有继承的继承，因为 `obj` 通过原型链依旧继承了 `Object`）。请记住，我们一次只能继承一个对象。
>
> 方法借用很灵活，它允许在需要时混合来自不同对象的方法。

### 4.原型方法，没有`__proto__`的对象

> 在这部分内容的第一章中，我们提到了设置原型的现代方法。
>
> `__proto__` 被认为是过时且不推荐使用的（deprecated），这里的不推荐使用是指 JavaScript 规范中规定，**proto** 必须仅在浏览器环境下才能得到支持。
>
> 现代的方法有：
>
> - Object.create(proto, [descriptors\])—— 利用给定的 `proto` 作为 `[[Prototype]]` 和可选的属性描述来创建一个空对象。
> - Object.getPrototypeOf(obj)—— 返回对象 `obj` 的 `[[Prototype]]`。
> - Object.setPrototypeOf(obj, proto)—— 将对象 `obj` 的 `[[Prototype]]` 设置为 `proto`。
>
> 应该使用这些方法来代替 `__proto__`。

> 例如：
>
> ```javascript
> let animal = {
>   eats: true
> };
> 
> // 创建一个以 animal 为原型的新对象
> let rabbit = Object.create(animal);
> 
> alert(rabbit.eats); // true
> 
> alert(Object.getPrototypeOf(rabbit) === animal); // true
> 
> Object.setPrototypeOf(rabbit, {}); // 将 rabbit 的原型修改为 {}
> ```

>`Object.create` 有一个可选的第二参数：属性描述器。我们可以在此处为新对象提供额外的属性，就像这样：
>
>```javascript
>let animal = {
>  eats: true
>};
>
>let rabbit = Object.create(animal, {
>  jumps: {
>    value: true
>  }
>});
>
>alert(rabbit.jumps); // true
>```

> 描述器的格式与 [属性标志和属性描述符](https://zh.javascript.info/property-descriptors) 一章中所讲的一样。
>
> 我们可以使用 `Object.create` 来实现比复制 `for..in` 循环中的属性更强大的对象克隆方式：
>
> ```javascript
> let clone = Object.create(Object.getPrototypeOf(obj), Object.getOwnPropertyDescriptors(obj));
> ```
>
> 此调用可以对 `obj` 进行真正准确地拷贝，包括所有的属性：可枚举和不可枚举的，数据属性和 setters/getters —— 包括所有内容，并带有正确的 `[[Prototype]]`。

#### 4.1.原型简史

> 如果我们数一下有多少种处理 `[[Prototype]]` 的方式，答案是有很多！很多种方法做的都是同一件事儿！
>
> 为什么会出现这种情况？
>
> 这是历史原因。
>
> - 构造函数的 `"prototype"` 属性自古以来就起作用。
> - 之后，在 2012 年，`Object.create` 出现在标准中。它提供了使用给定原型创建对象的能力。但没有提供 get/set 它的能力。因此，许多浏览器厂商实现了非标准的 `__proto__` 访问器，该访问器允许用户随时 get/set 原型。
> - 之后，在 2015 年，`Object.setPrototypeOf` 和 `Object.getPrototypeOf` 被加入到标准中，执行与 `__proto__` 相同的功能。由于 `__proto__` 实际上已经在所有地方都得到了实现，但它已过时，所以被加入到该标准的附件 B 中，即：在非浏览器环境下，它的支持是可选的。
>
> 目前为止，我们拥有了所有这些方式。
>
> 为什么将 `__proto__` 替换成函数 `getPrototypeOf/setPrototypeOf`？这是一个有趣的问题，需要我们理解为什么 `__proto__` 不好。继续阅读，你就会知道答案。

## 五、执行上下文/作用域链/闭包

### 1. 对闭包的理解

> 闭包是指内部函数总是可以访问其所在的外部函数中声明的变量和参数，即使在其外部函数被返回（寿命终结）了之后。

> - **闭包的概念**
>
>   函数执行时形成的私有上下文EC(FN)，正常情况下，代码执行完会出栈后释放;但是特殊情况下，如果当前私有上下文中的某个东西被上下文以外的事物占用了，则上下文不会出栈释放，从而形成不销毁的上下文。 函数执行函数执行过程中，会形成一个全新的私有上下文，可能会被释放，可能不会被释放，不论释放与否，他的作用是：
>
> （1）保护：划分一个独立的代码执行区域，在这个区域中有自己私有变量存储的空间，保护自己的私有变量不受外界干扰（操作自己的私有变量和外界没有关系）；
>
> （2）保存：如果当前上下文不被释放【只要上下文中的某个东西被外部占用即可】，则存储的这些私有变量也不会被释放，可以供其下级上下文中调取使用，相当于把一些值保存起来了；
>
> 我们把函数执行形成私有上下文，来保护和保存私有变量机制称为`闭包`。
>
> > 闭包是指有权访问另一个函数作用域中的变量的函数--《JavaScript高级程序设计》
>
> **稍全面的回答**： 在js中变量的作用域属于函数作用域, 在函数执行完后,作用域就会被清理,内存也会随之被回收,但是由于闭包函数是建立在函数内部的子函数, 由于其可访问上级作用域,即使上级函数执行完, 作用域也不会随之销毁, 这时的子函数(也就是闭包),便拥有了访问上级作用域中变量的权限,即使上级函数执行完后作用域内的值也不会被销毁。
>
> - **闭包的特性**：
>
>   - 1、内部函数可以访问定义他们外部函数的参数和变量。(作用域链的向上查找，把外围的作用域中的变量值存储在内存中而不是在函数调用完毕后销毁)设计私有的方法和变量，避免全局变量的污染。
>
>     1.1.闭包是密闭的容器，，类似于set、map容器，存储数据的
>
>     1.2.闭包是一个对象，存放数据的格式为 key-value 形式
>
>   - 2、函数嵌套函数
>
>   - 3、本质是将函数内部和外部连接起来。优点是可以读取函数内部的变量，让这些变量的值始终保存在内存中，不会在函数被调用之后自动清除
>
> - **闭包形成的条件**：
>
>   1. 函数的嵌套
>   2. 内部函数引用外部函数的局部变量，延长外部函数的变量生命周期
>
> - **闭包的用途**：
>
>   1. 模仿块级作用域
>   2. 保护外部函数的变量 能够访问函数定义时所在的词法作用域(阻止其被回收)
>   3. 封装私有化变量
>   4. 创建模块
>
> - **闭包应用场景**
>
>   闭包的两个场景，闭包的两大作用：`保存/保护`。 在开发中, 其实我们随处可见闭包的身影, 大部分前端JavaScript 代码都是“事件驱动”的,即一个事件绑定的回调方法; 发送ajax请求成功|失败的回调;setTimeout的延时回调;或者一个函数内部返回另一个匿名函数,这些都是闭包的应用。
>
> - **闭包的优点**：延长局部变量的生命周期
>
> - **闭包缺点**：会导致函数的变量一直保存在内存中，过多的闭包可能会导致内存泄漏

> **闭包是指有权访问另一个函数作用域中变量的函数**，创建闭包的最常见的方式就是在一个函数内创建另一个函数，创建的函数可以访问到当前函数的局部变量。
>
> 闭包有两个常用的用途；(保存、保护)
>
> - 闭包的第一个用途是使我们**在函数外部能够访问到函数内部的变量**。通过使用闭包，可以通过在外部调用闭包函数，从而在外部访问到函数内部的变量，可以使用这种方法来创建私有变量。
> - 闭包的另一个用途是**使已经运行结束的函数上下文中的变量对象继续留在内存中**，因为闭包函数保留了这个变量对象的引用，所以这个变量对象不会被回收。
>
> 比如，函数 A 内部有一个函数 B，函数 B 可以访问到函数 A 中的变量，那么函数 B 就是闭包。
>
> ```javascript
> function A() {
>   let a = 1
>   window.B = function () {
>       console.log(a)
>   }
> }
> A()
> B() // 1
> ```
>
> 在 JS 中，闭包存在的意义就是让我们可以间接访问函数内部的变量。
>
> #### 形成闭包的原因
> 
> > **内部的函数存在外部作用域的引用就会导致闭包**。从上面介绍的上级作用域的概念中其实就有闭包的例子 `return f`就是一个表现形式。
> 
> ```js
> var a = 0
> function foo(){
>    var b =14
>     function fo(){
>        console.log(a, b)
>     }
>    fo()
> }
> foo()
> ```
> 
> > 这里的子函数 `fo` 内存就存在外部作用域的引用 `a, b`，所以这就会产生闭包
> 
> #### 闭包变量存储的位置
>
> > 直接说明：**闭包中的变量存储的位置是堆内存。**
>
> - 假如闭包中的变量存储在栈内存中，那么栈的回收 会把处于栈顶的变量自动回收。所以闭包中的变量如果处于栈中那么变量被销毁后，闭包中的变量就没有了。所以闭包引用的变量是出于堆内存中的。
>
> #### 闭包的作用
> 
> - 保护函数的私有变量不受外部的干扰。形成不销毁的栈内存。
> - 保存，把一些函数内的值保存下来。闭包可以实现方法和属性的私有化

> ### 闭包经典使用场景
>
> - 1. `return` 回一个函数
>
> ```js
> var n = 10
> function fn(){
>     var n =20
>     function f() {
>        n++;
>        console.log(n)
>      }
>     return f
> }
> 
> var x = fn()
> x() // 21
> ```
>
> > 这里的 return `f`, `f()`就是一个闭包，存在上级作用域的引用。
>
> - 2. 函数作为参数
>
> ```js
> var a = '林一一'
> function foo(){
>     var a = 'foo'
>     function fo(){
>         console.log(a)
>     }
>     return fo
> }
> 
> function f(p){
>     var a = 'f'
>     p()
> }
> f(foo())
> /* 输出
> *   foo
> / 
> ```
>
> > 使用 return `fo` 返回回来，`fo()` 就是闭包，`f(foo())` 执行的参数就是函数 `fo`，因为 `fo() 中的 a`  的上级作用域就是函数`foo()`，所以输出就是`foo`
>
> - 3. IIFE（自执行函数）
>
> ```js
> var n = '林一一';
> (function p(){
>     console.log(n)
> })()
> /* 输出
> *   林一一
> / 
> ```
>
> > 同样也是产生了闭包`p()`，存在 `window`下的引用 `n`。
>
> - 4. 循环赋值
>
> ```js
> for(var i = 0; i<10; i++){
>   (function(j){
>        setTimeout(function(){
>         console.log(j)
>     }, 1000) 
>   })(i)
> }
> ```
>
> > 因为存在闭包的原因上面能依次输出1~10，闭包形成了10个互不干扰的私有作用域。将外层的自执行函数去掉后就不存在外部作用域的引用了，输出的结果就是连续的 10。为什么会连续输出10，因为 JS 是单线程的遇到异步的代码不会先执行(会入栈)，等到同步的代码执行完 `i++` 到 10时，异步代码才开始执行此时的 `i=10` 输出的都是 10。
>
> - 5. 使用回调函数就是在使用闭包
>
> ```js
> window.name = '林一一'
> setTimeout(function timeHandler(){
>   console.log(window.name);
> }, 100)
> ```
>
> - 6. 节流防抖
>
> ```js
> // 节流
> function throttle(fn, timeout) {
>     let timer = null
>     return function (...arg) {
>         if(timer) return
>         timer = setTimeout(() => {
>             fn.apply(this, arg)
>             timer = null
>         }, timeout)
>     }
> }
> 
> // 防抖
> function debounce(fn, timeout){
>     let timer = null
>     return function(...arg){
>         clearTimeout(timer)
>         timer = setTimeout(() => {
>             fn.apply(this, arg)
>         }, timeout)
>     }
> }
> ```
>
> - 7. 柯里化实现
>
> ```js
> function curry(fn, len = fn.length) {
>     return _curry(fn, len)
> }
> 
> function _curry(fn, len, ...arg) {
>     return function (...params) {
>         let _arg = [...arg, ...params]
>         if (_arg.length >= len) {
>             return fn.apply(this, _arg)
>         } else {
>             return _curry.call(this, fn, len, ..._arg)
>         }
>     }
> }
> 
> let fn = curry(function (a, b, c, d, e) {
>     console.log(a + b + c + d + e)
> })
> 
> fn(1, 2, 3, 4, 5)  // 15
> fn(1, 2)(3, 4, 5)
> fn(1, 2)(3)(4)(5)
> fn(1)(2)(3)(4)(5)
> ```

### 2.对执行上下文的理解

> 首先，什么是执行上下文？
>
> 官方一点地说，执行上下文（`Execution context stack` 简称 `ECS`）就**是一个评估和执行`JavaScript`代码的环境的抽象概念**。通俗地说，就是每当 `Javascript` 代码在运行的时候，它都是在执行上下文中运行。

> ##### 1. 执行上下文类型
>
> **（1）全局执行上下文**
>
> 任何不在函数内部的都是全局执行上下文，它首先会创建一个全局的window对象，并且设置this的值等于这个全局对象，一个程序中只有一个全局执行上下文。
>
> **（2）函数执行上下文**
>
> 当一个函数被调用时，就会为该函数创建一个新的执行上下文，函数的上下文可以有任意多个。
>
> 每个函数都有它自己的执行上下文，不过是在函数被调用时创建的。
>
> **（3）**`eval`**函数执行上下文**
>
> 执行在eval函数中的代码会有属于他自己的执行上下文，不过eval函数不常使用，不做介绍。
>
> ##### 2. 执行上下文栈
>
> * 也称**调用栈**、**执行栈**
>
> - JavaScript引擎使用执行上下文栈来管理执行上下文
> - 当JavaScript执行代码时，
>   - **首先遇到全局代码，会创建一个全局执行上下文并且压入执行栈中**；
>   - **每当遇到一个函数调用，就会为该函数创建一个新的执行上下文并压入栈顶**；
>   - **引擎会执行位于执行上下文栈顶的函数，当函数执行完成之后，执行上下文从栈中弹出，继续执行下一个上下文**。
>   - **当所有的代码都执行完毕之后，从栈中弹出全局执行上下文。**
>
>
> ```javascript
> let a = 'Hello World!';
> function first() {
>   console.log('Inside first function');
>   second();
>   console.log('Again inside first function');
> }
> function second() {
>   console.log('Inside second function');
> }
> first();
> //执行顺序
> //先执行second(),在执行first()
> ```
>
> ##### 3. 创建执行上下文
>
> 创建执行上下文有两个阶段：**创建阶段**和**执行阶段**
>
> **1）创建阶段**
>
> （1）this绑定
>
> - 在全局执行上下文中，this指向全局对象（window对象）
> - 在函数执行上下文中，this指向取决于函数如何调用。如果它被一个引用对象调用，那么 this 会被设置成那个对象，否则 this 的值被设置为全局对象或者 undefined
>
> （2）创建词法环境组件
>
> - 词法环境是一种有**标识符——变量映射**的数据结构，标识符是指变量/函数名，变量是对实际对象或原始数据的引用。
> - 词法环境的内部有两个组件：**环境记录器**:用来储存变量个函数声明的实际位置；**外部环境的引用**：可以访问父级作用域
>
> （3）创建变量环境组件
>
> - 变量环境也是一个词法环境，其环境记录器持有变量声明语句在执行上下文中创建的绑定关系。
>
> **2）执行阶段** 此阶段会完成对变量的分配，最后执行完代码。
>
> **简单来说执行上下文就是指：**
>
> 在执行一点JS代码之前，需要先解析代码。解析的时候会先创建一个全局执行上下文环境，先把代码中即将执行的变量、函数声明都拿出来，**变量先赋值为undefined，函数先声明好可使用**。这一步执行完了，才开始正式的执行程序。
>
> 在一个函数执行之前，也会创建一个函数执行上下文环境，跟全局执行上下文类似，不过函数执行上下文会多出this、arguments和函数的参数。
>
> - 全局上下文：变量定义，函数声明
> - 函数上下文：变量定义，函数声明，`this`，`arguments`

### 3.作用域和作用域链

> 创建函数的时候，已经声明了当前函数的作用域==>`当前创建函数所处的上下文`。如果是在全局下创建的函数就是`[[scope]]:EC(G)`，函数执行的时候，形成一个全新的私有上下文`EC(FN)`，供字符串代码执行(进栈执行)
>
> 定义：简单来说**作用域就是变量与函数的可访问范围**，`由当前环境与上层环境的一系列变量对象组成`
>  1.全局作用域：代码在程序的任何地方都能被访问，window 对象的内置属性都拥有全局作用域。
>  2.函数作用域：在固定的代码片段才能被访问
>
> 作用：作用域最大的用处就是`隔离变量`，不同作用域下同名变量不会有冲突。
>
> **作用域链参考链接**一般情况下，变量到 创建该变量 的函数的作用域中取值。但是如果在当前作用域中没有查到，就会向上级作用域去查，直到查到全局作用域，这么一个查找过程形成的链条就叫做作用域链。

> #### 块级作用域
>
> 什么是块级作用域呢？简单来说，花括号内 `{...}` 的区域就是块级作用域区域。
>
> 很多语言本身都是支持块级作用域的。上面我们说，`javascript` 中大部分情况下，只有两种作用域类型：**全局作用域** 和 **函数作用域**，那么 `javascript` 中有没有块级作用域呢？来看下面的代码：
>
> ```
> if (true) {
>   var a = 1;
> }
> 
> console.log(a); // 结果???
> ```
>
> 运行后会发现，结果还是 `1`，花括号内定义并赋值的 a 变量跑到全局了。这足以说明，`javascript` 不是原生支持块级作用域的，起码创作者创造这门语言的时候压根就没把块级作用域的事情考虑进去...（出来背锅！！）
>
> 但是 `ES6` 标准提出了使用 `let` 和 `const` 代替 `var` 关键字，来“创建块级作用域”。也就是说，上述代码改成如下方式，块级作用域是有效的：
>
> ```
> if (true) {
>   let a = 1;
> }
> 
> console.log(a); // ReferenceError
> ```

### 4.作用域,词法环境,执行上下文

> **作用域**：作用域就是一个独立的区域，它可以让变量不会向外暴露出去。作用域最大的用处就是隔离变量。内层作用域可以访问外层作用域。一个作用域下可能包含若干个执行上下文。
>
> **词法环境**：指相应代码块内标识符与变量值、函数值之间的关联关系的一种体现。**词法环境内部包含环境记录器和对外部环境的引用。**环境记录器是存储变量和函数声明的实际位置，对外部环境的引用意味着可以访问父级词法环境。
>
> **执行上下文**：JavaScript代码运行的环境。分为全局执行上下文，函数执行上下文和eval函数执行上下文（前两个较常见）。创建执行上下文时会进行this绑定、创建词法环境和变量环境。

### 5.变量提升

> #### 1.什么是变量提升？
>
> - 变量提升示例
>
> ```js
> /* 你应该见过下面的类似代码，那你知道这是为什么*/
> console.log(a)  // undefined
> var a = 10
> ```
>
> > 定义：变量提升是当栈内存作用域形成时，JS代码执行前，浏览器会将带有`var, function`关键字的变量**提前**进行声明 declare(值默认就是 undefined)，定义 defined(就是赋值操作)，这种预先处理的机制就叫做变量提升机制也叫预定义。
>
> 在变量提升阶段：带 `var` 的只声明还没有被定义，带 `function` 的已经声明和定义。所以在代码执行前有带 `var` 的就提前声明，比如这里的 `a` 就赋值成 `undefined`，在代码执行过程中遇到`创建函数的代码`浏览器会直接跳过。
>
> > 不考虑变量提升阶段的 js 运行机制相关参考 [JS 运行机制基础版](https://juejin.cn/post/6926729456790798343)

## 六、this/call/apply/bind

### 1. 对this对象的理解

> 在 ES5 中，其实 this 的指向，始终坚持一个原理：**this 永远指向最后调用它的那个对象**
>
> this 是执行上下文中的一个属性，它**指向最后一次调用它的对象**。在实际开发中，this 的指向可以通过四种调用模式来判断。
>
> - 第一种是**函数调用模式**，当一个函数不是一个对象的属性时，直接作为函数来调用时，this 指向全局对象。
> - 第二种是**方法调用模式**，如果一个函数作为一个对象的方法来调用时，this 指向这个对象。
> - 第三种是**构造器调用模式**，如果一个函数用 new 调用时，函数执行前会新创建一个对象，this 指向这个新创建的对象。
> - 第四种是 **apply 、 call 和 bind 调用模式**，这三个方法都可以显示的指定调用函数的 this 指向。其中 apply 方法接收两个参数：一个是 this 绑定的对象，一个是参数数组。call 方法接收的参数，第一个是 this 绑定的对象，后面的其余参数是传入函数执行的参数。也就是说，在使用 call() 方法时，传递给函数的参数必须逐个列举出来。bind 方法通过传入一个对象，返回一个 this 绑定了传入对象的新函数。这个函数的 this 指向除了使用 new 时会被改变，其他情况下都不会改变。
> - 箭头函数 箭头函数的`this`绑定看的是`this所在函数定义在哪个对象下`，就绑定哪个对象。如果有嵌套的情况，则this绑定到最近的一层对象上。
>
> 这四种方式，**使用构造器调用模式的优先级最高，然后是 apply、call 和 bind 调用模式，然后是方法调用模式，然后是函数调用模式**。

### 2.怎么改变 this 的指向

> 改变 this 的指向我总结有以下几种方法：
>
> - 使用 ES6 的箭头函数
> - 在函数内部使用 `_this = this`
> - 使用 `apply`、`call`、`bind`
> - new 实例化一个对象
>
> #### 箭头函数
>
> **箭头函数的 this 始终指向函数定义时的 this，而非执行时。**箭头函数需要记着这句话：“箭头函数中没有 this 绑定，必须通过查找作用域链来决定其值，如果箭头函数被非箭头函数包含，则 this 绑定的是最近一层非箭头函数的 this，否则，this 为 undefined”。
>
> ```js
>    var name = "windowsName";
> 
>     var a = {
>         name : "Cherry",
> 
>         func1: function () {
>             console.log(this.name)     
>         },
> 
>         func2: function () {
>             setTimeout( () => {
>                 this.func1()
>             },100);
>         }
> 
>     };
> 
>     a.func2()     // Cherry
> ```
>
> #### 在函数内部使用 `_this = this`
>
> 如果不使用 ES6，那么这种方式应该是最简单的不会出错的方式了，我们是先将调用这个函数的对象保存在变量 `_this` 中，然后在函数中都使用这个 `_this`，这样 `_this` 就不会改变了。
>
> ```js
>    var name = "windowsName";
> 
>     var a = {
> 
>         name : "Cherry",
> 
>         func1: function () {
>             console.log(this.name)     
>         },
> 
>         func2: function () {
>             var _this = this;
>             setTimeout( function() {
>                 _this.func1()
>             },100);
>         }
> 
>     };
> 
>     a.func2()       // Cherry
> ```
>
> #### 使用 apply、call、bind

### 3. apply、call、bind 区别

> 在MDN中定义 apply 如下；
>
> > apply() 方法调用一个函数, 其具有一个指定的this值，以及作为一个数组（或类似数组的对象）提供的参数
>
> 语法：
>
> > fun.apply(thisArg, [argsArray])
>
> - thisArg：在 fun 函数运行时指定的 this 值。需要注意的是，指定的 this 值并不一定是该函数执行时真正的 this 值，如果这个函数处于非严格模式下，则指定为 null 或 undefined 时会自动指向全局对象（浏览器中就是window对象），同时值为原始值（数字，字符串，布尔值）的 this 会指向该原始值的自动包装对象。
> - argsArray：一个数组或者类数组对象，其中的数组元素将作为单独的参数传给 fun 函数。如果该参数的值为null 或 undefined，则表示不需要传入任何参数。从ECMAScript 5 开始可以使用类数组对象。浏览器兼容性请参阅本文底部内容。

> #### apply 和 call 的区别
>
> 它们的作用一模一样，区别仅在于传入参数的形式的不同。
>
> call 的语法为：
>
> ```
> fun.call(thisArg[, arg1[, arg2[, ...]]])复制代码
> ```
>
> 所以 apply 和 call 的区别是 call 方法接受的是若干个参数列表，而 apply 接收的是一个包含多个参数的数组。
>
> ```js
>   var a ={
>         name : "Cherry",
>         fn : function (a,b) {
>             console.log( a + b)
>         }
>     }
> 
>     var b = a.fn;
>     b.apply(a,[1,2])     // 3
> 
>     b.call(a,1,2)       // 3
> ```

> #### bind 和 apply、call 区别
>
> ```js
>  var a ={
>         name : "Cherry",
>         fn : function (a,b) {
>             console.log( a + b)
>         }
>     }
> 
>     var b = a.fn;
>     b.bind(a,1,2)
> 
> ```
>
> 我们会发现并没有输出，这是为什么呢，我们来看一下 [MDN](https://link.juejin.cn?target=https%3A%2F%2Fdeveloper.mozilla.org%2Fzh-CN%2Fdocs%2FWeb%2FJavaScript%2FReference%2FGlobal_Objects%2FFunction%2Fbind) 上的文档说明：
>
> > bind()方法创建一个新的函数, 当被调用时，将其this关键字设置为提供的值，在调用新函数时，在任何提供之前提供一个给定的参数序列。
>
> 所以我们可以看出，bind 是创建一个新的函数，我们必须要手动去调用：
>
> ```js
>  b.bind(a,1,2)()           // 3
> ```

### 4. 实现call、apply 及 bind 函数

> **（1）call 函数的实现步骤：**
>
> - 判断调用对象是否为函数，即使是定义在函数的原型上的，但是可能出现使用 call 等方式调用的情况。
> - 判断传入上下文对象是否存在，如果不存在，则设置为 window 。
> - 处理传入的参数，截取第一个参数后的所有参数。
> - 将函数作为上下文对象的一个属性。
> - 使用上下文对象来调用这个方法，并保存返回结果。
> - 删除刚才新增的属性。
> - 返回结果。
>
> ```javascript
> Function.prototype.myCall = function(context) {
>   // 判断调用对象
>   if (typeof this !== "function") {
>     console.error("type error");
>   }
>   // 获取参数
>   let args = [...arguments].slice(1),
>     result = null;
>   // 判断 context 是否传入，如果未传入则设置为 window
>   context = context || window;
>   // 将调用函数设为对象的方法
>   context.fn = this;
>   // 调用函数
>   result = context.fn(...args);
>   // 将属性删除
>   delete context.fn;
>   return result;
> };
> ```
>
> **（2）apply 函数的实现步骤：**
>
> - 判断调用对象是否为函数，即使是定义在函数的原型上的，但是可能出现使用 call 等方式调用的情况。
> - 判断传入上下文对象是否存在，如果不存在，则设置为 window 。
> - 将函数作为上下文对象的一个属性。
> - 判断参数值是否传入
> - 使用上下文对象来调用这个方法，并保存返回结果。
> - 删除刚才新增的属性
> - 返回结果
>
> ```javascript
> Function.prototype.myApply = function(context) {
>   // 判断调用对象是否为函数
>   if (typeof this !== "function") {
>     throw new TypeError("Error");
>   }
>   let result = null;
>   // 判断 context 是否存在，如果未传入则为 window
>   context = context || window;
>   // 将函数设为对象的方法
>   context.fn = this;
>   // 调用方法
>   if (arguments[1]) {
>     result = context.fn(...arguments[1]);
>   } else {
>     result = context.fn();
>   }
>   // 将属性删除
>   delete context.fn;
>   return result;
> };
> ```
>
> **（3）bind 函数的实现步骤：**
>
> - 判断调用对象是否为函数，即使是定义在函数的原型上的，但是可能出现使用 call 等方式调用的情况。
> - 保存当前函数的引用，获取其余传入参数值。
> - 创建一个函数返回
> - 函数内部使用 apply 来绑定函数调用，需要判断函数作为构造函数的情况，这个时候需要传入当前函数的 this 给 apply 调用，其余情况都传入指定的上下文对象。
>
> ```javascript
> Function.prototype.myBind = function(context) {
>   // 判断调用对象是否为函数
>   if (typeof this !== "function") {
>     throw new TypeError("Error");
>   }
>   // 获取参数
>   var args = [...arguments].slice(1),
>     fn = this;
>   return function Fn() {
>     // 根据调用方式，传入不同绑定值
>     return fn.apply(
>       this instanceof Fn ? this : context,
>       args.concat(...arguments)
>     );
>   };
> };
> ```

## 七、异步编程

### 1. 异步编程的实现方式？

> - **回调函数** 的方式，使用回调函数的方式有一个缺点是，多个回调函数嵌套的时候会造成回调函数地狱，上下两层的回调函数间的代码耦合度太高，不利于代码的可维护。
> - **Promise** 的方式，使用 Promise 的方式可以将嵌套的回调函数作为链式调用。但是使用这种方法，有时会造成多个 then 的链式调用，可能会造成代码的语义不够明确。
> - **generator** 的方式，它可以在函数的执行过程中，将函数的执行权转移出去，在函数外部还可以将执行权转移回来。当遇到异步函数执行的时候，将函数执行权转移出去，当异步函数执行完毕时再将执行权给转移回来。因此在 generator 内部对于异步操作的方式，可以以同步的顺序来书写。使用这种方式需要考虑的问题是何时将函数的控制权转移回来，因此需要有一个自动执行 generator 的机制，比如说 co 模块等方式来实现 generator 的自动执行。
> - **async 函数** 的方式，async 函数是 generator 和 promise 实现的一个自动执行的语法糖，它内部自带执行器，当函数内部执行到一个 await 语句的时候，如果语句返回一个 promise 对象，那么函数将会等待 promise 对象的状态变为 resolve 后再继续向下执行。因此可以将异步逻辑，转化为同步的顺序来书写，并且这个函数可以自动执行。

### 2. setTimeout、Promise、Async/Await 的区别

>#### （1）setTimeout
>
>> settimeout的回调函数放到宏任务队列里，等到执行栈清空以后执行。
>
>```javascript
>console.log('script start')	//1. 打印 script start
>setTimeout(function(){
>console.log('settimeout')	// 4. 打印 settimeout
>})	// 2. 调用 setTimeout 函数，并定义其完成后执行的回调函数
>console.log('script end')	//3. 打印 script start
>// 输出顺序：script start->script end->settimeout
>```
>
>#### （2）Promise
>
>Promise本身是**同步的立即执行函数**， 当在executor中执行resolve或者reject的时候, 此时是异步操作， 会先执行then/catch等，当主栈完成后，才会去调用resolve/reject中存放的方法执行，打印p的时候，是打印的返回结果，一个Promise实例。
>
>```javascript
>console.log('script start')
>let promise1 = new Promise(function (resolve) {
>console.log('promise1')
>resolve()
>console.log('promise1 end')
>}).then(function () {
>console.log('promise2')
>})
>setTimeout(function(){
>console.log('settimeout')
>})
>console.log('script end')
>// 输出顺序: script start->promise1->promise1 end->script end->promise2->settimeout
>```
>
>当JS主线程执行到Promise对象时：
>
>- promise1.then() 的回调就是一个 task
>- promise1 是 resolved或rejected: 那这个 task 就会放入当前事件循环回合的 microtask queue
>- promise1 是 pending: 这个 task 就会放入 事件循环的未来的某个(可能下一个)回合的 microtask queue 中
>- setTimeout 的回调也是个 task ，它会被放入 macrotask queue 即使是 0ms 的情况
>
>#### （3）async/await
>
>```javascript
>async function async1(){
>  console.log('async1 start');
>   await async2();
>   console.log('async1 end')
>}
>async function async2(){
>   console.log('async2')
>}
>console.log('script start');
>async1();
>console.log('script end')
>// 输出顺序：script start->async1 start->async2->script end->async1 end
>```
>
>async 函数返回一个 Promise 对象，当函数执行的时候，一旦遇到 await 就会先返回，等到触发的异步操作完成，再执行函数体内后面的语句。可以理解为，是让出了线程，跳出了 async 函数体。
>
>例如：
>
>```javascript
>async function func1() {
>   return 1
>}
>console.log(func1())
>```
>
>![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/40194bdc07024b78b6da8bb43d84a7d8~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp) func1的运行结果其实就是一个Promise对象。因此也可以使用then来处理后续逻辑。
>
>```javascript
>func1().then(res => {
>   console.log(res);  // 30
>})
>```
>
>await的含义为等待，也就是 async 函数需要等待await后的函数执行完成并且有了返回结果（Promise对象）之后，才能继续执行下面的代码。await通过返回一个Promise对象来实现同步的效果。

### 3. 对Promise的理解

> Promise是异步编程的一种解决方案，它是一个对象，可以获取异步操作的消息，他的出现大大改善了异步编程的困境，避免了地狱回调，它比传统的解决方案回调函数和事件更合理和更强大。
>
> 所谓Promise，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。从语法上说，Promise 是一个对象，从它可以获取异步操作的消息。Promise 提供统一的 API，各种异步操作都可以用同样的方法进行处理。
>
> （1）Promise的实例有**三个状态**:
>
> - Pending（进行中）
> - Resolved（已完成）
> - Rejected（已拒绝）
>
> 当把一件事情交给promise时，它的状态就是Pending，任务完成了状态就变成了Resolved、没有完成失败了就变成了Rejected。
>
> （2）Promise的实例有**两个过程**：
>
> - pending -> fulfilled : Resolved（已完成）
> - pending -> rejected：Rejected（已拒绝）
>
> 注意：一旦从进行状态变成为其他状态就永远不能更改状态了。
>
> **Promise的特点：**
>
> - 对象的状态不受外界影响。promise对象代表一个异步操作，有三种状态，`pending`（进行中）、`fulfilled`（已成功）、`rejected`（已失败）。只有异步操作的结果，可以决定当前是哪一种状态，任何其他操作都无法改变这个状态，这也是promise这个名字的由来——“**承诺**”；
> - 一旦状态改变就不会再变，任何时候都可以得到这个结果。promise对象的状态改变，只有两种可能：从`pending`变为`fulfilled`，从`pending`变为`rejected`。这时就称为`resolved`（已定型）。如果改变已经发生了，你再对promise对象添加回调函数，也会立即得到这个结果。这与事件（event）完全不同，事件的特点是：如果你错过了它，再去监听是得不到结果的。
>
> **Promise的缺点：**
>
> - 无法取消Promise，一旦新建它就会立即执行，无法中途取消。
> - 如果不设置回调函数，Promise内部抛出的错误，不会反应到外部。
> - 当处于pending状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）。
>
> **总结：** Promise 对象是异步编程的一种解决方案，最早由社区提出。Promise 是一个构造函数，接收一个函数作为参数，返回一个 Promise 实例。一个 Promise 实例有三种状态，分别是pending、resolved 和 rejected，分别代表了进行中、已成功和已失败。实例的状态只能由 pending 转变 resolved 或者rejected 状态，并且状态一经改变，就凝固了，无法再被改变了。
>
> 状态的改变是通过 resolve() 和 reject() 函数来实现的，可以在异步操作结束后调用这两个函数改变 Promise 实例的状态，它的原型上定义了一个 then 方法，使用这个 then 方法可以为两个状态的改变注册回调函数。这个回调函数属于微任务，会在本轮事件循环的末尾执行。
>
> **注意：** 在构造 `Promise` 的时候，构造函数内部的代码是立即执行的

### 4. Promise的基本用法

> #### （1）创建Promise对象
>
> Promise对象代表一个异步操作，有三种状态：pending（进行中）、fulfilled（已成功）和rejected（已失败）。
>
> Promise构造函数接受一个函数作为参数，该函数的两个参数分别是`resolve`和`reject`。
>
> ```javascript
> const promise = new Promise(function(resolve, reject) {
>   // ... some code
>   if (/* 异步操作成功 */){
>     resolve(value);
>   } else {
>     reject(error);
>   }
> });
> ```
>
> **一般情况下都会使用**`new Promise()`**来创建promise对象，但是也可以使用**`promise.resolve`**和**`promise.reject`**这两个方法：**
>
> - **Promise.resolve**
>
> `Promise.resolve(value)`的返回值也是一个promise对象，可以对返回值进行.then调用，代码如下：
>
> ```javascript
> Promise.resolve(11).then(function(value){
>   console.log(value); // 打印出11
> });
> ```
>
> `resolve(11)`代码中，会让promise对象进入确定(`resolve`状态)，并将参数`11`传递给后面的`then`所指定的`onFulfilled` 函数；
>
> 创建promise对象可以使用`new Promise`的形式创建对象，也可以使用`Promise.resolve(value)`的形式创建promise对象；
>
> - **Promise.reject**
>
> `Promise.reject` 也是`new Promise`的快捷形式，也创建一个promise对象。代码如下：
>
> ```javascript
> Promise.reject(new Error(“我错了，请原谅俺！！”));
> ```
>
> 就是下面的代码new Promise的简单形式：
>
> ```javascript
> new Promise(function(resolve,reject){
>    reject(new Error("我错了！"));
> });
> ```
>
> 下面是使用resolve方法和reject方法：
>
> ```javascript
> function testPromise(ready) {
>   return new Promise(function(resolve,reject){
>     if(ready) {
>       resolve("hello world");
>     }else {
>       reject("No thanks");
>     }
>   });
> };
> // 方法调用
> testPromise(true).then(function(msg){
>   console.log(msg);
> },function(error){
>   console.log(error);
> });
> ```
>
> 上面的代码的含义是给`testPromise`方法传递一个参数，返回一个promise对象，如果为`true`的话，那么调用promise对象中的`resolve()`方法，并且把其中的参数传递给后面的`then`第一个函数内，因此打印出 “`hello world`”, 如果为`false`的话，会调用promise对象中的`reject()`方法，则会进入`then`的第二个函数内，会打印`No thanks`；
>
> #### （2）Promise方法
>
> Promise有五个常用的方法：then()、catch()、all()、race()、finally。下面就来看一下这些方法。
>
> 1. **then()**
>
> 当Promise执行的内容符合成功条件时，调用`resolve`函数，失败就调用`reject`函数。Promise创建完了，那该如何调用呢？
>
> ```js
> promise.then(function(value) {
>   // success
> }, function(error) {
>   // failure
> });
> ```
>
> `then`方法可以接受两个回调函数作为参数。第一个回调函数是Promise对象的状态变为`resolved`时调用，第二个回调函数是Promise对象的状态变为`rejected`时调用。其中第二个参数可以省略。 `then`方法返回的是一个新的Promise实例（不是原来那个Promise实例）。因此可以采用链式写法，即`then`方法后面再调用另一个then方法。
>
> 当要写有顺序的异步事件时，需要串行时，可以这样写：
>
> ```javascript
> let promise = new Promise((resolve,reject)=>{
>     ajax('first').success(function(res){
>         resolve(res);
>     })
> })
> promise.then(res=>{
>     return new Promise((resovle,reject)=>{
>         ajax('second').success(function(res){
>             resolve(res)
>         })
>     })
> }).then(res=>{
>     return new Promise((resovle,reject)=>{
>         ajax('second').success(function(res){
>             resolve(res)
>         })
>     })
> }).then(res=>{
>     
> })
> ```
>
> 那当要写的事件没有顺序或者关系时，还如何写呢？可以使用`all` 方法来解决。
>
> **2. catch()**
>
> Promise对象除了有then方法，还有一个catch方法，该方法相当于`then`方法的第二个参数，指向`reject`的回调函数。不过`catch`方法还有一个作用，就是在执行`resolve`回调函数时，如果出现错误，抛出异常，不会停止运行，而是进入`catch`方法中。
>
> ```javascript
> p.then((data) => {
>      console.log('resolved',data);
> },(err) => {
>      console.log('rejected',err);
>      }
> ); 
> p.then((data) => {
>     console.log('resolved',data);
> }).catch((err) => {
>     console.log('rejected',err);
> });
> ```
>
> **3. all()**
>
> `all`方法可以完成并行任务， 它接收一个数组，数组的每一项都是一个`promise`对象。当数组中所有的`promise`的状态都达到`resolved`的时候，`all`方法的状态就会变成`resolved`，如果有一个状态变成了`rejected`，那么`all`方法的状态就会变成`rejected`。
>
> ```javascript
> javascript
> let promise1 = new Promise((resolve,reject)=>{
> 	setTimeout(()=>{
>        resolve(1);
> 	},2000)
> });
> let promise2 = new Promise((resolve,reject)=>{
> 	setTimeout(()=>{
>        resolve(2);
> 	},1000)
> });
> let promise3 = new Promise((resolve,reject)=>{
> 	setTimeout(()=>{
>        resolve(3);
> 	},3000)
> });
> Promise.all([promise1,promise2,promise3]).then(res=>{
>     console.log(res);
>     //结果为：[1,2,3] 
> })
> ```
>
> 调用`all`方法时的结果成功的时候是回调函数的参数也是一个数组，这个数组按顺序保存着每一个promise对象`resolve`执行时的值。
>
> **（4）race()**
>
> `race`方法和`all`一样，接受的参数是一个每项都是`promise`的数组，但是与`all`不同的是，当最先执行完的事件执行完之后，就直接返回该`promise`对象的值。如果第一个`promise`对象状态变成`resolved`，那自身的状态变成了`resolved`；反之第一个`promise`变成`rejected`，那自身状态就会变成`rejected`。
>
> ```javascript
> let promise1 = new Promise((resolve,reject)=>{
> 	setTimeout(()=>{
>        reject(1);
> 	},2000)
> });
> let promise2 = new Promise((resolve,reject)=>{
> 	setTimeout(()=>{
>        resolve(2);
> 	},1000)
> });
> let promise3 = new Promise((resolve,reject)=>{
> 	setTimeout(()=>{
>        resolve(3);
> 	},3000)
> });
> Promise.race([promise1,promise2,promise3]).then(res=>{
> 	console.log(res);
> 	//结果：2
> },rej=>{
>     console.log(rej)};
> )
> ```
>
> 那么`race`方法有什么实际作用呢？当要做一件事，超过多长时间就不做了，可以用这个方法来解决：
>
> ```javascript
> Promise.race([promise1,timeOutPromise(5000)]).then(res=>{})
> ```
>
> **5. finally()**
>
> `finally`方法用于指定不管 Promise 对象最后状态如何，都会执行的操作。该方法是 ES2018 引入标准的。
>
> ```javascript
> promise
> .then(result => {···})
> .catch(error => {···})
> .finally(() => {···});
> ```
>
> 上面代码中，不管`promise`最后的状态，在执行完`then`或`catch`指定的回调函数以后，都会执行`finally`方法指定的回调函数。
>
> 下面是一个例子，服务器使用 Promise 处理请求，然后使用`finally`方法关掉服务器。
>
> ```javascript
> server.listen(port)
>   .then(function () {
>     // ...
>   })
>   .finally(server.stop);
> ```
>
> `finally`方法的回调函数不接受任何参数，这意味着没有办法知道，前面的 Promise 状态到底是`fulfilled`还是`rejected`。这表明，`finally`方法里面的操作，应该是与状态无关的，不依赖于 Promise 的执行结果。`finally`本质上是`then`方法的特例：
>
> ```javascript
> promise
> .finally(() => {
>   // 语句
> });
> // 等同于
> promise
> .then(
>   result => {
>     // 语句
>     return result;
>   },
>   error => {
>     // 语句
>     throw error;
>   }
> );
> ```
>
> 上面代码中，如果不使用`finally`方法，同样的语句需要为成功和失败两种情况各写一次。有了`finally`方法，则只需要写一次。

### 5. Promise解决了什么问题

> 在工作中经常会碰到这样一个需求，比如我使用ajax发一个A请求后，成功后拿到数据，需要把数据传给B请求；那么需要如下编写代码：
>
> ```javascript
> let fs = require('fs')
> fs.readFile('./a.txt','utf8',function(err,data){
>   fs.readFile(data,'utf8',function(err,data){
>     fs.readFile(data,'utf8',function(err,data){
>       console.log(data)
>     })
>   })
> })
> ```
>
> 上面的代码有如下缺点：
>
> - 后一个请求需要依赖于前一个请求成功后，将数据往下传递，会导致多个ajax请求嵌套的情况，代码不够直观。
> - 如果前后两个请求不需要传递参数的情况下，那么后一个请求也需要前一个请求成功后再执行下一步操作，这种情况下，那么也需要如上编写代码，导致代码不够直观。
>
> `Promise`出现之后，代码变成这样：
>
> ```javascript
> let fs = require('fs')
> function read(url){
>   return new Promise((resolve,reject)=>{
>     fs.readFile(url,'utf8',function(error,data){
>       error && reject(error)
>       resolve(data)
>     })
>   })
> }
> read('./a.txt').then(data=>{
>   return read(data) 
> }).then(data=>{
>   return read(data)  
> }).then(data=>{
>   console.log(data)
> })
> ```
>
> 这样代码看起了就简洁了很多，解决了地狱回调的问题。

### 6. Promise.all、Promise.race和Promise.any的区别的使用场景

> **（1）Promise.all**
>
>  `Promise.all`可以将多个`Promise`实例包装成一个新的Promise实例。同时，成功和失败的返回值是不同的，成功的时候返回的是**一个结果数组**，而失败的时候则返回**最先被reject失败状态的值**。
>
> Promise.all中传入的是数组，返回的也是是数组，并且会将进行映射，传入的promise对象返回的值是按照顺序在数组中排列的，但是注意的是他们执行的顺序并不是按照顺序的，除非可迭代对象为空。
>
> 需要注意，Promise.all获得的成功结果的数组里面的数据顺序和Promise.all接收到的数组顺序是一致的，这样当遇到发送多个请求并根据请求顺序获取和使用数据的场景，就可以使用Promise.all来解决。
>
> 语法：
>
> ```javascript
> let promise = Promise.all([...promises...]);
> ```
>
> #### 1.1.resolve
>
> `Promise.all` 接受一个 promise 数组作为参数（从技术上讲，它可以是任何可迭代对象，但通常是一个数组）并返回一个新的 promise。
>
> **当所有给定的 promise 都 resolve 时，新的 promise 才会 resolve，并且其结果数组将成为新 promise 的结果。**
>
> 例如，下面的 `Promise.all` 在 3 秒之后 settled，然后它的结果就是一个 `[1, 2, 3]` 数组：
>
> ```javascript
> Promise.all([
>   new Promise(resolve => setTimeout(() => resolve(1), 3000)), // 1
>   new Promise(resolve => setTimeout(() => resolve(2), 2000)), // 2
>   new Promise(resolve => setTimeout(() => resolve(3), 1000))  // 3
> ]).then(alert); // 1,2,3 当上面这些 promise 准备好时：每个 promise 都贡献了数组中的一个
> ```
>
> #### 1.2.reject
>
> **如果任意一个 promise 被 reject，由 `Promise.all` 返回的 promise 就会立即 reject，并且带有的就是这个 error。**
>
> 例如：
>
> ```javascript
> Promise.all([
>   new Promise((resolve, reject) => setTimeout(() => resolve(1), 1000)),
>   new Promise((resolve, reject) => setTimeout(() => reject(new Error("Whoops!")), 2000)),
>   new Promise((resolve, reject) => setTimeout(() => resolve(3), 3000))
> ]).catch(alert); // Error: Whoops!
> ```
>
> 这里的第二个 promise 在两秒后 reject。这立即导致了 `Promise.all` 的 reject，因此 `.catch` 执行了：被 reject 的 error 成为了整个 `Promise.all` 的结果。
>
> > **如果出现 error，其他 promise 将被忽略**
> >
> > 如果其中一个 promise 被 reject，`Promise.all` 就会立即被 reject，完全忽略列表中其他的 promise。它们的结果也被忽略。
>
> #### 1.3.Promise.all(iterable)
>
> **`Promise.all(iterable)` 允许在 `iterable` 中使用 non-promise 的“常规”值**
>
> 通常，`Promise.all(...)` 接受含有 promise 项的可迭代对象（大多数情况下是数组）作为参数。但是，如果这些对象中的任何一个不是 promise，那么它将被“按原样”传递给结果数组。
>
> 例如，这里的结果是 `[1, 2, 3]`：
>
> ```javascript
> Promise.all([
>   new Promise((resolve, reject) => {
>     setTimeout(() => resolve(1), 1000)
>   }),
>   2,
>   3
> ]).then(alert); // 1, 2, 3
> 
> Promise.all([]).then((res) => {
>     console.log('all');//有输出
> });//res=[]
> ```
>
> 所以我们可以在方便的地方将准备好的值传递给 `Promise.all`。

> #### 1.4.Promise.allSettled
>
> 如果任意的 promise reject，则 `Promise.all` 整个将会 reject。当我们需要 **所有** 结果都成功时，它对这种“全有或全无”的情况很有用：
>
> ```javascript
> Promise.all([
>   fetch('/template.html'),
>   fetch('/style.css'),
>   fetch('/data.json')
> ]).then(render); // render 方法需要所有 fetch 的数据
> ```
>
> `Promise.allSettled` 等待所有的 promise 都被 settle，无论结果如何。结果数组具有：
>
> - `{status:"fulfilled", value:result}` 对于成功的响应，
> - `{status:"rejected", reason:error}` 对于 error。
>
> 例如，我们想要获取（fetch）多个用户的信息。即使其中一个请求失败，我们仍然对其他的感兴趣。
>
> 让我们使用 `Promise.allSettled`：
>
> ```javascript
> let urls = [
>   'https://api.github.com/users/iliakan',
>   'https://api.github.com/users/remy',
>   'https://no-such-url'
> ];
> 
> Promise.allSettled(urls.map(url => fetch(url)))
>   .then(results => { // (*)
>     results.forEach((result, num) => {
>       if (result.status == "fulfilled") {
>         alert(`${urls[num]}: ${result.value.status}`);
>       }
>       if (result.status == "rejected") {
>         alert(`${urls[num]}: ${result.reason}`);
>       }
>     });
>   });
> ```
>
> 上面的 `(*)` 行中的 `results` 将会是：
>
> ```javascript
> [
>   {status: 'fulfilled', value: ...response...},
>   {status: 'fulfilled', value: ...response...},
>   {status: 'rejected', reason: ...error object...}
> ]
> ```
>
> 所以，对于每个 promise，我们都得到了其状态（status）和 `value/reason`。
>
> ##### Polyfill
>
> 如果浏览器不支持 `Promise.allSettled`，很容易进行 polyfill：
>
> ```javascript
> if (!Promise.allSettled) {
>   const rejectHandler = reason => ({ status: 'rejected', reason });
> 
>   const resolveHandler = value => ({ status: 'fulfilled', value });
> 
>   Promise.allSettled = function (promises) {
>     const convertedPromises = promises.map(p => Promise.resolve(p).then(resolveHandler, rejectHandler));
>     return Promise.all(convertedPromises);
>   };
> }
> ```
>
> 在这段代码中，`promises.map` 获取输入值，并通过 `p => Promise.resolve(p)` 将输入值转换为 promise（以防传递了 non-promise），然后向每一个 promise 都添加 `.then` 处理程序（handler）。
>
> 这个处理程序（handler）将成功的结果 `value` 转换为 `{status:'fulfilled', value}`，将 error `reason` 转换为 `{status:'rejected', reason}`。这正是 `Promise.allSettled` 的格式。
>
> 然后我们就可以使用 `Promise.allSettled` 来获取 **所有** 给定的 promise 的结果，即使其中一些被 reject。

> **（2）Promise.race**
>
> 与 `Promise.all` 类似，但只等待第一个 settled (无论是fulfilled还是rejected)的 promise 并获取其结果（或 error）。
>
> 语法：
>
> ```javascript
> let promise = Promise.race(iterable);
> ```
>
> 例如，这里的结果将是 `1`：
>
> ```javascript
> Promise.race([
>   new Promise((resolve, reject) => setTimeout(() => resolve(1), 1000)),
>   new Promise((resolve, reject) => setTimeout(() => reject(new Error("Whoops!")), 2000)),
>   new Promise((resolve, reject) => setTimeout(() => resolve(3), 3000))
> ]).then(alert); // 1
> ```
>
> 这里第一个 promise 最快，所以它变成了结果。第一个 settled 的 promise “赢得了比赛”之后，所有进一步的 result/error 都会被忽略。

> **（3）Promise.any**
>
> 与 `Promise.race` 类似，区别在于 `Promise.any` 只等待第一个 fulfilled 的 promise，并将这个 fulfilled 的 promise 返回。如果给出的 promise 都 rejected，那么则返回 rejected 的 promise 和 `AggregateError` 错误类型的 error 实例—— 一个特殊的 error 对象，在其 `errors` 属性中存储着所有 promise error。
>
> 语法如下：
>
> ```javascript
> let promise = Promise.any(iterable);
> ```
>
> 例如，这里的结果将是 `1`：
>
> ```javascript
> Promise.any([
>   new Promise((resolve, reject) => setTimeout(() => reject(new Error("Whoops!")), 1000)),
>   new Promise((resolve, reject) => setTimeout(() => resolve(1), 2000)),
>   new Promise((resolve, reject) => setTimeout(() => resolve(3), 3000))
> ]).then(alert); // 1
> ```
>
> 这里的第一个 promise 是最快的，但 rejected 了，所以第二个 promise 则成为了结果。在第一个 fulfilled 的 promise “赢得比赛”后，所有进一步的结果都将被忽略。
>
> 这是一个所有 promise 都失败的例子：
>
> ```javascript
> Promise.any([
>   new Promise((resolve, reject) => setTimeout(() => reject(new Error("Ouch!")), 1000)),
>   new Promise((resolve, reject) => setTimeout(() => reject(new Error("Error!")), 2000))
> ]).catch(error => {
>   console.log(error.constructor.name); // AggregateError
>   console.log(error.errors[0]); // Error: Ouch!
>   console.log(error.errors[1]); // Error: Error
> });
> ```
>
> 正如你所看到的，我们在 `AggregateError` 错误类型的 error 实例的 `errors` 属性中可以访问到失败的 promise 的 error 对象。



### 7. 对async/await 的理解

> async/await其实是`Generator` 的语法糖，它能实现的效果都能用then链来实现，它是为优化then链而开发出来的。从字面上来看，async是“异步”的简写，await则为等待，所以很好理解async 用于申明一个 function 是异步的，而 await 用于等待一个异步方法执行完成。当然语法上强制规定await只能出现在asnyc函数中，先来看看async函数返回了什么：
>
> ```javascript
> async function testAsy(){
>    return 'hello world';
> }
> let result = testAsy(); 
> console.log(result)
> ```
>
> ![image-20220310005355625](C:\Users\H\AppData\Roaming\Typora\typora-user-images\image-20220310005355625.png)
>
> 所以，async 函数返回的是一个 Promise 对象。async 函数（包含函数语句、函数表达式、Lambda表达式）会返回一个 Promise 对象，如果在函数中 `return` 一个直接量，async 会把这个直接量通过 `Promise.resolve()` 封装成 Promise 对象。
>
> async 函数返回的是一个 Promise 对象，所以在最外层不能用 await 获取其返回值的情况下，当然应该用原来的方式：`then()` 链来处理这个 Promise 对象，就像这样：
>
> ```javascript
> async function testAsy(){
>    return 'hello world'
> }
> let result = testAsy() 
> console.log(result)
> result.then(v=>{
>     console.log(v)   // hello world
> })
> ```
>
> 那如果 async 函数没有返回值，又该如何？很容易想到，它会返回 `Promise.resolve(undefined)`。
>
> 联想一下 Promise 的特点——无等待，所以在没有 `await` 的情况下执行 async 函数，它会立即执行，返回一个 Promise 对象，并且，绝不会阻塞后面的语句。这和普通返回 Promise 对象的函数并无二致。
>
> **注意：**`Promise.resolve(x)` 可以看作是 `new Promise(resolve => resolve(x))` 的简写，可以用于快速封装字面量对象或其他对象，将其封装成 Promise 实例。

### 8. await 到底在等啥？

> **await 在等待什么呢？** 一般来说，都认为 await 是在等待一个 async 函数完成。不过按语法说明，await 等待的是一个表达式，这个表达式的计算结果是 Promise 对象或者其它值（换句话说，就是没有特殊限定）。
>
> 因为 async 函数返回一个 Promise 对象，所以 await 可以用于等待一个 async 函数的返回值——这也可以说是 await 在等 async 函数，但要清楚，它等的实际是一个返回值。注意到 await 不仅仅用于等 Promise 对象，它可以等任意表达式的结果，所以，await 后面实际是可以接普通函数调用或者直接量的。所以下面这个示例完全可以正确运行：
>
> ```javascript
> function getSomething() {
>     return "something";
> }
> async function testAsync() {
>     return Promise.resolve("hello async");
> }
> async function test() {
>     const v1 = await getSomething();
>     const v2 = await testAsync();
>     console.log(v1, v2);
> }
> test();
> ```
>
> await 表达式的运算结果取决于它等的是什么。
>
> - 如果它等到的不是一个 Promise 对象，那 await 表达式的运算结果就是它等到的东西。
> - 如果它等到的是一个 Promise 对象，await 就忙起来了，它会阻塞后面的代码，等着 Promise 对象 resolve，然后得到 resolve 的值，作为 await 表达式的运算结果。
>
> 来看一个例子：
>
> ```javascript
> function testAsy(x){
>    return new Promise(resolve=>{setTimeout(() => {
>        resolve(x);
>      }, 3000)
>     }
>    )
> }
> async function testAwt(){    
>   let result =  await testAsy('hello world');
>   console.log(result);    // 3秒钟之后出现hello world
>   console.log('cuger')   // 3秒钟之后出现cug
> }
> testAwt();
> console.log('cug')  //立即输出cug
> ```
>
> 这就是 await 必须用在 async 函数中的原因。async 函数调用不会造成阻塞，它内部所有的阻塞都被封装在一个 Promise 对象中异步执行。await暂停当前async的执行，所以'cug''最先输出，hello world'和‘cuger’是3秒钟后同时出现的。

### 9. async/await的优势

> 单一的 Promise 链并不能发现 async/await 的优势，但是，如果需要处理由多个 Promise 组成的 then 链的时候，优势就能体现出来了（很有意思，Promise 通过 then 链来解决多层回调的问题，现在又用 async/await 来进一步优化它）。
>
> 假设一个业务，分多个步骤完成，每个步骤都是异步的，而且依赖于上一个步骤的结果。仍然用 `setTimeout` 来模拟异步操作：
>
> ```javascript
> /**
>  * 传入参数 n，表示这个函数执行的时间（毫秒）
>  * 执行的结果是 n + 200，这个值将用于下一步骤
>  */
> function takeLongTime(n) {
>     return new Promise(resolve => {
>         setTimeout(() => resolve(n + 200), n);
>     });
> }
> function step1(n) {
>     console.log(`step1 with ${n}`);
>     return takeLongTime(n);
> }
> function step2(n) {
>     console.log(`step2 with ${n}`);
>     return takeLongTime(n);
> }
> function step3(n) {
>     console.log(`step3 with ${n}`);
>     return takeLongTime(n);
> }
> ```
>
> 现在用 Promise 方式来实现这三个步骤的处理：
>
> ```javascript
> function doIt() {
>     console.time("doIt");
>     const time1 = 300;
>     step1(time1)
>         .then(time2 => step2(time2))
>         .then(time3 => step3(time3))
>         .then(result => {
>             console.log(`result is ${result}`);
>             console.timeEnd("doIt");
>         });
> }
> doIt();
> // c:\var\test>node --harmony_async_await .
> // step1 with 300
> // step2 with 500
> // step3 with 700
> // result is 900
> // doIt: 1507.251ms
> ```
>
> 输出结果 `result` 是 `step3()` 的参数 `700 + 200` = `900`。`doIt()` 顺序执行了三个步骤，一共用了 `300 + 500 + 700 = 1500` 毫秒，和 `console.time()/console.timeEnd()` 计算的结果一致。
>
> 如果用 async/await 来实现呢，会是这样：
>
> ```javascript
> async function doIt() {
>     console.time("doIt");
>     const time1 = 300;
>     const time2 = await step1(time1);
>     const time3 = await step2(time2);
>     const result = await step3(time3);
>     console.log(`result is ${result}`);
>     console.timeEnd("doIt");
> }
> doIt();
> ```
>
> 结果和之前的 Promise 实现是一样的，但是这个代码看起来是不是清晰得多，几乎跟同步代码一样

### 10. async/await对比Promise的优势

> * 代码读起来更加同步，Promise虽然摆脱了回调地狱，但是then的链式调⽤也会带来额外的阅读负担
>
> * Promise传递中间值⾮常麻烦，⽽async/await⼏乎是同步的写法，⾮常优雅
>
> * 错误处理友好，async/await可以⽤成熟的try/catch，Promise的错误捕获⾮常冗余
>
> * 调试友好，Promise的调试很差，由于没有代码块，你不能在⼀个返回表达式的箭头函数中设置断点，如果你在⼀个.then代码块中使⽤调试器的步进(step-over)功能，调试器并不会进⼊后续的.then代码块，因为调试器只能跟踪同步代码的每⼀步。

### 11. async/await 如何捕获异常

> ```js
> async function fn(){
>     try{
>         let a = await Promise.reject('error')
>     }catch(error){
>         console.log(error)
>     }
> }
> ```

### 12. 什么是回调函数？回调函数有什么缺点？如何解决回调地狱问题？

> 以下代码就是一个回调函数的例子：
>
> ```javascript
> ajax(url, () => {
>     // 处理逻辑
> })
> ```
>
> 回调函数有一个致命的弱点，就是容易写出回调地狱（Callback hell）。假设多个请求存在依赖性，可能会有如下代码：
>
> ```javascript
> ajax(url, () => {
>     // 处理逻辑
>     ajax(url1, () => {
>         // 处理逻辑
>         ajax(url2, () => {
>             // 处理逻辑
>         })
>     })
> })
> ```
>
> 以上代码看起来不利于阅读和维护，当然，也可以把函数分开来写：
>
> ```javascript
> function firstAjax() {
>   ajax(url1, () => {
>     // 处理逻辑
>     secondAjax()
>   })
> }
> function secondAjax() {
>   ajax(url2, () => {
>     // 处理逻辑
>   })
> }
> ajax(url, () => {
>   // 处理逻辑
>   firstAjax()
> })
> ```
>
> 以上的代码虽然看上去利于阅读了，但是还是没有解决根本问题。回调地狱的根本问题就是：
>
> 1. 嵌套函数存在耦合性，一旦有所改动，就会牵一发而动全身
> 2. 嵌套函数一多，就很难处理错误
>
> 当然，回调函数还存在着别的几个缺点，比如不能使用 `try catch` 捕获错误，不能直接 `return`。

### 13. setTimeout、setInterval、requestAnimationFrame 各有什么特点？

> 异步编程当然少不了定时器了，常见的定时器函数有 `setTimeout`、`setInterval`、`requestAnimationFrame`。最常用的是`setTimeout`，很多人认为 `setTimeout` 是延时多久，那就应该是多久后执行。
>
> 其实这个观点是错误的，因为 JS 是单线程执行的，如果前面的代码影响了性能，就会导致 `setTimeout` 不会按期执行。当然了，可以通过代码去修正 `setTimeout`，从而使定时器相对准确：
>
> ```javascript
> let period = 60 * 1000 * 60 * 2
> let startTime = new Date().getTime()
> let count = 0
> let end = new Date().getTime() + period
> let interval = 1000
> let currentInterval = interval
> function loop() {
> count++
> // 代码执行所消耗的时间
> let offset = new Date().getTime() - (startTime + count * interval);
> let diff = end - new Date().getTime()
> let h = Math.floor(diff / (60 * 1000 * 60))
> let hdiff = diff % (60 * 1000 * 60)
> let m = Math.floor(hdiff / (60 * 1000))
> let mdiff = hdiff % (60 * 1000)
> let s = mdiff / (1000)
> let sCeil = Math.ceil(s)
> let sFloor = Math.floor(s)
> // 得到下一次循环所消耗的时间
> currentInterval = interval - offset 
> console.log('时：'+h, '分：'+m, '毫秒：'+s, '秒向上取整：'+sCeil, '代码执行时间：'+offset, '下次循环间隔'+currentInterval) 
> setTimeout(loop, currentInterval)
> }
> setTimeout(loop, currentInterval)
> ```
>
> 接下来看 `setInterval`，其实这个函数作用和 `setTimeout` 基本一致，只是该函数是每隔一段时间执行一次回调函数。
>
> 通常来说不建议使用 `setInterval`。第一，它和 `setTimeout` 一样，不能保证在预期的时间执行任务。第二，它存在执行累积的问题，请看以下伪代码
>
> ```javascript
> function demo() {
> setInterval(function(){
>  console.log(2)
> },1000)
> sleep(2000)
> }
> demo()
> ```
>
> 以上代码在浏览器环境中，如果定时器执行过程中出现了耗时操作，多个回调函数会在耗时操作结束以后同时执行，这样可能就会带来性能上的问题。
>
> 如果有循环定时器的需求，其实完全可以通过 `requestAnimationFrame` 来实现：
>
> ```javascript
> function setInterval(callback, interval) {
> let timer
> const now = Date.now
> let startTime = now()
> let endTime = startTime
> const loop = () => {
>  timer = window.requestAnimationFrame(loop)
>  endTime = now()
>  if (endTime - startTime >= interval) {
>    startTime = endTime = now()
>    callback(timer)
>  }
> }
> timer = window.requestAnimationFrame(loop)
> return timer
> }
> let a = 0
> setInterval(timer => {
> console.log(1)
> a++
> if (a === 3) cancelAnimationFrame(timer)
> }, 1000)
> ```
>
> 首先 `requestAnimationFrame` 自带函数节流功能，基本可以保证在 16.6 毫秒内只执行一次（不掉帧的情况下），并且该函数的延时效果是精确的，没有其他定时器时间不准的问题，当然你也可以通过该函数来实现 `setTimeout`。

### 14.Async/Await 如何通过同步的方式实现异步

> Async/Await就是一个**自执行**的generate函数。利用generate函数的特性把异步的代码写成“同步”的形式,第一个请求的返回值作为后面一个请求的参数,其中每一个参数都是一个promise对象.

## 八、面向对象

### 1. 对象创建的方式有哪些？

> 一般使用字面量的形式直接创建对象，但是这种创建方式对于创建大量相似对象的时候，会产生大量的重复代码。但 js和一般的面向对象的语言不同，在 ES6 之前它没有类的概念。但是可以使用函数来进行模拟，从而产生出可复用的对象创建方式，常见的有以下几种：
>
> （1）第一种是工厂模式，工厂模式的主要工作原理是用函数来封装创建对象的细节，从而通过调用函数来达到复用的目的。但是它有一个很大的问题就是创建出来的对象无法和某个类型联系起来，它只是简单的封装了复用代码，而没有建立起对象和类型间的关系。
>
> （2）第二种是构造函数模式。js 中每一个函数都可以作为构造函数，只要一个函数是通过 new 来调用的，那么就可以把它称为构造函数。执行构造函数首先会创建一个对象，然后将对象的原型指向构造函数的 prototype 属性，然后将执行上下文中的 this 指向这个对象，最后再执行整个函数，如果返回值不是对象，则返回新建的对象。因为 this 的值指向了新建的对象，因此可以使用 this 给对象赋值。构造函数模式相对于工厂模式的优点是，所创建的对象和构造函数建立起了联系，因此可以通过原型来识别对象的类型。但是构造函数存在一个缺点就是，造成了不必要的函数对象的创建，因为在 js 中函数也是一个对象，因此如果对象属性中如果包含函数的话，那么每次都会新建一个函数对象，浪费了不必要的内存空间，因为函数是所有的实例都可以通用的。
>
> （3）第三种模式是原型模式，因为每一个函数都有一个 prototype 属性，这个属性是一个对象，它包含了通过构造函数创建的所有实例都能共享的属性和方法。因此可以使用原型对象来添加公用属性和方法，从而实现代码的复用。这种方式相对于构造函数模式来说，解决了函数对象的复用问题。但是这种模式也存在一些问题，一个是没有办法通过传入参数来初始化值，另一个是如果存在一个引用类型如 Array 这样的值，那么所有的实例将共享一个对象，一个实例对引用类型值的改变会影响所有的实例。
>
> （4）第四种模式是组合使用构造函数模式和原型模式，这是创建自定义类型的最常见方式。因为构造函数模式和原型模式分开使用都存在一些问题，因此可以组合使用这两种模式，通过构造函数来初始化对象的属性，通过原型对象来实现函数方法的复用。这种方法很好的解决了两种模式单独使用时的缺点，但是有一点不足的就是，因为使用了两种不同的模式，所以对于代码的封装性不够好。
>
> （5）第五种模式是动态原型模式，这一种模式将原型方法赋值的创建过程移动到了构造函数的内部，通过对属性是否存在的判断，可以实现仅在第一次调用函数时对原型对象赋值一次的效果。这一种方式很好地对上面的混合模式进行了封装。
>
> （6）第六种模式是寄生构造函数模式，这一种模式和工厂模式的实现基本相同，我对这个模式的理解是，它主要是基于一个已有的类型，在实例化时对实例化的对象进行扩展。这样既不用修改原来的构造函数，也达到了扩展对象的目的。它的一个缺点和工厂模式一样，无法实现对象的识别。

### 2. 对象继承的方式有哪些？

> 1）第一种是以原型链的方式来实现继承，但是这种实现方式存在的缺点是，在包含有引用类型的数据时，会被所有的实例对象所共享，容易造成修改的混乱。还有就是在创建子类型的时候不能向超类型传递参数。
>
> （2）第二种方式是使用借用构造函数的方式，这种方式是通过在子类型的函数中调用超类型的构造函数来实现的，这一种方法解决了不能向超类型传递参数的缺点，但是它存在的一个问题就是无法实现函数方法的复用，并且超类型原型定义的方法子类型也没有办法访问到。
>
> （3）第三种方式是组合继承，组合继承是将原型链和借用构造函数组合起来使用的一种方式。通过借用构造函数的方式来实现类型的属性的继承，通过将子类型的原型设置为超类型的实例来实现方法的继承。这种方式解决了上面的两种模式单独使用时的问题，但是由于我们是以超类型的实例来作为子类型的原型，所以调用了两次超类的构造函数，造成了子类型的原型中多了很多不必要的属性。
>
> （4）第四种方式是原型式继承，原型式继承的主要思路就是基于已有的对象来创建新的对象，实现的原理是，向函数中传入一个对象，然后返回一个以这个对象为原型的对象。这种继承的思路主要不是为了实现创造一种新的类型，只是对某个对象实现一种简单继承，ES5 中定义的 Object.create() 方法就是原型式继承的实现。缺点与原型链方式相同。
>
> （5）第五种方式是寄生式继承，寄生式继承的思路是创建一个用于封装继承过程的函数，通过传入一个对象，然后复制一个对象的副本，然后对象进行扩展，最后返回这个对象。这个扩展的过程就可以理解是一种继承。这种继承的优点就是对一个简单对象实现继承，如果这个对象不是自定义类型时。缺点是没有办法实现函数的复用。
>
> （6）第六种方式是寄生式组合继承，组合继承的缺点就是使用超类型的实例做为子类型的原型，导致添加了不必要的原型属性。寄生式组合继承的方式是使用超类型的原型的副本来作为子类型的原型，这样就避免了创建不必要的属性。

### 3.new运算符的实现机制

> 1. 首先创建了一个新的`空对象`
> 2. `设置原型`，将对象的原型设置为函数的`prototype`对象。
> 3. 让函数的`this`指向这个对象，执行构造函数的代码（为这个新对象添加属性）
> 4. 判断函数的返回值类型，如果是值类型，返回创建的对象。如果是引用类型，就返回这个引用类型的对象。

## 九、垃圾回收与内存泄漏

### 1. 浏览器的垃圾回收机制

> #### （1）垃圾回收的概念
>
> **垃圾回收**：JavaScript代码运行时，需要**分配内存空间来储存变量和值**。当变量不在参与运行时，就需要系统收回被占用的内存空间，这就是垃圾回收。
>
> **回收机制**：
>
> - Javascript 具有**自动垃圾回收机制，会定期对那些不再使用的变量、对象所占用的内存进行释放**，原理就是找到不再使用的变量，然后释放掉其占用的内存。
> - JavaScript中存在两种变量：局部变量和全局变量。全局变量的生命周期会持续要页面卸载；而局部变量声明在函数中，它的生命周期从函数执行开始，直到函数执行结束，在这个过程中，局部变量会在堆或栈中存储它们的值，当函数执行结束后，这些局部变量不再被使用，它们所占有的空间就会被释放。
> - 不过，当局部变量被外部函数使用时，其中一种情况就是闭包，在函数执行结束后，函数外部的变量依然指向函数内部的局部变量，此时局部变量依然在被使用，所以不会回收。
>
> #### （2）垃圾回收的方式
>
> 浏览器通常使用的垃圾回收方法有两种：标记清除，引用计数。 **1）标记清除**
>
> - 标记清除是浏览器常见的垃圾回收方式，当变量进入执行环境时，**就标记这个变量“进入环境”，被标记为“进入环境”的变量是不能被回收的**，因为他们正在被使用。当变量离开环境时，就会被**标记为“离开环境”，被标记为“离开环境”的变量会被内存释放**。
> - 垃圾收集器在运行的时候会给存储在内存中的所有变量都加上标记。然后，它会去掉环境中的变量以及被环境中的变量引用的标记。而在此之后再被加上标记的变量将被视为准备删除的变量，原因是环境中的变量已经无法访问到这些变量了。最后。垃圾收集器完成内存清除工作，销毁那些带标记的值，并回收他们所占用的内存空间。
>
> **2）引用计数**
>
> - 另外一种垃圾回收机制就是引用计数，这个用的相对较少。引用计数就是跟踪记录每个值被引用的次数。当声明了一个变量并将一个引用类型赋值给该变量时，则这个值的引用次数就是1。相反，如果包含对这个值引用的变量又取得了另外一个值，则这个值的引用次数就减1。当这个引用次数变为0时，说明这个变量已经没有价值，因此，在在机回收期下次再运行时，这个变量所占有的内存空间就会被释放出来。
> - 这种方法会引起**循环引用**的问题：例如：` obj1`和`obj2`通过属性进行相互引用，两个对象的引用次数都是2。当使用循环计数时，由于函数执行完后，两个对象都离开作用域，函数执行结束，`obj1`和`obj2`还将会继续存在，因此它们的引用次数永远不会是0，就会引起循环引用。
>
> ```javascript
> function fun() {
>     let obj1 = {};
>     let obj2 = {};
>     obj1.a = obj2; // obj1 引用 obj2
>     obj2.a = obj1; // obj2 引用 obj1
> }
> ```
> 
>这种情况下，就要手动释放变量占用的内存：
> 
>```javascript
> obj1.a =  null
>  obj2.a =  null
> ```
> 
> #### （3）减少垃圾回收
>
> 虽然浏览器可以进行垃圾自动回收，但是当代码比较复杂时，垃圾回收所带来的代价比较大，所以应该尽量减少垃圾回收。
>
> - **对数组进行优化：** 在清空一个数组时，最简单的方法就是给其赋值为[ ]，但是与此同时会创建一个新的空对象，可以将数组的长度设置为0，以此来达到清空数组的目的。
>- **对**`object`**进行优化：** 对象尽量复用，对于不再使用的对象，就将其设置为null，尽快被回收。
> - **对函数进行优化：** 在循环中的函数表达式，如果可以复用，尽量放在函数的外面。

### 2. 哪些情况会导致内存泄漏

> 以下四种情况会造成内存的泄漏：
>
> - **意外的全局变量：** 由于使用未声明的变量，而意外的创建了一个全局变量，而使这个变量一直留在内存中无法被回收。
> - **被遗忘的计时器或回调函数：** 设置了 setInterval 定时器，而忘记取消它，如果循环函数有对外部变量的引用的话，那么这个变量会被一直留在内存中，而无法被回收。
> - **脱离 DOM 的引用：** 获取一个 DOM 元素的引用，而后面这个元素被删除，由于一直保留了对这个元素的引用，所以它也无法被回收。
> - **闭包：** 不合理的使用闭包，从而导致某些变量一直被留在内存当中。

## 十、Dom

### 1.事件简介

> **事件** 是某事发生的信号。所有的 DOM 节点都生成这样的信号（但事件不仅限于 DOM）。
>
> 这是最有用的 DOM 事件的列表，你可以浏览一下：
>
> **鼠标事件：**
>
> - `click` —— 当鼠标点击一个元素时（触摸屏设备会在点击时生成）。
> - `contextmenu` —— 当鼠标右键点击一个元素时。
> - `mouseover` / `mouseout` —— 当鼠标指针移入/离开一个元素时。
> - `mousedown` / `mouseup` —— 当在元素上按下/释放鼠标按钮时。
> - `mousemove` —— 当鼠标移动时。
>
> **键盘事件**：
>
> - `keydown` 和 `keyup` —— 当按下和松开一个按键时。
>
> **表单（form）元素事件**：
>
> - `submit` —— 当访问者提交了一个 `<form>` 时。
> - `focus` —— 当访问者聚焦于一个元素时，例如聚焦于一个 `<input>`。
>
> **Document 事件**：
>
> - `DOMContentLoaded` —— 当 HTML 的加载和处理均完成，DOM 被完全构建完成时。
>
> **CSS 事件**：
>
> - `transitionend` —— 当一个 CSS 动画完成时。
>
> 还有很多其他事件。我们将在下一章中详细介绍具体事件。

> #### 事件处理程序
>
> 三种：
>
> 1. HTML 特性（attribute）：`onclick="..."`。
> 2. DOM 属性（property）：`elem.onclick = function`。
> 3. 方法（method）：`elem.addEventListener(event, handler[, phase])` 用于添加，`removeEventListener` 用于移除。
>
> HTML 特性很少使用，因为 HTML 标签中的 JavaScript 看起来有些奇怪且陌生。而且也不能在里面写太多代码。
>
> DOM 属性用起来还可以，但我们无法为特定事件分配多个处理程序。在许多场景中，这种限制并不严重。
>
> 最后一种方式是最灵活的，但也是写起来最长的。有少数事件只能使用这种方式。例如 `transtionend` 和 `DOMContentLoaded`（上文中讲到了）。`addEventListener` 也支持对象作为事件处理程序。在这种情况下，如果发生事件，则会调用 `handleEvent` 方法。
>
> 无论你如何分类处理程序 —— 它都会将获得一个事件对象作为第一个参数。该对象包含有关所发生事件的详细信息。
>
> ##### addEventListener语法：
>
> ```javascript
> element.addEventListener(event, handler[, options]);
> ```
>
> - `event`
>
>   事件名，例如：`"click"`。
>
> - `handler`
>
>   处理程序。
>
> - `options`
>
>   具有以下属性的附加可选对象：`once`：如果为 `true`，那么会在被触发后自动删除监听器。`capture`：事件处理的阶段。由于历史原因，`options` 也可以是 `false/true`，它与 `{capture: false/true}` 相同。`passive`：如果为 `true`，那么处理程序将不会调用 `preventDefault()`。
>
> 要移除处理程序，可以使用 `removeEventListener`：
>
> ```javascript
> element.removeEventListener(event, handler[, options]);
> ```

> #### 访问元素：this
>
> 处理程序中的 `this` 的值是对应的元素。就是处理程序所在的那个元素。
>
> 下面这行代码中的 `button` 使用 `this.innerHTML` 来显示它的内容：
>
> ```html
> <button onclick="alert(this.innerHTML)">Click me</button>
> ```

> ##### 事件对象
>
> 为了正确处理事件，我们需要更深入地了解发生了什么。不仅仅是 “click” 或 “keydown”，还包括鼠标指针的坐标是什么？按下了哪个键？等等。
>
> 当事件发生时，浏览器会创建一个 **`event` 对象**，将详细信息放入其中，并将其作为参数传递给处理程序。
>
> 下面是一个从 `event` 对象获取鼠标指针的坐标的示例：
>
> ```html
> <input type="button" value="Click me" id="elem">
> 
> <script>
>   elem.onclick = function(event) {
>     // 显示事件类型、元素和点击的坐标
>     alert(event.type + " at " + event.currentTarget);
>     alert("Coordinates: " + event.clientX + ":" + event.clientY);
>   };
> </script>
> ```
>
> `event` 对象的一些属性：
>
> - `event.type`
>
>   事件类型，这里是 `"click"`。
>
> - `event.currentTarget`
>
>   处理事件的元素。这与 `this` 相同，除非处理程序是一个箭头函数，或者它的 `this` 被绑定到了其他东西上，之后我们就可以从 `event.currentTarget` 获取元素了。
>
> - `event.clientX / event.clientY`
>
>   指针事件（pointer event）的指针的窗口相对坐标。
>
> 还有很多属性。其中很多都取决于事件类型：键盘事件具有一组属性，指针事件具有另一组属性，稍后我们将详细讨论不同事件，那时我们再对其进行详细研究。
>
> **`event` 对象在 HTML 处理程序中也可用**
>
> 如果我们在 HTML 中分配了一个处理程序，那么我们也可以使用 `event` 对象，像这样：
>
> ```markup
> <input type="button" onclick="alert(event.type)" value="Event type">
> ```

### 2.冒泡和捕获

> ####  冒泡
>
> 冒泡（bubbling）原理很简单。
>
> **当一个事件发生在一个元素上，它会首先运行在该元素上的处理程序，然后运行其父元素上的处理程序，然后一直向上到其他祖先上的处理程序。**
>
> 假设我们有 3 层嵌套 `FORM > DIV > P`，它们各自拥有一个处理程序：
>
> ```html
> <style>
>   body * {
>     margin: 10px;
>     border: 1px solid blue;
>   }
> </style>
> 
> <form onclick="alert('form')">FORM
>   <div onclick="alert('div')">DIV
>     <p onclick="alert('p')">P</p>
>   </div>
> </form>
> ```
>
> 点击内部的 `<p>` 会首先运行 `onclick`：
>
> 1. 在该 `<p>` 上的。
> 2. 然后是外部 `<div>` 上的。
> 3. 然后是外部 `<form>` 上的。
> 4. 以此类推，直到最后的 `document` 对象。

> 这个过程被称为“冒泡（bubbling）”，因为事件从内部元素“冒泡”到所有父级，就像在水里的气泡一样。
>
> ***\*几乎\** 所有事件都会冒泡。**
>
> 这句话中的关键词是“几乎”。
>
> 例如，`focus` 事件不会冒泡。同样，我们以后还会遇到其他例子。但这仍然是例外，而不是规则，大多数事件的确都是冒泡的。

> #### event.target
>
> 父元素上的处理程序始终可以获取事件实际发生位置的详细信息。
>
> **引发事件的那个嵌套层级最深的元素被称为目标元素,可以通过 `event.target` 访问。**
>
> 注意与 `this`（=`event.currentTarget`）之间的区别：
>
> - `event.target` —— 是引发事件的“目标”元素，它在冒泡过程中不会发生变化。
> - `this` —— 是“当前”元素，其中有一个当前正在运行的处理程序。
>
> 例如，如果我们有一个处理程序 `form.onclick`，那么它可以“捕获”表单内的所有点击。无论点击发生在哪里，它都会冒泡到 `<form>` 并运行处理程序。
>
> 在 `form.onclick` 处理程序中：
>
> - `this`（=`event.currentTarget`）是 `<form>` 元素，因为处理程序在它上面运行。
> - `event.target` 是表单中实际被点击的元素。

> #### 停止冒泡
>
> 冒泡事件从目标元素开始向上冒泡。通常，它会一直上升到 `<html>`，然后再到 `document` 对象，有些事件甚至会到达 `window`，它们会调用路径上所有的处理程序。
>
> 但是任意处理程序都可以决定事件已经被完全处理，并停止冒泡。
>
> 用于停止冒泡的方法是 `event.stopPropagation()`。
>
> 例如，如果你点击 `<button>`，这里的 `body.onclick` 不会工作：
>
> ```html
> <body onclick="alert(`the bubbling doesn't reach here`)">
>   <button onclick="event.stopPropagation()">Click me</button>
> </body>
> ```
>
> **event.stopImmediatePropagation()**
>
> 如果一个元素在一个事件上有多个处理程序，即使其中一个停止冒泡，其他处理程序仍会执行。
>
> 换句话说，`event.stopPropagation()` 停止向上移动，但是当前元素上的其他处理程序都会继续运行。
>
> 有一个 `event.stopImmediatePropagation()` 方法，可以用于停止冒泡，并阻止当前元素上的处理程序运行。使用该方法之后，其他处理程序就不会被执行。

> #### 捕获
>
> 事件处理的另一个阶段被称为“捕获（capturing）”。它很少被用在实际开发中，但有时是有用的。
>
> DOM 事件标准描述了事件传播的 3 个阶段：
>
> 1. 捕获阶段（Capturing phase）—— 事件（从 Window）向下走近元素。
> 2. 目标阶段（Target phase）—— 事件到达目标元素。
> 3. 冒泡阶段（Bubbling phase）—— 事件从元素上开始冒泡。

> 为了在捕获阶段捕获事件，我们需要将处理程序的 `capture` 选项设置为 `true`：
>
> ```javascript
> elem.addEventListener(..., {capture: true})
> // 或者，用 {capture: true} 的别名 "true"
> elem.addEventListener(..., true)
> ```
>
> `capture` 选项有两个可能的值：
>
> - 如果为 `false`（默认值），则在冒泡阶段设置处理程序。
>
> - 如果为 `true`，则在捕获阶段设置处理程序。
>
> - 让我们来看看捕获和冒泡：
>
>   ```html
>   <form>FORM
>     <div>DIV
>       <p>P</p>
>     </div>
>   </form>
>     
>   <script>
>     for(let elem of document.querySelectorAll('*')) {
>       elem.addEventListener("click", e => alert(`Capturing: ${elem.tagName}`), true);
>       elem.addEventListener("click", e => alert(`Bubbling: ${elem.tagName}`));
>     }
>   </script>
>   ```
>
>   上面这段代码为文档中的 **每个** 元素都设置了点击处理程序，以查看哪些元素上的点击事件处理程序生效了。
>
>   如果你点击了 `<p>`，那么顺序是：
>
>   1. `HTML` → `BODY` → `FORM` → `DIV`（捕获阶段第一个监听器）：
>   2. `P`（目标阶段，触发两次，因为我们设置了两个监听器：捕获和冒泡）
>   3. `DIV` → `FORM` → `BODY` → `HTML`（冒泡阶段，第二个监听器）。
>
>   有一个属性 `event.eventPhase`，它告诉我们捕获事件的阶段数。但它很少被使用，因为我们通常是从处理程序中了解到它。

### 3.事件委托

> 捕获和冒泡允许我们实现一种被称为 **事件委托** 的强大的事件处理模式。
>
> 这个想法是，如果我们有许多以类似方式处理的元素，那么就不必为每个元素分配一个处理程序 —— 而是将单个处理程序放在它们的共同祖先上。
>
> 在处理程序中，我们获取 `event.target` 以查看事件实际发生的位置并进行处理。

> **我们的任务是在点击时高亮显示被点击的单元格 `<td>`。**
>
> 与其为每个 `<td>`（可能有很多）分配一个 `onclick` 处理程序 —— 我们可以在 `<table>` 元素上设置一个“捕获所有”的处理程序。
>
> 它将使用 `event.target` 来获取点击的元素并高亮显示它。
>
> ```js
> let selectedTd;
> 
> table.onclick = function(event) {
>   let td = event.target.closest('td'); // (1)
> 
>   if (!td) return; // (2)
> 
>   if (!table.contains(td)) return; // (3)
> 
>   highlight(td); // (4)
> };
> 
> function highlight(td) {
>   if (selectedTd) { // 移除现有的高亮显示，如果有的话
>     selectedTd.classList.remove('highlight');
>   }
>   selectedTd = td;
>   selectedTd.classList.add('highlight'); // 高亮显示新的 td
> }
> ```

## 十一.其他杂项

### 1.异或运算

> XOR是基于二进制的位运算。用`^`符号表示。如果两个操作数相同，结果为0，否则结果为1。这其实是一种排他性的或运算。 只有一个参数是1时，最终结果才会是1
>
> ### 异或运算真值表
>
> | **x** | **y** | **x ^ y** |
> | ----- | ----- | --------- |
> | 0     | 0     | 0         |
> | 0     | 1     | 1         |
> | 1     | 0     | 1         |
> | 1     | 1     | 0         |
>
> 大多数编程语言将`^`作为位运算符来实现，这意味着XOR将其操作数当作32位的比特序列（由0和1组成）而不是十进制、十六进制或八进制数值。

> ## 异或运算定律
>
> 我们可以从前面的定义推导出一些特性，然后运用这些特性去解决面试题。一个个来，别着急！
>
> ### 1. 一个值与0的运算，等于其本身
>
> ```plain
> x ^ 0 = x
> ```
>
> 可以从真值表第1、2、3行看出来。
>
> ### 2. 同值异或得0
>
> ```plain
> x ^ x = 0
> ```
>
> 可以从真值表1、4行看出,当x = y 时，结果时0。
>
> ### 3. x与 -1的运算，等于x的反码
>
> ```plain
> x ^ -1 = ~x
> ```
>
> ### 4. 可交换性
>
> ```plain
> x ^ y = y ^ x
> ```
>
> ### 5. 结合性
>
> ```
> x ^ (y ^ z) = (x ^ y) ^ z
> ```

### 2.BigInt

> `BigInt` 是一种特殊的数字类型，它提供了对任意长度整数的支持。创建 bigint 的方式有两种：在一个整数字面量后面加 `n` 或者调用 `BigInt` 函数，该函数从字符串、数字等中生成 bigint。
>
> ```javascript
> const bigint = 1234567890123456789012345678901234567890n;
> 
> const sameBigint = BigInt("1234567890123456789012345678901234567890");
> 
> const bigintFromNumber = BigInt(10); // 与 10n 相同
> ```

> #### 数学运算符
>
> `BigInt` 大多数情况下可以像常规数字类型一样使用，例如：
>
> ```javascript
> alert(1n + 2n); // 3
> 
> alert(5n / 2n); // 2
> ```
>
> 请注意：除法 `5/2` 的结果向零进行舍入，舍入后得到的结果没有了小数部分。对 bigint 的所有操作，返回的结果也是 bigint。
>
> 我们不可以把 bigint 和常规数字类型混合使用：
>
> ```javascript
> alert(1n + 2); // Error: Cannot mix BigInt and other types
> ```
>
> 如果有需要，我们应该显式地转换它们：使用 `BigInt()` 或者 `Number()`，像这样：
>
> ```javascript
> let bigint = 1n;
> let number = 2;
> 
> // 将 number 转换为 bigint
> alert(bigint + BigInt(number)); // 3
> 
> // 将 bigint 转换为 number
> alert(Number(bigint) + number); // 3
> ```
>
> 转换操作始终是静默的，绝不会报错，但是如果 bigint 太大而数字类型无法容纳，则会截断多余的位，因此我们应该谨慎进行此类转换。
>
> **BigInt 不支持一元加法**
>
> 一元加法运算符 `+value`，是大家熟知的将 `value` 转换成数字类型的方法。
>
> 为了避免混淆，在 bigint 中不支持一元加法：
>
> ```javascript
> let bigint = 1n;
> 
> alert( +bigint ); // error
> ```
>
> 所以我们应该用 `Number()` 来将一个 bigint 转换成一个数字类型。
>
> #### 比较运算符
>
> 比较运算符，例如 `<` 和 `>`，使用它们来对 bigint 和 number 类型的数字进行比较没有问题：
>
> ```javascript
> alert( 2n > 1n ); // true
> 
> alert( 2n > 1 ); // true
> ```
>
> 但是请注意，由于 number 和 bigint 属于不同类型，它们可能在进行 `==` 比较时相等，但在进行 `===`（严格相等）比较时不相等：
>
> ```javascript
> alert( 1 == 1n ); // true
> 
> alert( 1 === 1n ); // false
> ```
>
> #### 布尔运算
>
> 当在 `if` 或其他布尔运算中时，bigint 的行为类似于 number。
>
> 例如，在 `if` 中，bigint `0n` 为假，其他值为 `true`：
>
> ```javascript
> if (0n) {
>   // 永远不会执行
> }
> ```
>
> 布尔运算符，例如 `||`，`&&` 和其他运算符，处理 bigint 的方式也类似于 number：
>
> ```javascript
> alert( 1n || 2 ); // 1（1n 被认为是真）
> 
> alert( 0n || 2 ); // 2（0n 被认为是假）
> ```

### 3.JSON.stringify

> JSON.stringify()将值转换为相应的JSON格式：
>
> \- 转换值如果有 toJSON() 方法，该方法定义什么值将被序列化。
>
> \- 非数组对象的属性不能保证以特定的顺序出现在序列化后的字符串中。
>
> \- 布尔值、数字、字符串的包装对象在序列化过程中会自动转换成对应的原始值。
>
> \- undefined、任意的函数以及 symbol 值，在序列化过程中会被忽略（出现在非数组对象的属性值中时）或者被转换成 null（出现在数组中时）。
>
> -函数、undefined 被单独转换时，会返回 undefined，如JSON.stringify(function(){}) or JSON.stringify(undefined). 
>
> -对包含循环引用的对象（对象之间相互引用，形成无限循环）执行此方***抛出错误。
>
> \- 所有以 symbol 为属性键的属性都会被完全忽略掉，即便 replacer 参数中强制指定包含了它们。 
>
> -Date 日期调用了 toJSON() 将其转换为了 string 字符串（同Date.toISOString()），因此会被当做字符串处理。 
>
> -NaN 和 Infinity 格式的数值及 null 都会被当做 null。
> -其他类型的对象，包括 Map/Set/WeakMap/WeakSet，仅会序列化可枚举的属性。

### 4.split和join

> #### split
>
> `split`方法按照给定规则分割字符串，返回一个由分割出来的子字符串组成的数组。
>
> ```
> 'a|b|c'.split('|') // ["a", "b", "c"]
> ```
>
> 如果分割规则为空字符串，则返回数组的成员是原字符串的每一个字符。
>
> ```
> 'a|b|c'.split('') // ["a", "|", "b", "|", "c"]
> ```
>
> 如果省略参数，则返回数组的唯一成员就是原字符串。
>
> ```
> 'a|b|c'.split() // ["a|b|c"]
> ```
>
> 如果满足分割规则的两个部分紧邻着（即两个分割符中间没有其他字符），则返回数组之中会有一个空字符串。
>
> ```
> 'a||c'.split('|') // ['a', '', 'c']
> ```
>
> 如果满足分割规则的部分处于字符串的开头或结尾（即它的前面或后面没有其他字符），则返回数组的第一个或最后一个成员是一个空字符串。
>
> ```
> '|b|c'.split('|') // ["", "b", "c"]
> 'a|b|'.split('|') // ["a", "b", ""]
> ```
>
> `split`方法还可以接受第二个参数，限定返回数组的最大成员数。
>
> ```
> 'a|b|c'.split('|', 0) // []
> 'a|b|c'.split('|', 1) // ["a"]
> 'a|b|c'.split('|', 2) // ["a", "b"]
> 'a|b|c'.split('|', 3) // ["a", "b", "c"]
> 'a|b|c'.split('|', 4) // ["a", "b", "c"]
> ```
>
> 上面代码中，`split`方法的第二个参数，决定了返回数组的成员数。`split`方法还可以使用正则表达式作为参数，详见《正则表达式》一节。

> #### join
>
> `join()`方法以指定参数作为分隔符，将所有数组成员连接为一个字符串返回。如果不提供参数，默认用逗号分隔。
>
> ```
> var a = [1, 2, 3, 4];
> 
> a.join(' ') // '1 2 3 4'
> a.join(' | ') // "1 | 2 | 3 | 4"
> a.join() // "1,2,3,4"
> ```
>
> 如果数组成员是`undefined`或`null`或空位，会被转成空字符串。
>
> ```
> [undefined, null].join('#')
> // '#'
> 
> ['a',, 'b'].join('-')
> // 'a--b'
> ```
>
> 通过`call`方法，这个方法也可以用于字符串或类似数组的对象。
>
> ```
> Array.prototype.join.call('hello', '-')
> // "h-e-l-l-o"
> 
> var obj = { 0: 'a', 1: 'b', length: 2 };
> Array.prototype.join.call(obj, '-')
> // 'a-b'
> ```

