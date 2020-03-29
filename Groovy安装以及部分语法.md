### 下载安装
在mac上推荐使用`SDKMAN`! (The Software Development Kit Manager) 该工具使在任何Bash平台上安装Groovy(Mac OSX, Linux, Cygwin, Solaris or FreeBSD) 非常简单。

首先安装SDKMAN，打开终端，输入：Simply open a new terminal and enter:
```
$ curl -s get.sdkman.io | bash
```
根据屏幕上的指示完成安装。安装完毕后，你可以把终端关掉然后重新打开，或者在终端中输入：
```
$ source "$HOME/.sdkman/bin/sdkman-init.sh"
```
查看SDKMAN的版本在终端输入`sdk v`
```
sdk v 
SDKMAN 5.7.4+362
```
现在就可以使用SDKMAN安装Groovy了。一行命令搞定：
```
$ sdk install groovy
```
安装完毕以后，查看当前Groovy的版本：
```
$ groovy -version
Groovy Version: 3.0.2 JVM: 1.8.0_171 Vendor: Oracle Corporation OS: Mac OS X
```
现在Groovy就成功安装完毕了。

SDKMAN安装的SDK所在的路径都在`/Users/用户名/.sdkman/candidates/`目录下面，例如Groovy的安装路径是`/Users/用户名/.sdkman/candidates/groovy/3.0.2`。

# 语法

## 3. 标识符

### 3.2 带引号的标识符

一些在Java语言规范中禁止的非法标识符，在Groovy中使用引号扩起来以后是允许的。例如破折号，空格和感叹号等等。

```groovy
//定义一个Map
def map = [:]
//使用双引号
map."an identifier with a space and double quotes" = "ALLOWED"
//使用单引号
map.'with-dash-signs-and-single-quotes' = "ALLOWED"

map.'with-dash-signs!!' = "ALLOWED"

assert map."an identifier with a space and double quotes" == "ALLOWED"
assert map.'with-dash-signs-and-single-quotes' == "ALLOWED"
assert map.'with-dash-signs!!' == "ALLOWED"
```
## 4. 字符串
Groovy中字符串份两种，一种就是普通的`java.lang.String`，另一种是 `groovy.lang.GString`，在其他语言中也被叫做插值字符串。
### 4.1 单引号字符串

```
'a single-quoted string'
```
单引号字符串类型是`java.lang.String`并且不支持插值。

### 4.2. 字符串拼接
和Java一样，可以使用`+`来拼接字符串。
```
String s = 'a' + 'b'
String s1 = "a" + "b"
```

### 4.3 三引号字符串
```
'''a triple-single-quoted string'''
```
三引号字符串类型是`java.lang.String`并且不支持插值。

三引号字符串可以跨越多行，如下所示：
```
def aMultilineString = '''line one
line two
line three'''
```
如果三引号字符串有缩进的话，缩进的空格会保留。Groovy提供了方法来移除字符串前面的空白。

#### String#stripIndent()
移除每一行**开头**的空格，拥有最少空格数量的行决定移除空格的数量。当计算移除的空格数量的时候，如果某一行只包含空格，那么这一行会被忽略。看下面的例子：

定义了一个三引号的字符串，第一行开头没有空格，第二行由4个空格，第3行有8个空格。
```
def string = '''lineone
    linetwo
        linethree
'''
println(string)
//这个是不起作用的，因为第一行没有空格，计算处要移除的空格数量是0
println(string.stripIndent())
//每行强行移除4个空格
println(string.stripIndent(4))
```
输出结果
```
lineone
    linetwo
        linethree

lineone
    linetwo
        linethree

one
linetwo
    linethree

```
修改一下字符串，第1行全是空格，第3行2个空格，第3行由4个空格，第4行有8个空格。
```
def string = '''            
  lineone
    linetwo
        linethree
'''
println(string)
println(string.stripIndent())
```
输出结果，只移除了2个空格，因为第一行全都是空格，计算要移除的最少空格数量的时候，会忽略第一行。
```
            
  lineone
    linetwo
        linethree


lineone
  linetwo
      linethree
```

#### String#stripMargin()
stripMargin方法可以用来移除三引号字符串中每一行开头的所有空格，但是使用这个方法，我们需要为字符串加一个前缀，标记空格结束的位置，如下所示：
```
def string = '''    
    |line one
        |line two
                |line three
'''
println(string.stripMargin('|'))
```
输出结果
```
    
line one
line two
line three
```
#### 去掉三引号字符串开头的一个空行。

注意我们定义的三引号字符串默认会在第一行输出一个空行，如下所示：
```
def string = '''
line one
line two
line three
'''
println(string)
```
输出结果，在`line one`上会有一个空行，
```

line one
line two
line three
```
如何去掉这个空行呢？如下所示，在第一行加一个反斜杠。
```
def strippedFirstNewline = '''\
line one
line two
line three
'''
    println(string)
```
输出结果已经去掉了空行。
```
line one
line two
line three

```
#### 4.3.1 转义特殊字符
Groovy中使用反斜杠`\`来转义特殊字符

使用反斜杠`\`转义单引号
```
'an escaped single quote: \' needs a backslash'
```
使用使用反斜杠`\`转义反斜杠
```
'an escaped escape character: \\ needs a double backslash'
```
### 4.4 双引号字符串
```
"a double-quoted string"
```
双引号字符串类型是`java.lang.String`，但是如果中间有插值的话，那么类型就是`groovy.lang.GString`。转义双引号也是使用反斜杠。
```
"a double-quoted\" string"
```
#### 4.4.1 字符串插值

除了单引号和三引号字符串之外，任何Groovy表达式都可以插入到双引号字符串文本中。

```
def name = 'Guillaume' // a plain string
def greeting = "Hello ${name}" //这里的花括号不是必须的

assert greeting.toString() == 'Hello Guillaume'
```
插入一个表达式，这里的花括号是必须的
```groovy
def sum = "The sum of 2 and 3 equals ${2 + 3}"
assert sum.toString() == 'The sum of 2 and 3 equals 5'
```
除了`${}`占位符以外，我们也可以只用`$`,如下所示：
```groovy
def person = [name: 'Guillaume', age: 36]
assert "$person.name is $person.age years old" == 'Guillaume is 36 years old'
```
如果要在字符串中使用`$`或`${}`，也需要使用反斜杠来进行转义。

```
assert '$5' == "\$5"
assert '${name}' == "\${name}"
```
#### 4.4.2. 插值的特殊情况闭包表达式

当嵌入表达式包含一个箭头的时候，那么这个表达式就是一个闭包表达式。
```groovy
def sParameterLessClosure = "1 + 2 == ${-> 3}" //注释1
//输出：1 + 2 == 3
print(sParameterLessClosure)

def sOneParamClosure = "1 + 2 == ${ w -> w << 3}" //注释2
//输出：1 + 2 == 3
println(sOneParamClosure)

```
* 注释1处的闭包没有参数。
* 注释2处，这里闭包有一个`java.io.StringWriter`类型的参数，你可以使用`<<`操作符来添加内容。

闭包表达式作为插值看起来并不直观，和普通的表达式相比，它有一个有趣的优点：`懒惰计算`。考虑下面的例子：
```
def number = 1 //注释1处，
def eagerGString = "value == ${number}"
def lazyGString = "value == ${ -> number }"

assert eagerGString == "value == 1" //注释2处
assert lazyGString ==  "value == 1" //注释3处

number = 2 //4
assert eagerGString == "value == 1"  //5
assert lazyGString ==  "value == 2"  //6
```
* 注释1处，定义了一个`number`变量，然后插入到了eagerGString和lazyGString中。
* 注释2处，我们期望eagerGString中包含`1`  。
* 注释3处，我们期望lazyGString中也包含`1`。
* 注释4处，我们改变number的值。
* 注释5处，普通的插值表达式，eagerGString的值在创建的时候就确定了，也就是说eagerGString的值还是"value == 1"。
* 注释6处，但是一个具有闭包表达式作为插入值的GString，在每次将GString转化成最终的String的时候都会调用闭包表达式，导致最终的结果中包含最新的number值，"value == 2" 。

#### 4.4.4 GString 和 String 的hashCodes

尽管可以使用插值字符串（GString）替代Java字符串（String），但是它们的hashCodes是不同的。String是不可更改的而GString可以变化，依赖于它的插值。即使最终GString转化成String以后是一样的，GString和String的hashCode并不相同。
```
 println("one: ${1}".hashCode())
println("one: 1".hashCode())
```
输出结果
```
-1012478518
-1012478555
```
GString 和 Strings的hashCode不一样，应该避免使用GString作为`Map`的key，尤其是我们尝试使用String而不是GString来获取Map中的value的时候。
```groovy
def key = "a"
def m = ["${key}": "letter ${key}"]   //1  
assert m["a"] == null  //2
```
* 注释1处，使用一个键值对创建了一个Map对象，key是一个GString。
* 然后注释2处，我们通过一个String类型的key去获取value是获取不到的。因为GString和String的hashCode不一样。

### 4.5    三-双引号字符串（Triple-double-quoted string）

三-双引号字符串和双引号字符串类似，但是它可以跨越多行和三-单引号字符串类似。
```groovy
def name = 'Groovy'
def template = """
    Dear Mr ${name},

    You're the winner of the lottery!

    Yours sincerly,

    Dave
"""

assert template.toString().contains('Groovy')
```
单引号和双引号在三引号字符串中都不需要转义。

### 4.6 斜线字符串（Slashy string）

除了普通的引号字符串，Groovy提供了斜线字符串，使用`/`作为起始和分割符。

斜杠字符串在定义常规表达式和模版的时候特别有用，因为不需要转义反斜杠`\` 。

斜杠字符串的例子。
```
 def fooPattern = /.*foo.\\*/
println(fooPattern)
```
输出
```
.*foo.\\*
```
只有正斜杠需要反斜杠来转义
```
def escapeSlash = /The character \/ is a forward slash/
```
输出
```
The character / is a forward slash
```
斜杠字符串可以跨越多行
```
def multilineSlashy = /one
    two
    three/

println(multilineSlashy)
```
输出
```
one
    two
    three
```
斜杠字符串可以认为是定义GString的另外一种方式，但是有不同的转义规则。所以斜杠字符串是支持插值的。

#### 4.6.1 特殊情况

空字符串不能使用两个斜杠替代表示，因为两个斜杠被识别为单行注释。
```
assert '' == //
```
斜杠字符串主要是为了是正则表达式更容易而设计的。在斜杠字符串中，反斜杠是不需要转义的。例如`/\t/`表示的就是一个反斜杠加一个字符`\t`。正斜杠是需要被转义的，例如`/\/folder/`表示的就是`/folder`。

斜杠字符串不能以斜杠或者反斜杠结尾
```
//编译不过
def string = /ends with slash //
//编译不过
def string = /ends with slash \/
```
可以使用一个特殊的技巧替代，如下所示：
```
def string1 = /ends with slash ${'/'}/
def string2 = /ends with slash ${'\\'}/
```
输出结果
```
ends with slash /
ends with slash \
```
但是，尽量避免这样使用斜杠字符串。我们可以使用普通的字符串来实现这种效果：
```
def string1 = "ends with slash /"
def string2 = "ends with slash \\"
```
### 4.7 $斜杠字符串( Dollar slashy string)

`$斜杠字符串`是GStrings，可以跨越多行，使用`$/`作为起始界定符，使用`/$`作为结束界定符。转义字符是美元符号，它可以转义其他美元符号或者一个正斜杠`/`。在`$斜杠字符串`中`$`和正斜杠`/`和反斜杠`\`是不需要转义的，看看下面的例子体会一下。
```
def name = "Guillaume"
    def date = "April, 1st"

    def dollarSlashy = $/
    Hello $name,
    today we're ${date}.

    $ dollar sign
    $$ escaped dollar sign
    \ backslash
    / forward slash
    $/ escaped forward slash
    $$$/ escaped opening dollar slashy
    $/$$ escaped closing dollar slashy
/$

    println(dollarSlashy)
```
输出结果
```
Hello Guillaume,
    today we're April, 1st.

    $ dollar sign
    $ escaped dollar sign
    \ backslash
    / forward slash
    / escaped forward slash
    $/ escaped opening dollar slashy
    /$ escaped closing dollar slashy
```
`$斜杠字符串`是为了用来克服斜杠字符串的一些规则。当`$斜杠字符串`的转义规则适用于你的字符串的时候可以使用`$斜杠字符串`（通常情况下是不想转义在字符串中的一些正斜杠）。

### 4.8 字符串总结表格

| 字符串名称 | 字符串语法 | 支持插值 | 跨越多行 |转义字符 |
| :------:| :------: | :------: | :------: | :------: |
|  单引号| 'hello world' | N |N| `\`|
|  三-单引号| '''hello world''' | N | Y| `\`|
|  双引号| ''hello world'' | Y | N| `\`|
|  三-双引号| """hello world"""| Y | Y| `\`|
|  斜杠| /hello world/ | Y | Y| `\`|
|  美元符号斜杠| `$/hello world/$`| Y | Y| `$`|

注释：Y表示支持，N表示不支持。

### 4.9 字符
Groovy中声明字符有下面三种方式：
```
char c1 = 'A' 
assert c1 instanceof Character

def c2 = 'B' as char 
assert c2 instanceof Character

def c3 = (char)'C' 
assert c3 instanceof Character
```

## 5 数字

#### 5.5.2 指数操作符

指数操作符用`**`表示。
```
assert    2    **   3    instanceof Integer    //  8
```
## 7. Lists

* List中存储不同的元素
```
def heterogeneous = [1, "a", true]
```

* 使用负数下标从后向前获取元素
```
def letters = ['a', 'b', 'c', 'd']
print(letters[-1])
print(letters[-2])
print(letters[-3])
print(letters[-4])
```
输出结果
```
dcba
```

* 使用左移操作符`<<`向列表添加元素
```
letters << 'e'
assert letters[ 4] == 'e'
assert letters[-1] == 'e'
```
* 使用多个下标获取一次性获取多个元素，返回一个新的List。
```
assert letters[1, 3] == ['b', 'd'] 
```
* 使用区间获取元素
```
assert letters[2..4] == ['c', 'd', 'e'] 
```

* 创建多维列表
```
def multi = [[0, 1], [2, 3]]     
assert multi[1][0] == 2 
assert multi[1][1] == 3 
```
## 8 数组

在Groovy中定义数组和列表是一样的语法，但是要显式指明数组类型，如下所示：
```
String[] arrStr = ['Ananas', 'Banana', 'Kiwi']
assert arrStr instanceof String[]    
assert !(arrStr instanceof List)

def numArr = [1, 2, 3] as int[]      

assert numArr instanceof int[]       
assert numArr.size() == 3
```

* 定义多维数组
```
def matrix3 = new Integer[3][3]         
assert matrix3.size() == 3

Integer[][] matrix2                     
matrix2 = [[1, 2], [3, 4]]
assert matrix2 instanceof Integer[][]
```

## 9 Map

* 定义Map
```
def colors = [red: '#FF0000', green: '#00FF00', blue: '#0000FF']
println(colors.red)
println(colors.getClass())
```
输出结果
```
#FF0000
class java.util.LinkedHashMap
```
Map的默认类型是`java.util.LinkedHashMap`。

```
def key = 'name'
//注释1处
def person = [key: 'Guillaume']      

assert !person.containsKey('name')   
assert person.containsKey('key') 
```
注意，注释1处定义了一个变量 `key`，这个字符串作为了键，而不是`name`。如果要使用变量代表的值作为Map的键，需要用括号扩起来。
```
person = [(key): 'Guillaume']  
assert person.containsKey('name')    
assert !person.containsKey('key')
```

参考链接：
* [Install Groovy](http://www.groovy-lang.org/install.html)的SDKMAN章节
* [https://sdkman.io/sdks#groovy](https://sdkman.io/sdks#groovy)
* [Syntax](http://www.groovy-lang.org/syntax.html)

* 百度翻译



















