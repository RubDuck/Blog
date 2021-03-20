#### vue数组变异

```javascript
cosnt arrayProto = Array.prototype;
export const arrayMethods = Object.create(arrayProto);
const methodsToPath = [
    "push",
    "pop",
    "unshift",
    "splice",
    "sort",
    "reverse",
];
methodsToPath.forEach(function(method){
    const original = arrayProtop[method];
    def(arrayMethods, method, function(){
        
    })
})
```

#### Vue 

```javascript
initMixin(Vue)
stateMixin(Vue)
eventsMixin(Vue)
lifecycleMixin(Vue)
renderMixin(Vue)

export default Vue
```

######  initMixin(Vue)

```javascript
Vue.prototype._init = function (options) {} 
```

###### stateMixin(Vue)

```javascript
Vue.prototype.$data 
Vue.prototype.$props 
Vue.prototype.$set = set 
Vue.prototype.$delete = del 
Vue.prototype.$watch = function(){} 
```

###### eventsMixin(Vue)

```javascript
Vue.prototype.$on 
Vue.prototype.$once 
Vue.prototype.$off 
Vue.prototype.$emit
```

###### lifecycleMixin(Vue)

```javascript
Vue.prototype._update 
Vue.prototype.$forceUpdate 
Vue.prototype.$destroy 
```

###### renderMixin(Vue)

```javascript
Vue.prototype.$nextTick 
Vue.prototype._render 
Vue.prototype._o = markOnce 
Vue.prototype._n = toNumber 
Vue.prototype._s = toString 
Vue.prototype._l = renderList 
Vue.prototype._t = renderSlot
Vue.prototype._q = looseEqual 
Vue.prototype._i = looseIndexOf 
Vue.prototype._m = renderStatic 
Vue.prototype._f = resolveFilter 
Vue.prototype._k = checkKeyCodes 
Vue.prototype._b = bindObjectProps 
Vue.prototype._v = createTextVNode 
Vue.prototype._e = createEmptyVNode 
Vue.prototype._u = resolveScopedSlots 
Vue.prototype._g = bindObjectListeners
```

#### Vue-router实现

###### hash

```
location.hash = ''
```

* '#' 加上对应的跳转路径，会在浏览器中添加一条记录，并且不会发生刷新。
* hashchange监听

###### history

* window.history.pushState()
* window.history.replaceState()
* popstate事件监听url变化

#### Vuex

###### state

###### getters

###### mutations

###### actions

###### modules





#### Directive

```
export default { 
  bind: (el, binding) => { /** dom插入时调用, dom树绘制前调用 **/ },
  componentUpdated：(el. binding) => { /** 组将更新调用， 更新后状态 **/ }
  unbind: (el) => {},
}
```

###### binding

* name
* value
* oldValue
* expression
* arg
* modifiers

###### update 和 componentUpdated

共同点：

* 组件更新都会调用，update在componentUpdated之前。

不同点： 

* update 组件更新前的状态， componentUpdated 组件更新后的状态。

###### bind 和 inserted

共同点： 

* dom插入都会调用，bind在inserted之前

不同点： 

* bind 时父节点为 null  
* inserted 时父节点存在。
* bind是在dom树绘制前调用，inserted在dom树绘制后调用

#### Vue - hookEvent

在vue 组件中，可以用$on，$once去监听所有生命周期钩子函数，如监听组件的updated钩子函数可以写成

```
this.$on('hook:updated', () => {});
```

#### Vue - observable

数据在各个组件之间进行传递共享，一般而言，我们需要一个管理工具。比如常用的Vuex，当然observable对象也是可以的

```javascript
import Vue from 'vue';
export const store = Vue.observable({
  userInfo: {},
  roleIds: [],
});
export const mutations = {
  setUserInfo(userInfo) {
    store.userInfo = userInfo;
  },
  serRoleIds(roleIds) {
    store.roleIds = roleIds;
  },
};
```

```javascript
<template>
  <div>
    {{ userInfo.name }}
  </div>
</template>
<script>
import { store, mutations } from '../store'
export default {
  computed: {
    userInfo() {
      return store.userInfo
    }
  },
  created() {
    mutations.setUserInfo({
      name: '子君'
    })
  }
}
</script>
```

#### Vue - extend

```javascript
import Alert from '../src/components/Toast.vue';
import Vue from 'vue';

let Toast = {};

Toast.install = function(Vue, options) {
    if (document.getElementsByClassName('toast').length) {
        return;
    }
    let toastTpl = Vue.extend(Alert); // 创建Vue构造器
    let $vm = new toastTpl(); // 创建Vue实例
    let tpl = $vm.$mount().$el; // 挂载
    document.body.appendChild(tpl);
    Vue.prototype.$toast = {
        show(options) {
            if (typeof options === 'string') {
                $vm.text = options;
            }
            if (typeof options === 'object') {
                Object.assign($vm, options);
            }
            $vm.show = true;
            setTimeout(() => {
                $vm.show = false;
            }, $vm.showTime);
        },
        hide() {
            $vm.show = false;
        },
    };
};

export default Toast;

```

#### Vue - watch

###### 深度监听

```
  watch: {
    // 在值发生变化之后，重新加载数据
    formData: {
      // 需要注意，因为对象引用的原因， newValue和oldValue的值一直相等
      handler(newValue, oldValue) {
        // 在这里标记页面编辑状态
      },
      // 通过指定deep属性为true, watch会监听对象里面每一个值的变化
      deep: true
    }
  }
```

###### 自定义watch

```javascript
  // 模拟异步请求数据
    $_loadData() {
      setTimeout(() => {
        // 先赋值
        this.formData = {
          name: '子君',
          age: 18
        }
        // 等表单数据回填之后，监听数据是否发生变化
        const unwatch = this.$watch(
          'formData',
          () => {
            console.log('数据发生了变化')
          },
          {
            deep: true
          }
        )
        // 模拟数据发生了变化
        setTimeout(() => {
          this.formData.name = '张三'
        }, 1000)
      }, 1000)
    }
  }

```

this.$watch可以主动触发变量的watch监听，它返回的是一个函数值，如果需要终止这种监听，只需要在适当的时机再次调用返回的方法即可。

#### Vue - if - show

```
// 计算属性
computed: {
  filterList: function () {
  return this.showData.filter(function (data) {
    return data.isShow
  })
}
  
// DOM
  
<ul>
  <li v-for="item in filterList" :key="item.id">
  {{ item.name }}
  </li>
</ul>
```

当我们又说会通过某个列表中的属性值去判断渲染过程时，可以通过计算属性首先优化当前数组列表

#### 事件冒泡和默认行为

###### **preventDefault**

作用是取消一个目标元素的默认行为

```
event.preventDefault()
```

###### **stopPropagation**

作用是阻止目标元素的冒泡事件，但是会不阻止默认行为

```
event.stopPropagation()
```

#### 修饰符

###### .stop

```
阻止冒泡
```

###### .prevent

```
阻止默认行为
```

###### .once

```
仅仅触发一次
```

###### .sync

```javascript
编译语法糖类似于v-model

父组件：
<comp :foo.sync="bar"></comp>
<comp :foo="bar" @update:foo="val => bar = val"></comp>

子组件：
this.$emit('update:foo', newVal)
```
## 特殊笔记

#### VNode

###### 构造函数

```javascript
function VNode(
    tag, data, children, 
    text, elm, context, 
    componentOptions
) {    
    this.tag = tag; // 标签名
    this.data = data;    
    this.children = children; // 子元素
    this.text = text; // 文本内容
    this.elm = elm; // Dom 节点
    this.context = context;    
    this.componentOptions = componentOptions;    
    this.componentInstance = undefined;    
    this.parent = undefined;    
    this.isStatic = false; // 是否静态节点
    this.isComment = false; // 是否是注释节点
    this.isCloned = false; // 是否克隆节点
};
```

example

```html
<div class="parent" style="height:0" href="2222">
    111111
</div>

#######  VNODE
{    
    tag: 'div',    
    data: {        
        attrs:{href:"2222"}
        staticClass: "parent",        
        staticStyle: {            
            height: "0"
        }
    },    
    children: [{        
        tag: undefined,        
        text: "111111"
    }]
}
```

###### VNODE生成

渲染函数

```javascript
function (){ 
    with(this){  
        return _c('div',{attrs:{"href":"xxxx"}},["1111"]).
    } 
}
```

_c

```
vm._c = function(a, b, c, d) {    
    return createElement(vm, a, b, c, d, false);
};
```

createElement

```javascript
function createElement(
    context, tag, data, 
    children, normalizationType
) {    
    var vnode;    
    if (tag是正常html标签) {
        vnode = new VNode(
            tag, data, children, undefined, 
            undefined, context
        );
    } 
    else if (tag 是组件) {
        vnode = createComponent(
            Ctor, data, context, 
            children, tag
        );
    }    
    return vnode
}
```

