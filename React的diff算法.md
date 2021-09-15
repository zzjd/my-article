- 今天面试官问了我一个问题：如果没有 id，React 如何知道 DOM 节点是否改变？这个问题我没答出来，没有想到原来是问 diff 算法，而且今天的面试体验挺差的，三道题都没打出来，感觉对自己打击很大，知耻而后勇，我一定可以化打击为动力，变得更强！！！

* 接下来说说 diff 算法

  - 首先，我们要明确 diff 算法是比较同一层次的一组子节点，他们通过唯一的标识 id 区分。React 通过 updateDepth 对 Virtual DOM 树进行层级控制。

  * 先比较节点类型是否相同，如果不同，直接删去旧的节点和它的子节点，创建新的节点。如果父节点不同，react 不会再去比对子节点。如果组件 D 和组件 G 的结构相似，但是 React 判断是 不同类型的组件，则不会比较其结构，而是删除 组件 D 及其子节点，创建组件 G 及其子节点。打个比方，如果写了一个`<div><List /></div>`（List 是一个有几千个节点的组件），切换的时候变成了`<section><List /></section>`，此时即使 List 的内容不变，它也会先被卸载在创建，其实是很浪费的。这样就给我们的性能优化提供了一个思路，就是我们要 **保持 DOM 标签的稳定性** 。
  * 如果节点类型相同，有两种情况，DOM 元素类型和自定义组件
    - 对于 DOM 元素类型，对比他们的属性，只改变需要改变的属性，比如：

  ```javascript
  <div className="before" title="stuff" />
  <div className="after" title="stuff" />
  ```

  react 会只更新 className 的值

  ```javascript
  <div style={{color: 'red', fontWeight: 'bold'}} />
  <div style={{color: 'green', fontWeight: 'bold'}} />
  ```

  react 会只更新 color 的值

  - 对于自定义组件，由于 React 此时并不知道如何去更新 DOM 树，因为这些逻辑都在 React 组件里面，所以它能做的就是根据新节点的 props 去更新原来根节点的组件实例，触发一个更新的过程，最后在对所有的 child 节点在进行 diff 的递归比较更新。 - shouldComponentUpdate - componentWillReceiveProps - componentWillUpdate - render - componentDidUpdate

  * 子节点比较，下面的例子：

  ```javascript
  <div>
    <A />
    <B />
  </div>
  // 从列表一到列表二
   <div>
    <A />
    <C />
    <B />
  </div>
  ```

  如果没有 key，react 会将 B 删掉，新建一个 C 放在 B 的位置，然后新建一个 B 放在尾部，生命周期如下：

```javascript
// 列表一
A is created
A render
B is created
B render
A componentDidMount
B componentDidMount
//列表二
A render
B componentWillUnmount
C is created
C render
B is created
B render
A componentDidUpdated
C componentDidMount
B componentDidMount
```

当节点很多的时候，这种做法是很低效的，有两种方法解决这个问题： 1.**保持 DOM 结构的稳定性**，上面的例子中，我们可以通过增加一个 null，保持 DOM 结构的稳定，这样在顺序对比的时候，B 节点不会被卸载了又重装回来。2.**key**，通过给节点配置 key，react 可以识别节点是否存在，生命周期如下：

```javascript
A render
C is created
C render
B render
A componentDidUpdate
C componentDidMount
B componentDidUpdate
```

key 值必须是稳定（所以我们不能用 Math.random()去创建 key），可预测，并且唯一的。
