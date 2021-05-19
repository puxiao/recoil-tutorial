# recoil-tutorial
Recoil 系列教程，学习和探索新一代 React 状态库。



<br>

## 本教程的背景和初衷

#### 本人背景

本人已学习使用 React 已有一年多，但实际做的项目并不多，且也相对比较简单，所以我从未曾学习或使用过任何第三方 React 状态库，例如：Redux、MobX 等。

对于 React 状态管理，我之前使用的是：useContext + useReducer。



<br>

> 我对 Redux 的学习难度早有耳闻，并且我对 Redux 有抵触情绪，我始终觉得 Redux 是类组件时期的产物，而我更习惯使用函数组件。



<br>

#### 本教程初衷

最近我正在开发一个比较复杂的 React 项目，所以需要用到专业的状态库。

恰好此时，我听说了 Recoil ，所以计划从今天( 2021.05.14 ) 开始学习和使用 Recoil。

**通过教别人的方式来记录自己的学习过程，这就是本教程的初衷。**

> 我用我的理解、我的语言来讲解 Recoil，希望给同样想学习 Recoil 的你提供一些参考。



<br>

实际上 Recoil 并不复杂，我更加推荐的是直接去官网查看和学习。

**Recoil官网地址：** https://recoiljs.org/

<br>

> 关于 Recoil 简体中文版文档，国内的站点有：
>
> 1. https://www.recoiljs.cn/ (翻译的比较差)
> 2. https://recoil.js.cn/ (翻译的还比较通顺)
>
> 但是目前这 2 个网站有 1/2 的文章还未翻译完成，并且如同 React 文档一样，虽然每个汉字都能读懂，但是你可能就是理解不了他在说什么。



<br>

## 文章目录

* [01 为什么选择 Recoil 而不是 Redux/MobX ？](https://github.com/puxiao/recoil-tutorial/blob/main/01%20%E4%B8%BA%E4%BB%80%E4%B9%88%E9%80%89%E6%8B%A9%20Recoil%20%E8%80%8C%E4%B8%8D%E6%98%AF%20ReduxMobX%20%EF%BC%9F.md)
* [02 Recoil 的理念和特性](https://github.com/puxiao/recoil-tutorial/blob/main/02%20Recoil%20%E7%9A%84%E7%90%86%E5%BF%B5%E5%92%8C%E7%89%B9%E6%80%A7.md)
* [03 Recoil 基础用法](https://github.com/puxiao/recoil-tutorial/blob/main/03%20Recoil%20%E5%9F%BA%E7%A1%80%E7%94%A8%E6%B3%95.md)
* [04 Recoil 高级用法](https://github.com/puxiao/recoil-tutorial/blob/main/04%20Recoil%20%E9%AB%98%E7%BA%A7%E7%94%A8%E6%B3%95.md)
  * 异步数据查询
  * 原子状态副作用
  * 代码单元测试
  * 代码调试工具
  * 原子状态快照
* [05 Recoil 钩子函数(hooks) 名称的讨论](https://github.com/puxiao/recoil-tutorial/blob/main/05%20Recoil%20%E9%92%A9%E5%AD%90%E5%87%BD%E6%95%B0(hooks)%20%E5%90%8D%E7%A7%B0%E7%9A%84%E8%AE%A8%E8%AE%BA.md)



<br>

> 我认为本教程中，真正有价值的是 `02 Recoil 的理念和特性`，当你理解 Recoil 的理念(运行逻辑)后，实际使用过程中哪个函数不会直接去看官方 API 文档就好了。



<br>

## 补充说明

**补充说明 1：**

本教程的示例使用 Create-React-App 4.0.3 版本创建的 React 项目：

* React 版本为 17.0.2
* TypeScript 版本为 4.1.2

如果你不会 TypeScript，可能阅读示例代码时，略微有些困难。



<br>

**补充说明 2：**

由于 Recoil 是针对 React Hooks 函数组件而产生的，所以如果你使用的是 类组件，那么你需要先学习 React Hooks，学完后再来看本教程。

<br>

关于 React Hooks，可以查阅我一年前写的 Hooks 教程：

react-hook-tutorial：https://github.com/puxiao/react-hook-tutorial

> 尽管该教程是 1 年前写的，但是最近 1 年 react hooks 并未发生任何使用上的变化，所以该教程依然不过时，可以放心阅读。



<br>

**补充说明 3：**

尽管现阶段非常推荐 Recoil，但是大家也不要忘了，还有世界上还有另外一个 React 状态管理库：jotai

jotai 和 recoil 非常类似，且 jotai 有自己的特有优势：jotai 的 API 名称比 recoil 更短，更好记。 

> 关于 API 名称这一点，可以查看本教程中的最后一篇 [05 Recoil 钩子函数(hooks) 名称的讨论](https://github.com/puxiao/recoil-tutorial/blob/main/05%20Recoil%20%E9%92%A9%E5%AD%90%E5%87%BD%E6%95%B0(hooks)%20%E5%90%8D%E7%A7%B0%E7%9A%84%E8%AE%A8%E8%AE%BA.md)



<br>

## 信息反馈

若有错误欢迎指正，本人微信同QQ(78657141)，或通过邮件联系：yangpuxiao@gmail.com

本教程在 Github 中的地址为：https://github.com/puxiao/recoil-tutorial