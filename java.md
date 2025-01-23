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

## 执行class
```sh
java -cp D:\work\toolkit\dev\toolkit\toolkit-io\target\toolkit-io-1.0.jar com.albert.toolkit.File.FileSysKit
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

## keytool

### 生成jks密钥库
```powershell
keytool -genkeypair -alias foo -keypass 123456 -keyalg RSA -storepass 123456 -keysize 2048 -validity 365 -keystore D:/data/cert/foo.jks
```

- -genkeypair： 这是生成密钥对的选项，用于生成一个新的密钥对
- -alias： 这是指定生成的密钥对的别名，用于标识该密钥对的名称。在密钥库中，每个条目（公钥/私钥对和证书）都有一个唯一的别名。
- -keyalg： 这是指定密钥算法的选项，如RSA、DSA、EC等。如果不指定，默认为RSA。
- -keysize：这是指定密钥长度的选项，如2048、3072、4096等。如果不指定，默认为2048。
- -keypass：这是指定密钥的密码的选项，用于保护生成的密钥对。如果不指定，默认与密钥库的密码相同。注意如果指定需要和密钥库的密码相同。 
- -storepass：这是指定密钥库的密码的选项，用于保护生成的密钥库。如果不指定，默认为空。
- -validity：有效天数
- -sigalg：签名算法名称
- -dname：唯一判别名，CN 所有者名称，OU 组织单位名称，O 组织名称，L 城市或区域名称，ST 州或省份名称，C 两字母国家代码

输入如下：
```
您的名字与姓氏是什么?
  [Unknown]:  losfoo
您的组织单位名称是什么?
  [Unknown]:  Layzei
您的组织名称是什么?
  [Unknown]:  ZooCourse
您所在的城市或区域名称是什么?
  [Unknown]:  SZ
您所在的省/市/自治区名称是什么?
  [Unknown]:  JS
该单位的双字母国家/地区代码是什么?
  [Unknown]:  CN
CN=losfoo, OU=Layzei, O=ZooCourse, L=SZ, ST=JS, C=CN是否正确?
  [否]:  Y
```

### 从密钥库导出证书
```powershell
keytool -exportcert -validity 365 -alias foo -file foo.cer -keystore foo.jks -keypass 123456 -storepass
 123456
```


### 列出keystore存在的所有证书
```powershell
 keytool -list -v -keystore foo.jks
```
输出结果如下：
```
输入密钥库口令:

密钥库类型: PKCS12
密钥库提供方: SUN

您的密钥库包含 1 个条目

别名: foo
创建日期: 2024年12月25日
条目类型: PrivateKeyEntry
证书链长度: 1
证书[1]:
所有者: CN=losfoo, OU=Layzei, O=ZooCourse, L=SZ, ST=JS, C=CN
发布者: CN=losfoo, OU=Layzei, O=ZooCourse, L=SZ, ST=JS, C=CN
序列号: bf712b7596dad169
生效时间: Wed Dec 25 17:04:10 CST 2024, 失效时间: Thu Dec 25 17:04:10 CST 2025
证书指纹:
         SHA1: 8B:8D:5E:E5:87:95:0D:06:F4:FA:44:6B:8A:1A:CD:BB:E9:37:54:93
         SHA256: E8:78:3C:F0:F2:94:92:B6:69:98:44:37:ED:81:BA:7B:F1:FE:A1:6D:E6:AA:D5:98:48:4F:36:74:B9:94:00:84
签名算法名称: SHA256withRSA
主体公共密钥算法: 2048 位 RSA 密钥
版本: 3

扩展:

#1: ObjectId: 2.5.29.14 Criticality=false
SubjectKeyIdentifier [
KeyIdentifier [
0000: AE FB 3C 27 15 63 85 52   78 14 D8 B7 73 F3 BF 5A  ..<'.c.Rx...s..Z
0010: 0D 3A 22 9F                                        .:".
]
]



*******************************************
*******************************************
```

> 说明：
- Subject和Issuer均为DN格式，常见格式为：C=国家,ST=省市, L=区县市, O=组织机构, OU=组织单位, CN=通用名称；

- DN是证书链查找的关键，验证时会根据Issuer的DN去匹配机构的证书，具体的匹配方法是比较DN的数量以及各DN参数值；

- DN中的CN通用名称一般为域名，如需支持子域名，例如server.com、bcd.server.com，可使用泛域名形式*.server.com；

- Subject Alternative Name扩展字段可用于多域名证书。

### 使用别名查看keystore指定条目
```powershell
keytool -list -v -keystore foo.jks -alias foo
```

### 列出信任的CA证书
```powershell
keytool -list -v -keystore $env:JAVA_HOME/lib/security/cacerts
```

> 备注： 默认密钥库口令：changeit

输出结果如下：
```
警告: 使用 -cacerts 选项访问 cacerts 密钥库
输入密钥库口令:

密钥库类型: JKS
密钥库提供方: SUN

您的密钥库包含 108 个条目

别名: actalisauthenticationrootca [jdk]
创建日期: 2011年9月22日
条目类型: trustedCertEntry

所有者: CN=Actalis Authentication Root CA, O=Actalis S.p.A./03358520967, L=Milan, C=IT
发布者: CN=Actalis Authentication Root CA, O=Actalis S.p.A./03358520967, L=Milan, C=IT
序列号: 570a119742c4e3cc
生效时间: Thu Sep 22 19:22:02 CST 2011, 失效时间: Sun Sep 22 19:22:02 CST 2030
证书指纹:
         SHA1: F3:73:B3:87:06:5A:28:84:8A:F2:F3:4A:CE:19:2B:DD:C7:8E:9C:AC
         SHA256: 55:92:60:84:EC:96:3A:64:B9:6E:2A:BE:01:CE:0B:A8:6A:64:FB:FE:BC:C7:AA:B5:AF:C1:55:B3:7F:D7:60:66
签名算法名称: SHA256withRSA
主体公共密钥算法: 4096 位 RSA 密钥
版本: 3

扩展:

#1: ObjectId: 2.5.29.35 Criticality=false
AuthorityKeyIdentifier [
KeyIdentifier [
0000: 52 D8 88 3A C8 9F 78 66   ED 89 F3 7B 38 70 94 C9  R..:..xf....8p..
0010: 02 02 36 D0                                        ..6.
]
]

#2: ObjectId: 2.5.29.19 Criticality=true
BasicConstraints:[
  CA:true
  PathLen: no limit
]

#3: ObjectId: 2.5.29.15 Criticality=true
KeyUsage [
  Key_CertSign
  Crl_Sign
]

#4: ObjectId: 2.5.29.14 Criticality=false
SubjectKeyIdentifier [
KeyIdentifier [
0000: 52 D8 88 3A C8 9F 78 66   ED 89 F3 7B 38 70 94 C9  R..:..xf....8p..
0010: 02 02 36 D0                                        ..6.
]
]



*******************************************
*******************************************

...

别名: xrampglobalca [jdk]
创建日期: 2004年11月2日
条目类型: trustedCertEntry

所有者: CN=XRamp Global Certification Authority, O=XRamp Security Services Inc, OU=www.xrampsecurity.com, C=US
发布者: CN=XRamp Global Certification Authority, O=XRamp Security Services Inc, OU=www.xrampsecurity.com, C=US
序列号: 50946cec18ead59c4dd597ef758fa0ad
生效时间: Tue Nov 02 01:14:04 CST 2004, 失效时间: Mon Jan 01 13:37:19 CST 2035
证书指纹:
         SHA1: B8:01:86:D1:EB:9C:86:A5:41:04:CF:30:54:F3:4C:52:B7:E5:58:C6
         SHA256: CE:CD:DC:90:50:99:D8:DA:DF:C5:B1:D2:09:B7:37:CB:E2:C1:8C:FB:2C:10:C0:FF:0B:CF:0D:32:86:FC:1A:A2
签名算法名称: SHA1withRSA
主体公共密钥算法: 2048 位 RSA 密钥
版本: 3

扩展:

#1: ObjectId: 1.3.6.1.4.1.311.20.2 Criticality=false
0000: 1E 04 00 43 00 41                                  ...C.A


#2: ObjectId: 1.3.6.1.4.1.311.21.1 Criticality=false
0000: 02 01 01                                           ...


#3: ObjectId: 2.5.29.19 Criticality=true
BasicConstraints:[
  CA:true
  PathLen: no limit
]

#4: ObjectId: 2.5.29.31 Criticality=false
CRLDistributionPoints [
  [DistributionPoint:
     [URIName: http://crl.xrampsecurity.com/XGCA.crl]
]]

#5: ObjectId: 2.5.29.15 Criticality=false
KeyUsage [
  DigitalSignature
  Key_CertSign
  Crl_Sign
]

#6: ObjectId: 2.5.29.14 Criticality=false
SubjectKeyIdentifier [
KeyIdentifier [
0000: C6 4F A2 3D 06 63 84 09   9C CE 62 E4 04 AC 8D 5C  .O.=.c....b....\
0010: B5 E9 B6 1B                                        ....
]
]



*******************************************
*******************************************
```

## 生成二级证书

### 1. 生成二级证书密钥库

二级证书需要由根证书签发

首先使用keytool生成二级证书的密钥库，但是此时还是自签的，我们需要从中生成一个二级证书请求（其中包含了二级证书的公钥）；
然后将证书请求发送到foo签发二级证书；

最后，我们将foo签发的二级证书导入到证书密钥库中，完成二级证书的生成。
下面命令是生成二级证书密钥库，密钥库的名称为subfoo，此时仍是自签的，证书颁发者和使用者都是自已：

```powershell
keytool -genkeypair -validity 365 -keyalg RSA -keypass 123456 -storepass 123456 -alias subfoo -keystore subfoo.jks
```

输入如下：
```
您的名字与姓氏是什么?
  [Unknown]:  subfoo.com
您的组织单位名称是什么?
  [Unknown]:  Developart
您的组织名称是什么?
  [Unknown]:  TechCenter
您所在的城市或区域名称是什么?
  [Unknown]:  SZ
您所在的省/市/自治区名称是什么?
  [Unknown]:  JS
该单位的双字母国家/地区代码是什么?
  [Unknown]:  CN
CN=subfoo.com, OU=Developart, O=TechCenter, L=SZ, ST=JS, C=CN是否正确?
  [否]:  Y
```

输出如下：
```
正在为以下对象生成 2,048 位RSA密钥对和自签名证书 (SHA256withRSA) (有效期为 365 天):
         CN=subfoo.com, OU=Developart, O=TechCenter, L=SZ, ST=JS, C=CN
```

### 2. 从二级证书密钥库中生成证书请求
```powershell
keytool -certreq -alias subfoo -file subfoo.csr -keystore subfoo.jks -keypass 123456 -storepass 123456
```

### 3. 使用根证书签发二级证书
通过keytool工具的签发证书功能，使用rootca对二级证书请求（subca.csr）签发一张二级证书。命令如下，alias指定证书的颁发者，infile指定证书请求文件，outfile是二级证书的文件名：

```powershell
keytool -gencert -validity 365 -alias foo -infile subfoo.csr -outfile subfoo.cer -keystore foo.jks -keypass 123456 -storepass 123456
```

### 4. 导入二级证书到密钥库中

因为本地生成的密钥库仍然是自签名的，此时需要将根证书签发的二级证书导入密钥库中，导入前需要先将自签名的根证书导入密钥库，这点非常重要，否则会报”无法从回复中建立链“，命令如下：
```powershell
keytool -importcert -alias foo -file foo.cer -keystore subfoo.jks -storepass 123456 -keypass 123456
```

然后，才能导入sub.cer到密钥库，alias subca就是之前用来生成密钥库或证书请求时用到的秘钥对别名：
```powershell
keytool -importcert -alias subfoo -file subfoo.cer -keystore subfoo.jks -storepass 123456 -keypass 123456
```