#####表单下载
```
<form id="ajaxForm" method="post" action="url" enctype="multipart/form-data">
	<input type="text" name="name" />
	<input type="text" name="sex" />
	<input type="file" name="file" />
	<button type="submit" id="btnSubmit">提交1</button>
</form>

$("#ajaxForm").ajaxForm(function () {
    alert("提交成功1");
});
```

```
// 外部操作提交
<button id="btnButton" type="button">提交2</button>

$("#btnButton").click(function () {
    $("#ajaxForm").ajaxSubmit(function () {
        alert("提交成功2");
    });
    return false;
});
```

#####动态创建表单 也可以用这中方式上传文件
```
var form = $("<form>");   //定义一个form表单
form.attr('style', 'display:none');   //在form表单中添加查询参数
form.attr('target', '');
form.attr('method', 'get');
form.attr('action', "url");

var input1 = $('<input>');
input1.attr('type', 'hidden');
$('body').append(form);  //将表单放置在web中
form.append(input1);   //将查询参数控件提交到表单上
form.submit(); //提交
```