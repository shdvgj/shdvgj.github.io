---
title: vue笔记
date: 2023-12-02 08:36:12
tags:
---

### Vue相比传统js的优点

Vue.js相比传统的JavaScript有以下几个优点：
1. 响应式数据绑定：Vue.js采用了响应式的数据绑定机制，可以将数据和DOM元素进行关联，当数据发生变化时，DOM会自动更新，减少了手动操作DOM的繁琐过程。
2. 组件化开发：Vue.js鼓励组件化开发，将UI界面拆分成独立、可复用的组件，每个组件负责自己的逻辑和视图，更易于维护和复用。
3. 虚拟DOM：Vue.js使用虚拟DOM（Virtual DOM）来优化性能。当数据变化时，Vue.js会生成一个新的虚拟DOM树与旧的虚拟DOM树进行比较，然后只更新变化的部分，减少了直接操作实际DOM的开销。
4. 双向数据绑定：Vue.js支持双向数据绑定，即数据的变化会实时反映到视图中，同时用户对视图的操作也可以自动更新数据。
5. 生态系统丰富：Vue.js拥有庞大而活跃的社区，提供了许多第三方插件和库，例如Vue Router用于路由管理，Vuex用于状态管理，Vue CLI用于快速搭建项目等，这些工具和插件可以极大地提升开发效率。
6. 简单易学：Vue.js具有较低的学习曲线，文档清晰易懂，上手相对容易，适合初学者快速掌握。

<!--more-->
### 什么是MVVM

MVVM是一种软件架构模式，它代表着Model-View-ViewModel（模型-视图-视图模型）。MVVM模式主要用于构建用户界面（UI）和业务逻辑的分离，使代码更加模块化、可维护和可测试。
下面是MVVM模式中各个组成部分的简要解释：
1. Model（模型）：
   模型代表应用程序的数据和业务逻辑。它包括从数据源获取数据、对数据进行操作和处理的方法，以及数据的验证和校验逻辑等。
2. View（视图）：
   视图是用户界面的可视化部分，负责展示数据和与用户进行交互。它通常是由HTML、CSS和其他前端技术来创建的，例如Web页面、移动应用程序的界面等。
3. ViewModel（视图模型）：
   视图模型是连接视图和模型之间的桥梁。它负责将模型中的数据转换为视图中可以展示的形式，并监听视图上的用户操作。视图模型包含了视图所需的数据、命令和方法，以及处理用户输入和对数据的操作等逻辑。
在MVVM模式中，视图和视图模型之间通过数据绑定建立了双向的关系。当模型的数据发生变化时，视图模型会通知视图进行更新，反之亦然。这种双向数据绑定使得当数据变化时，视图会自动更新，用户的操作也能够同步更新到数据模型中。
MVVM模式的一个重要特点是将界面逻辑和业务逻辑分离，使得开发人员可以专注于不同的任务。视图模板（View）主要关注界面的展示和用户交互，视图模型（ViewModel）主要负责处理数据和交互逻辑，而模型（Model）则专注于数据的获取和处理。这种分离使得代码更易于维护、测试和重用。
MVVM模式最初是由微软提出的，主要应用于WPF（Windows Presentation Foundation）和Silverlight等框架。后来，由于其优势和可扩展性，MVVM模式也在其他前端框架和技术中得到了广泛应用，例如Vue.js和Knockout.js等。

### Vue基本开发

#### 一个demo
```html
<!DOCTYPE html>
<html>
<head>
  <title>Vue.js Demo</title>
  <script src="https://cdn.jsdelivr.net/npm/vue@2.6.14/dist/vue.js"></script>
</head>
<body>
  <div id="app">
    <h1>{{ message }}</h1>
    <input v-model="inputText" type="text">
    <p>You entered: {{ inputText }}</p>
    <button v-on:click="changeMessage">Change Message</button>
  </div>

  <script>
    // 创建一个Vue实例
    var app = new Vue({
      el: '#app',
      data: {
        message: 'Hello, Vue!',
        inputText: ''
      },
      methods: {
        changeMessage: function() {
          this.message = 'New Message';
        }
      }
    });
  </script>
</body>
</html>
```
在这个示例中，我们使用`<div>`元素和`id="app"`来定义Vue实例的挂载点。然后，我们在Vue实例的`data`选项中定义了两个属性：`message`和`inputText`。`message`属性用于展示消息，`inputText`属性与一个输入框进行双向数据绑定。
在模板中，我们使用双大括号（`{{ }}`）来插入Vue实例中的数据，例如`{{ message }}`和`{{ inputText }}`。我们还使用了`v-model`指令将输入框和`inputText`属性进行绑定，实现了双向数据绑定。
在按钮上，我们使用了`v-on:click`指令监听点击事件，并在方法`changeMessage`中更新了`message`属性的值。
以上示例演示了Vue.js中常用的几个概念，包括数据绑定、事件监听和方法定义。你可以根据需要扩展和修改这个示例，进一步学习和探索Vue.js的功能和用法。

#### Vue生命周期

Vue.js生命周期指的是Vue实例在创建、更新和销毁过程中经历的一系列钩子函数，这些钩子函数允许开发者在特定阶段执行自定义的代码逻辑。Vue生命周期分为以下几个阶段：
1. 创建阶段（Creation）：
   - beforeCreate：在实例初始化之后，数据观测（data observation）和事件配置（event & watcher setup）之前调用。此时，实例的属性和方法还未被初始化。
   - created：在实例创建完成后被调用。此时，实例已完成数据观测、属性和方法的设置，但尚未开始DOM编译和挂载。
2. 挂载阶段（Mounting）：
   - beforeMount：在挂载开始之前被调用。在此阶段，Vue实例的模板编译已完成，但尚未将模板渲染到真实的DOM中。
   - mounted：在挂载完成后被调用。此时，Vue实例已经将模板渲染到了DOM中，并且可以进行DOM操作。
3. 更新阶段（Updating）：
   - beforeUpdate：在数据更新导致重新渲染之前被调用。此时，虚拟DOM已经重新渲染并将更新前后的差异进行了计算，但尚未应用到真实的DOM中。
   - updated：在数据更新并重新渲染DOM后被调用。此时，Vue实例的数据已经更新，DOM也已经更新完成，可以进行操作。
4. 销毁阶段（Destroying）：
   - beforeDestroy：在实例销毁之前调用。此时，Vue实例仍然完全可用，可以执行一些清理工作，如解绑自定义事件、清除定时器等。
   - destroyed：在实例销毁后调用。此时，Vue实例的所有指令和事件监听器都已被移除，不再可用。
此外，Vue还提供了一些其他的生命周期钩子函数，如activated和deactivated，用于处理Vue实例在组件切换时的特定行为。
通过这些生命周期钩子函数，开发者可以在不同的阶段执行自定义的代码逻辑，从而实现对应的操作和处理，例如初始化数据、发送网络请求、订阅事件、销毁资源等。生命周期钩子函数使得开发者可以更好地控制Vue实例的行为，并在需要时进行必要的操作和处理。

### Vue filter

Vue的filter用于在模板中对数据进行格式化显示。它可以将原始数据经过一系列的处理，返回处理后的结果。使用Vue的filter可以在模板中直接调用，使得数据的格式化操作更加方便和可维护。
下面是使用Vue的filter的步骤：
1. 定义过滤器：在Vue实例的`filters`选项中定义过滤器函数。过滤器函数接受一个输入值（原始数据）和一些可选的参数，并返回处理后的结果。可以通过全局Vue对象的`filter`方法来定义全局过滤器，或者在组件的选项中定义本地过滤器。
   全局过滤器的定义示例：

   ````javascript
   Vue.filter('capitalize', function(value) {
     if (!value) return '';
     return value.charAt(0).toUpperCase() + value.slice(1);
   });
   ```
   本地过滤器的定义示例：
   ````javascript
   filters: {
     capitalize(value) {
       if (!value) return '';
       return value.charAt(0).toUpperCase() + value.slice(1);
     }
   }
   ```
2. 在模板中使用过滤器：在模板中使用管道符 `|` 将数据与过滤器连接起来。可以在模板中的插值表达式、指令和绑定等地方使用过滤器。
   示例：
   ````html
   <div>{{ message | capitalize }}</div>
   ```
   上面的示例中，`message`是Vue实例中的一个数据属性，通过`capitalize`过滤器对它进行处理后显示。
   过滤器还可以接受参数。例如，可以在模板中传递参数给过滤器函数：
   ````html
   <div>{{ message | capitalize('first') }}</div>
   ```
   在上面的示例中，将字符串'first'作为参数传递给`capitalize`过滤器函数。
需要注意以下几点：
- 过滤器可以链式调用，多个过滤器会按顺序依次处理数据。
- 过滤器可以在Vue实例和组件的计算属性中使用，以便在数据的处理过程中重用。
- 过滤器是非变异的，即它们不会改变原始数据，而是返回新的处理结果。
- 过滤器函数可以定义在其他文件中，然后通过模块导入的方式使用。
通过使用Vue的filter，你可以方便地在模板中对数据进行格式化，使得数据展示更加清晰和易读。