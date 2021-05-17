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

## selector

selector 是选择器，也就是通过 RecoilState 实例 从顶层数据状态集合中 查找到对应变量的方法。

我们无法直接使用 selector，而是会使用他派生出来的 4 种钩子函数：

1. useRecoilState()：获取变量的读(值)、写
2. useRecoilValue()：仅获取变量的读(值)
3. useSetRecoilState()：仅获取变量的写
4. useResetRecoilState()：重新初始化该变量的值



<br>

//本文未完待续...