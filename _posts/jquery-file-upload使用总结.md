title: jquery-file-upload使用总结
categories: javascript##分类
tags: [file-upload,javascript]##标签，多标签格式为 [tag1,tag2,...]
keywords: file-upload,javascript##文章关键词，多关键词格式为 keyword1,keywords2,...
description: 一个优秀的异步上传文件插件
date: 2016/05/01 01:24:25 
---

``` html
<div class="row fileupload-buttonbar" style="padding-left:15px;">
    <div class="thumbnail">
        <img id="weixin_show" style="height:300px;margin-top:10px;margin-bottom:8px;" src="/uploads/head/head.jpg" data-holder-rendered="true">
        <div class="progress progress-striped active" role="progressbar" aria-valuemin="10" aria-valuemax="100" aria-valuenow="0">
            <div id="weixin_progress" class="progress-bar progress-bar-success" style="width:0%;"></div>
        </div>
        <div class="caption" align="center">
            <span id="weixin_upload" class="btn btn-primary fileinput-button">
                <span>上传</span>
                <input type="file" id="weixin_image" name="weixin_image" multiple>
            </span>
            <a id="weixin_cancel" href="javascript:void(0)" class="btn btn-warning" role="button" style="display:none">删除</a>
        </div>
    </div>
</div>
```
<!--more-->

``` javascript
$("#weixin_image").fileupload({
     acceptFileTypes: /(\.|\/)(gif|jpe?g|png)$/i,
     maxFileSize: 999000,
     url: '/data/upload',
     sequentialUploads: true,
     previewCrop: true
 }).on('fileuploadprogress', function (e, data) {
     var progress = parseInt(data.loaded / data.total * 100, 10);
     $("#weixin_progress").css('width', progress + '%');
     $("#weixin_progress").html(progress + '%');
 }).on('fileuploaddone', function (e, data) {
     d = data.result;
     console.log(e);
     console.log(data);
     if (d.status == 1) {
        $("#weixin_show").attr("src", d.url);
        $("#weixin_upload").css({display: "none"});
        $("#weixin_cancel").css({display: ""});
    } else {
        alert(d.msg);
    }
}).on('fileuploadfail', function (e, data) {
    console.log(e);
    console.log(data);
    alert(data.msg);
});
```

server端代码，java spring
``` java
@RequestMapping(value = "/upload", method = RequestMethod.POST, produces = {"application/json;charset=UTF-8"})
@ResponseBody
public String upload(@RequestParam("weixin_image") MultipartFile multipartFile) throws IOException {
    JSONObject res = new JSONObject();
    if (!multipartFile.isEmpty()) {
        String preName = "/uploads/head/";
        String subName = System.currentTimeMillis() + multipartFile.getOriginalFilename();
        FileUtils.copyInputStreamToFile(multipartFile.getInputStream(), new File("H:\\apache-tomcat-9.0.0.M4\\webapps\\contact\\src\\main\\webapp\\WEB-INF\\uploads\\head\\", subName));
        res.put("url", preName+subName);
        res.put("status", 1);
    } else {
        res.put("msg", "上传失败");
        res.put("status", 0);
    }
    return res.toString();
}
```

以上demo是单文件上传，若要开启多文件上传可以把`$("#weixin_upload").css({display: "none"});`这句注释掉