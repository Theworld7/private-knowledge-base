# Vue.js3的设计思路

## 声明式地描述 UI
Vue.js3 的声明式解决方案：
1. 使用与 HTML 标签一致的方式来描述 DOM 元素；
2. 使用与 HTML 标签一致的方式来描述属性；
3. 使用 `:` 或 `v-bind` 来描述动态绑定的属性；
4. 使用 `@` 或 `v-on` 来描述事件；
5. 使用与 HTML 标签一致的方式来描述层级结构。

除了使用模版描述，还可以使用对象描述真实 DOM 的数据结构：

```javascript
const title = {
	// 标签名称
	tag: 'h1',
	// 标签属性
	props: {
		onClick: handler
	},
	// 子节点
	children: [
		{ tag: 'span' }
	]
}
```
对象相较于模版描述 UI 更加灵活。使用对象描述就是虚拟 DOM 描述。

```javascript
import { h } from 'vue'

export default {
	render() {
		return h('h1', { onClick: handler }) // 虚拟DOM
	}
}

// h 函数返回一个对象
return {
	tag: 'h1',
	props: { onClick: handler }
}
```
一个组件要渲染的内容是通过渲染函数 render 来描述的，Vue.js 会根据组件的 render 函数的返回值拿到虚拟 DOM，然后就可以把组件的内容渲染出来。

## 初识渲染器

渲染器的作用就是把虚拟 DOM 渲染为真实 DOM 。

```javascript
function renderer(vnode, container) {
	// 使用 vnode.tag 作为标签名称创建 DOM 元素
	const el = document.createElement(vnode.tag)
	// 遍历 vnode.props 将属性、事件添加到 DOM 元素
	for (const key in vnode.props) {
		if (/^on/.test(key)) {
			// 如果 key 以 on 开头，说明它是事件
			el.addEventListener(
				// 事件名称 onClick --- click
	        	key.substr(2).toLowerCase(),
	        	vnode.props[key] // 事件处理函数
      		)
    	}
	}

	// 处理 children
	if (typeof vnode.children === 'string') {
		// 如果 children 是字符串，说明它是元素的文本子节点
		el.appendChild(document.createTextNode(vnode.children))
	} else if (Array.isArray(vnode.children)) {
		// 递归地调用 renderer 函数渲染子节点，使用当前元素 el 作为挂载点
		vnode.children.forEach(child => renderer(child, el))
	}

	// 将元素添加到挂载点下
	container.appendChild(el)
}

renderer(vnode, document.body)
```

### 实现思路

- 创建元素：把 `vnode.tag` 作为标签名称来创建DOM元素。

- 为元素添加属性和事件：遍历 `vnode.props` 对象，如果 key 以 on 字符开头，说明它是一个事件，把字符 on 截取掉后再调用 toLowerCase 函数将事件名称小写化，最终得到合法的事件名称，最后调用 addEventListener 绑定事件处理函数。

- 处理children：如果 children 是一个数组，就递归地调用 `renderer` 继续渲染，把刚刚创建的元素作为挂载点；如果 children 是字符串，则使用 createTextNode 函数创建一个文本节点，并将其添加到新创建的元素内。

## 组件的本质
虚拟DOM还可以用来描述组件。组件就是一组 DOM 元素的封装。可以定义一个对象来代表组件，对象有一个 render 函数返回值就代表要渲染的内容。

```javascript
const myComponent = {
	render() {
		return {
			tag: 'div',
			props: {
				onClick: () => alert('hello')
			},
			children: 'click me'
		}
	}
}
```
让虚拟 DOM 对象中的 tag 属性存储组件函数：

```javascript
const vnode = {
	tag: MyComponent
}
```
渲染器需要支持组件渲染：

```javascript
function renderer(vnode, container) {
	if (typeof vnode.tag === 'string') {
		// 说明 vnode 描述的是标签元素
		mountElement(vnode, container)
	} else if (typeof vnode.tag === 'object') {
		// 说明 vnode 描述的是组件
		mountComponent(vnode, container)
	}
}
```
`mountELement` 函数与上文 `renderer` 函数相同。

`mountComponent` 函数：

```javascript
function mountComponent(vnode, container) {
	// 调用组件函数
	const subtree = vnode.tag.render()
	// 递归地调用 renderer 渲染 subtree
	renderer(subtree, container)
}
```

## 模版的工作原理
### 编译器

编译器的作用就是将模版编译为渲染函数并添加到 `script` 标签块的组件对象上。

### Vue.js 页面渲染流程

对于一个组件来说，它要渲染的内容最终都是通过渲染函数产生的，然后渲染器再把渲染函数返回的虚拟 DOM 渲染为真实 DOM 。

### Vue.js 是各个模块组成的有机整体

编译器有能力分析动态内容，并在编译阶段把这些信息提取出来，然后直接交给渲染器，渲染器根据这些信息可以轻松找到变量并只更新变化的内容。

编译器和渲染器之间通过虚拟 DOM 对象进行信息交流，他们互相配合使得性能进一步提升。