```
// Url参数转成对象
function urlParamToJson (url) {

    var urlObject = {};

    if ((/\?/u).test(url)) {

        var urlString = url.substring(url.indexOf("?") + 1);
        var urlArray = urlString.split("&");

        for (var i = 0, len = urlArray.length; i < len; i++) {

            var urlItem = urlArray[i];
            var item = urlItem.split("=");

            urlObject[item[0]] = item[1];

        }
        return urlObject;

    }

    return urlObject;

}

// 获取url的指定参数值
function getUrlParam (name, url) {
    if(!name){
        return '';
    }
    url = url || window.location.search;
    name = name.replace(/(?=[\\^$*+?.():|{}])/, '\\');
    var reg = new RegExp('(?:[?&]|^)' + name + '=([^?&#]*)', 'i');
    var match = url.match(reg);
    return !match ? '' : match[1];
}
```