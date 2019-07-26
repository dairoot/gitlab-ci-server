# 部署 gitlab-ci 服务

## 1. 部署前操作
```markdown
- 域名映射
- nginx配置 反代8880，8443 端口
- 配置docker-compose 参数
```

## 2. 生成CI 密钥 (供CI操作，访问远程服务使用)
```bash
# 指定密钥存放位置(请切换到源码位置) 
# CI_SSH_PATH 默认: /root/.ssh 
# 最好将 CI_SSH_PATH 写入 ~/.bashrc文件
export CI_SSH_PATH=`pwd`/ssh 
mkdir $CI_SSH_PATH && chmod 700 $CI_SSH_PATH

# 生成ssh key
ssh-keygen -f $CI_SSH_PATH/id_rsa
```

## 3. 部署服务
```bash
# 运行完毕可访问 https://gitlab.dairoot.cn
docker-compose up
```

## 4. 注册 runner  
```bash
# https://gitlab.dairoot.cn/admin/runners
# 进入 gitlab-runner 容器
docker exec -it gitlab-runner /bin/bash
# 添加一个runner 服务 （注：volumes 必须为主机的绝对路径（非docker））
gitlab-runner register --url http://gitlab \
--docker-volumes $CI_SSH_PATH:/root/.ssh:ro
、--executor docker --tag-list "test" --docker-image docker:stable
```

## 5. runner web 配置
```
# 取消默认 Auto DevOps
https://gitlab.dairoot.cn/admin/application_settings/ci_cd

# 运行未标记的作业
https://gitlab.dairoot.cn/admin/runners/1
```
