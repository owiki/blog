---
title: 如何使用Chrome直接调试JS
categories: tool
tag: [Chrome, JavaScript]
---

#### Snippets

> [Snippets介绍](https://developers.google.com/web/tools/chrome-devtools/javascript/snippets)

首先我不知道各位有没有什么更好的方法来直接调试 JS，以下技巧是我自己在平时经常用的，如果有更好的方法，欢迎各位私信我邮箱，感谢阅读和不吝赐教！

Chrome控制台的Source资源管理目录下面有一个`Snippets`标签，直接添加多个代码段，并且可以直接打断点运行调试，然后还可以观察函数运行的所有参数细节等。这些代码段都是存在在本地的，你不删它，下次打开Chrome还会在那里。

![image_20200703162713](/images/image_20200703162713.png)

#### 使用步骤

以接下来我要调试的一段函数为例，直接新建一个js代码段的Snippet

![image_20200703164537](/images/image_20200703164537.png)

然后写入代码，点击`Ctrl+Enter`运行，就可以看到结果将会输出在下方

![image_20200703164845](/images/image_20200703164845.png)

我们可以为函数设置断点，重新运行；就可以在右侧观察本地变量，this指向，断点信息等参数，并且可以按步运行调试下去

![image_20200703165319](/images/image_20200703165319.png)

我们还可以直接通过这里的代码段获取当前网页的DOM元素，通过这段代码直接将GitHub首页改没了

```javascript
console.log('Hello, Snippets!');
document.body.innerHTML = '';
var p = document.createElement('p');
p.textContent = 'Hello, Snippets!';
document.body.appendChild(p);
```

![image_20200703165906](/images/image_20200703165906.png)

