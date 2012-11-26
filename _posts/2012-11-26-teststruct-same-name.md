---
layout: post
title: "TestStruct same name"
description: ""
category: 
tags: []
---
{% include JB/setup %}

在多个CPP文件中,定义了同名struct TestStruct.
那么在其中某个文件的函数中如果在栈上创建了TestStruct,则会崩溃...
Run-Time Check Failure #2 - Stack around the variable 'a' was corrupted.

