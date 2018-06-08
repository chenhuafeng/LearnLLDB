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

To see the list of **all** of the commands execute the help command in `LLDB` or go to the [Wiki](https://github.com/facebook/chisel/wiki).

```Python
(lldb) help
The following is a list of built-in, permanent debugger commands:
...

The following is a list of your current user-defined commands:
...
```

The bottom of the list will contain all of the commands sourced from `Chisel`.

You can also inspect a specific command by passing its name as an argument to the help command (as with all other `LLDB` commands). 

```
(lldb) help border
Draws a border around <viewOrLayer>. Color and width can be optionally provided.

Arguments:
  <viewOrLayer>; Type: UIView*; The view to border.

Options:
  --color/-c <color>; Type: string; A color name such as 'red', 'green', 'magenta', etc.
  --width/-w <width>; Type: CGFloat; Desired width of border.

Syntax: border [--color=color] [--width=width] <viewOrLayer>
```

All of the commands provided by `Chisel` come with verbose help. Be sure to read it when in doubt!

## Custom Commands
You can add local, custom commands. Here's a contrived example.

```python
#!/usr/bin/python
# Example file with custom commands, located at /magical/commands/example.py

import lldb
import fblldbbase as fb

def lldbcommands():
  return [ PrintKeyWindowLevel() ]
  
class PrintKeyWindowLevel(fb.FBCommand):
  def name(self):
    return 'pkeywinlevel'
    
  def description(self):
    return 'An incredibly contrived command that prints the window level of the key window.'
    
  def run(self, arguments, options):
    # It's a good habit to explicitly cast the type of all return
    # values and arguments. LLDB can't always find them on its own.
    lldb.debugger.HandleCommand('p (CGFloat)[(id)[(id)[UIApplication sharedApplication] keyWindow] windowLevel]')
```

Then all that's left is to source the commands in lldbinit. `Chisel` has a python function just for this, _loadCommandsInDirectory_ in the _fblldb.py_ module.

```Python
# ~/.lldbinit
...
command script import /path/to/fblldb.py
script fblldb.loadCommandsInDirectory('/magical/commands/')

```

There's also builtin support to make it super easy to specify the arguments and options that a command takes. See the _border_ and _pinvocation_ commands for example use.

## Development Workflow
Developing commands, whether for local use or contributing to `Chisel` directly, both follow the same workflow. Create a command as described in the [Custom Commands](#custom-commands) section and then

1. Start `LLDB`
2. Reach a breakpoint (or simply pause execution via the pause button in `Xcode`'s debug bar or `process interrupt` if attached directly)
3. Execute `command source ~/.lldbinit` in LLDB to source the commands
4. Run the command you are working on
5. Modify the command
6. Optionally run `script reload(modulename)`
7. Repeat steps 3-6 until the command becomes a source of happiness
