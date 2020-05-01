**kemis**是一个基于React框架的页面渲染器，通过JSON配置就能生成各种后台页面，并提供统一的UI风格，让开发过程更聚焦业务，以此简化前端开发，甚至说是去“前端”化，让后端同学可以零门槛入门，只需围绕**kemis**提供的渲染器以及对应的API进行JSON配置就能完成开发工作。

同时，**kemis**有良好的官方文档，并附加了各个渲染器的例子以及配套的代码，而且例子中的代码支持实时更改，页面实时生效，可以说是所写即所见。唯一美中不足的是页面都是基于渲染器，而渲染器及与之对应的渲染器API繁多，让你在开发中比较受限，一个功能支不支持，只能去翻阅文档，慢慢探索，但熟悉以后，效率上的提升显而易见。话不多说，下面让我们来看看怎么用**kemis**来进行开发。


## 一、基本用法

开始之前需要先安装**kemis**

`npm install @ke/kemis --registry=http://registry.npmjs.lianjia.com:7001`

其次，**kemis**基于react，并且要求版本`react>=16.8`，所以你也需要安装一下react

`npm install react --registry=http://registry.npmjs.lianjia.com:7001`

然后就可以进行开发了，直接上代码，如下：


```
import * as React from 'react';
// 引用 kemis 的 render方法
import { render as renderKemis } from '@ke/kemis';
// 引用 kemis 样式
import '@ke/kemis/lib/themes/skin000-kemis.css';
import '@ke/kemis/lib/themes/default.css';

class MyComponent extends React.Component {
  render() {
    return (
      <div>
        <p>通过 kemis 渲染页面</p>
        {renderKemis({
          // 这里是 kemis 的 Json 配置。
          type: 'page',//page(页面)渲染器
          title: '这是标题部分',
          subTitle: '这是子标题',
          remark: '这是小提示信息',
          aside: '这是侧边栏部分',
          body: '这是内容区',
          toolbar: '这是工具栏部分'
        })}
      </div>
    );
  }
}
```

效果如图：

![](https://user-gold-cdn.xitu.io/2020/5/1/171cf5d9896cf867?w=1760&h=276&f=png&s=40219)

## 二、核心要点介绍

* 渲染器

**kemis**最重要也最核心的概念就是渲染器，那什么是渲染器？

一个页面会由许多部分组成，比如一个常见的博客站点，顶部的导航区域，顶部下方的内容区域，而内容区域又分左边栏的文章详情，右边栏的文章目录，如下图示

![](https://user-gold-cdn.xitu.io/2020/5/1/171cf71c1e6f9ebf?w=2472&h=880&f=png&s=94040)

而渲染器简单理解就是页面的各个分区的容器，包括页面本身也是一个渲染器。在JSON配置中通过type字段来声明你所使用的渲染器，同时渲染器也承载了相应区域的功能，比如一个列表渲染器，`type: CRUD`，围绕列表的功能如过滤筛选、拖曳排序、添加删除操作、翻页等等，都是渲染器的API（或者理解为是属性），只要声明了对应的API，就具有了相应的功能，你需要的只是告诉渲染器，你要什么功能，声明它，而不用操心功能的实现

```!
需要注意的是，所有的渲染器都需在page浸染器之内声明，渲染器可嵌套
```
这里以一个海外需求的A类房源推荐为例写了一个简单的列表页面，如下：

```
{//这里只是一个例子，字段配置项就对应着你想要的功能，如果配置了就有相应功能，不配置默认没有
  "type": "page",//最外层的页面渲染器
  "api": "/api/options/mixed",//指定页面的数据api,
  "title": "新房聚焦",//页面标题
  "body": {
    "type": "crud",//crud为列表渲染器
    "draggable": true,//是否支持拖曳
    "headerToolbar": [],//列表头部的功能区域
    "footerToolbar": [//列表底部区域
      "pagination"//底部区域的翻页功能
    ],
    "columns": [//列表列的配置
    {
      "name": "id",//列数据填充时从接口返回数据取的字段
      "label": "序号",//列标题
    },
    ]
  }
}
```
效果图


![](https://user-gold-cdn.xitu.io/2020/5/1/171cf8daaa5108b4?w=2272&h=944&f=png&s=136659)

* 接口请求


```!
kemis渲染器的数据都来源于api，有一定的格式要求
```
具体是：

```
{
  "errno": 0, // 0 表示成功，非0 表示失败
  "errmsg": "操作成功", // 提示信息 包括失败和成功
  "data": {
    // ...
    // 具体的数据
  }
}
```
通过对应渲染器的api字段配置，支持字符串和对象，返回数据按上面的格式，

* 调试
