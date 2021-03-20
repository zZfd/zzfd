---
layout: post
title: "elementUi之elUpload"
date: 2021-03-21 07:14:03 +0800
categories: 所学
tags: 前端 vue
---

这篇总结下对 elementUi 库中文件上传组件的使用。

首先我很不喜欢“神话”已经封装好的组件库，也不需要向谁证明自己可不可以封装一个比较好的组件。如果需要，可以实现。在使用组件库和其他第三方库的时候，还是要点开他们的源码进行阅读，这不就是前端的魅力之一吗！活跃的社区，繁华的支持库。

**源码解析**<br />
该组件主要由 4 个小组件聚合而成。Upload-->UploadDragger, UploadList, ElProgress。
最最最根本的其实还是原生 input 文件上传。Upload.vue
{% highlight html %}
<input class="el-upload__input" type="file" ref="input" name={name} on-change={handleChange} multiple={multiple} accept={accept}></input>
{% endhighlight %}

文件拖拽上传的话是采用 H5 的拖拽事件实现的。UploadDragger 组件。
{% highlight html %}
<template>

  <div
    class="el-upload-dragger"
    :class="{
      'is-dragover': dragover
    }"
    @drop.prevent="onDrop"
    @dragover.prevent="onDragover"
    @dragleave.prevent="dragover = false"
  >
    <slot></slot>
  </div>
</template>
{% endhighlight %}

UploadList 组件是负责文件的展示。此组件是可以拿出来单独使用的，而且很好用！
{% highlight javaScript %}
const file = {uid:'', name:'', url:'', status:'', percentage:''}
{% endhighlight %}

<div align=center>
  <img src="{{site.baseurl}}/assets/res/03200101.jpg" width="800" alt="upload组件目录结构"/>
</div>

**使用总结**<br />
很早开始就不使用该组件的自动上传，因为在实际项目中需要定制上传的请求头，参数等等太多了。操作流程：文件选择直接手动上传，点击删除直接删除。

目前主要的策略是只绑定 on-remove 和 on-change 函数，注意是绑定，开始我也只是想当然地一直认为是事件监听哈哈。其他 on-success, on-error, on-progress 并没有设置，默认为空函数。on-preview 可以设置，自定义展示或者变为点击下载文件。

注意绑定的 on-change 函数会在 handleStart，handleSuccess, handleError 中调用，这三个都是组件自己封装的。input 标签只监听了 on-change 事件，组件在 on-change 事件触发后调用 handleStart-->绑定的 on-change 函数，文件上传后调用 handleSuccess 和 handleError。
{% highlight javaScript %}
<el-upload action="#" :on-remove="handleUploadRemove" :on-change="handleUploadChange(index)" list-type="picture" drag multiple :auto-upload="false">
</el-upload>
// :on-change="function(file,fileList){return handleUploadChange(file,fileList,index)}"
// 是不是比这样写优雅了很多哈哈
handleUploadChange(index) {
// 注意 this 的指向
return function(file, fileList) {
// 对文件的处理
// 校验文件的类型，大小是否合法
// 非法则可调用 fileList.pop()弹出该文件
// 上传文件，成功的处理，失败的处理
// 成功
// - 可根据返回结果直接修改 file 对象的属性值，name，url
// - 不推荐改变其 uid 属性，因为会涉及到展示组件的列表渲染，可设置 id 属性，用于文件的直接删除
}
}

// on-change 函数这样绑定是因为我们需要多传一个参数 index 用于在回调函数中使用
// 之所以能这样写，是因为在源码中是这样直接调用我们给的绑定函数。
// this.onChange(file, this.uploadFiles);
{% endhighlight %}

大概就是这些吧，在实际使用中可以多阅读源码，多输出信息看看。早点休息，狗头保命。还有什么 mixins 可以总结的，最后的最后我已经打算弃 Vue 使用 React 了，无关技术纯属喜好。
原文地址：<a href="https://zzfd.github.io/2021/03/20/elementUi之elUpload">elementUi 之 elUpload</a>
