<!--
 * @Descripttion: 
 * @version: 1.0
 * @Author: 
 * @Date: 2021-12-31 16:40:55
 * @LastEditors: YingJie Xing
 * @LastEditTime: 2021-12-31 16:45:02
 * @FilePath: /personalNotes/react/react-hooks/react-hooks.md
 * Copyright 2021 YingJie Xing, All Rights Reserved. 
-->


# hooks详解：

我们都知道react都核心思想就是，将一个页面拆成一堆独立的，可复用的组件，并且用自上而下的单向数据流的形式将这些组件串联起来。但假如你在大型的工作项目中用react，你会发现你的项目中实际上很多react组件冗长且难以复用。尤其是那些写成class的组件，它们本身包含了状态（state），所以复用这类组件就变得很麻烦。*Hook* 是 React 16.8 的新增特性。它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性。

**关于hooks的相关钩子：**

|     钩子名      | 作用                                                         |
| :-------------: | ------------------------------------------------------------ |
|    useState     | 初始化和设置状态                                             |
|    useEffect    | componentDidMount，componentDidUpdate和componentWillUnmount和结合体,所以可以监听useState定义值的变化 |
|   useContext    | 定义一个全局的对象,类似 context                              |
|   useReducer    | 可以增强函数提供类似 Redux 的功能                            |
|   useCallback   | 记忆作用,共有两个参数，第一个参数为一个匿名函数，就是我们想要创建的函数体。第二参数为一个数组，里面的每一项是用来判断是否需要重新创建函数体的变量，如果传入的变量值保持不变，返回记忆结果。如果任何一项改变，则返回新的结果 |
|     useMemo     | 作用和传入参数与 useCallback 一致,useCallback返回函数,useDemo 返回值 |
|     useRef      | 获取 ref 属性对应的 dom                                      |
| useLayoutEffect | 作用与useEffect相同，但在所有DOM改变后同步触发               |

## useState & useMemo & useCallback

- **React 假设当你多次调用 useState 的时候，你能保证每次渲染时它们的\**\*\*调用顺序\*\**\*是不变的。**
- 通过在函数组件里调用它来给组件添加一些内部 state，React会 **在重复渲染时保留这个 state**
- useState 唯一的参数就是初始 state
- useState 会返回一个数组(从数组里解构)，一个 state，一个更新 state 的函数
  - 在初始化渲染期间，返回的状态 (state) 与传入的第一个参数 (initialState) 值相同
  - 你可以在事件处理函数中或其他一些地方调用这个函数。它类似 class 组件的 this.setState，但是它**不会把新的 state 和旧的 state 进行合并，而是直接替换**

```
// 这里可以任意命名，因为返回的是数组，数组解构
const [state, setState] = useState(initialState);
```

```react
import React, { useState } from 'react';

function Example() {
  // 声明一个叫 "count" 的 state 变量  
  const [count, setCount] = useState(0);
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```

### 1.1每次渲染都是独立的闭包

- 每一次渲染都有它自己的 Props 和 State
- 每一次渲染都有它自己的事件处理函数
- 当点击更新状态的时候，函数组件都会重新被调用，那么每次渲染都是独立的，取到的值不会受后面操作的影响

```react
import { useState } from 'react'
import { PageContainer } from '@ant-design/pro-layout';
import { Card, Button, message } from 'antd';

const Ceshi1 = () => {
    let [number, setNumber] = useState(0);
    const alertNumber = () => {
        setTimeout(() => {
            // alert 只能获取到点击按钮时的那个状态
            message.success(number);
        }, 2000);
    }
    return (
        <PageContainer>
            <Card>
                <p>{number}</p>
                <Button onClick={() => setNumber(number + 1)}>+</Button>
                <Button onClick={alertNumber}>alertNumber</Button>
            </Card>
        </PageContainer>
    )
}
export default Ceshi1
```

### 1.2函数式更新

- **如果新的 state 需要通过使用先前的 state 计算得出，那么可以将回调函数当做参数传递给 setState。该回调函数将接收先前的 state，并返回一个更新后的值。**

```react
import { useState } from 'react'
import { PageContainer } from '@ant-design/pro-layout';
import { Card, Button, message } from 'antd';

const Ceshi1 = () => {
    let [number, setNumber] = useState(0);
    const lazy = () => {
        setTimeout(() => {
            // 这样每次执行时都会去获取一遍 state，而不是使用点击触发时的那个 state
            //setNumber(number+1);
            setNumber(number => number + 1);
        }, 3000);
    }

    return (
        <PageContainer>
            <Card>
                <p>{number}</p>
                <Button onClick={() => setNumber(number + 1)}>+</Button>
                <Button onClick={lazy}>lazy</Button>
            </Card>
        </PageContainer>
    )
}
export default Ceshi1
```

### 1.3惰性初始化 state

- **initialState 参数只会在组件的初始化渲染中起作用，后续渲染时会被忽略**
- **如果初始 state 需要通过复杂计算获得，则可以传入一个函数，在函数中计算并返回初始的 state，此函数只在初始渲染时被调用**

```
import { useState } from 'react'
import { PageContainer } from '@ant-design/pro-layout';
import { Card, Button, message } from 'antd';

const Ceshi1 = (props: any) => {
    let [counter, setCounter] = useState(getInitState);
    // 这个函数只在初始渲染时执行一次，后续更新状态重新渲染组件时，该函数就不会再被调用
    function getInitState() {
        const initNum = (4+8)/3
        return { number: initNum};
    }
    return (
        <PageContainer>
            <Card>
                <p>{counter.number}</p>
                <Button onClick={() => setCounter({ number: counter.number + 2})}>+</Button>
                <Button onClick={() => setCounter(counter)}>setCounter</Button>
            </Card>
        </PageContainer>
    )
}
export default Ceshi1
```

### 1.4性能优化

####  1.4.1Object.is（浅比较1）

- Hook 内部使用 Object.is 来比较新/旧 state 是否相等
- **与 class 组件中的 setState 方法不同，如果你修改状态的时候，传的状态值没有变化，则不重新渲染**
- **与 class 组件中的 setState 方法不同，useState 不会自动合并更新对象。你可以用函数式的 setState 结合展开运算符来达到合并更新对象的效果**

```react
function Counter(){
    const [counter,setCounter] = useState({name:'计数器',number:0});
    console.log('render Counter')
    // 如果你修改状态的时候，传的状态值没有变化，则不重新渲染
    return (
        <>
            <p>{counter.name}:{counter.number}</p>
            <button onClick={()=>setCounter({...counter,number:counter.number+1})}>+</button>
            <button onClick={()=>setCounter(counter)}>++</button>
        </>
    )
}
```

#### 1.4.2减少渲染次数

- **默认情况，只要父组件状态变了（不管子组件依不依赖该状态），子组件也会重新渲染**
- 一般的优化：
  1. **类组件**：可以使用 `pureComponent` ；
  2. **函数组件**：使用 `React.memo` ，将函数组件传递给 `memo` 之后，就会返回一个新的组件，新组件的功能：**如果接受到的属性不变，则不重新渲染函数**；
- 我理解的useMemo 的主要作用就是，通过一些变量计算得到新的值。通过把这些变量加入依赖 deps，当 deps 中的值均未发生变化时，跳过这次计算。useMemo 中传入的函数，将在 render 函数调用过程被同步调用。所以可以使用 useMemo 缓存一些相对耗时的计算。

```js
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

一个常用来做性能优化的 hook,看个栗子

```js
const MemoDemo = ({ count, color }) => {
   useEffect(() => {
       console.log('count effect')
   }, [count])
   const newCount = useMemo(() => {
       console.log('count 触发了')
       return Math.round(count)
   }, [count])
   const newColor = useMemo(() => {
       console.log('color 触发了')
       return color
   }, [color])
   return <div>
       <p>{count}</p>
       <p>{newCount}</p>
   {newColor}</div>
}
```

我们这个时候将传入的 count 值改变 的，log 执行循序

> count 触发了
>
> count effect

- 可以看出有点类似 effect, 监听 a、b 的值根据值是否变化来决定是否更新 UI
- memo 是在 DOM 更新前触发的，就像官方所说的，类比生命周期就是 shouldComponentUpdate
- 对比 **React.Memo** 默认是是基于 props 的浅对比，意思是对象只比较内存地址，只要你内存地址没变，管你对象里面的值千变万化都不会触发render。当然也可以开启第二个参数进行深对比。在最外层包装了整个组件，并且需要手动写一个方法比较那些具体的 props 不相同才进行 re-render。使用 **useMemo **可以精细化控制，进行局部 Pure



**useCallback**：useMemo 解决了值的缓存的问题，那么函数呢？useCallback接收一个内联回调函数参数和一个依赖项数组（子组件依赖父组件的状态，即子组件会使用到父组件的值） ，useCallback 会返回该回调函数的 memoized 版本，该回调函数仅在某个依赖项改变时才会更新

```js
const memoizedCallback = useCallback(
 () => {
   doSomething(a, b);
 },
 [a, b],
);
```

useCallback 的用法和上面 useMemo 差不多，是专门用来缓存函数的 hooks

```js
// 下面的情况可以保证组件重新渲染得到的方法都是同一个对象，避免在传给onClick的时候每次都传不同的函数引用
import React, { useState, useCallback } from 'react'

function MemoCount() {
   const [value, setValue] = useState(0)

   memoSetCount = useCallback(()=>{
       setValue(value + 1)
   },[])

   return (
       <div>
           <button
               onClick={memoSetCount}
               >
               Update Count
           </button>
           <div>{value}</div>
       </div>
   )
}
export default MemoCount
```

## useEffect

- **effect（副作用）：指那些没有发生在数据向视图转换过程中的逻辑，如 `ajax` 请求、访问原生`dom` 元素、本地持久化缓存、绑定/解绑事件、添加订阅、设置定时器、记录日志等。**
- useEffect 就是一个 Effect Hook，给函数组件增加了操作副作用的能力。它跟 class 组件中的 `componentDidMount`、`componentDidUpdate` 和 `componentWillUnmount` 具有相同的用途，只不过被合并成了一个 API
- **useEffect 接收一个函数，该函数会在组件渲染到屏幕之后才执行，该函数有要求：要么返回一个能清除副作用的函数，要么就不返回任何内容**
- 与 `componentDidMount` 或 `componentDidUpdate` 不同，使用 useEffect 调度的 effect 不会阻塞浏览器更新屏幕，这让你的应用看起来响应更快。大多数情况下，effect 不需要同步地执行。在个别情况下（例如测量布局），有单独的 useLayoutEffect Hook 可以使用，其 API 与 useEffect 相同。

```js
useEffect(effect, array)
```

effect 每次完成渲染之后触发, 配合 array 去模拟类的生命周期

- 如果不传，则每次 componentDidUpdate 时都会先触发 returnFunction（如果存在），再触发 effect
- [] 模拟 componentDidMount
- [id] 仅在 id 的值发生变化以后触发
- 清除 effect:组件卸载时处理一些内存问题，比如清除定时器、清除事件监听

```JS
useEffect(() => {
  ChatAPI.subscribeToFriendStatus(props.id, handleStatusChange);
  return () => {
    ChatAPI.unsubscribeFromFriendStatus(props.id, handleStatusChange);
  };
},[id]);
```

### useEffect知识点合集

1.只在第一次使用的componentDidMount，可以用来请求异步数据...、

> useEffect最后，加了[]就表示只第一次执行

```react
useEffect(()=>{
    const users = 获取全国人民的信息()
},[])

```

2.用来替代willUpdate等每次渲染都会执行的生命函数

> useEffect最后，不加[]就表示每一次渲染都执行

```react
useEffect(()=>{
    const users = 每次都获取全国人民的信息()
})

```

3.每次渲染都执行感觉有点费，所以：

> useEffect最后，加[]，并且[]里面加的字段就表示，这个字段更改了，我这个effect才执行

```react
useEffect(() => {
    const users = （name改变了我才获取全国人民的信息()）
},[name])

```

4.如果我想要分别name和age呢：

> 可以写多个useEffect

```react
useEffect(() => {
    const users = （name改变了我才获取全国人民的name信息()）
},[name])

useEffect(() => {
    const users = （name改变了我才获取全国人民的age信息()）
},[age])

```

5.如果我们之前订阅了什么，最后在willUnMount这个生命周期里面要取消订阅，这可咋用useEffect实现啊：

> 在effect的return里面可以做取消订阅的事

```react
useEffect(() => {
    const subscription = 订阅全国人民吃饭的情报！
    return () => {
        取消订阅全国人民吃饭的情报！
    }
},[])
```

为什么要取消订阅？

> 大家都知道，render了之后会执行重新useEffect，如果useEffect里面有一个每setInterval...那么每次render了，再次执行useEffect就会再创建一个setInterval，然后就混乱了...可以把下面案例return的内容删掉感受下

```react
useEffect(() => {
        console.log('use effect...',count)
        const timer = setInterval(() => setCount(count +1), 1000)
        return ()=> clearInterval(timer)
    })
```

6.useEffect的一些其他规则：

> 1.useEffect 里面使用到的state的值, 固定在了useEffect内部， 不会被改变，除非useEffect刷新，重新固定state的值

```react
 const [count, setCount] = useState(0)
    useEffect(() => {
        console.log('use effect...',count)
        const timer = setInterval(() => {
            console.log('timer...count:', count)
            setCount(count + 1)
        }, 1000)
        return ()=> clearInterval(timer)
    },[])
    //打印的一直是0
```

> 2.useEffect不能被判断包裹

```react
const [count, setCount] = useState(0)
if(2 < 5){
   useEffect(() => {
        console.log('use effect...',count)
        const timer = setInterval(() => setCount(count +1), 1000)
        return ()=> clearInterval(timer)
    }) 
}
```

具体原因跟到useEffect的生成执行规则有关系



## **useLayoutEffect**

- 跟 useEffect 使用差不多，通过同步执行状态更新可解决一些特性场景下的页面闪烁问题

- 主要是用在处理DOM的时候,当你的useEffect里面的操作需要处理DOM,并且会改变页面的样式,就需要用这个,否则可能会出现出现闪屏问题, useLayoutEffect里面的callback函数会在DOM更新完成后立即执行,但是会在浏览器进行任何绘制之前运行完成,阻塞了浏览器的绘制.

- 简单说下他们的不同：

  **useEffect 在全部渲染完毕后才会执行**

  **useLayoutEffect 会在 浏览器 layout 之后，painting 之前执行**

  其函数签名与 useEffect 相同，但它会在所有的 DOM 变更之后**同步**调用 effect

  **可以使用它来读取 DOM 布局并同步触发重渲染**

  在浏览器执行绘制之前 useLayoutEffect 内部的更新计划将被**同步**刷新

  **尽可能使用标准的 useEffect 以避免阻塞视图更新**
  
  

## **useRef**

- 类组件、React 元素用 React.createRef，函数组件使用 useRef
- useRef 返回一个可变的 ref 对象，其 `current` 属性被初始化为传入的参数（initialValue），它不仅仅可以用来管理 DOM ref 的，它还相当于 this , 可以存放任何变量，很好的解决闭包带来的不方便性。

```js
const refContainer = useRef(initialValue);
```

useRef 返回一个可变的 ref 对象，其 .current 属性被初始化为传入的参数（initialValue）。返回的 ref 对象在组件的整个生命周期内保持不变

解决引用问题--useRef 会在每次渲染时返回同一个 ref 对象（使用 React.createRef ，每次重新渲染组件都会重新创建 ref）

对比 createRef -- 在初始化阶段两个是没区别的，但是在更新阶段两者是有区别的。

我们知道,在一个局部函数中，函数每一次 update，都会在把函数的变量重新生成一次。 所以我们每更新一次组件, 就重新创建一次 ref， 这个时候继续使用 createRef 显然不合适，所以官方推出 **useRef**。useRef 创建的 ref 仿佛就像在函数外部定义的一个全局变量，不会随着组件的更新而重新创建。但组件销毁，它也会消失，不用手动进行销毁

前面提到的：

> useEffect 里面使用到的state的值, 固定在了useEffect内部， 不会被改变，除非useEffect刷新，重新固定state的值

```react
 const [count, setCount] = useState(0)
    useEffect(() => {
        console.log('use effect...',count)
        const timer = setInterval(() => {
            console.log('timer...count:', count)
            setCount(count + 1)
        }, 1000)
        return ()=> clearInterval(timer)
    },[])
    
```

`useEffect`里面的state的值，是固定的，这个是有办法解决的，就是用`useRef`，可以理解成`useRef`的一个作用：

> 就是相当于全局作用域，一处被修改，其他地方全更新...

### useRef知识点合集

1. 就是相当于全局作用域，一处被修改，其他地方全更新...

```react
 const [count, setCount] = useState(0)
 const countRef = useRef(0)
useEffect(() => {
    console.log('use effect...',count)
    const timer = setInterval(() => {
        console.log('timer...count:', countRef.current)
        setCount(++countRef.current)
    }, 1000)
    return ()=> clearInterval(timer)
},[])

```

2. 普遍操作，用来操作dom

> const btnRef = useRef(null)

> click me 

> 活学活用，记得取消绑定事件哦！  return ()=> btnRef.current.removeEventListener('click',onClick, false)

```react
const Hook =()=>{
    const [count, setCount] = useState(0)
    const btnRef = useRef(null)

    useEffect(() => {
        console.log('use effect...')
        const onClick = ()=>{
            setCount(count+1)
        }
        btnRef.current.addEventListener('click',onClick, false)
        return ()=> btnRef.current.removeEventListener('click',onClick, false)
    },[count])

    return(
        <div>
            <div>
                {count}
            </div>
            <button ref={btnRef}>click me </button>
        </div>
    )
}

```

**总结下就是 ceateRef 每次渲染都会返回一个新的引用，而 useRef 每次都会返回相同的引用**



## **useContext**

接收一个 context 对象（React.createContext 的返回值）并返回该 context 的当前值

当前的 context 值由上层组件中距离当前组件最近的 <MyContext.Provider> 的 value prop 决定

当组件上层最近的 <MyContext.Provider> 更新时，该 Hook 会触发重渲染，并使用最新传递给 MyContext provider 的 context value 值

**useContext(MyContext) 相当于 class 组件中的** `static contextType = MyContext` 或者 `<MyContext.Consumer>`

**useContext(MyContext) 只是让你能够读取 context 的值以及订阅 context 的变化。你仍然需要在上层组件树中使用 <MyContext.Provider> 来为下层组件提供 context**

```js
const context = useContext(Context)
```

**useContext 从名字上就可以看出，它是以 Hook 的方式使用 React Context, 简单来说就是一种生产者-消费者的模式**

```react
import React, { useContext, useState, useEffect } from "react";
const ThemeContext = React.createContext(null);
const Button = () => {
  const { color, setColor } = useContext(ThemeContext);
  useEffect(() => {
    console.info("Context changed:", color);
  }, [color]);
  const handleClick = () => {
    console.info("handleClick");
    setColor(color === "blue" ? "red" : "blue");
  };
  return (
    <button
      type="button"
      onClick={handleClick}
      style={{ backgroundColor: color, color: "white" }}
    >
      toggle color in Child
    </button>
  );
};
// app.js
const Ceshi2 = () => {
  const [color, setColor] = useState("blue");

  return (
    <ThemeContext.Provider value={{ color, setColor }}>
      <h3>
        Color in Parent: <span style={{ color: color }}>{color}</span>
      </h3>
      <Button />
    </ThemeContext.Provider>
  );
};

export default Ceshi2

```



## useReducer

- useReducer 和 redux 中 reducer 很像，是hooks的语法糖
- useState 内部就是靠 useReducer 来实现的
- useState 的替代方案，它接收一个形如 (state, action) => newState 的 reducer，并返回当前的 state 以及与其配套的 dispatch 方法
- 在某些场景下，useReducer 会比 useState 更适用，例如 state 逻辑较复杂且包含多个子值，或者下一个 state 依赖于之前的 state 等

```react
let initialState = 0;
// 如果你希望初始状态是一个{number:0}
// 可以在第三个参数中传递一个这样的函数 ()=>({number:initialState})
// 这个函数是一个惰性初始化函数，可以用来进行复杂的计算，然后返回最终的 initialState
const [state, dispatch] = useReducer(reducer, initialState, init);

const initialState = 0;
function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {number: state.number + 1};
    case 'decrement':
      return {number: state.number - 1};
    default:
      throw new Error();
  }
}
function init(initialState){
    return {number:initialState};
}
function Counter(){
    const [state, dispatch] = useReducer(reducer, initialState,init);
    return (
        <>
          Count: {state.number}
          <button onClick={() => dispatch({type: 'increment'})}>+</button>
          <button onClick={() => dispatch({type: 'decrement'})}>-</button>
        </>
    )
}
```

为什么使用useReducer?举个例子，有没有想过在某个组件里写了很多很多的useState是什么感受，比如下方的代码：

```react
const [name, setName] = useState(''); // 用户名 
const [pwd, setPwd] = useState(''); // 密码       
const [isLoading, setIsLoading] = useState(false); // 是否展示loading
const [error, setError] = useState(''); // 错误信息       
const [isLoggedIn, setIsLoggedIn] = useState(false); // 是否登录
```

### useState版login

我们先看看登录页常规的使用`useState`的实现方式：

```react
    function LoginPage() {
        const [name, setName] = useState(''); // 用户名
        const [pwd, setPwd] = useState(''); // 密码
        const [isLoading, setIsLoading] = useState(false); // 是否展示loading，发送请求中
        const [error, setError] = useState(''); // 错误信息
        const [isLoggedIn, setIsLoggedIn] = useState(false); // 是否登录

        const login = (event) => {
            event.preventDefault();
            setError('');
            setIsLoading(true);
            login({ name, pwd })
                .then(() => {
                    setIsLoggedIn(true);
                    setIsLoading(false);
                })
                .catch((error) => {
                    // 登录失败: 显示错误信息、清空输入框用户名、密码、清除loading标识
                    setError(error.message);
                    setName('');
                    setPwd('');
                    setIsLoading(false);
                });
        }
        return ( 
            //  返回页面JSX Element
        )
    }
```

上面Demo我们定义了5个state来描述页面的状态，在login函数中当登录成功、失败时进行了一系列复杂的state设置。可以想象随着需求越来越复杂更多的state加入到页面，更多的setState分散在各处，很容易设置错误或者遗漏，维护这样的老代码更是一个噩梦。

### useReducer版login

下面看看如何使用useReducer改造这段代码，先简单介绍下useReducer。

```react
    const [state, dispatch] = useReducer(reducer, initState);
```

[useReducer](https://link.juejin.cn?target=https%3A%2F%2Freactjs.org%2Fdocs%2Fhooks-reference.html%23usereducer)接收两个参数：

第一个参数：reducer函数，没错就是我们上一篇文章介绍的。第二个参数：初始化的state。返回值为最新的state和dispatch函数（用来触发reducer函数，计算对应的state）。按照官方的说法：对于复杂的state操作逻辑，嵌套的state的对象，推荐使用useReducer。

听起来比较抽象，我们先看一个简单的例子：

```react
    // 官方 useReducer Demo
    // 第一个参数：应用的初始化
    const initialState = {count: 0};

    // 第二个参数：state的reducer处理函数
    function reducer(state, action) {
        switch (action.type) {
            case 'increment':
              return {count: state.count + 1};
            case 'decrement':
               return {count: state.count - 1};
            default:
                throw new Error();
        }
    }

    function Counter() {
        // 返回值：最新的state和dispatch函数
        const [state, dispatch] = useReducer(reducer, initialState);
        return (
            <>
                // useReducer会根据dispatch的action，返回最终的state，并触发rerender
                Count: {state.count}
                // dispatch 用来接收一个 action参数「reducer中的action」，用来触发reducer函数，更新最新的状态
                <button onClick={() => dispatch({type: 'increment'})}>+</button>
                <button onClick={() => dispatch({type: 'decrement'})}>-</button>
            </>
        );
    }

```

了解了useReducer基本使用方法后，看看如何使用useReducer改造上面的login demo：

```react
    const initState = {
        name: '',
        pwd: '',
        isLoading: false,
        error: '',
        isLoggedIn: false,
    }
    function loginReducer(state, action) {
        switch(action.type) {
            case 'login':
                return {
                    ...state,
                    isLoading: true,
                    error: '',
                }
            case 'success':
                return {
                    ...state,
                    isLoggedIn: true,
                    isLoading: false,
                }
            case 'error':
                return {
                    ...state,
                    error: action.payload.error,
                    name: '',
                    pwd: '',
                    isLoading: false,
                }
            default: 
                return state;
        }
    }
    function LoginPage() {
        const [state, dispatch] = useReducer(loginReducer, initState);
        const { name, pwd, isLoading, error, isLoggedIn } = state;
        const login = (event) => {
            event.preventDefault();
            dispatch({ type: 'login' });
            login({ name, pwd })
                .then(() => {
                    dispatch({ type: 'success' });
                })
                .catch((error) => {
                    dispatch({
                        type: 'error'
                        payload: { error: error.message }
                    });
                });
        }
        return ( 
            //  返回页面JSX Element
        )
    }

```

乍一看useReducer改造后的代码反而更长了，但很明显第二版有更好的可读性，我们也能更清晰的了解state的变化逻辑。

可以看到login函数现在更清晰的表达了用户的意图，开始登录login、登录success、登录error。LoginPage不需要关心如何处理这几种行为，那是loginReducer需要关心的，表现和业务分离。

另一个好处是所有的state处理都集中到了一起，使得我们对state的变化更有掌控力，同时也更容易复用state逻辑变化代码，比如在其他函数中也需要触发登录error状态，只需要**dispatch({ type: 'error' })**。

useReducer可以让我们将`what`和`how`分开。比如点击了登录按钮，我们要做的就是发起登陆操作`dispatch({ type: 'login' })`，点击退出按钮就发起退出操作`dispatch({ type: 'logout' })`，所有和`how`相关的代码都在reducer中维护，组件中只需要思考`What`，让我们的代码可以像用户的行为一样，更加清晰。



## 自定义 Hook

- 自定义 Hook 更像是一种约定，而不是一种功能。如果函数的名字以 use 开头，并且调用了其他的 Hook，则就称其为一个自定义 Hook，一般我将 hooks 分为这几类

### util

顾名思义工具类，比如 useDebounce、useInterval、useWindowSize 等等。例如下面 useWindowSize

```js
import React, { useState, useCallback, useEffect } from "react";

export const useWinSize = () => {
  // 1. 使用useState初始化窗口大小state
  const [size, setSize] = useState({
    width: document.documentElement.clientWidth,
    height: document.documentElement.clientHeight
  });

  const changeSize = useCallback(() => {
    // useCallback 将函数缓存起来 节流
    setSize({
      width: document.documentElement.clientWidth,
      height: document.documentElement.clientHeight
    });
  }, []);
  // 2. 使用useEffect在组件创建时监听resize事件，resize时重新设置state (使用useCallback节流)
  useEffect(() => {
    //绑定一次页面监听事件 组件销毁时解绑
    window.addEventListener("resize", changeSize);
    return () => {
      window.removeEventListener("resize", changeSize);
    };
  }, []);

  return size;
};
```

在组件内使用该自定义hook：

```arduino
import React from "react";
import { useWinSize } from "../hooks";

export default () => {
  const size = useWinSize();

  return (
    <div>
      页面大小: `{size.width}*{size.height}`
    </div>
  );
};
```

### API

像之前的我们有一个公用的城市列表接口，在用 redux 的时候可以放在全局公用，不用的话我们就可能需要复制粘贴了。有了 hooks 以后我们只需要 use 一下就可以在其他地方复用了

```js
import { useState, useEffect } from 'react';
import { getCityList } from '@/services/static';
const useCityList = (params) => {
   const [cityList, setList] = useState([]);
   const [loading, setLoading] = useState(true)
   const getList = async () => {
       const { success, data } = await getCityList(params);
       if (success) setList(data);
       setLoading(false)
   };
   useEffect(
       () => {getList();},
       [],
   );
   return {
       cityList,
       loading
   };
};
export default useCityList;
// bjs
function App() {
   // ...
   const { cityList, loading } = useCityList()
   // ...
}
```

### logic

逻辑类，比如我们有一个点击用户头像关注用户或者取消关注的逻辑，可能在评论列表、用户列表都会用到，我们可以这样做

```js
import { useState, useEffect } from 'react';
import { followUser } from '@/services/user';
const useFollow = ({ accountId, isFollowing }) => {
    const [isFollow, setFollow] = useState(false);
    const [operationLoading, setLoading] = useState(false)
    const toggleSection = async () => {
        setLoading(true)
        const { success } = await followUser({ accountId });
        if (success) {
            setFollow(!isFollow);
        }
        setLoading(false)
    };
    useEffect(
        () => {
            setFollow(isFollowing);
        },
        [isFollowing],
    );
    return {
        isFollow,
        toggleSection,
        operationLoading
    };
};
export default useFollow;
```



## 需要注意的一些点：

## 1.不要过度依赖 useMemo

- `useMemo` 本身也有开销。`useMemo` 会「记住」一些值，同时在后续 render 时，将依赖数组中的值取出来和上一次记录的值进行比较，如果不相等才会重新执行回调函数，否则直接返回「记住」的值。这个过程本身就会消耗一定的内存和计算资源。因此，过度使用 `useMemo` 可能会影响程序的性能。
- 在使用 useMemo 前，应该先思考三个问题：
  - **传递给 `useMemo` 的函数开销大不大？** 有些计算开销很大，我们就需要「记住」它的返回值，避免每次 render 都去重新计算。如果你执行的操作开销不大，那么就不需要记住返回值。否则，使用 `useMemo` 本身的开销就可能超过重新计算这个值的开销。因此，对于一些简单的 JS 运算来说，我们不需要使用 `useMemo` 来「记住」它的返回值。
  - **返回的值是原始值吗？** 如果计算出来的是**基本类型**的值（`string`、 `boolean` 、`null`、`undefined` 、`number`、`symbol`），那么每次比较都是相等的，下游组件就不会重新渲染；如果计算出来的是**复杂类型**的值（`object`、`array`），哪怕值不变，但是地址会发生变化，导致下游组件重新渲染。所以我们也需要「记住」这个值。
  - **在编写自定义 Hook 时，返回值一定要保持引用的一致性。** 因为你无法确定外部要如何使用它的返回值。如果返回值被用做其他 Hook 的依赖，并且每次 re-render 时引用不一致（当值相等的情况），就可能会产生 bug。所以如果自定义 Hook 中暴露出来的值是 object、array、函数等，都应该使用 `useMemo` 。以确保当值相同时，引用不发生变化。

## 2.useEffect 不能接收 async 作为回调函数

useEffect 接收的函数，要么返回一个能清除副作用的函数，要么就不返回任何内容。而 async 返回的是 promise。

```react
    useEffect(() => {
        // 更优雅的方式    
        const fetchData = async () => {
            const result = await axios(
                'https://hn.algolia.com/api/v1/search?query=redux');
            setData(result.data);
        };
        fetchData();
    }, []);
```

## 3.hooks想在赋值某个值后迅速拿到该值

想在setsState某个值后迅速拿到该值或者console.log打印该值，之前的类式组件的做法是：

```react
this.setState({ count: this.state.count + 1 }, () => { console.log(this.state.count) }) 
```

就像这样，我们就能打印出更新后的count 

不过在hooks里，我们可能需要借助useEffect来实现，比如：useEffect(()=>{ 当number改变时，这里需要做的事情 },[number]);

## 4.useEffect 依赖`[]`时的闭包问题（闭包陷阱）

React 的闭包陷阱指的是当 useEffect 的依赖为`[]`的时候，当 React 函数式组件重新执行，useEffect hooks 并不会重新执行，这时候它内部的变量依旧是上一次的变量，这就构成了“闭包陷阱” ：

```js
import {useState, useEffect} from 'react'

export default function App() {
  const [count, setCount] = useState(0)

  // 模拟 DidMount
  useEffect(() => {
      // 定时任务
      // useEffect 中的 count 永远是第一次的 count
      const timer = setInterval(() => {
          console.log('setInterval...', count)
      }, 1000)

      // 清除定时任务
      return () => clearTimeout(timer)
  }, []) // 依赖为 []，re-render 不会重新执行 effect 函数

  return (<>
      <div>count: {count}</div>
      <div><button onClick={() => setCount(count+1)}>+1</button></div>
    </>)
}
```

![img](https://pic2.zhimg.com/80/v2-098624505d843a674d7379c3357207a5_1440w.jpg)

我们点击`+1` 按钮数次后，页面渲染已经成为 4，而控制台打印的依旧是 0。这就是闭包陷阱。

只有 useEffect 中才会存在闭包陷阱，因为我们重新执行函数式组件的时候，useEffect 不会被重新执行，这时它依赖的就是上一次的变量。

- **注意事项三：useEffect 依赖了「引用类型」可能会出现死循环（React 是通过`Object.is`来判断依赖是否改变的）**
- **注意事项四：使用 useCallback 缓存函数的时候，如果函数内部使用了外部的变量，则需要把这个变量加进依赖中，否则函数不会检测到该变量的改变。**

#### 如何解决 React Hooks 中的闭包陷阱？

主要有三种方式：

- 使用回调函数赋值
- 使用 useRef
- 使用 useReducer

**使用回调函数**

当我们使用 setState 时，新的 state 如果是通过计算旧的 state 得出，那么我们可以将回调函数当作参数传递给 setState。该回调函数将接收先前的 state，并返回一个更新后的值：

```js
import React, { useState, useEffect, useRef } from 'react'

function App() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    const timer = setInterval(() => {
      setCount((c) => c + 1)
    }, 1000);
    return () => clearInterval(timer);
  }, []);
  return <div>{count}</div>;
}

export default App
```

**使用 useRef**

同一个 ref 在 React 组件的整个生命周期中只存在一个引用，因此通过 current 永远是可以访问到引用中最新的函数值，不会存在闭包陈旧值的问题。

```js
import React, { useState, useEffect, useRef } from 'react'

function App() {
    const [count, setCount] = useState(0)

    const countRef = useRef(0)
    // 模拟 DidMount
    useEffect(() => {
        // 定时任务
        const timer = setInterval(() => {
            console.log('setInterval...', countRef.current)
            setCount(countRef.current++)
        }, 1000)

        // 清除定时任务
        return () => clearTimeout(timer)
    }, []) // 依赖为 []，re-render 不会重新执行 effect 函数

    return <div>count: {count}</div>
}

export default App
```

![img](https://pic2.zhimg.com/80/v2-151e49a0077dfd178f0ac3fa50d0133d_1440w.jpg)

**使用 useReducer**

利用 useReducer 获取的`dispatch`方法在组件的生命周期中保持唯一引用，并且总是能操作到最新的值：

```js
import {useEffect, useReducer} from 'react'

const initialCount = 0;

function reducer(count, action) {
  switch (action.type) {
    case 'increment':
      return count + 1
    case 'decrement':
      return count - 1
    default:
      throw new Error();
  }
}

function App() {
  const [count, dispatch] = useReducer(reducer, initialCount);
  useEffect(()=>{
    setInterval(() => {
      dispatch({type:'increment'})
    }, 1000);
  },[])

  return <div>Count: {count}</div>
}

export default App
```
