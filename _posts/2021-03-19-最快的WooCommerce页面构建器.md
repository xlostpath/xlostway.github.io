---
layout: post
title: "最快的WooCommerce页面构建器（速度测试）"
categories: tutorial
---



速度对于在线商店至关重要，在[WooCart](https://woocart.com/)上，我们希望帮助商店所有者建立最快的商店。

**主题影响页面速度的方式相同，页面构建器也是如此。**

我们想要进行这些页面构建器速度测试，以便每个人都可以理解使用它们时的性能损失。性能通常会被设计所忽略，即使在在线商店中，性能也应该相反。

性能测试在2021年3月完成。

概述
--

*   [我们测试了哪些页面构建器以及如何测试？](#which)
*   [哪个是最快测试WooCommerce页面构建器的？](#fastest)
    *   [页面加载时间-总体](#overall)
    *   [页面加载时间–单页](#pages)
*   [WooCommerce Page Builder速度测试分析](#analysis)
    *   [WooCommerce Page Builder 2019/20速度测试的结果](#2019)
*   [速度测试方法](#methodology)
    *   [WooCommerce商店配置](#storeconfig)
    *   [速度测试配置](#testingconfig)
*   [您应该选择哪种WooCommerce页面构建器？](#choose)

我们测试了哪些页面构建器以及如何测试？
-------------------

我们已经测试了**7种流行的付费和免费页面构建器以及Gutenberg**。

我们为测试商店提供了使用页面构建器构建的基本主页设计。没有对商店或产品页面进行任何更改。我们总共测试了三种页面（主页，商店页面，产品页面），每种页面测试十次。测试是通过GT Metrix API完成的。以统计页面加载时间作为结果。我们认为，这些结果能够反映出不同页面构建器的性能。

使用的主题是Astra，因为它是我们基准测试中[最快的WooCommerce主题](https://woocart.com/blog/fastest-woocommerce-theme)，并且支持所有流行主题。

要完全了解我们如何测试页面构建器，请阅读结果下方的“[测试方法”](#methodology)部分。

哪个是最快测试WooCommerce页面构建器的？
-------------------------

让我们直接跳到结果。在所有情况下，最低的数字都是最好的。

### 页面加载时间-总体

这是这三种测试的平均值。

![](https://blogwoocartcom-e6da.kxcdn.com/wp-content/uploads/Pagebuilders-01-Average-Page-Load-Overall-1024x386.png)

​                                           *总的页面平均负载*

### 页面加载时间–单页

每个页面经过10次测试。结果是中位数。

![](https://blogwoocartcom-e6da.kxcdn.com/wp-content/uploads/Pagebuilders-02-Home-Page-Load-1024x386.png)

​                                                    *主页加载* ![](https://blogwoocartcom-e6da.kxcdn.com/wp-content/uploads/Pagebuilders-03-Shop-Page-Load--1024x386.png) 

​                                              *商店页面加载*![](https://blogwoocartcom-e6da.kxcdn.com/wp-content/uploads/Pagebuilders-04-Product-Page-Load--1024x386.png)

​                                                *产品页面加载*

WooCommerce Page Builder速度测试分析
------------------------------

我们在测试中添加了一些新的页面构建器，但结果与上次几乎相同。主要区别在于古腾堡（Gutenberg）今年接管了Beaver Builder。

Beaver Builder以及新出来的Brizy给我们留下了非常深刻的印象。尽管它们具有完整的页面构建器功能，但与Gutenberg相比，几乎没有性能上的衰减。

<table><tbody><tr><td><strong>Page Builder</strong></td><td><strong>Version</strong></td><td><strong>Overall</strong></td><td><strong>Home</strong></td><td><strong>Shop Page</strong></td><td><strong>Product Page</strong></td></tr><tr><td>Gutenberg</td><td>5.6.2 (WP)</td><td>805</td><td>637</td><td>874</td><td>905</td></tr><tr><td>Beaver Builder</td><td>2.4.2.1</td><td>914</td><td>960</td><td>903</td><td>878</td></tr><tr><td>Brizy</td><td>2.2.8</td><td>1006</td><td>1077</td><td>1085</td><td>855</td></tr><tr><td>Site Origin</td><td>2.11.8</td><td>1263</td><td>1025</td><td>1514</td><td>1250</td></tr><tr><td>WP Bakery</td><td>6.5.0</td><td>1453</td><td>1693</td><td>1683</td><td>983</td></tr><tr><td>Visual Composer</td><td>34.1</td><td>1455</td><td>1525</td><td>1662</td><td>1178</td></tr><tr><td>Divi</td><td>4.9.0</td><td>1658</td><td>1261</td><td>2126</td><td>1587</td></tr><tr><td>Elementor</td><td>3.1.1</td><td>1721</td><td>1932</td><td>1965</td><td>1265</td></tr></tbody></table>

结果非常清楚地表明，最流行的复杂页面构建器（例如Divi和Elementor）带来了很高的性能成本。

### WooCommerce Page Builder速度测试2019/20的结果

这些是去年的结果。每种页面测试运行五次（相比之下，今年是十次）。统计的时间是完全加载后消耗的时间，不能直接与今年的页面加载时间进行比较。

<table><tbody><tr><td><strong>Page Builder</strong></td><td><strong>Version</strong></td><td><strong>Overall</strong></td><td><strong>Home</strong></td><td><strong>Shop</strong></td><td><strong>Product</strong></td></tr><tr><td>Beaver Builder</td><td>2.2.4.3</td><td>1005</td><td>934</td><td>1076</td><td>916</td></tr><tr><td>Gutenberg</td><td>5.2.3 (WP)</td><td>1080</td><td>993</td><td>1101</td><td>971</td></tr><tr><td>WP Bakery</td><td>6.0.5</td><td>1163</td><td>1150</td><td>1163</td><td>991</td></tr><tr><td>Visual Composer</td><td>19.0.0</td><td>1218</td><td>1163</td><td>1166</td><td>1098</td></tr><tr><td>Divi</td><td>2.0.0</td><td>1232</td><td>1334</td><td>1313</td><td>1106</td></tr><tr><td>Elementor</td><td>2.7.1</td><td>1817</td><td>1917</td><td>1717</td><td>1776</td></tr></tbody></table>页面加载时间以毫秒（ms）为单位。

速度测试方法
-------------------------

为了确保测试可靠性和一致性，我们设置了严格的测试标准。

### WooCommerce商店配置

我们使用了免费的Astra主题。页面构建器用于创建具有相同部分的首页-页首有一张图片，分类有三张图片，包含特色产品，一个大的特色产品以及常见问题文字。

商店托管在位于荷兰数据中心中的Google Cloud Platform上的WooCart托管中。商店中没有运行CDN。启用了缓存。

WordPress的版本是5.6.2，WooCommerce的版本是5.0，它们都是当前测试时的最新版本。所有页面构建器也都运行了最新版本。

商店运行在WooCart Turnkey上。这里为所有商店提供了35个演示产品，产品、主页、联系页面和法律声明页面使用了81张图片。

### 速度测试的配置

速度测试服务使用的是 [GT Metrix](https://gtmetrix.com/) 的API. 测试地点是Frankfurt （德国），浏览器是Chrome（台式机）。

我们**每种页面**运行测试**十次**：主页，产品页面和商店页面。我们计算页面加载时间的**中位数结果**。

**为什么使用中值？**

我们计算中值，因为对于页面速度测试的用例来说，它远远好于一般的平均值。这样，异常值对最终结果影响较小，使测试更加准确。你可以阅读更多关于本[博客文章中的](https://www.dynatrace.com/news/blog/why-averages-suck-and-percentiles-are-great/)该主题。

如果您对原始测试数据感兴趣，请查看[完整的电子表格](https://docs.google.com/spreadsheets/d/1ndEBlQgrjvunU89n1cNJDqClxYpu4Eu5YTMlV67J4oI/edit#gid=0)。

您应该选择哪种WooCommerce页面构建器？
-----------------------------------------------------

如果您要新建一家商店，建议您与**Gutenberg一起使用**。每年它都会变得更强大且更易于使用。如果您想要一个完整的页面构建器，那么**Beaver Builder和Brizy**几乎不会降低性能，我们极力地推荐它们。



了解如何加快您的WooCommerce商店的最佳技巧。

[了解更多 ](https://woocart.com/free/lm-speedguide?utm_source=woocart&utm_medium=blog&utm_campaign=blogcta)

> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 [woocart.com](https://woocart.com/blog/fastest-woocommerce-page-builder?utm_source=email&utm_medium=broadcast&utm_campaign=pbspeed2021)