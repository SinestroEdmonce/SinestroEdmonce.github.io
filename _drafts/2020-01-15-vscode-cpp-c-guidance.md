---
layout: post
title: 在VSCode中使用C或C++的流程和相关配置
categories: [VSCode, C/C++]
description: VSCode作为一款轻量级文本编辑器有极为强大的功能，可以作为多款语言的IDE来使用，但是想要将其作为C/C++开发的IDE工具，还需要一些配置和插件。
keywords: VSCode, C/C++
---

本文面向初学者，每一步都比较详细。阅读本文能学习一些命令行、程序编译和调试，以及使用VSCode的知识。

本文许多内容都可从VSCode官方文档：[C++ programming with Visual Studio Code](https://code.visualstudio.com/docs/languages/cpp) 以及各个扩展的文档中获得，并且他们还会进行更新，如果想更深入了解，可以去看官方文档。根据本文中的配置方法可以得到的最终效果是：实时显示编译阶段的错误、代码片段、补全、格式化、单文件的编译与调试。

## 环境的准备

VSCode的官网、下载、安装，我就不多说了。VSCode只是一个纯文本编辑器（editor），不是IDE（集成开发环境），不含编译器（compiler）和许多其它功能，所以编译器要自己装好。

### 下载编译器

编译器的下载地址是：[MinGW-w64-for-32-and-64-bit-Windows](https://sourceforge.net/projects/mingw-w64/files/) 。进入网页后往下稍微翻一下，选最新版本中的`x86_64-posix-seh`。最好不要用`Download Latest Version`，这个是在线安装包，可能因为国内的“网络环境”下载失败。如果浏览器下载失败就换迅雷下或者连手机开的热点下，还失败，就翻墙来下载。

### “安装”编译器 
下载下来的是一个`.7z`的压缩包。解压完后放到一个不容易被删的地方，层叠的可以去掉一些。看好`bin`文件夹的完整路径，我图里的是`C:\mingw64\bin`，把它加到环境变量中的`PATH`里去。

<div style="text-align: center;">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/mingw_storage_path.jpg">
</div>

<div style="text-align: center;">
    <figure>
        <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/env_path.jpg"/>
        <figcaption>环境变量的添加顺序不重要；bin文件夹路径可以不一样</figcaption>
    </figure>
</div>

### 验证
按`Win+R`，运行CMD（不要跳这一步），输入`gcc --version`，如果没有显示版本号则说明环境变量没有添加成功，或者添加的`bin`文件夹路径不对，或者`bin`文件夹中没有`gcc.exe`文件。

![img](https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/gcc_cmd.jpg)

![img](https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/gcc_version.jpg)

### 安装扩展（extension）

必须的扩展如下：

- C/C++：又名`cpptools`，提供Debug和Format功能
- Code Runner：右键即可编译运行单文件，但无法Dubug

其他可选扩展如下：

- Bracket Pair Colorizer 2：彩虹花括号
- One Dark Pro：VSCode主题

### 补充知识

- 编译器是把源代码变成可执行文件的，编辑器是你打字的软件。记事本就是一个编辑器，VSC也是编辑器。**编辑器是无法编译运行程序的**，因为那是编译器的工作

- MinGW是gcc在Windows下的移植，gcc是世界上最流行的C/C++编译器组合。但gcc这个名字也指编译C语言的那个程序，g++才是C++编译器。即gcc程序和g++程序包含在gcc套件以及MinGW里，当只说gcc时要根据语境自己区分

- 其实MinGW和MinGW-w64只是名字像，它们是两个不同的项目。为了方便，本文中的MinGW指的其实都是MinGW-w64。MinGW只能产生32位程序

- 扩展是`extension`，插件是`plugin`，VSC用的是前者这种称呼。大部分文章都是混用两者的，不严谨但是能理解就行，要学会抓主要矛盾。当然本文用的都是正确的

- 可选阅读：[MinGW vs MinGW-W64及其它](https://tieba.baidu.com/p/3186234212?red_tag=0394483506)、[What is difference between sjlj vs dwarf vs seh?](https://stackoverflow.com/questions/15670169/what-is-difference-between-sjlj-vs-dwarf-vs-seh)

## 配置.json文件

创建一个打算存放代码的文件夹，称作工作区文件夹；路径不能含有中文和引号，最好不要有空格，我用的是`C:\VS-Code-C`。C和C++需要分别建立不同的文件夹，除非用虚拟工作区。不要选上一段建出来的文件夹，源代码和编译器要分开放。

打开VSC，选打开文件夹；最好不要选“添加工作区文件夹”，这个就是虚拟工作区，我没用过，不保证没问题。点新建文件夹，名称为.vscode。不在资源管理里新建的原因是Windows的Explorer不允许创建的文件夹第一个字符是点（1903后才支持）。然后创建`launch.json`，`tasks.json`，`settings.json`放到.vscode文件夹下：

<div style="text-align: center;">
    <figure>
        <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/vscode_json.jpg"/>
        <figcaption>要在.vscode文件夹里</figcaption>
    </figure>
</div>

这几个文件的内容见下述。有些地方可选修改，自己对照着注释进行修改。注意：如果是写C++，`tasks.json`的一个地方必须要修改。

### `launch.json`代码

`externalConsole`可根据自己喜好修改；`cwd`可以是程序运行时的相对路径，如有需要可以改为`$`{fileDirname}`。

```json
// https://github.com/Microsoft/vscode-cpptools/blob/master/launch.md
{
    "version": "0.2.0",
    "configurations": [{
        "name": "(gdb) Launch", // 配置名称，将会在启动配置的下拉菜单中显示
        "type": "cppdbg", // 配置类型，cppdbg对应cpptools提供的调试功能；可以认为此处只能是cppdbg
        "request": "launch", // 请求配置类型，可以为launch（启动）或attach（附加）
        "program": "${fileDirname}/${fileBasenameNoExtension}.exe", // 将要进行调试的程序的路径
        "args": [], // 程序调试时传递给程序的命令行参数，一般设为空即可
        "stopAtEntry": false, // 设为true时程序将暂停在程序入口处，相当于在main上打断点
        "cwd": "${workspaceFolder}", // 调试程序时的工作目录，此为工作区文件夹；改成${fileDirname}可变为文件所在目录
        "environment": [], // 环境变量
        "externalConsole": true, // 为true时使用单独的cmd窗口，与其它IDE一致；18年10月后设为false可调用VSC内置终端
        "internalConsoleOptions": "neverOpen", // 如果不设为neverOpen，调试时会跳到“调试控制台”选项卡，你应该不需要对gdb手动输命令吧？
        "MIMode": "gdb", // 指定连接的调试器，可以为gdb或lldb。但我没试过lldb
        "miDebuggerPath": "gdb.exe", // 调试器路径，Windows下后缀不能省略，Linux下则不要
        "setupCommands": [
            { // 模板自带，好像可以更好地显示STL容器的内容，具体作用自行Google
                "description": "Enable pretty-printing for gdb",
                "text": "-enable-pretty-printing",
                "ignoreFailures": false
            }
        ],
        "preLaunchTask": "Compile" // 调试会话开始前执行的任务，一般为编译程序。与tasks.json的label相对应
    }]
}
```

### ·tasks.json·代码

如果是编写C++，编译器需改成g++；如果不想要额外警告，把`-Wall`那一条删去；`-std`根据自己需要修改；Linux下不需要加`-fexec-charset`；`reveal`控制编译时是否跳转到终端面板，可根据自己喜好修改；即使设为`never`，也只是不自动跳转，手动点进去还是可以看到信息。

```json
// https://code.visualstudio.com/docs/editor/tasks
{
    "version": "2.0.0",
    "tasks": [{
        "label": "Compile", // 任务名称，与launch.json的preLaunchTask相对应
        "command": "gcc",   // 要使用的编译器，C++用g++
        "args": [
            "${file}",
            "-o",    // 指定输出文件名，不加该参数则默认输出a.exe，Linux下默认a.out
            "${fileDirname}/${fileBasenameNoExtension}.exe",
            "-g",    // 生成和调试有关的信息
            "-Wall", // 开启额外警告
            "-static-libgcc",     // 静态链接libgcc，一般都会加上
            "-fexec-charset=GBK", // 生成的程序使用GBK编码，不加这一条会导致Win下输出中文乱码
            // "-std=c11", // C++最新标准为c++17，或根据自己的需要进行修改
        ], // 编译的命令，其实相当于VSC帮你在终端中输了这些东西
        "type": "process", // process是vsc把预定义变量和转义解析后直接全部传给command；shell相当于先打开shell再输入命令，所以args还会经过shell再解析一遍
        "group": {
            "kind": "build",
            "isDefault": true // 不为true时ctrl shift B就要手动选择了
        },
        "presentation": {
            "echo": true,
            "reveal": "always", // 执行任务时是否跳转到终端面板，可以为always，silent，never。具体参见VSC的文档
            "focus": false,     // 设为true后可以使执行task时焦点聚集在终端，但对编译C/C++来说，设为true没有意义
            "panel": "shared"   // 不同的文件的编译信息共享一个终端面板
        },
        // "problemMatcher":"$gcc" // 此选项可以捕捉编译时终端里的报错信息；但因为有Lint，再开这个可能有双重报错
    }]
}
```

### `settings.json`代码

把这个文件里的东西放到“用户设置”里可以覆盖全局设置，否则只在当前工作区才有效。这两点各有自己的优势。Code Runner的命令行和某些选项可以根据自己的需要在此处修改，想自定义或者想知道是什么意思还是参见此扩展的文档和gcc使用教程。如果终端用的是cmd（Win7默认）需要改用注释掉的，或者把`terminal.integrated.shell.windows`改为PowerShell；Win10默认就是PowerShell就不用改。

```json
{
    "files.defaultLanguage": "c", // ctrl+N新建文件后默认的语言
    "editor.formatOnType": true,  // 输入分号(C/C++的语句结束标识)后自动格式化当前这一行的代码
    "editor.suggest.snippetsPreventQuickSuggestions": false, // clangd的snippets有很多的跳转点，不用这个就必须手动触发Intellisense了
    "editor.acceptSuggestionOnEnter": "off", // 我个人的习惯，按回车时一定是真正的换行，只有tab才会接受Intellisense
    // "editor.snippetSuggestions": "top", // （可选）snippets显示在补全列表顶端，默认是inline

    "code-runner.runInTerminal": true, // 设置成false会在“输出”中输出，无法输入
    "code-runner.executorMap": {
        "c": "cd $dir && gcc '$fileName' -o '$fileNameWithoutExt.exe' -Wall -g -O2 -static-libgcc -std=c11 -fexec-charset=GBK && &'$dir$fileNameWithoutExt'",
        "cpp": "cd $dir && g++ '$fileName' -o '$fileNameWithoutExt.exe' -Wall -g -O2 -static-libgcc -std=c++17 -fexec-charset=GBK && &'$dir$fileNameWithoutExt'"
        // "c": "cd $dir && gcc $fileName -o $fileNameWithoutExt.exe -Wall -g -O2 -static-libgcc -std=c11 -fexec-charset=GBK && $dir$fileNameWithoutExt",
        // "cpp": "cd $dir && g++ $fileName -o $fileNameWithoutExt.exe -Wall -g -O2 -static-libgcc -std=c++17 -fexec-charset=GBK && $dir$fileNameWithoutExt"
    }, // 右键run code时运行的命令；未注释的仅适用于PowerShell（Win10默认），文件名中有空格也可以编译运行；注释掉的适用于cmd（win7默认），PS和bash也能用，但文件名中有空格时无法运行
    "code-runner.saveFileBeforeRun": true, // run code前保存
    "code-runner.preserveFocus": true,     // 若为false，run code后光标会聚焦到终端上。如果需要频繁输入数据可设为false
    "code-runner.clearPreviousOutput": false, // 每次run code前清空属于code runner的终端消息，默认false
    "code-runner.ignoreSelection": true,   // 默认为false，效果是鼠标选中一块代码后可以单独执行，但C是编译型语言，不适合这样用

    "C_Cpp.clang_format_sortIncludes": true, // 格式化时调整include的顺序（按字母排序）
}
```

### `c_cpp_properties.json`代码

如果确定不需要使用别人的库，则现在的版本（0.18.0之后）不需要创建这个文件了，`cpptools`会自动使用默认的设置。所以本文也不再包含此文件的配置。如果你自己编写了头文件又不在`workspaceFolder`下，或是使用别人的库，就需要手动创建这个文件放到`.vscode`文件夹下了。模板可以参考：[Microsoft/vscode-cpptools](https://code.visualstudio.com/docs/cpp/config-mingw)。一些曾经的经验：

- 库的路径要加到`includePath`和`browse`里
- 如果需要递归包含，末尾加`/**`。
- 这个json不允许有注释。
- `compilerPath`好像必需是MinGW的完整路径，精确到gcc.exe，否则会提示找不到头文件；Linux下是/usr/bin/gcc；
- Windows下的目录分隔符为反斜杠，原本应使用两个反斜杠来转义，但直接用斜杠这里也接受
- 除了配置这个文件，还需要进行别的操作。一部分可以参考下文的“多文件编译”

### 补充知识

JSON是一种数据交换格式，属于Javascript子集。在这里就是用作**配置**文件。VSCode和各个扩展会读取JSON中的条目，来决定某些功能和行为。

这么多条目哪里来的呢？这其实和API差不多。扩展开发者会把允许修改的选项“告诉”VSCode，各个扩展的安装页面都有写。作为使用者，输入的时候VSCode会提示你哪些是可用的，所以其实很容易写。

为什么要往JSON里写这么多的东西？因为VSCode本身并没有对C语言特别优待，对其他许多语言也一样。而且最关键的编译命令和VSCode是没有关系的，这就是上面提到过的编辑器和编译器的事。VSCode不负责、无法、不能编译C语言。

以`$`开头的是VSCode预定义的变量，具体参见：[Variables Reference](https://code.visualstudio.com/docs/editor/variables-reference)。比如`$file`在实际运行时会替换成当前打开的文件名。

## 多文件编译

如果想进行少量的多文件编译，C语言直接用`gcc 源文件1.c 源文件2.c 头文件1.h`这样就可以，C++用g++。默认生成`a.exe`，加`-o`可指定输出文件名，其余选项可以查看gcc使用教程。如果要多次编译可以写一个批处理。

如果想进行大量的多文件编译，请学习如何写`MakeFile`或使用`cmake`。然后把`tasks`的命令改成调用`make`等。

如果想使用别人的库，比如`ffmpeg`，可能需要在命令中指定`-I`、`-l`、`-L`。具体参数阅读那个库的文档。还可能需要把路径添加到`c_cpp_properties.json`里来配置智能补全等功能。这些情况下可以考虑单独建一个工作区，不要和单文件编译的共用。

## 清理临时文件

按照这样配置，长期编译代码下来肯定有一大堆的`.exe`，还可能分散在不同的文件夹里。可以考虑修改一下JSON文件，把生成文件的目录指定到一个专门放`.exe`的文件夹里；或者资源管理器右上角搜索`*.exe`然后手动删除；也可也写个`.bat`进行批处理，放到工作区里，要用的时候运行即可：

```bat
del *.exe /q /s
del tempCodeRunnerFile.c /q /s
del a.out /q /s
del *.o /q /s
```

## 关于中文和乱码

VSCode输出中文会出现乱码，很多人都遇到过。这是因为源代码默认是UTF-8编码，`cmd/PowerShell`是GBK编码。直接编译，会把“你好”输出成“浣犲ソ”。Linux就没有这个问题。

一种解决方法是用gcc，编译时用`-fexec-charset=GBK`这个参数（目前的配置是有的），生成的程序就是GBK编码的，源文件仍是UTF8。

如果是打开已有的以GBK编码的文件，VSCode默认会以UTF-8编码打开（除非你设置了猜测编码），这样编辑器内的中文就会乱码，此时要点右下角的GBK，选“通过编码重新打开”，选UTF-8即可。那为什么不打开自动猜测编码呢？可以参见知乎上的这个回答：[VSCode中文注释显示乱码怎么办？](https://www.zhihu.com/question/34415763/answer/596058039)。

## 找不到头文件的错误

例如下面这张图片所显示错误信息：

<div style="text-align: center;">
    <img src="https://github.com/SinestroEdmonce/SinestroEdmonce.github.io/raw/master/images/posts/error_cannot_find_header.jpg">
</div>

可能的原因和解决方法如下：

- gcc不在`PATH`里。回去看上面的**验证**那一步
- 手动配置了`c_cpp_properties.json`且包含的路径不正确。如果没有创建此文件就不用管
- 重启试试

## 参考

转载自[知乎文章](https://www.zhihu.com/question/30315894/answer/154979413)