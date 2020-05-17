---
title: 基于antd官网方案的换肤
categories: React
tag: antd
---

#### [rc-color-picker](rc-color-picker)

- 一款社区拾色器组件，也是antd官方推荐的

#### 安装

```
npm install --save rc-color-picker
```
#### 加载

```
import ColorPicker from 'rc-color-picker';

//注意引入样式
import 'rc-color-picker/assets/index.css';      
```
#### 回调函数onChange，注意返回的是一个对象

```
handleColorChange = result => {
    console.log(result.color);
}

render(){
    return (
        <ColorPicker
            style={{
                margin: 'auto'
            }}
            animation="slide-up"
            color={'#36c'}
            onChange={handleColorChange}
        />
    )
}
```
#### 基于less结合rc-color-picker实现的换肤

- 故事是这样的，在一个月黑风高的夜晚，有一个需求是换色，看了antd的官网就支持随机取色然后替换，那么开始谷歌折腾起来！
- 最后效果

![换肤](/images/换肤.gif)

- 具体实现方案是这样的
1. 利用className+less来为DOM元素设置颜色

```
<div className="themeBtn"></div>

//.less文件
@primary-color: blue;

.themeBtn{
    width: 200px;
    height: 200px;
    background: @primary-color;
}
```
2. 然后利用less的[修改变量](https://www.html.cn/doc/less/usage/#using-less-in-the-browser)来替换颜色值变量

> [Using Less in the Browser](http://lesscss.org/usage/#using-less-in-the-browser)

i: 我们要将刚才设置的less引用在html页面，如果是react，直接放public里面的`index.html`就行

```
<link rel="stylesheet/less" type="text/css" href="styles.less" />
```
ii：下载`less.js`文件到本地，然后也放在public文件夹里，并且在`index.html`的`<head>`里面引用

```
<script src="less.js" type="text/javascript"></script>
```
iii：最后就可以使用`window.less`这个属性了[改变less里的全局变量](http://lesscss.org/usage/#using-less-in-the-browser-modify-variables)了

```
//这是rc-color-picker的取色回调函数
handleColorChange = result => {
    console.log(result.color);
    window.less
      .modifyVars({  // 调用 `less.modifyVars` 方法来改变变量值
        '@primary-color': result.color,
      })
    localStorage['primary-color'] = result.color;
}
```
iv：如果要持久化处理，直接将颜色值存到`localStorage`就行，然后在`index.html`每次加载获取并判断`localStorage`的变量值并改变就行

```
 <script type="text/javascript">
    var primaryColor = localStorage.getItem('primary-color');
    if (primaryColor) {
      window.less
        .modifyVars({  // 调用 `less.modifyVars` 方法来改变变量值
          '@primary-color': primaryColor,
        })
    }
 </script>
```
