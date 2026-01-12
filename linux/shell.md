# shell

- [shell](#shell)
  - [bash 的基础特性](#bash-的基础特性)
  - [变量](#变量)
    - [环境变量](#环境变量)
    - [特殊变量](#特殊变量)
    - [特殊状态变量](#特殊状态变量)
    - [bash 的执行](#bash-的执行)
  - [bash 一些基础的内置命令](#bash-一些基础的内置命令)
  - [shell 子串的用法](#shell-子串的用法)
  - [条件判断](#条件判断)
  - [流程控制](#流程控制)
    - [if 判断](#if-判断)
    - [case 语句](#case-语句)
    - [for 循环](#for-循环)
    - [while 循环](#while-循环)
  - [读取控制台](#读取控制台)
  - [函数](#函数)
    - [系统函数](#系统函数)
      - [basename](#basename)
      - [dirname](#dirname)
    - [自定义函数](#自定义函数)
  - [正则表达式入门](#正则表达式入门)
    - [常规匹配](#常规匹配)
    - [常用特殊字符](#常用特殊字符)
  - [文本处理工具](#文本处理工具)
    - [cut](#cut)
    - [awk](#awk)

## bash 的基础特性

```shell
history # 命令历史记录
        # -c 清空历史记录
        # -r 回复历史记录
echo $HISTSIZE
less ~/.bash_history
!历史id 这执行理事命令
!! 执行上一条命令
# ctrl+l 快速清屏
```

## 变量

- `'` 单引号，不识别特殊语法
- `"` 双引号，能识别特殊符号
- \` 反引号，中的命令执行结果会被保留下来 `` name=`ls`  ``

每次调用 bash/sh 解释器执行脚本，都会开启一个子 shell，因此不保留当前的 shell 变量，通过调用 source（或者点.符号，在当前 shell 环境加载脚本，因此保留变量

### 环境变量

环境变量一般指的是用 export 内置命令导出的变量，用于定义 shell 的运行环境、保证 sh 酬命令的正确执行。shell 通过环境变量确定登录的用户名、PATH 路径、文件系统等各种应用。

环境变量可以在命令行中临时创建，但是用户退出 shell 终端，变量即丢失，如要永久生效，需要修改`环境变量配置文件`。

- 用户个人配置文件`~/bash-profi1e~`、`/.bashrc`远程登录用户特有文件。
- 全局配置文件`/etc/profile`、`/etc/bashrc`，且系统建议最好创建在`/etc/profile.d/`，而非直接修改主文件，修改全局配置文件，影响所有登录系统的用户。

**检查系统环境变量的命令**

- set，输出所有变量，包括全局变量、局部变量。
- env，只显示全局变量。
- declare，输出所有的变量，如同 set。
- export，显示和设置环境变量值。

**撤销环境变量**

- unset 变量名，删除变量或函数。

**设置只读变量**

- readonly，只有 shell 结束，只读变量失效。

```shell
readonly name="what"
name="d"
# -bash: name: readonlyvariable
```

```shell
$? # 上一次命令的返回值 通常0是成功 1-255为失败
```

### 特殊变量

shell 的特殊变量，用在如脚本，函数传递参数使用，有如下特殊的，位置参数变量。

- `$0` 获取 shell 脚本文件名，以及本路径。
- `$n` 获取 shell 脚本的第 n 个 0 数，n 在 1~9 之间，如$1, $2,$9，大于 9 则需要写，\${10}，参数空格隔开。
- `$#` 获取执行的 shell 后面的参数总个数。
- `$*` 获取 shell 脚本所有参数，`"$*"`为单个字符串。
- `$@` 获取 shell 脚本所有参数，`"$@"`可迭代。

### 特殊状态变量

- `$?`上一次命令执行状态返回值，1 正确，非 0 失败。
- `$$` 当前 shell 脚本的进程号。
- `$!` 上一次后台进程的 PID。
- `$_` 一再次之前执行的命令，最后一个参数。

```shell
man bash # 搜索`specialParameters`
```

### bash 的执行

1. source 命令与.执行脚本是相同的。
1. 每次调用 bash/sh 解释器执行脚本，都会开启一个子 Shell，因此不保留当前的 Shell 变量，通过 pstree 命令检查进程树。
1. 调用 sourse（e 或者点符号，在当前 shell 环境加载脚本，因此保留变量

## bash 一些基础的内置命令

```shell
echo hello # -n 不换行输出
           # -e 解析字符串中的特殊符号
                # \n换行
                # \r回车
                # \t制表符四个空格
                # \b退格
printf hello # 不换行输出
eval "ls;cd /etc" # 执行命令
exec "ls" # 不创建子进程，执行后续命令，且执行完毕后，自动exit
```

## shell 子串的用法

`${param}` 返回变量值。  
`${#param}` 返回变量长度，字符长度。  
`${param:start}` 返回变量 start 数值之后的字符。  
`${param:start:length}` 提取 offset 之后的 length 限制的字符。  
`${param#word}` 从变量开头删除最短匹配的 word 子串。  
`${param##word}` 从变量开头，删除最长匹配的 word。  
`${param％word}` 从变量结尾删除最短的 word。  
`${param％％word}` 从变量结尾开始删除最长匹配的 word。  
`${param/pattern/string}` 用 string 代替第一个匹配的 patter。  
`${param//pattern/string}` 用 string 代替所有的 pattern。

```shell
# 统计字符长度的方法
strr="hello"
wc # 统计命令
   # -l 统计行数。 -L统计最长的行的长度。
echo $strr | wc -l # 1
echo $strr | wc -L # 5

expr # 数值计算
expr length $strr # 5
echo ${#strr}

seq # 序列命令 -s 指定分隔符
seq -s "~" 10 # 1~2~3~4~5~6~7~8~9~10

time ls # 统计代码执行的时间
        #real实际运行时间 user用户态运行时间 sys内核态运行时间
```

## 条件判断

1. 基本语法  
   (1). test condition  
   (2). \[ condition \]（注意 condition 前后要有空格）  
   注意：条件非空即为 true，\[atguigu\]返回 true，\[ \]返回 false。0 为真值，非 0 为假，这与常见的编程语言不太一样。

2. 常用判断条件

   1. 两个整数之间的比较  
      -eq 等于(equal)  
      -lt 小于(less than)  
      -gt 大于(greater than)  
      -ne 不等于(not equal)  
      -le 小于等于（less equal)  
      -ge 大于等于(greater equal)  
      注：如果是字符串之间的比较，用等号“=”判断 不相等；用“!=”判断不等。
   2. 按照文件权限进行的判断
      -r 有读的权限(read)  
      -w 有写的杖限(write)  
      -x 有执行的权限(execute)
   3. 按照文件类型进行判断  
      -e 文件存在(existence)  
      -f 文件存在并且是一个常规的文件(file)  
      -d 文件存在并且是一个目录(directory)
   4. 逻辑判断  
      -a and 逻辑与  
      -o or 逻辑或
   5. 字符串长度判断  
      -z 长度为 0  
      -n 长度大于 0

3. 示例

```shell
[ -r hello.sh ] # 判断是否有可读的权限
echo $?

[ -r hello.txt ] && [ 1 = 2 ] 多条件判断
echo $?

[ 1 -eq 1 ] && echo OK || echo Not ok # 三元运算
# OK
```

## 流程控制

### if 判断

1. 基本语法

   1. 单分支

   ```shell
   if [ 条件判断式 ];then
        程序
   if


   if [ 条件判断式 ]
   then
        程序
   if
   ```

   2. 多分支

   ```shell
   if [ 条件判断式 ]
   then
        程序
   elif [ 条件判断式 ]
   then
        程序
   else
        程序
   fi
   ```

### case 语句

基本语法

```shell
case $变量名  in
"值1")
     如果、量的值等于值1，则执行程序1
;;
"值2")
     如果变量的值等于值2，则执行到序2
;;
# ...省略其他分支..
*)
     如果变量的值都不是以上的值，则执行此程序“
;;
esac
```

**注意事项：**

1. case 行尾必须为单词"in"，每一个模式匹配必须以右括号")"结束。
2. 双分号";;"表示命令序列结束，相当于 java 中的 break。
3. 最后的"\*)"表示默认模式，相当 java 中的 default。

### for 循环

基本语法

```
for (( 初始值;循坏控制条件;变量变化，))
do
     程序
done

for 变量 in 值1 值2 值3 ...
do
     程序
done
```

示例

```shell
for (( i=1;i<=$1;i++ ))
do
    sum=$[ $sum+$i ]
done

echo $sum

echo ======$*=====
for a in "$*"
do
    echo $a
done

echo ======$@=====
for a in "$@"
do
    echo $a
done
```

### while 循环

基本语法

```shell
while [ 条件判断式 ]
do
     程序
done
```

## 读取控制台

基本语法  
read 选项 参数

1. 选项
   -p: 指定读取值时的提示符;
   -t: 指定读取值时等待的时间（秒）如果-t 不加表示一直等待
2. 参数
   变量：指定读取值的变量名

## 函数

### 系统函数

#### basename

基本语法  
 basename \[strmg/pathname\] \[suffix\]  
**功能描述**：  
basename 命令会删掉所有的前缀包括最后一个（"/"）字符，然后将字符串显示出来。basename 可以理解为取路径里的文件名称。  
选项：  
 suffix 为后缀，如果 suffix 被指定了，会将 pathname 或 string 中的 suffix 去掉。

#### dirname

dirname 文件绝对路径  
**功能描述**：  
 从给定的包含绝对路的文件名中去除文件名（非目录的部分），然后返回剩下的路径（目录的部分））  
dirname 可以理解为取文件路彳爷的绝对路径名称。

### 自定义函数

**基本语法**

```shell
[function]  funname [()]
{
  Action
  [return int;]
}
```

**经验技巧**

1. 必须在调用函数地方之前，先声明函数，shell 脚本是逐行运行。不会像其它语言一样先编译。
2. 函数返回值，只能通过$?系统变量获得，可以显示加：return 返回，如果不加，将以最后、条命令运行结果，作为返回值。return 后跟数值 n(0．255)

## 正则表达式入门

正则表达式使用单个字符串来揣述、匹配一系列符合某个语法规则的字符串。在很多文本编辑器里，正则表达式通常被用来检索、替换那些符合某个模式的文本。在 linux 中，grep，sed，awk 等文本处理工具都支持通过正则表达式进行模式匹配。

### 常规匹配

一串不包含特殊字符的正则表达式匹配它自己，例如：  
`linux@xxx:cat /etc/passwd | grep aaa`  
就会匹配所有包含 aaa 的行。

### 常用特殊字符

1. 特殊字符：`^`  
   `^`匹配一行的开头，例如：  
   `linux@xxx:cat/etc/passwd | grep ^a`  
   会匹配出所有以 a 开头的行。
1. 特殊字符：`$`  
   `$`匹配一行的结束，例如:  
   `linux@xxx:cat/etc/passwd | grep a$`  
   会匹配出所有以 a 结尾的行。
1. 特殊字符：`.`  
   `.`匹配一行的结束，例如:  
   `linux@xxx:cat/etc/passwd | grep r..t`  
   会匹配包含 rabt，rbbt,l\*dt,root 等的所有行。
1. 特殊字符：`*`  
   `*`不单独使用，他和上一个字符连用，表示匹配上一个字符 0 次或多次，例如:  
   `linux@xxx:cat/etc/passwd | grep ro*t`  
   会匹配 rot，root，rooot，roooot 等所有行。
1. 字符区间（中括号）：`[]`
   `[]`表示匹配某个范围内的一个字符，例如:  
   \[6,8] 匹配 6 或者 8  
   \[0-9] 匹配一个 0-9 的数字  
   \[0-9]_ 匹配任意长度的数字字符串  
   \[a-z] 匹配一个 a-z 之间的字符  
   \[a-z]_ 匹配任意长度的字母字符串  
   \[a-c,e-f] 匹配 a-c 或者 e-f 之间的任意字符  
   `linux@xxx:cat/etc/passwd | grep r[a,b,c]*t`  
   会匹配直，rat，小 t，rabt，rbact，rabccbaaacbt 等等所有行。

## 文本处理工具

### cut

cut 的工作就是“剪”，具体的说就是在文件中负责剪切数据用的。cut 命令从文件的每一行剪切字节、字符和字段并将这些字节、字符和字段输出。

1. 基本用法
   cut \[选项参数] filename
   说明：默认分隔符是制表符
1. 选项参数说明
   | 选项参数 | 功能 |
   | -------- | ---------------------------------------------- |
   | -f | 列号，提取第几列 |
   | -d | 分隔符，按照指定分隔符分割列，默认是制表符“\t” |
   | -c | 按字符进行切割后，加加 n 表示取第几列，比如-c 1 |

### awk

一个强大的文本分析工具，把文件逐行的读入，以空格为默认分隔符将每行切片，切开的部分再进行分析处理。

1. 基本用法
   awk \[选项参数] '/pattenl/{action1}/patten2/{action2}' filename  
   pattern：表示 awk 在数据中查找的内容，就是匹配模式  
   action：在找到匹配内容时所执行的一系列命令
1. 选项参数说明  
   | 选项参数 | 功能 |
   | -------- | -------------------- |
   | -F | 指定输入文件分隔符 |
   | -v | 赋值一个用户定义变量 |
