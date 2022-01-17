## useMemo

### 为啥使用useMemo?

从 **useEffect** 可以知道，可以通过向其传递一些参数来影响某些函数的执行。 React 检查这些参数是否已更改，并且只有在存在差异的情况下才会执行此。

**useMemo** 做类似的事情，假设有大量方法，并且只想在其参数更改时运行它们，而不是每次组件更新时都运行它们，那就可以使用 **useMemo** 来进行性能优化。

> 记住，传入 `useMemo` 的函数会在**渲染期间执行**。请不要在这个函数内部执行与渲染无关的操作，诸如副作用这类的操作属于 `useEffect` 的适用范畴，而不是 `useMemo` 。

### 怎么使用useMemo?

```js
function changeName(name) {
  return name + '给name做点操作返回新name'
}

const newName = useMemo(() => {
	return changeName(name)
}, [name])

```

### 场景举例

##### 1.常规使用，避免重复执行没必要的方法：

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

##### 2.useEffect 和 useMemo 区别

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



##### 3.useMemo和React.memo的区别

React.memo

- 包裹`react组件`，来自父组件的props没有发生改变的话，就不会渲染子组件
- 第二个参数，可以传入一个判断方`isEqual`，可以拿到`preProps`和`props`做比较，返回布尔值，决定是否更新渲染组件

useMemo

- `useMemo`可以用于处理 **颗粒度更细** 的情况，对于组件内的某一部分进行缓存，只有第二个参数更新，才会执行回调，**得到最新的变量/组件，否则不变**。
- `useCallback`的原理也是一样的，区别就是，它是为了避免函数重复定义，一种**对函数的缓存**



##### 4.适当使用useMemo（简单类型计算时并不划算）

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

### 5.1 useCallback 大部分场景没有提升性能

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

### 5.2 useCallback 让代码可读性变差

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



### 5.3 useCallback 和 useMemo 区别

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



## 结合useMemo，React.memo和useCallback的知识，来看一个具体的例子：

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

