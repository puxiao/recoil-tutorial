# 05 Recoil 钩子函数(hooks) 名称的讨论



之前我们学习了一些基础的 Recoil API，其中包括：

<RecoilRoot \>、atom()、selector()、useRecoilState、useRecoilValue、useSetRecoilState、useResetRecoilState



<br>

除了这些基础的 API 之外，还有其他很多。

1. 状态(State)相关的：Loadable、useRecoilStateLoadable()、useRecoilValueLoadable()、useGetRecoilValueInfo()、isRecoilValue()
2. 回调相关的：useRecoilCallback()
3. 工具类(Utils)相关的：atomFamily()、selectorFamily()、constSelector()、errorSelector()、noWait()、waitForAll()、waitForAllSettled()、waitForNone()、waitForAny()
4. 快照(snapshot)相关的：Snapshot、useRecoilTransactionObserver()、useRecoilSnapshot()、useGotoRecoilSnapshot()
5. 其他杂项：useRecoilBridgeAcrossReactRoots()



<br>

**是不是被这些 API ，尤其是钩子函数(hooks)的名字绕晕了。**



<br>

**再说一下我的困惑**

我在学习 Recoil 基础时，对于获取原子变量状态值的钩子函数 useRecoilValue 感觉到特别别扭。

因为：

1. 获取设置值对应的钩子函数名字为 useSetRecoilState  ...  Set
2. 获取重置对应的钩子函数名称为 useResetRecoilState  ...  Reset
3. 那凭什么获取值的钩子函数为什么不叫 useGetRecoilState 呢？

单独看 "useRecoilValue" 并没有问题，但是感觉 get / set / reset 才是统一的，也便于记忆。



<br>

于是我向 Recoil 官方提交了一个 Issues：

https://github.com/facebookexperimental/Recoil/issues/1035

> 我希望将 useRecoilValue 名称修改为 useGetRecoilState，或者保留 useRecoilValue 同时增加上 useGetRecoilState。



<br>

很快，我收到了官方的回复。

```
See rename proposal here for a potential future release: #804
```



<br>

**原来 Recoil 核心开发人员也认为 Recoil 的一些钩子函数名称过于长，于是单独创建了一个页面，用于征集、讨论如何改进、重命名这些函数的名字。**

<br>

**该帖子的地址：** https://github.com/facebookexperimental/Recoil/issues/804



<br>

摘选出该帖子的一些讨论内容。

#### Recoil 核心开发人员的观点和计划

> 这段内容发布于 2020年12月份

Recoil 核心人员目前觉得将原子状态，读写采用 RecoilState、仅读值采用 RecoilValue 会更加容易区分 2 者的区别。



<br>

但是，对于 Recoil 的 .d.ts 中的类型则统一采用 RecoilState 来作为命名前缀，比如：

1. 可读可写：RecoilState
2. 只读：RecoilStateReadOnly
3. 只写：RecoilStateWriteable



> Facebook 内部可能并不使用 TypeScript，而是使用自家发明的 Flow。
>
> Flow 和 TypeScript 用户几乎相同，也是用于将 JS 变为面对对象的编程框架。
>
> 对于 Flow，上面依次对应的命名是：RecoilValue、RecoilValueReadOnly、RecoilState
>
> 额~ Recoil 的 Flow 版 和 TS 版 名字还不一样...



<br>

Recoil 核心开发人员计划未来将所有的 Recoil 钩子函数都以 “useRecoil” 作为开头。在这个计划影响之下，目前的一些函数名字会发生变化，例如：

1. 将目前的 useSetRecoilState 修改为 useRecoilSetter
2. 将目前的 useResetRecoilState 修改为 useRecoilResetter
3. 将目前的 useRecoilValueLoadable 修改为 useRecoilLoadable
4. 将目前的 useGetRecoilValueInfo_UNSTABLE 修改为 useRecoilGetInfo_UNSTABLE
5. 将目前的 isRecoilValue 修改为 isRecoilState



<br>

#### 一些网友给出的个人观点和建议

**网友A：**

直接简化名称，例如：

1. useRecoilState 修改为 useRecoil
2. useSetRecoilState 修改为 useRecoilSet
3. useResetRecoilState 修改为 useRecoilReset
4. isRecoilValue 修改为 isRecoil



<br>

**网友B：**

建议直接参考 jotai 的命名规则。

> jotai 也是一个 React 第三方状态库，并且据说非常好用，比 recoil 都好用，因为 jotai 的函数名字特别短，好记。
>
> https://github.com/pmndrs/jotai



<br>

**网友C：**

我就是 Facebook 的员工，我见过我们的内部代码，每一个名字都非常长，快疯了。



<br>

**大家的共识：如果 Recoil 不能够很好的解决 API 函数名称过长的问题，那么 Recoil 肯定不会流行起来。**



<br>

最后，说一点我个人的感受：

1. 尽管我们都是普普通通的开发者，但是在我们使用各种 JS 库的时候，也需要多一些深层次思考。
2. 当我们觉得哪里不好，有问题的时候，勇敢去尝试分析、解决，向官方提交 Issues 或 PR。
3. 这样会加速我们的成长。

