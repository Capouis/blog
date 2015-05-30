---
layout: default
title: Javascript Paginator
comments: true
---

最近忙于做Django的项目， 又一次有机会重新将前端的知识重新构造了一遍。
前端的东西在我看来基本都是提供一些格式化工具， 用来规范后端数据的显示。  在渲染页面时候， 我手写了一个分页器， 
在较短的时间（2个小时）完成这样工作， 效果还是令我满意的。
Django框架在后端集成了分页的模块， 所以完全可以将分页的逻辑放在后端完成， 而前端的task则是如何合理的显示分页的button

我要描述的一个分页的button列表， 用来进行ajax请求， 其中的样式有些硬编， 请勿模仿。。。

* 这段代码是用来进行 click button之后的Ajax请求， 可以看到处理了 <, <<<, >, >>>这样的特殊button， 再每一次Ajax请求之后，
会重新渲染页面的button列表

```

function searchWeblogs(btn){
	var url = '../weblogAjax/'
	data = new Object()
	if(btn.value == "<"){
		data.pageIndex = 0 //left
	}
	else if(btn.value == ">"){
		data.pageIndex = -1//right
	}
	else if(btn.value === "<<<"){
		data.pageIndex = 1
	}
	else if(btn.value == ">>>"){
		data.pageIndex = pageCnt
	}
	else{
		data.pageIndex = parseInt(btn.value)	
	}

	//console.log(data.pageIndex)
	page(data.pageIndex)
	$.ajax({
		type : 'post',
		dataType : 'json',
		url : url,
		async : true,
		data :  data,

		success : function(data){
			console.log('success')
			//Construct Data 
		},

		error : function(data){
			console.log("Oops,  some errors !")
		}
	})
}
```
</br>

* 下面这段则是整个分页button显示的核心逻辑， 仔细分析之后还是可以比较容易的描述出来

```
function page(pageIndex){
	if(pageIndex == 0){
		pageIndex = clickBtn - 1
	}
	else if(pageIndex == -1){
		pageIndex = clickBtn + 1
	}
	clickBtn = pageIndex

	var pagebtns = ""
	
	//Calc the left position where buttons begin
	var start = pageIndex - 5
	
	//Consider the left buttons
	if(start > 1){
		pagebtns += "<input type = 'button' name='bigleft' onclick= 'searchWeblogs(this)'"  +
		 "value='<<<'/>"
		pagebtns += "<input type = 'button' name='left' onclick= 'searchWeblogs(this)'"  +
		 "value='<'/>"
	}
	if(start < 1){
		start = 1
	}
	
	//Calc the right position where buttons end
	var end = 10 + start - 1 
	if(end > pageCnt){
		end = pageCnt
	}
	
	//Add the HTML of number buttons
	style = "border:1px solid; background-color:#E1E2E3"
	for(var i = start; i <= end; ++i){
		if(i == pageIndex){
			var highlight = "border:1px solid; background-color:white"
			pagebtns += "<input type = 'button' name='cnt' onclick= 'searchWeblogs(this)'"  +
		 	"value='" + i.toString() + "'" + "style='" + highlight + "'/>"
		}
		else{
			pagebtns += "<input type = 'button' name='cnt' onclick= 'searchWeblogs(this)'"  +
			 "value='" + i.toString() + "'" + "style='" + style + "'/>"	
		}	
	}
	
	//Consider the right buttons
	if(end < pageCnt){
		pagebtns += "<input type = 'button' name='right' onclick= 'searchWeblogs(this)'"  +
		 "value='>'/>"
		 pagebtns += "<input type = 'button' name='bigright' onclick= 'searchWeblogs(this)'"  +
		 "value='>>>'/>"
	}

	$('#page').html(pagebtns)
}
```
