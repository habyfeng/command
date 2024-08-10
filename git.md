# 拉取kafka源码
```sh
git clone https://github.com/apache/kafka.git
git clone git@github.com:apache/kafka.git

git fetch origin

git checkout -b 0.10.2 origin/0.10.2
```


创建本地开发分支：
```sh
cd AppService
git checkout br_develop
```

配置远程分支：
```sh
git remote add codehub ssh://
```
	
从远端服务器中获取某个分支的更新，再与本地指定的分支进行自动合并：
```sh
git pull origin be_develop
```

拉取项目仓远程分支信息到本地仓：
```sh	
get fetch codehub
```
	
将更新merge到本地：
```sh
get merge codehub/br_develop
```
	
推送代码到远程分支：
```sh
git push origin br_develop
```
	
查看本地仓库添加的远程仓库地址：
```sh
git remote -v
```
	
获取原始仓（主库仓）的branch分支最新代码到本地，合并两个版本的代码（fetch + merge 不建议使用）
```sh
git pull codehub br_develop
```