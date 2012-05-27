---
layout: post
title: "FirstTest"
description: ""
category: Test
tags: [test, first]
---
{% include JB/setup %}

This is only a test page, try to show some Chinese.
这只是一个测试页面，试试看中文是否能正常显示。

## 测试一段C++代码：
{% highlight cpp linenos %}
class A
{
    int m_i;
    void SetValue(int i)
    {
        m_i = i;
    }
};
{% endhighlight %}