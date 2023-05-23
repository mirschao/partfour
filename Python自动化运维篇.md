# Python自动化运维

-author: liuchao

-email: mirschao@gmail.com

-github: https://github.com/mirschao

-gitee: https://gitee.com/mirschao

---



### 一、Python概述

Python是一门高级编程语言，它具有简洁、易读和强大的特点。以下是Python语言的历史和特点的简要介绍：

#### <u>1. Python的历史进程</u>

- Python由Guido van Rossum于1989年在荷兰创造，最初是作为一种用于编写脚本的语言而设计的。
- Python的设计理念强调代码的可读性和简洁性，借鉴了一些其他编程语言的优点，并且加入了一些自己的独特特性。
- Python的命名灵感来自于Guido van Rossum喜欢的电视剧《Monty Python's Flying Circus》。

#### <u>2. Python的语言特点</u>

1. 简洁易读：Python采用简洁的语法和清晰的代码风格，使得代码易于阅读和理解。它使用缩进来表示代码块，而不是依赖于大括号等符号，这使得代码具有一致性和可读性。
2. 动态类型：Python是一种动态类型语言，它允许变量在运行时绑定到对象，而不需要事先声明类型。这使得Python非常灵活，可以更快地进行原型设计和开发。
3. 强大的标准库：Python拥有丰富的标准库，涵盖了各种常用任务，例如文件操作、网络通信、数据库访问等。这使得开发者可以更快速地编写功能完善的应用程序，而无需从头开始实现所有功能。
4. 跨平台：Python是一种跨平台的语言，它可以在多个操作系统上运行，包括Windows、macOS和Linux等。这使得开发者能够轻松地将其代码移植到不同的平台上。
5. 大量的第三方库和框架：Python拥有庞大而活跃的社区，产生了许多优秀的第三方库和框架，如NumPy、Pandas、Django和Flask等。这些库和框架提供了丰富的功能和工具，使得开发者能够更高效地开发各种应用程序和项目。
6. 具有广泛应用领域：由于Python的易用性和丰富的生态系统，它被广泛应用于各个领域，包括Web开发、数据分析、人工智能、科学计算、自动化脚本和网络爬虫等。

总结起来，Python是一种简洁、易读、灵活且功能强大的编程语言，具有广泛的应用领域和强大的社区支持，适合各种编程任务和项目的开发。



### 二、Python环境及项目上线流程

Python的环境大致分为两类：本地开发环境、线上生产环境；对于本地的开发环境而言大多数人都是使用Windows进行开发的，所以官网也提供了很容易得部署方案，只需要下载点击安装即可。而对于线上生产环境而言，由于开发级别的问题，不同公司会有不同Python 版本的要求，所以针对Python的线上环境来说，版本控制就需要提前做好规划，防止线上因为版本问题导致系统崩溃。

- ***开发环境的部署***：这里就不再赘述了，因为其简单的操作步骤和无脑化的界面展示，相信大多数IT从业者都是可以应付的。下一步！就显得很重要。这里唯一要注意的就是选择哪个包了，[Windows包点此下载](https://www.python.org/downloads/windows)、[MacOS包点此下载](https://www.python.org/downloads/macos)、Ubuntu或其他桌面操作系统的包请使用apt或其系统自带的包管理工具安装即可。 Windows安装时注意勾选 `Add Python VERSION to PATH ` 将环境变量添加至系统中, 随后按提示操作即可;

- ***开发编辑器***：在`python`解释器安装完毕后, 还需要一个开发程序用的 IDE 编辑器, 推荐使用开源的 [VSCode](https://code.visualstudio.com/) 对程序进行开发, 在其中安装好对应的插件即可开始程序的开发工作了; 插件安装 `Python` 插件即可;

上方涉及的两点如果不明的话，可以参考我们录制的视频，查看具体的操作步骤。



下面我们介绍，线上生产环境的安装操作过程：线上环境中 `python环境` 的设置也是尤为重要的, 这里主要介绍单体应用的环境设置, 容器环境只需要采用相应镜像即可; 在线上环境中同样采用 `virtualenv` 来针对不同的项目及异步程序进行环境的设定, 根据项目根目录中提供的 `requirements.txt` 包版本控制文件进行依赖项的安装及同步; 为了更好的对 `python版本` 的控制, 采用源码部署是通用的方式; 

```bash
$ yum -y groupinstall "Development Tools"
$ yum -y install gcc zlib zlib-devel libffi libffi-devel \
                 readline-devel openssl-devel openssl11 openssl11-devel

$ vim /etc/profile.d/openssl11.sh
export CFLAGS=$(pkg-config --cflags openssl11)
export LDFLAGS=$(pkg-config --libs openssl11)
$ source /etc/profile

$ wget https://www.python.org/ftp/python/3.11.1/Python-3.11.1.tar.xz
$ tar xf Python-3.11.1.tar.xz -C /opt/
$ ./configure --prefix=/usr/local/python311 --with-ssl && make -j 4 && make install

$ vim /etc/profile.d/python311.sh
export PATH=$PATH:/usr/local/python311/bin

$ python3
Python 3.11.1 (main, Dec 27 2022, 12:20:43) [GCC 4.8.5 20150623 (Red Hat 4.8.5-44)] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> exit()
```

在线上环境中, 发布 `python` 程序并让其时刻保持服务状态, 通常会选择 `supervisor` 对其进行管理; `supervisor` 是一个可以帮助系统管理员监控和控制一定数量服务进程的守护进程工具. 它可以将服务进程作为子进程管理, 并放置后台运行不占用前台资源. 且具备在服务进程非正常退出后自动拉起的能力; 下面我们来看一下使用supervisor控制Python程序后台运行的过程及准备工作。

```bash
# 设置运行python后端程序的虚拟环境目录及代码存放目录
$ mkdir -p /srv/{envs/apps,apps}

# 将后端程序从gitlab中克隆到apps目录中(这里如果没有代码就自己创建代码)
$ cd /srv/apps
$ mkdir -p /srv/apps/helloworld
$ touch /srv/apps/helloworld/server.py
$ touch /srv/apps/helloworld/requirements.txt
###>>> 上方两个文件在该笔记的附录板块中有存在,请移步附录获取代码

# 构建项目对应的虚拟环境并安装项目依赖
$ virtualenv -p $(which python3) /srv/envs/apps/helloworld
$ cd /srv/apps/helloworld
$ source /srv/envs/apps/helloworld/bin/activate
(helloworld) $ pip install -r requirements.txt
(helloworld) $ deactivate

# 主机系统中安装supervisor并根据项目README文件配置启动文件
$ pip3 install supervisor
$ echo_supervisord_conf >/etc/supervisord.conf
$ supervisord -c /etc/supervisord.conf

$ mkdir /etc/supervisord.d
$ vim /etc/supervisord.d/helloworld.ini
[program:helloworld]
directory=/srv/apps/helloworld
command=/srv/envs/apps/helloworld/bin/python /srv/apps/helloworld/server.py
autostart=true
autorestart=true
startsecs=10
stderr_logfile=helloworld_stderr.log
stdout_logfile=helloworld_stdout.log
stdout_logfile_maxbytes = 20MB
stdout_logfile_backups = 20

# 验证启动
$ supervisorctl reload
$ supervisorctl update
$ supervisorctl status
helloworld    RUNNING   pid 20964, uptime 0:00:34
```

这里如果我们想要实现自动化发布Python程序的话, 最好使用ansible来进行发布, 当然如果想要实现gitlab+Jenkins的CICD的话, 只需要将剧本绑定至Jenkins的job上即可。

<img src="images/content.drawio.png" alt="cicd-structure" style="zoom: 67%;" />

```yaml
---
- hosts: helloworld
  vars:
    proj_name: "helloworld"
    proj_url: "https://xxx.gitlab.com/backend/helloworld.git" # 这里写成你们公司的项目地址,一般情况下使用ssh方式
    proj_path: "/srv/apps/helloworld"
    virtualenv_path: "/srv/envs/apps/helloword"
    requirement_file_name: "requirements.txt"
  tasks:
  - name: update {{ proj_name }} project source codes.
    git: repo={{ proj_url }} dest={{ proj_path }}
  - name: installtaion requirements site-packages.
    pip: requirements={{ proj_path }}/{{ requirement_file_name }} virtualenv={{ virtualenv_path }}
  - name: restart {{ proj_name }} project.
    supervisorctl: name={{ proj_name }} state=restarted
```

```ini
[helloworld]
192.168.108.11
192.168.108.12
[helloworld:vars]
ansible_python_interpreter=/usr/local/python311/bin/python3.11
```

至此大家就了解了Python的两类环境部署了。动动小手，实践一下吧。最好是将gitlab、Jenkins一并部署出来，实现CICD的发布流程。



### 三、Python的变量及基本数据类型

在任何编程语言中，都存在一个概念，那就是***<u>变量</u>***。在计算机编程中，变量的本质可以理解为在计算机内存中的一块存储空间。当我们定义一个变量时，计算机会为其分配一部分内存来存储值。

变量在内存中有以下几个方面的特征：

1. **内存地址**：每个变量都有一个唯一的内存地址，用于标识变量在内存中的位置。通过这个地址，计算机可以找到存储在变量中的值。
2. **数据类型**：变量具有数据类型，它指定了变量可以存储的数据的种类和范围。例如，整数类型变量可以存储整数值，字符串类型变量可以存储文本等。数据类型决定了变量占用的内存空间大小和存储方式。
3. **值**：变量存储的实际数据被称为值。这可以是数字、文本、布尔值等任何可表示的数据。我们可以通过给变量赋值来设置其值，并随时更改。

当我们使用变量时，计算机会<u>*根据变量名来查找相应的内存地址*</u>，并读取或修改存储在该地址中的值。这使得我们可以在程序中引用变量，并对其进行操作。

需要注意的是，变量本身不是数据，而是存储和访问数据的容器。通过变量，我们可以轻松地处理和操作数据，而无需直接操作内存地址和值。这种抽象使得编程更加灵活和易于理解。

```python
#> 选择你喜欢的编辑器,将代码存储在/path/to/dir/variable.py中
#> 接下来定义变量varA, 并将helloworld作为值, 赋给varA
#> 使用print函数将变量varA所代表的值打印出来
#> 代码如下:


#!/usr/bin/env python311
#
# author: <@xxxxxx>
# email: <@xxxxxx@company_email_inc.com>
# date: <@create_time>
# business: <@business_name>


varA = "helloworld"
print("this word is: ", varA)
```

在Python中不只有上方一种定义变量的方法，还可以使用***链式赋值*** 和 ***隔离赋值*** 两种方式。这两种赋值各有各的优点也各有各的缺点。下面就来一起看一下这两种方式

```python
#!/usr/bin/env python311
#
# author: <@xxxxxx>
# email: <@xxxxxx@company_email_inc.com>
# date: <@create_time>
# business: <@business_name>


varA = varB = varC = "123"       #>> 链式赋值方式;缺点就是都指向同一块内存空间,牵一发而动全身
print(
  "varA is: ", varA,
  "varB is: ", varB,
  "varC is: ", varC
)

varA, varB, varC = "123", "456", "123"        #>> 隔离赋值方式;缺点就是写的多!别问为啥程序员都懒
print(
  "varA is: ", varA,
  "varB is: ", varB,
  "varC is: ", varC
)
```



在了解了上方的变量赋值方式后，我们接下来看一下Python中的数据类型，现阶段对于所有编程语言中均存在这四种数据类型：整型、浮点型、字符串类型、布尔值类型。当然在Python中还含有复数类型complex，就是高中时所学的虚数，这里它和运维没什么关系，就不多介绍了，焦点主要集中在前四种数据类型即可。

1. **整型和浮点型**
   对于整型和浮点型来说，使我们最熟悉的类型了。主要用于计数和科学运算使用，当然在业务上也是有很多应用场景的，对于运维工程师来说经常也作为分析日志、监控指标采集时使用到。下面一起来看下整型和浮点型的定义和应用。

   ```python
   #!/usr/bin/env python311
   #
   # author: <@xxxxxx>
   # email: <@xxxxxx@company_email_inc.com>
   # date: <@create_time>
   # business: <@business_name>
   
   
   # 定义intA、intB为两个整型变量
   intA, intB = 123, 456
   # 使用type函数，将intA、intB的类型进行输出
   print(
     "intA's type: ", type(intA), 
     "intB's type: ", type(intB)
   )
   
   # 定义floatA、floatB为两个整型变量
   floatA, floatB = 1.23, 4.56
   # 使用type函数，将intA、intB的类型进行输出
   print(
     "floatA's type: ", type(floatA), 
     "floatB's type: ", type(floatB)
   )
   
   # 这里我们解释下：在Python中并不需要像c或者go一样在声明变量的时候，要指定变量的类型。因为Python属于弱类型语言，根据所设置的值就可以自动的判断出类型了。
   ```

   对于整型和浮点型而言，在Python中一定会涉及到的问题就是如何进行运算，很激动的告诉大家，只要存在于这个世界上的公式，那么Python就一定能够实现它的算法，只不过不同的人写出来的代码不同罢了。接下来我们就看一下如何使用整型及浮点型数据进行运算的。再有现实世界中对于计数的所有规则在Python中也是适用的，比如“**<u>先乘除后加减，小括号里最优先</u>**”

   ```python
   #!/usr/bin/env python311
   #
   # author: <@xxxxxx>
   # email: <@xxxxxx@company_email_inc.com>
   # date: <@create_time>
   # business: <@business_name>
   
   
   intA, intB = 45, 13
   # 求和
   print(intA + intB)
   
   # 相减
   print(intA - intB)
   
   # 相乘
   print(intA * intB)
   
   # 相除
   print(intA / intB)
   
   # 取余
   print(intA % intB)
   
   # 取整
   print(intA // intB)
   ```

   注意上方的除法运算，最终得到的值，是个浮点数，类型得到了转换。

   

2. **字符串类型**

   字符串类型充斥着我们生活中，几乎无时无刻你不在用它。对于字符串类型的操作方法也是很多的。这里我们直接上代码，进行演示

   ```python
   #!/usr/bin/env python311
   #
   # author: <@xxxxxx>
   # email: <@xxxxxx@company_email_inc.com>
   # date: <@create_time>
   # business: <@business_name>
   
   
   # 字符串的定义声明
   strA = 'hello\n world'
   strB = "hello\n world"
   strC = '''hello
    world'''
   strD = """hello
    world"""
   # 三引号可以包含换行，而在单引号和双引号中需要使用\n来表示换行
   
   
   # 字符串的索引
   strA = "python is devops best language"
   
   # 索引编号规则
   # p   y   t   h   o   n       is  devops best     l   a   n   g   u   a   g   e
   # 0   1   2   3   4   5   6   7..............  21 22  23  24  25  26  27  28  len(strA)-1
   # -len(strA) ................................................................ -1
   
   #>> 对于字符串中的字符而言,每个字符都有整数的索引(有正有负),使用下角标的方式对其中的字符进行调用,以及使用区间的方式进行切片
   
   #>>> 单独调用其中的某个字符
   print(strA[4])  # Out: o
   print(strA[28]) # Out: g
   
   #>>> 使用区间调用其中的某个单词,注意左闭右开区间
   print(strA[0:6])   # Out: python
   print(strA[17:21]) # Out: best
   
   #>>> 对于区间而言还可以设置步长和None
   print(strA[22:])   # Out: language
   print(strA[:6])    # Out: python
   print(strA[22::2]) # Out: lnug
   print(strA[::-1])  # Out: egaugnal tseb spoved si nohtyp
   ```

   字符串这个类型，还含有其本身所带有的方法，可以帮助研发人员快速的处理字符串中的数据。从而得到想要的字符串形式。以满足业务和逻辑需求。

   ```python
   #!/usr/bin/env python311
   #
   # author: <@xxxxxx>
   # email: <@xxxxxx@company_email_inc.com>
   # date: <@create_time>
   # business: <@business_name>
   
   
   strA = "python is devops best language"
   
   #>> 将strA中的字符全部变成大写、小写、词头转换
   print(strA.upper())  # Out: PYTHON IS DEVOPS BEST LANGUAGE
   print(strA.lower())  # Out: python is devops best language
   print(strA.title())  # Out: Python Is Devops Best Language
   
   #>> 将strA按照特点字符进行切割
   print(strA.split())  # Out: ['python', 'is', 'devops', 'best', 'language']
   
   #>> 将strA中的字符进行替换
   print(strA.replace('python', 'PYTHON')) # Out: PYTHON is devops best language
   
   #>> 将strA中的结尾的language删掉
   print(strA.strip('language')) # Out: "python is devops best "
   
   #>> 将^穿插在每个字符中间
   print('^'.join(strA))         # Out: p^y^t^h^o^n^ ^i^s^ ^d^e^v^o^p^s^ ^b^e^s^t^ ^l^a^n^g^u^a^g^e
   
   #>> 将strA以变量的形式插入到某个字符串中
   print("this segment is: {segment}".format(segment=strA))
   ```

   

3. **布尔值类型**

   对于整型、浮点型、字符串类型在Python中都是显示调用的，而布尔值类型都是隐式调用的. 因为在程序中不会输出True 或者 False之后再去执行什么流程，而是在if条件判断中就已经知道对错了，按照if的流程走就可以了，所以并不会有显示的调用。

   ```python
   #!/usr/bin/env python311
   #
   # author: <@xxxxxx>
   # email: <@xxxxxx@company_email_inc.com>
   # date: <@create_time>
   # business: <@business_name>
   
   
   #>> 数据比较判断符
   print(1 < 2)  # Out: True
   print(1 > 2)  # Out: False
   print(1 == 2) # Out: False
   print(1 <= 2) # Out: True
   print(1 >= 2) # Out: False
   print(1 != 2) # Out: True
   
   #>> 逻辑判断符
   print(True and False)  # Out: False
   print(True or False)   # Out: True
   print(not False)       # Out: True
   
   #>> 数据逻辑判断符
   number = 12
   print(number is 12)            # Out: True
   print(number in [11, 12, 13])  # Out: True
   ```



### 四、Python的判断循环及数据结构类型

在python中需要运维掌握的数据结构包含 元组tuple、列表list、字典dict、集合set, 这些数据结构可以将零散的数据有效、有规律的组合到一起, 方便进行传输、检索、存储等动作; 元组常被用来存放固定的数据、列表常被用来存储动态的数据、字典常被用来存放不同类别的数据、集合常被用来作为祛重的天然工具; 使用自带的数据结构可以更好的对互联网中传输的数据进行管理和传输;



#### Python中的判断和循环

```python
#!/usr/bin/env python311
#
# author: <@xxxxxx>
# email: <@xxxxxx@company_email_inc.com>
# date: <@create_time>
# business: <@business_name>


## if 条件判断
In [1]: variable = 123
In [2]: if variable > 10:
   ...:     print("variable: {variable} is more then 10".format(variable=variable))
   ...:
variable: 123 is more then 10

# --------------------------------------------我是分割线------------------------------------------------#

## for 循环语句
In [3]: listA = ["hello world", False, 1, 2, 3, 3.14]
In [4]: for data in listA:
   ...:     print(data)
   ...:
hello world
False
1
2
3
3.14

# --------------------------------------------我是分割线------------------------------------------------#

## while 循环语句
In [5]: n = 0
In [6]: while n < 5:
   ...:     print(n)
   ...:     n += 1
   ...:
0
1
2
3
4
```



#### 数据结构类型

在Python中除了简单的数据类型, 还会涉及到组织这些基本数据类型的数据结构, 在Python中数据结构分为四种：元组、列表、字典、集合四种类型。它们各有各的用途，相互之间也可配合完成任务。在互联网的传输过程中也常用字典和列表来进行数据的组织, 常常被称作json数据。元组和集合通常都是配合中间状态的数据进行使用的。

**元组的定义声明及操作方法**

```python
#!/usr/bin/env python311
#
# author: <@xxxxxx>
# email: <@xxxxxx@company_email_inc.com>
# date: <@create_time>
# business: <@business_name>


#> 定义元组
tupleA = (1, 2, 3, "hello world", "123", True, 3.14)

# --------------------------------------------我是分割线------------------------------------------------#

#> 对元组中的数据进行遍历
for element in tupleA:
  print(element)

##>> Out:
1
2
3
hello world
123
True
3.14

# --------------------------------------------我是分割线------------------------------------------------#

#> 使用索引和切片控制元组的元素
tupleA[2:5]  # Out: (3, 'hello world', '123')
tupleA[::-1] # Out: (3.14, True, '123', 'hello world', 3, 2, 1)
tupleA[:5]   # Out: (1, 2, 3, 'hello world', '123')
tupleA[2:]   # Out: (3, 'hello world', '123', True, 3.14)

# --------------------------------------------我是分割线------------------------------------------------#

#> 使用for循环对元组的索引进行操作
for index in range(len(tupleA)):
  print("{index} is: {tupleA_element}".format(index=index, tupleA_element=tupleA[index]))

##>> Out:
0 is: 1
1 is: 2
2 is: 3
3 is: hello world
4 is: 123
5 is: True
6 is: 3.14

# --------------------------------------------我是分割线------------------------------------------------#

#> 元组的解包
tupleB = ("abc", 123)
varA, varB = tupleB
print("varA is: {varA}".format(varA=varA))
print("varB is: {varB}".format(varB=varB))

##>> Out: 
varA is: abc
varB is: 123

# --------------------------------------------我是分割线------------------------------------------------#

#> 元组的自建方法
tupleA.count(1)  # Out: 2 数量
tupleA.index(3)  # Out: 2 索引号
```

**集合的定义声明和操作方法**

```python
#!/usr/bin/env python311
#
# author: <@xxxxxx>
# email: <@xxxxxx@company_email_inc.com>
# date: <@create_time>
# business: <@business_name>


#> 定义集合
setA = {1, 1, 2, 2, 3, 3}
print(setA)  # Out: {1, 2, 3} # 天然的去重工具

# --------------------------------------------我是分割线------------------------------------------------#

#> 使用for循环遍历集合
for element in setA:
  print(element)

##>> Out:
1
2
3

# --------------------------------------------我是分割线------------------------------------------------#

#> 两个集合的交并集
setA = {1, 2, 3, 4, 5}
setB = {4, 5, 6, 7, 8}

#>> 取并集
setA | setB  # Out: {1, 2, 3, 4, 5, 6, 7, 8}

#>> 取交集
setA & setB  # Out: {4, 5}

# --------------------------------------------我是分割线------------------------------------------------#

#> 集合中的增删改查操作方法

setA = {1, 2, 3, 4, 5}
setA.add(6)              # Out: {1, 2, 3, 4, 5, 6}
setA.update({7, 8, 9})   # Out: {1, 2, 3, 4, 5, 6, 7, 8, 9}
setA.pop()               # Out: {2, 3, 4, 5, 6, 7, 8, 9}
setA.remove(2)           # Out: {3, 4, 5, 6, 7, 8, 9}
setA.pop()               # Out: {4, 5, 6, 7, 8, 9}
setA.clear()             # Out: {}

# --------------------------------------------我是分割线------------------------------------------------#

#> 在判断条件中的应用
if 3.14 not in {1, 2, 3, 4}:
  print("3.14 is not exist of set.")
```

**列表的定义声明和操作方法**

```python
#!/usr/bin/env python311
#
# author: <@xxxxxx>
# email: <@xxxxxx@company_email_inc.com>
# date: <@create_time>
# business: <@business_name>


#> 定义列表
listA = [1, 2, 3, "hello world", False, 3.14]

# --------------------------------------------我是分割线------------------------------------------------#

#> 使用for循环遍历列表
for element in listA:
  print(element)

##>> Out:
1
2
3
hello world
False
3.14

# --------------------------------------------我是分割线------------------------------------------------#

#> 列表的索引及切片
listA[::-1]    # Out: [3.14, False, 'hello world', 3, 2, 1]
listA[:4]      # Out: [1, 2, 3, 'hello world']
listA[3:]      # Out: ['hello world', False, 3.14]
listA[2:4]     # Out: [3, 'hello world']

#> 修改列表中某个元素的值
listA[2] = "san"
print(listA)   # Out: [1, 2, "san", "hello world", False, 3.14]

# --------------------------------------------我是分割线------------------------------------------------#

#> 列表的有序性
listB = [23, 14, 56, 91, 12, 34, 11, 15]
for i in range(len(listB)):
  for j in range(len(listB)-1):
    if listB[j] > listB[j+1]:
      listB[j], listB[j+1] = listB[j+1], listB[j]
print(listB)

# --------------------------------------------我是分割线------------------------------------------------#

#> list列表的操作方法
listC = [1, 2, 3]

##>> 增加元素
listC.append("san")              # Out: [1, 2, 3, 'san']
listC.insert(2, "insert")        # Out: [1, 2, 'insert', 3, 'san']
listC.extend([4, 5, 6])          # Out: [1, 2, 'insert', 3, 'san', 4, 5, 6]
listC.extend(("七", "八", "九"))  # Out: [1, 2, 'insert', 3, 'san', 4, 5, 6, '七', '八', '九']

##>> 删除元素
listC.pop()            # Out: [1, 2, 'insert', 3, 'san', 4, 5, 6, '七', '八']
listC.remove('san')    # Out: [1, 2, 'insert', 3, 4, 5, 6, '七', '八']

##>> 修改元素
listC[1] = "二"        # Out: [1, '二', 'insert', 3, 4, 5, 6, '七', '八']
listB = [23, 14, 56, 91, 12, 34, 11, 15]
listB.sort()              # Out: [11, 12, 14, 15, 23, 34, 56, 91]
listB.sort(reverse=True)  # Out: [91, 56, 34, 23, 15, 14, 12, 11]

##>> 查询元素
listC.index('七')      # Out: 8 # 索引号
listC.count(1)        # Out: 1 # 数量
```

**字典的定义声明和操作方法**

```python
#!/usr/bin/env python311
#
# author: <@xxxxxx>
# email: <@xxxxxx@company_email_inc.com>
# date: <@create_time>
# business: <@business_name>


#> 字典定义
dictA = {'key1': 'value', 'key2': 'value'}
dictB = dict([('key1', 'value'), ('key2', 'value')])

# --------------------------------------------我是分割线------------------------------------------------#

for key in dictA.keys():
    print(key)
key1
key2

# --------------------------------------------我是分割线------------------------------------------------#

for key in dictA.keys():
    print(key, dictA[key])
key1 value
key2 value

# --------------------------------------------我是分割线------------------------------------------------#

for item in dictA.items():
    print(item)
('key1', 'value')
('key2', 'value')

# --------------------------------------------我是分割线------------------------------------------------#

dir(dictA)
[......
 'clear',
 'copy',
 'fromkeys',
 'get',
 'items',
 'keys',
 'pop',
 'popitem',
 'setdefault',
 'update',
 'values']

dictA = {'key1': 'value', 'key2': 'value'}

##>> 增加键值
dictA.update({'key3': 'value'})         # Out: {'key1': 'value', 'key2': 'value', 'key3': 'value'}
dictA.setdefault('key4', 'keyvalue111') # Out: {'key1': 'value', ......, 'key4': 'keyvalue111'}
{}.fromkeys(['k1', 'k2', 'k3', 'k4'])   # Out: {'k1': None, 'k2': None, 'k3': None, 'k4': None}

##>> 删除键值
dictA.pop('key1')           # Out: {'key2': 'value', 'key3': 'value', 'key4': 'keyvalue111'}
dictA.popitem()
dictA.popitem()             # Out: {'key2': 'value'}

##>> 键值改动
dictA['key2'] = 'value222'             # Out: {'key2': 'value222'}
dictA.update({'key2': 'value123'})     # Out: {'key2': 'value123'}

##>> dict键值对查询
dictA.items()      # Out: dict_items([('key2', 'value123')])
dictA.keys()       # Out: dict_keys(['key2'])
dictA.values()     # Out: dict_values(['value123'])
dictA.get('key2')  # Out: 'value123'
```



### 五、Python函数

自定义函数可以将重复使用的代码功能片段封装成一段单独可重复执行的功能, 这样可以避免重复代码的出现, 减小了代码冗余程度从而提升了编程效率, 自定义函数也是日常开发过程中最为常用的编程手段之一; 自定义函数的学习包含参数定义、返回值定义、函数体定义以及逻辑设定等方法;



### 六、Python库

模块并不是一个新奇的概念, 模块其实就是多个函数或类集合成的一个文件, 也可以称之为叫做包; 包里面封装了不同类型、不同功能的类或函数, 从而让程序员在其`.py` 文件中对其进行调用, 从而实现跨文件调用功能实例的方法, 也同样可以在整个项目中实现降低冗余; 

```bash
## os, sys
(pystudy) ➜  ~ export TESTENV='123'
(pystudy) ➜  ~ ipython
In [1]: import os
In [2]: os.getenv('TESTENV')
Out[2]: '123'
In [3]: os.mkdir(path='/Users/chaoliu/Documents/testdir', mode=755)
In [4]: os.rmdir('/Users/chaoliu/Documents/testdir')

In [5]: dir(os)
......

## subprocess
In [6]: import subprocess

In [7]: help(subprocess.Popen)

In [8]: execute = subprocess.Popen(
    ...:     args="ls -l ~/Documents",
    ...:     shell=True,
    ...:     stdout=subprocess.PIPE,
    ...:     stderr=subprocess.STDOUT,
    ...:     encoding='utf-8'
    ...: )

In [9]: if execute.poll() == 0:
    ...:     result = execute.communicate()[0]
    ...:     print(result)
    ...:
total 0
drwxr-xr-x@ 6 chaoliu  staff  192  1  5 15:38 Codes
drwxr-xr-x@ 2 chaoliu  staff   64 12  7 11:29 Docs
drwxr-xr-x@ 3 chaoliu  staff   96  1 10 11:08 Projects
drwxr-xr-x@ 4 chaoliu  staff  128 12  9 15:28 SnapShots
drwxr-xr-x@ 4 chaoliu  staff  128  1  7 19:13 Virtualenvs

## re
In [10]: import re

In [11]: regx = re.compile("\d+")
In [12]: content = "my age is 24"
In [13]: regx.findall(content)
Out[13]: ['24']

In [14]: contentB = "my age is 456"
In [15]: regx.findall(contentB)
Out[15]: ['456']

## json(互联网中如何传输数据【字符串】)
In [1]: data = {
   ...:   'os_system': 'centos',
   ...:   'version': '7.9.2009',
   ...:   'shell': 'Bash',
   ...:   'ipaddress': '192.168.108.11',
   ...:   'hostname': 'cloudclass'
   ...: }

In [2]: type(data)
Out[2]: dict

In [3]: import json
In [4]: result = json.dumps(data)
In [5]: result
Out[5]: '{"os_system": "centos", "version": "7.9.2009", "shell": "Bash", "ipaddress": "192.168.108.11", "hostname": "cloudclass"}'
In [6]: type(result)
Out[6]: str

In [7]: result_to_dict = json.loads(result)
In [8]: result_to_dict
Out[8]:
{'os_system': 'centos',
 'version': '7.9.2009',
 'shell': 'Bash',
 'ipaddress': '192.168.108.11',
 'hostname': 'cloudclass'}
In [9]: type(result_to_dict)
Out[9]: dict
```

```python
## requests
>>> import requests
>>> r = requests.get('https://httpbin.org/basic-auth/user/pass', auth=('user', 'pass'))
>>> r.status_code
200
>>> r.headers['content-type']
'application/json; charset=utf8'
>>> r.encoding
'utf-8'
>>> r.text
'{"authenticated": true, ...'
>>> r.json()
{'authenticated': True, ...}

## pymysql
CREATE TABLE `users` (
    `id` int(11) NOT NULL AUTO_INCREMENT,
    `email` varchar(255) COLLATE utf8_bin NOT NULL,
    `password` varchar(255) COLLATE utf8_bin NOT NULL,
    PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin
AUTO_INCREMENT=1 ;

import pymysql.cursors

# Connect to the database
connection = pymysql.connect(host='localhost',
                             user='user',
                             password='passwd',
                             database='db',
                             cursorclass=pymysql.cursors.DictCursor)

with connection:
    with connection.cursor() as cursor:
        # Create a new record
        sql = "INSERT INTO `users` (`email`, `password`) VALUES (%s, %s)"
        cursor.execute(sql, ('webmaster@python.org', 'very-secret'))

    # connection is not autocommit by default. So you must commit to save
    # your changes.
    connection.commit()

    with connection.cursor() as cursor:
        # Read a single record
        sql = "SELECT `id`, `password` FROM `users` WHERE `email`=%s"
        cursor.execute(sql, ('webmaster@python.org',))
        result = cursor.fetchone()
        print(result)

## paramiko
import paramiko

client = paramiko.SSHClient()
privkey = paramiko.RSAKey.from_private_key_file('~/.ssh/id_rsa')
client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
client.connect(hostname='192.168.108.11', port=22, username='root', pkey=privkey)
stdin, stdout, stderr = client.exec_command(f"ifconfig")
if stdout is None:
    print(stderr.read().decode('utf-8'))
print(stdout.read().decode('utf-8'))
client.close()
```

```python
$ pip install wheel twine
$ tree .
├── addn
│   ├── __init__.py
│   └── add_number.py
└── setup.py
1 directory, 3 files

$ vim setup.py
from distutils.core import setup
packages = ['addn']
setup(
    name='addn',                            # pip list 时显示的包名
    version='0.0.1',                        # 版本号
    author='mirschao',                      # 作者名
    packages=packages,                      # import 时写的名字
)

$ vim addn/__init__.py
from . import add_number

$ vim addn/add_number.py
def add(n=0, m=0):
    return n + m

$ python setup.py build
$ python setup.py sdist
$ python setup.py bdist_wheel
$ tree .
.
├── addn
│   ├── __init__.py
│   └── add_number.py
├── addn.egg-info
│   ├── PKG-INFO
│   ├── SOURCES.txt
│   ├── dependency_links.txt
│   └── top_level.txt
├── build
│   ├── bdist.macosx-10.9-x86_64
│   └── lib
│       └── addn
│           ├── __init__.py
│           └── add_number.py
├── dist
│   ├── addn-0.0.1-py3-none-any.whl
│   └── addn-0.0.1.tar.gz
└── setup.py
7 directories, 11 files

$ twine upload dist/*
Uploading distributions to https://upload.pypi.org/legacy/
Enter your username: mirschao
Enter your password:
Uploading addn-0.0.1-py3-none-any.whl
100% ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 3.8/3.8 kB • 00:00 • ?
Uploading addn-0.0.1.tar.gz
100% ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 3.5/3.5 kB • 00:00 • ?
View at:
https://pypi.org/project/addn/0.0.1/
```





### 附录

```python
#> /srv/apps/helloworld/server.py
from sanic import Sanic
from sanic.response import text

app = Sanic("MyHelloWorldApp")

@app.get("/")
async def hello_world(request):
    return text("Hello, kitty")

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8080, access_log=False)
```

```ini
#> /srv/apps/helloworld/requirements.txt
aiofiles==23.1.0
html5tagger==1.3.0
httptools==0.5.0
multidict==6.0.4
sanic==23.3.0
sanic-routing==22.8.0
tracerite==1.1.0
ujson==5.7.0
uvloop==0.17.0
websockets==11.0.2
```

