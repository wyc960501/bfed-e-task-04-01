## 1. 请简述 React 16 版本中初始渲染的流程

- 调用 legacyCreateRootFromDOMContainer，判断是否为服务器端渲染，如果不是服务器端渲染，清空 container 容器中的节点

- 调用 createLegacyRoot 方法，返回 ReactDOMBlockingRoot 类实例对象
- 在 ReactDOMBlockingRoot 方法中，为 container 添加 _reactRootContainer 属性，属性值为对象，对象有 _internalRoot 属性，值为 createRootImpl 方法的返回值
- 最后通过 createFiberRoot 方法创建 fiberRoot 对象和 rootFiber 对象

## 2. 为什么 React 16 版本中 render 阶段放弃了使用递归

- 在 React 15 的版本中，采用了循环加递归的方式进行了 virtualDOM 的比对，由于递归使用 JavaScript 自身的执行栈，一旦开始就无法停止，直到任务执行完成。如果 VirtualDOM 树的层级比较深，virtualDOM 的比对就会长期占用 JavaScript 主线程，由于 JavaScript 又是单线程的无法同时执行其他任务，所以在比对的过程中无法响应用户操作，无法即时执行元素动画，造成了页面卡顿的现象。

- 在 React 16 的版本中，放弃了 JavaScript 递归的方式进行 virtualDOM 的比对，而是采用循环模拟递归。而且比对的过程是利用浏览器的空闲时间完成的，不会长期占用主线程，这就解决了 virtualDOM 比对造成页面卡顿的问题。


## 3. 请简述 React 16 版本中 commit 阶段的三个子阶段分别做了什么事情

- commit 阶段的第一个子阶段

  - 调用类组件的 getSnapshotBeforeUpdate 生命周期函数，传入旧的 props、旧的 state
  
- commit 阶段的第二个子阶段

  - 根据 effectTag 属性执行 DOM 操作

- commit 阶段的第三个子阶段

  - 调用类组件的生命周期函数和函数组件的钩子函数



## 4. 请简述 workInProgress Fiber 树存在的意义是什么

- 实现快速的 DOM 更新

  - 在 React 中最多会同时存在两棵 Fiber 树，当前在屏幕中显示的内容对应的 Fiber 树叫做 current Fiber 树，当发生更新时，React 会在内存中重新构建一颗新的 Fiber 树，这颗正在构建的 Fiber 树叫做 workInProgress Fiber 树。在双缓存技术中，workInProgress Fiber 树就是即将要显示在页面中的 Fiber 树，当这颗 Fiber 树构建完成后，React 会使用它直接替换 current Fiber 树达到快速更新 DOM 的目的，因为 workInProgress Fiber 树是在内存中构建的所以构建它的速度是非常快的。 

