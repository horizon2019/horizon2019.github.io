---
title: jquery碎片知识整理
date: 2018-08-16 20:51:27
tags:
category: jquery
---

一：根据id遍历
```
//根据id遍历
  $("#toggle").each(function() {  });
//根据classname遍历
  $(".toggle").each(function() {  
                if ($(this).prop('checked') ==true) {
                    var val = $(this).val();
                    var filename = $(this).attr("data-name");
                    downloadimage(val,filename);
                }
             });
```
二：限制上传文件为图片
后端：
```
 if (strtolower($type) == '.png' || strtolower($type) == '.jpg' || strtolower($type) == '.bmp' || strtolower($type) == '.gif') 
```
前端：
```
<input type="file" accept="image/*">
```

三：移除下拉框被选中项
这个是直接将option移除：
```
$("#sysusergroupList").find("option:selected").remove();
```
而这个是将option选中状态移除：
```
$("#kt_select2_3_modal").find("option:selected").attr("selected", false);
```

四：点击按钮执行不同的事件
```
      var f = 0;  
        $("#btn-id").click(function(e){   
            if(f == 0){
                    f=f+1;
                   //第一次点击执行的事件
                }else{
                    //第二次点击执行的事件
                }
        });
```