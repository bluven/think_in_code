# 支持TLS的Docker Registry


## 1. 用docker-compose.yml运行一个基本的registry

### 用docker-compose运行registry
```
mkdir -p ~/registry/data
cd ~/registry
```
创建docker-compose.yml
```
registry:
  image: registry:2.4.0
  ports:
    - 127.0.0.1:5000:5000
  environment:
    REGISTRY_STORAGE_FILESYSTEM_ROOTDIRECTORY: /data
  volumes:
    - ./data:/data
```
运行
```
docker-compose up -d
```

### 设置nginx

创建nginx目录
```
mkdir ~/registry/nginx
```
添加nginx配置文件
```
upstream docker-registry {
  server registry:5000;
}

server {
  listen 443;
  server_name myregistrydomain.com;

  # SSL
  # ssl on;
  # ssl_certificate /etc/nginx/conf.d/domain.crt;
  # ssl_certificate_key /etc/nginx/conf.d/domain.key;

  # disable any limits to avoid HTTP 413 for large image uploads
  client_max_body_size 0;

  # required to avoid HTTP 411: see Issue #1486 (https://github.com/docker/docker/issues/1486)
  chunked_transfer_encoding on;

  location /v2/ {
    # Do not allow connections from docker 1.5 and earlier
    # docker pre-1.6.0 did not properly set the user agent on ping, catch "Go *" user agents
    if ($http_user_agent ~ "^(docker\/1\.(3|4|5(?!\.[0-9]-dev))|Go ).*$" ) {
      return 404;
    }

    # To add basic authentication to v2 use auth_basic setting plus add_header
    auth_basic "registry.localhost";
    auth_basic_user_file /etc/nginx/conf.d/registry.password;
    add_header 'Docker-Distribution-Api-Version' 'registry/2.0' always;

    proxy_pass                          http://docker-registry;
    proxy_set_header  Host              $http_host;   # required for docker client's sake
    proxy_set_header  X-Real-IP         $remote_addr; # pass on real client's IP
    proxy_set_header  X-Forwarded-For   $proxy_add_x_forwarded_for;
    proxy_set_header  X-Forwarded-Proto $scheme;
    proxy_read_timeout                  900;
  }
}
```

利用htpasswd创建一个身份验证文件：
```
cd nginx
htpasswd -c registry.password USERNAME
```

在docker-compose.yml里添加
```
nginx:
  image: "nginx:1.9"
  ports:
    - 5043:443
  volumes:
    - ./nginx/:/etc/nginx/conf.d:ro
```
重启容器
```
docker-compose restart
```

试访问：
```
curl http://USERNAME:PASSWORD@localhost:5043/v2/
```

## 2. 配置SSL

将nginx.conf里的SSL配置项注释去掉，去掉后如下

```
# SSL
ssl on;
ssl_certificate /etc/nginx/conf.d/domain.crt;
ssl_certificate_key /etc/nginx/conf.d/domain.key;
```

###　生成证书与私钥

生成CA的证书与私钥
```
openssl genrsa -out devdockerCA.key 2048
openssl req -x509 -new -nodes -key devdockerCA.key -days 10000 -out devdockerCA.crt
```

生成nginx的证书与私钥
```
openssl genrsa -out domain.key 2048
openssl req -new -key domain.key -out dev-docker-registry.com.csr
openssl x509 -req -in dev-docker-registry.com.csr -CA devdockerCA.crt -CAkey devdockerCA.key -CAcreateserial -out domain.crt -days 10000
```

在这里，我们使用的证书不是来自公有的CA机构，所以为了让客户端(docker)信任我们的证书，必须让客户端可以用我们的CA证书去验证nginx的证书
```
sudo mkdir /usr/local/share/ca-certificates/docker-dev-cert
sudo cp devdockerCA.crt /usr/local/share/ca-certificates/docker-dev-cert
sudo update-ca-certificates
```

如要有其他的客户端也需要相应的配置，具体实现看客户端的要求。

测试
```
curl https://bluven:yanshiyi@bluven.io/v2/
```
## 3. 让客户端相信自制的证书

因为这里的证书是自己生成的，一般的客户端访问我们搭建的服务会有问题，所以需要一些措施让客户端相信nginx使用的证书。

### requests for python

```
requests.get('https://github.com', verify='/path/to/certfile')
```
或者
```
requests.get('https://kennethreitz.com', verify=False)
```

### Docker client 

让docker client信任证书，有两个法子，一个是：
```
sudo mkdir /usr/local/share/ca-certificates/docker-dev-cert
sudo cp devdockerCA.crt /usr/local/share/ca-certificates/docker-dev-cert
sudo update-ca-certificates
```
这是上门用过的，还有一个
把证书放到`/etc/docker/certs.d/bluven.io`目录下，详情见：[https://docs.docker.com/engine/security/certificates/][1]

## 参考

[How To Set Up a Private Docker Registry on Ubuntu 14.04][2]  
[OpenSSL: Generating an RSA Key From the Command Line][3]  
[http://www.ruanyifeng.com/blog/2011/08/what_is_a_digital_signature.html][4]  


  [1]: https://docs.docker.com/engine/security/certificates/
  [2]: https://www.digitalocean.com/community/tutorials/how-to-set-up-a-private-docker-registry-on-ubuntu-14-04
  [3]: https://rietta.com/blog/2012/01/27/openssl-generating-rsa-key-from-command/
  [4]: http://www.ruanyifeng.com/blog/2011/08/what_is_a_digital_signature.html