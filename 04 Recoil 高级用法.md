# 04 Recoil 高级用法

本文将讲解 Recoil 一些高级用法，但是由于本人也是刚开始使用 Recoil 不久，这些高级用法也是在摸索中。

所以本文只是比较笼统的讲解一些都有哪些高级用法，以及他们对应的使用场景。



<br>

## 异步数据查询

在 Recoil 基础用法一文中，我们修改 原子数据状态，都使用的是同步方式。

而 Recoil 强大之处就在于还提供异步方式去修改 原子数据状态。



<br>

试想一下这个场景：

假设我们在输入框中输入一个 ID，点击某个按钮，我们需要在页面中显示这个 ID 对应的用户名。

如果用户名本身就储存在应用程序中，那么我们的代码可能为：

```
const getUserName = (id:string):string => {
  return xxx[id].name
}

const currentUserIDState = atom({
  key: 'CurrentUserID',
  default: 1,
});

const currentUserNameState = selector({
  key:'currentUserNameState',
  get:({get} =>{
     const id = get(currentUserIdState)
     return getUserName(id)
  })
})
```

以上为同步写法。



<br>

假设我们现在不是同步写法获取用户名，而是请求网络，将 ID 作为参数，通过异步的方式返回得到用户名。

那么我们需要做的，就是将 get 属性对应的箭头函数修改为 异步，即添加上 async。

```
const currentUserIDState = atom({
  key: 'CurrentUserID',
  default: 1,
});

const currentUserNameState = selector({
  key:'currentUserNameState',
  get: async ({get} =>{
     const id = get(currentUserIdState)
     const respone = await mydb.query({id}) //异步请求网络，获取用户名
     return respone.name //将异步得到的用户名作为新值进行更新
  })
})
```

> 当异步网络请求完成后，会自动通知 相关组件进行 用户名的更新。



<br>

由于是异步网络请求，那么我们就需要考虑一下几点：

1. 在异步网络请求的过程中，那页面状态该显示什么呢？
2. 假设发生了异步请求错误，又该如何处理？
3. 相同的请求，会产生缓存问题吗？还是每次都发起新的请求？
4. ...



<br>

关于上述问题，由于我并未在实际项目中真正使用过 Recoil 的异步请求。

所以不做过多讲解，因为我担心我理解的是错误，误导你。

你可以查看 Recoil 官方文档，其中有对上述问题的解决办法。

https://recoiljs.org/docs/guides/asynchronous-data-queries



<br>

## 原子状态副作用

我们之前在讲解 atom() 时提到，除了必填属性 key、default 之外，还有 2 个可选属性，其中一个是 effect_UNSTABLE。

这个 effect_UNSTABLE 对应的就是 原子状态副作用 函数。

不过该属性目前依然处于实验阶段，因此暂时并不适合投入到生产环境中。



<br>

关于原子副作用，请查看官方 原子副作用 相关文档：

https://recoiljs.org/docs/guides/atom-effects



<br>

## 代码单元测试

代码单元测试属于我的知识盲区。

请查看官方 单元测试 相关文档：

https://recoiljs.org/docs/guides/testing



<br>

## 代码调试工具

这里说的代码调试并不是指在浏览器中使用调试工具调试。

而是指 Recoil 提供的一些用于调试的 API.

请查看官方网 调试 相关文档：

https://recoiljs.org/docs/guides/dev-tools



<br>

## 快照(snapshot)

无论同步还是异步修改 原子状态，当有新值替换旧值后，都不会进行旧值保存。

假设我们想做一个项目，其中需要记录每一步 原子状态 的数据值，类似历史记录。

那么就需要用到 Recoil 的快照(snapshot)。

请查看官方 快照 相关文档：

https://recoiljs.org/docs/api-reference/core/Snapshot/



## 总结

<br>

本文只是罗列出 Recoil 一些高级用法，但是具体怎么用，还是需要你查阅官方文档。

或许未来某一天，当我对这些高级用法比较熟练，且这些相关钩子都已经稳定后，会再来更新本文档。



<br>

至此，关于 Recoil 的讲解就暂告一段落。

让我们在实践中去学习，提高自己。

加油，加油！