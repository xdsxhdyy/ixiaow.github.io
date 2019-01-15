---
layout: post
title:  "Android NFC开发（一）"
date:   2017-09-02
desc: "Android NFC开发"
keywords: "android,NFC"
categories: [Android]
tags: [Android, NFC]
---

# Android开发之NFC （一）

前段时间做了一个有关nfc的demo，这也是我第一次对NFC有了一个较为全面的认识吧，特此总结一哈。

## 理论知识
> Near Field Communication 近场通信，是一种数据传输技术。
>与wifi、蓝牙、红外线等数据传输技术的一个主要差异就是有效距离一般不能超过4cm。

NFC支持3种工作模式：
1. 读卡器模式；
2. 仿真卡模式；
3. 点对点模式；

#### 读卡器模式：

通过NFC设备（支持NFC的Android手机）从带有NFC芯片的标签、贴纸、报纸、明信片等媒介读取信息，或将数据写到这些媒介中。

#### 仿真卡模式：

是将支持NFC的手机或其他电子设备当成借记卡、信用卡、公交卡、门禁卡等IC卡使用；基本原理是将相应的IC卡中的信息（支付凭证）封装成数据包存储在支持NFC的手机中，在使用时还需要一个NFC射频器（相当于刷传统IC卡时使用的刷卡器），将手机靠近NFC射频器，手机就会收到NFC射频器发过来的信号，在通过一系列复杂的验证后，将IC卡的相应信息传入NFC射频器，最后这些IC卡数据会传入NFC射频器连接的计算机，并进行相应的处理（如电子转账、开门等操作）。

#### 点对点模式：

与蓝牙、红外差不多，可以用于不同的NFC设备之间进行数据交换，只是NFC的点对点模式有效距离更短，不能超过4cm；但是如果两个设备使用的都是Android4.2及以上版本，NFC会直接利用蓝牙传输，这种技术被称为android Beam，所以Android Beam传输数据的两部设备不局限于4cm之内。

----
概念就到这儿了，下面来看看在android中的开发吧。

## Android --> NFC

在Android中开发NFC程序，可以参考开发文档：

 https://developer.android.com/guide/topics/connectivity/nfc/index.html

### Android NFC标准

Android SDK API 支持的标准被称为NDEF（NFC Data Exchange Format，即NFC数据交换格式。

Android SDK API支持如下三种NDEF数据的操作：

1. 从NFC标签读取NDEF格式的数据；
2. 向NFC标签写入NDEF格式的数据；
3. 通过Android Beam技术将NDEF数据发送到另一部NFC设备；

在一个NFC设备读取NFC标签或另一个NFC设备中的数据之前会在0.1秒的时间之内建立NFC连接，然后数据会自动从被读取一端流向读取数据的一端；数据接收端会根据具体的数据格式和标签类型调用相应的Activity（这种行为也称为Tag Dispatch），这些Activity都需要定义Intent Filter，这些Intent Filter中就会指定不同的过滤机制，分为三个级别，也称为NFC的三重过滤机制。

*  NDEF_DISCOVERED：

只过滤固定格式的NDEF数据。例如：纯文本、指定协议（http、ftp、smb等）的URI等；

* TECH_DISCOVERED：

当ACTION_NDEF_DISCOVERED指定的过滤机制无法匹配Tag时，就会使用这种过滤机制进行匹配，这种过滤机制并不是通过Tag中的数据格式进行匹配的，而是根据Tag支持的数据存储格式进行匹配，因此这种过滤机制的范围更广；

* TAG_DISCOVERED：

如果将NFC过滤机制看成if...else if...else语句的话，那么这种过滤机制就相当于else部分，当前面两种过滤机制都匹配失败后，系统就会利用这种过滤机制来处理，这种过滤机制用来处理未识别的Tag（数据格式不对，而且Tag支持的格式也不匹配）。

Android系统会依次匹配NDEF_DISCOVERED、TECH_DISCOVERED和TAG_DISCOVERED；如果通过三重过滤机制仍然无法匹配Tag，则什么都不做。