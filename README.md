
# ELK 日志收集Docker安装记录
平台：window 10

版本： 8.6.X版本  
当前情况：日志配置未完成

宿主机项目配置目录 :  
```
/D/CodeProject/DockerTest/ELK/
```

# 安装步骤
## elasticsearch
```
docker pull elasticsearch:8.6.2
```

elasticsearch.yml文件添加允许跨域访问(官方文档没有，不用加)
```
http.cors.enabled: true

http.cors.allow-origin: "*"
```

文件映射：
```
docker run -d --name elasticsearch --net elk -v /D/CodeProject/DockerTest/ELK/elasticsearch/elasticsearch.yml:/usr/share/elasticsearch/config/elasticsearch.yml -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:8.6.2
```
不映射：

echo "http.cors.enabled: true" >> elasticsearch.yml
```
docker run -d --name elasticsearch --net elk  -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" elasticsearch:8.6.2
```


查看容器IP:
```
docker insepct elasticsearch
```



## kibana
版本对应：  
https://www.elastic.co/cn/support/matrix#matrix_compatibility

8.6.* = 》8.6.*

docker pull kibana:8.6.2
```
docker run --name kibana --net elk -d -p 5601:5601 kibana:8.6.2
```
把里面的配置文件映射出来
```
docker run --name kibana --net elk -d -p 5601:5601 -v /D/CodeProject/DockerTest/ELK/kibana:/usr/share/kibana/config   kibana:8.6.2

```
不映射：

```
docker run --name kibana --net elk -d -p 5601:5601    kibana:8.6.2

```
修改文件：/usr/share/kibana/config/kibana.yml es的ip

访问验证：kibanalocalhost:5601

## 创建测试数据卷

```
docker volume create –name test-log
```


## logstash

8.6.* = 》7.17.x - 8.6.x

docker pull logstash:8.6.2
```
docker run -d --name logstash --net elk -p 5044:5044 logstash:8.6.2
```

把里面的配置文件映射出来
```


```

不映射：
```
docker run -d --name logstash  --net elk  -p 5044:5044   logstash:8.6.2
```


# 启动UI，进入

进入elasticsearch容器，默认进入安装目录，然后cd 到bin目录  
执行：
```
elasticsearch-create-enrollment-token --scope kibana
```
生成token:
```
eyJ2ZXIiOiI4LjYuMiIsImFkciI6WyIxNzIuMjMuMC4yOjkyMDAiXSwiZmdyIjoiODQxNTQ5MjUxYzA0YWRlYzUzZDUyMTE2ZWI2Zjg5ZDc2YTU3YzM5ZmUxNTIzZDQ1MWQ2ZTFiZTIyMGE4MzUzZiIsImtleSI6IkNlaGVmWVlCX29reDlfMnlVZURnOlU0WVBOODl2UTVPRHdHb2JRNUxLUFEifQ==
```
浏览器进入：http://localhost:5601/
输入token 
进入kibana容器，默认进入安装目录，然后cd 到bin目录
执行脚本：
```
kibana-verification-code
```
得到6位数code输入到网页中

提示登录用户名密码

进入elasticsearch容器，默认进入安装目录，然后cd 到bin目录

执行：
```
elasticsearch-setup-passwords auto
```
生成几个默认的用户名密码，使用最后一个：
```
Changed password for user elastic
PASSWORD elastic = wIUD7YC34nSTh1zRhKhC
```
ok了

[](微信截图_20230223165429.png)