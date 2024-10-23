## 打war包
```sh
jar cf0M app-0.0.1-SNAPSHOT.war META-INF org WEB-INF
```

## maven打包
```sh
mvn -e clean package -Dmaven.test.skip=true
```

## 导出依赖的jar包
```sh
mvn dependency:copy-dependencies -DoutputDirectory=C:/lib
```	

## idea不限制控制台行数
idea.properties添加如下配置：
```
idea.cycle.buffer.size=disabled
```

## 提取jar中的文件：
例如提取gateway-starter-V3.1.1.RELEASE.jar中config目录下的application.yml
```
C:\app\jdk1.8.0_202\bin\jar xvf gateway-starter-V3.1.1.RELEASE.jar config\application.yml
```

说明：config\application.yml会被提取到当前目录

## 更新jar包中的文件：
例如更新gateway-starter-V3.1.1.RELEASE.jar中config目录下的application.yml:

```sh
jar -uvf gateway-starter-V3.1.1.RELEASE.jar config\application.yml
```

注意config\application.yml是jar里的路径，也是当前执行目录的相对路径
