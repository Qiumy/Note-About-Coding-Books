# 第10章 构建基本脚本

## 重定向输入和输出

### 输出重定向

- 采用大于号（>）来完成将命令的输出发到一个文件中。如果输出文件已经存在，则会用新的文件数据覆盖已经存在的文件。

```shell
$ command > outputfile
```

- 使用双大于号（>>）可以用来追加数据。

```shell
command >> outputfile
```

### 输入重定向

- 使用小于号（<）将文件的内容重定向到命令。

```shell
$ command < inputfile
```

`wc`命令提供了对数据中文本的计数。默认情况下，会输出3个值：文本的行数、文本的词数、文本的字节数。

## 管道

发送某个命令的输出作为另一个命令的输入。

```shell
$ command1 | command2
```

## 执行数学运算

### expr

```shell
$ expr 1 + 5
$ expr 5 \* 2
```

在shell脚本中使用expr命令需要使用反引号`。

### 使用方括号

```shell
$ var1 = $[1+5]
$ var2 = $[$var1 * 2]
```

bash shell数学运算符只支持整数运算。

### 浮点解决方案

使用内建的bash计算器，通过`bc`命令访问bash计算器。bash计算器可以识别数字、变量、注释、表达式、编程语言、函数。

浮点运算由一个内建的scale的变量控制的。设定小数点后面的位数，默认值为0。

```shell
$ bc
scale = 4
```

- 在脚本中使用bc：可以使用反引号来运行bc命令并输出赋给一个变量，基本格式如下：

```shell
variable=`echo " scale=4: 3.44 / 5" | bc`
```

## 退出脚本

### 查看退出状态码

使用$?专属变量来保存上个执行的命令的退出状态码。按照惯例，一个成功结束的命令的退出状态码是0。

| 状态码   | 描述            |
| ----- | ------------- |
| 0     | 命令成功结束        |
| 1     | 通用未知错误        |
| 2     | 误用shell命令     |
| 126   | 命令不可执行        |
| 127   | 没找到命令         |
| 128   | 无效退出参数        |
| 128+x | Linux信号x的严重错误 |
| 130   | 命令通过ctrl+c终止  |
| 255   | 退出状态码越界       |

# 第11章 使用结构化命令

## 使用if-then语句

bash shell的if语句会运行if行定义的那个命令。如果该命令的退出状态码是0（该命令成功运行），位于then部分的命令就会执行。否则then部分的命令不会执行。

```shell
if command
then
	commands
fi
```

## if-then-else语句

```shell
if command
then
	commands
else
	commands
fi
```

## 嵌套if

```shell
if command1
then
	command set 1
elif command2
then
	command set 2
elif command3
then
	command set 3
fi
```

## test命令

如果test命令中列出的田间成立，test命令就会退出并返回退出状态码0；否则会退出并返回退出码1。

```shell
test condition
```

- 在if-then语句中的使用

```shell
if test condition
then
	commands
fi

if [ condition ]
then
	commands
fi
```



test命令可以判断三类条件：数值比较、字符串比较、文件比较。

### 数值比较

```shell
n1 -eq n2
n1 -ge n2
n1 -gt n2
n1 -le n2
n1 -lt n2
n1 -ne n2
```

### 字符串比较

```shell
str1 = str2
str1 != str2
str1 < str2
str1 > str2
-n str1   #检查str1的长度是否非0
-z str1   #检查str1的长度是否为0
```

- 使用大于小于符号必须转义，否则shell会把它们当做重定向符号而把字符串的值当做文件名

### 文件比较

```shell
-d file    #检查file是否存在并是一个目录
-e file    #检查file是否存在
-f file    #检查file是否存在并是一个文件
-r file    #检查file是否存在并可读
-s file    #检查是否存在并非空
-w file    #检查file是否存在并可写
-x file    #检查file是否存在并可执行
-O file    #检查file是否存在并属当前用户所有
-G file    #检查file是否存在并且默认组与当前用户相同
file1 -nt file2    #检查file1是否比file2新
file1 -ot file2    #检查file1是否比file2旧
```

## 复合条件测试

```shell
[ condition1 ] && [ condition2 ]
[ condition1 ] && [ condition2 ]
```

## if-then的高级特性

### 使用双圆括号

```shell
(( expression ))
```

术语expression可以是任意的数学复制或者比较表达式。

### 使用双方括号

```shell
[[ expression ]]
```

双方括号命令提供了针对字符串比较的高级特性。可以使用模式匹配。

```shell
if [[ $user == r* ]]
then
	echo "hello $user"
else
	echo "Sorry. I do not know you!"
fi
```

## case命令

```shell
case variable in
pattern1 | pattern2)
	commands1;;
pattern3)
	commands2;;
*)
	default commands;;
esac
```

# 第12章 更多的结构化命令

## for命令

```shell
for var in list
do
	commands
done
```

### 读取列表中复杂的值

- 使用转义字符（反斜线）来将单引号转义
- 使用双引号来定义用到单引号的值

### 从变量中读取列表

```shell
list="test1 test2 test3"
for word in $list:
do 
	echo "hello $word?"
done
```

### 从命令中读取值

```shell
file = "states"
for state in `cat $file`
do
	echo "visit $state"
done
```

### 更改字段分隔符

默认情况下，bash shell会将下列字符当作字段分隔符：空格、制表符、换行符。

```shell
file = "states"
IFS=$'\n'
for state in `cat $file`
do
	echo "visit $state"
done
```

### 通过通配符读取目录

```shell
for file in /home/test/*
do
	echo "this is file $file"
done
```

## c语言风格的for

```shell
for (( i=1; i<=10; i++ ))
do
	echo "then next number is $i"
done
```

## while命令

```shell
while test command
do
	commands
done
```

### 使用多个测试命令

```shell
var1 = 10
while echo $var1
	[ $var1 -ge 0]
do
	echo "this is inside the loop"
	var1$[ $var1 -1 ]
done
```

## until命令

```shell
until [ $var1 -eq 0 ]
do 
	echo $var1
	var1=$[ $var1 -25 ]
done
```

- 嵌套循环

## 控制循环

- break命令
- continue命令

## 处理循环的输出

在done命令之后可以添加一个处理命令，要么管接要么重定向循环的输出。

```shell
...
done > ouput.txt

...
done | sort
```

# 第13章 处理用户输入

## 命令行参数

\$0是程序名，\$1是第一个参数，\$2是第二个参数，以此类推，直到第9个参数\$9。可以使用test命令里使用了-n参数来检查命令行参数是否有数据。

## 特殊参数变量

\$#特殊变量含有脚本运行时就有的命令行参数的个数。

变量${!#}代表了最后一个命令行参数变量。

### 抓取所有的数据

\$*和\$@变量提供了对所有参数的快速访问。这两个都能够在单个变量中存储所有的命令行参数。

\$*变量会把命令行上提供的所有参数当做单个单词保存。

\$@变量会将命令行上提供的所有参数当做同一个字符串中的多个独立的单词。它允许你遍历所有的值，将提供的每个参数分割开来。

```shell
count=1
for param in "$*"
do 
	echo "\$* Parameter #$count = $param"
done

count=1
for param in "$@"
do
	echo "\$@ Parameter #$count = $param"
done
```

## 移动变量

shift会根据它们的相对位置来移动命令行参数。

## 处理选项

### 查找选项

- 处理简单选项

```shell
#!bin/bash
# extracting command line options as parameters

while [ -n "$1" ]
do
	case "$1" in
		-a) echo "found the -a option";;
		-b) echo "found the -b option";;
		*) echo "$1 is not an option";;
	esac
	shift
done
```

- 分离参数和选项

```shell
#!bin/bash
# extracting command line options as parameters

while [ -n "$1" ]
do
	case "$1" in
		-a) echo "found the -a option";;
		-b) echo "found the -b option";;
		--) shift
			break;;
		*) echo "$1 is not an option";;
	esac
	shift
done	
```

- 处理带值的选项

```shell
#!/bin/bash
# extracting command line options and values

while [ -n "$1" ]
do
	case "$1" in
		-a) echo "found the -a options";;
		-b) param="$2"
			echo "found the -b option with parameter value $param"
			shift;;
		-c) echo "found the -c option";;
		--) shift
			break;;
		*) echo "$1 is not an option";;
	esac
	shift
done
```

### 使用getopt命令

```shell
#getopt optstring options parameters
$getopt ab:cd -a -b test1 -cd test2 test3
# -a -b test1 -c -d -- test2 test3
```

```shell
#!/bin/bash
# extracting command line options and values with getopt

set -- `getopt -q ab:c "$@"`

while [ -n "$1" ]
do
	case "$1" in
		-a) echo "found the -a options";;
		-b) param="$2"
			echo "found the -b option with parameter value $param"
			shift;;
		-c) echo "found the -c option";;
		--) shift
			break;;
		*) echo "$1 is not an option";;
	esac
	shift
done

count=1
for param in "$@"
do
	echo "Parameter #$count: $param"
	count=$[ $count + 1 ]
done
```

### 使用更高级的getopts

```shell
getopts optstring variable
```

getopts命令会使用到两个环境变量。如果选项需要跟一个参数值，OPTARG环境变量会保存这个值。OPTIND环境变量保存了参数列表中getopts正在处理的参数位置。

## 将选项标准化

例如在使用read命令的时候，使用-p选项允许世界在read命令行指定提示符；使用-t选项指定read命令等待输入的秒数；使用-s选项组织奖传给read命令的数据显示在显示器上。

显示例子说明将文件中的数据传给read命令。将文件运行cat命令后的输入通过管道直接传给含有read命令的while命令。

```shell
#!/bin/bash
# reading data from a file

count=1
cat test | while read line
do
	echo "Line $count: $line"
	count=$[ $count + 1 ]
done
echo "fininshed processing the file"
```

# 第14章 呈现数据

## 理解输入和输出

### 标准文件描述符

| 文件描述符 |   缩写   |  描述  |
| :---: | :----: | :--: |
|   0   | STDIN  | 标准输入 |
|   1   | STOUT  | 标准输出 |
|   2   | STDERR | 标准错误 |

### STDIN

对于终端界面来说，标准输入是键盘；在使用输入重定向符号时，Linux会用重定向指定文件来替换标准输入文件描述符。

### STDOUT

在终端界面，标准输出就是终端显示器；通过重定向符号，通常会显示到显示器的所有输出会被shell重定向到指定的重定向文件。

### STDERR

默认情况下，STDERR文件描述符会和STDOUT文件描述符指向相同的地方，即默认情况下，错误信息也会输出到显示器输出中。

## 重定向错误

### 只重定向错误

```shell
{ chapter14 }  >> ls -al badfile 2> test                              
{ chapter14 }  >> cat test                                           
ls: cannot access badfile: No such file or directory
```

### 重定向错误和数据

```shell
{ chapter14 }  >> ls -al test badtest 2> test1 1> test2              
{ chapter14 }  >> cat test1                                          
ls: cannot access badtest: No such file or directory
{ chapter14 }  >> cat test2                                            
-rw-rwxr--+ 1 minyi None 53 May 23 10:23 test
```

可以使用特殊的重定向符号`&>`将STDERR和STDOUT的输出重定向到同一个输出文件。

## 在脚本中重定向输出和输入

### 临时重定向

如果故意在脚本中生成错误信息，可以将单独的一行输出重定向到STDERR。在重定向到文件描述符时，你必须在文件描述符数字之前加一个and符（&）。

```shell
#!/bin/bash
#testing STDERR messages

echo "this is error" >&2
echo "this is normal output"
```

运行脚本时重定向STDERR

```shell
{ chapter14 }  >> bash stderr01.sh 2>test                              
this is normal output
{ chapter14 }  >> cat test                                             
this is error
```

### 永久重定向

```shell
#!/bin/bash
#redirecting output to different locations

exec 2>testerror

echo "this is th e start of the script"
echo "now redirecting all output to another location"

exec 1>testout

echo "this output should go to the testout file"
echo "but this should go to the testerror file" >&2
```

运行脚本

```shell
{ chapter14 }  >> bash stderr_stdin.sh                               
this is th e start of the script
now redirecting all output to another location
{ chapter14 }  >> cat testout                                          
this output should go to the testout file
{ chapter14 }  >> cat testerror                                        
but this should go to the testerror file
```

### 重定向输入

```shell
#!/bin/bash
# redirecting file input

exec 0<testfile
count=1

while read line
do
	echo "Line #$count : $line"
	count=$[ $count+1 ]
done
```

## 创建自己的重定向

- 创建输出文件描述符

```shell
#!/bin/bash
# using an alternative file descript

exec 3>testout3

echo "this should display on the monitor"
echo "this should be store in the file" >&3
echo "this should be back on the monitor"
```

- 重定向文件描述符

```shell
#storing STDPPUT. then coming back to it

exec 3>&1
exec 1>test4out

echo "this should store in output file"
echo "along with this line"

exec 1>&3

echo "be back to normal"
```

- 关闭文件描述符

要关闭文件描述符，将它重定向到特殊符号&-。

```shell
exec 3>&-
```

## 阻止命令输入

Linux系统上null文件的标准位置是/dev/null。重定向到盖文治的任何数据都会被丢掉。

此外，还可以在输入重定向中将/dev/null作为输入文件，以达到快速移除现有文件中的数据而不用先删除文件再创建。

```shell
/dev/null/ > testfile
```

## 创建临时文件

### 创建本地临时文件

```shell
mktemp testing.XXX
```

### 在/tmp目录创建临时文件

```shell
mktemp -t test.XXXX
```

### 创建临时目录

```shell
mktemp -d dir.XXXXX
```

## 记录消息

有时将输出一边发送到显示器一边发送到日志文件，可以使用特殊的tee命令。

tee命令相当于管道的一个T型接头，它将从STDIN过来的数据同时发给两个目的地。一个目的地是STDOUT，另一个目的地是tee命令行所指定的文件名。

```shell
tee filename
```

# 第15章 控制脚本

## 处理信号

常见的Linux系统信号

|  信号  |    值    |       描述        |
| :--: | :-----: | :-------------: |
|  1   | SIGHUP  |      挂起进程       |
|  2   | SIGINT  |      终止进程       |
|  3   | SIGQUIT |      停止进程       |
|  9   | SIGKILL |     无条件终止进程     |
|  15  | SIGTERM |    可能的话终止进程     |
|  17  | SIGSTOP | 无条件停止进程，但不是终止进程 |
|  18  | SIGTSTP | 停止或暂停进程，但不终止进程  |
|  19  | SIGCONT |    继续运行停止的进程    |

- Ctrl+C组合间会生成SIGINT信号，并将其发送给shell中当前运行的所有进程。
- Ctrl+Z组合键会生成SIGTSTP信号，停止shell中运行的任何进程。
- 停止进程和终止进程不同，停止进程会让程序继续保留在内存中，并能从上次停止的位置继续运行。

可以使用ps命令来查看已停止的作业。在STAT一列中，ps命令显示已停止作业的状态为T。可以使用kill命令来发送一个SIGKILL信号来终止它。

```shell
kill -9 [PID]
```

### 捕捉信号

可以使用trap命令来指定shell脚本要观察那些Linux信号，并从shell中拦截。

```shell
trap commands signals
```

```shell
#!/bin/bash
#testing signal trapping

trap "echo 'sorry, i have trapped ctrl-c'" SIGINT SIGTERM
echo "this is a test program"
count=1
while [ $count -le 10 ]
do
	echo "Loop #$count"
	sleep 5
	count=$[ $count+1 ]
done
echo "this is the end of the test program"
```

### 捕捉脚本的退出

```shell
trap "echo byebye" EXIT
```

### 移除捕捉

可以用单破折线作为命令，后跟要移除的信号来移除一组信号捕捉，将其恢复到正常状态。一旦信号捕捉被移除了，脚本会忽略该信号。但是，如果在捕捉被移除前收到信号，脚本就会在trap命令中处理它。

```shell
#!/bin/bash
# removing a set trap

trap "echo byebye" EXIT

count=1
while [ $count -le 5 ]
do
	echo "loop #$count"
	sleep 2
	count=$[ $count + 1 ]
done
trap - EXIT
echo "i just removed the trap"
```

(尝试在脚本运行过程中，按Ctrl+C)

## 以后台模式运行脚本

- 在命令行界面以后台模式运行shell脚本，只要在命令后面加个&符就行了。


- 在终端会话中使用后台进程时，如果后台进程绑定到了该终端回话的终端上了，进程会话退出时，后台进程也会退出。

## 非控制台下运行脚本

`nohup`命令可以实现：在终端回话中启动脚本，然后让脚本一直以后台模式运行，直到其完成（计时退出了终端会话）。

## 作业控制

- 可以使用`jobs`命令查看作业

  带加号的作业会被当做默认的作业。在使用作业控制命令时，如果未在命令行指定任何作业号，该作业会被当成操作对象。带减号的作业会在当前默认作业完成处理时成为下一个默认作业。

- `bg`命令加上作业号可以以后台模式重启一个作业。

- `fg`命令加上作业号可以以前台模式重启作业。

## 调整谦让度

- `nice`命令可以允许在启东市调整一个命令的调度优先级。

```shell
nice -n 10 ./test4 > testout &
```

- `renice`命令改变系统上已允许命令的优先级。

## 定时运行作业

### 使用at命令来计划执行作业

- at命令基本格式

```shell
at [-f filename] time
```

- `atq`命令可以查看系统中有哪些作业在等待


- `atrm`命令来删除等待中的作业（`atrm [作业号]`）

### 计划定期执行脚本

- corn时间表——指定作业何时运行

  ```shell
  min hour dayofmonth month dayofweek command

  #e.g.
  # run at 12:00 on the first day in each month
  00 12 1 * * command
  ```

- 列出已有的cron时间表

  ```shell
  crontab -l
  ```

- corn目录

  当创建的脚本不需要有精确的执行时间是，用预配置的cron脚本目录会更加方便。有4个基本的目录：hourly、daily、monthly、weekly。

  ```shell
  $ ls /etc/cron.*ly
  /etc/cron.daily:
  0anacron  apport  apt  bsdmainutils  cracklib-runtime  dpkg  logrotate  man-db  mlocate  passwd  popularity-contest  update-notifier-common  upstart
  /etc/cron.hourly:

  /etc/cron.monthly:
  0anacron
  /etc/cron.weekly:
  0anacron  apt-xapian-index  fstrim  man-db  update-notifier-common
  ```

  如果有脚本需要每天运行一次，只要将脚本复制到daily目录，cron就会每天执行它。

- anacron程序：

  > anacron 并不能指定何时执行某项任务， 而是以天为单位或者是在开机后立刻进行 anacron 的动作，他会去侦测停机期间应该进行但是并没有进行的 cron服务，如果有就将该任务执行一遍，然后就自动停止。

## 启动时运行

- 开机时运行脚本

  可以修改本地开机文件（不同的发行版，开机文件的位置不同），使用脚本时，必须指定脚本的全路径。

- 在新shell中启动

  bash shell会用主目录下的两个文件.bash_profile和.bashrc来自动启动脚本并设置环境变量。

  当新shell是新的登录生成的话，bash shell会运行.bash_profile文件。可以把任何登录时要运行的脚本放到该文件中。

  当新shell启动时，包括有新的登录的情况，bash shell 会运行.bashrc文件。

  如果想为系统中所有用户运行一个脚本。大多数Linux发行版提供了/etc/bashrc文件。