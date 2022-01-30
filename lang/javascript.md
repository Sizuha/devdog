# JavaScript

## Tips

### 문서 안에서 요소 찾기

```javascript
document.findElementById('id').innerHTML = '변경할 내용';
```

### URL 이동

```javascript
document.location = "url...";
```

### JSON 문자열 얻기

```javascript
var json_str = JSON.stringify(object);
```

### POST 전송

```javascript
/*
 * path : 전송 URL
 * params : 전송 데이터 {'q':'a','s':'b','c':'d'...}으로 묶어서 배열 입력
 * method : 전송 방식(생략가능)
 */
function post_to_url(path, params, method) {
    method = method || "post"; // Set method to post by default, if not specified.
    // The rest of this code assumes you are not using a library.
    // It can be made less wordy if you use one.
    var form = document.createElement("form");
    form.setAttribute("method", method);
    form.setAttribute("action", path);
    for(var key in params) {
        var hiddenField = document.createElement("input");
        hiddenField.setAttribute("type", "hidden");
        hiddenField.setAttribute("name", key);
        hiddenField.setAttribute("value", params[key]);
        form.appendChild(hiddenField);
    }
    document.body.appendChild(form);
    form.submit();
}
```

**주의!**
Django에서는 이걸로 POST하면 안된다! Django에서는 이렇게 하면 CSRF 보안오류를 발생시킨다.

Django에서 사용할 때는 document.getElementById('form'); 으로 이미 존재하는 폼을 가져다가 필드를 추가하는 방식으로 구현하면 된다.

### base url 변경

```html
<meta http-equiv="content-type" content="text/html;charset=UTF-8" />
<head>
  <meta charset="utf-8" />
  <base href="http://localhost/" />

<script type="text/javascript">
function setBaseHref(basehref) {
  var theBase = document.getElementsByTagName("base");
  theBase[0].href = basehref;
}
</script> 

</head>
```
