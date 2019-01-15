---
layout: post
title:  "javascript 可变数组长度"
date:   2017-10-25
desc: "javascript 可变数组长度"
keywords: "web,design, java script"
categories: [Server]
tags: [Web, js]
---

## javascript &lt; select &gt;标签
 由于Javascript的数组长度可变所以在使用&lt; select &gt;  &lt;/select &gt; 中遍历选择时要使用selectedIndex。

* 如下代码：
	
		document.getElementById("add").onclick = function(){
			var first = document.getElementsByName("first")[0];
			var second = document.getElementsByName("second")[0];
			var firstOptions = first.getElementsByTagName("option");
			var len = firstOptions.length;
			for(var i = 0; i < len; i++){
			/**
				* selectIndex 如果选中则为选中中第一个元素的下标，如果选不中则为-1
				*/
			if(first.selectedIndex != -1){//如果选中则为第一个选中元素的下表
				var option = firstOptions[first.selectedIndex];
				second.appendChild(option);
		}

---

* onchang事件
	- select 中的onchange事件至少有两个以上的option才可以。