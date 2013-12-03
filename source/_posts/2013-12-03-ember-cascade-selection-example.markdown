---
layout: post
title: "A cascade selection example using EmberJS"
date: 2013-12-03 18:19
comments: true
categories: 
  - EmberJS
---

## 使用Ember JS创建级联下拉框

[Ember JS](http://emberjs.com)因为有`Ember.ComputedProperty`的存在，因为做级联查询其实是很简单的。

本帖提供一个省、市、镇的级联查询小示例，仅供参考。

<!-- more -->

核心代码很少：

在`controller`里添加以下代码：

```javascript
// XxxController

provinces: function() {
  return this.get('store').find('province');
}.property(),

cities: function() {
  if(!this.get('province')) {
    return;
  }

  this.set('city', null);
  this.set('town', null);

  return this.get('store').findQuery('city', {
    provinceId: this.get('province.id')
  });
}.property('province'),

towns: function() {
  if(!this.get('city')) {
    return;
  }

  this.set('town', null);

  return this.get('store').findQuery('town', {
    cityId: this.get('city.id')
  });
}.property('city')
```

`template`内容如下：

{% raw %}
```html
<form>
  <label>省</label>
  {{view Ember.Select
      contentBinding="provinces"
      optionLabelPath="content.name"
      optionValuePath="content.id"
      selectionBinding="province"
      prompt="--- 选择省 ---"}}

  <label>市</label>
  {{view Ember.Select
      contentBinding="cities"
      optionLabelPath="content.name"
      optionValuePath="content.id"
      selectionBinding="city"
      prompt="--- 选择市 ---"}}

  <label>镇</label>
  {{view Ember.Select
      contentBinding="towns"
      optionLabelPath="content.name"
      optionValuePath="content.id"
      selectionBinding="town"
      prompt="--- 选择镇 ---"}}
</form>
```
{% endraw %}

具体例子请参考；http://jsbin.com/OGIrUDA/22

完整代码：https://gist.github.com/pudgecon/7414207

BTW: 欢迎访问[Ember JS 中文论坛(emberjs.cn)](http://emberjs.cn)进行交流。
