# React

## 一、组件基础

### 1. React 事件机制

> React并不是将click事件绑在该div的真实DOM上，而是`在document处监听所有支持的事件`，当事件发生并冒泡至document处时，React将事件内容封装并交由真正的处理函数运行。这样的方式不仅减少了内存消耗，还能在组件挂载销毁时统一订阅和移除事件。
>  另外冒泡到 document 上的事件也不是原生浏览器事件，而是 React 自己实现的合成事件（SyntheticEvent）。因此我们如果不想要事件冒泡的话，调用 event.stopPropagation 是无效的，而应该调用 `event.preventDefault`。
>
> ![react事件绑定原理](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/2089718f74b342869de15f01588f033f~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)

> `React`基于浏览器的事件机制自身实现了一套事件机制，包括事件注册、事件的合成、事件冒泡、事件派发等
>
> 在`React`中这套事件机制被称之为合成事件
>
> #### 合成事件（SyntheticEvent）
>
> 合成事件是 `React`模拟原生 `DOM`事件所有能力的一个事件对象，即浏览器原生事件的跨浏览器包装器
>
> 根据 `W3C`规范来定义合成事件，兼容所有浏览器，拥有与浏览器原生事件相同的接口，例如：
>
> ```jsx
> const button = <button onClick={handleClick}>按钮</button>
> ```
>
> 如果想要获得原生`DOM`事件，可以通过`e.nativeEvent`属性获取
>
> ```js
> const handleClick = (e) => console.log(e.nativeEvent);
> const button = <button onClick={handleClick}>按钮</button>
> ```
>
> 从上面可以看到`React`事件和原生事件也非常的相似，但也有一定的区别：
>
> - 事件名称命名方式不同
>
> ```jsx
> // 原生事件绑定方式
> <button onclick="handleClick()">按钮命名</button>
>       
> // React 合成事件绑定方式
> const button = <button onClick={handleClick}>按钮命名</button>
> ```
>
> - 事件处理函数书写不同
>
> ```jsx
> // 原生事件 事件处理函数写法
> <button onclick="handleClick()">按钮命名</button>
>       
> // React 合成事件 事件处理函数写法
> const button = <button onClick={handleClick}>按钮命名</button>
> ```
>
> 虽然`onclick`看似绑定到`DOM`元素上，但**实际并不会把事件代理函数直接绑定到真实的节点上**，而是把所有的事件绑定到结构的最外层，使用一个统一的事件去监听
>
> 这个事件监听器上维持了一个映射来保存所有组件内部的事件监听和处理函数。当组件挂载或卸载时，只是在这个统一的事件监听器上插入或删除一些对象
>
> 当事件发生时，首先被这个统一的事件监听器处理，然后在映射里找到真正的事件处理函数并调用。这样做简化了事件处理和回收机制，效率也有很大提升
>
> `React`事件机制总结如下：
>
> - React 上注册的事件最终会绑定在document这个 DOM 上，而不是 React 组件对应的 DOM(减少内存开销就是因为所有的事件都绑定在 document 上，其他节点没有绑定事件)
> - React 自身实现了一套事件冒泡机制，所以这也就是为什么我们 event.stopPropagation()无效的原因。
> - React 通过队列的形式，从触发的组件向父组件回溯，然后调用他们 JSX 中定义的 callback
> - React 有一套自己的合成事件 SyntheticEvent

> ```javascript
> <div onClick={this.handleClick.bind(this)}>点我</div>
> ```

> JSX 上写的事件并没有绑定在对应的真实 DOM 上，而是通过事件代理的方式，将所有的事件都统一绑定在了 `document` 上。这样的方式不仅减少了内存消耗，还能在组件挂载销毁时统一订阅和移除事件。
>
> 另外冒泡到 `document` 上的事件也不是原生浏览器事件，而是 React 自己实现的合成事件（SyntheticEvent）。因此我们如果不想要事件冒泡的话，调用 `event.stopPropagation` 是无效的，而应该调用 `event.preventDefault`。
>
> 实现合成事件的目的如下：
>
> - 合成事件首先抹平了浏览器之间的兼容问题，另外这是一个跨浏览器原生事件包装器，赋予了跨浏览器开发的能力；
> - 对于原生浏览器事件来说，浏览器会给监听器创建一个事件对象。如果你有很多的事件监听，那么就需要分配很多的事件对象，造成高额的内存分配问题。但是对于合成事件来说，有一个事件池专门来管理它们的创建和销毁，当事件需要被使用时，就会从池子中复用对象，事件回调结束后，就会销毁事件对象上的属性，从而便于下次复用事件对象。

### 2. React的事件和普通的HTML事件有什么不同？

> 区别：
>
> - 对于事件名称命名方式，原生事件为全小写，react 事件采用小驼峰；
> - 对于事件函数处理语法，原生事件为字符串，react 事件为函数；
> - react 事件不能采用 return false 的方式来阻止浏览器的默认行为，而必须要地明确地调用`preventDefault()`来阻止默认行为。
>
> 合成事件是 react 模拟原生 DOM 事件所有能力的一个事件对象，其优点如下：
>
> - 兼容所有浏览器，更好的跨平台；
> - 将事件统一存放在一个数组，避免频繁的新增与删除（垃圾回收）。
> - 方便 react 统一管理和事务机制。
>
> 事件的执行顺序为原生事件先执行，合成事件后执行，合成事件会冒泡绑定到 document 上，所以尽量避免原生事件与合成事件混用，如果原生事件阻止冒泡，可能会导致合成事件不执行，因为需要冒泡到document 上合成事件才会执行。

### 3. React 组件中怎么做事件代理？它的原理是什么？

> React基于Virtual DOM实现了一个SyntheticEvent层（合成事件层），定义的事件处理器会接收到一个合成事件对象的实例，它符合W3C标准，且与原生的浏览器事件拥有同样的接口，支持冒泡机制，所有的事件都自动绑定在最外层上。
>
> 在React底层，主要对合成事件做了两件事：
>
> - **事件委派：** React会把所有的事件绑定到结构的最外层，使用统一的事件监听器，这个事件监听器上维持了一个映射来保存所有组件内部事件监听和处理函数。
> - **自动绑定：** React组件中，每个方法的上下文都会指向该组件的实例，即自动绑定this为当前组件。

### 4. React 高阶组件、Render props、hooks 有什么区别，为什么要不断迭代

> 这三者是目前react**解决代码复用**的主要方式：
>
> - 高阶组件（HOC）是 React 中用于复用组件逻辑的一种高级技巧。HOC 自身不是 React API 的一部分，它是一种基于 React 的组合特性而形成的设计模式。具体而言，**高阶组件是参数为组件，返回值为新组件的函数**。
> - render props是指一种在 React 组件之间使用一个值为函数的 prop 共享代码的简单技术。更具体的说，render prop 是一个用于告知组件需要渲染什么内容的函数 prop。
> - 通常，render props 和高阶组件只渲染一个子节点。让 Hook 来服务这个使用场景更加简单。这两种模式仍有用武之地，（例如，一个虚拟滚动条组件或许会有一个 renderltem 属性，或是一个可见的容器组件或许会有它自己的 DOM 结构）。但在大部分场景下，Hook 足够了，并且能够帮助减少嵌套。
>
> **（1）HOC** 官方解释∶
>
> > 高阶组件（HOC）是 React 中用于复用组件逻辑的一种高级技巧。HOC 自身不是 React API 的一部分，它是一种基于 React 的组合特性而形成的设计模式。
>
> 简言之，HOC是一种组件的设计模式，HOC接受一个组件和额外的参数（如果需要），返回一个新的组件。HOC 是纯函数，没有副作用。
>
> ```javascript
> // hoc的定义
> function withSubscription(WrappedComponent, selectData) {
>   return class extends React.Component {
>     constructor(props) {
>       super(props);
>       this.state = {
>         data: selectData(DataSource, props)
>       };
>     }
>     // 一些通用的逻辑处理
>     render() {
>       // ... 并使用新数据渲染被包装的组件!
>       return <WrappedComponent data={this.state.data} {...this.props} />;
>     }
>   };
> 
> // 使用
> const BlogPostWithSubscription = withSubscription(BlogPost,
>   (DataSource, props) => DataSource.getBlogPost(props.id));
> ```
>
> HOC的优缺点∶
>
> - 优点∶ 逻辑服用、不影响被包裹组件的内部逻辑。
> - 缺点∶ hoc传递给被包裹组件的props容易和被包裹后的组件重名，进而被覆盖
>
> **（2）Render props** 官方解释∶
>
> > "render prop"是指一种在 React 组件之间使用一个值为函数的 prop 共享代码的简单技术
>
> 具有render prop 的组件接受一个返回React元素的函数，将render的渲染逻辑注入到组件内部。在这里，"render"的命名可以是任何其他有效的标识符。
>
> ```javascript
> // DataProvider组件内部的渲染逻辑如下
> class DataProvider extends React.Components {
>      state = {
>     name: 'Tom'
>   }
> 
>     render() {
>     return (
>         <div>
>           <p>共享数据组件自己内部的渲染逻辑</p>
>           { this.props.render(this.state) }
>       </div>
>     );
>   }
> }
> 
> // 调用方式
> <DataProvider render={data => (
>   <h1>Hello {data.name}</h1>
> )}/>
> ```
>
> 由此可以看到，render props的优缺点也很明显∶
>
> - 优点：数据共享、代码复用，将组件内的state作为props传递给调用者，将渲染逻辑交给调用者。
> - 缺点：无法在 return 语句外访问数据、嵌套写法不够优雅
>
> **（3）Hooks** 官方解释∶
>
> > Hook是 React 16.8 的新增特性。它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性。通过自定义hook，可以复用代码逻辑。
>
> ```javascript
> // 自定义一个获取订阅数据的hook
> function useSubscription() {
>   const data = DataSource.getComments();
>   return [data];
> }
> // 
> function CommentList(props) {
>   const {data} = props;
>   const [subData] = useSubscription();
>     ...
> }
> // 使用
> <CommentList data='hello' />
> ```
>
> 以上可以看出，hook解决了hoc的prop覆盖的问题，同时使用的方式解决了render props的嵌套地狱的问题。hook的优点如下∶
>
> - 使用直观；
> - 解决hoc的prop 重名问题；
> - 解决render props 因共享数据 而出现嵌套地狱的问题；
> - 能在return之外使用数据的问题。
>
> 需要注意的是：hook只能在组件顶层使用，不可在分支语句中使用。
>
> **总结∶** Hoc、render props和hook都是为了解决代码复用的问题，但是hoc和render props都有特定的使用场景和明显的缺点。hook是react16.8更新的新的API，让组件逻辑复用更简洁明了，同时也解决了hoc和render props的一些缺点。

### 5. 对React-Fiber的理解，它解决了什么问题？

> React V15 在渲染时，会**递归比对 VirtualDOM 树**，找出需要变动的节点，然后同步更新它们， 一气呵成。这个过程期间， React 会占据浏览器资源，这会导致用户触发的事件得不到响应，并且会导致掉帧，**导致用户感觉到卡顿**。
>
> 为了给用户制造一种应用很快的“假象”，不能让一个任务长期霸占着资源。 可以将浏览器的渲染、布局、绘制、资源加载(例如 HTML 解析)、事件响应、脚本执行视作操作系统的“进程”，需要通过某些调度策略合理地分配 CPU 资源，从而提高浏览器的用户响应速率, 同时兼顾任务执行效率。
>
> 所以 React 通过Fiber 架构，让这个执行过程变成可被中断。“适时”地让出 CPU 执行权，除了可以让浏览器及时地响应用户的交互，还有其他好处:
>
> - 分批延时对DOM进行操作，避免一次性操作大量 DOM 节点，可以得到更好的用户体验；
> - 给浏览器一点喘息的机会，它会对代码进行编译优化（JIT）及进行热代码优化，或者对 reflow 进行修正。
>
> **核心思想:** Fiber 也称协程或者纤程。它和线程并不一样，协程本身是没有并发或者并行能力的（需要配合线程），它只是一种控制流程的让出机制。让出 CPU 的执行权，让 CPU 能在这段时间执行其他的操作。渲染的过程可以被中断，可以将控制权交回浏览器，让位给高优先级的任务，浏览器空闲后再恢复渲染。

### 6. React.Component 和 React.PureComponent 的区别

> PureComponent表示一个纯组件，可以用来优化React程序，减少render函数执行的次数，从而提高组件的性能。
>
> 在React中，当prop或者state发生变化时，可以通过在shouldComponentUpdate生命周期函数中执行return false来阻止页面的更新，从而减少不必要的render执行。React.PureComponent会自动执行 shouldComponentUpdate。
>
> 不过，pureComponent中的 shouldComponentUpdate() 进行的是**浅比较**，也就是说如果是引用数据类型的数据，只会比较不是同一个地址，而不会比较这个地址里面的数据是否一致。浅比较会忽略属性和或状态突变情况，其实也就是数据引用指针没有变化，而数据发生改变的时候render是不会执行的。如果需要重新渲染那么就需要重新开辟空间引用数据。PureComponent一般会用在一些纯展示组件上。
>
> 使用pureComponent的**好处**：当组件更新时，如果组件的props或者state都没有改变，render函数就不会触发。省去虚拟DOM的生成和对比过程，达到提升性能的目的。这是因为react自动做了一层浅比较。

### 7. Component, Element, Instance 之间有什么区别和联系？

> - **元素：** 一个元素`element`是一个普通对象(plain object)，描述了对于一个DOM节点或者其他组件`component`，你想让它在屏幕上呈现成什么样子。元素`element`可以在它的属性`props`中包含其他元素(译注:用于形成元素树)。创建一个React元素`element`成本很低。元素`element`创建之后是不可变的。
> - **组件：** 一个组件`component`可以通过多种方式声明。可以是带有一个`render()`方法的类，简单点也可以定义为一个函数。这两种情况下，它都把属性`props`作为输入，把返回的一棵元素树作为输出。
> - **实例：** 一个实例`instance`是你在所写的组件类`component class`中使用关键字`this`所指向的东西(译注:组件实例)。它用来存储本地状态和响应生命周期事件很有用。
>
> 函数式组件(`Functional component`)根本没有实例`instance`。类组件(`Class component`)有实例`instance`，但是永远也不需要直接创建一个组件的实例，因为React帮我们做了这些。

### 8. React.createClass和extends Component的区别有哪些？

> React.createClass和extends Component的bai区别主要在于：
>
> **（1）语法区别**
>
> - createClass本质上是一个工厂函数，extends的方式更加接近最新的ES6规范的class写法。两种方式在语法上的差别主要体现在方法的定义和静态属性的声明上。
> - createClass方式的方法定义使用逗号，隔开，因为creatClass本质上是一个函数，传递给它的是一个Object；而class的方式定义方法时务必谨记不要使用逗号隔开，这是ES6 class的语法规范。
>
> **（2）propType 和 getDefaultProps**
>
> - React.createClass：通过proTypes对象和getDefaultProps()方法来设置和获取props.
> - React.Component：通过设置两个属性propTypes和defaultProps
>
> **（3）状态的区别**
>
> - React.createClass：通过getInitialState()方法返回一个包含初始值的对象
> - React.Component：通过constructor设置初始状态
>
> **（4）this区别**
>
> - React.createClass：会正确绑定this
> - React.Component：由于使用了 ES6，这里会有些微不同，属性并不会自动绑定到 React 类的实例上。
>
> **（5）Mixins**
>
> - React.createClass：使用 React.createClass 的话，可以在创建组件时添加一个叫做 mixins 的属性，并将可供混合的类的集合以数组的形式赋给 mixins。
> - 如果使用 ES6 的方式来创建组件，那么 `React mixins` 的特性将不能被使用了。

### 9. 对componentWillReceiveProps 的理解

> 该方法当`props`发生变化时执行，初始化`render`时不执行，在这个回调函数里面，你可以根据属性的变化，通过调用`this.setState()`来更新你的组件状态，旧的属性还是可以通过`this.props`来获取,这里调用更新状态是安全的，并不会触发额外的`render`调用。
>
> **使用好处：** 在这个生命周期中，可以在子组件的render函数执行前获取新的props，从而更新子组件自己的state。 可以将数据请求放在这里进行执行，需要传的参数则从componentWillReceiveProps(nextProps)中获取。而不必将所有的请求都放在父组件中。于是该请求只会在该组件渲染时才会发出，从而减轻请求负担。
>
> componentWillReceiveProps在初始化render的时候不会执行，它会在Component接受到新的状态(Props)时被触发，一般用于父组件状态更新时子组件的重新渲染。

### 10. 哪些方法会触发 React 重新渲染？重新渲染 render 会做些什么？

> **（1）哪些方法会触发 react 重新渲染?**
>
> - **setState（）方法被调用**
>
> setState 是 React 中最常用的命令，通常情况下，执行 setState 会触发 render。但是这里有个点值得关注，执行 setState 的时候不一定会重新渲染。当 setState 传入 null 时，并不会触发 render。
>
> ```javascript
> class App extends React.Component {
>   state = {
>     a: 1
>   };
> 
>   render() {
>     console.log("render");
>     return (
>       <React.Fragement>
>         <p>{this.state.a}</p>
>         <button
>           onClick={() => {
>             this.setState({ a: 1 }); // 这里并没有改变 a 的值
>           }}
>         >
>           Click me
>         </button>
>         <button onClick={() => this.setState(null)}>setState null</button>
>         <Child />
>       </React.Fragement>
>     );
>   }
> }
> ```
>
> - **父组件重新渲染**
>
> 只要父组件重新渲染了，即使传入子组件的 props 未发生变化，那么子组件也会重新渲染，进而触发 render
>
> **（2）重新渲染 render 会做些什么?**
>
> - 会对新旧 VNode 进行对比，也就是我们所说的Diff算法。
> - 对新旧两棵树进行一个深度优先遍历，这样每一个节点都会一个标记，在到深度遍历的时候，每遍历到一和个节点，就把该节点和新的节点树进行对比，如果有差异就放到一个对象里面
> - 遍历差异对象，根据差异的类型，根据对应对规则更新VNode
>
> React 的处理 render 的基本思维模式是每次一有变动就会去重新渲染整个应用。在 Virtual DOM 没有出现之前，最简单的方法就是直接调用 innerHTML。Virtual DOM厉害的地方并不是说它比直接操作 DOM 快，而是说不管数据怎么变，都会尽量以最小的代价去更新 DOM。React 将 render 函数返回的虚拟 DOM 树与老的进行比较，从而确定 DOM 要不要更新、怎么更新。当 DOM 树很大时，遍历两棵树进行各种比对还是相当耗性能的，特别是在顶层 setState 一个微小的修改，默认会去遍历整棵树。尽管 React 使用高度优化的 Diff 算法，但是这个过程仍然会损耗性能.

### 12. React如何判断什么时候重新渲染组件？

> 组件状态的改变可以因为`props`的改变，或者直接通过`setState`方法改变。组件获得新的状态，然后React决定是否应该重新渲染组件。只要组件的state发生变化，React就会对组件进行重新渲染。这是因为React中的`shouldComponentUpdate`方法默认返回`true`，这就是导致每次更新都重新渲染的原因。
>
> 当React将要渲染组件时会执行`shouldComponentUpdate`方法来看它是否返回`true`（组件应该更新，也就是重新渲染）。所以需要重写`shouldComponentUpdate`方法让它根据情况返回`true`或者`false`来告诉React什么时候重新渲染什么时候跳过重新渲染。

###  13.React声明组件有哪几种方法，有什么不同？

> React 声明组件的三种方式：
>
> - 函数式定义的`无状态组件`
> - ES5原生方式`React.createClass`定义的组件
> - ES6形式的`extends React.Component`定义的组件
>
> **（1）无状态函数式组件** 它是为了创建纯展示组件，这种组件只负责根据传入的props来展示，不涉及到state状态的操作 组件不会被实例化，整体渲染性能得到提升，不能访问this对象，不能访问生命周期的方法
>
> **（2）ES5 原生方式 React.createClass // RFC** React.createClass会自绑定函数方法，导致不必要的性能开销，增加代码过时的可能性。
>
> **（3）E6继承形式 React.Component // RCC** 目前极为推荐的创建有状态组件的方式，最终会取代React.createClass形式；相对于 React.createClass可以更好实现代码复用。
>
> **无状态组件相对于于后者的区别：** 与无状态组件相比，React.createClass和React.Component都是创建有状态的组件，这些组件是要被实例化的，并且可以访问组件的生命周期方法。
>
> #### 函数式创建
>
> 在`React Hooks`出来之前，函数式组件可以视为无状态组件，只负责根据传入的`props`来展示视图，不涉及对`state`状态的操作
>
> 大多数组件可以写为无状态组件，通过简单组合构建其他组件
>
> 在`React`中，通过函数简单创建组件的示例如下：
>
> ```jsx
> function HelloComponent(props, /* context */) {
>   return <div>Hello {props.name}</div>
> }
> ```
>
> #### 通过 React.createClass 方法创建
>
> `React.createClass`是react刚开始推荐的创建组件的方式，目前这种创建方式已经不怎么用了
>
> 像上述通过函数式创建的组件的方式，最终会通过`babel`转化成`React.createClass`这种形式，转化成如下：
>
> ```jsx
> function HelloComponent(props) /* context */{
>   return React.createElement(
>     "div",
>     null,
>     "Hello ",
>     props.name
>   );
> }
> ```
>
> 由于上述的编写方式过于冗杂，目前基本上不使用上
>
> #### 继承 React.Component 创建
>
> 同样在`react hooks`出来之前，有状态的组件只能通过继承`React.Component`这种形式进行创建
>
> 有状态的组件也就是组件内部存在维护的数据，在类创建的方式中通过`this.state`进行访问
>
> 当调用`this.setState`修改组件的状态时，组价会再次会调用`render()`方法进行重新渲染
>
> 通过继承`React.Component`创建一个时钟示例如下：
>
> ```jsx
> class Timer extends React.Component {
>   constructor(props) {
>     super(props);
>     this.state = { seconds: 0 };
>   }
> 
>   tick() {
>     this.setState(state => ({
>       seconds: state.seconds + 1
>     }));
>   }
> 
>   componentDidMount() {
>     this.interval = setInterval(() => this.tick(), 1000);
>   }
> 
>   componentWillUnmount() {
>     clearInterval(this.interval);
>   }
> 
>   render() {
>     return (
>       <div>
>         Seconds: {this.state.seconds}
>       </div>
>     );
>   }
> }
> ```

### 14.如何理解 React

> * react 是什么？
>
>   React，用于构建用户界面的 JavaScript 库，只提供了 UI 层面的解决方案
>
>   遵循组件设计模式、声明式编程范式和函数式编程概念，以使前端应用程序更高效
>
>   使用虚拟`DOM`来有效地操作`DOM`，遵循从高阶组件到低阶组件的单向数据流
>
>   帮助我们将界面成了各个独立的小块，每一个块就是组件，这些组件之间可以组合、嵌套，构成整体页面
>
>   `react` 类组件使用一个名为 `render()` 的方法或者函数组件`return`，接收输入的数据并返回需要展示的内容
>
>   ```js
>   class HelloMessage extends React.Component {
>     render() {
>       return (
>         <div>
>           Hello {this.props.name}
>         </div>
>       );
>     }
>   }
>   
>   ReactDOM.render(
>     <HelloMessage name="Taylor" />,
>     document.getElementById('hello-example')
>   );
>   ```
>
>   上述这种类似 `XML`形式就是`JSX`，最终会被`babel`编译为合法的`JS`语句调用
>
>   被传入的数据可在组件中通过 `this.props` 在 `render()` 访问
>
> * 为什么会出现 react 这样的框架
>
>   在我们早期的前端开发中，我们通过 JavaScript 来手动操作 DOM 以达到和用户交互的目的，但是原生的 DOM 操作繁琐并且还会存在兼容性问题。因此在我们前端开发中出现 jquery 这样的框架，来帮助我们简化 DOM 操作，同时解决了各种兼容性问题。但是随着我们的项目变得越来越复杂，我们发现，光有 DOM 操作远远不够，我们还要有应用结构，因此我们开始探讨前端组件化的问题，通过组件化的方式来让我们的项目结构变得更加清晰，同时也引入了 MVX 的概念。接着大家发现 MVX 的数据绑定依然需要我们手动监听 model 的变化，然后去做各种 DOM 操作，数据到视图层的映射依然繁琐。于是大家就开始推崇 MVVM 的数据绑定，于是就出现了例如 Angular，React 和 Vue 这样的前端框架。
>
> * react 的特点
>
>   react具有声明式，组件化以及一次学习，随处编写的特点，因此 react 特别适合用于构建快速响应的大型 web 应用

### 15.React与Vue的异同

> 相同点：
>
> - **使用虚拟 DOM** 
> - **提供响应式和组件化的视图组件** 
> - **将注意力集中保持在核心库，而将其他功能如路由、全局状态管理交给相关的库** 
>
> 不同点：
>
> - **视图更新**
>
>   在react中，当某个组件的状态发生变化的时候，会以该组件为根，重新渲染整个组件树，如果要避免不必要的子组件重渲染，我们需要在所有可能的地方使用 `PureComponent` 或者是 `shouldComponentUpdate` 方法。
>
>   在 Vue 应用中，组件的依赖是在渲染过程中自动追踪的，所以vue能精确知晓哪个组件确实需要被重渲染。Vue的特点可以让开发者不再考虑此类优化，从而更好地专注于应用本身
>
> - **HTML 和 CSS**
>
>   在 react 中，everything is JavaScript，不仅是 html 可以用 jsx 表示，连 css 也有 js 的解决方案。
>
>    Vue 的整体思想是拥抱经典的 Web 技术，并在其上进行扩展。使用 html，css 和 js 来构建模板。
>
>   在这个方面我觉得 vue 是在适应开发者，而 react 想要改变开发者。
>
>   同时，在组件作用域内的 css 上，react 是使用的 css-in-js 的解决方案或者是 css modules。而 vue 使用 scoped 来控制 css
>
> - **规模**
>
>   vue 的路由管理和状态管理由官方维护，而 react 则是把这些问题交给社区，创建了一个更为分散的生态系统，因此react的生态系统相对于 vue 更加繁荣。
>
> - **原生渲染**
>
>   由于两者都是使用的虚拟 dom 技术，因此都可以构建原生的安卓或者 ios 应用，例如 react-native 和 weex

### 16.state 和 props有什么区别？

> #### state
>
> 一个组件的显示形态可以由数据状态和外部参数所决定，而数据状态就是`state`，一般在 `constructor` 中初始化
>
> 当需要修改里面的值的状态需要通过调用`setState`来改变，从而达到更新组件内部数据的作用，并且重新调用组件`render`方法，如下面的例子：
>
> ```jsx
> class Button extends React.Component {
>     constructor() {
>         super();
>         this.state = {
>             count: 0,
>         };
>     }
> 
>     updateCount() {
>         this.setState((prevState, props) => {
>             return { count: prevState.count + 1 }
>         });
>     }
> 
>     render() {
>         return (<button
>                     onClick={() => this.updateCount()}
>                     >
>                 Clicked {this.state.count} times
>             </button>);
>     }
> }
> ```
>
> `setState`还可以接受第二个参数，它是一个函数，会在`setState`调用完成并且组件开始重新渲染时被调用，可以用来监听渲染是否完成
>
> ```js
> this.setState({
>   name:'JS每日一题'
> },()=>console.log('setState finished'))
> ```

> #### props
>
> `React`的核心思想就是组件化思想，页面会被切分成一些独立的、可复用的组件
>
> 组件从概念上看就是一个函数，可以接受一个参数作为输入值，这个参数就是`props`，所以可以把`props`理解为从外部传入组件内部的数据
>
> `react`具有单向数据流的特性，所以他的主要作用是从父组件向子组件中传递数据
>
> `props`除了可以传字符串，数字，还可以传递对象，数组甚至是回调函数，如下：
>
> ```jsx
> class Welcome extends React.Component {
>   render() {
>     return <h1>Hello {this.props.name}</h1>;
>   }
> }
> 
> const element = <Welcome name="Sara" onNameChanged={this.handleName} />;
> ```
>
> 上述`name`属性与`onNameChanged`方法都能在子组件的`props`变量中访问
>
> 在子组件中，`props`在内部不可变的，如果想要改变它看，只能通过外部组件传入新的`props`来重新渲染子组件，否则子组件的`props`和展示形式不会改变
>
> #### 区别
>
> 相同点：
>
> - 两者都是 JavaScript 对象
> - 两者都是用于保存信息
> - props 和 state 都能触发渲染更新
>
> 区别：
>
> - props 是外部传递给组件的，而 state 是在组件内被组件自己管理的，一般在 constructor 中初始化
> - props 在组件内部是不可修改的，但 state 在组件内部可以进行修改
> - state 是多变的、可以修改

### 17.super()和super(props)有什么区别？

> #### ES6类
>
> 在`ES6`中，通过`extends`关键字实现类的继承，方式如下：
>
> ```js
> class sup {
>     constructor(name) {
>         this.name = name
>     }
> 
>     printName() {
>         console.log(this.name)
>     }
> }
> 
> 
> class sub extends sup{
>     constructor(name,age) {
>         super(name) // super代表的事父类的构造函数
>         this.age = age
>     }
> 
>     printAge() {
>         console.log(this.age)
>     }
> }
> 
> let jack = new sub('jack',20)
> jack.printName()    //输出 : jack
> jack.printAge()    //输出 : 20
> ```
>
> 在上面的例子中，可以看到通过`super`关键字实现调用父类，`super`代替的是父类的构建函数，使用`super(name)`相当于调用`sup.prototype.constructor.call(this,name)`
>
> 如果在子类中不使用`super`，关键字，则会引发报错，如下：
>
> 报错的原因是 子类是没有自己的`this`对象的，它只能继承父类的`this`对象，然后对其进行加工
>
> 而`super()`就是将父类中的`this`对象继承给子类的，没有`super()`子类就得不到`this`对象
>
> 如果先调用`this`，再初始化`super()`，同样是禁止的行为
>
> ```js
> class sub extends sup{
>     constructor(name,age) {
>         this.age = age
>         super(name) // super代表的事父类的构造函数
>     }
> }
> ```
>
> 所以在子类`constructor`中，必须先代用`super`才能引用`this`

> #### 类组件
>
> 在`React`中，类组件是基于`es6`的规范实现的，继承`React.Component`，因此如果用到`constructor`就必须写`super()`才初始化`this`
>
> 这时候，在调用`super()`的时候，我们一般都需要传入`props`作为参数，如果不传进去，`React`内部也会将其定义在组件实例中
>
> ```js
> // React 内部
> const instance = new YourComponent(props);
> instance.props = props;
> ```
>
> 所以无论有没有`constructor`，在`render`中`this.props`都是可以使用的，这是`React`自动附带的，是可以不写的：
>
> ```js
> class HelloMessage extends React.Component{
>     render (){
>         return (
>             <div>nice to meet you! {this.props.name}</div>
>         );
>     }
> }
> ```
>
> 但是也不建议使用`super()`代替`super(props)`
>
> 因为在`React`会在类组件构造函数生成实例后再给`this.props`赋值，所以在不传递`props`在`super`的情况下，调用`this.props`为`undefined`，如下情况：
>
> ```jsx
> class Button extends React.Component {
>   constructor(props) {
>     super(); // 没传入 props
>     console.log(props);      //  {}
>     console.log(this.props); //  undefined
>   // ...
> }
> ```
>
> 而传入`props`的则都能正常访问，确保了 `this.props` 在构造函数执行完毕之前已被赋值，更符合逻辑，如下：
>
> ```jsx
> class Button extends React.Component {
>   constructor(props) {
>     super(props); // 没传入 props
>     console.log(props);      //  {}
>     console.log(this.props); //  {}
>   // ...
> }
> ```

> #### 总结
>
> 在`React`中，类组件基于`ES6`，所以在`constructor`中必须使用`super`
>
> 在调用`super`过程，无论是否传入`props`，`React`内部都会将`porps`赋值给组件实例`porps`属性中
>
> 如果只调用了`super()`，那么`this.props`在`super()`和构造函数结束之间仍是`undefined`

### 18.React事件绑定的方式有哪些？区别？

> 常见的绑定方式有如下：
>
> - render方法中使用bind
> - render方法中使用箭头函数
> - constructor中bind
> - 定义阶段使用箭头函数绑定
>
> #### 区别
>
> 上述四种方法的方式，区别主要如下：
>
> - 编写方面：方式一、方式二写法简单，方式三的编写过于冗杂
> - 性能方面：方式一和方式二在每次组件render的时候都会生成新的方法实例，性能问题欠缺。若该函数作为属性值传给子组件的时候，都会导致额外的渲染。而方式三、方式四只会生成一个方法实例
>
> 综合上述，方式四是最优的事件绑定方式

### 19.说说对React refs 的理解？应用场景？如何获取组件对应的DOM元素？

> `Refs` 在计算机中称为弹性文件系统（英语：Resilient File System，简称ReFS）
>
> `React` 中的 `Refs`提供了一种方式，允许我们访问 `DOM`节点或在 `render`方法中创建的 `React`元素
>
> 本质为`ReactDOM.render()`返回的组件实例，如果是渲染组件则返回的是组件实例，如果渲染`dom`则返回的是具体的`dom`节点。如下场景使用 Refs 比较适合：
>
> - 处理焦点、文本选择或者媒体的控制
> - 触发必要的动画
> - 集成第三方 DOM 库
>
> #### 如何使用
>
> 1. 字符串形式的ref  
>
>    ```jsx
>    showData2 = ()=>{
>        const {input2} = this.refs
>        alert(input2.value)
>    }
>    render(){
>     return(
>    <input ref="input2" onBlur={this.showData2} type="text" placeholder="失去焦点提示数据"/>
>    )
>    }
>    ```
>
> 2. 回调形式的ref
>
>    ```jsx
>    showData2 = ()=>{
>        const {input2} = this
>        alert(input2.value)
>    }
>    render(){
>     return(
>    <input ref={c => this.input2 = c} onBlur={this.showData2} type="text" placeholder="失去焦点提示数据"/>
>    )
>    }
>    ```
>
> 3. createRef创建ref容器·
>
>    ```jsx
>    myRef2 = React.createRef()
>    showData2 = ()=>{
>        alert(this.myRef2.current.value);
>    }
>     return(
>    <input ref={this.myRef2} onBlur={this.showData2} type="text" placeholder="失去焦点提示数据"/>
>    )
>    }
>    ```
>
> 4. 使用hooks
>
>    通过`useRef`创建一个`ref`，整体使用方式与`React.createRef`一致
>
>    ```jsx
>    function App(props) {
>      const myref = useRef()//{import useRef ...}
>      return (
>        <>
>          <div ref={myref}></div>
>        </>
>      )
>    }
>    ```
>
>    获取`ref`属性也是通过`hook`对象的`current`属性
>
>    ```js
>    const node = myref.current;
>    ```
>    
>    **注意：**
>    
>    - 不应该过度的使用 Refs
>    - ref 的返回值取决于节点的类型：
>      - 当 `ref` 属性被用于一个普通的 HTML 元素时，`React.createRef()` 将接收底层 DOM 元素作为他的 `current` 属性以创建 `ref`。
>      - 当 `ref` 属性被用于一个自定义的类组件时，`ref` 对象将接收该组件已挂载的实例作为他的 `current`。
>    - 当在父组件中需要访问子组件中的 `ref` 时可使用传递 Refs 或回调 Refs。

### 20.说说对React中类组件和函数组件的理解？有什么区别？

> ##### 类组件
>
> 类组件，顾名思义，也就是通过使用`ES6`类的编写形式去编写组件，该类必须继承`React.Component`
>
> 如果想要访问父组件传递过来的参数，可通过`this.props`的方式去访问
>
> 在组件中必须实现`render`方法，在`return`中返回`React`对象，如下：
>
> ```jsx
> class Welcome extends React.Component {
>   constructor(props) {
>     super(props)
>   }
>   render() {
>     return <h1>Hello, {this.props.name}</h1>
>   }
> }
> ```
>
> ##### 函数组件
>
> 函数组件，顾名思义，就是通过函数编写的形式去实现一个`React`组件，是`React`中定义组件最简单的方式
>
> ```jsx
> function Welcome(props) {
>   return <h1>Hello, {props.name}</h1>;
> }
> ```
>
> 函数第一个参数为`props`用于接收父组件传递过来的参数

> #### 区别
>
> 针对两种`React`组件，其区别主要分成以下几大方向：
>
> - 编写形式
> - 状态管理
> - 生命周期
> - 调用方式
> - 获取渲染的值
>
> ##### 编写形式
>
> 两者最明显的区别在于编写形式的不同，同一种功能的实现可以分别对应类组件和函数组件的编写形式
>
> 函数组件：
>
> ```jsx
> function Welcome(props) {
>   return <h1>Hello, {props.name}</h1>;
> }
> ```
>
> 类组件：
>
> ```jsx
> class Welcome extends React.Component {
>   constructor(props) {
>     super(props)
>   }
>   render() {
>     return <h1>Hello, {this.props.name}</h1>
>   }
> }
> ```
>
> ##### 状态管理
>
> 在`hooks`出来之前，函数组件就是无状态组件，不能保管组件的状态，不像类组件中调用`setState`
>
> 如果想要管理`state`状态，可以使用`useState`，如下：
>
> ```jsx
> const FunctionalComponent = () => {
>     const [count, setCount] = React.useState(0);
> 
>     return (
>         <div>
>             <p>count: {count}</p >
>             <button onClick={() => setCount(count + 1)}>Click</button>
>         </div>
>     );
> };
> ```
>
> 在使用`hooks`情况下，一般如果函数组件调用`state`，则需要创建一个类组件或者`state`提升到你的父组件中，然后通过`props`对象传递到子组件
>
> ##### 生命周期
>
> 在函数组件中，并不存在生命周期，这是因为这些生命周期钩子都来自于继承的`React.Component`
>
> 所以，如果用到生命周期，就只能使用类组件
>
> 但是函数组件使用`useEffect`也能够完成替代生命周期的作用，这里给出一个简单的例子：
>
> ```jsx
> const FunctionalComponent = () => {
>     useEffect(() => {
>         console.log("Hello");
>     }, []);
>     return <h1>Hello, World</h1>;
> };
> ```
>
> 上述简单的例子对应类组件中的`componentDidMount`生命周期
>
> 如果在`useEffect`回调函数中`return`一个函数，则`return`函数会在组件卸载的时候执行，正如`componentWillUnmount`
>
> ```jsx
> const FunctionalComponent = () => {
>  React.useEffect(() => {
>    return () => {
>      console.log("Bye");
>    };
>  }, []);
>  return <h1>Bye, World</h1>;
> };
> ```
>
> ##### 调用方式
>
> 如果是一个函数组件，调用则是执行函数即可：
>
> ```jsx
> // 你的代码 
> function SayHi() { 
>     return <p>Hello, React</p > 
> } 
> // React内部 
> const result = SayHi(props) // » <p>Hello, React</p >
> ```
>
> 如果是一个类组件，则需要将组件进行实例化，然后调用实例对象的`render`方法：
>
> ```jsx
> // 你的代码 
> class SayHi extends React.Component { 
>     render() { 
>         return <p>Hello, React</p > 
>     } 
> } 
> // React内部 
> const instance = new SayHi(props) // » SayHi {} 
> const result = instance.render() // » <p>Hello, React</p >
> ```
>
> ##### 获取渲染的值
>
> 首先给出一个示例，函数组件对应如下：
>
> ```jsx
> function ProfilePage(props) {
>   const showMessage = () => {
>     alert('Followed ' + props.user);
>   }
>   const handleClick = () => {
>     setTimeout(showMessage, 3000);
>   }
>   return (
>     <button onClick={handleClick}>Follow</button>
>   )
> }
> ```
>
> 类组件对应如下：
>
> ```jsx
> class ProfilePage extends React.Component {
>   showMessage() {
>     alert('Followed ' + this.props.user);
>   }
>   handleClick() {
>     setTimeout(this.showMessage.bind(this), 3000);
>   }
>   render() {
>     return <button onClick={this.handleClick.bind(this)}>Follow</button>
>   }
> }
> ```
>
> 两者看起来实现功能是一致的，但是在类组件中，输出`this.props.user`，`Props`在 `React`中是不可变的所以它永远不会改变，但是 `this` 总是可变的，以便您可以在 `render` 和生命周期函数中读取新版本
>
> 因此，如果我们的组件在请求运行时更新。`this.props` 将会改变。`showMessage`方法从“最新”的 `props` 中读取 `user`
>
> 而函数组件，本身就不存在`this`，`props`并不发生改变，因此同样是点击，`alert`的内容仍旧是之前的内容
>
> ##### 小结
>
> 两种组件都有各自的优缺点
>
> 函数组件语法更短、更简单，这使得它更容易开发、理解和测试
>
> 而类组件也会因大量使用 `this`而让人感到困惑

### 21.说说对受控组件和非受控组件的理解？应用场景？

> **（1）受控组件** 在使用表单来收集用户输入时，例如`<input><select><textearea>`等元素都要绑定一个change事件，当表单的状态发生变化，就会触发onChange事件，更新组件的state。这种组件在React中被称为**受控组件**，在受控组件中，组件渲染出的状态与它的value或checked属性相对应，react通过这种方式消除了组件的局部状态，使整个状态可控。react官方推荐使用受控表单组件。
>
> 受控组件更新state的流程：
>
> - 可以通过初始state中设置表单的默认值
> - 每当表单的值发生变化时，调用onChange事件处理器
> - 事件处理器通过事件对象e拿到改变后的状态，并更新组件的state
> - 一旦通过setState方法更新state，就会触发视图的重新渲染，完成表单组件的更新
>
> **受控组件缺陷：** 表单元素的值都是由React组件进行管理，当有多个输入框，或者多个这种组件时，如果想同时获取到全部的值就必须每个都要编写事件处理函数，这会让代码看着很臃肿，所以为了解决这种情况，出现了非受控组件。
>
> **（2）非受控组件** 如果一个表单组件没有value props（单选和复选按钮对应的是checked props）时，就可以称为非受控组件。在非受控组件中，可以使用一个ref来从DOM获得表单值。而不是为每个状态更新编写一个事件处理程序。
>
> React官方的解释：
>
> > 要编写一个非受控组件，而不是为每个状态更新都编写数据处理函数，你可以使用 ref来从 DOM 节点中获取表单数据。 因为非受控组件将真实数据储存在 DOM 节点中，所以在使用非受控组件时，有时候反而更容易同时集成 React 和非 React 代码。如果你不介意代码美观性，并且希望快速编写代码，使用非受控组件往往可以减少你的代码量。否则，你应该使用受控组件。
>
> 例如，下面的代码在非受控组件中接收单个属性：
>
> ```javascript
> class NameForm extends React.Component {
>   constructor(props) {
>     super(props);
>     this.handleSubmit = this.handleSubmit.bind(this);
>   }
>   handleSubmit(event) {
>     alert('A name was submitted: ' + this.input.value);
>     event.preventDefault();
>   }
>   render() {
>     return (
>       <form onSubmit={this.handleSubmit}>
>         <label>
>           Name:
>           <input type="text" ref={(input) => this.input = input} />
>         </label>
>         <input type="submit" value="Submit" />
>       </form>
>     );
>   }
> }
> 复制代码
> ```
>
> **总结：** 页面中所有输入类的DOM如果是现用现取的称为非受控组件，而通过setState将输入的值维护到了state中，需要时再从state中取出，这里的数据就受到了state的控制，称为受控组件。

> #### 应用场景
>
> 大部分时候推荐使用受控组件来实现表单，因为在受控组件中，表单数据由`React`组件负责处理
>
> 如果选择非受控组件的话，控制能力较弱，表单数据就由`DOM`本身处理，但更加方便快捷，代码量少

### 22. 对有状态组件和无状态组件的理解及使用场景

> **（1）有状态组件**
>
> **特点：**
>
> - 是类组件
> - 有继承
> - 可以使用this
> - 可以使用react的生命周期
> - 使用较多，容易频繁触发生命周期钩子函数，影响性能
> - 内部使用 state，维护自身状态的变化，有状态组件根据外部组件传入的 props 和自身的 state进行渲染。
>
> **使用场景：**
>
> - 需要使用到状态的。
> - 需要使用状态操作组件的（无状态组件的也可以实现新版本react hooks也可实现）
>
> **总结：** 类组件可以维护自身的状态变量，即组件的 state ，类组件还有不同的生命周期方法，可以让开发者能够在组件的不同阶段（挂载、更新、卸载），对组件做更多的控制。类组件则既可以充当无状态组件，也可以充当有状态组件。当一个类组件不需要管理自身状态时，也可称为无状态组件。
>
> **（2）无状态组件** **特点：**
>
> - 不依赖自身的状态state
> - 可以是类组件或者函数组件。
> - 可以完全避免使用 this 关键字。（由于使用的是箭头函数事件无需绑定）
> - 有更高的性能。当不需要使用生命周期钩子时，应该首先使用无状态函数组件
> - 组件内部不维护 state ，只根据外部组件传入的 props 进行渲染的组件，当 props 改变时，组件重新渲染。
>
> **使用场景：**
>
> - 组件不需要管理 state，纯展示
>
> **优点：**
>
> - 简化代码、专注于 render
> - 组件不需要被实例化，无生命周期，提升性能。 输出（渲染）只取决于输入（属性），无副作用
> - 视图和数据的解耦分离
>
> **缺点：**
>
> - 无法使用 ref
> - 无生命周期方法
> - 无法控制组件的重渲染，因为无法使用shouldComponentUpdate 方法，当组件接受到新的属性时则会重渲染
>
> **总结：** 组件内部状态且与外部无关的组件，可以考虑用状态组件，这样状态树就不会过于复杂，易于理解和管理。当一个组件不需要管理自身状态时，也就是无状态组件，应该优先设计为函数组件。比如自定义的 `<Button/>`、 `<Input />` 等组件。

### 23. React中可以在render访问refs吗？为什么？

> ```javascript
> <>
>   <span id="name" ref={this.spanRef}>{this.state.title}</span>
>   <span>{
>      this.spanRef.current ? '有值' : '无值'
>   }</span>
> </>
> ```
>
> 不可以，render 阶段 DOM 还没有生成，无法获取 DOM。DOM 的获取需要在 pre-commit 阶段和 commit 阶段

### 24. 对React的插槽(Portals)的理解，如何使用，有哪些使用场景

> React 官方对 Portals 的定义：
>
> > Portal 提供了一种将子节点渲染到存在于父组件以外的 DOM 节点的优秀的方案
>
> Portals 是React 16提供的官方解决方案，使得组件可以脱离父组件层级挂载在DOM树的任何位置。通俗来讲，就是我们 render 一个组件，但这个组件的 DOM 结构并不在本组件内。
>
> Portals语法如下：
>
> ```javascript
> ReactDOM.createPortal(child, container);
> ```
>
> - 第一个参数 child 是可渲染的 React 子项，比如元素，字符串或者片段等;
> - 第二个参数 container 是一个 DOM 元素。
>
> 一般情况下，组件的render函数返回的元素会被挂载在它的父级组件上：
>
> ```javascript
> import DemoComponent from './DemoComponent';
> render() {
>   // DemoComponent元素会被挂载在id为parent的div的元素上
>   return (
>     <div id="parent">
>         <DemoComponent />
>     </div>
>   );
> }
> ```
>
> 然而，有些元素需要被挂载在更高层级的位置。最典型的应用场景：当父组件具有`overflow: hidden`或者`z-index`的样式设置时，组件有可能被其他元素遮挡，这时就可以考虑要不要使用Portal使组件的挂载脱离父组件。例如：对话框，模态窗。
>
> ```javascript
> import DemoComponent from './DemoComponent';
> render() {
>   // DemoComponent元素会被挂载在id为parent的div的元素上
>   return (
>     <div id="parent">
>         <DemoComponent />
>     </div>
>   );
> }
> ```

### 25.说说对React Hooks的理解？解决了什么问题？

> `Hook` 是 React 16.8 的新增特性。它可以让你在不编写 `class` 的情况下使用 `state` 以及其他的 `React` 特性
>
> 至于为什么引入`hook`，官方给出的动机是解决长时间使用和维护`react`过程中常遇到的问题，例如：
>
> - 难以重用和共享组件中的与状态相关的逻辑
> - 逻辑复杂的组件难以开发与维护，当我们的组件需要处理多个互不相关的 local state 时，每个生命周期函数中可能会包含着各种互不相关的逻辑在里面
> - 类组件中的this增加学习成本，类组件在基于现有工具的优化上存在些许问题
> - 由于业务变动，函数组件不得不改为类组件等等
>
> 在以前，函数组件也被称为无状态的组件，只负责渲染的一些工作
>
> 因此，现在的函数组件也可以是有状态的组件，内部也可以维护自身的状态以及做一些逻辑方面的处理

> #### 有哪些
>
> 上面讲到，`Hooks`让我们的函数组件拥有了类组件的特性，例如组件内的状态、生命周期
>
> 最常见的`hooks`有如下：
>
> - useState
> - useEffect
> - 其他
>
> ##### useState
>
> 首先给出一个例子，如下：
>
> ```js
> import React, { useState } from 'react';
> 
> function Example() {
>   // 声明一个叫 "count" 的 state 变量
>   const [count, setCount] = useState(0);
> 
>   return (
>     <div>
>       <p>You clicked {count} times</p >
>       <button onClick={() => setCount(count + 1)}>
>         Click me
>       </button>
>     </div>
>   );
> }
> ```
>
> 在函数组件中通过`useState`实现函数内部维护`state`，参数为`state`默认的值，返回值是一个数组，第一个值为当前的`state`，第二个值为更新`state`的函数。
>
> 从上述代码分析，可以看出两者区别：
>
> - state声明方式：在函数组件中通过 useState 直接获取，类组件通过constructor 构造函数中设置
> - state读取方式：在函数组件中直接使用变量，类组件通过`this.state.count`的方式获取
> - state更新方式：在函数组件中通过 setCount 更新，类组件通过this.setState()
>
> 总的来讲，useState 使用起来更为简洁，减少了`this`指向不明确的情况
>
> ##### useEffect
>
> `useEffect`可以让我们在函数组件中进行一些带有副作用的操作
>
> 同样给出一个计时器示例：
>
> ```js
> class Example extends React.Component {
>   constructor(props) {
>     super(props);
>     this.state = {
>       count: 0
>     };
>   }
> 
>   componentDidMount() {
>     document.title = `You clicked ${this.state.count} times`;
>   }
>   componentDidUpdate() {
>     document.title = `You clicked ${this.state.count} times`;
>   }
> 
>   render() {
>     return (
>       <div>
>         <p>You clicked {this.state.count} times</p >
>         <button onClick={() => this.setState({ count: this.state.count + 1 })}>
>           Click me
>         </button>
>       </div>
>     );
>   }
> }
> ```
>
> 从上面可以看见，组件在加载和更新阶段都执行同样操作
>
> 而如果使用`useEffect`后，则能够将相同的逻辑抽离出来，这是类组件不具备的方法
>
> 对应的`useEffect`示例如下：
>
> ```jsx
> import React, { useState, useEffect } from 'react';
> function Example() {
>   const [count, setCount] = useState(0);
>  
>   useEffect(() => {    document.title = `You clicked ${count} times`;  });
>   return (
>     <div>
>       <p>You clicked {count} times</p >
>       <button onClick={() => setCount(count + 1)}>
>         Click me
>       </button>
>     </div>
>   );
> }
> ```
>
> `useEffect`第一个参数接受一个回调函数，默认情况下，`useEffect`会在第一次渲染和更新之后都会执行，相当于在`componentDidMount`和`componentDidUpdate`两个生命周期函数中执行回调
>
> 如果某些特定值在两次重渲染之间没有发生变化，你可以跳过对 effect 的调用，这时候只需要传入第二个参数，如下：
>
> ```js
> useEffect(() => {
>   document.title = `You clicked ${count} times`;
> }, [count]); // 仅在 count 更改时更新
> ```
>
> 上述传入第二个参数后，如果 `count` 的值是 `5`，而且我们的组件重渲染的时候 `count` 还是等于 `5`，React 将对前一次渲染的 `[5]` 和后一次渲染的 `[5]` 进行比较，如果是相等则跳过`effects`执行
>
> 回调函数中可以返回一个清除函数，这是`effect`可选的清除机制，相当于类组件中`componentwillUnmount`生命周期函数，可做一些清除副作用的操作，如下：
>
> ```jsx
> useEffect(() => {
>     function handleStatusChange(status) {
>         setIsOnline(status.isOnline);
>     }
> 
>     ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
>     return () => {
>         ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
>     };
> });
> ```
>
> 所以， `useEffect`相当于`componentDidMount`，`componentDidUpdate` 和 `componentWillUnmount` 这三个生命周期函数的组合
>
> ##### 其它 hooks
>
> 在组件通信过程中可以使用`useContext`，`refs`学习中我们也用到了`useRef`获取`DOM`结构，还有很多额外的`hooks`，如：
>
> - useReducer
> - useCallback
> - useMemo
> - useRef

### 26.说说react中引入css的方式有哪几种？区别？

> 组件式开发选择合适的`css`解决方案尤为重要
>
> 通常会遵循以下规则：
>
> - 可以编写局部css，不会随意污染其他组件内的原生；
> - 可以编写动态的css，可以获取当前组件的一些状态，根据状态的变化生成不同的css样式；
> - 支持所有的css特性：伪类、动画、媒体查询等；
> - 编写起来简洁方便、最好符合一贯的css风格特点
>
> 在这一方面，`vue`使用`css`起来更为简洁：
>
> - 通过 style 标签编写样式
> - scoped 属性决定编写的样式是否局部有效
> - lang 属性设置预处理器
> - 内联样式风格的方式来根据最新状态设置和改变css
>
> 而在`react`中，引入`CSS`就不如`Vue`方便简洁，其引入`css`的方式有很多种，各有利弊
>
> #### 方式
>
> 常见的`CSS`引入方式有以下：
>
> - 在组件内直接使用
> - 组件中引入 .css 文件
> - 组件中引入 .module.css 文件
> - CSS in JS
>
> ##### 在组件内直接使用
>
> 直接在组件中书写`css`样式，通过`style`属性直接引入，如下：
>
> ```js
> import React, { Component } from "react";
> 
> const div1 = {
>   width: "300px",
>   margin: "30px auto",
>   backgroundColor: "#44014C",  //驼峰法
>   minHeight: "200px",
>   boxSizing: "border-box"
> };
> 
> class Test extends Component {
>   constructor(props, context) {
>     super(props);
>   }
>  
>   render() {
>     return (
>      <div>
>        <div style={div1}>123</div>
>        <div style={{backgroundColor:"red"}}>
>      </div>
>     );
>   }
> }
> 
> export default Test;
> ```
>
> 上面可以看到，`css`属性需要转换成驼峰写法
>
> 这种方式优点：
>
> - 内联样式, 样式之间不会有冲突
> - 可以动态获取当前state中的状态
>
> 缺点：
>
> - 写法上都需要使用驼峰标识
> - 某些样式没有提示
> - 大量的样式, 代码混乱
> - 某些样式无法编写(比如伪类/伪元素)
>
> ##### 组件中引入css文件
>
> 将`css`单独写在一个`css`文件中，然后在组件中直接引入
>
> `App.css`文件：
>
> ```css
> .title {
>   color: red;
>   font-size: 20px;
> }
> 
> .desc {
>   color: green;
>   text-decoration: underline;
> }
> ```
>
> 组件中引入：
>
> ```js
> import React, { PureComponent } from 'react';
> 
> import Home from './Home';
> 
> import './App.css';
> 
> export default class App extends PureComponent {
>   render() {
>     return (
>       <div className="app">
>         <h2 className="title">我是App的标题</h2>
>         <p className="desc">我是App中的一段文字描述</p >
>         <Home/>
>       </div>
>     )
>   }
> }
> ```
>
> 这种方式存在不好的地方在于样式是全局生效，样式之间会互相影响
>
> ##### 组件中引入 .module.css 文件
>
> 将`css`文件作为一个模块引入，这个模块中的所有`css`，只作用于当前组件。不会影响当前组件的后代组件
>
> 这种方式是`webpack`特工的方案，只需要配置`webpack`配置文件中`modules:true`即可
>
> ```jsx
> import React, { PureComponent } from 'react';
> 
> import Home from './Home';
> 
> import './App.module.css';
> 
> export default class App extends PureComponent {
>   render() {
>     return (
>       <div className="app">
>         <h2 className="title">我是App的标题</h2>
>         <p className="desc">我是App中的一段文字描述</p >
>         <Home/>
>       </div>
>     )
>   }
> }
> ```
>
> 这种方式能够解决局部作用域问题，但也有一定的缺陷：
>
> - 引用的类名，不能使用连接符(.xxx-xx)，在 JavaScript 中是不识别的
> - 所有的 className 都必须使用 {style.className} 的形式来编写
> - 不方便动态来修改某些样式，依然需要使用内联样式的方式；
>
> ##### CSS in JS
>
> CSS-in-JS， 是指一种模式，其中`CSS`由 `JavaScript`生成而不是在外部文件中定义
>
> 此功能并不是 React 的一部分，而是由第三方库提供，例如：
>
> - styled-components
> - emotion
> - glamorous
>
> 下面主要看看`styled-components`的基本使用
>
> 本质是通过函数的调用，最终创建出一个组件：
>
> - 这个组件会被自动添加上一个不重复的class
> - styled-components会给该class添加相关的样式
>
> 基本使用如下：
>
> 创建一个`style.js`文件用于存放样式组件：
>
> ```js
> export const SelfLink = styled.div`
>   height: 50px;
>   border: 1px solid red;
>   color: yellow;
> `;
> 
> export const SelfButton = styled.div`
>   height: 150px;
>   width: 150px;
>   color: ${props => props.color};
>   background-image: url(${props => props.src});
>   background-size: 150px 150px;
> `;
> ```
>
> 引入样式组件也很简单：
>
> ```jsx
> import React, { Component } from "react";
> 
> import { SelfLink, SelfButton } from "./style";
> 
> class Test extends Component {
>   constructor(props, context) {
>     super(props);
>   }  
>  
>   render() {
>     return (
>      <div>
>        <SelfLink title="People's Republic of China">app.js</SelfLink>
>        <SelfButton color="palevioletred" style={{ color: "pink" }} src={fist}>
>           SelfButton
>         </SelfButton>
>      </div>
>     );
>   }
> }
> 
> export default Test;
> ```

> #### 区别
>
> 通过上面四种样式的引入，可以看到：
>
> - 在组件内直接使用`css`该方式编写方便，容易能够根据状态修改样式属性，但是大量的演示编写容易导致代码混乱
> - 组件中引入 .css 文件符合我们日常的编写习惯，但是作用域是全局的，样式之间会层叠
> - 引入.module.css 文件能够解决局部作用域问题，但是不方便动态修改样式，需要使用内联的方式进行样式的编写
> - 通过css in js 这种方法，可以满足大部分场景的应用，可以类似于预处理器一样样式嵌套、定义、修改状态等
>
> 至于使用`react`用哪种方案引入`css`，并没有一个绝对的答案，可以根据各自情况选择合适的方案

### 27.在react中组件间过渡动画如何实现？

> 在日常开发中，页面切换时的转场动画是比较基础的一个场景。当一个组件在显示与消失过程中存在过渡动画，可以很好的增加用户的体验。在`react`中实现过渡动画效果会有很多种选择，如`react-transition-group`，`react-motion`，`Animated`，以及原生的`CSS`都能完成切换动画

> ## 如何实现
>
> 在`react`中，`react-transition-group`是一种很好的解决方案，其为元素添加`enter`，`enter-active`，`exit`，`exit-active`这一系列勾子。可以帮助我们方便的实现组件的入场和离场动画
>
> 其主要提供了三个主要的组件：
>
> - CSSTransition：在前端开发中，结合 CSS 来完成过渡动画效果
> - SwitchTransition：两个组件显示和隐藏切换时，使用该组件
> - TransitionGroup：将多个动画组件包裹在其中，一般用于列表中元素的动画
>
> ##### CSSTransition
>
> 其实现动画的原理在于，当`CSSTransition`的`in`属性置为`true`时，`CSSTransition`首先会给其子组件加上`xxx-enter`、`xxx-enter-active`的`class`执行动画
>
> 当动画执行结束后，会移除两个`class`，并且添加`-enter-done`的`class`
>
> 所以可以利用这一点，通过`css`的`transition`属性，让元素在两个状态之间平滑过渡，从而得到相应的动画效果
>
> 当`in`属性置为`false`时，`CSSTransition`会给子组件加上`xxx-exit`和`xxx-exit-active`的`class`，然后开始执行动画，当动画结束后，移除两个`class`，然后添加`-enter-done`的`class`
>
> 如下例子：
>
> ```jsx
> export default class App2 extends React.PureComponent {
> 
>   state = {show: true};
> 
>   onToggle = () => this.setState({show: !this.state.show});
> 
>   render() {
>     const {show} = this.state;
>     return (
>       <div className={'container'}>
>         <div className={'square-wrapper'}>
>           <CSSTransition
>             in={show}
>             timeout={500}
>             classNames={'fade'}
>             unmountOnExit={true}
>           >
>             <div className={'square'} />
>           </CSSTransition>
>         </div>
>         <Button onClick={this.onToggle}>toggle</Button>
>       </div>
>     );
>   }
> }
> ```
>
> 对应`css`样式如下：
>
> ```css
> .fade-enter {
>   opacity: 0;
>   transform: translateX(100%);
> }
> 
> .fade-enter-active {
>   opacity: 1;
>   transform: translateX(0);
>   transition: all 500ms;
> }
> 
> .fade-exit {
>   opacity: 1;
>   transform: translateX(0);
> }
> 
> .fade-exit-active {
>   opacity: 0;
>   transform: translateX(-100%);
>   transition: all 500ms;
> }
> ```
>
> ##### SwitchTransition
>
> `SwitchTransition`可以完成两个组件之间切换的炫酷动画
>
> 比如有一个按钮需要在`on`和`off`之间切换，我们希望看到`on`先从左侧退出，`off`再从右侧进入
>
> `SwitchTransition`中主要有一个属性`mode`，对应两个值：
>
> - in-out：表示新组件先进入，旧组件再移除；
> - out-in：表示就组件先移除，新组建再进入
>
> `SwitchTransition`组件里面要有`CSSTransition`，不能直接包裹你想要切换的组件
>
> 里面的`CSSTransition`组件不再像以前那样接受`in`属性来判断元素是何种状态，取而代之的是`key`属性
>
> 下面给出一个按钮入场和出场的示例，如下：
>
> ```jsx
> import { SwitchTransition, CSSTransition } from "react-transition-group";
> 
> export default class SwitchAnimation extends PureComponent {
>   constructor(props) {
>     super(props);
> 
>     this.state = {
>       isOn: true
>     }
>   }
> 
>   render() {
>     const {isOn} = this.state;
> 
>     return (
>       <SwitchTransition mode="out-in">
>         <CSSTransition classNames="btn"
>                        timeout={500}
>                        key={isOn ? "on" : "off"}>
>           {
>           <button onClick={this.btnClick.bind(this)}>
>             {isOn ? "on": "off"}
>           </button>
>         }
>         </CSSTransition>
>       </SwitchTransition>
>     )
>   }
> 
>   btnClick() {
>     this.setState({isOn: !this.state.isOn})
>   }
> }
> ```
>
> `css`文件对应如下：
>
> ```css
> .btn-enter {
>   transform: translate(100%, 0);
>   opacity: 0;
> }
> 
> .btn-enter-active {
>   transform: translate(0, 0);
>   opacity: 1;
>   transition: all 500ms;
> }
> 
> .btn-exit {
>   transform: translate(0, 0);
>   opacity: 1;
> }
> 
> .btn-exit-active {
>   transform: translate(-100%, 0);
>   opacity: 0;
>   transition: all 500ms;
> }
> ```
>
> ##### TransitionGroup
>
> 当有一组动画的时候，就可将这些`CSSTransition`放入到一个`TransitionGroup`中来完成动画
>
> 同样`CSSTransition`里面没有`in`属性，用到了`key`属性
>
> `TransitionGroup`在感知`children`发生变化的时候，先保存移除的节点，当动画结束后才真正移除
>
> 其处理方式如下：
>
> - 插入的节点，先渲染dom，然后再做动画
> - 删除的节点，先做动画，然后再删除dom
>
> 如下：
>
> ```jsx
> import React, { PureComponent } from 'react'
> import { CSSTransition, TransitionGroup } from 'react-transition-group';
> 
> export default class GroupAnimation extends PureComponent {
>   constructor(props) {
>     super(props);
> 
>     this.state = {
>       friends: []
>     }
>   }
> 
>   render() {
>     return (
>       <div>
>         <TransitionGroup>
>           {
>             this.state.friends.map((item, index) => {
>               return (
>                 <CSSTransition classNames="friend" timeout={300} key={index}>
>                   <div>{item}</div>
>                 </CSSTransition>
>               )
>             })
>           }
>         </TransitionGroup>
>         <button onClick={e => this.addFriend()}>+friend</button>
>       </div>
>     )
>   }
> 
>   addFriend() {
>     this.setState({
>       friends: [...this.state.friends, "coderwhy"]
>     })
>   }
> }
> 
> ```
>
> 对应`css`如下：
>
> ```css
> .friend-enter {
>     transform: translate(100%, 0);
>     opacity: 0;
> }
> 
> .friend-enter-active {
>     transform: translate(0, 0);
>     opacity: 1;
>     transition: all 500ms;
> }
> 
> .friend-exit {
>     transform: translate(0, 0);
>     opacity: 1;
> }
> 
> .friend-exit-active {
>     transform: translate(-100%, 0);
>     opacity: 0;
>     transition: all 500ms;
> }
> ```

### 28.说说你对immutable的理解？如何应用在react项目中？

> Immutable，不可改变的，在计算机中，即指一旦创建，就不能再被更改的数据
>
> 对 `Immutable`对象的任何修改或添加删除操作都会返回一个新的 `Immutable`对象
>
> `Immutable` 实现的原理是 `Persistent Data Structure`（持久化数据结构）:
>
> - 用一种数据结构来保存数据
> - 当数据被修改时，会返回一个对象，但是新的对象会尽可能的利用之前的数据结构而不会对内存造成浪费
>
> 也就是使用旧数据创建新数据时，要保证旧数据同时可用且不变，同时为了避免 `deepCopy`把所有节点都复制一遍带来的性能损耗，`Immutable` 使用了 `Structural Sharing`（结构共享）
>
> 如果对象树中一个节点发生变化，只修改这个节点和受它影响的父节点，其它节点则进行共享

> #### 如何使用
>
> 使用`Immutable`对象最主要的库是`immutable.js`
>
> immutable.js 是一个完全独立的库，无论基于什么框架都可以用它
>
> 其出现场景在于弥补 Javascript 没有不可变数据结构的问题，通过 structural sharing来解决的性能问题
>
> 内部提供了一套完整的 Persistent Data Structure，还有很多易用的数据类型，如`Collection`、`List`、`Map`、`Set`、`Record`、`Seq`，其中：
>
> - List: 有序索引集，类似 JavaScript 中的 Array
> - Map: 无序索引集，类似 JavaScript 中的 Object
> - Set: 没有重复值的集合
>
> 主要的方法如下：
>
> - fromJS()：将一个js数据转换为Immutable类型的数据
>
> ```js
> const obj = Immutable.fromJS({a:'123',b:'234'})
> ```
>
> - toJS()：将一个Immutable数据转换为JS类型的数据
> - is()：对两个对象进行比较
>
> ```js
> import { Map, is } from 'immutable'
> const map1 = Map({ a: 1, b: 1, c: 1 })
> const map2 = Map({ a: 1, b: 1, c: 1 })
> map1 === map2   //false
> Object.is(map1, map2) // false
> is(map1, map2) // true
> ```
>
> - get(key)：对数据或对象取值
> - getIn([]) ：对嵌套对象或数组取值，传参为数组，表示位置
>
> ```js
> let abs = Immutable.fromJS({a: {b:2}});
> abs.getIn(['a', 'b']) // 2
> abs.getIn(['a', 'c']) // 子级没有值
> 
> let arr = Immutable.fromJS([1 ,2, 3, {a: 5}]);
> arr.getIn([3, 'a']); // 5
> arr.getIn([3, 'c']); // 子级没有值
> ```
>
> 如下例子：使用方法如下：
>
> ```js
> import Immutable from 'immutable';
> foo = Immutable.fromJS({a: {b: 1}});
> bar = foo.setIn(['a', 'b'], 2);   // 使用 setIn 赋值
> console.log(foo.getIn(['a', 'b']));  // 使用 getIn 取值，打印 1
> console.log(foo === bar);  //  打印 false
> ```
>
> 如果换到原生的`js`，则对应如下：
>
> ```js
> let foo = {a: {b: 1}};
> let bar = foo;
> bar.a.b = 2;
> console.log(foo.a.b);  // 打印 2
> console.log(foo === bar);  //  打印 true
> ```
>
> ##### 在React中应用
>
> 使用 `Immutable`可以给 `React` 应用带来性能的优化，主要体现在减少渲染的次数
>
> 在做`react`性能优化的时候，为了避免重复渲染，我们会在`shouldComponentUpdate()`中做对比，当返回`true`执行`render`方法
>
> `Immutable`通过`is`方法则可以完成对比，而无需像一样通过深度比较的方式比较
>
> 在使用`redux`过程中也可以结合`Immutable`，不使用`Immutable`前修改一个数据需要做一个深拷贝
>
> ```jsx
> import '_' from 'lodash';
> 
> const Component = React.createClass({
>   getInitialState() {
>     return {
>       data: { times: 0 }
>     }
>   },
>   handleAdd() {
>     let data = _.cloneDeep(this.state.data);
>     data.times = data.times + 1;
>     this.setState({ data: data });
>   }
> }
> ```
>
> 使用 Immutable 后：
>
> ```jsx
> getInitialState() {
>   return {
>     data: Map({ times: 0 })
>   }
> },
>   handleAdd() {
>     this.setState({ data: this.state.data.update('times', v => v + 1) });
>     // 这时的 times 并不会改变
>     console.log(this.state.data.get('times'));
>   }
> ```
>
> 同理，在`redux`中也可以将数据进行`fromJS`处理
>
> ```js
> import * as constants from './constants'
> import {fromJS} from 'immutable'
> const defaultState = fromJS({ //将数据转化成immutable数据
>     home:true,
>     focused:false,
>     mouseIn:false,
>     list:[],
>     page:1,
>     totalPage:1
> })
> export default(state=defaultState,action)=>{
>     switch(action.type){
>         case constants.SEARCH_FOCUS:
>             return state.set('focused',true) //更改immutable数据
>         case constants.CHANGE_HOME_ACTIVE:
>             return state.set('home',action.value)
>         case constants.SEARCH_BLUR:
>             return state.set('focused',false)
>         case constants.CHANGE_LIST:
>             // return state.set('list',action.data).set('totalPage',action.totalPage)
>             //merge效率更高，执行一次改变多个数据
>             return state.merge({
>                 list:action.data,
>                 totalPage:action.totalPage
>             })
>         case constants.MOUSE_ENTER:
>             return state.set('mouseIn',true)
>         case constants.MOUSE_LEAVE:
>             return state.set('mouseIn',false)
>         case constants.CHANGE_PAGE:
>             return state.set('page',action.page)
>         default:
>             return state
>     }
> }
> ```

### 29.说说React render方法的原理？在什么时候会被触发？

> 首先，`render`函数在`react`中有两种形式：
>
> 在类组件中，指的是`render`方法：
>
> ```jsx
> class Foo extends React.Component {
>     render() {
>         return <h1> Foo </h1>;
>     }
> }
> ```
>
> 在函数组件中，指的是函数组件本身：
>
> ```js
> function Foo() {
>     return <h1> Foo </h1>;
> }
> ```
>
> 在`render`过程中，`React` 将新调用的 `render`函数返回的树与旧版本的树进行比较，这一步是决定如何更新 `DOM` 的必要步骤，然后进行 `diff` 比较，更新 `DOM`树

> ##### 触发时机
>
> `render`的执行时机主要分成了两部分：
>
> - 类组件调用 setState 修改状态
>
> ```jsx
> class Foo extends React.Component {
>   state = { count: 0 };
> 
>   increment = () => {
>     const { count } = this.state;
> 
>     const newCount = count < 10 ? count + 1 : count;
> 
>     this.setState({ count: newCount });
>   };
> 
>   render() {
>     const { count } = this.state;
>     console.log("Foo render");
> 
>     return (
>       <div>
>         <h1> {count} </h1>
>         <button onClick={this.increment}>Increment</button>
>       </div>
>     );
>   }
> }
> ```
>
> 点击按钮，则调用`setState`方法，无论`count`发生变化辩护，控制台都会输出`Foo render`，证明`render`执行了
>
> - 函数组件通过`useState hook`修改状态
>
> ```jsx
> function Foo() {
>   const [count, setCount] = useState(0);
> 
>   function increment() {
>     const newCount = count < 10 ? count + 1 : count;
>     setCount(newCount);
>   }
> 
>   console.log("Foo render");
>   
>   return (
>     <div>
>       <h1> {count} </h1>
>       <button onClick={increment}>Increment</button>
>     </div>
>   );
> }
> ```
>
> 函数组件通过`useState`这种形式更新数据，当数组的值不发生改变了，就不会触发`render`
>
> - 类组件重新渲染
>
> ```js
> class App extends React.Component {
>   state = { name: "App" };
>   render() {
>     return (
>       <div className="App">
>         <Foo />
>         <button onClick={() => this.setState({ name: "App" })}>
>           Change name
>         </button>
>       </div>
>     );
>   }
> }
> 
> function Foo() {
>   console.log("Foo render");
> 
>   return (
>     <div>
>       <h1> Foo </h1>
>     </div>
>   );
> }
> ```
>
> 只要点击了 `App` 组件内的 `Change name` 按钮，不管 `Foo` 具体实现是什么，都会被重新`render`渲染
>
> - 函数组件重新渲染
>
> ```jsx
> function App(){
>     const [name,setName] = useState('App')
> 
>     return (
>         <div className="App">
>             <Foo />
>             <button onClick={() => setName("aaa")}>
>                 { name }
>             </button>
>       </div>
>     )
> }
> 
> function Foo() {
>   console.log("Foo render");
> 
>   return (
>     <div>
>       <h1> Foo </h1>
>     </div>
>   );
> }
> ```
>
> 可以发现，使用`useState`来更新状态的时候，只有首次会触发`Foo render`，后面并不会导致`Foo render`
>
> ##### 总结
>
> ```
> render`函数里面可以编写`JSX`，转化成`createElement`这种形式，用于生成虚拟`DOM`，最终转化成真实`DOM
> ```
>
> 在`React` 中，类组件只要执行了 `setState` 方法，就一定会触发 `render` 函数执行，函数组件使用`useState`更改状态不一定导致重新`render`
>
> 组件的`props` 改变了，不一定触发 `render` 函数的执行，但是如果 `props` 的值来自于父组件或者祖先组件的 `state`
>
> 在这种情况下，父组件或者祖先组件的 `state` 发生了改变，就会导致子组件的重新渲染
>
> 所以，一旦执行了`setState`就会执行`render`方法，`useState` 会判断当前值有无发生改变确定是否执行`render`方法，一旦父组件发生渲染，子组件也会渲染

### 30.说说你是如何提高组件的渲染效率的？在React中如何避免不必要的render？

> `react` 基于虚拟 `DOM` 和高效 `Diff`算法的完美配合，实现了对 `DOM`最小粒度的更新，大多数情况下，`React`对 `DOM`的渲染效率足以我们的业务日常。复杂业务场景下，性能问题依然会困扰我们。此时需要采取一些措施来提升运行性能，避免不必要的渲染则是业务中常见的优化手段之一

> #### 如何做
>
> 在之前文章中，我们了解到`render`的触发时机，简单来讲就是类组件通过调用`setState`方法， 就会导致`render`，父组件一旦发生`render`渲染，子组件一定也会执行`render`渲染
>
> 从上面可以看到，父组件渲染导致子组件渲染，子组件并没有发生任何改变，这时候就可以从避免无谓的渲染，具体实现的方式有如下：
>
> - shouldComponentUpdate
> - PureComponent
> - React.memo
>
> ##### shouldComponentUpdate
>
> 通过`shouldComponentUpdate`生命周期函数来比对 `state`和 `props`，确定是否要重新渲染
>
> 默认情况下返回`true`表示重新渲染，如果不希望组件重新渲染，返回 `false` 即可
>
> ##### PureComponent
>
> 跟`shouldComponentUpdate`原理基本一致，通过对 `props` 和 `state`的浅比较结果来实现 `shouldComponentUpdate`，源码大致如下：
>
> ```js
> if (this._compositeType === CompositeTypes.PureClass) {
>     shouldUpdate = !shallowEqual(prevProps, nextProps) || ! shallowEqual(inst.state, nextState);
> }
> ```
>
> `shallowEqual`对应方法大致如下：
>
> ```js
> const hasOwnProperty = Object.prototype.hasOwnProperty;
> 
> /**
>  * is 方法来判断两个值是否是相等的值，为何这么写可以移步 MDN 的文档
>  * https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/is
>  */
> function is(x: mixed, y: mixed): boolean {
>   if (x === y) {
>     return x !== 0 || y !== 0 || 1 / x === 1 / y;
>   } else {
>     return x !== x && y !== y;
>   }
> }
> 
> function shallowEqual(objA: mixed, objB: mixed): boolean {
>   // 首先对基本类型进行比较
>   if (is(objA, objB)) {
>     return true;
>   }
> 
>   if (typeof objA !== 'object' || objA === null ||
>       typeof objB !== 'object' || objB === null) {
>     return false;
>   }
> 
>   const keysA = Object.keys(objA);
>   const keysB = Object.keys(objB);
> 
>   // 长度不相等直接返回false
>   if (keysA.length !== keysB.length) {
>     return false;
>   }
> 
>   // key相等的情况下，再去循环比较
>   for (let i = 0; i < keysA.length; i++) {
>     if (
>       !hasOwnProperty.call(objB, keysA[i]) ||
>       !is(objA[keysA[i]], objB[keysA[i]])
>     ) {
>       return false;
>     }
>   }
> 
>   return true;
> }
> ```
>
> 当对象包含复杂的数据结构时，对象深层的数据已改变却没有触发 `render`
>
> 注意：在`react`中，是不建议使用深层次结构的数据
>
> ##### React.memo
>
> `React.memo`用来缓存组件的渲染，避免不必要的更新，其实也是一个高阶组件，与 `PureComponent` 十分类似。但不同的是， `React.memo` 只能用于函数组件
>
> ```jsx
> import { memo } from 'react';
> 
> function Button(props) {
>   // Component code
> }
> 
> export default memo(Button);
> ```
>
> 如果需要深层次比较，这时候可以给`memo`第二个参数传递比较函数
>
> ```jsx
> function arePropsEqual(prevProps, nextProps) {
>   // your code
>   return prevProps === nextProps;
> }
> 
> export default memo(Button, arePropsEqual);
> ```
>
> #### 总结
>
> 在实际开发过程中，前端性能问题是一个必须考虑的问题，随着业务的复杂，遇到性能问题的概率也在增高
>
> 除此之外，建议将页面进行更小的颗粒化，如果一个过大，当状态发生修改的时候，就会导致整个大组件的渲染，而对组件进行拆分后，粒度变小了，也能够减少子组件不必要的渲染

### 31.说说React Jsx转换成真实DOM过程？

> `react`通过将组件编写的`JSX`映射到屏幕，以及组件中的状态发生了变化之后 `React`会将这些「变化」更新到屏幕上
>
> 在前面文章了解中，`JSX`通过`babel`最终转化成`React.createElement`这种形式，例如：
>
> ```jsx
> <div>
>   < img src="avatar.png" className="profile" />
>   <Hello />
> </div>
> ```
>
> 会被`bebel`转化成如下：
>
> ```jsx
> React.createElement(
>   "div",
>   null,
>   React.createElement("img", {
>     src: "avatar.png",
>     className: "profile"
>   }),
>   React.createElement(Hello, null)
> );
> ```
>
> 在转化过程中，`babel`在编译时会判断 JSX 中组件的首字母：
>
> - 当首字母为小写时，其被认定为原生 `DOM` 标签，`createElement` 的第一个变量被编译为字符串
> - 当首字母为大写时，其被认定为自定义组件，createElement 的第一个变量被编译为对象
>
> 最终都会通过`RenderDOM.render(...)`方法进行挂载，如下：
>
> ```jsx
> ReactDOM.render(<App/>,  document.getElementById("root"));
> ```

> #### 过程
>
> 在`react`中，节点大致可以分成四个类别：
>
> - 原生标签节点
> - 文本节点
> - 函数组件
> - 类组件
>
> 如下所示：
>
> ```jsx
> class ClassComponent extends Component {
>   static defaultProps = {
>     color: "pink"
>   };
>   render() {
>     return (
>       <div className="border">
>         <h3>ClassComponent</h3>
>         <p className={this.props.color}>{this.props.name}</p >
>       </div>
>     );
>   }
> }
> 
> function FunctionComponent(props) {
>   return (
>     <div className="border">
>       FunctionComponent
>       <p>{props.name}</p >
>     </div>
>   );
> }
> 
> const jsx = (
>   <div className="border">
>     <p>xx</p >
>     < a href=" ">xxx</ a>
>     <FunctionComponent name="函数组件" />
>     <ClassComponent name="类组件" color="red" />
>   </div>
> );
> ```
>
> 这些类别最终都会被转化成`React.createElement`这种形式
>
> `React.createElement`其被调用时会传⼊标签类型`type`，标签属性`props`及若干子元素`children`，作用是生成一个虚拟`Dom`对象，如下所示：
>
> ```js
> function createElement(type, config, ...children) {
>     if (config) {
>         delete config.__self;
>         delete config.__source;
>     }
>     // ! 源码中做了详细处理，⽐如过滤掉key、ref等
>     const props = {
>         ...config,
>         children: children.map(child =>
>    typeof child === "object" ? child : createTextNode(child)
>   )
>     };
>     return {
>         type,
>         props
>     };
> }
> function createTextNode(text) {
>     return {
>         type: TEXT,
>         props: {
>             children: [],
>             nodeValue: text
>         }
>     };
> }
> export default {
>     createElement
> };
> ```
>
> `createElement`会根据传入的节点信息进行一个判断：
>
> - 如果是原生标签节点， type 是字符串，如div、span
> - 如果是文本节点， type就没有，这里是 TEXT
> - 如果是函数组件，type 是函数名
> - 如果是类组件，type 是类名
>
> 虚拟`DOM`会通过`ReactDOM.render`进行渲染成真实`DOM`，使用方法如下：
>
> ```jsx
> ReactDOM.render(element, container[, callback])
> ```
>
> 当首次调用时，容器节点里的所有 `DOM` 元素都会被替换，后续的调用则会使用 `React` 的 `diff`算法进行高效的更新
>
> 如果提供了可选的回调函数`callback`，该回调将在组件被渲染或更新之后被执行
>
> `render`大致实现方法如下：
>
> ```js
> function render(vnode, container) {
>     console.log("vnode", vnode); // 虚拟DOM对象
>     // vnode _> node
>     const node = createNode(vnode, container);
>     container.appendChild(node);
> }
> 
> // 创建真实DOM节点
> function createNode(vnode, parentNode) {
>     let node = null;
>     const {type, props} = vnode;
>     if (type === TEXT) {
>         node = document.createTextNode("");
>     } else if (typeof type === "string") {
>         node = document.createElement(type);
>     } else if (typeof type === "function") {
>         node = type.isReactComponent
>             ? updateClassComponent(vnode, parentNode)
>         : updateFunctionComponent(vnode, parentNode);
>     } else {
>         node = document.createDocumentFragment();
>     }
>     reconcileChildren(props.children, node);
>     updateNode(node, props);
>     return node;
> }
> 
> // 遍历下子vnode，然后把子vnode->真实DOM节点，再插入父node中
> function reconcileChildren(children, node) {
>     for (let i = 0; i < children.length; i++) {
>         let child = children[i];
>         if (Array.isArray(child)) {
>             for (let j = 0; j < child.length; j++) {
>                 render(child[j], node);
>             }
>         } else {
>             render(child, node);
>         }
>     }
> }
> function updateNode(node, nextVal) {
>     Object.keys(nextVal)
>         .filter(k => k !== "children")
>         .forEach(k => {
>         if (k.slice(0, 2) === "on") {
>             let eventName = k.slice(2).toLocaleLowerCase();
>             node.addEventListener(eventName, nextVal[k]);
>         } else {
>             node[k] = nextVal[k];
>         }
>     });
> }
> 
> // 返回真实dom节点
> // 执行函数
> function updateFunctionComponent(vnode, parentNode) {
>     const {type, props} = vnode;
>     let vvnode = type(props);
>     const node = createNode(vvnode, parentNode);
>     return node;
> }
> 
> // 返回真实dom节点
> // 先实例化，再执行render函数
> function updateClassComponent(vnode, parentNode) {
>     const {type, props} = vnode;
>     let cmp = new type(props);
>     const vvnode = cmp.render();
>     const node = createNode(vvnode, parentNode);
>     return node;
> }
> export default {
>     render
> };
> ```

### 32. 对 React-Intl 的理解，它的工作原理？

> React-intl是雅虎的语言国际化开源项目FormatJS的一部分，通过其提供的组件和API可以与ReactJS绑定。
>
> React-intl提供了两种使用方法，一种是引用React组件，另一种是直接调取API，官方更加推荐在React项目中使用前者，只有在无法使用React组件的地方，才应该调用框架提供的API。它提供了一系列的React组件，包括数字格式化、字符串格式化、日期格式化等。
>
> 在React-intl中，可以配置不同的语言包，他的工作原理就是根据需要，在语言包之间进行切换。

### 33. 对 React context 的理解

> 在React中，数据传递一般使用props传递数据，维持单向数据流，这样可以让组件之间的关系变得简单且可预测，但是单项数据流在某些场景中并不适用。单纯一对的父子组件传递并无问题，但要是组件之间层层依赖深入，props就需要层层传递显然，这样做太繁琐了。
>
> Context 提供了一种在组件之间共享此类值的方式，而不必显式地通过组件树的逐层传递 props。
>
> 可以把context当做是特定一个组件树内共享的store，用来做数据传递。**简单说就是，当你不想在组件树中通过逐层传递props或者state的方式来传递数据时，可以使用Context来实现跨层级的组件数据传递。**
>
> JS的代码块在执行期间，会创建一个相应的作用域链，这个作用域链记录着运行时JS代码块执行期间所能访问的活动对象，包括变量和函数，JS程序通过作用域链访问到代码块内部或者外部的变量和函数。
>
> 假如以JS的作用域链作为类比，React组件提供的Context对象其实就好比一个提供给子组件访问的作用域，而 Context对象的属性可以看成作用域上的活动对象。由于组件 的 Context 由其父节点链上所有组件通 过 getChildContext（）返回的Context对象组合而成，所以，组件通过Context是可以访问到其父组件链上所有节点组件提供的Context的属性。

### 34. 为什么React并不推荐优先考虑使用Context？

> * Context目前还处于实验阶段，可能会在后面的发行版本中有很大的变化，事实上这种情况已经发生了，所以为了避免给今后升级带来大的影响和麻烦，不建议在app中使用context。
>
> * 尽管不建议在app中使用context，但是独有组件而言，由于影响范围小于app，如果可以做到高内聚，不破坏组件树之间的依赖关系，可以考虑使用context
>
> * 对于组件之间的数据通信或者状态管理，有效使用props或者state解决，然后再考虑使用第三方的成熟库进行解决，以上的方法都不是最佳的方案的时候，在考虑context。
>
> * context的更新需要通过setState()触发，但是这并不是很可靠的，Context支持跨组件的访问，但是如果中间的子组件通过一些方法不影响更新，比如 shouldComponentUpdate() 返回false 那么不能保证Context的更新一定可以使用Context的子组件，因此，Context的可靠性需要关注

### 35.何时要使用异步组件？如和使用异步组件

> - 加载大组件的时候
> - 路由异步加载的时候
>
> react 中要配合 Suspense 使用
>
> ```js
> // 异步懒加载
> const Box = lazy(()=>import('./components/Box'));
> // 使用组件的时候要用suspense进行包裹
> <Suspense fallback={<div>loading...</div>}>
>     {show && <Box/>}
> </Suspense>
> ```

## 二、数据管理

### 1.说说 React中的setState执行机制

> 一个组件的显示形态可以由数据状态和外部参数所决定，而数据状态就是`state`
>
> 当需要修改里面的值的状态需要通过调用`setState`来改变，从而达到更新组件内部数据的作用
>
> 如下例子：
>
> ```jsx
> import React, { Component } from 'react'
> 
> export default class App extends Component {
>  constructor(props) {
>      super(props);
> 
>      this.state = {
>          message: "Hello World"
>      }
>  }
> 
>  render() {
>      return (
>          <div>
>              <h2>{this.state.message}</h2>
>              <button onClick={e => this.changeText()}>面试官系列</button>
>          </div>
>      )
>  }
> 
>  changeText() {
>      this.setState({
>          message: "JS每日一题"
>      })
>  }
> }
> ```
>
> 通过点击按钮触发`onclick`事件，执行`this.setState`方法更新`state`状态，然后重新执行`render`函数，从而导致页面的视图更新
>
> 如果直接修改`state`的状态，如下：
>
> ```jsx
> changeText() {
>  this.state.message = "你好啊,李银河";
> }
> ```
>
> 我们会发现页面并不会有任何反应，但是`state`的状态是已经发生了改变
>
> 这是因为`React`并不像`vue2`中调用`Object.defineProperty`数据响应式或者`Vue3`调用`Proxy`监听数据的变化
>
> 必须通过`setState`方法来告知`react`组件`state`已经发生了改变
>
> 关于`state`方法的定义是从`React.Component`中继承，定义的源码如下：
>
> ```js
> Component.prototype.setState = function(partialState, callback) {
> invariant(
>  typeof partialState === 'object' ||
>    typeof partialState === 'function' ||
>    partialState == null,
>  'setState(...): takes an object of state variables to update or a ' +
>    'function which returns an object of state variables.',
> );
> this.updater.enqueueSetState(this, partialState, callback, 'setState');
> };
> ```
>
> 从上面可以看到`setState`第一个参数可以是一个对象，或者是一个函数，而第二个参数是一个回调函数，用于可以实时的获取到更新之后的数据

> ###  React setState 调用之后发生了什么？是同步还是异步？
>
> **（1）React中setState后发生了什么**
>
> 在代码中调用setState函数之后，React 会将传入的参数对象与组件当前的状态合并，然后触发调和过程(Reconciliation)。经过调和过程，React 会以相对高效的方式根据新的状态构建 React 元素树并且着手重新渲染整个UI界面。
>
> 在 React 得到元素树之后，React 会自动计算出新的树与老树的节点差异，然后根据差异对界面进行最小化重渲染。在差异计算算法中，React 能够相对精确地知道哪些位置发生了改变以及应该如何改变，这就保证了按需更新，而不是全部重新渲染。
>
> 如果在短时间内频繁setState。React会将state的改变压入栈中，在合适的时机，批量更新state和视图，达到提高性能的效果。
>
> **（2）setState 是同步还是异步的**
>
> #### 更新类型
>
> 在使用`setState`更新数据的时候，`setState`的更新类型分成：
>
> - 异步更新
> - 同步更新
>
> ##### 异步更新
>
> 先举出一个例子：
>
> ```jsx
> changeText() {
>   this.setState({
>     message: "你好啊"
>   })
>   console.log(this.state.message); // Hello World
> }
> ```
>
> 从上面可以看到，最终打印结果为`Hello world`，并不能在执行完`setState`之后立马拿到最新的`state`的结果
>
> 如果想要立刻获取更新后的值，在第二个参数的回调中更新后会执行
>
> ```jsx
> changeText() {
>   this.setState({
>     message: "你好啊"
>   }, () => {
>     console.log(this.state.message); // 你好啊
>   });
> }
> ```
>
> ##### 同步更新
>
> 同样先给出一个在`setTimeout`中更新的例子：
>
> ```jsx
> changeText() {
>   setTimeout(() => {
>     this.setState({
>       message: "你好啊
>     });
>     console.log(this.state.message); // 你好啊
>   }, 0);
> }
> ```
>
> 上面的例子中，可以看到更新是同步
>
> 再来举一个原生`DOM`事件的例子：
>
> ```jsx
> componentDidMount() {
>   const btnEl = document.getElementById("btn");
>   btnEl.addEventListener('click', () => {
>     this.setState({
>       message: "你好啊,李银河"
>     });
>     console.log(this.state.message); // 你好啊,李银河
>   })
> }
> ```
>
> #### 小结
>
> setState 并不是单纯同步/异步的，它的表现会因调用场景的不同而不同。在源码中，通过 isBatchingUpdates 来判断setState 是先存进 state 队列还是直接更新，如果值为 true 则执行异步操作，为 false 则直接更新。
>
> - **异步：** 在 React 可以控制的地方，就为 true，比如在 React 生命周期事件和合成事件中，都会走合并操作，延迟更新的策略。
> - **同步：** 在 React 无法控制的地方，比如原生事件，具体就是在 addEventListener 、setTimeout、setInterval 等事件中，就只能同步更新。
>
> 一般认为，做异步设计是为了性能优化、减少渲染次数：
>
> - `setState`设计为异步，可以显著的提升性能。如果每次调用 `setState`都进行一次更新，那么意味着`render`函数会被频繁调用，界面重新渲染，这样效率是很低的；最好的办法应该是获取到多个更新，之后进行批量更新；
> - 如果同步更新了`state`，但是还没有执行`render`函数，那么`state`和`props`不能保持同步。`state`和`props`不能保持一致性，会在开发中产生很多的问题；
>
> #### 批量更新
>
> 同样先给出一个例子：
>
> ```jsx
> handleClick = () => {
>     this.setState({
>         count: this.state.count + 1,
>     })
>     console.log(this.state.count) // 1
> 
>     this.setState({
>         count: this.state.count + 1,
>     })
>     console.log(this.state.count) // 1
> 
>     this.setState({
>         count: this.state.count + 1,
>     })
>     console.log(this.state.count) // 1
> }
> ```
>
> 点击按钮触发事件，打印的都是 1，页面显示 `count` 的值为 2
>
> 对同一个值进行多次 `setState`， `setState` 的批量更新策略会对其进行覆盖，取最后一次的执行结果
>
> 上述的例子，实际等价于如下：
>
> ```js
> Object.assign(
>   previousState,
>   {index: state.count+ 1},
>   {index: state.count+ 1},
>   ...
> )
> ```
>
> 由于后面的数据会覆盖前面的更改，所以最终只加了一次
>
> 如果是下一个`state`依赖前一个`state`的话，推荐给`setState`一个参数传入一个`function`，如下：
>
> ```jsx
> onClick = () => {
>     this.setState((prevState, props) => {
>       return {count: prevState.count + 1};
>     });
>     this.setState((prevState, props) => {
>       return {count: prevState.count + 1};
>     });
> }
> ```
>
> 而在`setTimeout`或者原生`dom`事件中，由于是同步的操作，所以并不会进行覆盖现象
>
> **小结**
>
> 调用 `setState` 时，组件的 `state` 并不会立即改变， `setState` 只是把要修改的 `state` 放入一个队列， `React` 会优化真正的执行时机，并出于性能原因，会将 `React` 事件处理程序中的多次`React` 事件处理程序中的多次 `setState` 的状态修改合并成一次状态修改。 最终更新只产生一次组件及其子组件的重新渲染，这对于大型应用程序中的性能提升至关重要。
>
> ```javascript
> this.setState({
>   count: this.state.count + 1    ===>    入队，[count+1的任务]
> });
> this.setState({
>   count: this.state.count + 1    ===>    入队，[count+1的任务，count+1的任务]
> });
>                                           ↓
>                                          合并 state，[count+1的任务]
>                                           ↓
>                                          执行 count+1的任务
> ```
>
> 需要注意的是，只要同步代码还在执行，“攒起来”这个动作就不会停止。（注：这里之所以多次 +1 最终只有一次生效，是因为在同一个方法中多次 setState 的合并动作不是单纯地将更新累加。比如这里对于相同属性的设置，React 只会为其保留最后一次的更新）。

### 2. React中有使用过getDefaultProps吗？它有什么作用？

> 通过实现组件的getDefaultProps，对属性设置默认值（ES5的写法）：
>
> ```javascript
> var ShowTitle = React.createClass({
>   getDefaultProps:function(){
>     return{
>       title : "React"
>     }
>   },
>   render : function(){
>     return <h1>{this.props.title}</h1>
>   }
> });
> ```

### 3. React中setState的第二个参数作用是什么？

> `setState` 的第二个参数是一个可选的回调函数。这个回调函数将在组件重新渲染后执行。等价于在 `componentDidUpdate` 生命周期内执行。通常建议使用 `componentDidUpdate` 来代替此方式。在这个回调函数中你可以拿到更新后 `state` 的值：
>
> ```javascript
> this.setState({
>     key1: newState1,
>     key2: newState2,
>     ...
> }, callback) // 第二个参数是 state 更新完成后的回调函数
> ```

### 4. React中的setState和replaceState的区别是什么？

> **（1）setState()** setState()用于设置状态对象，其语法如下：
>
> ```javascript
> setState(object nextState[, function callback])
> ```
>
> - nextState，将要设置的新状态，该状态会和当前的state合并
> - callback，可选参数，回调函数。该函数会在setState设置成功，且组件重新渲染后调用。
>
> 合并nextState和当前state，并重新渲染组件。setState是React事件处理函数中和请求回调函数中触发UI更新的主要方法。
>
> **（2）replaceState()** replaceState()方法与setState()类似，但是方法只会保留nextState中状态，原state不在nextState中的状态都会被删除。其语法如下：
>
> ```javascript
> replaceState(object nextState[, function callback])
> ```
>
> - nextState，将要设置的新状态，该状态会替换当前的state。
> - callback，可选参数，回调函数。该函数会在replaceState设置成功，且组件重新渲染后调用。
>
> **总结：** setState 是修改其中的部分状态，相当于 Object.assign，只是覆盖，不会减少原来的状态。而replaceState 是完全替换原来的状态，相当于赋值，将原来的 state 替换为另一个对象，如果新状态属性减少，那么 state 中就没有这个状态了。

### 5. 在React中组件的this.state和setState有什么区别？

> this.state通常是用来初始化state的，this.setState是用来修改state值的。如果初始化了state之后再使用this.state，之前的state会被覆盖掉，如果使用this.setState，只会替换掉相应的state值。所以，如果想要修改state的值，就需要使用setState，而不能直接修改state，直接修改state之后页面是不会更新的。

### 6. state 是怎么注入到组件的，从 reducer 到组件经历了什么样的过程

> 通过connect和mapStateToProps将state注入到组件中：
>
> ```javascript
> import { connect } from 'react-redux'
> import { setVisibilityFilter } from '@/reducers/Todo/actions'
> import Link from '@/containers/Todo/components/Link'
> 
> const mapStateToProps = (state, ownProps) => ({
>     active: ownProps.filter === state.visibilityFilter
> })
> 
> const mapDispatchToProps = (dispatch, ownProps) => ({
>     setFilter: () => {
>         dispatch(setVisibilityFilter(ownProps.filter))
>     }
> })
> 
> export default connect(
>     mapStateToProps,
>     mapDispatchToProps
> )(Link)
> ```
>
> 上面代码中，active就是注入到Link组件中的状态。 mapStateToProps（state，ownProps）中带有两个参数，含义是∶
>
> - state-store管理的全局状态对象，所有都组件状态数据都存储在该对象中。
> - ownProps 组件通过props传入的参数。
>
> **reducer 到组件经历的过程：**
>
> - reducer对action对象处理，更新组件状态，并将新的状态值返回store。
> - 通过connect（mapStateToProps，mapDispatchToProps）（Component）对组件 Component进行升级，此时将状态值从store取出并作为props参数传递到组件。
>
> **高阶组件实现源码∶**
>
> ```javascript
> import React from 'react'
> import PropTypes from 'prop-types'
> 
> // 高阶组件 contect 
> export const connect = (mapStateToProps, mapDispatchToProps) => (WrappedComponent) => {
>     class Connect extends React.Component {
>         // 通过对context调用获取store
>         static contextTypes = {
>             store: PropTypes.object
>         }
> 
>         constructor() {
>             super()
>             this.state = {
>                 allProps: {}
>             }
>         }
> 
>         // 第一遍需初始化所有组件初始状态
>         componentWillMount() {
>             const store = this.context.store
>             this._updateProps()
>             store.subscribe(() => this._updateProps()); // 加入_updateProps()至store里的监听事件列表
>         }
> 
>         // 执行action后更新props，使组件可以更新至最新状态（类似于setState）
>         _updateProps() {
>             const store = this.context.store;
>             let stateProps = mapStateToProps ?
>                 mapStateToProps(store.getState(), this.props) : {} // 防止 mapStateToProps 没有传入
>             let dispatchProps = mapDispatchToProps ?
>                 mapDispatchToProps(store.dispatch, this.props) : {
>                                     dispatch: store.dispatch
>                                 } // 防止 mapDispatchToProps 没有传入
>             this.setState({
>                 allProps: {
>                     ...stateProps,
>                     ...dispatchProps,
>                     ...this.props
>                 }
>             })
>         }
> 
>         render() {
>             return <WrappedComponent {...this.state.allProps} />
>         }
>     }
>     return Connect
> }
> ```

### 7. React组件的state和props有什么区别？

> **（1）props**
>
> props是一个从外部传进组件的参数，主要作为就是从父组件向子组件传递数据，它具有可读性和不变性，只能通过外部组件主动传入新的props来重新渲染子组件，否则子组件的props以及展现形式不会改变。
>
> **（2）state**
>
> state的主要作用是用于组件保存、控制以及修改自己的状态，它只能在constructor中初始化，它算是组件的私有属性，不可通过外部访问和修改，只能通过组件内部的this.setState来修改，修改state属性会导致组件的重新渲染。
>
> **（3）区别**
>
> - props 是传递给组件的（类似于函数的形参），而state 是在组件内被组件自己管理的（类似于在一个函数内声明的变量）。
> - props 是不可修改的，所有 React 组件都必须像纯函数一样保护它们的 props 不被更改。
> - state 是在组件中创建的，一般在 constructor中初始化 state。state 是多变的、可以修改，每次setState都异步更新的。

### 8. React中的props为什么是只读的？

> `this.props`是组件之间沟通的一个接口，原则上来讲，它只能从父组件流向子组件。React具有浓重的函数式编程的思想。
>
> 提到函数式编程就要提一个概念：纯函数。它有几个特点：
>
> - 给定相同的输入，总是返回相同的输出。
> - 过程没有副作用。
> - 不依赖外部状态。
>
> `this.props`就是汲取了纯函数的思想。props的不可以变性就保证的相同的输入，页面显示的内容是一样的，并且不会产生副作用

### 9. 在React中组件的props改变时更新组件的有哪些方法？

> 在一个组件传入的props更新时重新渲染该组件常用的方法是在`componentWillReceiveProps`中将新的props更新到组件的state中（这种state被成为派生状态（Derived State）），从而实现重新渲染。React 16.3中还引入了一个新的钩子函数`getDerivedStateFromProps`来专门实现这一需求。
>
> **（1）componentWillReceiveProps（已废弃）**
>
> 在react的componentWillReceiveProps(nextProps)生命周期中，可以在子组件的render函数执行前，通过this.props获取旧的属性，通过nextProps获取新的props，对比两次props是否相同，从而更新子组件自己的state。
>
> 这样的好处是，可以将数据请求放在这里进行执行，需要传的参数则从componentWillReceiveProps(nextProps)中获取。而不必将所有的请求都放在父组件中。于是该请求只会在该组件渲染时才会发出，从而减轻请求负担。
>
> **（2）getDerivedStateFromProps（16.3引入）**
>
> 这个生命周期函数是为了替代`componentWillReceiveProps`存在的，所以在需要使用`componentWillReceiveProps`时，就可以考虑使用`getDerivedStateFromProps`来进行替代。
>
> 两者的参数是不相同的，而`getDerivedStateFromProps`是一个静态函数，也就是这个函数不能通过this访问到class的属性，也并不推荐直接访问属性。而是应该通过参数提供的nextProps以及prevState来进行判断，根据新传入的props来映射到state。
>
> 需要注意的是，**如果props传入的内容不需要影响到你的state，那么就需要返回一个null**，这个返回值是必须的，所以尽量将其写到函数的末尾：
>
> ```javascript
> static getDerivedStateFromProps(nextProps, prevState) {
>     const {type} = nextProps;
>     // 当传入的type发生变化的时候，更新state
>     if (type !== prevState.type) {
>         return {
>             type,
>         };
>     }
>     // 否则，对于state不进行任何操作
>     return null;
> }
> ```

### 10. React中怎么检验props？验证props的目的是什么

> **React**为我们提供了**PropTypes**以供验证使用。当我们向**Props**传入的数据无效（向Props传入的数据类型和验证的数据类型不符）就会在控制台发出警告信息。它可以避免随着应用越来越复杂从而出现的问题。并且，它还可以让程序变得更易读。
>
> ```javascript
> import PropTypes from 'prop-types';
> 
> class Greeting extends React.Component {
>   render() {
>     return (
>       <h1>Hello, {this.props.name}</h1>
>     );
>   }
> }
> 
> Greeting.propTypes = {
>   name: PropTypes.string
> };
> ```
>
> 当然，如果项目汇中使用了TypeScript，那么就可以不用PropTypes来校验，而使用TypeScript定义接口来校验props。

## 三、生命周期

### 1. React的生命周期有哪些？

> React 通常将组件生命周期分为三个阶段：
>
> - 装载阶段（Mount），组件第一次在DOM树中被渲染的过程；
> - 更新过程（Update），组件状态发生变化，重新更新渲染的过程；
> - 卸载过程（Unmount），组件从DOM树中被移除的过程；
>
> ![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1570030fdd4a49f2ad8cfd01a24f80d7~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
>
> #### 1）组件挂载阶段
>
> 挂载阶段组件被创建，然后组件实例插入到 DOM 中，完成组件的第一次渲染，该过程只会发生一次，在此阶段会依次调用以下这些方法：
>
> - constructor
> - getDerivedStateFromProps
> - render
> - componentDidMount
>
> ##### （1）constructor
>
> 组件的构造函数，第一个被执行，若没有显式定义它，会有一个默认的构造函数，但是若显式定义了构造函数，我们必须在构造函数中执行 `super(props)`，否则无法在构造函数中拿到this。
>
> 如果不初始化 state 或不进行方法绑定，则不需要为 React 组件实现构造函数**Constructor**。
>
> constructor中通常只做两件事：
>
> - 初始化组件的 state
> - 给事件处理方法绑定 this
>
> ```javascript
> constructor(props) {
>   super(props);
>   // 不要在构造函数中调用 setState，可以直接给 state 设置初始值
>   this.state = { counter: 0 }
>   this.handleClick = this.handleClick.bind(this)
> }
> ```
>
> ##### （2）getDerivedStateFromProps
>
> ```javascript
> static getDerivedStateFromProps(props, state)
> ```
>
> 这是个静态方法，所以不能在这个函数里使用 `this`，有两个参数 `props` 和 `state`，分别指接收到的新参数和当前组件的 `state` 对象，这个函数会返回一个对象用来更新当前的 `state` 对象，如果不需要更新可以返回 `null`。
>
> 该函数会在装载时，接收到新的 `props` 或者调用了 `setState` 和 `forceUpdate` 时被调用。如当接收到新的属性想修改 `state` ，就可以使用。
>
> ```javascript
> // 当 props.counter 变化时，赋值给 state 
> class App extends React.Component {
>   constructor(props) {
>     super(props)
>     this.state = {
>       counter: 0
>     }
>   }
>   static getDerivedStateFromProps(props, state) {
>     if (props.counter !== state.counter) {
>       return {
>         counter: props.counter
>       }
>     }
>     return null
>   }
>   
>   handleClick = () => {
>     this.setState({
>       counter: this.state.counter + 1
>     })
>   }
>   render() {
>     return (
>       <div>
>         <h1 onClick={this.handleClick}>Hello, world!{this.state.counter}</h1>
>       </div>
>     )
>   }
> }
> ```
>
> 现在可以显式传入 `counter` ，但是这里有个问题，如果想要通过点击实现 `state.counter` 的增加，但这时会发现值不会发生任何变化，一直保持 `props` 传进来的值。这是由于在 React 16.4^ 的版本中 `setState` 和 `forceUpdate` 也会触发这个生命周期，所以当组件内部 `state` 变化后，就会重新走这个方法，同时会把 `state` 值赋值为 `props` 的值。因此需要多加一个字段来记录之前的 `props` 值，这样就会解决上述问题。具体如下：
>
> ```javascript
> // 这里只列出需要变化的地方
> class App extends React.Component {
>   constructor(props) {
>     super(props)
>     this.state = {
>       // 增加一个 preCounter 来记录之前的 props 传来的值
>       preCounter: 0,
>       counter: 0
>     }
>   }
>   static getDerivedStateFromProps(props, state) {
>     // 跟 state.preCounter 进行比较
>     if (props.counter !== state.preCounter) {
>       return {
>         counter: props.counter,
>         preCounter: props.counter
>       }
>     }
>     return null
>   }
>   handleClick = () => {
>     this.setState({
>       counter: this.state.counter + 1
>     })
>   }
>   render() {
>     return (
>       <div>
>         <h1 onClick={this.handleClick}>Hello, world!{this.state.counter}</h1>
>       </div>
>     )
>   }
> }
> ```
>
> ##### （3）render
>
> render是React 中最核心的方法，一个组件中必须要有这个方法，它会根据状态 `state` 和属性 `props` 渲染组件。这个函数只做一件事，就是返回需要渲染的内容，所以不要在这个函数内做其他业务逻辑，通常调用该方法会返回以下类型中一个：
>
> - **React 元素**：这里包括原生的 DOM 以及 React 组件；
> - **数组和 Fragment（片段）**：可以返回多个元素；
> - **Portals（插槽）**：可以将子元素渲染到不同的 DOM 子树种；
> - **字符串和数字**：被渲染成 DOM 中的 text 节点；
> - **布尔值或 null**：不渲染任何内容。
>
> ##### （4）componentDidMount()
>
> componentDidMount()会**在组件挂载后（插入 DOM 树中）立即调**。该阶段通常进行以下操作：
>
> - 执行依赖于DOM的操作；
> - 发送网络请求；（官方建议）
> - 添加订阅消息（会在componentWillUnmount取消订阅）；
>
> 如果在 `componentDidMount` 中调用 `setState` ，就会触发一次额外的渲染，多调用了一次 `render` 函数，由于它是在浏览器刷新屏幕前执行的，所以用户对此是没有感知的，但是我应当避免这样使用，这样会带来一定的性能问题，尽量是在 `constructor` 中初始化 `state` 对象。
>
> 在组件装载之后，将计数数字变为1：
>
> ```javascript
> class App extends React.Component  {
>   constructor(props) {
>     super(props)
>     this.state = {
>       counter: 0
>     }
>   }
>   componentDidMount () {
>     this.setState({
>       counter: 1
>     })
>   }
>   render ()  {
>     return (
>       <div className="counter">
>         counter值: { this.state.counter }
>       </div>
>     )
>   }
> }
> ```
>
> #### 2）组件更新阶段
>
> 当组件的 `props` 改变了，或组件内部调用了 `setState/forceUpdate`，会触发更新重新渲染，这个过程可能会发生多次。这个阶段会依次调用下面这些方法：
>
> - getDerivedStateFromProps
> - shouldComponentUpdate
> - render
> - getSnapshotBeforeUpdate
> - componentDidUpdate
>
> ##### （1）shouldComponentUpdate
>
> ```javascript
> shouldComponentUpdate(nextProps, nextState)
> ```
>
> 在说这个生命周期函数之前，来看两个问题：
>
> - **setState 函数在任何情况下都会导致组件重新渲染吗？例如下面这种情况：**
>
> ```javascript
> this.setState({number: this.state.number})
> ```
>
> - **如果没有调用 setState，props 值也没有变化，是不是组件就不会重新渲染？**
>
> 第一个问题答案是 **会** ，第二个问题如果是父组件重新渲染时，不管传入的 props 有没有变化，都会引起子组件的重新渲染。
>
> 那么有没有什么方法解决在这两个场景下不让组件重新渲染进而提升性能呢？这个时候 `shouldComponentUpdate` 登场了，这个生命周期函数是用来提升速度的，它是在重新渲染组件开始前触发的，默认返回 `true`，可以比较 `this.props` 和 `nextProps` ，`this.state` 和 `nextState` 值是否变化，来确认返回 true 或者 `false`。当返回 `false` 时，组件的更新过程停止，后续的 `render`、`componentDidUpdate` 也不会被调用。
>
> **注意：** 添加 `shouldComponentUpdate` 方法时，不建议使用深度相等检查（如使用 `JSON.stringify()`），因为深比较效率很低，可能会比重新渲染组件效率还低。而且该方法维护比较困难，建议使用该方法会产生明显的性能提升时使用。
>
> ##### （2）getSnapshotBeforeUpdate
>
> ```javascript
> getSnapshotBeforeUpdate(prevProps, prevState)
> ```
>
> 这个方法在 `render` 之后，`componentDidUpdate` 之前调用，有两个参数 `prevProps` 和 `prevState`，表示更新之前的 `props` 和 `state`，这个函数必须要和 `componentDidUpdate` 一起使用，并且要有一个返回值，默认是 `null`，这个返回值作为第三个参数传给 `componentDidUpdate`。
>
> ##### （3）componentDidUpdate
>
> componentDidUpdate() 会在更新后会被立即调用，首次渲染不会执行此方法。 该阶段通常进行以下操作：
>
> - 当组件更新后，对 DOM 进行操作；
> - 如果你对更新前后的 props 进行了比较，也可以选择在此处进行网络请求；（例如，当 props 未发生变化时，则不会执行网络请求）。
>
> ```javascript
> componentDidUpdate(prevProps, prevState, snapshot){}
> ```
>
> 该方法有三个参数：
>
> - prevProps: 更新前的props
> - prevState: 更新前的state
> - snapshot: getSnapshotBeforeUpdate()生命周期的返回值
>
> #### 3）组件卸载阶段
>
> 卸载阶段只有一个生命周期函数，componentWillUnmount() 会在组件卸载及销毁之前直接调用。在此方法中执行必要的清理操作：
>
> - 清除 timer，取消网络请求或清除
> - 取消在 componentDidMount() 中创建的订阅等；
>
> 这个生命周期在一个组件被卸载和销毁之前被调用，因此你不应该再这个方法中使用 `setState`，因为组件一旦被卸载，就不会再装载，也就不会重新渲染。
>
> #### 4）错误处理阶段
>
> componentDidCatch(error, info)，此生命周期在后代组件抛出错误后被调用。 它接收两个参数∶
>
> - error：抛出的错误。
> - info：带有 componentStack key 的对象，其中包含有关组件引发错误的栈信息
>
> React常见的生命周期如下： ![image.png](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/18842f9ec3674acdbdd7ff35f9683200~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp) **React常见生命周期的过程大致如下：**
>
> - 挂载阶段，首先执行constructor构造方法，来创建组件
> - 创建完成之后，就会执行render方法，该方法会返回需要渲染的内容
> - 随后，React会将需要渲染的内容挂载到DOM树上
> - **挂载完成之后就会执行componentDidMount生命周期函数**
> - 如果我们给组件创建一个props（用于组件通信）、调用setState（更改state中的数据）、调用forceUpdate（强制更新组件）时，都会重新调用render函数
> - render函数重新执行之后，就会重新进行DOM树的挂载
> - **挂载完成之后就会执行componentDidUpdate生命周期函数**
> - **当移除组件时，就会执行componentWillUnmount生命周期函数**
>
> **React主要生命周期总结：**
>
> 1. **getDefaultProps**：这个函数会在组件创建之前被调用一次（有且仅有一次），它被用来初始化组件的 Props；
> 2. **getInitialState**：用于初始化组件的 state 值；
> 3. **componentWillMount**：在组件创建后、render 之前，会走到 componentWillMount 阶段。这个阶段我个人一直没用过、非常鸡肋。后来React 官方已经不推荐大家在 componentWillMount 里做任何事情、到现在 **React16 直接废弃了这个生命周期**，足见其鸡肋程度了；
> 4. **render**：这是所有生命周期中唯一一个你必须要实现的方法。一般来说需要返回一个 jsx 元素，这时 React 会根据 props 和 state 来把组件渲染到界面上；不过有时，你可能不想渲染任何东西，这种情况下让它返回 null 或者 false 即可；
> 5. **componentDidMount**：会在组件挂载后（插入 DOM 树中后）立即调用，标志着组件挂载完成。一些操作如果依赖获取到 DOM 节点信息，我们就会放在这个阶段来做。此外，这还是 React 官方推荐的发起 ajax 请求的时机。该方法和 componentWillMount 一样，有且仅有一次调用。

### 2. React 废弃了哪些生命周期？为什么？

> 被废弃的三个函数都是在render之前，因为fber的出现，很可能因为高优先级任务的出现而打断现有任务导致它们会被执行多次。另外的一个原因则是，React想约束使用者，好的框架能够让人不得已写出容易维护和扩展的代码，这一点又是从何谈起，可以从新增加以及即将废弃的生命周期分析入手
>
> **1) componentWillMount**
>
> 首先这个函数的功能完全可以使用componentDidMount和 constructor来代替，异步获取的数据的情况上面已经说明了，而如果抛去异步获取数据，其余的即是初始化而已，这些功能都可以在constructor中执行，除此之外，如果在 willMount 中订阅事件，但在服务端这并不会执行 willUnMount事件，也就是说服务端会导致内存泄漏所以componentWilIMount完全可以不使用，但使用者有时候难免因为各 种各样的情况在 componentWilMount中做一些操作，那么React为了约束开发者，干脆就抛掉了这个API
>
> **2) componentWillReceiveProps**
>
> 在老版本的 React 中，如果组件自身的某个 state 跟其 props 密切相关的话，一直都没有一种很优雅的处理方式去更新 state，而是需要在 componentWilReceiveProps 中判断前后两个 props 是否相同，如果不同再将新的 props更新到相应的 state 上去。这样做一来会破坏 state 数据的单一数据源，导致组件状态变得不可预测，另一方面也会增加组件的重绘次数。类似的业务需求也有很多，如一个可以横向滑动的列表，当前高亮的 Tab 显然隶属于列表自身的时，根据传入的某个值，直接定位到某个 Tab。为了解决这些问题，React引入了第一个新的生命周期：getDerivedStateFromProps。它有以下的优点∶
>
> - getDSFP是静态方法，在这里不能使用this，也就是一个纯函数，开发者不能写出副作用的代码
> - 开发者只能通过prevState而不是prevProps来做对比，保证了state和props之间的简单关系以及不需要处理第一次渲染时prevProps为空的情况
> - 基于第一点，将状态变化（setState）和昂贵操作（tabChange）区分开，更加便于 render 和 commit 阶段操作或者说优化。
>
> **3) componentWillUpdate**
>
> 与 componentWillReceiveProps 类似，许多开发者也会在 componentWillUpdate 中根据 props 的变化去触发一些回调 。 但不论是 componentWilReceiveProps 还 是 componentWilUpdate，都有可能在一次更新中被调用多次，也就是说写在这里的回调函数也有可能会被调用多次，这显然是不可取的。与 componentDidMount 类 似， componentDidUpdate 也不存在这样的问题，一次更新中 componentDidUpdate 只会被调用一次，所以将原先写在 componentWillUpdate 中 的 回 调 迁 移 至 componentDidUpdate 就可以解决这个问题。
>
> 另外一种情况则是需要获取DOM元素状态，但是由于在fber中，render可打断，可能在wilMount中获取到的元素状态很可能与实际需要的不同，这个通常可以使用第二个新增的生命函数的解决 getSnapshotBeforeUpdate(prevProps, prevState)
>
> **4) getSnapshotBeforeUpdate(prevProps, prevState)**
>
> 返回的值作为componentDidUpdate的第三个参数。与willMount不同的是，getSnapshotBeforeUpdate会在最终确定的render执行之前执行，也就是能保证其获取到的元素状态与didUpdate中获取到的元素状态相同。官方参考代码：
>
> ```javascript
> class ScrollingList extends React.Component {
>   constructor(props) {
>     super(props);
>     this.listRef = React.createRef();
>   }
> 
>   getSnapshotBeforeUpdate(prevProps, prevState) {
>     // 我们是否在 list 中添加新的 items ？
>     // 捕获滚动位置以便我们稍后调整滚动位置。
>     if (prevProps.list.length < this.props.list.length) {
>       const list = this.listRef.current;
>       return list.scrollHeight - list.scrollTop;
>     }
>     return null;
>   }
> 
>   componentDidUpdate(prevProps, prevState, snapshot) {
>     // 如果我们 snapshot 有值，说明我们刚刚添加了新的 items，
>     // 调整滚动位置使得这些新 items 不会将旧的 items 推出视图。
>     //（这里的 snapshot 是 getSnapshotBeforeUpdate 的返回值）
>     if (snapshot !== null) {
>       const list = this.listRef.current;
>       list.scrollTop = list.scrollHeight - snapshot;
>     }
>   }
> 
>   render() {
>     return (
>       <div ref={this.listRef}>{/* ...contents... */}</div>
>     );
>   }
> }
> ```

### 3. React 16.X 中 props 改变后在哪个生命周期中处理

> **在getDerivedStateFromProps中进行处理。**
>
> 这个生命周期函数是为了替代`componentWillReceiveProps`存在的，所以在需要使用`componentWillReceiveProps`时，就可以考虑使用`getDerivedStateFromProps`来进行替代。
>
> 两者的参数是不相同的，而`getDerivedStateFromProps`是一个静态函数，也就是这个函数不能通过this访问到class的属性，也并不推荐直接访问属性。而是应该通过参数提供的nextProps以及prevState来进行判断，根据新传入的props来映射到state。
>
> 需要注意的是，**如果props传入的内容不需要影响到你的state，那么就需要返回一个null**，这个返回值是必须的，所以尽量将其写到函数的末尾：
>
> ```javascript
> static getDerivedStateFromProps(nextProps, prevState) {
>     const {type} = nextProps;
>     // 当传入的type发生变化的时候，更新state
>     if (type !== prevState.type) {
>         return {
>             type,
>         };
>     }
>     // 否则，对于state不进行任何操作
>     return null;
> }
> ```

### 4. React 性能优化在哪个生命周期？它优化的原理是什么？

> react的父级组件的render函数重新渲染会引起子组件的render方法的重新渲染。但是，有的时候子组件的接受父组件的数据没有变动。子组件render的执行会影响性能，这时就可以使用shouldComponentUpdate来解决这个问题。
>
> 使用方法如下：
>
> ```javascript
> shouldComponentUpdate(nexrProps) {
>     if (this.props.num === nexrProps.num) {
>         return false
>     }
>     return true;
> }
> ```
>
> **shouldComponentUpdate提供了两个参数nextProps和nextState，表示下一次props和一次state的值，当函数返回false时候，render()方法不执行，组件也就不会渲染，返回true时，组件照常重渲染**。此方法就是拿当前props中值和下一次props中的值进行对比，数据相等时，返回false，反之返回true。
>
> 需要注意，在进行新旧对比的时候，是**浅对比，**也就是说如果比较的数据时引用数据类型，只要数据的引用的地址没变，即使内容变了，也会被判定为true。
>
> 面对这个问题，可以使用如下方法进行解决： （1）使用setState改变数据之前，先采用ES6中assgin进行拷贝，但是assgin只深拷贝的数据的第一层，所以说不是最完美的解决办法：
>
> ```javascript
> const o2 = Object.assign({},this.state.obj)
>     o2.student.count = '00000';
>     this.setState({
>         obj: o2,
>     })
> ```
>
> （2）使用JSON.parse(JSON.stringfy())进行深拷贝，但是遇到数据为undefined和函数时就会错。
>
> ```javascript
> const o2 = JSON.parse(JSON.stringify(this.state.obj))
>     o2.student.count = '00000';
>     this.setState({
>         obj: o2,
>     })
> ```

### 5. state 和 props 触发更新的生命周期分别有什么区别？

> **state 更新流程：** <img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/1451ff964c89442c9384478e494ce283~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp" alt="img" style="zoom:50%;" /> 这个过程当中涉及的函数：
>
> 1. shouldComponentUpdate: 当组件的 state 或 props 发生改变时，都会首先触发这个生命周期函数。它会接收两个参数：nextProps, nextState——它们分别代表传入的新 props 和新的 state 值。拿到这两个值之后，我们就可以通过一些对比逻辑来决定是否有 re-render（重渲染）的必要了。如果该函数的返回值为 false，则生命周期终止，反之继续；
>
> > 注意：此方法仅作为**性能优化的方式**而存在。不要企图依靠此方法来“阻止”渲染，因为这可能会产生 bug。应该**考虑使用内置的 PureComponent 组件**，而不是手动编写 `shouldComponentUpdate()`
>
> 2. componentWillUpdate：当组件的 state 或 props 发生改变时，会在渲染之前调用 componentWillUpdate。componentWillUpdate **是 React16 废弃的三个生命周期之一**。过去，我们可能希望能在这个阶段去收集一些必要的信息（比如更新前的 DOM 信息等等），现在我们完全可以在 React16 的 getSnapshotBeforeUpdate 中去做这些事；
>
> 3. componentDidUpdate：componentDidUpdate() 会在UI更新后会被立即调用。它接收 prevProps（上一次的 props 值）作为入参，也就是说在此处我们仍然可以进行 props 值对比（再次说明 componentWillUpdate 确实鸡肋哈）。
>
> ****
>
> **props 更新流程：** ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/774a110865454a7b8cdc5a01562b9d0c~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp) 相对于 state 更新，props 更新后唯一的区别是增加了对 componentWillReceiveProps 的调用。关于 componentWillReceiveProps，需要知道这些事情：
>
> - componentWillReceiveProps：它在Component接受到新的 props 时被触发。componentWillReceiveProps 会接收一个名为 nextProps 的参数（对应新的 props 值）。**该生命周期是 React16 废弃掉的三个生命周期之一**。在它被废弃前，可以用它来比较 this.props 和 nextProps 来重新setState。在 React16 中，用一个类似的新生命周期 getDerivedStateFromProps 来代替它。

### 6. React中发起网络请求应该在哪个生命周期中进行？为什么？

> 对于异步请求，最好放在componentDidMount中去操作，对于同步的状态改变，可以放在componentWillMount中，一般用的比较少。
>
> 如果认为在componentWillMount里发起请求能提早获得结果，这种想法其实是错误的，通常componentWillMount比componentDidMount早不了多少微秒，网络上任何一点延迟，这一点差异都可忽略不计。
>
> **react的生命周期：** constructor() -> componentWillMount() -> render() -> componentDidMount()
>
> 上面这些方法的调用是有次序的，由上而下依次调用。
>
> - constructor被调用是在组件准备要挂载的最开始，此时组件尚未挂载到网页上。
> - componentWillMount方法的调用在constructor之后，在render之前，在这方法里的代码调用setState方法不会触发重新render，所以它一般不会用来作加载数据之用。
> - componentDidMount方法中的代码，是在组件已经完全挂载到网页上才会调用被执行，所以可以保证数据的加载。此外，在这方法中调用setState方法，会触发重新渲染。所以，官方设计这个方法就是用来加载外部数据用的，或处理其他的副作用代码。与组件上的数据无关的加载，也可以在constructor里做，但constructor是做组件state初绐化工作，并不是做加载数据这工作的，constructor里也不能setState，还有加载的时间太长或者出错，页面就无法加载出来。所以有副作用的代码都会集中在componentDidMount方法里。
>
> 总结：
>
> - 跟服务器端渲染（同构）有关系，如果在componentWillMount里面获取数据，fetch data会执行两次，一次在服务器端一次在客户端。在componentDidMount中可以解决这个问题，componentWillMount同样也会render两次。
> - 在componentWillMount中fetch data，数据一定在render后才能到达，如果忘记了设置初始状态，用户体验不好。
> - react16.0以后，componentWillMount可能会被执行多次。

### 7. React 16中新生命周期有哪些

> 关于 React16 开始应用的新生命周期： ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f2f59925a79e470eb730e605854e81b8~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp) 可以看出，React16 自上而下地对生命周期做了另一种维度的解读：
>
> - **Render 阶段**：用于计算一些必要的状态信息。这个阶段可能会被 React 暂停，这一点和 React16 引入的 Fiber 架构（我们后面会重点讲解）是有关的；
> - **Pre-commit阶段**：所谓“commit”，这里指的是“更新真正的 DOM 节点”这个动作。所谓 Pre-commit，就是说我在这个阶段其实还并没有去更新真实的 DOM，不过 DOM 信息已经是可以读取的了；
> - **Commit 阶段**：在这一步，React 会完成真实 DOM 的更新工作。Commit 阶段，我们可以拿到真实 DOM（包括 refs）。
>
> 与此同时，新的生命周期在流程方面，仍然遵循“挂载”、“更新”、“卸载”这三个广义的划分方式。它们分别对应到：
>
> - 挂载过程：
>   - **constructor**
>   - **getDerivedStateFromProps**
>   - **render**
>   - **componentDidMount**
> - 更新过程：
>   - **getDerivedStateFromProps**
>   - **shouldComponentUpdate**
>   - **render**
>   - **getSnapshotBeforeUpdate**
>   - **componentDidUpdate**
> - 卸载过程：
>   - **componentWillUnmount**

## 四.React 组件间通信方式

> React组件间通信常见的几种情况:

- 父组件向子组件通信
- 子组件向父组件通信
- 跨级组件通信
- 非嵌套关系的组件通信

###  1.父组件向子组件通信

> 父组件通过 props 向子组件传递需要的信息

```js
// 子组件: Child
const Child = props =>{
  return <p>{props.name}</p>
}
// 父组件 Parent
const Parent = ()=>{
    return <Child name="react"></Child>
}
```

### 2.子组件向父组件通信

> props+回调的方式
>
> 父组件向子组件传递函数，在子组件中调用的时候传递参数
>
> ```js
> // 子组件: Child
> const Child = props =>{
>   const cb = msg =>{
>       return ()=>{
>           props.callback(msg)
>       }
>   }
>   return (
>       <button onClick={cb("你好!")}>你好</button>
>   )
> }
> // 父组件 Parent
> class Parent extends Component {
>     callback(msg){
>         console.log(msg)
>     }
>     render(){
>         return <Child callback={this.callback.bind(this)}></Child>    
>     }
> }
> ```

### 3. 跨级组件的通信方式？

> 父组件向子组件的子组件通信，向更深层子组件通信：
>
> - 使用props，利用中间组件层层传递,但是如果父组件结构较深，那么中间每一层组件都要去传递props，增加了复杂度，并且这些props并不是中间组件自己需要的。
> - 使用context，context相当于一个大容器，可以把要通信的内容放在这个容器中，这样不管嵌套多深，都可以随意取用，对于跨越多层的全局数据可以使用context实现。
>
> ```js
> // context方式实现跨级组件通信 
> // Context 设计目的是为了共享那些对于一个组件树而言是“全局”的数据
> `1) 创建Context容器对象：
> 	const XxxContext = React.createContext() 
> `
> const BatteryContext = createContext();
> // 父组件
> `2) 渲染子组时，外面包裹xxxContext.Provider, 通过value属性给后代组件传递数据：
> 	<xxxContext.Provider value={数据}>
> 		子组件
>     </xxxContext.Provider>
> `
> class Parent extends Component {
>       state = {
>           color:"red"
>       }
>       render(){
>           const {color} = this.state
>           return (
>           <BatteryContext.Provider value={color}>
>               <Child></Child>
>           </BatteryContext.Provider>
>           )
>       }
> }
> //  子组件
> const Child = () =>{
>     return (
>         <GrandChild/>
>     )
> }
> //  子组件的子组件 
> `
> 3) 后代组件读取数据：
> 
> 	//第一种方式:仅适用于类组件 
> 	  static contextType = xxxContext  // 声明接收context
> 	  this.context // 读取context中的value数据
> 	  
> 	//第二种方式: 函数组件与类组件都可以
> 	  <xxxContext.Consumer>
> 	    {
> 	      value => ( // value就是context中的value数据
> 	        要显示的内容
> 	      )
> 	    }
> 	  </xxxContext.Consumer>
> `
> class GrandChild extends Component {
>     render(){
>         return (
>             <BatteryContext.Consumer>
>                 {
>                     color => <h1 style={{"color":color}}>我是红色的:{color}</h1>
>                 }
>             </BatteryContext.Consumer>
>         )
>     }
> }
> ```

### 4.非嵌套关系组件的通信方式？

> 即没有任何包含关系的组件，包括兄弟组件以及不在同一个父级中的非兄弟组件。
>
> - 可以使用自定义事件通信（发布订阅模式）
>
>   ```js
>   import PubSub from 'pubsub-js'
>   ```
>
> - 可以通过redux等进行全局状态管理
>
> - 如果是兄弟组件通信，可以找到这两个兄弟节点共同的父节点, 结合父子间通信方式进行通信。

### 5.如何解决 props 层级过深的问题

> - 使用Context API：提供一种组件之间的状态共享，而不必通过显式组件树逐层传递props；
> - 使用Redux等状态库。

### 6. 组件通信的方式有哪些

> **⽗组件向⼦组件通讯**: ⽗组件可以向⼦组件通过传 props 的⽅式，向⼦组件进⾏通讯
>
> **⼦组件向⽗组件通讯**: props+回调的⽅式，⽗组件向⼦组件传递props进⾏通讯，此props为作⽤域为⽗组件⾃身的函 数，⼦组件调⽤该函数，将⼦组件想要传递的信息，作为参数，传递到⽗组件的作⽤域中
>
> **兄弟组件通信**: 找到这两个兄弟节点共同的⽗节点,结合上⾯两种⽅式由⽗节点转发信息进⾏通信
>
> **跨层级通信**: Context 设计⽬的是为了共享那些对于⼀个组件树⽽⾔是“全局”的数据，例如当前认证的⽤户、主题或⾸选语⾔，对于跨越多层的全局数据通过 Context 通信再适合不过
>
> **发布订阅模式**: 发布者发布事件，订阅者监听事件并做出反应,我们可以通过引⼊event模块进⾏通信
>
> **全局状态管理⼯具**: 借助Redux或者Mobx等全局状态管理⼯具进⾏通信,这种⼯具会维护⼀个全局状态中⼼Store,并根据不同的事件产⽣新的状态

## 五.React Router

### 1.React Router v6之前版本的使用

> #### 1.1. 路由的基本使用
>
> ​      1.明确好界面中的导航区、展示区
> ​      2.导航区的a标签改为Link标签
> ​            <Link to="/xxxxx">Demo</Link>
> ​      3.展示区写Route标签进行路径的匹配
> ​            <Route path='/xxxx' component={Demo}/>
> ​      4.<App>的最外侧包裹了一个<BrowserRouter>或<HashRouter>
>
> #### 1.2.路由组件与一般组件
>
> ​      1.写法不同：
> ​            一般组件：<Demo/>
> ​            路由组件：<Route path="/demo" component={Demo}/>
> ​      2.存放位置不同：
> ​            一般组件：components
> ​            路由组件：pages
> ​      3.接收到的props不同：
> ​            一般组件：写组件标签时传递了什么，就能收到什么
> ​            路由组件：接收到三个固定的属性
> ​                      history:
> ​                            go: ƒ go(n)
> ​                            goBack: ƒ goBack()
> ​                            goForward: ƒ goForward()
> ​                            push: ƒ push(path, state)
> ​                            replace: ƒ replace(path, state)
> ​                      location:
> ​                            pathname: "/about"
> ​                            search: ""
> ​                            state: undefined
> ​                      match:
> ​                            params: {}
> ​                            path: "/about"
> ​                            url: "/about"
>
> #### 1.3.NavLink与封装NavLink
>
> > NavLink可以实现路由链接的高亮，通过activeClassName指定样式名
>
> #### 1.4.Switch的使用
>
> ​        1.通常情况下，path和component是一一对应的关系。
> ​        2.Switch可以提高路由匹配效率(单一匹配)。
>
> #### 1.5.解决多级路径刷新页面样式丢失的问题
>
> ​        1.public/index.html 中 引入样式时不写 ./ 写 / （常用）
> ​        2.public/index.html 中 引入样式时不写 ./ 写 %PUBLIC_URL% （常用）
> ​        3.使用HashRouter
>
> #### 1.6.路由的严格匹配与模糊匹配
>
> ​        1.默认使用的是模糊匹配（简单记：【输入的路径】必须包含要【匹配的路径】，且顺序要一致）
> ​        2.开启严格匹配：<Route exact={true} path="/about" component={About}/>
> ​        3.严格匹配不要随便开启，需要再开，有些时候开启会导致无法继续匹配二级路由
>
> #### 1.7.Redirect的使用
>
> ​        1.一般写在所有路由注册的最下方，当所有路由都无法匹配时，跳转到Redirect指定的路由
> ​        2.具体编码：
> ​            <Switch>
> ​              <Route path="/about" component={About}/>
> ​              <Route path="/home" component={Home}/>
> ​              <Redirect to="/about"/>
> ​            </Switch>
>
> #### 1.8.嵌套路由
>
> ​        1.注册子路由时要写上父路由的path值
> ​        2.路由的匹配是按照注册路由的顺序进行的
>
> #### 1.9.向路由组件传递参数
>
> ​        1.params参数
> ​              路由链接(携带参数)：<Link to='/demo/test/tom/18'}>详情</Link>
> ​              注册路由(声明接收)：<Route path="/demo/test/:name/:age" component={Test}/>
> ​              接收参数：this.props.match.params
> ​        2.search参数
> ​              路由链接(携带参数)：<Link to='/demo/test?name=tom&age=18'}>详情</Link>
> ​              注册路由(无需声明，正常注册即可)：<Route path="/demo/test" component={Test}/>
> ​              接收参数：this.props.location.search
> ​              备注：获取到的search是urlencoded编码字符串，需要借助querystring解析
> ​        3.state参数
> ​              路由链接(携带参数)：<Link to={{pathname:'/demo/test',state:{name:'tom',age:18}}}>详情</Link>
> ​              注册路由(无需声明，正常注册即可)：<Route path="/demo/test" component={Test}/>
> ​              接收参数：this.props.location.state
> ​              备注：刷新也可以保留住参数
>
> #### 1.10.编程式路由导航
>
> ​          借助this.prosp.history对象上的API对操作路由跳转、前进、后退
> ​              -this.prosp.history.push()
> ​              -this.prosp.history.replace()
> ​              -this.prosp.history.goBack()
> ​              -this.prosp.history.goForward()
> ​              -this.prosp.history.go()
>
> #### 1.11.BrowserRouter与HashRouter的区别
>
> ​      1.底层原理不一样：
> ​            BrowserRouter使用的是H5的history API，不兼容IE9及以下版本。
> ​            HashRouter使用的是URL的哈希值。
> ​      2.path表现形式不一样
> ​            BrowserRouter的路径中没有#,例如：localhost:3000/demo/test
> ​            HashRouter的路径包含#,例如：localhost:3000/#/demo/test
> ​      3.刷新后对路由state参数的影响
> ​            (1).BrowserRouter没有任何影响，因为state保存在history对象中。
> ​            (2).HashRouter刷新后会导致路由state参数的丢失！！！
> ​      4.备注：HashRouter可以用于解决一些路径错误相关的问题。

### 2.React Router V6

> #### 概述
>
> 1. React Router 以三个不同的包发布到 npm 上，它们分别为：
>
>    1. react-router: 路由的核心库，提供了很多的：组件、钩子。
>    2. <strong style="color:#dd4d40">**react-router-dom:**</strong > <strong style="color:#dd4d40">包含react-router所有内容，并添加一些专门用于 DOM 的组件，例如 `<BrowserRouter>`等 </strong>。
>    3. react-router-native: 包括react-router所有内容，并添加一些专门用于ReactNative的API，例如:`<NativeRouter>`等。
>
> 2. 与React Router 5.x 版本相比，改变了什么？
>
>    1. 内置组件的变化：移除`<Switch/>` ，新增 `<Routes/>`等。
>
>    2. 语法的变化：`component={About}` 变为 `element={<About/>}`等。
>
>    2. 去除Switch中的`<Redirect>`，用react-router-dom中的Redirect 替代，或者用 `<Navigate>` 实现
>
>    2. `<Link to>`支持相对位置
>
>    2. 使用useNavigate代替useHistory
>    
>    5. 新增多个hook：`useParams`、`useNavigate`、`useMatch`等。
>    
>    7. <strong style="color:#dd4d40">官方明确推荐函数式组件了！！！</strong>
>    
>       ......
>
> #### Component
>
> ##### 1. `<BrowserRouter>`
>
> 1. 说明：`<BrowserRouter> `用于包裹整个应用。
>
> 2. 示例代码：
>
>    ```jsx
>    import React from "react";
>    import ReactDOM from "react-dom";
>    import { BrowserRouter } from "react-router-dom";
>       
>    ReactDOM.render(
>      <BrowserRouter>
>        {/* 整体结构（通常为App组件） */}
>      </BrowserRouter>,root
>    );
>    ```
>
> ##### 2. `<HashRouter>`
>
> 1. 说明：作用与`<BrowserRouter>`一样，但`<HashRouter>`修改的是地址栏的hash值。
> 2. 备注：6.x版本中`<HashRouter>`、`<BrowserRouter> ` 的用法与 5.x 相同。
>
> ##### 3. `<Routes/> 与 <Route/>`
>
> 1. v6版本中移出了先前的`<Switch>`，引入了新的替代者：`<Routes>`。
>
> 2. `<Routes>` 和 `<Route>`要配合使用，且必须要用`<Routes>`包裹`<Route>`。
>
> 3. `<Route>` 相当于一个 if 语句，如果其路径与当前 URL 匹配，则呈现其对应的组件。
>
> 4.  `<Route caseSensitive>` 属性用于指定：匹配时是否区分大小写（默认为 false）。
>
> 5. 当URL发生变化时，`<Routes> `都会查看其所有子` <Route>` 元素以找到最佳匹配并呈现组件 。
>
> 6.  `<Route>` 也可以嵌套使用，且可配合`useRoutes()`配置 “路由表” ，但需要通过 `<Outlet>` 组件来渲染其子路由。
>
> 7. 示例代码：
>
>    ```jsx
>          <Routes>
>            <Route path="/" element={<Navigate to="/article"/>}/>
>            <Route path="/article/" element={<Sidebar/>}>
>              <Route index element={<Article/>}/>
>              <Route path="about" element={<About/>}/>
>              <Route path=":id" element={<ArticleDetail/>}/>
>              <Route path="cateArticle/:cate" element={<CateArticle/>}></Route>
>            </Route>
>            <Route path="/login" element={<Login/>}/>
>            <Route path="/home/" element={<Home/>}>
>              <Route index element={<Welcome/>}/>
>              <Route path="articleEdit/" element={<ArticleEdit/>}>
>                <Route path=":id" element={<ArticleEdit/>}/>
>              </Route>
>              <Route path="userManage" element={<UserManage/>}/>
>              <Route path="articleManage" element={<ArticleManage/>}/>
>              <Route path="cateManage" element={<CateManage/>}></Route>
>            </Route>
>          </Routes>
>    ```
>
> ##### 4. `<Link>`
>
> 1. 作用: 修改URL，且不发送网络请求（路由链接）。
>
> 2. 注意: 外侧需要用`<BrowserRouter>`或`<HashRouter>`包裹。
>
> 3. 示例代码：
>
>    ```jsx
>    import { Link } from "react-router-dom";
>       
>    function Test() {
>      return (
>        <div>
>         <Link to="/路径">按钮</Link>
>        </div>
>      );
>    }
>    ```
>
> ##### 5. `<NavLink>`
>
> 1. 作用: 与`<Link>`组件类似，且可实现导航的“高亮”效果。
>
> 2. 示例代码：
>
>    ```jsx
>    // 注意: NavLink默认类名是active，下面是指定自定义的class
>       
>    //自定义样式
>    <NavLink
>        to="login"
>        className={({ isActive }) => {
>            console.log('home', isActive)
>            return isActive ? 'base one' : 'base'
>        }}
>    >login</NavLink>
>       
>    /*
>     默认情况下，当Home的子组件匹配成功，Home的导航也会高亮，
>     当NavLink上添加了end属性后，若Home的子组件匹配成功，则Home的导航没有高亮效果。
>    */
>    <NavLink to="home" end >home</NavLink>
>    ```
>
> ##### 6. `<Navigate>`
>
> 1. 作用：只要`<Navigate>`组件被渲染，就会修改路径，切换视图。
>
> 2. `replace`属性用于控制跳转模式（push 或 replace，默认是push）。
>
> 3. 示例代码：
>
>    ```jsx
>    import React,{useState} from 'react'
>    import {Navigate} from 'react-router-dom'
>       
>    export default function Home() {
>     const [sum,setSum] = useState(1)
>     return (
>       <div>
>         <h3>我是Home的内容</h3>
>         {/* 根据sum的值决定是否切换视图 */}
>         {sum === 1 ? <h4>sum的值为{sum}</h4> : <Navigate to="/about" replace={true}/>}
>         <button onClick={()=>setSum(2)}>点我将sum变为2</button>
>       </div>
>     )
>    }
>    ```
>
> ##### 7. `<Outlet>`
>
> 1. 当`<Route>`产生嵌套时，渲染其对应的后续子路由。
>
> 2. 示例代码：
>
>    ```jsx
>    //根据路由表生成对应的路由规则
>    const element = useRoutes([
>      {
>        path:'/about',
>        element:<About/>
>      },
>      {
>        path:'/home',
>        element:<Home/>,
>        children:[
>          {
>            path:'news',
>            element:<News/>
>          },
>          {
>            path:'message',
>            element:<Message/>,
>          }
>        ]
>      }
>    ])
>       
>    //Home.js
>    import React from 'react'
>    import {NavLink,Outlet} from 'react-router-dom'
>       
>    export default function Home() {
>     return (
>       <div>
>         <h2>Home组件内容</h2>
>         <div>
>           <ul className="nav nav-tabs">
>             <li>
>               <NavLink className="list-group-item" to="news">News</NavLink>
>             </li>
>             <li>
>               <NavLink className="list-group-item" to="message">Message</NavLink>
>             </li>
>           </ul>
>           {/* 指定路由组件呈现的位置 */}
>           <Outlet />
>         </div>
>       </div>
>     )
>    }
>    ```
>
> #### Hooks
>
> ##### 1. useRoutes()
>
> 1. 作用：根据路由表，动态创建`<Routes>`和`<Route>`。
>
> 2. 示例代码：
>
>    ```jsx
>    //路由表配置：src/routes/index.js
>    import About from '../pages/About'
>    import Home from '../pages/Home'
>    import {Navigate} from 'react-router-dom'
>       
>    export default [
>     {
>       path:'/about',
>       element:<About/>
>     },
>     {
>       path:'/home',
>       element:<Home/>
>     },
>     {
>       path:'/',
>       element:<Navigate to="/about"/>
>     }
>    ]
>       
>    //App.jsx
>    import React from 'react'
>    import {NavLink,useRoutes} from 'react-router-dom'
>    import routes from './routes'
>       
>    export default function App() {
>     //根据路由表生成对应的路由规则
>     const element = useRoutes(routes)
>     return (
>       <div>
>         ......
>          {/* 注册路由 */}
>          {element}
>         ......
>       </div>
>     )
>    }
>    ```
>
> ##### 2. useNavigate()
>
> 1. 作用：返回一个函数用来实现编程式导航。
>
> 2. 示例代码：
>
>    ```jsx
>    import React from 'react'
>    import {useNavigate} from 'react-router-dom'
>       
>    export default function Demo() {
>      const navigate = useNavigate()
>      const handle = () => {
>        //第一种使用方式：指定具体的路径
>        navigate('/login', {
>          replace: false,
>          state: {a:1, b:2}
>        }) 
>        //第二种使用方式：传入数值进行前进或后退，类似于5.x中的 history.go()方法
>        navigate(-1)
>      }
>         
>      return (
>        <div>
>          <button onClick={handle}>按钮</button>
>        </div>
>      )
>    }
>    ```
>
> ##### 3. useParams()
>
> 1. 作用：回当前匹配路由的`params`参数，类似于5.x中的`match.params`。
>
> 2. 示例代码：
>
>    ```jsx
>    import React from 'react';
>    import { Routes, Route, useParams } from 'react-router-dom';
>    import User from './pages/User.jsx'
>       
>    function ProfilePage() {
>      // 获取URL中携带过来的params参数
>      let { id } = useParams();
>    }
>       
>    function App() {
>      return (
>        <Routes>
>          <Route path="users/:id" element={<User />}/>
>        </Routes>
>      );
>    }
>    ```
>
> ##### 4. useSearchParams()
>
> 1. 作用：用于读取和修改当前位置的 URL 中的查询字符串。
>
> 2. 返回一个包含两个值的数组，内容分别为：当前的seaech参数、更新search的函数。
>
> 3. 示例代码：
>
>    ```jsx
>    import React from 'react'
>    import {useSearchParams} from 'react-router-dom'
>       
>    export default function Detail() {
>     const [search,setSearch] = useSearchParams()
>     const id = search.get('id')
>     const title = search.get('title')
>     const content = search.get('content')
>     return (
>       <ul>
>         <li>
>           <button onClick={()=>setSearch('id=008&title=哈哈&content=嘻嘻')}>点我更新一下收到的search参数</button>
>         </li>
>         <li>消息编号：{id}</li>
>         <li>消息标题：{title}</li>
>         <li>消息内容：{content}</li>
>       </ul>
>     )
>    }
>    ```
>
> ##### 5. useLocation()
>
> 1. 作用：获取当前 location 信息，对标5.x中的路由组件的`location`属性。
>
> 2. 示例代码：
>
>    ```jsx
>    import React from 'react'
>    import {useLocation} from 'react-router-dom'
>       
>    export default function Detail() {
>     const x = useLocation()
>     //location.search可以接受search参数
>     //location.state可以接受state参数
>     console.log('@',x)
>      // x就是location对象: 
>     /*
>       {
>          hash: "",
>          key: "ah9nv6sz",
>          pathname: "/login",
>          search: "?name=zs&age=18",
>          state: {a: 1, b: 2}
>        }
>     */
>     return (
>       <ul>
>         <li>消息编号：{id}</li>
>         <li>消息标题：{title}</li>
>         <li>消息内容：{content}</li>
>       </ul>
>     )
>    }
>    ```
>
> ##### 6. useMatch()
>
> 1. 作用：返回当前匹配信息，对标5.x中的路由组件的`match`属性。
>
> 2. 示例代码：
>
>    ```jsx
>    <Route path="/login/:page/:pageSize" element={<Login />}/>
>    <NavLink to="/login/1/10">登录</NavLink>
>       
>    export default function Login() {
>      const match = useMatch('/login/:x/:y')
>      console.log(match) //输出match对象
>      //match.params可以接受params参数
>      //match对象内容如下：
>      /*
>       {
>          params: {x: '1', y: '10'}
>          pathname: "/LoGin/1/10"  
>          pathnameBase: "/LoGin/1/10"
>          pattern: {
>           path: '/login/:x/:y', 
>           caseSensitive: false, 
>           end: false
>          }
>        }
>      */
>      return (
>       <div>
>          <h1>Login</h1>
>        </div>
>      )
>    }
>    ```
>
> ##### 7. useInRouterContext()
>
> ​     作用：如果组件在 `<Router>` 的上下文中呈现，则 `useInRouterContext` 钩子返回 true，否则返回 false。
>
> ##### 8. useNavigationType()
>
> 1. 作用：返回当前的导航类型（用户是如何来到当前页面的）。
> 2. 返回值：`POP`、`PUSH`、`REPLACE`。
> 3. 备注：`POP`是指在浏览器中直接打开了这个路由组件（刷新页面）。
>
> ##### 9. useOutlet()
>
> 1. 作用：用来呈现当前组件中渲染的嵌套路由。
>
> 2. 示例代码：
>
>    ```jsx
>    const result = useOutlet()
>    console.log(result)
>    // 如果嵌套路由没有挂载,则result为null
>    // 如果嵌套路由已经挂载,则展示嵌套的路由对象
>    ```
>
> ##### 10.useResolvedPath()
>
> 1. 作用：给定一个 URL值，解析其中的：path、search、hash值。

## 六.Redux

> React是视图层框架。Redux是一个用来管理数据状态和UI状态的JavaScript应用工具。随着JavaScript单页应用（SPA）开发日趋复杂， JavaScript需要管理比任何时候都要多的state（状态）， Redux就是降低管理难度的。（Redux支持React、Angular、jQuery甚至纯JavaScript）
>
> Redux 提供了一个叫 store 的统一仓储库，组件通过 dispatch 将 state 直接传入store，不用通过其他的组件。并且组件通过 subscribe 从 store获取到 state 的改变。使用了 Redux，所有的组件都可以从 store 中获取到所需的 state，他们也能从store 获取到 state 的改变。这比组件之间互相传递数据清晰明朗的多。
>
> **主要解决的问题：** 单纯的Redux只是一个状态机，是没有UI呈现的，react- redux作用是将Redux的状态机和React的UI呈现绑定在一起，当你dispatch action改变state的时候，会自动更新页面。

> **工作流程**
>
> * const store= createStore（fn）生成数据;
>
> * action: {type: Symble('action01), payload:'payload' }定义行为;
>
> * dispatch发起action：store.dispatch(doSomething('action001'));
>
> * reducer：处理action，返回新的state;

```js
//三个核心概念
//1.store:统一仓储库,存放组件间的共用state
//2.action:一个 Action 就是一个简单的 JavaScript 对象{type:xxxx,data:xxxx}
//一个action对象包括两个属性，type表示更新state的方法，data表示传递的数据
//在 Redux 的概念术语中，更新 Store 的状态有且仅有一种方式：那就是调用 dispatch 函数，传递一个 action 给这个函数 

//3.reducer:
//reducer 是一个普通的 JavaScript 函数，它接收两个参数：state 和 action，前者为 Store 中存储的那棵 JavaScript 对象状态树，后者即为我们在组件中 dispatch 的那个 Action。
//reducer 根据 action 的指示，对 state 进行对应的操作，然后返回操作后的 state，Redux Store 会自动保存这份新的 state。
```

> **注意**
>
> Redux 官方社区对 reducer 的约定是一个纯函数，即我们不能直接修改 `state` ，而是可以使用 `{...}` 等对象解构手段返回一个被修改后的新 `state`。
>
> 比如我们对 `state = { a: 1, b: 2 }`  进行修改，将 `a` 替换成 3，我们应该这么做：`newState = { ...state, a: 3 }`，而不应该 `state.a = 3`。 这种不直接修改原对象，而是返回一个新对象的修改，我们称之为 “纯化” 的修改。

```js
//1.首先创建store
//store.js
/* 
	该文件专门用于暴露一个store对象，整个应用只有一个store对象
*/
import {createStore,applyMiddleware} from 'redux'
import reducer from './reducer'
import thunk from 'redux-thunk'  //用于异步请求的redux中间件

const store=createStore(reducer,applyMiddleware(thunk))

export default store
```

```js
//2.action.js 
//和vuex中的action类似都是用于存放修改state的方法的
//action:一个 Action 就是一个简单的 JavaScript 对象{type:xxxx,data:xxxx}
//一个action对象包括两个属性，type表示更新state的方法，data表示传递的数据
import axios from "axios"

export const setListsdata =data=> ({type:'setLists',data}) //同步的修改state的方法

// dispatch为自动接收的store.dispatch函数          
export const getLists = (data) => (dispatch) =>{   //异步修改state的方法
  const {url}=data
  axios.get('/api/'+url)
  .then(function(res){
      let newArr=[]
      newArr.splice(0,newArr.length,...res.data)
      newArr=newArr.reverse()
      dispatch(setListsdata({...data,newArr}))
  })
}
```

```js
//reducer.js
//接收两个参数：state 和 action，前者为 Store 中存储的那棵 JavaScript 对象状态树，后者即为我们在组件中 dispatch 的那个 Action。
/* 
	1.该文件是用于创建一个为reducer，reducer的本质就是一个函数
	2.reducer函数会接到两个参数，分别为：之前的状态(preState)，动作对象(action)
*/
const initState={ //初始的state，即redux中保存的公共数据
    articleList:[],
    cateList:[],
    cate:null,
    id:''
}
export default function Reducer(preState=initState, action) {

  const {type,data} = action;
  switch (type) {
      case 'setLists':{
        let {url,newArr} = data
        if(url==='article'){
          return{
            ...preState,
            articleList:newArr
          }
        }
        else if(url==='cate'){
          return{
            ...preState,
            cateList:newArr
          }
        }
      }
      break;
      default:
        return preState
  }
}
```

> 在组件中的使用
>
> 1.store.dispatch(getLists({url:'cate'}))              //触发对应的action
>
> 2.const cateName=store.getState().cateList  //获取保存的公共数据
>
> 3.store.subscribe(()=>{									  //订阅更新后的数据，触发视图响应
>
> this.setState({})
>
> })

> React-Redux
>
> 单纯的Redux只是一个状态机，是没有UI呈现的，react- redux作用是将Redux的状态机和React的UI呈现绑定在一起，当你dispatch action改变state的时候，会自动更新页面。
>
> React Redux 将组件区分为 容器组件 和 UI 组件
> 1.前者会处理逻辑
> 2.后者只负责显示和交互，内部不处理逻辑，状态完全由外部掌控

```js
//两个核心
//1.Provider
//一般我们都将顶层组件包裹在Provider组件之中，这样的话，所有组件就都可以在react-redux的控制之下了,但是store必须作为参数放到Provider组件中去

//index.js
ReactDOM.render(
  <Provider store={store}>
  <BrowserRouter>
    <App />
  </BrowserRouter>
  </Provider>,
  document.getElementById('root')
)

//2.connect
import {
  getLists
} from '../../../redux/actions'
import {connect} from 'react-redux'
export default connect(
    
  //mapStateToProps   //把Redux中的数据映射到React中的props中去             
  state =>({
    articleList:state.articleList
  }),


  // mapDispatchToProps //把各种dispatch也变成了props让你可以直接使用
  dispatch => ({
    getList:data=>dispatch(getLists(data)),
  })
)(Article)
```

### 1.Redux 和 Vuex 有什么区别，它们的共同思想

> **（1）Redux 和 Vuex区别**
>
> - Vuex改进了Redux中的Action和Reducer函数，以mutations变化函数取代Reducer，无需switch，只需在对应的mutation函数里改变state值即可
> - Vuex由于Vue自动重新渲染的特性，无需订阅重新渲染函数，只要生成新的State即可
> - Vuex数据流的顺序是∶View调用store.commit提交对应的请求到Store中对应的mutation函数->store改变（vue检测到数据变化自动渲染）
>
> 通俗点理解就是，vuex 弱化 dispatch，通过commit进行 store状态的一次更变；取消了action概念，不必传入特定的 action形式进行指定变更；弱化reducer，基于commit参数直接对数据进行转变，使得框架更加简易;
>
> **（2）共同思想**
>
> - 单—的数据源
> - 变化可以预测
>
> 本质上∶ redux与vuex都是对mvvm思想的服务，将数据从视图中抽离的一种方案。

### 2. 对 Redux 的理解，主要解决什么问题

> React是视图层框架。Redux是一个用来管理数据状态和UI状态的JavaScript应用工具。随着JavaScript单页应用（SPA）开发日趋复杂， JavaScript需要管理比任何时候都要多的state（状态）， Redux就是降低管理难度的。（Redux支持React、Angular、jQuery甚至纯JavaScript）。
>
> 在 React 中，UI 以组件的形式来搭建，组件之间可以嵌套组合。但 React 中组件间通信的数据流是单向的，顶层组件可以通过 props 属性向下层组件传递数据，而下层组件不能向上层组件传递数据，兄弟组件之间同样不能。这样简单的单向数据流支撑起了 React 中的数据可控性。
>
> 当项目越来越大的时候，管理数据的事件或回调函数将越来越多，也将越来越不好管理。管理不断变化的 state 非常困难。如果一个 model 的变化会引起另一个 model 变化，那么当 view 变化时，就可能引起对应 model 以及另一个 model 的变化，依次地，可能会引起另一个 view 的变化。直至你搞不清楚到底发生了什么。state 在什么时候，由于什么原因，如何变化已然不受控制。 当系统变得错综复杂的时候，想重现问题或者添加新功能就会变得举步维艰。如果这还不够糟糕，考虑一些来自前端开发领域的新需求，如更新调优、服务端渲染、路由跳转前请求数据等。state 的管理在大项目中相当复杂。
>
> Redux 提供了一个叫 store 的统一仓储库，组件通过 dispatch 将 state 直接传入store，不用通过其他的组件。并且组件通过 subscribe 从 store获取到 state 的改变。使用了 Redux，所有的组件都可以从 store 中获取到所需的 state，他们也能从store 获取到 state 的改变。这比组件之间互相传递数据清晰明朗的多。
>
> **主要解决的问题：** 单纯的Redux只是一个状态机，是没有UI呈现的，react- redux作用是将Redux的状态机和React的UI呈现绑定在一起，当你dispatch action改变state的时候，会自动更新页面。

### 3. Redux 原理及工作流程

> **（1）原理** Redux源码主要分为以下几个模块文件
>
> - compose.js 提供从右到左进行函数式编程
> - createStore.js 提供作为生成唯一store的函数
> - combineReducers.js 提供合并多个reducer的函数，保证store的唯一性
> - bindActionCreators.js 可以让开发者在不直接接触dispacth的前提下进行更改state的操作
> - applyMiddleware.js 这个方法通过中间件来增强dispatch的功能
>
> ```javascript
> const actionTypes = {
>     ADD: 'ADD',
>     CHANGEINFO: 'CHANGEINFO',
> }
> 
> const initState = {
>     info: '初始化',
> }
> 
> export default function initReducer(state=initState, action) {
>     switch(action.type) {
>         case actionTypes.CHANGEINFO:
>             return {
>                 ...state,
>                 info: action.preload.info || '',
>             }
>         default:
>             return { ...state };
>     }
> }
> 
> export default function createStore(reducer, initialState, middleFunc) {
> 
>     if (initialState && typeof initialState === 'function') {
>         middleFunc = initialState;
>         initialState = undefined;
>     }
> 
>     let currentState = initialState;
> 
>     const listeners = [];
> 
>     if (middleFunc && typeof middleFunc === 'function') {
>         // 封装dispatch 
>         return middleFunc(createStore)(reducer, initialState);
>     }
> 
>     const getState = () => {
>         return currentState;
>     }
> 
>     const dispatch = (action) => {
>         currentState = reducer(currentState, action);
> 
>         listeners.forEach(listener => {
>             listener();
>         })
>     }
> 
>     const subscribe = (listener) => {
>         listeners.push(listener);
>     }
> 
>     return {
>         getState,
>         dispatch,
>         subscribe
>     }
> }
> ```
>
> **（2）工作流程**
>
> - const store= createStore（fn）生成数据;
> - action: {type: Symble('action01), payload:'payload' }定义行为;
> - dispatch发起action：store.dispatch(doSomething('action001'));
> - reducer：处理action，返回新的state;
>
> 通俗点解释：
>
> - 首先，用户（通过View）发出Action，发出方式就用到了dispatch方法
> - 然后，Store自动调用Reducer，并且传入两个参数：当前State和收到的Action，Reducer会返回新的State
> - State—旦有变化，Store就会调用监听函数，来更新View
>
> 以 store 为核心，可以把它看成数据存储中心，但是他要更改数据的时候不能直接修改，数据修改更新的角色由Reducers来担任，store只做存储，中间人，当Reducers的更新完成以后会通过store的订阅来通知react component，组件把新的状态重新获取渲染，组件中也能主动发送action，创建action后这个动作是不会执行的，所以要dispatch这个action，让store通过reducers去做更新React Component 就是react的每个组件。

### 4. Redux 中异步的请求怎么处理

> 可以在 componentDidmount 中直接进⾏请求⽆须借助redux。但是在⼀定规模的项⽬中,上述⽅法很难进⾏异步流的管理,通常情况下我们会借助redux的异步中间件进⾏异步处理。redux异步流中间件其实有很多，当下主流的异步中间件有两种redux-thunk、redux-saga。
>
> **（1）使用react-thunk中间件**
>
> **redux-thunk**优点:
>
> - 体积⼩: redux-thunk的实现⽅式很简单,只有不到20⾏代码
> - 使⽤简单: redux-thunk没有引⼊像redux-saga或者redux-observable额外的范式,上⼿简单
>
> **redux-thunk**缺陷:
>
> - 样板代码过多: 与redux本身⼀样,通常⼀个请求需要⼤量的代码,⽽且很多都是重复性质的
> - 耦合严重: 异步操作与redux的action偶合在⼀起,不⽅便管理
> - 功能孱弱: 有⼀些实际开发中常⽤的功能需要⾃⼰进⾏封装
>
> 使用步骤：
>
> - 配置中间件，在store的创建中配置
>
> ```javascript
> import {createStore, applyMiddleware, compose} from 'redux';
> import reducer from './reducer';
> import thunk from 'redux-thunk'
> 
> // 设置调试工具
> const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ? window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}) : compose;
> // 设置中间件
> const enhancer = composeEnhancers(
>   applyMiddleware(thunk)
> );
> 
> const store = createStore(reducer, enhancer);
> 
> export default store;
> ```
>
> - 添加一个返回函数的actionCreator，将异步请求逻辑放在里面
>
> ```javascript
> /**
>   发送get请求，并生成相应action，更新store的函数
>   @param url {string} 请求地址
>   @param func {function} 真正需要生成的action对应的actionCreator
>   @return {function} 
> */
> // dispatch为自动接收的store.dispatch函数 
> export const getHttpAction = (url, func) => (dispatch) => {
>     axios.get(url).then(function(res){
>         const action = func(res.data)
>         dispatch(action)
>     })
> }
> ```
>
> - 生成action，并发送action
>
> ```javascript
> componentDidMount(){
>     var action = getHttpAction('/getData', getInitTodoItemAction)
>     // 发送函数类型的action时，该action的函数体会自动执行
>     store.dispatch(action)
> }
> ```
>
> **（2）使用redux-saga中间件**
>
> **redux-saga**优点:
>
> - 异步解耦: 异步操作被被转移到单独 saga.js 中，不再是掺杂在 action.js 或 component.js 中
> - action摆脱thunk function: dispatch 的参数依然是⼀个纯粹的 action (FSA)，⽽不是充满 “⿊魔法” thunk function
> - 异常处理: 受益于 generator function 的 saga 实现，代码异常/请求失败 都可以直接通过 try/catch 语法直接捕获处理
> - 功能强⼤: redux-saga提供了⼤量的Saga 辅助函数和Effect 创建器供开发者使⽤,开发者⽆须封装或者简单封装即可使⽤
> - 灵活: redux-saga可以将多个Saga可以串⾏/并⾏组合起来,形成⼀个⾮常实⽤的异步flow
> - 易测试，提供了各种case的测试⽅案，包括mock task，分⽀覆盖等等
>
> **redux-saga**缺陷:
>
> - 额外的学习成本: redux-saga不仅在使⽤难以理解的 generator function,⽽且有数⼗个API,学习成本远超redux-thunk,最重要的是你的额外学习成本是只服务于这个库的,与redux-observable不同,redux-observable虽然也有额外学习成本但是背后是rxjs和⼀整套思想
> - 体积庞⼤: 体积略⼤,代码近2000⾏，min版25KB左右
> - 功能过剩: 实际上并发控制等功能很难⽤到,但是我们依然需要引⼊这些代码
> - ts⽀持不友好: yield⽆法返回TS类型
>
> redux-saga可以捕获action，然后执行一个函数，那么可以把异步代码放在这个函数中，使用步骤如下：
>
> - 配置中间件
>
> ```javascript
> import {createStore, applyMiddleware, compose} from 'redux';
> import reducer from './reducer';
> import createSagaMiddleware from 'redux-saga'
> import TodoListSaga from './sagas'
> 
> const composeEnhancers = window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__ ? window.__REDUX_DEVTOOLS_EXTENSION_COMPOSE__({}) : compose;
> const sagaMiddleware = createSagaMiddleware()
> 
> const enhancer = composeEnhancers(
>   applyMiddleware(sagaMiddleware)
> );
> 
> const store = createStore(reducer, enhancer);
> sagaMiddleware.run(TodoListSaga)
> 
> export default store;
> ```
>
> - 将异步请求放在sagas.js中
>
> ```javascript
> import {takeEvery, put} from 'redux-saga/effects'
> import {initTodoList} from './actionCreator'
> import {GET_INIT_ITEM} from './actionTypes'
> import axios from 'axios'
> 
> function* func(){
>     try{
>         // 可以获取异步返回数据
>         const res = yield axios.get('/getData')
>         const action = initTodoList(res.data)
>         // 将action发送到reducer
>         yield put(action)
>     }catch(e){
>         console.log('网络请求失败')
>     }
> }
> 
> function* mySaga(){
>     // 自动捕获GET_INIT_ITEM类型的action，并执行func
>     yield takeEvery(GET_INIT_ITEM, func)
> }
> 
> export default mySaga
> ```
>
> - 发送action
>
> ```javascript
> componentDidMount(){
>   const action = getInitTodoItemAction()
>   store.dispatch(action)
> }
> ```

### 5. Redux 怎么实现属性传递，介绍下原理

> react-redux 数据传输∶ view-->action-->reducer-->store-->view。看下点击事件的数据是如何通过redux传到view上：
>
> - view 上的AddClick 事件通过mapDispatchToProps 把数据传到action ---> click:()=>dispatch(ADD)
> - action 的ADD 传到reducer上
> - reducer传到store上 const store = createStore(reducer);
> - store再通过 mapStateToProps 映射穿到view上text:State.text
>
> 代码示例∶
>
> ```javascript
> import React from 'react';
> import ReactDOM from 'react-dom';
> import { createStore } from 'redux';
> import { Provider, connect } from 'react-redux';
> class App extends React.Component{
>     render(){
>         let { text, click, clickR } = this.props;
>         return(
>             <div>
>                 <div>数据:已有人{text}</div>
>                 <div onClick={click}>加人</div>
>                 <div onClick={clickR}>减人</div>
>             </div>
>         )
>     }
> }
> const initialState = {
>     text:5
> }
> const reducer = function(state,action){
>     switch(action.type){
>         case 'ADD':
>             return {text:state.text+1}
>         case 'REMOVE':
>             return {text:state.text-1}
>         default:
>             return initialState;
>     }
> }
> 
> let ADD = {
>     type:'ADD'
> }
> let Remove = {
>     type:'REMOVE'
> }
> 
> const store = createStore(reducer);
> 
> let mapStateToProps = function (state){
>     return{
>         text:state.text
>     }
> }
> 
> let mapDispatchToProps = function(dispatch){
>     return{
>         click:()=>dispatch(ADD),
>         clickR:()=>dispatch(Remove)
>     }
> }
> 
> const App1 = connect(mapStateToProps,mapDispatchToProps)(App);
> 
> ReactDOM.render(
>     <Provider store = {store}>
>         <App1></App1>
>     </Provider>,document.getElementById('root')
> )
> ```

### 6. Redux 中间件是什么？接受几个参数？柯里化函数两端的参数具体是什么？

> Redux 的中间件提供的是位于 action 被发起之后，到达 reducer 之前的扩展点，换而言之，原本 view -→> action -> reducer -> store 的数据流加上中间件后变成了 view -> action -> middleware -> reducer -> store ，在这一环节可以做一些"副作用"的操作，如异步请求、打印日志等。
>
> applyMiddleware源码：
>
> ```javascript
> export default function applyMiddleware(...middlewares) {
>     return createStore => (...args) => {
>         // 利用传入的createStore和reducer和创建一个store
>         const store = createStore(...args)
>         let dispatch = () => {
>             throw new Error()
>         }
>         const middlewareAPI = {
>             getState: store.getState,
>             dispatch: (...args) => dispatch(...args)
>         }
>         // 让每个 middleware 带着 middlewareAPI 这个参数分别执行一遍
>         const chain = middlewares.map(middleware => middleware(middlewareAPI))
>         // 接着 compose 将 chain 中的所有匿名函数，组装成一个新的函数，即新的 dispatch
>         dispatch = compose(...chain)(store.dispatch)
>         return {
>             ...store,
>             dispatch
>         }
>     }
> }
> ```
>
> 从applyMiddleware中可以看出∶
>
> - redux中间件接受一个对象作为参数，对象的参数上有两个字段 dispatch 和 getState，分别代表着 Redux Store 上的两个同名函数。
> - 柯里化函数两端一个是 middewares，一个是store.dispatch

### 7. Redux 请求中间件如何处理并发

> **使用redux-Saga** redux-saga是一个管理redux应用异步操作的中间件，用于代替 redux-thunk 的。它通过创建 Sagas 将所有异步操作逻辑存放在一个地方进行集中处理，以此将react中的同步操作与异步操作区分开来，以便于后期的管理与维护。 redux-saga如何处理并发：
>
> - **takeEvery**
>
> 可以让多个 saga 任务并行被 fork 执行。
>
> ```javascript
> import {
>     fork,
>     take
> } from "redux-saga/effects"
> 
> const takeEvery = (pattern, saga, ...args) => fork(function*() {
>     while (true) {
>         const action = yield take(pattern)
>         yield fork(saga, ...args.concat(action))
>     }
> })
> ```
>
> - **takeLatest**
>
> takeLatest 不允许多个 saga 任务并行地执行。一旦接收到新的发起的 action，它就会取消前面所有 fork 过的任务（如果这些任务还在执行的话）。 在处理 AJAX 请求的时候，如果只希望获取最后那个请求的响应， takeLatest 就会非常有用。
>
> ```javascript
> import {
>     cancel,
>     fork,
>     take
> } from "redux-saga/effects"
> 
> const takeLatest = (pattern, saga, ...args) => fork(function*() {
>     let lastTask
>     while (true) {
>         const action = yield take(pattern)
>         if (lastTask) {
>             yield cancel(lastTask) // 如果任务已经结束，则 cancel 为空操作
>         }
>         lastTask = yield fork(saga, ...args.concat(action))
>     }
> })
> ```

### 8. Redux 状态管理器和变量挂载到 window 中有什么区别

> 两者都是存储数据以供后期使用。但是Redux状态更改可回溯——Time travel，数据多了的时候可以很清晰的知道改动在哪里发生，完整的提供了一套状态管理模式。
>
> 随着 JavaScript 单页应用开发日趋复杂，JavaScript 需要管理比任何时候都要多的 state （状态）。 这些 state 可能包括服务器响应、缓存数据、本地生成尚未持久化到服务器的数据，也包括 UI状态，如激活的路由，被选中的标签，是否显示加载动效或者分页器等等。
>
> 管理不断变化的 state 非常困难。如果一个 model 的变化会引起另一个 model 变化，那么当 view 变化时，就可能引起对应 model 以及另一个model 的变化，依次地，可能会引起另一个 view 的变化。直至你搞不清楚到底发生了什么。state 在什么时候，由于什么原因，如何变化已然不受控制。 当系统变得错综复杂的时候，想重现问题或者添加新功能就会变得举步维艰。 如果这还不够糟糕，考虑一些来自前端开发领域的新需求，如更新调优、服务端渲染、路由跳转前请求数据等等。前端开发者正在经受前所未有的复杂性，难道就这么放弃了吗?当然不是。
>
> 这里的复杂性很大程度上来自于：我们总是将两个难以理清的概念混淆在一起：变化和异步。 可以称它们为曼妥思和可乐。如果把二者分开，能做的很好，但混到一起，就变得一团糟。一些库如 React 视图在视图层禁止异步和直接操作 DOM来解决这个问题。美中不足的是，React 依旧把处理 state 中数据的问题留给了你。Redux就是为了帮你解决这个问题。

### 9. mobox 和 redux 有什么区别？

> **（1）共同点**
>
> - 为了解决状态管理混乱，无法有效同步的问题统一维护管理应用状态;
> - 某一状态只有一个可信数据来源（通常命名为store，指状态容器）;
> - 操作更新状态方式统一，并且可控（通常以action方式提供更新状态的途径）;
> - 支持将store与React组件连接，如react-redux，mobx- react;
>
> **（2）区别** Redux更多的是遵循Flux模式的一种实现，是一个 JavaScript库，它关注点主要是以下几方面∶
>
> - Action∶ 一个JavaScript对象，描述动作相关信息，主要包含type属性和payload属性∶
>
>   ```
>   o type∶ action 类型; o payload∶ 负载数据;
>   ```
>
> - Reducer∶ 定义应用状态如何响应不同动作（action），如何更新状态;
>
> - Store∶ 管理action和reducer及其关系的对象，主要提供以下功能∶
>
>   ```
>   o 维护应用状态并支持访问状态(getState());
>   o 支持监听action的分发，更新状态(dispatch(action)); 
>   o 支持订阅store的变更(subscribe(listener));
>   ```
>
> - 异步流∶ 由于Redux所有对store状态的变更，都应该通过action触发，异步任务（通常都是业务或获取数据任务）也不例外，而为了不将业务或数据相关的任务混入React组件中，就需要使用其他框架配合管理异步任务流程，如redux-thunk，redux-saga等;
>
> Mobx是一个透明函数响应式编程的状态管理库，它使得状态管理简单可伸缩∶
>
> - Action∶定义改变状态的动作函数，包括如何变更状态;
> - Store∶ 集中管理模块状态（State）和动作(action)
> - Derivation（衍生）∶ 从应用状态中派生而出，且没有任何其他影响的数据
>
> **对比总结：**
>
> - redux将数据保存在单一的store中，mobx将数据保存在分散的多个store中
> - redux使用plain object保存数据，需要手动处理变化后的操作;mobx适用observable保存数据，数据变化后自动处理响应的操作
> - redux使用不可变状态，这意味着状态是只读的，不能直接去修改它，而是应该返回一个新的状态，同时使用纯函数;mobx中的状态是可变的，可以直接对其进行修改
> - mobx相对来说比较简单，在其中有很多的抽象，mobx更多的使用面向对象的编程思维;redux会比较复杂，因为其中的函数式编程思想掌握起来不是那么容易，同时需要借助一系列的中间件来处理异步和副作用
> - mobx中有更多的抽象和封装，调试会比较困难，同时结果也难以预测;而redux提供能够进行时间回溯的开发工具，同时其纯函数以及更少的抽象，让调试变得更加的容易

### 10. Redux 中间件是怎么拿到store 和 action? 然后怎么处理?

> redux中间件本质就是一个函数柯里化。redux applyMiddleware Api 源码中每个middleware 接受2个参数， Store 的getState 函数和dispatch 函数，分别获得store和action，最终返回一个函数。该函数会被传入 next 的下一个 middleware 的 dispatch 方法，并返回一个接收 action 的新函数，这个函数可以直接调用 next（action），或者在其他需要的时刻调用，甚至根本不去调用它。调用链中最后一个 middleware 会接受真实的 store的 dispatch 方法作为 next 参数，并借此结束调用链。所以，middleware 的函数签名是（{ getState，dispatch })=> next => action。

### 11. Redux中的connect有什么作用

> connect负责连接React和Redux
>
> **（1）获取state**
>
> connect 通过 context获取 Provider 中的 store，通过` store.getState()` 获取整个store tree 上所有state
>
> **（2）包装原组件**
>
> 将state和action通过props的方式传入到原组件内部 wrapWithConnect 返回—个 ReactComponent 对 象 Connect，Connect 重 新 render 外部传入的原组件 WrappedComponent ，并把 connect 中传入的 mapStateToProps，mapDispatchToProps与组件上原有的 props合并后，通过属性的方式传给WrappedComponent
>
> **（3）监听store tree变化**
>
> connect缓存了store tree中state的状态，通过当前state状态 和变更前 state 状态进行比较，从而确定是否调用 `this.setState()`方法触发Connect及其子组件的重新渲染

## 七.虚拟DOM

### 1. 对虚拟 DOM 的理解？虚拟 DOM 主要做了什么？虚拟 DOM 本身是什么？

> 从本质上来说，Virtual Dom是一个JavaScript对象，通过对象的方式来表示DOM结构。将页面的状态抽象为JS对象的形式，配合不同的渲染工具，使跨平台渲染成为可能。通过事务处理机制，将多次DOM修改的结果一次性的更新到页面上，从而有效的减少页面渲染的次数，减少修改DOM的重绘重排次数，提高渲染性能。
>
> 虚拟DOM是对DOM的抽象，这个对象是更加轻量级的对DOM的描述。它设计的最初目的，就是更好的跨平台，比如node.js就没有DOM，如果想实现SSR，那么一个方式就是借助虚拟dom，因为虚拟dom本身是js对象。 在代码渲染到页面之前，vue或者react会把代码转换成一个对象（虚拟DOM）。以对象的形式来描述真实dom结构，最终渲染到页面。在每次数据发生变化前，虚拟dom都会缓存一份，变化之时，现在的虚拟dom会与缓存的虚拟dom进行比较。在vue或者react内部封装了diff算法，通过这个算法来进行比较，渲染时修改改变的变化，原先没有发生改变的通过原先的数据进行渲染。
>
> 另外现代前端框架的一个基本要求就是无须手动操作DOM，一方面是因为手动操作DOM无法保证程序性能，多人协作的项目中如果review不严格，可能会有开发者写出性能较低的代码，另一方面更重要的是省略手动DOM操作可以大大提高开发效率。
>
> **为什么要用 Virtual DOM：**
>
> **（1）保证性能下限，在不进行手动优化的情况下，提供过得去的性能**
>
> 下面对比一下修改DOM时真实DOM操作和Virtual DOM的过程，来看一下它们重排重绘的性能消耗∶
>
> - 真实DOM∶ 生成HTML字符串＋ 重建所有的DOM元素
> - Virtual DOM∶ 生成vNode＋ DOMDiff＋必要的DOM更新
>
> Virtual DOM的更新DOM的准备工作耗费更多的时间，也就是JS层面，相比于更多的DOM操作它的消费是极其便宜的。尤雨溪在社区论坛中说道∶ 框架给你的保证是，你不需要手动优化的情况下，我依然可以给你提供过得去的性能。 **（2）跨平台** Virtual DOM本质上是JavaScript的对象，它可以很方便的跨平台操作，比如服务端渲染、uniapp等。

### 2. React diff 算法的原理是什么？

> 实际上，diff 算法探讨的就是虚拟 DOM 树发生变化后，生成 DOM 树更新补丁的方式。它通过对比新旧两株虚拟 DOM 树的变更差异，将更新补丁作用于真实 DOM，以最小成本完成视图更新。 ![CgqCHl_qyouAAkb9AAB_cmWuZhc920_mh1609406106571.jpg](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/e44d4ebb48a74ffda63754428c9d5273~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp) 具体的流程如下：
>
> - 真实的 DOM 首先会映射为虚拟 DOM；
> - 当虚拟 DOM 发生变化后，就会根据差距计算生成 patch，这个 patch 是一个结构化的数据，内容包含了增加、更新、移除等；
> - 根据 patch 去更新真实的 DOM，反馈到用户的界面上。
>
> ![CgqCHl_qypGAZPuGAADYrK9nkJY878_mh1609406162857.jpg](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/246e03a02e3e48ff941f921843bd8676~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp) 一个简单的例子：
>
> ```javascript
> import React from 'react'
> export default class ExampleComponent extends React.Component {
>   render() {
>     if(this.props.isVisible) {
>        return <div className="visible">visbile</div>;
>     }
>      return <div className="hidden">hidden</div>;
>   }
> }
> ```
>
> 这里，首先假定 ExampleComponent 可见，然后再改变它的状态，让它不可见 。映射为真实的 DOM 操作是这样的，React 会创建一个 div 节点。
>
> ```javascript
> <div class="visible">visbile</div>
> ```
>
> 当把 visbile 的值变为 false 时，就会替换 class 属性为 hidden，并重写内部的 innerText 为 hidden。**这样一个生成补丁、更新差异的过程统称为 diff 算法。**
>
> diff算法可以总结为三个策略，分别从树、组件及元素三个层面进行复杂度的优化：
>
> **策略一：忽略节点跨层级操作场景，提升比对效率。（基于树进行对比）**
>
> 这一策略需要进行树比对，即对树进行分层比较。树比对的处理手法是非常“暴力”的，即两棵树只对同一层次的节点进行比较，如果发现节点已经不存在了，则该节点及其子节点会被完全删除掉，不会用于进一步的比较，这就提升了比对效率。
>
> **策略二：如果组件的 class 一致，则默认为相似的树结构，否则默认为不同的树结构。（基于组件进行对比）**
>
> 在组件比对的过程中：
>
> - 如果组件是同一类型则进行树比对；
> - 如果不是则直接放入补丁中。
>
> 只要父组件类型不同，就会被重新渲染。这也就是为什么 shouldComponentUpdate、PureComponent 及 React.memo 可以提高性能的原因。
>
> **策略三：同一层级的子节点，可以通过标记 key 的方式进行列表对比。（基于节点进行对比）**
>
> 元素比对主要发生在同层级中，通过标记节点操作生成补丁。节点操作包含了插入、移动、删除等。其中节点重新排序同时涉及插入、移动、删除三个操作，所以效率消耗最大，此时策略三起到了至关重要的作用。通过标记 key 的方式，React 可以直接移动 DOM 节点，降低内耗。

### 3. React key 是干嘛用的 为什么要加？key 主要是解决哪一类问题的

> Keys 是 React 用于追踪哪些列表中元素被修改、被添加或者被移除的辅助标识。在开发过程中，我们需要保证某个元素的 key 在其同级元素中具有唯一性。
>
> 在 React Diff 算法中 React 会借助元素的 Key 值来判断该元素是新近创建的还是被移动而来的元素，从而减少不必要的元素重渲染此外，React 还需要借助 Key 值来判断元素与本地状态的关联关系。
>
> 注意事项：
>
> - key值一定要和具体的元素—一对应；
> - 尽量不要用数组的index去作为key；
> - 不要在render的时候用随机数或者其他操作给元素加上不稳定的key，这样造成的性能开销比不加key的情况下更糟糕。

### 4. 虚拟 DOM 的引入与直接操作原生 DOM 相比，哪一个效率更高，为什么

> 虚拟DOM相对原生的DOM不一定是效率更高，如果只修改一个按钮的文案，那么虚拟 DOM 的操作无论如何都不可能比真实的 DOM 操作更快。在首次渲染大量DOM时，由于多了一层虚拟DOM的计算，虚拟DOM也会比innerHTML插入慢。它能保证性能下限，在真实DOM操作的时候进行针对性的优化时，还是更快的。所以要根据具体的场景进行探讨。
>
> 在整个 DOM 操作的演化过程中，其实主要矛盾并不在于性能，而在于开发者写得爽不爽，在于研发体验/研发效率。虚拟 DOM 不是别的，正是前端开发们为了追求更好的研发体验和研发效率而创造出来的高阶产物。虚拟 DOM 并不一定会带来更好的性能，React 官方也从来没有把虚拟 DOM 作为性能层面的卖点对外输出过。**虚拟 DOM 的优越之处在于，它能够在提供更爽、更高效的研发模式（也就是函数式的 UI 编程方式）的同时，仍然保持一个还不错的性能。**

### 5. React 与 Vue 的 diff 算法有何不同？

> diff 算法是指生成更新补丁的方式，主要应用于虚拟 DOM 树变化后，更新真实 DOM。所以 diff 算法一定存在这样一个过程：触发更新 → 生成补丁 → 应用补丁。
>
> React 的 diff 算法，触发更新的时机主要在 state 变化与 hooks 调用之后。此时触发虚拟 DOM 树变更遍历，采用了深度优先遍历算法。但传统的遍历方式，效率较低。为了优化效率，使用了分治的方式。将单一节点比对转化为了 3 种类型节点的比对，分别是树、组件及元素，以此提升效率。
>
> - 树比对：由于网页视图中较少有跨层级节点移动，两株虚拟 DOM 树只对同一层次的节点进行比较。
> - 组件比对：如果组件是同一类型，则进行树比对，如果不是，则直接放入到补丁中。
> - 元素比对：主要发生在同层级中，通过标记节点操作生成补丁，节点操作对应真实的 DOM 剪裁操作。
>
> 以上是经典的 React diff 算法内容。自 React 16 起，引入了 Fiber 架构。为了使整个更新过程可随时暂停恢复，节点与树分别采用了 FiberNode 与 FiberTree 进行重构。fiberNode 使用了双链表的结构，可以直接找到兄弟节点与子节点。整个更新过程由 current 与 workInProgress 两株树双缓冲完成。workInProgress 更新完成后，再通过修改 current 相关指针指向新节点。
>
> Vue 的整体 diff 策略与 React 对齐，虽然缺乏时间切片能力，但这并不意味着 Vue 的性能更差，因为在 Vue 3 初期引入过，后期因为收益不高移除掉了。除了高帧率动画，在 Vue 中其他的场景几乎都可以使用防抖和节流去提高响应性能。

## 八、Hooks

### 1. 对 React Hook 的理解，它的实现原理是什么

> React-Hooks 是 React 团队在 React 组件开发实践中，逐渐认知到的一个改进点，这背后其实涉及对**类组件**和**函数组件**两种组件形式的思考和侧重。
>
> **（1）类组件：** 所谓类组件，就是基于 ES6 Class 这种写法，通过继承 React.Component 得来的 React 组件。以下是一个类组件：
>
> ```javascript
> class DemoClass extends React.Component {
>   state = {
>     text: ""
>   };
>   componentDidMount() {
>     //...
>   }
>   changeText = (newText) => {
>     this.setState({
>       text: newText
>     });
>   };
> 
>   render() {
>     return (
>       <div className="demoClass">
>         <p>{this.state.text}</p>
>         <button onClick={this.changeText}>修改</button>
>       </div>
>     );
>   }
> }
> ```
>
> 可以看出，React 类组件内部预置了相当多的“现成的东西”等着我们去调度/定制，state 和生命周期就是这些“现成东西”中的典型。要想得到这些东西，难度也不大，只需要继承一个 React.Component 即可。
>
> 当然，这也是类组件的一个不便，它太繁杂了，对于解决许多问题来说，编写一个类组件实在是一个过于复杂的姿势。复杂的姿势必然带来高昂的理解成本，这也是我们所不想看到的。除此之外，由于开发者编写的逻辑在封装后是和组件粘在一起的，这就使得**类组件内部的逻辑难以实现拆分和复用。**
>
> **（2）函数组件**：函数组件就是以函数的形态存在的 React 组件。早期并没有 React-Hooks，函数组件内部无法定义和维护 state，因此它还有一个别名叫“无状态组件”。以下是一个函数组件：
>
> ```javascript
> function DemoFunction(props) {
>   const { text } = props
>   return (
>     <div className="demoFunction">
>       <p>{`函数组件接收的内容：[${text}]`}</p>
>     </div>
>   );
> }
> ```
>
> 相比于类组件，函数组件肉眼可见的特质自然包括轻量、灵活、易于组织和维护、较低的学习成本等。
>
> 通过对比，从形态上可以对两种组件做区分，它们之间的区别如下：
>
> - 类组件需要继承 class，函数组件不需要；
> - 类组件可以访问生命周期方法，函数组件不能；
> - 类组件中可以获取到实例化后的 this，并基于这个 this 做各种各样的事情，而函数组件不可以；
> - 类组件中可以定义并维护 state（状态），而函数组件不可以；
>
> 除此之外，还有一些其他的不同。通过上面的区别，我们不能说谁好谁坏，它们各有自己的优势。在 React-Hooks 出现之前，**类组件的能力边界明显强于函数组件。**
>
> 实际上，类组件和函数组件之间，是面向对象和函数式编程这两套不同的设计思想之间的差异。而函数组件更加契合 React 框架的设计理念： <img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/84fdd6a1619e497c9c0096a960ba4629~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp" alt="image.png" style="zoom:50%;" /> React 组件本身的定位就是函数，一个输入数据、输出 UI 的函数。作为开发者，我们编写的是声明式的代码，而 React 框架的主要工作，就是及时地把声明式的代码转换为命令式的 DOM 操作，把数据层面的描述映射到用户可见的 UI 变化中去。这就意味着从原则上来讲，React 的数据应该总是紧紧地和渲染绑定在一起的，而类组件做不到这一点。**函数组件就真正地将数据和渲染绑定到了一起。函数组件是一个更加匹配其设计理念、也更有利于逻辑拆分与重用的组件表达形式。**
>
> 为了能让开发者更好的的去编写函数式组件。于是，React-Hooks 便应运而生。
>
> React-Hooks 是一套能够使函数组件更强大、更灵活的“钩子”。
>
> 函数组件比起类组件少了很多东西，比如**生命周期、对 state 的管理**等。这就给函数组件的使用带来了非常多的局限性，导致我们并不能使用函数这种形式，写出一个真正的全功能的组件。而React-Hooks 的出现，就是为了帮助函数组件补齐这些（相对于类组件来说）缺失的能力。
>
> 如果说函数组件是一台轻巧的快艇，那么 React-Hooks 就是一个内容丰富的零部件箱。“重装战舰”所预置的那些设备，这个箱子里基本全都有，同时它还不强制你全都要，而是允许你自由地选择和使用你需要的那些能力，然后将这些能力以 Hook（钩子）的形式“钩”进你的组件里，从而定制出一个最适合你的“专属战舰”。

### 2. 为什么 useState 要使用数组而不是对象

> useState 的用法：
>
> ```javascript
> const [count, setCount] = useState(0)
> ```
>
> 可以看到 useState 返回的是一个数组，那么为什么是返回数组而不是返回对象呢？
>
> 这里用到了解构赋值，所以先来看一下ES6 的解构赋值：
>
> ##### 数组的解构赋值
>
> ```javascript
> const foo = [1, 2, 3];
> const [one, two, three] = foo;
> console.log(one);	// 1
> console.log(two);	// 2
> console.log(three);	// 3
> ```
>
> ##### 对象的解构赋值
>
> ```javascript
> const user = {
>   id: 888,
>   name: "xiaoxin"
> };
> const { id, name } = user;
> console.log(id);	// 888
> console.log(name);	// "xiaoxin"
> ```
>
> 看完这两个例子，答案应该就出来了：
>
> - 如果 useState 返回的是数组，那么使用者可以对数组中的元素命名，代码看起来也比较干净
> - 如果 useState 返回的是对象，在解构对象的时候必须要和 useState 内部实现返回的对象同名，想要使用多次的话，必须得设置别名才能使用返回值
>
> 下面来看看如果 useState 返回对象的情况：
>
> ```javascript
> // 第一次使用
> const { state, setState } = useState(false);
> // 第二次使用
> const { state: counter, setState: setCounter } = useState(0) 
> ```
>
> 这里可以看到，返回对象的使用方式还是挺麻烦的，更何况实际项目中会使用的更频繁。 **总结：\**useState 返回的是 array 而不是 object 的原因就是为了\**降低使用的复杂度**，返回数组的话可以直接根据顺序解构，而返回对象的话要想使用多次就需要定义别名了。

### 3. React Hooks 解决了哪些问题？

> React Hooks 主要解决了以下问题：
>
> **（1）在组件之间复用状态逻辑很难**
>
> React 没有提供将可复用性行为“附加”到组件的途径（例如，把组件连接到 store）解决此类问题可以使用 render props 和 高阶组件。但是这类方案需要重新组织组件结构，这可能会很麻烦，并且会使代码难以理解。由 providers，consumers，高阶组件，render props 等其他抽象层组成的组件会形成“嵌套地狱”。尽管可以在 DevTools 过滤掉它们，但这说明了一个更深层次的问题：React 需要为共享状态逻辑提供更好的原生途径。
>
> 可以使用 Hook 从组件中提取状态逻辑，使得这些逻辑可以单独测试并复用。Hook 使我们在无需修改组件结构的情况下复用状态逻辑。 这使得在组件间或社区内共享 Hook 变得更便捷。
>
> **（2）复杂组件变得难以理解**
>
> 在组件中，每个生命周期常常包含一些不相关的逻辑。例如，组件常常在 componentDidMount 和 componentDidUpdate 中获取数据。但是，同一个 componentDidMount 中可能也包含很多其它的逻辑，如设置事件监听，而之后需在 componentWillUnmount 中清除。相互关联且需要对照修改的代码被进行了拆分，而完全不相关的代码却在同一个方法中组合在一起。如此很容易产生 bug，并且导致逻辑不一致。
>
> 在多数情况下，不可能将组件拆分为更小的粒度，因为状态逻辑无处不在。这也给测试带来了一定挑战。同时，这也是很多人将 React 与状态管理库结合使用的原因之一。但是，这往往会引入了很多抽象概念，需要你在不同的文件之间来回切换，使得复用变得更加困难。
>
> 为了解决这个问题，Hook 将组件中相互关联的部分拆分成更小的函数（比如设置订阅或请求数据），而并非强制按照生命周期划分。你还可以使用 reducer 来管理组件的内部状态，使其更加可预测。
>
> **（3）难以理解的 class**
>
> 除了代码复用和代码管理会遇到困难外，class 是学习 React 的一大屏障。我们必须去理解 JavaScript 中 this 的工作方式，这与其他语言存在巨大差异。还不能忘记绑定事件处理器。没有稳定的语法提案，这些代码非常冗余。大家可以很好地理解 props，state 和自顶向下的数据流，但对 class 却一筹莫展。即便在有经验的 React 开发者之间，对于函数组件与 class 组件的差异也存在分歧，甚至还要区分两种组件的使用场景。
>
> 为了解决这些问题，Hook 使你在非 class 的情况下可以使用更多的 React 特性。 从概念上讲，React 组件一直更像是函数。而 Hook 则拥抱了函数，同时也没有牺牲 React 的精神原则。Hook 提供了问题的解决方案，无需学习复杂的函数式或响应式编程技术

### 4. React Hook 的使用限制有哪些？

> React Hooks 的限制主要有两条：
>
> - 不要在循环、条件或嵌套函数中调用 Hook；
> - 在 React 的函数组件中调用 Hook。
>
> 那为什么会有这样的限制呢？Hooks 的设计初衷是为了改进 React 组件的开发模式。在旧有的开发模式下遇到了三个问题。
>
> - 组件之间难以复用状态逻辑。过去常见的解决方案是高阶组件、render props 及状态管理框架。
> - 复杂的组件变得难以理解。生命周期函数与业务逻辑耦合太深，导致关联部分难以拆分。
> - 人和机器都很容易混淆类。常见的有 this 的问题，但在 React 团队中还有类难以优化的问题，希望在编译优化层面做出一些改进。
>
> 这三个问题在一定程度上阻碍了 React 的后续发展，所以为了解决这三个问题，Hooks **基于函数组件**开始设计。然而第三个问题决定了 Hooks 只支持函数组件。
>
> 那为什么不要在循环、条件或嵌套函数中调用 Hook 呢？因为 Hooks 的设计是基于数组实现。在调用时按顺序加入数组中，如果使用循环、条件或嵌套函数很有可能导致数组取值错位，执行错误的 Hook。当然，实质上 React 的源码里不是数组，是链表。
>
> 这些限制会在编码上造成一定程度的心智负担，新手可能会写错，为了避免这样的情况，可以引入 ESLint 的 Hooks 检查插件进行预防。

### 5. useEffect 与 useLayoutEffect 的区别

> **（1）共同点**
>
> - **运用效果：** useEffect 与 useLayoutEffect 两者都是用于处理副作用，这些副作用包括改变 DOM、设置订阅、操作定时器等。在函数组件内部操作副作用是不被允许的，所以需要使用这两个函数去处理。
> - **使用方式：** useEffect 与 useLayoutEffect 两者底层的函数签名是完全一致的，都是调用的 mountEffectImpl方法，在使用上也没什么差异，基本可以直接替换。
>
> **（2）不同点**
>
> - **使用场景：** useEffect 在 React 的渲染过程中是被异步调用的，用于绝大多数场景；而 useLayoutEffect 会在所有的 DOM 变更之后同步调用，主要用于处理 DOM 操作、调整样式、避免页面闪烁等问题。也正因为是同步处理，所以需要避免在 useLayoutEffect 做计算量较大的耗时任务从而造成阻塞。
> - **使用效果：** useEffect是按照顺序执行代码的，改变屏幕像素之后执行（先渲染，后改变DOM），当改变屏幕内容时可能会产生闪烁；useLayoutEffect是改变屏幕像素之前就执行了（会推迟页面显示的事件，先改变DOM后渲染），不会产生闪烁。**useLayoutEffect总是比useEffect先执行。**
>
> 在未来的趋势上，两个 API 是会长期共存的，暂时没有删减合并的计划，需要开发者根据场景去自行选择。React 团队的建议非常实用，如果实在分不清，先用 useEffect，一般问题不大；如果页面有异常，再直接替换为 useLayoutEffect 即可。

### 6. React Hooks在平时开发中需要注意的问题和原因

> （1）**不要在循环，条件或嵌套函数中调用Hook，必须始终在 React函数的顶层使用Hook**
>
> 这是因为React需要利用调用顺序来正确更新相应的状态，以及调用相应的钩子函数。一旦在循环或条件分支语句中调用Hook，就容易导致调用顺序的不一致性，从而产生难以预料到的后果。
>
> **（2）使用useState时候，使用push，pop，splice等直接更改数组对象的坑**
>
> 使用push直接更改数组无法获取到新值，应该采用析构方式，但是在class里面不会有这个问题。代码示例：
>
> ```javascript
> function Indicatorfilter() {
>   let [num,setNums] = useState([0,1,2,3])
>   const test = () => {
>     // 这里坑是直接采用push去更新num
>     // setNums(num)是无法更新num的
>     // 必须使用num = [...num ,1]
>     num.push(1)
>     // num = [...num ,1]
>     setNums(num)
>   }
> return (
>     <div className='filter'>
>       <div onClick={test}>测试</div>
>         <div>
>           {num.map((item,index) => (
>               <div key={index}>{item}</div>
>           ))}
>       </div>
>     </div>
>   )
> }
> 
> class Indicatorfilter extends React.Component<any,any>{
>   constructor(props:any){
>       super(props)
>       this.state = {
>           nums:[1,2,3]
>       }
>       this.test = this.test.bind(this)
>   }
> 
>   test(){
>       // class采用同样的方式是没有问题的
>       this.state.nums.push(1)
>       this.setState({
>           nums: this.state.nums
>       })
>   }
> 
>   render(){
>       let {nums} = this.state
>       return(
>           <div>
>               <div onClick={this.test}>测试</div>
>                   <div>
>                       {nums.map((item:any,index:number) => (
>                           <div key={index}>{item}</div>
>                       ))}
>                   </div>
>           </div>
> 
>       )
>   }
> }
> ```
>
> （3）**useState设置状态的时候，只有第一次生效，后期需要更新状态，必须通过useEffect**
>
> TableDeail是一个公共组件，在调用它的父组件里面，我们通过set改变columns的值，以为传递给TableDeail 的 columns是最新的值，所以tabColumn每次也是最新的值，但是实际tabColumn是最开始的值，不会随着columns的更新而更新：
>
> ```javascript
> const TableDeail = ({
>     columns,
> }:TableData) => {
>     const [tabColumn, setTabColumn] = useState(columns) 
> }
> 
> // 正确的做法是通过useEffect改变这个值
> const TableDeail = ({
>     columns,
> }:TableData) => {
>     const [tabColumn, setTabColumn] = useState(columns) 
>     useEffect(() =>{setTabColumn(columns)},[columns])
> }
> ```
>
> **（4）善用useCallback**
>
> 父组件传递给子组件事件句柄时，如果我们没有任何参数变动可能会选用useMemo。但是每一次父组件渲染子组件即使没变化也会跟着渲染一次。
>
> **（5）不要滥用useContext**
>
> 可以使用基于 useContext 封装的状态管理工具。

### 7. React Hooks 和生命周期的关系？

> **函数组件** 的本质是函数，没有 state 的概念的，因此**不存在生命周期**一说，仅仅是一个 **render 函数**而已。 但是引入 **Hooks** 之后就变得不同了，它能让组件在不使用 class 的情况下拥有 state，所以就有了生命周期的概念，所谓的生命周期其实就是 `useState`、 `useEffect()` 和 `useLayoutEffect()` 。
>
> 即：**Hooks 组件（使用了Hooks的函数组件）有生命周期，而函数组件（未使用Hooks的函数组件）是没有生命周期的**。
>
> 下面是具体的 class 与 Hooks 的**生命周期对应关系**：
>
> - `constructor`：函数组件不需要构造函数，可以通过调用 `**useState 来初始化 state**`。如果计算的代价比较昂贵，也可以传一个函数给 `useState`。
>
> ```javascript
> const [num, UpdateNum] = useState(0)
> ```
>
> - `getDerivedStateFromProps`：一般情况下，我们不需要使用它，可以在**渲染过程中更新 state**，以达到实现 `getDerivedStateFromProps` 的目的。
>
> ```javascript
> function ScrollView({row}) {
>   let [isScrollingDown, setIsScrollingDown] = useState(false);
>   let [prevRow, setPrevRow] = useState(null);
>   if (row !== prevRow) {
>     // Row 自上次渲染以来发生过改变。更新 isScrollingDown。
>     setIsScrollingDown(prevRow !== null && row > prevRow);
>     setPrevRow(row);
>   }
>   return `Scrolling down: ${isScrollingDown}`;
> }
> ```
>
> React 会立即退出第一次渲染并用更新后的 state 重新运行组件以避免耗费太多性能。
>
> - `shouldComponentUpdate`：可以用 `**React.memo**` 包裹一个组件来对它的 `props` 进行浅比较
>
> ```javascript
> const Button = React.memo((props) => {  // 具体的组件});
> ```
>
> 注意：`**React.memo 等效于 **``**PureComponent**`，它只浅比较 props。这里也可以使用 `useMemo` 优化每一个节点。
>
> - `render`：这是函数组件体本身。
> - `componentDidMount`, `componentDidUpdate`： `useLayoutEffect` 与它们两的调用阶段是一样的。但是，我们推荐你**一开始先用 useEffect**，只有当它出问题的时候再尝试使用 `useLayoutEffect`。`useEffect` 可以表达所有这些的组合。
>
> ```javascript
> // componentDidMount
> useEffect(()=>{
>   // 需要在 componentDidMount 执行的内容
> }, [])
> useEffect(() => { 
>   // 在 componentDidMount，以及 count 更改时 componentDidUpdate 执行的内容
>   document.title = `You clicked ${count} times`; 
>   return () => {
>     // 需要在 count 更改时 componentDidUpdate（先于 document.title = ... 执行，遵守先清理后更新）
>     // 以及 componentWillUnmount 执行的内容       
>   } // 当函数中 Cleanup 函数会按照在代码中定义的顺序先后执行，与函数本身的特性无关
> }, [count]); // 仅在 count 更改时更新
> ```
>
> **请记得 React 会等待浏览器完成画面渲染之后才会延迟调用 ，因此会使得额外操作很方便**
>
> - `componentWillUnmount`：相当于 `useEffect `里面返回的 `cleanup` 函数
>
> ```javascript
> // componentDidMount/componentWillUnmount
> useEffect(()=>{
>   // 需要在 componentDidMount 执行的内容
>   return function cleanup() {
>     // 需要在 componentWillUnmount 执行的内容      
>   }
> }, [])
> ```
>
> - `componentDidCatch` and `getDerivedStateFromError`：目前**还没有**这些方法的 Hook 等价写法，但很快会加上
> - ![image-20220314175542275](C:\Users\H\AppData\Roaming\Typora\typora-user-images\image-20220314175542275.png)

### 8.常用的Hooks

> *Hook* 是 React 16.8 的新增特性。它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性。
>
> React Hooks的几个常用钩子:
>
> 1. `useState()` //状态钩子
> 2. `useContext()` //共享状态钩子
> 3. `useReducer()` //action 钩子
> 4. `useEffect()` //副作用钩子
>
> 还有几个不常见的大概的说下，后续会专门写篇文章描述下
>
> - 1.useCallback 记忆函数 一般把**函数式组件理解为class组件render函数的语法糖**，所以每次重新渲染的时候，函数式组件内部所有的代码都会重新执行一遍。而有了 useCallback 就不一样了，你可以通过 useCallback 获得一个记忆后的函数。
>
>   ```js
>   function App() {
>     const memoizedHandleClick = useCallback(() => {
>       console.log('Click happened')
>     }, []); // 空数组代表无论什么情况下该函数都不会发生改变
>     return <SomeComponent onClick={memoizedHandleClick}>Click Me</SomeComponent>;
>   }
>   ```
>
>   第二个参数传入一个数组，数组中的每一项一旦值或者引用发生改变，useCallback 就会重新返回一个新的记忆函数提供给后面进行渲染。
>
> - 2.useMemo 记忆组件 useCallback 的功能完全可以由 useMemo 所取代，如果你想通过使用 useMemo 返回一个记忆函数也是完全可以的。 唯一的区别是：**useCallback 不会执行第一个参数函数，而是将它返回给你，而 useMemo 会执行第一个函数并且将函数执行结果返回给你**。
>    所以 useCallback 常用记忆事件函数，生成记忆后的事件函数并传递给子组件使用。而 useMemo 更适合经过函数计算得到一个确定的值，比如记忆组件。
>
> - 3.useRef 保存引用值
>
>   useRef 跟 createRef 类似，都可以用来生成对 DOM 对象的引用。useRef 返回的值传递给组件或者 DOM 的 ref 属性，就可以通过 ref.current 值**访问组件或真实的 DOM 节点，重点是组件也是可以访问到的**，从而可以对 DOM 进行一些操作，比如监听事件等等。
>
> - 4.useImperativeHandle 穿透 Ref
>
>   通过 useImperativeHandle 用于让父组件获取子组件内的索引
>
> - 5.useLayoutEffect 同步执行副作用
>
>   大部分情况下，使用 useEffect 就可以帮我们处理组件的副作用，但是如果想要同步调用一些副作用，比如对 DOM 的操作，就需要使用 useLayoutEffect，useLayoutEffect 中的副作用会在 DOM 更新之后同步执行。
>
>   **useEffect和useLayoutEffect有什么区别**：简单来说就是调用时机不同，useLayoutEffect和原来componentDidMount&componentDidUpdate一致，在react完成DOM更新后马上同步调用的代码，会阻塞页面渲染。而useEffect是会在整个页面渲染完才会调用的代码。`官方建议优先使用useEffect`

### 9.useState

> #### useState用法
>
> > const [state, setState] = useState(initValue)
>
> useState接收一个初始值，这个初始值可以是对象，也可以是简单数据类型。
>
> 在初始渲染期间，返回的状态 (state) 与传入的第一个参数 (initValue) 值相同。
>
> 返回的setState是一个函数，它接收一个新的 state 值并将组件的一次重新渲染加入队列。
>
> #### 规则
>
> 只能React函数组件中使用Hooks。
>
> useState不能在内部的循环、条件判断、嵌套的方法中使用。
>
> #### setState异步更新
>
> 接上面的代码
>
> ```js
> export default function App() {
>   const initValue = 0;
>   const [n, setN] = useState(initValue);
>   const add = () => {
>     setN(n + 1);
>     console.log(n)
>     setN(n+1)
>     console.log(n)
>   };
>   return (
>     <div className="App">
>       <button onClick={add}>+1,此时n:{n}</button>
>     </div>
>   );
> }
> ```
>
> 点击之后视图上只加了1，但是log了两次，说明setN是异步的，不能立即更新，而是把所有同步代码执行完之后，再执行视图更新，而这时候入参的n还是旧的值，并没有被更新。
>
> <img src="https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/54dbc20480264576bdeda54b0d4fea2d~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp" alt="img" style="zoom:50%;" />
>
> 注：这时候传入的 n 由于闭包的原因，所以拿到的是旧的值。比如刚开始 n 是0，调用两次 setN 后相当于设置了两次1。
>
> 在React 事件中，setState 确实是异步的，为了提高性能（每次 setState 都会重新执行 hooks 函数），所以相同的setState会进行合并。

> #### setN传函数
>
> setN是可以传递函数的，它的参数是旧的n，返回新的value
>
> 上面的代码如果修改成以下内容就有效
>
> ```js
> export default function App() {
>   const initValue = 0;
>   const [n, setN] = useState(initValue);
>   const add = () => {
>   +   setN((oldN) => {
>   +   	console.log(oldN);
>   +   	return oldN + 1;
>     });
>   +  setN((oldN) => {
>   + 	console.log(oldN);
>   +  	return oldN + 1;
>     });
>   };
>   return (
>     <div className="App">
>       <button onClick={add}>+1,此时n:{n}</button>
>     </div>
>   );
> }
> ```
>
> 如果可以的话，推荐优先使用函数。
>
> #### state可以是对象
>
> 当 useState 中的 state 为对象时，调用相应的 setState 有一些要注意的地方，useState 不会自动合并更新对象，你可以用函数式的 setState 结合展开运算符来达到合并更新对象的效果。
>
> #### 错误示例
>
> ```js
> export default function App() {
>   const initValue = { n: 0, m: 0 };
>   const [state, setState] = useState(initValue);
>   const addN = () => {
>     setState((state) => {
>       return { n: state.n + 1 };
>     });
>   };
>   const addM = () => {
>     setState((state) => {
>       return { m: state.m + 1 };
>     });
>   };
>   return (
>     <div className="App">
>       <button onClick={addN}>+1,此时n:{state.n}</button>
>       <button onClick={addM}>+1,此时m:{state.m}</button>
>     </div>
>   );
> }
> ```
>
> 这里的state不会自动帮我们合并，所以需要我们使用...运算符来帮助我们手动合并。
>
> #### 正确示例
>
> ```js
> export default function App() {
>   const initValue = { n: 0, m: 0 };
>   const [state, setState] = useState(initValue);
>   const addN = () => {
>     setState((state) => {
>    +  return { ...state, n: state.n + 1 };
>     });
>   };
>   const addM = () => {
>     setState((state) => {
>    +  return { ...state, m: state.m + 1 };
>     });
>   };
>   return (
>     <div className="App">
>       <button onClick={addN}>+1,此时n:{state.n}</button>
>       <button onClick={addM}>+1,此时m:{state.m}</button>
>     </div>
>   );
> }
> ```

> #### 总结
>
> - useState返回一对数组，第一个值是初始值，第二个值state的setter方法
> - 如果state是对象的话，需要我们手动合并
> - setState时，可以传函数也可以传值，如果依赖于旧的state，最好使用函数的形式

### 10.useReducer

> ####  什么是useReducer
>
> React 本身不提供状态管理功能，通常需要使用外部库。这方面最常用的库是 Redux。
>
> Redux 的核心概念是，组件发出 action 与状态管理器通信。状态管理器收到 action 以后，使用 Reducer 函数算出新的state，Reducer 函数的形式是`(state, action) => newState`
>
> `useReducer`用来引入 Reducer 功能。
>
> #### useReducer语法
>
> ```
> const [state, dispatch] = useReducer(reducer, initialState);
> ```
>
> useReducer用来接收两个参数，分别是Reducer函数和初始state
>
> useReducer 返回了一个数组，2个元素分别为 state 和 dispatch 方法。其中 state 在我们的例子中就是当前的 n 值，dispatch 方法接受一个参数，执行对应的 action。dispatch 执行后，对应的 state 会改变，组件会 rerender，来展示最新的状态。
>
> #### reducer函数
>
> reducer函数里面可以存放state的各种操作，它类似状态管理器（其实我觉得应该叫state数据管理器），通过dispatch函数的传递action，可以触发reducer函数内部运算操作，并返回当前state
>
> #### 用法
>
> 首先，我们要定义一个Reducer函数，下面的函数将定义加法和减法
>
> ```js
> const reducer=(state,action)=>{
> 	if(action.type==="add"){
>     	return {
>         	n:state.n+1
>         }
>     }else if(action.type==="sub"){
>     	return {
>         	n:state.n-1
>         }
>     }
> }
> ```
>
> 其次，使用useReducer接收Reducer函数和一个初始state，并返回当前值state与dispatch函数
>
> 当触发事件时，使用dispatch传递action，让reducer计算出新的state
>
> ```js
> export default function App() {
>   const initialState = { n: 1 };
>   const [state, dispatch] = useReducer(reducer, initialState); 
> //使用useReducer接收reducer参数和初始state
>   return (
>     <>
>       <div>{state.n}</div>
>       <button
>         onClick={() => {
>           dispatch({ type: "add" });  // 传递action
>         }}
>       >
>         点击+
>       </button>
>       <button
>         onClick={() => {
>           dispatch({ type: "sub" });//传递action触发reducer函数
>         }}
>       >
>         点击-
>       </button>
>     </>
>   );
> }
> ```
>
> 当传递的action的type是对应值时，就会算出对应的内容 <img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7fbc356937354c09920bb66371709a75~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp" alt="img" style="zoom:50%;" />
>
> dispatch()是发出 Action 的唯一方法。它发出action后使reducer执行，并返回一个新的state。
>
> #### 小结
>
> 我们学习到reducer的使用步骤
>
> - 先定义一个initialValue
> - 定义一个reducer函数，把所有操作方法都丢进去
> - 把initialValue跟reducer通过useReducer关联起来，并返回一个当前state和dispatch
> - 当需要计算时，使用dispatch传递一个action值，触发reducer函数执行，返回一个新的state

> #### 新的示例
>
> 来看一个比较复杂的示例
>
> ```js
> import React from "react";
> import "./styles.css";
> // 定义操作器
> const reducer = (state, action) => {
>   if (action.type === "change") {
>     return { ...state, ...action.formData };
>   } else if (action.type === "reset") {
>     return { name: "", age: "", love: "" };
>   }
> };
> export default function App() {
>   const [state, dispatch] = React.useReducer(reducer, {
>     name: "",
>     age: "",
>     love: ""
>   });
>   const onclick = () => {
>     alert(`您的姓名是${state.name},年龄是${state.age},您喜欢${state.love}`);
>   };
>   return (
>     <>
>       <form>
>         <div>
>           姓名
>           <input
>             onInput={(e) => {
>               dispatch({ type: "change", formData: { name: e.target.value } });
>             }}
>           />
>         </div>
>         <div>
>           年龄
>           <input
>             onInput={(e) => {
>               dispatch({ type: "change", formData: { age: e.target.value } });
>             }}
>           />
>         </div>
>         <div>
>           喜欢
>           <input
>             onInput={(e) => {
>               dispatch({ type: "change", formData: { love: e.target.value } });
>             }}
>           />
>         </div>
>         <button type="reset">重置</button>
>         <button type="button" onClick={onclick}>
>           确认
>         </button>
>       </form>
>       <hr />
>     </>
>   );
> }
> ```
>
> ![img](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/9960b03c35ea418db9f5c2a2acdec890~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
>
> 上面使用了action传递更多的属性来进行更多的计算可能性。代码直接直接复制到[代码沙盒](https://link.juejin.cn?target=https%3A%2F%2Fcodesandbox.io%2F)中运行。
>
> #### 使用useReducer替代redux
>
> 我们可以使用useContext配合useReducer来帮助我们完成一些数据、操作的流转
>
> 主要分成以下步骤
>
> #### 创建store用来存放数据
>
> 示例：
>
> ```js
> const store={
> 	user:null,
> 	age:null,
>    	movies:null
> }
> ```
>
> Store 就是保存数据的地方，你可以把它看成一个容器。整个应用只能有一个 Store。
>
> #### 创建reducer函数存放操作
>
> 示例：
>
> ```js
> const reducer = (state, action) => {
>   switch (action.type) {
>     case "setUser":
>       return { ...state, user: action.user };
>     case "setBooks":
>       return { ...state, age: action.age };
>     case "setMovies":
>       return { ...state, movies: action.movies };
>     default:
>       break;
>   }
> }
> ```
>
> reducer是一个操作管理器，当Store 收到 Action 以后，必须给出一个新的 State，这样 View 才会发生变化。这种 State 的计算过程就叫做 Reducer。
>
> Reducer 是一个函数，它接受 Action 和当前 State 作为参数，返回一个新的 State。
>
> #### 创建一个Context
>
> ```js
> const Context =React.createContext()
> ```
>
> 创建上下文
>
> #### 创建读写api
>
> ```js
>  const [state, dispatch] = useReducer(reducer, store);
> ```
>
> store需要知道reducer函数，做法就是使用useReducer将store与reducer关联起来，再通过dispatch发出action
>
> ### Context.provider提供Context
>
> 主要渲染组件
>
> ```js
> return (
>     <Context.Provider value={{state:state,dispatch:dispatch}}>
>       <User />
>       <hr />
>       <Books />
>       <Movies />
>     </Context.Provider>
>   );
> ```
>
> 在provider内的所有组件都可以通过useContext来获取到value里面的API
>
> #### 各个组件使用useContext获取读写api
>
> 子组件
>
> ```js
>  const { state, dispatch } = useContext(Context);
> ```
>
> #### 表结构编程
>
> 上面的步骤已经介绍完毕，但是有时候数据多，reducer的代码量很大，那么能否将代码完善一下？
>
> 可以使用表结构编程，将reduce里面的代码保存在一个对象里。
>
> ```js
> const fnObj = {
>   setUser: (state, action) => {
>     return { ...state, user: action.user };
>   },
>   setBooks: (state, action) => {
>     return { ...state, books: action.books };
>   },
>   setMovies: (state, action) => {
>     return { ...state, movies: action.movies };
>   }
> };
> ```
>
> 此时的reducer为
>
> ```js
> function reducer(state, action) {
>   const fn = obj[action.type];
>   if (fn) {
>     return fn(state, action);
>   } else {
>     throw new Error("这是不对的");
>   }
> }
> ```
>
> 这种方法比较适合模块化开发，因为对象是可以合并的，所以当模块化后就可以通过...运算符将不同的逻辑运算的对象合并到一个表对象里面，类似于这样
>
> ```js
> const objFn={
> 	...app,
>     ...children
> }
> ```
>
> ### 总结
>
> 我们学习到使用reducer函数的步骤以及通过useContext（上下文）provider（提供）让各组件共享state的操作步骤
>
> 如果需要优化reducer的代码，则推荐表结构编程来让代码更加优雅

### 10.useContext

> #### 什么是Context
>
> `context`（上下文）可以看成是扩大版的`props`，它可以将全局的数据通过`provider`接口传递value给局部的组件，让包围在`provider`中的局部组件可以获取到全局数据的读写接口
>
> 全局变量可以看成是全局的上下文
>
> 而上下文则是局部的全局变量，因为只有包围在`provider`中的局部组件才可以获取到这些全局变量的读写接口

> #### 用法
>
> - 创建context
> - 设置`provider`并通过value接口传递state
> - 局部组件获取读写接口
>
> #### 案例理解
>
> 案例理解是最快的方式，我在下面的代码中，将设置一个父组件，一个子组件，通过useContext来传递state，并在子组件上设置一个按钮来改变全局state
>
> ```js
> import React, { createContext, useContext, useState } from "react";
> const initialState = { m: 100, n: 50 }; // 定义初始state
> const X = createContext(); // 创建Context
> let a = 0;
> export default function App() {
>   console.log(`render了${a}次`);//用来检查执行App函数多少次
>   const [state, setState] = useState(initialState); // 创建state读写接口
>   a += 1;
>   return (
>     <X.Provider value={{ state, setState }}> // 通过provider提供value给包围里内部组件，只有包围里的组件才有效
>       <Father></Father>
>     </X.Provider>
>   );
> }
> 
> const Father = (props) => {
>   const { state, setState } = useContext(X);//拿到 名字为X的上下文的value，用两个变量来接收读写接口
>   const addN = () => {
>     setState((state) => {
>       return { ...state, n: state.n + 1 };
>     });
>   };
>   const addM = () => {
>     setState((state) => {
>       return { ...state, m: state.m + 1 };
>     });
>   };
>   return (
>     <div>
>       爸爸组件
>       <div>n:{state.n}</div>
>       <Child />
>       <button onClick={addN}>设置n</button>
>       <button onClick={addM}>设置m</button>
>     </div>
>   );
> };
> const Child = (props) => {
>   const { state } = useContext(X); // 读取state
>   return (
>     <div>
>       儿子组件
>       <div>m:{state.m}</div>
>     </div>
>   );
> };
> ```
>
> 拿到读写接口的组件就可以控制state数据 ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/da920a39db1143a2be23383d97e13174~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
>
> > tips：注意到最上层的变量a没？这是我用来测试的，我发现点击按钮后会触发App函数并更新页面，说明react下使用`context`来修改数据的时候，都会重新进行全局执行，而不是数据响应式的。
>
> # 总结
>
> 我们学习到`Context`上下文的基本概念和作用，并且通过小案例总结得出`context`的使用方法：
>
> - 使用`creacteContext`创建一个上下文
> - 设置`provider`并通过`value`接口传递`state`数据
> - 局部组件从`value`接口中传递的数据对象中获取读写接口

### 11.useEffect

> ### 什么是Effect
>
> effect（副作用）是react函数组件用来替代生命周期的函数。你可以把 `useEffect Hook` 看做 `componentDidMount`，`componentDidUpdate` 和 `componentWillUnmount` 这三个函数的组合。
>
> #### 用法
>
> 它的参数如下： `useEffect(callback[,[]])`
>
> 第一个参数接受一个`callback`回调函数，里面可以写执行业务代码
>
> 第二个参数可省略，它接受一个数组，可以是空数组，也包含了`state`数据

> #### 说明
>
> 当不传递第二个参数时，每次render都会执行一遍callback函数，相当于包含第一遍render的`componentDidUpdate`
>
> 当传递第二个参数且是空数组时，只有第一次render才会执行callback，类似于`componentDidMount`
>
> 不管是否传递第二个参数，只要在`callback`中return 一个函数，就相当于告诉react此组件挂掉之前执行什么操作，类似于`componentWillUnMount`
>
> ### 示例
>
> #### 模拟componentDidMount
>
> ```js
> export default function App() {
>   const [state, setState] = useState(0);
>   useEffect(() => {
>     console.log("第一次render了");
>   }, []); //这里传递了空数组
>   return (
>     <div className="App">
>       <h1>{state}</h1>
>       <button
>         onClick={() => {
>           setState((x) => x + 1);
>         }}
>       >
>         按钮+1
>       </button>
>     </div>
>   );
> }
> ```
>
> 上面的例子中，我们在第二个参数中传递了数组，这个数组表示让react监控数组内部的数据，当其发生变化时，执行`useEffect`函数。
>
> 当传递的是空数组时，就只会触发第一次渲染. ![img](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ab4f0917ab474e03b637a36379209a47~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
>
> #### 模拟componentDidUpdate
>
> 当我把上面的例子改成
>
> ```js
> React.useEffect(() => {
>     console.log("state发生变化了");
>   }, [state]); //这里传递了数组，里面是监控的数据
> ```
>
> 如果state发生变化，就会执行此段函数。
>
> 这里注意，**useEffect总是后于render函数的，所以不管怎样，都会通过render函数把页面更新完成，才会执行useEffect内的代码 **<img src="https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/8c7d0247150a4f3dbb7ef7a3cc5d8405~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp" alt="img" style="zoom:50%;" />
>
> > 小tips： 上面的代码实际上并不能模拟componentDidUpdate，因为当state第一次初始化时，也会执行Effect内部的代码
>
> 如果要监控所有数据，可以不传第二个参数
>
> #### 模拟componentWillUnMount
>
> 在useEffect参数的callback函数中直接return一个函数，就相当于告诉react，我要在这个组件死之前 执行一段代码
>
> ```js
> import React from "react";
> export default function App() {
>   const [state, setState] = React.useState(0);
> 
>   return (
>     <div className="App">
>       {state === 1 ? null : <Child />}
>       <h1>{state}</h1>
>       <button
>         onClick={() => {
>           setState((x) => x + 1);
>         }}
>       >
>         按钮+1
>       </button>
>     </div>
>   );
> }
> const Child = () => {
>   React.useEffect(() => {
>   console.log("这个child组件第一次render了")//这里是第一次进入时执行的代码
>     return () => { // **注意看这里的代码**
>       console.log("这个div灭亡前的代码");
>     };
>   }, []);
>   return <div>孩子组件</div>;
> };
> ```
>
> 上面的代码设置了一个button，当点击时，触发state+1，然后页面不再渲染`Child`组件，就相当于删除了`Child`组件,在Child灭亡前，会执行`console.log("这个div灭亡前的代码")`
>
> ### LayoutEffect
>
> 要了解layoutEffect和useEffect的区别，需要看下面这张图 ![img](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4452a2050c154087bb4212e7abd08fac~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
>
> react函数组件执行过程是**先执行函数组件代码**，变成虚拟dom，再经过编译后转化成真实dom，让render函数去渲染并更新视图。是一个从上到下数据变换的过程。
>
> useEffect发生在视图更新后，而layoutEffect发生在编译成dom元素之后视图更新之前
>
> #### useLayoutEffect比useEffect先执行
>
> ```js
> export default function App() {
>   const [state, setState] = React.useState(0);
>   useEffect(() => {
>     console.log("第一次render时执行");
>   }, []);
>   useEffect(() => {
>     if (state > 0) {
>       console.log("第二次之后render时执行");
>     }
>   }, [state]);
>   useLayoutEffect(() => {
>     console.log("我比useEffect先执行");
>   });
>   return (
>     <div className="App">
>       <h1>{state}</h1>
>       <button
>         onClick={() => {
>           setState((x) => x + 1);
>         }}
>       >
>         按钮+1
>       </button>
>     </div>
>   );
> }
> ```
>
> 上面的代码第一次渲染后是这样的
>
> ```
> ** log控制台 **
> 我比useEffect先执行 
> 第一次render时执行 
> ```
>
> 点击button后的控制台变成了这样
>
> ```
> ** log控制台 **
> 我比useEffect先执行 
> 第一次render时执行 
> 我比useEffect先执行 
> 第二次之后render时执行
> ```
>
> #### 结论
>
> `useEffect`的代码是按照顺序执行的，但`useLayoutEffect`总是比`useEffect`先执行。
>
> 由于`useLayoutEffect`的代码是跟DOM操作相关的，所以最好在里面写跟DOM相关的代码
>
> #### useEffect和useLayoutEffect如何抉择
>
> 两者的执行顺序不同，定位也不同，由于操作DOM需要一定时间，我们最好优先使用`useEffect`，以保证用户页面第一时间渲染出来。
>
> 如果业务需求是先要进行DOM操作或者跟页面布局相关的，那么就可以使用`useLayoutEffect`。
>
> #### 总结
>
> 由于函数组件没有生命周期，所以`react`创造了`useEffect`，以保证支持一定的生命周期操作，我们从上面的例子中了解到`useEffect`总是后于`render`执行，也熟悉了`useEffect`的传参与生命周期的关系，如果说都无法满足需求，且需要操作DOM布局的情况那么可以尝试使用`useLayoutEffect`

### 12.useMemo

> ### Memo
>
> 在class的时代，我们一般是通过pureComponent来对数据进行一次浅比较，引入Hook特性后，我们可以使用Memo进行性能提升。
>
> 在此之前，我们来做一个实验
>
> ```js
> import React, { useState } from "react";
> import ReactDOM from "react-dom";
> 
> import "./styles.css";
> 
> function App() {
>   const [n, setN] = useState(0);
>   const [m, setM] = useState(10);
>   console.log("执行最外层盒子了");
>   return (
>     <>
>       <div>
>         最外层盒子
>         <Child1 value={n} />
>         <Child2 value={m} />
>         <button
>           onClick={() => {
>             setN(n + 1);
>           }}
>         >
>           n+1
>         </button>
>         <button
>           onClick={() => {
>             setM(m + 1);
>           }}
>         >
>           m+1
>         </button>
>       </div>
>     </>
>   );
> }
> function Child1(props) {
>   console.log("执行子组件1了");
>   return <div>子组件1上的n：{props.value}</div>;
> }
> function Child2(props) {
>   console.log("执行子组件2了");
>   return <div>子组件2上的m：{props.value}</div>;
> }
> 
> const rootElement = document.getElementById("root");
> ReactDOM.render(<App />, rootElement);
> ```
>
> 上面的代码我设置了两个子组件，分别读取父组件上的n跟m，然后父组件上面设置两个点击按钮，当点击后分别让设置的n、m加1。以下是第一次渲染时log控制台的结果
>
> ```!
> 执行最外层盒子了 
> 执行子组件1了 
> 执行子组件2了 
> ```
>
> 跟想象中一样，render时先进入App函数，执行，发现里面的两个child函数，执行，创建虚拟dom，创建实体dom，最后将画面渲染到页面上。 ![img](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/4ee29f9aa15c4ba7af3129028494d2cf~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
>
> #### 使用Memo优化
>
> 当我点击n+1按钮时，此时state里面的n必然+1，也会重新引发render渲染，并把新的n更新到视图中。 ![img](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7f55bac905da45b2bd56bde9836bcf22~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp) 我们再看控制台
>
> ```!
> 执行最外层盒子了 
> 执行子组件1了 
> 执行子组件2了 
> + 执行最外层盒子了 
> + 执行子组件1了 
> + 执行子组件2了 //为什么组件2也渲染了，里面的m没有变化 
> ```
>
> 你会发现子组件2也渲染了，显然react重新把所有的函数都执行了一遍，把未曾有n数据的子组件2也重新执行了。
>
> 如何优化？我们可以使用`memo`把子组件改成以下代码
>
> ```js
> const Child1 = React.memo((props) => {
>   console.log("执行子组件1了");
>   return <div>子组件1上的n：{props.value}</div>;
> });
> 
> const Child2 = React.memo((props) => {
>   console.log("执行子组件2了");
>   return <div>子组件2上的m：{props.value}</div>;
> });
> ```
>
> 再重新点击试试？
>
> ```!
> 执行最外层盒子了 
> 执行子组件1了 
> 执行子组件2了 
> + 执行最外层盒子了 
> + 执行子组件1了 
> ```
>
> 会发现没有执行子组件2了
>
> 这样的话react就会只执行对应state变化的组件，而没有变化的组件，则复用上一次的函数，也许memo也有memory的意思，代表记忆上一次的函数，不重新执行（我瞎猜的- -！！）
>
> #### 出现bug
>
> 上面的代码虽然已经优化好了性能，但是会有一个bug
>
> 上面的代码是由父组件控制`<button>`的，如果我把控制state的函数传递给子组件，会怎样呢？
>
> ```html
>  <Child2 value={m} onClick={addM} /> //addM是修改M的函数
> ```
>
> 点击按钮让n+1
>
> ```!
> 执行最外层盒子了 
> 执行子组件1了 
> 执行子组件2了 
> + 执行最外层盒子了 
> + 执行子组件1了 
> + 执行子组件2了 
> ```
>
> 又重新执行子组件2。
>
> 为什么会这样？因为App重新执行了，它会修改addM函数的地址（函数是复杂数据类型），而addM又作为props传递给子组件2，那么就会引发子组件2函数的重新执行。
>
> ### useMemo
>
> 这时候就要用useMemo解决问题。
>
> ```
> useMemo(()=>{},[])
> ```
>
> useMemo接收两个参数，分别是函数和一个数组（实际上是依赖），函数里return 函数,数组内存放依赖。
>
> ```js
> const addM = useMemo(() => {
>     return () => {
>       setM({ m: m.m + 1 });
>     };
>   }, [m]); //表示监控m变化
> ```
>
> 使用方式就跟useEffect似的。
>
> #### useCallback
>
> 上面的代码很奇怪有没有
>
> ```js
> useMemo(() => {
>     return () => {
>       setM({ m: m.m + 1 });
>     };
>   }, [m])
> ```
>
> react就给我们准备了语法糖，useCallback。它是这样写的
>
> ```javascript
>   const addM = useCallback(() => {
>     setM({ m: m.m + 1 });
>   }, [m]);
> ```
>
> 是不是看上去正常多了？
>
> #### 最终代码
>
> ```js
> import React, { useCallback, useMemo, useState } from "react";
> import ReactDOM from "react-dom";
> 
> import "./styles.css";
> 
> function App() {
>   const [n, setN] = useState(0);
>   const [m, setM] = useState({ m: 1 });
>   console.log("执行最外层盒子了");
>   const addN = useMemo(() => {
>     return () => {
>       setN(n + 1);
>     };
>   }, [n]);
>   const addM = useCallback(() => {
>     setM({ m: m.m + 1 });
>   }, [m]);
>   return (
>     <>
>       <div>
>         最外层盒子
>         <Child1 value={n} click={addN} />
>         <Child2 value={m} click={addM} />
>         <button onClick={addN}>n+1</button>
>         <button onClick={addM}>m+1</button>
>       </div>
>     </>
>   );
> }
> const Child1 = React.memo((props) => {
>   console.log("执行子组件1了");
>   return <div>子组件1上的n：{props.value}</div>;
> });
> 
> const Child2 = React.memo((props) => {
>   console.log("执行子组件2了");
>   return <div>子组件2上的m：{props.value.m}</div>;
> });
> 
> const rootElement = document.getElementById("root");
> ReactDOM.render(<App />, rootElement);
> ```
>
> 建议直接拷到[codesanbox](https://link.juejin.cn?target=https%3A%2F%2Fcodesandbox.io%2F)查看
>
> #### 总结
>
> - 使用`memo`可以帮助我们优化性能，让`react`没必要执行不必要的函数
> - 由于复杂数据类型的地址可能发生改变，于是传递给子组件的`props`也会发生变化，这样还是会执行不必要的函数，所以就用到了`useMemo`这个api
> - `useCallback`是`useMemo`的语法糖

### 13.useRef

> ### useRef是什么
>
> useRef 返回一个可变的 ref 对象，其 .current 属性被初始化为传入的参数（initialValue）。返回的 ref 对象在组件的整个生命周期内保持不变。
>
> 这个ref对象只有一个current属性，你把一个东西保存在内，它的地址一直不会变。
>
> #### 简单示例
>
> ```js
> import React, { useRef } from "react";
> export default function App() {
>   const r = useRef(0);
>   console.log(r);
>   const add = () => {
>     r.current += 1;
>     console.log(`r.current:${r.current}`);
>   };
>   return (
>     <div className="App">
>       <h1>r的current:{r.current}</h1>
>       <button onClick={add}>点击+1</button>
>     </div>
>   );
> }
> ```
>
> 我在上面设置了一个r保存`useRef`的结果,并把它`log`打出来，我们来看看内部是什么
>
> ```
> >  {current: 0}
> ```
>
> 内部是一个`current`属性，保存了我传入的0。 <img src="https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/019e9812521b4fc1a18cd3cc1246fa16~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp" alt="img" style="zoom:50%;" />
>
> #### useRef变化不会主动使页面渲染
>
> 我们点击上方的按钮，让`current`+1,可以看到页面没有主动渲染，但是新的`current`的值已经变成了1 <img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/d5d8dcfd83a54c4dadc6161fd09529e0~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp" alt="img" style="zoom:50%;" />
>
> 说明current属性的值发生变化，不会跟`useState`或者`useReducer`一样触发页面变化。要怎样解决这个问题呢？我们先来个小结再解决
>
> #### 小结
>
> - 我们使用`useCurrent`传入`initialValue`,可以得出其变成了一个内含`current`属性的对象，`current`属性对应的值为`initialValue`
> - 根据react文档，可以知道这个对象的地址从头到位都不会变化
> - useRef变化不会主动使页面渲染
>
> #### 应用解决
>
> 我们来使用`useRef`配合`useEffect`模拟一个`ComponentDidUpdate`
>
> ```js
> import React, { useEffect, useRef, useState } from "react";
> export default function App() {
>   const r = useRef(0);
>   const [n, setN] = useState(0);
>   useEffect(() => {
>     r.current += 1;
>     if (r.current > 1) {
>       console.log("r.current:" + r.current);
>       console.log("n:" + n);
>     }
>   });
>   return (
>     <div className="App">
>       <h1>n:{n}</h1>
>       <h1>r.current{r.current}</h1>
>       <button
>         onClick={() => {
>           setN(n + 1);
>         }}
>       >
>         {" "}
>         +1
>       </button>
>     </div>
>   );
> }
> ```
>
> 第一次渲染结果 ![img](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/de723310c433473f968f7b53786be8fa~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
>
> 当点击+1按钮时， ![img](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/49a2c151bdbe45d2a8001f8f3722ff82~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp) 会发现执行了代码，这样就可以实现`ComponentDidUpdate`
>
> #### current发生变化了
>
> 上面的代码中，可以看出页面上的`current`发生变化了，这是由于我们的state发生改变，所以将新的current也进行了渲染。
>
> 你可以理解为react的机制：
>
> > 这里有个落单的current，我不管。
>
> > 这个state发生变化了，快更新页面。等等，把这个current也带上吧。
>
> 所以我们可以通过设置一个不实用的state来手动设置`current`变化主动引发页面更新的情况。
>
> 如果你细心一点，就会发现，页面上渲染的值并不是最新的`current`的值。原因是`Effect`总是在`render`后才执行，而这时候`current`刚刚变成最新的，对`react`来说，它不会单单为了`current`再一次执行更新视图。所以页面上依然是旧的`current`。这也验证了我们的一开始的结论
>
> > useRef变化不会主动使页面渲染
>
> 我们一般不会让current显示在视图中，上面的例子只是希望你能注意到current的变化和视图之间的微妙关系
>
> #### 小结
>
> 由于`current`不会主动让页面渲染，所以我们最好设置一个假的state，来手动设置让current随着这个假的state一起被视图所更新就行。
>
> ### 不得不说forwardRef
>
> #### ref
>
> 在hook组件上，是不能直接使用ref的，官网甚至不推荐在class组件上使用ref。但是我们也许会用到ref，ref主要用于传递给子组件，并拿到对应子组件的dom。这里是官方文档[Refs和函数组件](https://link.juejin.cn?target=https%3A%2F%2Fzh-hans.reactjs.org%2Fdocs%2Frefs-and-the-dom.html%23refs-and-function-components)
>
> #### 何时使用 Refs
>
> 下面是几个适合使用 refs 的情况：
>
> - 管理焦点，文本选择或媒体播放。
> - 触发强制动画。
> - 集成第三方 DOM 库。
>
> ## 解读官方例子
>
> ```js
> function CustomTextInput(props) {
>   // 这里必须声明 textInput，这样 ref 才可以引用它
>   const textInput = useRef(null);//创建一个包含current属性的对象
> 
>   console.log(textInput);
>   function handleClick() {
>     textInput.current.focus();
>   }
> 
>   return (
>     <div>
>       <input type="text" ref={textInput} />//挂到内部dom上
>       <input type="button" value="Focus the text input" onClick={handleClick} />
>     </div>
>   );
> }
> ```
>
> > tips：注意这个例子没有用到`fowardRef`，因为ref没有在子组件上用，而是在函数组件内部用而已
>
> 我们可以发现只要把`ref`挂到某个react元素上，就可以拿到它的dom。
>
> 一共分两个步骤
>
> - `useRef`创建一个`ref`对象
> - `ref={xx}`挂到`react`元素上
>
> 然后就可以使用这个元素了。官方例子是取到这个元素并且通过点击按钮让元素聚焦。我们目前大概理解了ref是怎样用的。
>
> #### 子组件上使用ref
>
> 上面的方法不能直接在子组件上使用，也许你会这样写
>
> ```html
> <Child ref={textInput} />
> ```
>
> 但是这样还拿不到子组件的DOM，我们需要使用`forwardRef`配合使用，上面的例子可以写成这样
>
> ```js
> function CustomTextInput(props) {
>   // 这里必须声明 textInput，这样 ref 才可以引用它
>   const textInput = useRef(null);
> 
>   console.log(textInput);
>   function handleClick() {
>     textInput.current.focus();
>   }
>   return (
>     <div>
>       <Child ref={textInput}/>  **依然使用ref传递**
>       <input type="button" value="Focus the text input" onClick={handleClick} />
>     </div>
>   );
> }
> const Child = forwardRef((props, ref) => {  //** 看我 **
>   return <input type="text" ref={ref} />;//** 看我挂到对应的dom上 **
> });
> ```
>
> 上面是**通过`forwardRef`把`Child`函数包起来**，然后传入第二个参数`ref`最后挂载`ref={ref}` 这样就可以拿到对应的DOM了，控制台打印一下看看
>
> > current: <input type="text"></input>
>
> 拿到子组件的DOM元素了。
>
> #### 小结
>
> 我们通过上面的例子得出在函数组件内部和子组件上使用ref是不同的
>
> - 如果要在函数内部使用，那就直接创建后挂ref属性给react元素就行了。
> - 如果要在子组件上使用，除了上面的步骤，还需要使用`forwardRef`把子组件的函数包起来，然后再传入第二个参数`ref`，最后挂载`ref`就可以正常取到DOM了。
>
> ### 总结
>
> 我们现在已经知道`useRef`会生成一个包含current属性的对象，它能够在react渲染全生命周期内保持地址不变，我们可以用它来做一些操作。
>
> 例如创建一个全局的始终不变的数据，拿来模拟`ComponentDidUpdate`
>
> 也可以用来挂载到react元素上，拿到这个元素的DOM。（这里区分函数内部组件直接使用`ref`和子组件配合`forwardRef`）

### 14.自定义Hook

> #### 自定义Hook
>
> 通过自定义 Hook，可以将组件逻辑提取到可重用的函数中。
>
> 可以理解成Hook就是用来放一些重复代码的函数。
>
> 下面我将做手动实现一个列表渲染、删除的组件，然后把它做成自定义Hook。
>
> #### 示例
>
> 定义数据列表
>
> ```js
> const initialState = [
>   { id: 1, name: "qiu" },
>   { id: 2, name: "yan" },
>   { id: 2, name: "xi" }
> ];
> ```
>
> 创建一个App组件并渲染它
>
> ```js
> function App(props) {
>   const [state, setState] = useState(initialState);
>   const deleteLi = (index) => {
>     setState((state) => {
>       const newState = JSON.parse(JSON.stringify(state));//深拷贝数据
>       newState.splice(index, 1);
>       return newState;
>     });
>   };
>   return (
>     <>
>       <ul>
>         {state
>           ? state.map((v, index) => {
>               return (
>                 <li key={index}>
>                   {index + "、"}
>                   {v.name}
>                   <button
>                     onClick={() => {
>                       deleteLi(index);
>                     }}
>                   >
>                     X
>                   </button>
>                 </li>
>               );
>             })
>           : "加载中"}
>       </ul>
>     </>
>   );
> }
> ```
>
> 上面的代码，我对一个数组进行渲染+删除操作，当点击按钮时，就会删除数组的对应index的数据，从而执行页面更新 ![img](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/f4b99c901f2a4807934d78d74242207b~tplv-k3u1fbpfcp-zoom-in-crop-mark:1304:0:0:0.awebp)
>
> #### 封装成Hook
>
> ```js
> const useList = () => {
>   const [state, setState] = useState(initialState);
>   const deleteLi = (index) => {
>     setState((state) => {
>       const newState = JSON.parse(JSON.stringify(state));
>       newState.splice(index, 1);
>       return newState;
>     });
>   };
>   return { state, setState, deleteLi };//返回查、改、删
> };
> ```
>
> 我把上面的业务逻辑都放在`useList`这个函数中，并将查、改、删的API给放在一个对象中return出去。这样就形成了一个自定义Hook
>
> #### 使用自定义Hook
>
> 一般我都会将自定义Hook给单独放在一个文件中，如果要使用，就引过来
>
> ```js
> + import useList from "./useList";
> ```
>
> 在需要使用的App组件中执行自定义Hook并接收API
>
> ```js
> function App(props) {
>  + const { state, deleteLi } = useList();//这里接收return出来的查、删API
>   return (
>  	... //这里跟最开始的App组件里是一样的，为了页面整洁，就不贴代码了
>   );
> }
> ```
>
> ### 总结
>
> 所谓的自定义Hook，实际上就是把很多重复的逻辑都放在一个函数里面，通过闭包的方式给`return`出来，这是非常高级的方式，程序员崇尚代码简洁，如果说以后业务开发时需要大量的重复代码，我们就可以将它封装成自定义Hook。
