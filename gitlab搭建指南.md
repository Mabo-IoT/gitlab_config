# gitlab 搭建指南

本文用于指导如何搭建一个gitlab，以及其相关的CI、证书的指南。

本文使用私有证书，公有证书不太清楚，但理论上用法差不多。

所有应用都使用docker进行搭建。

## 私有证书生成与设置

1. 在服务器上，需要编辑openssl的相关配置。

   > https://stackoverflow.com/questions/44458410/gitlab-ci-runner-ignore-self-signed-certificate

   ```
   vim /etc/pki/tls/openssl.cnf
   
   [ v3_ca ]
   subjectAltName=IP:192.168.253.131 <---- Add this line. 192.168.1.1 is your GitLab server IP.
   ```

2. 生成自签名证书

   ```
   sudo openssl req -x509 -nodes -days 36500 -newkey rsa:2048 -keyout 192.168.253.131.key -out 192.168.253.131.crt -subj "/C=US/CN=gitlab/O=gitlab.com"
   sudo openssl dhparam -out dhparam.pem 2048
   ```

   这样生成3个文件，将这三个文件放入`\gitlab_config\config`中。

   **注意修改对应的ip**

## gitlab启动

服务器配置要求：

4GB RAM和4或8个CPU内核。

<!--如果需求不足，还是推荐使用GitHub。几亿人在为GitHub debug呢。-->

这里以docker-compose的方式进行gitlab的启动操作。

主要使用端口有（原始默认===》实际使用）（以泛亚实际环境为例）。

- 80 （HTTP）===》未使用
- 443 （HTTPS）====》9091
- 5000  (registry) ====》9095

**注意:这个库存放于内网对应的库中。**

详见[docker-compose.yaml](实际部署\gitlab_config\docker-compose.yaml)

需要挂载的目录：

```
		- './config:/etc/gitlab'			# 配置
        - './logs:/var/log/gitlab'			# 日志
        - './data:/var/opt/gitlab'			# 数据卷
        - './registry:/var/lib/registry'	# registry的保存卷
```

配置使用docker的环境变量，`GITLAB_OMNIBUS_CONFIG`进行配置。

配置信息简略说明：

> https://zhuanlan.zhihu.com/p/49499229
>
> https://docs.gitlab.com/omnibus/settings/configuration.html

- `external_url`：为了让`GitLab`向用户显示正确的存储库克隆链接，它需要知道用户可以访问的URL。
- `nginx` 三连：`gitlab`使用`Nginx`作为代理分发，所以注意配置其`https`证书，就用上一步生成的证书即可。
- `registry`：注意`enable`它。（理论上也可以使用其他的`registry`）
- `registry_nginx`：这里使用上一步生成的证书即可。（对`ci`等玩意有好处，更主要是轻松。）

```
docker-compose up -d
```

然后启动，等待一会即可。

通过以下命令查看日志：

```
docker logs --tail 10 -f <gitlab-containner-name>
```

