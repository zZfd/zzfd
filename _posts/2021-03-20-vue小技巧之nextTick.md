---
layout: post
title: "vue小技巧之nextTick"
date: 2021-03-20 21:00:03 +0800
categories: 所学
tags: 前端 vue
---

忙着面试和准备软件设计师，博客落下了好多，还有点偷懒了哈哈。接着沉淀，先向中级工程师看齐！最重要的还是要有颗想学习的心，学得多了，沉淀下来，做啥都能出彩。

这篇简单地记录下在使用 vue 时的小技巧—nextTick。<br />
**nextTick**<br />
在下次 DOM 更新循环结束之后执行延迟回调。在修改数据之后立即使用这个方法，获取更新后的 DOM。

**使用方法**<br />
{% highlight javaScript %}
this.$nextTick(function(){// 更新了 dom})
vue2.1.0 后返回了 Promise 对象，所以可以使用.then()或者结合 async await 语法糖使用
{% endhighlight %}

**使用场景**<br />
在弹出对话框的同时通过 ref 获取内嵌表格的实例。需等待此次 DOM 更新完毕，表格组件被挂载到父组件上才能正确获取。这时候就需要结合 this.$nextTick使用。
{% highlight javaScript %}
    async onOpenDialog() {
      this.model.visible = true
      // undefined
      console.log(this.$refs.form)
setTimeout(function() {
// window
console.log(this)
}, 100)
this.$nextTick(function() {
        // parent vm 有疑惑吗？
        console.log(this)
        // form vm
        console.log(this.$refs.form)
})
await this.$nextTick()
      // form vm
      console.log(this.$refs.form)
this.$nextTick().then((vm)=>{
      // parent vm
      console.log(vm)
      // form vm
      	console.log(this.$refs.form)
})
}
{% endhighlight %}

**源码解析**<br />
{% highlight javaScript %}
function nextTick (cb, ctx) {
var _resolve;
// 放入回调函数，等待 DOM 重新渲染完毕后执行
callbacks.push(function () {
if (cb) {
try {
// 修改执行上下文，指向当前页面实例
// 所以在我们没有使用箭头函数的前提下，this 指向仍然正确
cb.call(ctx);
} catch (e) {
handleError(e, ctx, 'nextTick');
}
} else if (_resolve) {
_resolve(ctx);
}
});
if (!pending) {
pending = true;
timerFunc();
}
// $flow-disable-line
    if (!cb && typeof Promise !== 'undefined') {
      return new Promise(function (resolve) {
        _resolve = resolve;
      })
    }
    }
// 原型链上，挂载此方法
Vue.prototype.$nextTick = function (fn) {
//参数 1：回调函数，参数二：页面实例执行上下文
return nextTick(fn, this)
};
{% endhighlight %}

原文地址：<a href="https://zzfd.github.io/2021/03/20/vue小技巧之nextTick">vue 小技巧之 nextTick</a>
