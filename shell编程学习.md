---
typora-root-url:/shell编程学习.assets
---

# Shell脚本学习

 方便自动化

**shell脚本就是将完成一个任务的所有的命令按照执行的先后顺序，自上而下写入到一个文本文件中，然后给予执行权限**

### 一、如何抒写一个shell脚本

shell脚本的**命名**：

名字要有意义，最好不要用a、b、c、d、1、2、3、4这种方式命名；虽然linux系统中，文件没有扩展名的概念，依然建议你用.sh结尾；名字不要太长，最好能在30个字节以内解决。例如：check_memory.sh

shell**脚本格式**：

shell脚本开头**必须指定脚本运行环境** 以 **#！**这个==特殊符号==组合来组成。

如： #!/bin/bash 指定该脚本是运行解析由/bin/bash来完成的；

shell中的注释使用 # 号

```sh
  shell脚本中，最好加入脚本说明字段 
  # which bash
  #!/bin/bash        
  #Author: Bai Shuming        
  #Created Time: 2018/08/27 12:27        
  #Script Description: first shell study script
```

### 二、如何运行一个shell脚本

```sh
脚本运行需要执行权限，当我们给一个文件赋予执行权限后，该脚本就可以运行。   
#chmod u+x filename    
如果不希望赋予脚本执行权限，解释器直接运行那么可以使用bash命令来运行未给予执行权限的脚本         #bash filename
```

### 三、shell中的特殊符号

```sh
   有基础的同学不要和正则表达式中的符号含义搞混淆了。
   ~:                家目录    # cd ~ 代表进入用户home目录
   !:                执行历史命令   !! 执行上一条命令
   $:                变量中取内容符
   + - * / %:        对应数学运算  加 减 乘 除 取余数
   &:                后台执行
   *:                星号是shell中的通配符  匹配所有
   ?:                问号是shell中的通配符  匹配除回车以外的一个字符
   ;：               分号可以在shell中一行执行多个命令，命令之间用分号分割
   |：               管道符 上一个命令的输出作为下一个命令的输入cat filename | grep "abc"        
   \:                转义字符
   ``:               反引号 命令中执行命令    echo "today is `date +%F`"    
   ' ':              单引号，脚本中字符串要用单引号引起来，但是不同于双引号的是，单引号不解释变量      
   " ":              双引号，脚本中出现的字符串可以用双引号引起来
```

### 四、shell中的管道运用

```
    |  管道符在shell中使用是最多的，很多组合命令都需要通过组合命令来完成输出。管道符其实就是下一个命令对上一个命令的输出做处理。
```

### 五、shell重定向

```sh
>   重定向输入  覆盖原数据
>>  重定向追加输入，在原数据的末尾添加
<   重定向输出     wc -l < /etc/passwd
<<  重定向追加输出  fdisk /dev/sdb <
```

### 六、shell数学运算

```sh
expr 命令：只能做整数运算，格式比较古板，注意空格
         [root@baism ~]# expr 1 + 1
         2
         [root@baism ~]# expr 5 - 2
         3
         [root@baism ~]# expr 5 \* 2  #注意*出现应该转义，否则认为是通配符
         10
         [root@baism ~]# expr 5 / 2
         2
         [root@baism ~]# expr 5 % 2
         1
     使用bc计算器处理浮点运算,scale=2代表小数点保留两位
         [root@baism ~]# echo "scale=2;3+100"|bc
         103
         [root@baism ~]# echo "scale=2;100-3"|bc
         97
         [root@baism ~]# echo "scale=2;100/3"|bc
         33.33
         [root@baism ~]# echo "scale=2;100*3"|bc
         300
     双小圆括号运算，在shell中(( ))也可以用来做数学运算
         [root@baism ~]# echo $(( 100+3))
         103
         [root@baism ~]# echo $(( 100-3)) 
         97
         [root@baism ~]# echo $(( 100%3))
         1
         [root@baism ~]# echo $(( 100*3))
         300
         [root@baism ~]# echo $(( 100/3))
         33
         [root@baism ~]# echo $(( 100**3))     #开方运算
         1000000
```

