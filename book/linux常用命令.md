####linux端口号与PID的互相查询
    * 由端口号查询PID号 sudo netstat -antup
    * 用PID查询端口号 sudo netstat -antup|grep PID号
    * 用进程名查询PID号 sudo ps -ef|grep 进程
    * rm -ef 文件名/文件名 删除文件
    * mkdir 文件夹名 新建文件夹
    * vi 文件名  新建文件

####自定义命令alias
  alias 查看所有别名
  alias 别名='原命令'
  alias unalias 删除

   alias sb='open -a "Sublime Text"'
   alias vs='open -a "Visual Studio Code"'
   alias ws='open -a "WebStorm"'
   alias znn='expect ~/shell/znn/znnsnake'
   alias gg='open -a "Google Chrome"'
   alias ggb='open -a "Google Chrome" https://baidu.com'
  可以放在.bash_profile文件里，重新运行下这个文件就永久生效了
