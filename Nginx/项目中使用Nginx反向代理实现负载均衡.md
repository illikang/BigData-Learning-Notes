# 项目中使用Nginx反向代理实现负载均衡

## 构建反向代理
Nginx只处理静态不处理动态内容，动态内容交给后台Tomcat处理。也就是说，网站所有的静态内容，包括html,css,js（前端工程）都放到Nginx服务器上，而动态内容则放到Tomcat上。
```
upstream itripbiz_server{

  server 127.0.0.1:8080
}

server{
  listen  80;     //服务器监听端口
  server_name  itrip.project.bdqn.cn;   //定义服务器名称，hosts文件中也要对应设置
  root   /data/itrip/itripfront   //前段工程发布路径
  index  index.html    //服务器默认页

  location / {
    proxy_set_header Host $host;
    proxy_set_header X-Real-IP $remote_addr  //将request请求中的host和客户端的真实IP代理到后端的Tomcat，也就是说后端的Java程序能取到前端的真实IP
    proxy_pass http://itripbiz_server;
  }

  location ~ .*\.(gif|jpg|png|html|mp3){
      expires 6h;
    }      //设置静态文件的缓存
  location ~ .*\.(js|css)?&{
      expire 2h;
  }
  access_log /data/logs/nginx/app_access.log   //本机日志


}
```
