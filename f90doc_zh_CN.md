# [f90doc](http://erikdemaine.org/software/f90doc/Release/f90doc-0.4.0.tar.gz) 使用说明
作者： Erik Demaine

译： nile

### 内容目录
- [代码注释](#comCode)
- [注释格式](#comFormat)
- [宏](#macros) 
- [用法](#usage)
- [内部选项](#ineropts)

## <span id="comCode">代码注释</span>
f90doc 识别一切以 `!!` 开头的注释。既可以在被注释行前放注释块，也可以在其后面加注释。例如

	!! Support for variable-length strings.
	module string
等价与

	module string !! Support for variable-length strings
但是不同于下面的情况

	module string; !! Support for variable-length strings
因为分号的原因，使注释与`module string`处于不同的语句中。

注释也可以使用续行符，例如：

	module string 	& !! Support for variable-length strings
					& !! @version 1.0
					& !! author Joe Bloggs
这种形式，对`module`没多大意义，不过我发现对变量注释很有用。用之前的形式，上面的注释也可以写成

	!! Support for variable-length strings.
	!! @version 1.0
	!! @author Joe Bloggs
	module string
也可以用混合形式，在某写情况下，可能会更有用：

    !! Support for variable-length strings.
    !! @author Joe Bloggs
    module string !! @version 1.0

## <span id="comFormat">注释格式</span>
有一个准则，即代码中的注释应该看着舒适，同时也要使得代码文档格式优美。因此，单独追求在一个方面表现良好的方案是不可取的（当然，这仍然可以实现）。

在文档输出中，有种“聪明”的规则。注释中单行或多行空行（都是以`!!`打头的）表示文档中的段落分段。前面已经提到，以`!!`开头的注释块。在注释块中的换行，并不引起文档中的换行。

如果在一行的"!!"后面加上`v`，则该行格式不变，被原样输出（译者注：类似python中的"\`\`\`"）。其中`v`被当作空格处理，该行中的其余字符不做处理。如果需要多行的原格式输出，需要用`!!>`(开始行)和`!!<`(结束行)。(字符`<`和`>`被视为`v`为表达结束和起始的含义而转了个方向) 其中`<`和`>`也被当作空格处理。所以，下面的注释和其在文档中的格式将是一样的。
 
    !! An example of this module's usage is
    !!>       call copier_read ("input_file")
    !!        call  copier_write ("output_file")
    !!<       call copier_close
最有趣的特性是列表模式。这看起来像人们（至少像我自己）制作ASCII码表的过程。一个短线`-`，后面跟任意数量的空格，表示一个列表。同级列表必须位于相同的缩进行上。缩进更多的`-`表示嵌套列表。比最后一个`-`有相同或更少缩进的文本表示列表的结束。例如：

    !! Mouths are defined by the following operations:
    !!    - Eat, which can be performed in two modes,
    !!          - open-mouth or
    !!          - close-mouth
    !!        The latter mode is preferred
    !!    - Chew
    !! There are more operations, but this serves as an example.
这段注释在HTML格式中，显示为：

> Mouths are defined by the following operations:

> * Eat, which can be performed in two modes,

>    * open-mouth or
>    * close-mouth.

>     The latter mode is preferred

>* Chew

> There are more operations, but this serves as an example.

注意，前面提到的保持原格式的输出格式，被当作前一项的一部分。

最后是两种格式修饰，_斜体_和 __加粗__。斜体模式，是在单词的开头和结尾加上`_`。例如`_big_`产生_big_。在斜体字内部的`_`被视为空格，如`_really big_`和`_really_big_`都产生  `_really big_`。（注意：不鼓励把`big`变为斜体）

类似地，粗体模式只需要把上面的`_`替换为`*`就OK了。需要注意的是，多行文本斜体现在还不支持。    

## <span id="macros">宏</span>
目前，有三个简单的宏支持。

`@author A. Person`

&emsp;&emsp; 添加一个作者。每个`@author`只能添加一个作者。他们最后都会放在文档注释区底部。

`@version 1.2.3q`

&emsp;&emsp;设置版本号为`1.2.3q`。每个注释区应只有一个`@version`语句。它放在作者之后

`@see ModuleName`

&emsp;&emsp; 添加"see also"，以参照其他`module`。在以后，这也将支持`modules`中的对象。

## <span id="usage">用法</span>
f90doc程序简单易用。主要选项是`-c(p|s|h)`。之后，你可以选择一些`f90`文件（例如，`*.f90`，如果你的shell支持的话）。

还可以指明文件是`-fixed`还是`-free`的，以判断是使用f77格式还是自由格式。默认是自由格式。

在`f90`文件前，可以用`-o file.html`指明该源文件的输出文件名。HTML文件默认输出在当前目录，名字跟顶级的`nodules`,`subroutines`,`functions`和`programs`相关联。为命名的`program`默认产生`program.html`。

`-c`选项指明处理文件中的`!!`注释语句。`-cp`选项指明将文件中的注释原格式输出。`-cs`指明将文件中的注释智能格式化。这是推荐选项，也是默认选项。最后，`-ch`指明注释写成HTML格式，就像[JavaSoft](http://www.oracle.com/technetwork/java/index.html)的[javadoc](http://www.oracle.com/technetwork/java/index.html)一样。

## <span id="ineropts">内部选项</span>

f90doc 有几个可调整的选项。例如，在`htmling.pl`中，`PUBLIC GLOBALS` 下，可以找到

- `$htmling::suppress_calls = 0 ;`

&emsp;&emsp; 设置为`1`，可以去掉每个单元的调用列表。这用于向后兼容。

- `$htmling::calls_make_links = 0;`

&emsp;&emsp; 设置为`1`,可以给调用历程设置链接。例如，调用`xyz`，会产生一个链向`xyz.html`的超链接。

#### [原文地址](http://erikdemaine.org/software/f90doc/doc.html#-c)
