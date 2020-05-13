## Shell
- ** Shell 是一个用 C 语言编写的程序，它是用户使用 Linux 的桥梁。Shell 既是一种命令语言，又是一种程序设计语言。

### 运行Shell
- ** 作为可执行程序
  -  chmod +x ./test.sh  #使脚本具有执行权限
  - ./test.sh  #执行脚本
- ** 作为解释器参数
  - sh ./test.sh

## Shell变量
### 规范
- ** 命名只能使用英文字母，数字和下划线，首个字符不能以数字开头。
- ** 中间不能有空格，可以使用下划线（_）。
- ** 不能使用标点符号。
- ** 不能使用bash里的关键字（可用help命令查看保留关键字）。

### 使用
- ** 直接在要使用的变量前 + $  可加{} 也可不加，帮助识别变量的边界
### 只读变量
- ** 使用 readonly 命令可以将变量定义为只读变量，只读变量的值不能被改变。 

### 删除变量
- ** 使用unset可以删除变量，不能删除只读变量  unset 变量名称

### 变量的类型
- ** 局部变量 局部变量在脚本或命令中定义，仅在当前shell实例中有效，其他shell启动的程序不能访问局部变量。
- ** 环境变量 所有的程序，包括shell启动的程序，都能访问环境变量，有些程序需要环境变量来保证其正常运行。必要的时候shell脚本也可以定义环境变量。
- ** shell变量 shell变量是由shell程序设置的特殊变量。shell变量中有一部分是环境变量，有一部分是局部变量，这些变量保证了shell的正常运行

### Shell字符串
- ** 字符串是shell编程中最常用最有用的数据类型，
字符串可以用单引号，也可以用双引号，也可以不用引号。
- #### 单引号字符串
- ** 单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的；
- ** 单引号字串中不能出现单独一个的单引号（对单引号使用转义符后也不行），但可成对出现，作为字符串拼接使用。

- #### 双引号字符串
- ** 双引号里可以有变量
- ** 双引号里可以出现转义字符

- #### 获取字符串长度
```  
string="abcd"
echo ${#string}
```

- #### 将命令的输出结果赋值给遍历
- ** 直接用反引号 `` 包裹命令
- ** 用$() 包裹命令



