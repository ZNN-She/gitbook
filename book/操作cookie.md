```
const getCookie = (name) => {
    if (name != null) {
        var value = new RegExp("(?:^|; )" + encodeURIComponent(String(name)) + "=([^;]*)").exec(document.cookie);
        return value ? decodeURIComponent(value[1]) : null;
    }
};
const removeCookie = (name) => {
    document.cookie = name + "=v; max-age=0; path=/";
    setCookie(name, "", -1);
};
const setCookie = (name, value, time) => {
    // 当设置的时间等于0时，不设置expires属性，cookie在浏览器关闭后删除
    if(time !== 0){
        var exp = new Date();
        exp.setTime(exp.getTime() + time);
        document.cookie = name + "="+ escape(value) + ";expires=" + exp.toGMTString();
    }else{
        document.cookie = name + "=" + escape(value);
    }
};

const cookieUtils = {
    getCookie,
    setCookie,
    removeCookie
};
export default cookieUtils;
```