<!--
 * @Descripttion: 
 * @version: 1.0
 * @Author: 
 * @Date: 2021-12-31 16:40:57
 * @LastEditors: YingJie Xing
 * @LastEditTime: 2021-12-31 16:44:49
 * @FilePath: /personalNotes/react/react-hooks/useCallback.md
 * Copyright 2021 YingJie Xing, All Rights Reserved. 
-->
### useCallback 使用场景

先看一个最简单的例子：



```jsx
// 用于记录 getData 调用次数
let count = 0;

function App() {
  const [val, setVal] = useState("");

  function getData() {
    setTimeout(()=>{
      setVal('new data '+count);
      count++;
    }, 500)
  }

  useEffect(()=>{
    getData();
  }, []);

  return (
    <div>{val}</div>
  );
}
```

`getData`模拟发起网络请求。在这种场景下，没有`useCallback`什么事，组件本身是高内聚的。

如果涉及到组件通讯，情况就不一样了：



```jsx
// 用于记录 getData 调用次数
let count = 0;

function App() {
  const [val, setVal] = useState("");

  function getData() {
    setTimeout(() => {
      setVal("new data " + count);
      count++;
    }, 500);
  }

  return <Child val={val} getData={getData} />;
}

function Child({val, getData}) {
  useEffect(() => {
    getData();
  }, [getData]);

  return <div>{val}</div>;
}
```

就这么轻轻松松，一个死循环就诞生了...

先来分析下这段代码的用意，`Child`组件是一个纯展示型组件，其业务逻辑都是通过外部传进来的，这种场景在实际开发中很常见。

再分析下代码的执行过程：

1. `App`渲染`Child`，将`val`和`getData`传进去
2. `Child`使用`useEffect`获取数据。因为对`getData`有依赖，于是将其加入依赖列表
3. `getData`执行时，调用`setVal`，导致`App`重新渲染
4. `App`重新渲染时生成新的`getData`方法，传给`Child`
5. `Child`发现`getData`的引用变了，又会执行`getData`
6. 3 -> 5 是一个死循环

如果明确`getData`只会执行一次，最简单的方式当然是将其从依赖列表中删除。但如果装了 hook 的lint 插件，会提示：`React Hook useEffect has a missing dependency`



```jsx
useEffect(() => {
  getData();
}, []);
```

实际情况很可能是当`getData`改变的时候，是需要重新获取数据的。这时就需要通过`useCallback`来将引用固定住：



```jsx
const getData = useCallback(() => {
  setTimeout(() => {
    setVal("new data " + count);
    count++;
  }, 500);
}, []);
```

上面例子中`getData`的引用永远不会变，因为他它的依赖列表是空。可以根据实际情况将依赖加进去，就能确保依赖不变的情况下，函数的引用保持不变。

### 三、useCallback 依赖 state

假如在`getData`中需要用到`val`( useState 中的值)，就需要将其加入依赖列表，这样的话又会导致每次`getData`的引用都不一样，死循环又出现了...



```jsx
const getData = useCallback(() => {
  console.log(val);

  setTimeout(() => {
    setVal("new data " + count);
    count++;
  }, 500);
}, [val]);
```

如果我们希望无论`val`怎么变，`getData`的引用都保持不变，同时又能取到`val`最新的值，可以通过自定义 hook 实现。注意这里不能简单的把`val`从依赖列表中去掉，否则`getData`中的`val`永远都只会是初始值（闭包原理）。



```jsx
function useRefCallback(fn, dependencies) {
  const ref = useRef(fn);

  // 每次调用的时候，fn 都是一个全新的函数，函数中的变量有自己的作用域
  // 当依赖改变的时候，传入的 fn 中的依赖值也会更新，这时更新 ref 的指向为新传入的 fn
  useEffect(() => {
    ref.current = fn;
  }, [fn, ...dependencies]);

  return useCallback(() => {
    const fn = ref.current;
    return fn();
  }, [ref]);
}
```

使用：

```jsx
const getData = useRefCallback(() => {
  console.log(val);

  setTimeout(() => {
    setVal("new data " + count);
    count++;
  }, 500);
}, [val]);
```

### 四、性能

一般会觉得使用`useCallback`的性能会比普通重新定义函数的性能好， 如下面例子：



```jsx
function App() {
  const [val, setVal] = useState("");

  const onChange = (evt) => {
    setVal(evt.target.value);
  };

  return <input val={val} onChange={onChange} />;
}
```

将`onChange`改为：



```jsx
const onChange = useCallback(evt => {
  setVal(evt.target.value);
}, []);
```

实际性能会更差，究其原因，上面的写法几乎等同于下面：



```jsx
const temp = evt => {
  setVal(evt.target.value);
};

const onChange = useCallback(temp, []);
```

可以看到`onChange`的定义是省不了的，而且额外还要加上调用`useCallback`产生的开销，性能怎么可能会更好？

真正有助于性能改善的，有 2 种场景：

- 函数`定义`时需要进行大量运算， 这种场景极少
- 需要比较引用的场景，如上文提到的`useEffect`，又或者是配合`React.Memo`使用：



```php
const Child = React.memo(function({val, onChange}) {
  console.log('render...');

  return <input value={val} onChange={onChange} />;
});

function App() {
  const [val1, setVal1] = useState('');
  const [val2, setVal2] = useState('');

  const onChange1 = useCallback( evt => {
    setVal1(evt.target.value);
  }, []);

  const onChange2 = useCallback( evt => {
    setVal2(evt.target.value);
  }, []);

  return (
  <>
    <Child val={val1} onChange={onChange1}/>
    <Child val={val2} onChange={onChange2}/>
  </>
  );
}
```

上面的例子中，如果不用`useCallback`, 任何一个输入框的变化都会导致另一个输入框重新渲染。

可能这个例子不是非常直观，再来看一个例子：

- 下面的代码，当handleClick1时间触发时，PageB组件也会重新渲染

```react
import React, { memo, useCallback, useState } from 'react'

function PageA (props) {
  const { onClick, children } = props
  console.log(111, props)
  return <div onClick={onClick}>{children}</div>
}

function PageB ({ onClick, name }) {
  console.log(222)
  return <div onClick={onClick}>{name}</div>
}

function UseCallback() {
  const [a, setA] = useState(0)
  const [b, setB] = useState(0)

  const handleClick1 = () => {
    setA(a + 1)
  }

  const handleClick2 =() => {
    setB(b + 1)
  }

  return (
    <>
      <PageA onClick={handleClick1}>{a}</PageA>
      <PageB onClick={handleClick2} name={b} />
    </>
  )
}

export default UseCallback
```

- 使用useCallback进行处理

1. 点击事件handleClick1触发时，PageB组件也会重新渲染，当PageB组件比较耗时时，就会造成新能问题
2. PageB组件重新渲染的原因在于每次重新渲染，onClick都会重新定义，即上次的与这次的不一致
3. 思路：通过useCallback包裹onClick来达到缓存的效果，即useCallback的依赖项不变时不重新生成
4. 用过memo方法包裹PageB组件，并且通过useCallback包裹PageB组件的onClick方法，memo与PureComponent比较类似，前者是对Function Component的优化，后者是对Class Component的优化，都会对传入组件的数据进行浅比较，useCallback则会保证handleClick2不会发生变化

```react
import React, { memo, useCallback, useState } from 'react'

function PageA (props) {
  const { onClick, children } = props
  console.log(111, props)
  return <div onClick={onClick}>{children}</div>
}

function PageB ({ onClick, name }) {
  console.log(222)
  return <div onClick={onClick}>{name}</div>
}

const PageC = memo(PageB)

function UseCallback() {
  const [a, setA] = useState(0)
  const [b, setB] = useState(0)

  const handleClick1 = () => {
    setA(a + 1)
  }

  const handleClick2 = useCallback(() => {
    setB(b + 1)
  }, [b])

  return (
    <>
      <PageA onClick={handleClick1}>{a}</PageA>
      <PageC onClick={handleClick2} name={b} />
    </>
  )
}

export default UseCallback
```

### 五、总结

总结一下，其实`useCallback`并不是一定能够提高性能的，使用的时候有许多需要注意的点，错误的使用反而会适得其反。