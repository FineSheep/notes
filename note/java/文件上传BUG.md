# 文件上传BUG

1. 需指定`Content-Type`，以防格式为`json`

> ```js
> headers: {'Content-Type': 'multipart/form-data'},
> ```

```js
            // 设置好头部
            uploadFile(parameter) {
                return axios({
                    url: 'http://127.0.0.1:8080/img/personUrl',
                    method: 'post',
                    headers: {'Content-Type': 'multipart/form-data'},
                    data: parameter
                })
            },
```

2. 当后端接收多个参数时，使用注解`@RequestParam`，否则报错

> 使用Vo接收时，尝试报错

```java
@PostMapping("/personUrl")
public BaseResponse uploadImage(@RequestParam("file") MultipartFile file, @RequestParam Long userId) {
    if (file == null || userId == null) {
        return ResultUtils.error(ErrorCode.PARAMS_ERROR);
    }
    FileInfo fileInfo = uploadImgService.userUrl(file, userId);
    return ResultUtils.success(fileInfo);
}
```

3. 前端上传

```js
upload(file) {
    const form = new FormData()
    const userId = Number(localStorage.getItem("userId"))
    form.append('file', file.file)
    form.append('userId', userId)
    // console.log(form)
    this.uploadFile(form).then(function (res) {
        console.log(res.data)
    })
```

```java
response.setHeader("content-type", "application/octet-stream");
response.setContentType("application/octet-stream");
//txt文本格式
response.setHeader("Content-Disposition", "attachment;filename=" + flowId + ".txt");
```

## 前端下载

```js
    var eleLink = document.createElement("a");
    console.log(res);
    eleLink.download = `${id}.txt`;
    eleLink.style.display = "none";
    // 字符内容转变成blob地址
    var blob = new Blob([res]);
    eleLink.href = URL.createObjectURL(blob);
    // 自动触发点击
    document.body.appendChild(eleLink);
    eleLink.click();
    // 然后移除
    document.body.removeChild(eleLink);
```