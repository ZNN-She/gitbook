#gitbook serve端口占用

##listen EADDRINUSE :::35729
```
... Uhoh. Got error listen EADDRINUSE :::35729 ...
Error: listen EADDRINUSE :::35729
    at Object._errnoException (util.js:1022:11)
    at _exceptionWithHostPort (util.js:1044:20)
    at Server.setupListenHandle [as _listen2] (net.js:1367:14)
    at listenInCluster (net.js:1408:12)
    at Server.listen (net.js:1492:7)
    at Server.listen (/Users/zhangnanning/.gitbook/versions/3.2.3/node_modules/tiny-lr/lib/server.js:164:15)
    at Promise.apply (/Users/zhangnanning/.gitbook/versions/3.2.3/node_modules/q/q.js:1165:26)
    at Promise.promise.promiseDispatch (/Users/zhangnanning/.gitbook/versions/3.2.3/node_modules/q/q.js:788:41)
    at /Users/zhangnanning/.gitbook/versions/3.2.3/node_modules/q/q.js:1391:14
    at runSingle (/Users/zhangnanning/.gitbook/versions/3.2.3/node_modules/q/q.js:137:13)

You already have a server listening on 35729
You should stop it and try again.
```

##原因
gitbook启动的web 服务默认监听4000端口，而重启监控进程默认监听35729端口

##指定端口号启动
```
gitbook serve --lrport 35288 --port 4001
```