4.10 其他的预处理指令。
======

NASM 还有一些预处理指令允许从外部源中获取信息，现在，他们包括:下面的预处理指令使 NASM 能正确地自理 C++/C 语言预处理器的输出。

*  `%line` 使 NASM 能正确地自理 C++/C 语言预处理器的输出。(参阅 4.10.1)
*  `%!` 使 NASM 从一个环境变量中读取信息，然后这些信息就可以在你的程序中使用了。(4.10.2)

## 4.10.1 `%line`操作符。

`%line`操作符被用来通知 NASM，输入行与另一个文件中指定的行号相关。

一般这另一个文件会是一个源程序文件，它作为现在 NASM 的输入，但它是一个预处理器的输出。

`%line`指令允许 NASM 输出关于在这个源程序文件中的指定行号的信息，而不是被 NASM 读进来的整个文件。

这个预处理指令通常不会被程序员用到，但会让预处理器的作者感兴趣，`%line`的使用方法如下:

```nasm
       %line nnn[+mmm] [filename]
```

在这个指令中，`nnn`指定源程序文件中与之相关的特定行，`mmm`是一个可选的参数，它指定一个行递增的值;

每一个被读进来的源文件行被认为与源程序文件中的`mmm`行相关。

最终，`filename`可选参数指定源程序文件的文件名。

在读到一条`%line`预处理指令后，NASM 会报告与指定的值相关的所有的文件名和行号

## 4.10.2 `%!``<env>`: 读取一个环境变量。

`%!<env>` 操作符可以在汇编时读取一个环境变量的值，这可以用在一个环境变量的内容保存到一个字符串中。

该字符串可以用在你程序的其他地方。

比如，假设你有一个环境变量`FOO`，你希望把`FOO`的值嵌入到你的程序中去。

你可以这样做:

```nasm
       %define FOO    %!FOO 
       %define quote   ' 

       tmpstr  db      quote FOO quote
```

在写的时候，在定义`quote`时，它会产生一个`没有结束的字符串`的警告信息，它会自己在读进来的字符串的前后加上一个空格。

我没有办法找到一个简单的工作方式(尽管可以通过宏来创建)，我认为，你没有必要学习创建更为复杂的宏，或者如果你用这种方式使用这个特性，你没有必要使用额外的空间。