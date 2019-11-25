# 帮助命令

--help

man ls

### 常用命令

ls 

* -a	显示所有加隐藏
* -l     显示详细信息
* -r   反向排序
* -d    显示大小
* -I    忽略

cd

* ~
* /
* ./
* ../

pwd  查看当前路径

who  查看谁登录了本机

df

* -h   查看磁盘信息
* -m   

free 

* -h  查看内存

top   进程

ps -ef

du	-h test

mkdir  创建文件夹

touch 

cp 

- -r  复制递归文件
- -f  强制复制

mv

cat hl | wc(wordcount)

cat hl | grap 'yan' 过滤

rm -rf

echo "hello" > hl

echo "hello" >> hl

find ./hadoop*

more

less

head -2 ssh_config

tail 

tail -f

env  查看环境变量

export HL=Yani

vim /etc/profile



ctrl + w  清除一个单词

ctrl + u  清除光标前的内容

ctrl + k  清除光标后的内容

ctrl + a

ctrl + e

ctrl + b

ctrl + f



history



ctrl + r

vim模式

* 一般模式
* 编辑模式
  * i   编辑 
  * I
  * o
  * O
  * u
  * set number
  * set nonumber
  * ：3 定位到某一行
* 命令模式
  * /  搜索
  * n  查找下一个
  * N  上一个
  * nyy  复制
  * p  沾贴
  * ndd
  * dG  光标到最后
  * dj
  * dk
  * dw  删除一个单词
  * gg  到文首
  * G  到文末
  * %s/ 选择替换的内容/替换的内容 /g
  * x
  * r  改变一个字符
* 可视模式
  * Ctrl + v
  * I  插入
  * d 删除
  * x
  * r

e

ssh-keygen

ssh-copy-id

公钥只能给私钥解密

私钥只能给公钥解密

scp -r root@192.168.1.127:/root/*.tar.gz ./

scp -r ./*.tar.gz root@192.168.1.127:/root/ 



file  查看文件信息

tar - zcvf 压缩 

tar -tvf  查看 

tar -zxvf   解压



vim /etc/hosts

vim /etc/hostname  需要重启

hostnamectl set-hostname zheng



passwd  root 



shut down 关机

init 0

init 6



date 

cal



