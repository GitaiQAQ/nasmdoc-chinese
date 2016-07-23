2.2 MASM 用户速成。
======

如果你曾使用 MASM 写程序，或者使用在 MASM 兼容模式下使用 TASM， 或者使用`a86`，本节将阐述 MASM 与 NASM 语法之间的主要区别。

如果你没有使用过 MASM，那最好先跳过这一节。

## 2.2.1 NASM 是大小写敏感的。

一个简单的区别是 NASM 是大小写敏感的。

当你调用你的符号`foo`，`Foo`，或`FOO`时，它们是不同的。

如果你在汇编`DOS`或`OS/2`， `.OBJ`文件，你可以使用`UPPERCASE`操作符来保证所有的导出到其他代码模式的符号都是大写的;

但是，在仅仅一个单独的模块中，NASM 会区分大小写符事情。

## 2.2.2 NASM 需要方括号来引用内存地址。

NASM 的设计思想是语法尽可能简洁。

它的一个设计目标是，它将在被使用的过程中，尽可能得让用户看到一个单行的 NASM 代码时，就可以说出它会产生什么操作码。

你可以在 NASM 中这样做，比如，如果你声明了:

```nasm

       foo     equ     1 
       bar     dw      2

```

然后有两行的代码

```nasm

        mov     ax,foo 
        mov     ax,bar

```

尽管它们有看上去完全相同的语法，但却产生了完全不同的操作码NASM 为了避免这种令人讨厌的情况，拥有一个相当简单的内存引用语未能。

规则是任何对内存中内容的存取操作必须要在地址上加上方括号。

但任何对地址值的操作不需要。

所以，形如`mov ax，foo`的指令总是代表一个编译时常数，不管它是一个 `EQU`或一个变量的地址;

如果要取变量`bar`的内容，你必须与`mov ax，[bar]`。

这也意味着 NASM 不需要 MASM 的`OFFSET`关键字，因为 MASM 的代码`mov ax，offsetbar`同 NASM 的语法`mov ax，bar`是完全等效的。

如果你希望让大量的 MASM 代码能够被NASM 汇编通过，你可以编写`%idefine offset`让预处理器把`OFFSET`处理成一个无操作符。

这个问题在`a86`中就更混乱了。

NASM 因为关注简洁性，同样不支持 MASM 和它的衍生产品支持的的混合语法，比如像:`mov ax， table[bx]`，这里，一个中括号外的部分加上括号内的一个部分引用一个内存地址，上面代码的正确语法是:`mov ax，[table+bx] 。

同样，`mov ax，es:[di]`也是错误的，正确的应该是`mov ax，[es:di]`。

## 2.2.3 NASM 不存储变量的类型。

NASM 被设计成不记住你声明的变量的类型。

然而，MASM 在看到`var dw 0`时会记住类型，然后就可以隐式地合用`mov var， 2`给变量赋值。

NASM 不会记住关于变量`var`的任何东西，除了它的位置，所以你必须显式地写上代码`mov word [var]，2`。

因为这个原因，NASM 不支持`LODS`，`MOVS`，`STOS`，`SCANS`，`CMPS`，`INS`，或`OUTS`指令，仅仅支持形如`LODSB`，`MOVSW`，和`SCANSD`之类的指令。

它们都显式地指定被处理的字符串的尺寸。

## 2.2.4 NASM 不会 `ASSUME`

作为 NASM 简洁性的一部分，它同样不支持`ASSUME`操作符。

NASM 不会记住你往段寄存器里放了什么值，也不会__自动__产生段替换前缀。

## 2.2.5 NASM 不支持内存模型。

NASM 同样不含有任何操作符来支持不同的 16 位内存模型。

程序员需要自己跟踪那些函数需要 far call，哪些需要 near call。

并需要确定放置正确的`RET`指令(`RETN`或`RETF`;

 NASM 接受`RET`作为`RETN`的另一种形式);

另外程序员需要在调用外部函数时在需要的编写 CALL FAR 指令，并必须跟踪哪些外部变量定义是 far，哪些是 near。

## 2.2.6 浮点处理上的不同。

NASM 使用跟 MASM 不同的浮点寄存器名:MASM 叫它们`ST(0)`，`ST(1)`等，而`a86`叫它们`0`，`1`等，NASM 则叫它们`st0`，`st1`等。

在版本 0.96 上，NASM 现在以跟 MASM 兼容汇编器同样的方式处理`nowait`形式的指令，

0.95 以及更早的版本上的不同的处理方式主要是因为作者的误解。

## 2.2.7 其他不同。

由于历史的原因，NASM 把 MASM 兼容汇编器的`TBYTE`写成`TWORD`。

NASM 以跟 MASM 不同的一种方式声明未初始化的内存。

MASM 的程序员必须使用`stack db 64 dup (?)`， NASM 需要这样写:`stack resb 64`，读作"保留 64 字节"。

为了保持可移植性，NASM 把`?`看作是符号名称中的一个有效的字符，所以你可以编写这样的代码`? equ 0`， 然后写`dw ?`可以做一些有用的事情。

`DUP`还是一个不被支持的语法。

另外，宏与操作符的工作方式也与 MASM 完全不同，可以到参阅第 4，第 5 章。