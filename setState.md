# React--setState深入理解
setState 是 React 组件的核心功能之一，允许我们更新组件的状态并触发重新渲染。

1. 异步更新:

为了提高性能，React 可能会异步地执行 setState。因此，调用 setState 后，this.state 不会立即反映新的值。
这也意味着连续的 setState 调用可能会合并，导致只进行一次实际的更新。

2. 函数式更新:

当新的状态取决于前一个状态时，建议使用函数形式的 setState。

``` js
this.setState(prevState => ({ count: prevState.count + 1 }));
```

3. 批处理:

在某些事件处理函数中，React 会批处理多个 setState 调用，从而避免频繁的渲染。例如，多次连续调用 setState 通常会导致单次重新渲染。
但在某些异步代码（如 setTimeout 或 Promise 回调）中，React 不能自动批处理。

4. 回调函数:

因为 setState 可能是异步的，所以如果你想在更新状态后执行某些操作，可以提供一个回调函数。或在 componentDidUpdate 生命周期方法中进行。


``` JS
this.setState({ count: this.state.count + 1 }, () => {
    console.log('State updated:', this.state.count);
});
```

5. 同步 vs. 异步:

setState 并不真正是“异步”的，因为它不返回一个 promise，也不使用 JavaScript 的异步机制。更准确地说，React 控制了 setState 的执行时机，确保在必要时以最有效的方式更新组件。

简而言之，尽管 setState 的行为可能看起来像是异步的，但它实际上是由 React 的内部机制控制的，以确保以最有效的方式进行组件更新。

6. react 18
   在 React 18 中，新引入的并发模式进一步强化了 setState 的异步性，以及批量更新的策略。
React 18 为所有更新默认启用了批量处理。这意味着，当你在事件处理程序或生命周期方法中连续调用多次 setState，React 会将它们聚合成一个单一的更新，从而避免不必要的重新渲染。

要在 React 18+ 中实现同步效果，即在 setState 之后立即获取更新后的状态，你可以使用 React 的新并发模式与 flushSync 函数。但需要注意，过度使用 flushSync 可能会导致性能问题。

``` JS 
import { unstable_flushSync } from 'react-dom';

// ... your component code ...

handleIncrement = () => {
  unstable_flushSync(() => {
    this.setState({ count: this.state.count + 1 }, () => {
      console.log("After setState:", this.state.count);
    });
  });
};
```
7. 使用 hooks:

在函数组件中，你可以使用 useState 钩子来管理状态。它为状态提供了一个更简洁的 API，并与 setState 在类组件中的行为类似。

8. 面试题目

```js
import React, { Component } from 'react';

export default class App extends Component {
  state = {
    count: 0
  };

  handleIncrement = () => {
    console.log('increment setState前的count', this.state.count);
    this.setState({
      count: this.state.count + 1
    });
    console.log('increment setState后的count', this.state.count);
  };

  handleTriple = () => {
    console.log('triple setState前的count', this.state.count);
    this.setState({
      count: this.state.count + 1
    });
    this.setState({
      count: this.state.count + 1
    });
    this.setState({
      count: this.state.count + 1
    });
    console.log('triple setState后的count', this.state.count);
  };

  handleReduce = () => {
    setTimeout(() => {
      console.log('reduce setState前的count', this.state.count);
      this.setState({
        count: this.state.count - 1
      });
      console.log('reduce setState后的count', this.state.count);
    }, 0);
  };

  render() {
    return (
      <div>
        <div>{this.state.count}</div>
        <button onClick={this.handleIncrement}>点我增加</button>
        <button onClick={this.handleTriple}>点我增加三倍</button>
        <button onClick={this.handleReduce}>点我减少</button>
      </div>
    );
  }
}


```

