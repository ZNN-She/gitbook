####常用命令
    * pm2 start xxx.js --name 服务名字  不写服务名字会成文件名字
    * pm2 lsit 查看pm2启动的所有程序
    * pm2 stop name/id 停止对应的进程 参数为服务的名字或则id
    * pm2 stop all 停止所有的进程
    * pm2 delete name/id 删除进程
    * pm2 delete all 杀死所有进程
    * pm2 restart name/id/all 重启指定进程、全部进程
    
####配置文件
    module.exports = {
      apps : [
          {
              name: "gitbookWeb",
              script: "./app.js",
              watch: true,
              env: {
                  "PORT": 3000,
                  "NODE_ENV": "development",
                  "STATIC_PATH": "./webapp",
              },
              env_production: {
                  "PORT": 80,
                  "NODE_ENV": "production",
                  "STATIC_PATH": "../../../../../www/gitbook/_book",
              }
          },
          {
            "name": "mywork",
            "cwd": "/srv/node-app/current",
            "script": "bin/www",
            "log_date_format": "YYYY-MM-DD HH:mm Z",
            "error_file": "/var/log/node-app/node-app.stderr.log",
            "out_file": "log/node-app.stdout.log",
            "pid_file": "pids/node-geo-api.pid",
            "instances": 6,
            "min_uptime": "200s",
            "max_restarts": 10,
            "max_memory_restart": "1M",
            "cron_restart": "1 0 * * *",
            "watch": false,
            "merge_logs": true,
            "exec_interpreter": "node",
            "exec_mode": "fork",
            "autorestart": false,
            "vizion": false
          }
      ]
    }

	• apps:json结构，apps是一个数组，每一个数组成员就是对应一个pm2中运行的应用
	• name:应用程序名称
	• cwd:应用程序所在的目录
	• script:应用程序的脚本路径
	• log_date_format:
	• error_file:自定义应用程序的错误日志文件
	• out_file:自定义应用程序日志文件
	• pid_file:自定义应用程序的pid文件
	• instances: 1 启动的实例数量
	• min_uptime:最小运行时间，这里设置的是60s即如果应用程序在60s内退出，pm2会认为程序异常退出，此时触发重启max_restarts设置数量
	• max_restarts:设置应用程序异常退出重启的次数，默认15次（从0开始计数）
	• cron_restart:定时启动，解决重启能解决的问题
	• watch:是否启用监控模式，默认是false。如果设置成true，当应用程序变动时，pm2会自动重载。这里也可以设置你要监控的文件。
	• merge_logs:
	• exec_interpreter:应用程序的脚本类型，这里使用的shell，默认是nodejs
	• exec_mode:应用程序启动模式，这里设置的是cluster_mode（集群），默认是fork
	• autorestart:启用/禁用应用程序崩溃或退出时自动重启
    • vizion:启用/禁用vizion特性(版本控制)

    执行 pm2 start ecosystem.config.js --env production
    这时候 process.env.PORT 就是 80 了
    --env 参数用来区分不同环境
    
    app.js中就可以取到不同的值了
    const Koa = require("koa");
    const path = require("path");
    const bodyparser = require("koa-bodyparser");
    const Router = require("koa-router");
    const koaStatic = require("koa-static");
    
    const app = new Koa();
    const router = new Router();
    
    const staticPath = process.env.STATIC_PATH;
    const port = process.env.PORT;
    
    app.use(bodyparser());
    
    app.use(router.routes()).
        use(router.allowedMethods());
    
    app.use(koaStatic(path.join(__dirname, staticPath)));
    
    app.listen(port, () => {
    
        console.log("[Koa2] start-quick is starting at port " + port);
    
    });

