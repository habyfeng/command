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

