---
title: JSON和XML的区别
excerpt: JSON和XML的区别
tags: [json]
categories: json
banner_img: /img/壁纸.jpg
---

**(1)XML定义**
扩展标记语言 (Extensible Markup Language, XML) ，用于标记电子文件使其具有结构性的标记语言，可以用来标记数据、定义数据类型，是一种允许用户对自己的标记语言进行定义的源语言。 XML使用DTD(document type definition)文档类型定义来组织数据;格式统一，跨平台和语言，早已成为业界公认的标准。
XML是标准通用标记语言 (SGML) 的子集，非常适合 Web 传输。XML 提供统一的方法来描述和交换独立于应用程序或供应商的结构化数据。

**(2)JSON定义**
JSON(JavaScript Object Notation)一种轻量级的数据交换格式，具有良好的可读和便于快速编写的特性。可在不同平台之间进行数据交换。JSON采用兼容性很高的、完全独立于语言文本格式，同时也具备类似于C语言的习惯(包括C, C++, C#, Java, JavaScript, Perl, Python等)体系的行为。这些特性使JSON成为理想的数据交换语言。
JSON基于JavaScript Programming Language , Standard ECMA-262 3rd Edition - December 1999 的一个子集。

#### XML的优缺点

​	**优点：**1、格式统一，符合标准

​				2、容易和其他系统进行远程交互

​				3、SAX解析方式XML提供了对大规模数据的逐步解析方案，这种方案很适合于对大量数据的处理。

​	**缺点：**1、服务端和客户端解析需要花费大量时间和资源，代码效率过低

​				2、XML文件庞大，格式复杂，不易压缩，传输占带宽

​				3、不同浏览器解析XML格式不一样，不兼容

#### JSON的优缺点

​	优点：1、文件简单，格式简单，易压缩，占用带宽小，传输速度更快

​				2、解析简单，简单的对JSON数据进行读取，占很少服务器时间

​				3、JSON格式能直接为服务器端代码使用，大大简化服务器端和客户端的代码开发量

​	缺点：1、没有XML推广的那么深入人心，没有XML那么通用性

​				2、JSON只提供整体解析方案，而这种方法只在解析较少的数据时才能起到良好的效果

​				

​	