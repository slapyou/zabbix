1. 启用nginx status配置
检查是否带有ngx_status模块，如有可以设置，如果没有需要安装

nginx -V 2>&1 |grep -o http_stub_status_module --color

在默认主机里面加上location或者你希望能访问到的主机里面。

server {
    listen  *:80 default_server;
    server_name _;
    location /status 
    {
        stub_status on;
        access_log off;
        allow 127.0.0.1;
        #deny all;
    }
}

2. 刷新nginx配置文件
请依照你的环境重启你的nginx

# service nginx reload

3. 打开status页面
# curl http://127.0.0.1/status
Active connections: 11921 
server accepts handled requests
 11989 11989 11991 
Reading: 0 Writing: 7 Waiting: 42


4. nginx status详解
active connections – 活跃的连接数量
server accepts handled requests — 总共处理了11989个连接 , 成功创建11989次握手, 总共处理了11991个请求
reading — 读取客户端的连接数.
writing — 响应数据到客户端的数量
waiting — 开启 keep-alive 的情况下,这个值等于 active – (reading+writing), 意思就是 Nginx 已经处理完正在等候下一次请求指令的驻留连接.


5、zabbix客户端配置

自定义的UserParameter加入配置文件，然后重启agentd，如下：
[root@xxxxxxx]# cat /etc/zabbix/zabbix_agentd.conf.d/zbx_nginx.conf
UserParameter=nginx[*],/bin/bash  /etc/zabbix/scripts/nginx_status.sh $1

6、将脚本文件放到/etc/zabbix/scripts/权限设置为755
[root@xxxxxx]# ll  /etc/zabbix/scripts/nginx_status.sh
-rwxr-xr-x 1 root root 1129 Jan  7 10:19 /etc/zabbix/scripts/nginx_status.sh

7、重启zabbix-agent
service zabbix-agent restart

8、在server端web导入zabbix模板配置文件


Tpl_NGINX_http_agent.xml




