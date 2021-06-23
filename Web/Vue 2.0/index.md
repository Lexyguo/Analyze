# 一、vue 初始化流程
### 1、初始化函数框架
- new Vue()
  - _init()
- $mount()
  - mountComponent()
    - updateComponent()
      - render()
      - update()
    - new Watcher()

### 2、获取渲染函数

> 获取模板并编译 => 获取渲染函数
> 
> 编译顺序 el < template < render
>
> 执行挂载

### 3、实例方法的初始化

| 函数 | 执行功能 |
|:--:|:--:|
| initMixin(Vue) | 混入_init() |
| stateMixin(Vue) | \$set/\$delete/\$watch |
| eventsMixin(Vue) | \$emit/\$on/\$off/\$once |
| lifecycleMixin(Vue) | 生命周期相关方法\$_update/\$forceUpdate |
| renderMixin(Vue) | \$nextTick/\_render() |

### 4、核心初始化逻辑【initMixin(Vue)】

| 函数 | 执行功能 |
|:--:|:--:|
| initLifecycle(vm) | \$parent/\$root/\$children |
| initEvents(vm) | 事件监听 |
| initRender(vm) | slots/$createElement() |
| callHook(vm, 'beforeCreate') | 组件创建之前的钩子 |
| initInjections(vm) | 注入祖辈传递的数据 |
| callHook(vm, 'beforeCreate') | 组件创建之前的钩子 |
| initState(vm) | 重要： 组件数据初始化，包括props/data/methods/computed/watch |
| initProvide(vm) | 初始化provide的值并赋值给_provided属性 |
| callHook(vm, 'created') | 组件创建完成的钩子 |

### 5、图示
  
![流程图](https://res.kaikeba.com/other/123/20200709071034-16035/FohlZ_H-UapUJxtTqJy-LtfZPdOC.png)
