
# 说明
1. 1-docker[ghost+nginx]
利用nginx反代ghost，反代配置仅实现最基本要求
2. 2-docker[ghost+nginx-proxy]
利用nginx-proxy反代ghost，目前比较简单常用的方式，不用再自己编写配置了，但如果想自定义配置可以参照下文
3. 3-docker[ghost+nginx-proxy+docker-letsencrypt-nginx-proxy-companion]
在2的基础上添加自动ssl功能，该docker-compose.yml未经测试，因为我用自己的域名执行了无数次测试，导致域名申请达到letsencrypt上限了！建议本机环境测试该yml
4. example.com 替换为自己的域名
5. 备份ghost只需要备份与docker-compose.yml同目录的ghost文件夹即可
# 其他
## docker
1. 如果不“显示”指定网络，则会attach到同一个默认网络，模式为bridge
```
Creating network "dockerghostnginx_default" with the default driver
Creating dockerghostnginx_nginx_1 ... done
Creating dockerghostnginx_ghost_1 ... done
Attaching to dockerghostnginx_nginx_1, dockerghostnginx_ghost_1
```
2. 需要持久化的数据或者配置文件可以映射到宿主目录，如果是启动container必要的目录被映射到宿主空目录，则会启动异常，例如直接映射nginx的/etc/nginx 目录，此时有两种方式解决：

- 提前从container拷贝/etc/nginx至host路径再执行映射（[可参照此链接](http://www.ruanyifeng.com/blog/2018/02/nginx-docker.html)）
```
#copy
docker container cp container-name:/etc/nginx ./host-nginx-path
```
```
#docker-compose.yml volume
- ./host-nginx-path:/etc/nginx
```
- 利用DockerFile直接拷贝配置至对应目录（本仓库目录1的做法）
```
#Dockerfile
FROM nginx:latest
COPY ./conf/ooiii.com.conf /etc/nginx/conf.d
```

## nginx-proxy
1. nginx-proxy会根据nginx.tmpl生成默认的反代配置位于
` /etc/nginx/conf.d/default.conf`
自定义配置参照上述配置自定义方式，nginx.tmpl也可直接从github下载
```
#下载nginx.tmpl
#curl -LO https://raw.githubusercontent.com/jwilder/nginx-proxy/master/nginx.tmpl
```
2. 手动申请的ssl证书需要放在`/etc/nginx/certs`映射的宿主目录中，命名需与被代理的webapp中的`VIRTUAL_HOST`一致，如果使用`letsencrypt-nginx-proxy-companion`，该操作会自动完成。
# 相关链接
  1. https://github.com/jwilder/nginx-proxy
  2. https://github.com/JrCs/docker-letsencrypt-nginx-proxy-companion
  3. https://github.com/gilyes/docker-nginx-letsencrypt-sample
  4. https://github.com/evertramos/docker-compose-letsencrypt-nginx-proxy-companion

