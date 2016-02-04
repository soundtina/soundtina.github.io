---
title: Vue-loader 开启压缩后的一些坑
date: 2016-02-04 16:44:25
tags:
desc: vue-loader 压缩代码丢失<td> 和 type="text" 属性
---
在使用vue-loader 配合webpack 对.vue文件进行加载的时候，如果开启了代码压缩会出来下面
几种问题，做个记录。
- 丢失td结束标记，导致页面的布局错乱
- input的属性type为text 时会被删了
- `<input ... checked="{check('id')}" />`这个表达式会被压成 `<input ... checked />`
<!-- more -->

## 丢失td结束标记

```html
<table>
    <tr>
        <td>1</td>
        <td>2</td>
        <td>3</td>
        <td>4</td>
    </tr>
</table>
```

**最终压成:**

```html
<table>
    <tr>
        <td>1
        <td>2
        <td>3
        <td>4
    </tr>
</table>
```
<div class="tip">
    这样就会造成页面的布局混乱
</div>

**解决方法:**
```js
//webpack.config.js配置
//设置vue-html-loader中的参数removeOptionalTags=false
module:{
    ....
},
vue: {
        loaders: {
            html: 'vue-html-loader?removeOptionalTags=false',
        }
    }
//hell
```
## type="text"会被删了

```html
压缩前
<input type="text" />
压缩后
<input />
```
<div class="tip">
如果有`.text`这样的选择器，就会失效
</div>

**解决方法:**
```js
//和上面类似加上removeRedundantAttributes=false
module:{
    ....
},
vue: {
        loaders: {
            html: 'vue-html-loader?removeRedundantAttributes=false',
        }
    }
```

## checked="xxxx"被压缩为checked

```html
压缩前
<input type="checkbox" checked="{checkRole('id')}" />
压缩后
<input type="checkbox" checked/>
```
<div class="tip">
    这会导致所有的checkbox都被选中，所绑定的判断方法直接被删除了
</div>
**解决方法可以有两个:**
1.跟上面一样:设置参数让vue-html-loader不要去截断这个
```js
html: 'vue-html-loader?collapseBooleanAttributes=false'
```
但是这个会带来另外的问题：如果你自自定义控件中也用了checked/multiple 这种默认的属性，
它会自动给他补全了。如：
```html
压缩前
<slef-component multiple />
你在slefComponent里面定义的prop.multiple 是一个bool类型
压缩后
<slef-component multiple="multiple"/>
这里直接导致了程序的出错
```
2.第二种方法可以避免这种情况
**不修改vue-html-loader的collapseBooleanAttributes**
```html
该用v-bind来绑定控件(自定义控件，原生控件)的属性
<input type="checkbox" v-bind:checked="checkRole('id')" />
<slef-component multiple />
```
