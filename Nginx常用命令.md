##  nginx常用命令

###  常用命令

1. 帮助命令：nginx -h

2. 启动Nginx服务器 ：nginx

3. 查看进程： ps aux | grep nginx

4. 检查配置文件：nginx -t

5. 配置文件路径：/usr/local/nginx/conf/nginx.conf

6. 指定启动配置文件：nginx -c /usr/local/nginx/conf/nginx.conf

7. 暴力停止服务：nginx -s stop

8. 优雅停止服务：nginx -s quit

9. 重新加载配置文件：nginx -s reload

10. 重启服务： service nginx restart

11. 查看上一个命令是否执行成功 返回0执行成功 其他不成
    echo $?

###  负载均衡策略

1. 轮询（默认）
   每个请求按时间顺序逐一分配到不同的后端服务器，如果后端服务器down掉，能自动剔除。
2. weight(加权轮询)
   指定轮询几率，weight和访问比率成正比，用于后端服务器性能不均的情况。
3. ip_hash（客户端ip哈希结果分配）
   每个请求按访问ip的hash结果分配，这样每个访客固定访问一个后端服务器，可以解决session的问题
4. fair（响应时间）
   按后端服务器的响应时间来分配请求，响应时间短的优先分配
5. url_hash（客户端url哈希结果分配）
   按访问url的hash结果来分配请求，使每个url定向到同一个（对应的）后端服务器，后端服务器为缓存时比较有效。
6. least_conn(最小连接数)
   将请求优先分配给压力较小的服务器，它可以平衡每个队列的长度，并避免向压力大的服务器添加更多的请求。