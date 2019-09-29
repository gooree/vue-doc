# Vue.js

Vue并非是一种新的编程语言，而是一种全新的MVVM编程框架。它使用的编程语言仍然是javascript，只不过与传统的前端编程方式发生了很大的变化。

一个Vue组件（或页面）由3个部分组成，分别是template、script和style。

## template(模板区)

定义视图模板，可包含普通html标签和antd组件标签

需要掌握如何对控件和数据进行绑定，如何在控件上定义事件响应方法。

## script(脚本区)

定义视图model和一系列生命周期及事件响应处理函数。

```
<script>
import xxx from xxx // 引入外部组件或函数

export default {
  name: 'xxx',  // 命名空间，必须全局唯一
  components: { // 本页面中引用的外部组件列表，可选
    ...
  },
  data () { // 视图对象view model，必须
    return {
      name: 'Hello world!',
      ...
    }
  },
  computed: { // 此处定义计算属性，一般只有比较复杂的页面会用到计算函数
    ... // 一般只有购物车、报表统计等页面中会用到，这些页面的特点是修改其中某个控件的值会引发其它的值产生变更
  },
  created () { // created生命周期函数，可选
    ... // 可以异步调用后台接口获取数据
  },
  mounted() { // mounted生命周期函数，可选
    ...  // 可以异步调用后台接口获取数据
  },
  methods: { // 此处定义事件响应函数，必须
    // 函数内部可以使用this访问当前组件的视图模型
    echo() {
      console.log(this.name)
      // 此处还可以访问Vue全局扩展方法
      this.$message.info('Welcome to China!')
    }
    ...
  }
}
</script>
```

## style(样式区)

定义当前页面的局部样式，公用样式建议使用独立的css文件。

## 学习要点

1. 学会面向对象的编程方式

- 变量声明

```before
var variable = 0; // 变量
var PI = 3.1415926; // 常量
```

```after
let variable = 0 // 变量
const PI = 3.1415926 // 常量
```

- 函数声明

```before
function echo (msg) {
  console.log(msg);
}

or

var echo = function(msg) {
  console.log(msg);
}
```

```after
echo: (msg) => console.log(msg) //使用箭头函数
```

- 数组遍历

```before
var array = ['a','b','c'];
for(var i=0;i<array.length;i++>) {
  console.log(array[i]);
}
```

```after
let array = ['a','b','c']
array.forEach(item => {
  console.log(item)
})

array.filter((item, index) => {
  if(index % 2 == 0) {
    return true
  }

  return false
})
```

2. 理解和传统面向dom编程的区别

- 不再人为操作dom元素

```@Deprecated
<script>
function rawjs() { // 原生js
  var ele = document.getElementById('#name');
  ... // 修改元素属性或样式
}

$(function() { // jQuery
  $('#id').css('display', 'none');
  $('#id').addClass('hidden');
  $('#id').removeClass('active');
  $('#parent').append('<span>动态添加</span>');
  $('#parent').html('...html元素');
  ...
})
</script>
```

- 一切通过view model绑定来实现

当有如下模板:
```
<div
  class="static"
  v-bind:class="{ active: isActive, 'text-danger': hasError }"
></div>
```

和如下data：
```
data: {
  isActive: true,
  hasError: false
}
```

结果渲染为：

```
<div class="static active"></div>
```