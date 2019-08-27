---
title: 语法
categories: docs
comments: false
---

art-template 支持标准语法与原始语法。标准语法可以让模板易读写，而原始语法拥有强大的逻辑表达能力。

标准语法支持基本模板语法以及基本 JavaScript 表达式；原始语法支持任意 JavaScript 语句，这和 EJS 一样。

## 输出

**标准语法**

```html
{{value}}
{{data.key}}
{{data['key']}}
{{a ? b : c}}
{{a || b}}
{{a + b}}
```

**原始语法**

```html
<%= value %>
<%= data.key %>
<%= data['key'] %>
<%= a ? b : c %>
<%= a || b %>
<%= a + b %>
```

模板一级特殊变量可以使用 `$data` 加下标的方式访问：

```html
{{$data['user list']}}
```

## 原文输出

**标准语法**

```html
{{@ value }}
```

**原始语法**

```html
<%- value %>
```

> 原文输出语句不会对 `HTML` 内容进行转义处理，可能存在安全风险，请谨慎使用。

## 条件

**标准语法**

```html
{{if value}} ... {{/if}}
{{if v1}} ... {{else if v2}} ... {{/if}}
```

**原始语法**

```html
<% if (value) { %> ... <% } %>
<% if (v1) { %> ... <% } else if (v2) { %> ... <% } %>
```

## 循环

**标准语法**

```html
{{each target}}
    {{$index}} {{$value}}
{{/each}}
```

**原始语法**

```html
<% for(var i = 0; i < target.length; i++){ %>
    <%= i %> <%= target[i] %>
<% } %>
```

1. `target` 支持 `array` 与 `object` 的迭代，其默认值为 `$data`。
2. `$value` 与 `$index` 可以自定义：`{% raw %}{{each target val key}}{% endraw %}`。

## 变量

**标准语法**

```html
{{set temp = data.sub.content}}
```

**原始语法**

```html
<% var temp = data.sub.content; %> 
```

## 模板继承

**标准语法**

```html
{{extend './layout.art'}}
{{block 'head'}} ... {{/block}}
```

**原始语法**

```html
<% extend('./layout.art') %>
<% block('head', function(){ %> ... <% }) %>
```

模板继承允许你构建一个包含你站点共同元素的基本模板“骨架”。范例：

```html
<!--layout.art-->
<!doctype html>
<html>
<head>
    <meta charset="utf-8">
    <title>{{block 'title'}}My Site{{/block}}</title>

    {{block 'head'}}
    <link rel="stylesheet" href="main.css">
    {{/block}}
</head>
<body>
    {{block 'content'}}{{/block}}
</body>
</html>
```

```html
<!--index.art-->
{{extend './layout.art'}}

{{block 'title'}}{{title}}{{/block}}

{{block 'head'}}
    <link rel="stylesheet" href="custom.css">
{{/block}}

{{block 'content'}}
<p>This is just an awesome page.</p>
{{/block}}
```

渲染 index.art 后，将自动应用布局骨架。

## 子模板

**标准语法**

```html
{{include './header.art'}}
{{include './header.art', data}}
```

**原始语法**

```html
<% include('./header.art') %>
<% include('./header.art', data) %>
```

1. `data` 数默认值为 `$data`；标准语法不支持声明 `object` 与 `array`，只支持引用变量，而原始语法不受限制。
2. art-template 内建 HTML 压缩器，请避免书写 HTML 非正常闭合的子模板，否则开启压缩后标签可能会被意外“优化。

## 过滤器

**注册过滤器**

```js
template.defaults.imports.dateFormat = function(date, format){/*[code..]*/};
template.defaults.imports.timestamp = function(value){return value * 1000};
```

过滤器函数第一个参数接受目标值。

**标准语法**

```html
{{date | timestamp | dateFormat 'yyyy-MM-dd hh:mm:ss'}}
```

`{% raw %}{{value | filter}}{% endraw %}` 过滤器语法类似管道操作符，它的上一个输出作为下一个输入。

**原始语法**

```html
<%= $imports.dateFormat($imports.timestamp(date), 'yyyy-MM-dd hh:mm:ss') %>
```

> 如果想修改 `{% raw %}{{{% endraw %}` `{% raw %}}}{% endraw %}` 与 `<%` `%>`，请参考 [解析规则](rules.html)。
