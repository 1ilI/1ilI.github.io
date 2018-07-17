---
layout: page
title: Something
permalink: /about/
icon: heart
type: page
---

* content
{:toc}

<!-- ## 关于我

## 联系我

* GitHub：[https://github.com/1ilI](https://github.com/1ilI)
* email：[zhangyue_developer@icloud.com](mailto:zhangyue_developer@icloud.com)


## 关于本站


### Update Log


## 友情链接 -->

<style>
    strong {
      color: #6CA6CD;
    }

    .return {
      color: #7B68EE;
    }

    .name {
      color: #EEAD0E;
    }

    .parameter-types, .parameters {
      color: #00CD66;
    }
    
    .nullability {
      color: #EC407A;
    }

    .disclaimer {
      line-height: 2em;
    }
    code {
      color: #839496;
      display: block;
      font-family: Monaco, Menlo, monospace;
      font-size: 15px;
      padding-top :2em;
      padding-bottom :2em;
      padding-left :1em;
      padding-right :1em;
      margin-top:1.5em;
    }

</style>

# 在 Objective-C 中声明 Block

## 作为 <strong>局部变量</strong> :
<code>
    <span class="return">returnType</span> (^<span class="name">blockName</span>)(<span class="parameter-types">parameterTypes</span>) = ^<span class="return">returnType</span>(<span class="parameters">parameters</span>) {...};
</code>

## 作为 <strong>属性</strong> :
<code>
    @property (nonatomic, copy, <span class="nullability">nullability</span>) <span class="return">returnType</span> (^<span class="name">blockName</span>)(<span class="parameter-types">parameterTypes</span>);
</code>

## 作为 <strong>方法的参数</strong> :
<code>
    - (void)someMethodThatTakesABlock:(<span class="return">returnType</span> (^<span class="nullability">nullability</span>)(<span class="parameter-types">parameterTypes</span>))<span class="name">blockName</span>;
</code>

## 作为 <strong>方法的回调</strong> :
<code>
    [someObject someMethodThatTakesABlock:^<span class="return">returnType</span> (<span class="parameters">parameters</span>) {...}];
</code>

## 作为 <strong>typedef</strong> :
<code>
    typedef <span class="return">returnType</span> (^<span class="name">TypeName</span>)(<span class="parameter-types">parameterTypes</span>);<br/>
    <span class="name">TypeName</span> blockName = ^<span class="return">returnType</span>(<span class="parameters">parameters</span>) {...};
</code>

{% include comments.html %}
