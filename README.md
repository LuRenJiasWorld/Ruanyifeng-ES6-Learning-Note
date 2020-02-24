# Ruanyifeng-ES6-Learning-Note

> 个人阅读阮一峰[《ECMAScript 6 入门》](https://es6.ruanyifeng.com/)时做的笔记，以提纲形式罗列。
>
> 着重于常用的、已成规范的、简明的内容，去掉了一些使用不多，意义不大，纯粹为了炫技/完善语言理论框架的内容。
>
> 去掉了一些使用不多，意义不大，纯粹为了炫技的内容
>
> 便于希望了解ES6新特性的读者快速入门，减少困惑，在最短时间内对整个ES6体系有完整了解。
>
> 本文完整阅读约需1小时，相比较原文10+小时阅读时间更适合粗略了解。如果需要了解更多细节，建议阅读阮一峰的相关原文，内容相比较本文更加完善、细致。
>
> 本文以[Creative Commons 0 - 1.0协议 ](https://creativecommons.org/publicdomain/zero/1.0/legalcode.txt)释出，允许任意用途使用。

## 目录

- [let和const](https://github.com/LuRenJiasWorld/Ruanyifeng-ES6-Learning-Note/blob/master/%E9%98%AE%E4%B8%80%E5%B3%B0ES6%E5%85%A5%E9%97%A8%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md#let%E5%92%8Cconst)
- [变量的解构赋值](https://github.com/LuRenJiasWorld/Ruanyifeng-ES6-Learning-Note/blob/master/%E9%98%AE%E4%B8%80%E5%B3%B0ES6%E5%85%A5%E9%97%A8%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md#%E5%8F%98%E9%87%8F%E7%9A%84%E8%A7%A3%E6%9E%84%E8%B5%8B%E5%80%BC)
- [字符串的扩展](https://github.com/LuRenJiasWorld/Ruanyifeng-ES6-Learning-Note/blob/master/%E9%98%AE%E4%B8%80%E5%B3%B0ES6%E5%85%A5%E9%97%A8%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md#%E5%AD%97%E7%AC%A6%E4%B8%B2%E7%9A%84%E6%89%A9%E5%B1%95)
- [字符串的新增方法](https://github.com/LuRenJiasWorld/Ruanyifeng-ES6-Learning-Note/blob/master/%E9%98%AE%E4%B8%80%E5%B3%B0ES6%E5%85%A5%E9%97%A8%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md#%E5%AD%97%E7%AC%A6%E4%B8%B2%E7%9A%84%E6%96%B0%E5%A2%9E%E6%96%B9%E6%B3%95)
- [正则的扩展](https://github.com/LuRenJiasWorld/Ruanyifeng-ES6-Learning-Note/blob/master/%E9%98%AE%E4%B8%80%E5%B3%B0ES6%E5%85%A5%E9%97%A8%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md#%E6%AD%A3%E5%88%99%E7%9A%84%E6%89%A9%E5%B1%95)
- [数值的扩展](https://github.com/LuRenJiasWorld/Ruanyifeng-ES6-Learning-Note/blob/master/%E9%98%AE%E4%B8%80%E5%B3%B0ES6%E5%85%A5%E9%97%A8%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md#%E6%95%B0%E5%80%BC%E7%9A%84%E6%89%A9%E5%B1%95)
- [函数的扩展](https://github.com/LuRenJiasWorld/Ruanyifeng-ES6-Learning-Note/blob/master/%E9%98%AE%E4%B8%80%E5%B3%B0ES6%E5%85%A5%E9%97%A8%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md#%E5%87%BD%E6%95%B0%E7%9A%84%E6%89%A9%E5%B1%95)
  - [关于this/apply/call/bind](https://github.com/LuRenJiasWorld/Ruanyifeng-ES6-Learning-Note/blob/master/%E9%98%AE%E4%B8%80%E5%B3%B0ES6%E5%85%A5%E9%97%A8%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md#%E5%85%B3%E4%BA%8Ethisapplycallbind)
- [数组的扩展](https://github.com/LuRenJiasWorld/Ruanyifeng-ES6-Learning-Note/blob/master/%E9%98%AE%E4%B8%80%E5%B3%B0ES6%E5%85%A5%E9%97%A8%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md#%E6%95%B0%E7%BB%84%E7%9A%84%E6%89%A9%E5%B1%95)
  - [展平数组的若干方法](https://github.com/LuRenJiasWorld/Ruanyifeng-ES6-Learning-Note/blob/master/%E9%98%AE%E4%B8%80%E5%B3%B0ES6%E5%85%A5%E9%97%A8%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md#%E5%B1%95%E5%B9%B3%E6%95%B0%E7%BB%84%E7%9A%84%E8%8B%A5%E5%B9%B2%E6%96%B9%E6%B3%95)
- [对象的扩展](https://github.com/LuRenJiasWorld/Ruanyifeng-ES6-Learning-Note/blob/master/%E9%98%AE%E4%B8%80%E5%B3%B0ES6%E5%85%A5%E9%97%A8%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md#%E5%AF%B9%E8%B1%A1%E7%9A%84%E6%89%A9%E5%B1%95)
  - [对象的各种方法](https://github.com/LuRenJiasWorld/Ruanyifeng-ES6-Learning-Note/blob/master/%E9%98%AE%E4%B8%80%E5%B3%B0ES6%E5%85%A5%E9%97%A8%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md#%E5%AF%B9%E8%B1%A1%E7%9A%84%E5%90%84%E7%A7%8D%E6%96%B9%E6%B3%95)
- [Set和Map数据结构](https://github.com/LuRenJiasWorld/Ruanyifeng-ES6-Learning-Note/blob/master/%E9%98%AE%E4%B8%80%E5%B3%B0ES6%E5%85%A5%E9%97%A8%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md#set%E5%92%8Cmap%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84)
- [Promise](https://github.com/LuRenJiasWorld/Ruanyifeng-ES6-Learning-Note/blob/master/%E9%98%AE%E4%B8%80%E5%B3%B0ES6%E5%85%A5%E9%97%A8%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md#promise)
- [Iterator&for...of](https://github.com/LuRenJiasWorld/Ruanyifeng-ES6-Learning-Note/blob/master/%E9%98%AE%E4%B8%80%E5%B3%B0ES6%E5%85%A5%E9%97%A8%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md#iteratorforof)
- [Generator](https://github.com/LuRenJiasWorld/Ruanyifeng-ES6-Learning-Note/blob/master/%E9%98%AE%E4%B8%80%E5%B3%B0ES6%E5%85%A5%E9%97%A8%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md#generator)
- [async函数](https://github.com/LuRenJiasWorld/Ruanyifeng-ES6-Learning-Note/blob/master/%E9%98%AE%E4%B8%80%E5%B3%B0ES6%E5%85%A5%E9%97%A8%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md#async%E5%87%BD%E6%95%B0)
- [Class](https://github.com/LuRenJiasWorld/Ruanyifeng-ES6-Learning-Note/blob/master/%E9%98%AE%E4%B8%80%E5%B3%B0ES6%E5%85%A5%E9%97%A8%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md#class)
- [Module](https://github.com/LuRenJiasWorld/Ruanyifeng-ES6-Learning-Note/blob/master/%E9%98%AE%E4%B8%80%E5%B3%B0ES6%E5%85%A5%E9%97%A8%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md#module)
- [ArrayBuffer](https://github.com/LuRenJiasWorld/Ruanyifeng-ES6-Learning-Note/blob/master/%E9%98%AE%E4%B8%80%E5%B3%B0ES6%E5%85%A5%E9%97%A8%E5%AD%A6%E4%B9%A0%E7%AC%94%E8%AE%B0.md#arraybuffer)

## 反馈

如果对本文有任何建议（谬误、缺失等），欢迎在issues中提出。