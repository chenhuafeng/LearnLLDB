# LearnLLDB
lldb命令介绍

## Chisel
`Chisel`是一个LLDB命令的集合，用于辅助调试iOS应用程序。

### 安装

```shell
brew update
brew install chisel
```

如果 `.lldbinit` 文件不存在，您可以通过终端命令创建并打开它。

```shell
 touch .lldbinit 
 open .lldbinit 
```

然后将下面的行添加到 `~/.lldbinit` 文件中。

```Python
# ~/.lldbinit
...
command script import /usr/local/opt/chisel/libexec/fblldb.py
```

或者，下载`Chisel `并将下面的行添加到您的 `~/.lldbinit` 文件中。

```Python
# ~/.lldbinit
...
command script import /path/to/fblldb.py

```

命令会在下一次启动 `Xcode` 的时候生效。

### 命令
There are many commands; here's a few:
*(Compatibility with iOS/Mac indicated at right)*

|命令				 |描述             |iOS    |OS X   |
|-----------------|----------------|-------|-------|
|pviews           |递归打印 `keyWindow` 的view description。|Yes|Yes|
|pvc              |递归打印 `keyWindow` 的view controller description。|Yes|No|
|visualize        |在你的Mac上用 `预览.app` 打开一个 `UIImage`, `CGImageRef`, `UIView`, `CALayer`, `NSData` (of an image), `UIColor`, `CIColor`, 或者 `CGColorRef`。|Yes|No|
|fv               |在层次结构中找到其class name与所提供的正则表达式匹配的一个view。|Yes|No|
|fvc              |在层次结构中找到其class name与所提供的正则表达式匹配的一个view controller。|Yes|No|
|show/hide        |显示或隐藏给定的view或layer。你甚至不用continue进程就可以看到变化！|Yes|Yes|
|mask/unmask      |用显而易见的矩形覆盖view或layer，以可视化它所在的位置。|Yes|No|
|border/unborder  |向view或layer添加边框以可视化它所在的位置。|Yes|Yes|
|caflush          |刷新渲染服务 (如果没有动画在执行中，相当于"repaint").|Yes|Yes|
|bmessage         |在类方法或实例方法上设置一个符号断点，而不用担心层次结构中的哪一个类实际上实现了该方法。|Yes|Yes|
|wivar            |在对象的实例变量上设置watchpoint。|Yes|Yes|
|presponder       |从给定的对象开始打印响应链。|Yes|Yes|
|...              |... and many more!|

要查看**所有**命令的列表，请在 `LLDB` 中执行 `help` 命令，或者转到[Wiki](https://github.com/facebook/chisel/wiki)。

```Python
(lldb) help
The following is a list of built-in, permanent debugger commands:
...

The following is a list of your current user-defined commands:
...
```