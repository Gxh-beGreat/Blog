---
title: VueJs - 高阶组件实践
date: 2017-07-06 15:50:32
categories:
- JavaScript
tags:
- JavaScript
- VueJS
---
-----

> VueJs - 高阶组件实践

-----

### Higher-Order Components (hoc)
[Higher-Order Components](https://facebook.github.io/react/docs/higher-order-components.html) 高阶组件是 [React](https://facebook.github.io/react/) 的一种高级应用，确切的说，高阶组件其实只是一个方法( `function` )，它接受一个组件，处理并返回另一个新组件
```javascript
const EnhancedComponent = higherOrderComponent(WrappedComponent)
```

### 使用场景
探讨前我们先理下需求，在 `组件化` 开发中，我们往往会遇到这样一种情况：完全不同的两个组件，可能会需要拥有一个或N个相同的功能，我们以 `input number` 为例，在按钮点击时我们需要让其 `console` 当前按钮的 `counter data`，并在按钮前面加上 `hoc-` 的字眼，这时我们有两种选择：
1. 直接新建一个按钮组件，让其包含以上功能
2. 使用 `hoc` 函数处理组件，并返回新组件供使用

为遵循 [DRY (Don't repeat yourself)](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself) 规则，我们选择 `hoc` 来实现改需求

### 按钮组件
```javascript
// add-btn.js
<template lang="html">
  <span class="add-btn" @click="handleClick">add: {{counter}}</span>
</template>

<script>
export default {
  name: 'add-btn',
  data () {
    return {
      counter: 0
    }
  },
  methods: {
    handleClick () {
      this.counter++
      this.$emit('change', 1)
    }
  }
}
</script>
```

```javascript
// reduce-btn.js
<template lang="html">
  <span class="reduce-btn" @click="handleClick">reduce: {{counter}}</span>
</template>

<script>
export default {
  name: 'reduce-btn',
  data () {
    return {
      counter: 0
    }
  },
  methods: {
    handleClick () {
      this.counter--
      this.$emit('change', -1)
    }
  }
}
</script>
```

### HOC 实践
```javascript
export default function hoc (container) {
  return {
    name: 'hoc',
    props: {
      ...container.props
    },
    components: {
      container
    },
    methods: {
      handleChange (data) {
        this.$emit('change', data)
        const { $options, counter } = this.$children[0]
        console.log(`%s's counter is %d`, $options.name, counter)
      }
    },
    render (h) {
      const data = {
        props: this.$props
      }
      return (
        <div>
          hoc-<container onChange={this.handleChange} {...data}/>
        </div>
      )
    }
  }
}
```

```javascript
// App.vue
<template>
  <div id="app">
    <add-btn @change="handleChange" a="b"/>
    <reduce-btn @change="handleChange"/>
    <div>total: {{total}}</div>
  </div>
</template>

<script>
import hoc from './hoc.js'
import AddBtn from './AddBtn.vue'
import ReduceBtn from './ReduceBtn.vue'

export default {
  name: 'app',
  components: {
    'add-btn': hoc(AddBtn),
    'reduce-btn': hoc(ReduceBtn)
  },
  data () {
    return {
      total: 0
    }
  },
  methods: {
    handleChange (number) {
      this.total += number
    }
  }
}
</script>

```
