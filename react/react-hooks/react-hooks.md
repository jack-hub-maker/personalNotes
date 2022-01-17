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


# 关于React hooks：

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

## useState 

- **React 假设当你多次调用 useState 的时候，你能保证每次渲染时它们的\**\*\*调用顺序\*\**\*是不变的。**
- 通过在函数组件里调用它来给组件添加一些内部 state，React会 **在重复渲染时保留这个 state**
- useState 唯一的参数就是初始 state
- useState 会返回一个数组(从数组里解构)，一个 state，一个更新 state 的函数
  - 在初始化渲染期间，返回的状态 (state) 与传入的第一个参数 (initialState) 值相同
  - 你可以在事件处理函数中或其他一些地方调用这个函数。它类似 class 组件的 this.setState，但是它**不会把新的 state 和旧的 state 进行合并，而是直接替换**

```js
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

```js
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

### 1.4 useState使用Object.is来替换state

- **Hook 内部使用 Object.is 来比较新/旧 state 是否相等**
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



### 1.5 能用其他状态计算出来就不用单独声明状态

**一个 state 必须不能通过其它 state/props 直接计算出来，否则就不用定义 state。**

```js
const SomeComponent = (props) => {
  
  const [source, setSource] = useState([
      {type: 'done', value: 1},
      {type: 'doing', value: 2},
  ])
  
  const [doneSource, setDoneSource] = useState([])
  const [doingSource, setDoingSource] = useState([])

  useEffect(() => {
    setDoingSource(source.filter(item => item.type === 'doing'))
    setDoneSource(source.filter(item => item.type === 'done'))
  }, [source])
  
  return (
    <div>
       ..... 
    </div>
  )
}
```

上面的示例中，变量 `doneSource` 和 `doingSource` 可以通过变量 `source` 计算出来，那就不要定义 `doneSource` 和 `doingSource` 了！

```js
const SomeComponent = (props) => {
  
  const [source, setSource] = useState([
      {type: 'done', value: 1},
      {type: 'doing', value: 2},
    ])
  
  const doneSource = useMemo(()=> source.filter(item => item.type === 'done'), [source]);
  const doingSource = useMemo(()=> source.filter(item => item.type === 'doing'), [source]);
  
  return (
    <div>
       ..... 
    </div>
  )
}
```

一般在项目中此类问题都比较隐晦，层层传递，在 Code Review 中很难一眼看出。如果能把变量定义清楚，那事情就成功了一半。

### 1.6 保证数据源唯一

**在项目中同一个数据，保证只存储在一个地方。**

不要既存在 redux 中，又在组件中定义了一个 state 存储。

不要既存在父级组件中，又在当前组件中定义了一个 state 存储。

不要既存在 url query 中，又在组件中定义了一个 state 存储。

```js
function SearchBox({ data }) {
  const [searchKey, setSearchKey] = useState(getQuery('key'));

  const handleSearchChange = e => {
    const key = e.target.value;
    setSearchKey(key);
    history.push(`/movie-list?key=${key}`);
  }

  return (
      <input
        value={searchKey}
        placeholder="Search..."
        onChange={handleSearchChange}
      />
  );
}
```

在上面的示例中，`searchKey` 存储在两个地方，既在 url query 上，又定义了一个 state。完全可以优化成下面这样：

```js
function SearchBox({ data }) {
  const searchKey = parse(localtion.search)?.key;

  const handleSearchChange = e => {
    const key = e.target.value;
    history.push(`/movie-list?key=${key}`);
  }

  return (
      <input
        value={searchKey}
        placeholder="Search..."
        onChange={handleSearchChange}
      />
  );
}
```

在实际项目开发中，此类问题也是比较隐晦，编码时应注意。

### 1.7 useState 适当合并

项目中有木有写过这样的代码：

```js
const [firstName, setFirstName] = useState();
const [lastName, setLastName] = useState();
const [school, setSchool] = useState();
const [age, setAge] = useState();
const [address, setAddress] = useState();

const [weather, setWeather] = useState();
const [room, setRoom] = useState();
```

反正我最开始是写过，useState 拆分过细，导致代码中一大片 useState。

我建议，同样含义的变量可以合并成一个 state，代码可读性会提升很多：

```js
const [userInfo, setUserInfo] = useState({
  firstName,
  lastName,
  school,
  age,
  address
});

const [weather, setWeather] = useState();
const [room, setRoom] = useState();
```

当然这种方式我们在变更变量时，一定不要忘记带上老的字段，比如我们只想修改 `firstName`：

```js
setUserInfo(s=> ({
  ...s,
  fristName,
}))
```

其实如果是 React Class 组件，state 是会自动合并的：

```js
this.setState({
  firstName
})
```

在 Hooks 中，可以有这种用法吗？其实是可以的，我们自己封装一个 Hooks 就可以，比如 ahooks 的 [useSetState](https://link.zhihu.com/?target=https%3A//ahooks.js.org/zh-CN/hooks/use-set-state)，就封装了类似的逻辑：

```js
const [userInfo, setUserInfo] = useSetState({
  firstName,
  lastName,
  school,
  age,
  address
});

// 自动合并
setUserInfo({
  firstName
})
```



## useEffect

- **effect（副作用）：指那些没有发生在数据向视图转换过程中的逻辑，如 `ajax` 请求、访问原生`dom` 元素、本地持久化缓存、绑定/解绑事件、添加订阅、设置定时器、记录日志等。**
- useEffect 就是一个 Effect Hook，给函数组件增加了操作副作用的能力。它就是 class 组件中的 `componentDidMount`、`componentDidUpdate` 和 `componentWillUnmount` 三个的组合API
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
    //打印的一直是0
```



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

  #### 为啥使用useRef?

  它不仅仅是用来管理 DOM ref 的，它还相当于 this , 可以存放任何变量，很好的解决闭包带来的不方便性。

  #### 怎么使用useRef?

  ```js
  const [count, setCount] = useState<number>(0)
  const countRef = useRef<number>(count)
  ```

useRef 返回一个可变的 ref 对象，其 .current 属性被初始化为传入的参数（initialValue）。返回的 ref 对象在组件的整个生命周期内保持不变

解决引用问题--useRef 会在每次渲染时返回同一个 ref 对象（使用 React.createRef ，每次重新渲染组件都会重新创建 ref）

对比 createRef -- 在初始化阶段两个是没区别的，但是在更新阶段两者是有区别的。

我们知道,在一个局部函数中，函数每一次 update，都会在把函数的变量重新生成一次。 所以我们每更新一次组件, 就重新创建一次 ref， 这个时候继续使用 createRef 显然不合适，所以官方推出 **useRef**。useRef 创建的 ref 仿佛就像在函数外部定义的一个全局变量，不会随着组件的更新而重新创建。但组件销毁，它也会消失，不用手动进行销毁

### 4.1解决闭包问题

它不仅仅是用来管理 DOM ref 的，它还相当于 this , 可以存放任何变量，很好的解决闭包带来的不方便性。



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

## 

### 4.2因为变更`.current` 属性不会引发组件重新渲染，根据这个特性可以获取状态的前一个值：

```js
const Counter = () => {
  const [count, setCount] = useState<number>(0)
  const preCountRef = useRef<number>(count)

  useEffect(() => {
    preCountRef.current = count
  })

  return (
    <div>
      <p>pre count: { preCountRef.current }</p>
      <p>current count: { count }</p>
      <button onClick={() => setCount(count + 1)}>加</button>
    </div>
  )
}
```

从结果可以看出，显示的总是状态的前一个值：

```
pre count: 4

current count: 5
```



## useMemo

### 5.1 为啥使用useMemo?

从 **useEffect** 可以知道，可以通过向其传递一些参数来影响某些函数的执行。 React 检查这些参数是否已更改，并且只有在存在差异的情况下才会执行此。

**useMemo** 做类似的事情，假设有大量方法，并且只想在其参数更改时运行它们，而不是每次组件更新时都运行它们，那就可以使用 **useMemo** 来进行性能优化。

> 记住，传入 `useMemo` 的函数会在**渲染期间执行**。请不要在这个函数内部执行与渲染无关的操作，诸如副作用这类的操作属于 `useEffect` 的适用范畴，而不是 `useMemo` 。

### 5.2 怎么使用useMemo?

```js
function changeName(name) {
  return name + '给name做点操作返回新name'
}

const newName = useMemo(() => {
	return changeName(name)
}, [name])

```

### 5.3 场景举例

##### 5.3.1.常规使用，避免重复执行没必要的方法：

我们先来看一个很简单的例子，以下是还未使用 `useMemo` 的代码：

```js
import React, { useState, useMemo } from 'react'

// 父组件
const Example = () => {
  const [time, setTime] = useState<number>(0)
  const [random, setRandom] = useState<number>(0)

  return (
    <div>
      <button onClick={() => setTime(new Date().getTime())}>获取当前时间</button>
      <button onClick={() => setRandom(Math.random())}>获取当前随机数</button>
      <Show time={time}>{random}</Show>
    </div>
  )
}

type Data = {
  time: number
}

// 子组件
const Show:React.FC<Data> = ({ time, children }) => {
  function changeTime(time: number): string {
    console.log('changeTime excuted...')
    return new Date(time).toISOString()
  }

  return (
    <div>
      <p>Time is: { changeTime(time) }</p>
      <p>Random is: { children }</p>
    </div>
  )
}

export default Example

```



在这个例子中，无论你点击的是 **获取当前时间** 按钮还是 **获取当前随机数** 按钮， `<Show />` 这个组件中的方法 `changeTime` 都会执行。

但事实上，点击 **获取当前随机数** 按钮改变的只会是 `children` 这个参数，但我们的 `changeTime` 也会因为子组件的重新渲染而重新执行，这个操作是很没必要的，消耗了无关的性能。

使用 `useMemo` 改造我们的 `<Show />` 子组件：

```js
const Show:React.FC<Data> = ({ time, children }) => {
  function changeTime(time: number): string {
    console.log('changeTime excuted...')
    return new Date(time).toISOString()
  }

  const newTime: string = useMemo(() => {
    return changeTime(time)
  }, [time])

  return (
    <div>
      <p>Time is: { newTime }</p>
      <p>Random is: { children }</p>
    </div>
  )
}

```

这个时候只有点击 **获取当前时间** 才会执行 `changeTime` 这个函数，而点击 **获取当前随机数** 已经不会触发该函数执行了。

##### 5.3.2.useEffect 和 useMemo 区别

- useEffect是在`DOM改变之后触发`，useMemo在`DOM渲染之前就触发`
- useMemo是在`DOM更新前触发的`，就像官方所说的，类比生命周期就是shouldComponentUpdate
- useEffect可以帮助我们在`DOM更新完成后执行某些副作用操作`，如数据获取，设置订阅以及手动更改 React 组件中的 DOM 等
- 不要在这个useMemo函数内部`执行与渲染无关的操作`，诸如`副作用这类的操作属于 useEffect 的适用范畴`，而不是 useMemo
- 在useMemo中使用`setState你会发现会产生死循环`，并且会有警告，因为useMemo是`在渲染中进行的`，你在其中操作`DOM`后，又会导致触发`memo`

你可能会好奇， `useMemo` 能做的难道不能用 `useEffect` 来做吗？答案是否定的！如果你在子组件中加入以下代码：

```js
const Show:React.FC<Data> = ({ time, children }) => {
	//...
  
  useEffect(() => {
    console.log('effect function here...')
  }, [time])

  const newTime: string = useMemo(() => {
    return changeTime(time)
  }, [time])
	//...
}

```

你会发现，控制台会打印如下信息：

```js
> changeTime excuted...
> effect function here...

```

正如我们一开始说的：传入 `useMemo` 的函数会在**渲染期间执行**。 在此不得不提 `React.memo` ，它的作用是实现整个组件的 `Pure` 功能：

```
const Show:React.FC<Data> = React.memo(({ time, children }) => {...}
```

所以简单用一句话来概括 `useMemo` 和 `React.memo` 的区别就是：前者在某些情况下不希望组件对所有 `props` 做浅比较，只想实现局部 `Pure` 功能，即只想对特定的 `props` 做比较，并决定是否局部更新。



##### 5.3.3.useMemo和React.memo的区别

React.memo

- 包裹`react组件`，来自父组件的props没有发生改变的话，就不会渲染子组件
- 第二个参数，可以传入一个判断方`isEqual`，可以拿到`preProps`和`props`做比较，返回布尔值，决定是否更新渲染组件

useMemo

- `useMemo`可以用于处理 **颗粒度更细** 的情况，对于组件内的某一部分进行缓存，只有第二个参数更新，才会执行回调，**得到最新的变量/组件，否则不变**。
- `useCallback`的原理也是一样的，区别就是，它是为了避免函数重复定义，一种**对函数的缓存**



##### 5.3.4.适当使用useMemo（简单类型计算时并不划算）

在我们处理很简单的基础类型计算时，可能 useMemo 并不划算。

```js
const a = 1;
const b = 2;

const c = useMemo(()=> a + b, [a, b]);
```

比如上面的例子，请问计算 `a+b` 的消耗大？还是记录 `a/b` ，并比较`a/b` 是否变化的消耗大？

明显 `a+b` 消耗更小。

```js
const a = 1;
const b = 2;
const c = a + b;
```

这笔账大家可以自己算，所以如果是简单的基础类型计算，就不要用 useMemo 了



## useCallback

### 6.1 useCallback 大部分场景没有提升性能

useCallback 可以记住函数，避免函数重复生成，这样函数在传递给子组件时，可以避免子组件重复渲染，提高性能。

```js
const someFunc = useCallback(()=> {
   doSomething();
}, []);

return <ExpensiveComponent func={someFunc} />
```

基于以上认知，我一开始的时候，只要是个函数，都加个 useCallback。

但我们要注意，提高性能还必须有另外一个条件，子组件必须使用了 `shouldComponentUpdate` 或者 `React.memo` 来忽略同样的参数重复渲染。

假如 `ExpensiveComponent` 组件只是一个普通组件，是没有任何用的。比如下面这样：

```js
const ExpensiveComponent = ({ func }) => {
  return (
    <div onClick={func}>
        hello
    </div>
  )
}
```

必须通过 `React.memo` 包裹 `ExpensiveComponent` ，才会避免参数不变的情况下的重复渲染，提高性能。

```js
const ExpensiveComponent = React.memo(({ func }) => {
  return (
    <div onClick={func}>
        hello
    </div>
  )
})
```

**所以，useCallback 是要和 `shouldComponentUpdate/React.memo` 配套使用的。当然，我建议一般项目中不用考虑性能优化的问题，也就是不要使用 useCallback 了，除非有个别非常复杂的组件，单独使用即可。**

### 6.2 useCallback 让代码可读性变差

我看到过一些代码，使用 useCallback 后，大概长这样：

```js
const someFuncA = useCallback((d, g, x, y)=> {
   doSomething(a, b, c, d, g, x, y);
}, [a, b, c]);

const someFuncB = useCallback(()=> {
   someFuncA(d, g, x, y);
}, [someFuncA, d, g, x, y]);

useEffect(()=>{
  someFuncB();
}, [someFuncB]);
```

在上面的代码中，变量依赖一层一层传递，最终要判断具体哪些变量变化会触发 useEffect 执行，是一件很头疼的事情。

我期望不要用 useCallback，直接裸写函数就好：

```js
const someFuncA = (d, g, x, y)=> {
   doSomething(a, b, c, d, g, x, y);
};

const someFuncB = ()=> {
   someFuncA(d, g, x, y);
};

useEffect(()=>{
  someFuncB();
}, [...]);
```



### 6.3 useCallback 和 useMemo 区别

- `useMemo` 和 `useCallback` 接收的参数都是一样，都是在其依赖项发生变化后才执行，都是返回缓存的值，区别在于 `useMemo` 返回的是函数运行的结果， `useCallback` 返回的是函数。

```js
const renderButton = useCallback(
     () => (
         <Button type="link">
            {buttonText}
         </Button>
     ),
     [buttonText]    // 当buttonText改变时才重新渲染renderButton
);

```

- `useMemo返回的的是一个值`，用于避免在每次渲染时都进行高开销的计算

```js
const result = useMemo(() => {
    for (let i = 0; i < 100000; i++) {
      (num * Math.pow(2, 15)) / 9;
    }
}, [num]);
```



**个人建议：没事尽量别用 useCallback，在用useCallback时要注意useCallback可以在一些计算操作的时候使用useMemo来进行优化。**



### 6.4结合useMemo，React.memo和useCallback的知识，来看一个具体的例子：

- 父组件将一个 `【值】 传递给子组件`，若父组件的其他值发生变化时，子组件也会跟着渲染多次，会造成性能浪费； useMemo是将父组件传递给`子组件的值缓存起来`，只有当 useMemo中的第二个参数状态变化时，子组件才重新渲染
- useMemo便是用于`缓存该函数的执行结果`，仅当依赖项改变后才会重新计算

```js
修改后如下，注意useMemo缓存的是函数执行的结果, 只有当[count, price]改变时才会重走一遍

const Parent = () => {
    const [count, setCount] = useState(0);
    const [color,setColor] = useState("");
    const [price,setPrice] = useState(10);
    const handleClick = () => {
        setCount(count + 1);
    }
    const getTotal = useMemo(()=>{
        console.log("getTotal exec ...") 
        return count * price
    },[count, price])
    
    return (<div>
        <div> <input onChange={(e) => setColor(e.target.value)}/></div>
        <div> <input value={price}  onChange={(e) => setPrice(Number(e.target.value))}/></div>
        <div> {count} <button onClick={handleClick}>+1</button></div>
        <div> {getTotal}</div>
    </div>)
}

```

> memo

- 父组件name属性或text属性变化都会导致Parent函数重新执行，所以即使传入子组件props没有任何变化，甚至子组件没有依赖于任何props属性，`都会导致子组件重新渲染`
- 使用memo包裹子组件时，`只有props发生改变子组件才会重新渲染`,以提升一定的性能

```js
const Child = memo((props: any) => {
    console.log("子组件更新..."); // 只有当props属性改变，name属性改变时，子组件才会重新渲染
    return (
        <div>
            <h3>子组件</h3>
            <div>text:{props.name}</div>
            <div>{new Date().getTime()}</div>
        </div>
    )
})

const Parent = () => {
    const [text, setText] = useState("")
    …… ……
    <Child name ={text}/>
}

```

- 使用memoAPI来缓存组件

```js
import React, { memo } from "react"
const CacheComponent = memo(() => {
  return <div> ^^ </div>  
})

```

> useCallback

- 父组件将一个`【方法】传递给子组件`，若父组件的其他状态发生变化时，子组件也会跟着`渲染多次`，会造成性能浪费； usecallback是将父组件传给`子组件的方法给缓存下来`，只有当 usecallback中的第二个参数状态变化时，子组件才`重新渲染`
- 但如果传入的props`包含函数`，父组件每次重新渲染`都是创建新的函数`，所以传递函数子组件还是会`重新渲染`，即使函数的内容还是一样，我们希望把`函数也缓存起来`，于是引入useCallback

```js
const Child = memo((props: any) => {
    console.log("子组件更新..."); // 父级Parent函数有东西变化，Child重新执行，handleInputChange已经指向新的函数实例，所以子组件依然会刷新
    return (
        <div>
            <div>text:{props.name}</div>
            <div> <input onChange={props.handleInputChange} /></div>
        </div>
    )
})
const Parent = () => {
    const [text, setText] = useState("")
    const [count, setCount] = useState(0)
    const handleInputChange =useCallback((e) => {
         setText(e.target.value )
    },[]) 
    return (<div>
            …… ……
        <Child name={text} handleInputChange={handleInputChange}/>
    </div>)
}

```

- useCallback用用于缓存函数，只有当依赖项改变时，函数才会重新执行返回新的函数对于父组件中的函数作为props传递给子组件时，只要父组件数据改变，函数重新执行，`作为props的函数也会产生新的实例`，导致子组件的刷新使用useCallback可以缓存函数。`需要搭配memo使用`

```js
1 - 在Parent组件里 handleInputChange用useCallback 包裹起来
2 - 当前 handleInputChange 不依赖与任何项，所以handleInputChange只在初始化的时候调用一次函数就被缓存起来

const handleInputChange =useCallback((e) => {
     setText(e.target.value )
},[]) 

3 - 将count加入到依赖项，count变化后重新生成新的函数，改变函数内部的count值
const handleInputChange =useCallback((e) => {
     setText(e.target.value )
},[count]) 
```



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

```js
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

比如有一个公用的城市列表接口，在用 redux 的时候可以放在全局公用，不用的话我们就可能需要复制粘贴了。有了 hooks 以后我们只需要 use 一下就可以在其他地方复用了

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



## 结语

首先作为一名完全拥抱React Hooks 用户，我非常认可 React Hooks 带来的优化。但是不得不说，Hooks很多时候也是一把双刃剑，在使用时还有不少需要注意的地方。

注：以上对React Hooks的理解仅为个人看法，如有什么错误的地方，欢迎指出。

