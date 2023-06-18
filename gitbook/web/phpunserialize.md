[![5EPNff.png](https://z3.ax1x.com/2021/10/10/5EPNff.png)

#### 序列化			[原文链接](https://www.cnblogs.com/20175211lyz/p/11403397.html)

所有php里面的值都可以使用函数serialize()来返回一个包含字节流的字符串来表示。unserialize()函数能够重新把字符串变回php原来的值。 序列化一个对象将会保存对象的所有变量，但是不会保存对象的方法，只会保存类的名字

#### 魔术方法

[php官方文档中关于魔术方法的部分](https://secure.php.net/manual/zh/language.oop5.magic.php)

当调用创建的对象时就会触发魔术方法

__construct()//创建对象时触发

__destruct() //对象被销毁时触发

__call() //在对象上下文中调用不可访问的方法时触发

__callStatic() //在静态上下文中调用不可访问的方法时触发

__get() //用于从不可访问的属性读取数据

__set() //用于将数据写入不可访问的属性

__isset() //在不可访问的属性上调用 isset()或 empty()触发

__unset() //在不可访问的属性上使用 unset()时触发

__invoke() //当脚本尝试将对象调用为函数时触发

serialize() 	//将一个对象转换成一个字符串

unserialize()	 //将字符串还原成一个对象

public属性被序列化的时候属性值会变成属性名
protected属性被序列化的时候属性值会变成\x00*\x00属性名
private属性被序列化的时候属性值会变成\x00类名\x00属性名
其中：\x00表示空字符，但是还是占用一个字符位置
这就是为什么上面的payload中serialize($a)执行后的序列化字符串中属性file变成Demofile，长度为10
原文链接：https://blog.csdn.net/qq_41617034/article/details/104573548

##### 2.1构造函数和析构函数

- __construct()
  具有构造函数的类会在每次创建新对象时先调用此方法，所以非常适合在使用对象之前做一些初始化工作。
- __destruct()
  析构函数会在到某个对象的所有引用都被删除或者当对象被显式销毁时执行。

`new`出一个新的对象时就会调用__construct(),而对象被销毁时，例如程序退出时，就会调用__destruct()

##### 2.2`__sleep()`和`__wakeup()`

![img](https://img2018.cnblogs.com/blog/1270588/201908/1270588-20190824032542441-1837350348.png)

##### 2.3 __tostring()

![img](https://img2018.cnblogs.com/blog/1270588/201908/1270588-20190824032551899-928220919.png)

##### 2.4  `__set(), __get(), __isset(),__unset()`

![img](https://img2018.cnblogs.com/blog/1270588/201908/1270588-20190824032610474-94470061.png)

##### 2.5  `__invoke(), __call()`

当尝试以调用函数的方式调用一个对象时，`__invoke()` 方法会被自动调用。
在对象中调用一个不可访问方法时，`__call()` 会被调用。

##### 2.6  其他

__callStatic(), __set_state(), __clone(), __debugInfo()等和序列化没有多大关系，详情参考官网

#### 序列化细节

##### 3.1  序列的含义

例如：`O:4:"user":2:{s:3:"age";i:18;s:4:"name";s:3:"LEO";}`
O代表对象；4代表对象名长度；2代表2个成员变量；其余参照如下

![5EiUER.png](https://z3.ax1x.com/2021/10/10/5EiUER.png)

| 类型      | 结构                                       |
| ------- | ---------------------------------------- |
| String  | s:size:value;                            |
| Integer | i:value;                                 |
| Boolean | b:value;(保存1或0)                          |
| Null    | N;                                       |
| Array   | a:size:{key definition;value definition;(repeated per element)} |
| Object  | O:strlen(object name):object name:object size:{s:strlen(property name):property name:property definition;(repeated per property)} |

##### 3.2 public、protected、private下序列化对象的区别

php v7.x反序列化的时候对访问类别不敏感

- public变量
  直接变量名反序列化出来
- protected变量
  `\x00 + * + \x00 + 变量名`
  可以用`S:5:"\00*\00op"`来代替`s:5:"?*?op"`
- private变量
  `\x00 + 类名 + \x00 + 变量名`

##### 3.3  反序列化中s和S的区别

如果类型是`S`，会调用以下函数，简单来说就是将`\`解释成十六进制，来转成字符

~~~php
static zend_string *unserialize_str(const unsigned char **p, size_t len, size_t maxlen)
{
	size_t i, j;
	zend_string *str = zend_string_safe_alloc(1, len, 0, 0);
	unsigned char *end = *(unsigned char **)p+maxlen;

	if (end < *p) {
		zend_string_efree(str);
		return NULL;
	}

	for (i = 0; i < len; i++) {
		if (*p >= end) {
			zend_string_efree(str);
			return NULL;
		}
		if (**p != '\\') {
			ZSTR_VAL(str)[i] = (char)**p;
		} else {
			unsigned char ch = 0;

			for (j = 0; j < 2; j++) {
				(*p)++;
				if (**p >= '0' && **p <= '9') {
					ch = (ch << 4) + (**p -'0');
				} else if (**p >= 'a' && **p <= 'f') {
					ch = (ch << 4) + (**p -'a'+10);
				} else if (**p >= 'A' && **p <= 'F') {
					ch = (ch << 4) + (**p -'A'+10);
				} else {
					zend_string_efree(str);
					return NULL;
				}
			}
			ZSTR_VAL(str)[i] = (char)ch;
		}
		(*p)++;
	}
	ZSTR_VAL(str)[i] = 0;
	ZSTR_LEN(str) = i;
	return str;
}
~~~

#### 反序列化的利用

__wakeup失效
php版本`< 5.6.25 | < 7.0.10`
当序列化字符串中，如果表示对象属性个数的值大于真实的属性个数时就会跳过__wakeup()的执行
例：

~~~php
O:4:"Demo":1:{s:10:"Demofile";s:16:"f15g_1s_here.php";}
O:4:"Demo":2:{s:10:"Demofile";s:16:"f15g_1s_here.php";}
~~~

使用`+`绕过正则
例：

`preg_match('/[oc]:\d+:/i', $var)O:4:"Demo":1:{s:10:"Demofile";s:16:"f15g_1s_here.php";}O:+4:"Demo":1:{s:10:"Demofile";s:16:"f15g_1s_here.php";}`

#### Session序列化问题

#### Phar反序列化



##### phar 触发点

- fileatime / filectime / filemtime

- stat / fileinode / fileowner / filegroup / fileperms

- file / file_get_contents / readfile / fopen`

- file_exists / is_dir / is_executable / is_file / is_link / is_readable / is_writeable / is_writable

- parse_ini_file

- unlink

- copy

- - exif

    exif_thumbnailexif_imagetype

- - gd

    imageloadfontimagecreatefrom***

- - hash

    hash_hmac_filehash_filehash_update_filemd5_filesha1_file

- - file / url

    get_meta_tagsget_headers

- - standard

    getimagesizegetimagesizefromstring

##### 原生类利用

#### Tricks



#### 魔术方法

##### Magic函数

- `__construct()` 构建对象的时被调用
- `__destruct()` 销毁对象或脚本结束时被调用
- `__call()` 调用不可访问或不存在的方法时被调用
- `__callStatic()` 调用不可访问或不存在的静态方法时被调用
- `__get()` 读取不可访问或不存在属性时被调用
- `__set()` 给不可访问或不存在属性赋值时被调用
- `__isset()` 对不可访问或不存在的属性调用 `isset` 或 `empty()` 时被调用
- `__unset()` 对不可访问或不存在的属性进行 `unset` 时被调用
- `__sleep()` 对象序列化时被调用
- `__wakeup()` 对象反序列化时被调用，其中序列化字符串中表示对象属性个数的值大于真实的属性个数时会跳过wakeup的执行
- `__toString()` 当一个类被转换成字符串时被调用
- `__invoke()` 对象被以函数方式调用时被调用
- `__set_state()` 调用 `var_export()` 导出类时被调用
- `__clone()` 进行对象clone时被调用
- `__debugInfo()` 调用 `var_dump()` 打印对象时被调用







