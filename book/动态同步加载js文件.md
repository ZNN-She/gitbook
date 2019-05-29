#动态同步加载js文件 onload和onreadystatechange
```
// 加载资源的方法，类似第三方模块加载器
// 加载js -- 每次都加载
window.seriesLoadScripts = function (scripts, callback) {
    var HEAD = document.getElementsByTagName('head')[0] || document.documentElement;
    var s = [];
    var last = scripts.length - 1;
    //递归
    var recursiveLoad = function (i) {
        s[i] = document.createElement('script');
        s[i].setAttribute('type', 'text/javascript');
        // Attach handlers for all browsers
        // 异步
        // Firefox、Opera, Chorme 和 Safari 3+会在<script>节点接收完成之后发出一个 onload 事件 Internet Explorer 支持另一种实现方式，它发出一个 readystatechange 事件
        // 在这里有另外一种写法比较好明白 最后有代码
        s[i].onload = s[i].onreadystatechange = function () {
        
    		//Internet Explorer 对这两个 readyState 值所表示的最终状态并不一致 可能是complete也可能是loaded
    		//!/*@cc_on!@*/0 判断ie浏览器
            if (!/*@cc_on!@*/0 || this.readyState === 'loaded' || this.readyState === 'complete') {
            
            	//删除 readystatechange 事件（保证事件不会被处理两次）
                this.onload = this.onreadystatechange = null;
                this.parentNode.removeChild(this);
                if (i !== last) {
                    recursiveLoad(i + 1);
                } else if (typeof(callback) === 'function') {
                    callback()
                }
            }
        };
        // 同步
        s[i].setAttribute('src', scripts[i]);
        HEAD.appendChild(s[i]);
    };
    recursiveLoad(0);
};
```

##onreadystatechange
	readyState
	“uninitialized”：默认状态
	“loading”：下载开始
	“loaded”：下载完成
	“interactive”：下载完成但尚不可用
	“complete”：所有数据已经准备好 
Internet Explorer 对这两个 readyState 值所表示的最终状态并不一致，有时< script >元素会得到“loader”却从不出现“complete”，但另外一些情况下出现“complete”而用不到“loaded”。最安全的办法就是在 readystatechange 事件中检查这两种状态，并且当其中一种状态出现时，删除 readystatechange 事件句柄（保证事件不会被处理两次）

##另一种写法
```
function loadScript(url, callback){
    var script = document.createElement ("script")
    script.type = "text/javascript";
    if (script.readyState){ //IE
        script.onreadystatechange = function(){
            if (script.readyState == "loaded" || script.readyState == "complete"){
                script.onreadystatechange = null;
                callback();
            }
        };
    } else { //Others
        script.onload = function(){
            callback();
        };
    }
    script.src = url;
    document.getElementsByTagName("head")[0].appendChild(script);
}
```

