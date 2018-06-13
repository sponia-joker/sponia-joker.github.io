---
layout: post
title:  "TSL与SSL之间关系"
date:   2018-06-13 11:34:15 +0800
categories: 翻译
---

原文地址：[SSL vs. TLS - What's the Difference?](https://www.globalsign.com/en/blog/ssl-vs-tls-difference/)

互联网安全有很多让人费解的首字母缩写单词，列如：SSL,TLS,ECC,SHA，而且还在不断增加。这些缩写字母让我们很难理解其含义。 也许我们被问到的最多的是 -   [SSL](https://www.globalsign.com/en/ssl/)（安全套接层）和TLS（安全传输层）之间的区别是什么？你想加强网站安全(或者其他类型通信)。但是你确定你需要SSL ?TSL ?还是SSL和TLS ? 让我们弄明白它。<!-- more -->

## SSL和TLS简史

SSL和TLS是一种能够在服务器，machines和通过网络运行的应用程序（列如，客户端连接到web服务器）之间提供身份认证和数据加密的加密协议。SSL是TLS的前世。多年来，新版本的发布用来解决漏洞，提供更强大支持，更安全的密码套件和算法。

SSL最初是由Netscape开发的，早在1995年以SSL 2.0的方式发布（1.0从未对公众发布）。在一些漏洞被发现之后，版本2.0在1996年很快被3.0所取代。注意：版本2.0和3.0有时会写成SSLv2和SSLv3。

[TLS](https://tools.ietf.org/html/rfc2246)以SSL 3.0为基础于1999年作为SSL的新版本推出。

> TLS协议和SSL 3.0之间的差异并不明显，但是他们都非常重要且TLS 1.0 和 SSL 3.0不具有互操作性

## 你应该使用SSL还是TLS？

SSL2.0和SSL3.0已经被IEFT组织废弃（分别在2011年，2015年）多年来，在被废弃的SSL协议中一直存在漏洞并被发现 (e.g. [POODLE](https://www.globalsign.com/en/blog/poodle-vulnerability-in-ssl-30/), [DROWN](https://www.globalsign.com/en/blog/drown-attack-sslv2/))。大多数现代浏览器遇到使用废弃协议的web服务时，会降低用户体验（红线穿过挂锁标志或者https表示警告）来表现。因为这些原因，你应该在服务端禁止使用SSL协议，仅仅保留TLS协议开启。

## 证书并不是协议

在开始担心需要用TLS证书来替换SSL证书之前，认识到证书并不依赖协议这很重要。也就是说，你不需要使用TLS证书和SSL证书。然后很多产商倾向于使用短语“SSL/TLS证书”。也许“用于SSL和TLS的证书”的说法更加准确，因为是你服务的配置而不是证书决定了你使用的协议。

你可能会继续看到被称作SSL证书的证书，因为现在人们更加熟悉这个术语。但是我们开始看到术语TLS在行业的使用不断增加，在更多人熟悉TLS之前，SSL / TLS是一种常见的折中方案。

## SSL 和 TLS 是否有任何不同的加密？

 事实上，这个问题的答案是肯定的。但是对于历史版本SSL 2 和 SSL 3或 TLS 1 和1.1，1.2，1.3你也可以说同样的话。SSL和TLS都是关于同一协议的，但是由于版本的不同，SSL 2，3不具有互操作性，SSL 3和TLS 1也不具有互操作性。你可以认为Transport Layer Security(TLS)仅仅是SSL v4的新名称。本质上，我们谈论的是相同的协议。

每当协议最新的版本发布时都会进行改进，添加、废弃一些功能。SSL 1从未对公众发布过，SSL 2 对外发布过，但是因为其设计带有重要缺陷，SSL 3 在 SSL 2上进行了重写，TLS 1是SSL 3的改进版本。自从TLS1.0之后很少有重大的改变，但是每次改变都很重要。

值得注意的是，SSL和TLS简单指发生在客户端和服务端的握手过程，握手本身实际不进行任何加密，它只是同意将要使用的共享秘钥和加密方式。

更多信息可以访问 [new features released in TLS 1.3, visit the Cloudflare blog](https://blog.cloudflare.com/introducing-tls-1-3/).

## 禁用SSL 2.0 和3.0

如果你不确定你的服务器是否任然支持SSL 协议，你可以通过[SSL Server Test](https://globalsign.ssllabs.com/).来检查

有关 [如何在流行服务器类型上禁用SSL 2.0 和 3.0 的指导](https://support.globalsign.com/customer/portal/articles/2356063)，包括Apache，NGINX和Tomcat，请阅读我们相关支持文章。

所以SSL和TLS之间到底有什么不同？在实际对话中，没有太多不同并且很多人继续使用术语SSL。就服务器配置角度而言，在漏洞，过时的密码套件和浏览器安全警告方面就会存在不同。当涉及到你的服务时，你应该仅仅开启TLS协议。



