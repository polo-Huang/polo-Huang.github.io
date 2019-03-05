---
layout:     post
title:      "Ajax 异步上传文件"
subtitle:   "\"Ajax 异步上传文件 - 显示上传进度 - 百分比进度条\""
date:       2019-03-05 11:40:00
author:     "Polo"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - program
    - ajax
    - post
    - js
    - jq
---

> Ajax  


## 需求  
在web实际需求中会需要上传一些大体积的文件，通过ajax异步上传文件的方式并显示进度条可以提升用户体验


## Js代码  
```
// 创建进度条
function addResourceUploadPanel(course_section_id, title) {
  var uploadDiv = $(
    "<hr class=\"my-10 w-100\">" +
    "<div class=\"col-sm-2 pl-0\">" + title + "</div>" +
      "<div class=\"progress col-sm-8 px-0 my-05\">" +
        "<div class=\"progress-bar\" role=\"progressbar\" aria-valuenow=\"0\" aria-valuemin=\"0\" aria-valuemax=\"100\" style=\"width: 0;\">0%</div>" +
      "</div>" +
    "<div class=\"col-sm-2 px-0 text-right\"><i class=\"fa fa-close\"></i></div>"
  );
  var sectionBtnGroup = $('.section-panel-' + course_section_id);
  sectionBtnGroup.find('.resource-upload-list').append(uploadDiv);
  return uploadDiv;
}
// 创建上传成功的记录
function addResourcePanel(courseSectionResource) {
  console.log(courseSectionResource);
  var resourceP = $(
    "<p class=\"mb-0\"><span class=\"mr-10\">" + courseSectionResource.num + "</span>" +
      courseSectionResource.title +
      "<i class=\"fa fa-edit ml-5\"></i>" +
      "<i class=\"fa fa-trash-o ml-10\"></i></p>"
  );
  var sectionBtnGroup = $('.section-panel-' + courseSectionResource.course_section_id);
  sectionBtnGroup.find('.section-resource-group').append(resourceP);
}
var fileAjaxForm = fileForm.ajaxForm({
	url: '/tutor/course/submitResource',
	beforeSend: function(){
	//提交表單前的操作
	fileForm.find('.uploading-btn').removeClass('hide');
	fileForm.find('.upload-btn').addClass('hide');
	fileForm.find('.add-library-btn').addClass('hide');
	var uploadFileName = fileForm.find('input[name=path]').val().replace(/.*\\([^\\]+)$/,"$1");
	uploadDiv = addResourceUploadPanel(courseSectionId, uploadFileName);
},
uploadProgress: function(event, position, total, percentComplete) {
  var percentVal = percentComplete + '%';
  uploadDiv.find('.progress-bar').width(percentVal)
  uploadDiv.find('.progress-bar').html(percentVal);
},
success:function(data){
var data = JSON.parse(data);//将data转为JSON数据
if (data.success) {
  //正确操作
  console.log(data.result);
  // 关闭表单
  closeFileForm(newFilePanel);
  addResourcePanel(data.result);
} else {
  //错误操作
  if (data.returnType == 'url') {
    console.log(data.url);
    location.href = data.url;
  }
  if (data.returnType == 'errors') {
    console.log(data.errors);
    $(".help-block").html("");
    $(".form-group").removeClass('has-error');
    var errors = data.errors;
    for (var key in errors) {
      var formGroup = fileForm.find('input[name='+key+']').parent();
      formGroup.addClass('has-error');
      formGroup.addClass('is-focused');
      formGroup.find('.help-block').html(errors[key]);
    }
  }
}
},
//完成后恢复按钮
complete: function() {
uploadDiv.remove();
fileForm.find('.uploading-btn').addClass('hide');
fileForm.find('.upload-btn').removeClass('hide');
fileForm.find('.add-library-btn').removeClass('hide');
}
});
```