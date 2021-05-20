# 03 Recoil 基础用法

在上一节中，啰里啰嗦得讲了 Recoil 的原理和特征，那么本文将开始真正去编写 Recoil 代码。

这里先说一句狠话：

如果你真的理解了 Recoil 工作原理，那么你可能只需要花费 30 分钟就能掌握 Recoil 基础用法， Recoil 的基础用法就够你解决日常 80% 的状态管理工作需求。



<br>

## 安装 Recoil 模块

```
yarn add recoil
```

或

```
npm i recoil
```



<br>

> 目前 Recoil 最新版本为 0.3.0



<br>

在开始讲解 Recoil 的 API 用法前，我们再次强调 2 点：

1. Recoil 只适用于 Hooks 编写的函数组件，不支持类组件。

2. 本文示例使用的是 React 17.0.2 + TypeScript，所以本文示例中会使用大量 Recoil .d.ts 中定义的数据类型。

   > 所以有可能在示例中使用的 TS 类型，并不会存在于 Recoil 中。



<br>

## RecoilRoot

如同传统的状态管理方式，我们也需要将所有共享变量储存在一个顶层数据集合中，而 RecoilRoot 就是 Recoil 提供的那个顶层数据集合对象。



**<RecoilRoot \> 为包裹的子组件，提供 Recoil 的状态的上下文作用域。**



<br>

**RecoilRoot 对应的 .d.ts 解读：**

```
export type RecoilRootProps = {
  initializeState?: (mutableSnapshot: MutableSnapshot) => void,
  override?: true,
} | {override: false};

export const RecoilRoot: React.FC<RecoilRootProps>;
```

可以看出，RecoilRoot 包裹的组件，必须是 React.FC 函数组件。

此外 <RecoilRoot \> 接收一个可选参数 initializeState，用于初始化整个数据状态。

该值类型为 `MutableSnapshot`。

> mutable：可以改变的  
> snapshot：快照  
> MutableSnapshot：可变快照，具体用法我们现在不做过多讨论，计划在后面单独写一篇关于 快照(Snapshot) 的文章。



<br>

**具体用法示例：**

我们只需在 App.tsx 中使用 <RecoilRoot \> 标签包裹住所有子组件即可：

```
import { RecoilRoot } from 'recoil'

function App() {
  return (
    <RecoilRoot>
        <IndexPage />
    </RecoilRoot>
  )
}
```

> 由于 <RecoilRoot \> 处于顶层，那么意味着 React 所有不同层级的子组件均可 ”访问“ 顶层数据集合对象。
>
> 请注意，这里的 “访问” 是添加引号的，因为对于子组件而言根本感知不到 顶层数据集合对象的存在。



<br>

**允许同时使用多组 <RecoilRoot \> 嵌套，但是最里面的那一层作用域将完全覆盖上一层作用域。**

<br>

假设某个应用程序一共有 4 个大模块：A、B、C、D，如果 A、B 模块完全独立于整体：

1. A、B 模块中不会有任何变量会被 C、D 模块调用
2. A、B 也不会调用 C、D 中的任何变量。

那么此时，就可以使用 <RecoilRoot \> 标签在内部再包裹一下模块 A、B。

这样就会将 A 、B 模块内的变量完全与外界隔离。

> 但是一般情况下，我们还是将 <RecoilRoot \> 标签包裹在 <App \> 顶层内。



<br>

## atom

`atom()` 是 Recoil 提供的一个函数，用来定义变量。

atom() 可以在任何组件内使用。



<br>

**atom 对应的 .d.ts 解读：**

```
export class RecoilState<T> extends AbstractRecoilValue<T> {}

type NodeKey = string;

export interface AtomOptions<T> {
  key: NodeKey;
  default: RecoilValue<T> | Promise<T> | T;
  effects_UNSTABLE?: ReadonlyArray<AtomEffect<T>>;
  dangerouslyAllowMutability?: boolean;
}

export function atom<T>(options: AtomOptions<T>): RecoilState<T>;
```

我们可以看到，atom()：

1. 接收一个 AtomOptions 类型的参数
2. 返回一个 RecoilState 类型的实例



<br>

#### 参数 AtomOptions

一共有 2 个必填属性、2 个选填属性：

1.  key：变量全局唯一 ID，类型为字符串

2. default：变量默认值

   > 请注意，默认值究竟是什么，也表明了该变量的 TS 数据类型。
   >
   > 通过 TS 可以看到 Promise<T>，也就是支持异步设置变量默认值
   >
   > 至于如何设置，我们会在以后讲解

3. effects_UNSTABLE：变量的可选副作用(类似 useEffect )

   > 副作用的单词是 effect，而 "unstable" 单词的意思为：不稳定、易改变。
   >
   > 也就是说这个 API 实际上还在不断更新开发中，并未稳定，因此才被标记上 “UNSTABLE”。
   >
   > 所以慎用这个 API，不要轻易使用在生产环境中

4. dangerouslyAllowMutability：是否允许在某些情况下改变变量的数据类型。

   > 请注意，该属性前缀为危险(dangerously)，也就是说这个操作是危险的，也请慎用。

<br>

小总结一下：

**目前我们应该尽量只使用 key、default 2 个必填属性，剩下 2 个选填属性 effects_UNSTABLE、dangerouslyAllowMutability 我们尽量不要去使用他们。**



<br>

#### 返回值 RecoilState

atom() 的返回值是一个 RecoilState 实例。

请注意 RecoilState 实例并不是指 变量本身，而是指向该变量在 作用域 内的引用。

> 你可以先不必过分纠结思考，等到后面我们学习完其他几个对变量的 读写 操作后，你自然就明白 RecoilState 的含义了。



<br>

#### 举一个示例：

```
const selectedIndex = atom({
    key: 'selectedIndex',
    default: 0
})

console.log(selectedIndex.key) // selectedIndex
```

> 这样，通过 atom() 我们就像全局添加了一个 ID(key) 为 “selectedIndex” 的变量。



<br>

#### 实际使用技巧

在实际的项目中，我更倾向于将相关的多个变量定义在同一个文件中，然后集中导出这些原子变量的引用(RecoilState实例)。

```
export const xxAa = atom({
  key:'xxAa',
  default:'xxxxxxx'
})

export const xxBb = atom({
  key:'xxBb',
  default:[]
})

export const xxCc = atom({
  key:'xxCc',
  default:{}
})
```

> 这样当其他地方需要引入这些变量时，比较容易引入这些变量的 RecoilState 实例，也就是下面要讲解的 Selector 选择器。
>
> ```
> import { xxBb } from 'xxxxx'
> 
> ...
> ```



<br>

> 或许你可以在项目创建一个名为 atoms 的目录，用来专门存放这些集中定义的变量。例如：
>
> 1. src/atoms/modeA.ts
> 2. src/atoms/modeB.ts
> 3. ...



<br>

## selector

selector() 是一个函数，官方的定义为：接收原子数据(RecoilState)或其他选项作为数据输入源的纯函数。

我们正是通过 selector() 函数来将 原子数据 派生/衍生 出新的数据。



<br>

**selector 对应的 .d.ts 解读：**

```
export interface ReadOnlySelectorOptions<T> {
    key: string;
    get: (opts: { get: GetRecoilValue, getCallback: GetCallback }) => Promise<T> | RecoilValue<T> | T;
    dangerouslyAllowMutability?: boolean;
}

export interface ReadWriteSelectorOptions<T> extends ReadOnlySelectorOptions<T> {
  set: (
    opts: {
      set: SetRecoilState;
      get: GetRecoilValue;
      reset: ResetRecoilState;
    },
    newValue: T | DefaultValue,
  ) => void;
}

export function selector<T>(options: ReadWriteSelectorOptions<T>): RecoilState<T>;
export function selector<T>(options: ReadOnlySelectorOptions<T>): RecoilValueReadOnly<T>;
```



<br>

#### 参数 ReadWriteSelectorOptions、ReadOnlySelectorOptions

我们可以看到，selector() 实际上可接受 2 种参数：

2. ReadOnlySelectorOptions：需要配置 get 属性，用于 `只读` 数据

   > get 对应的箭头函数中，有 2 个参数：get、getCallback
   
2. ReadWriteSelectorOptions：需要配置 set/get/reset 属性，用于 `读/写/重置` 数据

   > 可以看出 selector() 的配置参数，无论是哪种配置类型，get 永远是必填项。

在本文中，我们先学习一下 get 这种情况，至于 set/get/reset 我们以后再详细讲解。



<br>

#### 返回值 RecoilState、RecoilValueReadOnly

根据 selector() 参数不同，返回的对象也不同。

1. 参数为 ReadWriteSelectorOptions，则返回 RecoilState

   > 可用作  衍生，因为相当于创建了一个可读可写、新的变量
   >
   > 请注意：上面这句 衍生 的理解是我个人目前的认知，不一定正确。

2. 参数为 ReadOnlySelectorOptions，则返回 RecoilValueReadOnly

   > 可用作 派生，因为相当于创建了一个对原始数据某属性值或分支 加工而成的只读变量值





#### 举个例子

1. 我们先使用 atom() 创建一个变量，该变量用于记录一个字符串的值。
2. 然后再使用 selector() 派生出一个用于表示该字符串长度的只读变量。

```
const textState = atom({
    key: 'textState',
    default: ''
})

const countState = selector({
    key: 'charCountState',
    get: ({ get }) => {
        const text = get(textState) //获取到字符串
        return text.length //返回字符串的长度
    }
})
```

> 请注意，我们在 selector() 的配置项的 get 属性中，目前只使用 get 方法，暂时用不到 getCallback。
>
> 该 get 方法类型为 GetRecoilValue

> 由于 get 出现了 2 次，这里重申一下，千万别记混淆了：
>
> 1. 第 1 个 get ：selector() 配置项 get 属性
> 2. 第 2 个 get：第 1 个 get 属性对应的箭头函数中约定的 get() 函数。

> 为了简便区分 2 个 get，在本文中我可能称呼他们为：get 属性、get 函数



<br>

**补充说明：**

假设你使用 selector() 派生的变量依赖多个 原子数据状态，那么你可以多次使用 get 函数。

举例：

```
const xxxState = selector({
    key: 'xxxState',
    get: ({ get }) => {
        const aa = get(aaState)
        const bb = get(bbState)
        ...
        return xxxxx
    }
})
```



<br>

## Recoil的 4 个基础 钩子函数(hooks)

我们前面讲解过的 atom()、selector() 函数都是用于定义原子变量的。

那么如何 获取变量值 或者 修改变量值？

这就需要用到 Recoil 为我们提供的 4 个基础的钩子函数，他们分别是：

1. useRecoilState()：获取变量的读(值)、写
2. useRecoilValue()：仅获取变量的读(值)
3. useSetRecoilState()：仅获取变量的写
4. useResetRecoilState()：重新初始化该变量的值

> 还有其他钩子函数，但是我们本文只讲解这 4 个。



<br>

#### useRecoilState()

对应的 .d.ts 的定义：

```
export function useRecoilState<T>(recoilState: RecoilState<T>): [T, SetterOrUpdater<T>];
```

<br>

useRecoilState() 的用发几乎和 useState 一模一样。

为什么说一模一样呢，因为 useRecoilState() 返回的数组和 useState 也是一样的，第 1 个值为 变量的值，第 2 个值为修改变量值的函数。

唯一区别在于 useRecoilState() 需要传入的参数是 原子变量状态，也就是 RecoilState 实例。



<br>

**使用示例：**

```
const textState = atom({
    key: 'textState',
    default: ''
})

const [text, setText] = useRecoilState(textState)
```



<br>

**补充说明：**

useRecoilState 返回的第 2 个修改函数，如同 useState 那样，也有 2 种修改方式：

> 我们以上面使用示例中的 setText 来举例

第1种：直接传入新值覆盖旧值

```
setText(newValue)
```

第2种：使用箭头函数，先获取旧值，再执行一些计算后，将箭头函数的返回值作为新值

```
setText((currentValue) => { return xxx })
```





<br>

#### useRecoilValue()、useSetRecoilState()

当我们理解完 useRecoilState() 之后，就顺带可以理解 useRecoilValue() 和 useSetRecoilState() 了。

useRecoilState() 即返回变量的值，又返回修改变量的方法。

1. useRecoilValue()：只得到变量的值
2. useSetRecoilState()：只得到修改变量的方法



<br>

**使用示例：**

```
const [text, setText] = useRecoilState(textState)

...

const text = useRecoilValue(textState)
const setText = useSetRecoilState(textState)
```

> 请注意，对于一些由 selector() 派生出的只读变量，我们就需要用到 useRecoilValue() 来获取该变量的值。



<br>

#### useResetRecoilState()

对应的 .d.ts 的定义：

```
export function useResetRecoilState(recoilState: RecoilState<any>): Resetter;
```

<br>

useResetRecoilState() 函数用于重置变量。

1. 参数是重置变量对应的 RecoilState 实例
2. 返回值是重置变量对应的函数



<br>

**使用示例：**

```
const textState = atom({
    key: 'textState',
    default: ''
})

const reset = useResetRecoilState(textState)

const handleClick = () => {
  reset()
}

<button onClick={handleClick}>reset</button>
```

> 当点击按钮后，会将 textState 对应的变量值恢复成默认值，也就是当初使用 atom() 创建该变量时配置的 default 的值。



<br>

## 完整示例

本文，我们学习了 Recoil 最为基础的一些知识。

1. <RecoilRoot \>
2. atom()
3. selector()
4. useRecoilState()、useRecoilValue()、useSetRecoilState()、useResetRecoilState()



<br>

我们现在将使用 React + TypeScript + Recoil 创建一个最基础的完整使用示例。

示例内容：

1. 页面上有一个输入框，默认什么内容都没有
2. 当输入文字后，分别显示输入框的文字内容、文字的总字数
3. 当点击按钮时，恢复输入框的默认值(空)



<br>

**App.tsx**

```
import { RecoilRoot } from 'recoil'
import HelloRecoil from '@/components/hello-recoil'

function App() {
  return (
    <RecoilRoot>
        <HelloRecoil />
    </RecoilRoot>
  )
}
export default App
```

> 使用 <RecoilRoot \> 标签包裹住整个 App 的子项，创建出 Recoil 的上下作用域。

> 提示：我实际还添加了 react 的 alias，采用 @/components/ 这种方式获取自定义组件



<br>

**HelloRecoil.tsx**

```
import React from 'react'
import { atom, selector, useRecoilState, useRecoilValue, useResetRecoilState } from 'recoil'

//创建一个用于记录 文字 值的变量
const textState = atom({
    key: 'textState',
    default: ''
})

//派生出获取 文字总字数 的变量
const countState = selector({
    key: 'charCountState',
    get: ({ get }) => {
        const text = get(textState)
        return text.length
    }
})

const HelloRecoil = () => {
    const [text, setText] = useRecoilState(textState) //获取文字值、修改文字值的函数
    const count = useRecoilValue(countState) //获取文字总字数对应的变量值
    const reset = useResetRecoilState(textState) //获取重置文字值的函数

    const handleOnChange: React.ChangeEventHandler<HTMLInputElement> = (event) => {
        setText(event.target.value) //修改文字的值
    }

    const handleClick = () => {
        reset() //重置，恢复文字默认值
    }

    return (
        <div>
            <input onChange={handleOnChange} value={text} />
            <div>text: {text}</div>
            <div>count: {count}</div>
            <button onClick={handleClick}>reset</button>
        </div>
    )
}

export default HelloRecoil
```

> 本示例是为了简化，所以将所有数据变量(状态) 的内容都写在了同一个组件内。
>
> 实际项目中操控这些数据变化(状态)的，一定是分散在不同的组件中的。



<br>

虽然本文比较出长，但是实际讲解的 Recoil 基础内容却没有多少。

但是当你学会这些基础的用法后，已经可以应付很多常见的 状态共享管理应用场景了。



<br>

**至此，你已经学习了：**

1. Recoil 原理和特性
2. Recoil 基础用法

那么接下来再去学习 Recoil 其他钩子函数，就会比较容易了。

我强烈建议你直接去 Recoil 官方文档，逐个阅读他的 API，对其他 钩子函数 有一个初步的印象，然后在实战中不断提高。

https://recoiljs.org/docs/api-reference/core/atom



<br>

下一篇，我会讲解一下 Recoil 都会有哪些高级用法。