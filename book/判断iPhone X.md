```
// 是否是iphone X
isIphonex () {

    var u = navigator.userAgent;
    var isIOS = Boolean(u.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/));

    if (isIOS) {

        // 这个高度是在iPhone X上是对的，但是在网页模拟器上不一定正确，大屏分辨率搞得可以
        if (screen.height === 812 && screen.width === 375) {

            return true;

        }

        return false;

    }

},
```