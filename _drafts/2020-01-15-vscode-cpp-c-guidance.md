---
layout: post
title: 在VSCode中使用C或C++的流程和相关配置
categories: [VSCode, C/C++]
description: VSCode作为一款轻量级文本编辑器有极为强大的功能，可以作为多款语言的IDE来使用，但是想要将其作为C/C++开发的IDE工具，还需要一些配置和插件。
keywords: VSCode, C/C++
---

0. 前言本文面向初学者，每一步都比较详细。阅读本文能学习一些命令行、程序编译和调试，以及使用VS Code的知识。如果嫌本文说的麻烦，我给个精简版的：装gcc和c/c++扩展，打开文件夹，点开源代码，F1，build and debug active file，完。本文许多内容都可从VS Code官方文档：C++ programming with Visual Studio Code 以及各个扩展的文档中获得，并且他们还会进行更新（本文也进行过几次重大更新），如果你想更深入了解，可以去看。本文也基本上是由多次尝试得出来的，如果有错误可以指出。最终效果：实时显示编译阶段的错误、代码片段、补全、格式化、单文件的编译与调试。1. 环境的准备VSC的官网、下载、安装，我就不多说了。VSC只是一个纯文本编辑器(editor)，不是IDE(集成开发环境)，不含编译器(compiler)和许多其它功能，所以编译器要自己装好。下载编译器：MinGW-w64 - for 32 and 64 bit Windows 往下稍微翻一下，选最新版本中的x86_64-posix-seh。最好不要用 Download Latest Version，这个是在线安装包，可能因为国内的“网络环境”下载失败。如果浏览器下载失败就换迅雷下或者连手机开的热点下，还失败，那就使用能访问Google的那种方法下。“安装”编译器：下下来的是一个7z的压缩包。如果你不会解压可以百度“压缩包怎么解压”。解压完了放到一个不容易被删的地方，层叠的可以去掉一些。看好bin文件夹的完整路径，我图里的是C:\mingw64\bin，把它加到环境变量中的PATH里去。如果你不会这一步，看本文最后面的“B. 如何添加环境变量”（可以在本页用Ctrl+F搜索）Debian系Linux用sudo apt update; sudo apt install build-essential即可。<img src="https://pic4.zhimg.com/50/v2-5a148b7365261adeeb10da8b2f5e10e2_hd.jpg" data-caption="" data-size="normal" data-rawwidth="317" data-rawheight="378" data-default-watermark-src="https://pic4.zhimg.com/50/v2-b806dc7c91c37777bd226946de458c75_hd.jpg" class="content_image" width="317"/><img src="https://pic4.zhimg.com/50/v2-9113e35e8de23a0e5c30c2329ab0125f_hd.jpg" data-size="normal" data-rawwidth="667" data-rawheight="386" data-default-watermark-src="https://pic3.zhimg.com/50/v2-5f200d31da3155ee80ea0fd5df8f053d_hd.jpg" class="origin_image zh-lightbox-thumb" width="667" data-original="https://pic4.zhimg.com/v2-9113e35e8de23a0e5c30c2329ab0125f_r.jpg"/>顺序不重要；路径可以不一样，反正保证gcc.exe在那个文件夹里就行验证按Win+R，运行cmd（不要跳这一步），输入gcc，应该会提示 no input files 而不是“不是内部命令或外部命令”或者“无法将 "gcc" 项识别为 cmdlet、函数、脚本文件或可运行程序的名称”。如果是“不是内部命令或外部命令”，说明gcc在的文件夹没有在环境变量的Path中，要加进去才行。如果加了还是这样，重启。如果重启了还不行，那就是你自己进行的操作有问题。输gcc -v可以显示出gcc的版本。如果显示出来的版本与你刚下的不同/更老，说明Path里原本有老版本的编译器，可能是安装其它IDE时装上的。则需要去掉Path里原来的那一个gcc的路径。这两项验证一定要符合，否则必须修改环境变量。小心别错删了。<img src="https://pic1.zhimg.com/50/v2-09f3ba476bdede18e9a9ab36ae8d71f3_hd.jpg" data-size="normal" data-rawwidth="819" data-rawheight="351" class="origin_image zh-lightbox-thumb" width="819" data-original="https://pic1.zhimg.com/v2-09f3ba476bdede18e9a9ab36ae8d71f3_r.jpg"/>现在不用管clang，必定出错。clang的教程移到本文后面去了<img src="https://pic4.zhimg.com/50/v2-b63d57e63e10a991441b23ec45be86f6_hd.jpg" data-size="normal" data-rawwidth="711" data-rawheight="43" class="origin_image zh-lightbox-thumb" width="711" data-original="https://pic4.zhimg.com/v2-b63d57e63e10a991441b23ec45be86f6_r.jpg"/>输入gcc -v的最后一行输出。版本要和你自己下的对应，例如64位要有x86_64和seh安装扩展(extension)C/C++：又名 cpptools，提供Debug和Format功能Code Runner：右键即可编译运行单文件，很方便；但无法Dubug其他可选扩展：Bracket Pair Colorizer 2：彩虹花括号One Dark Pro：大概是VS Code安装量最高的主题不建议/不需要装的扩展：GBKtoUTF8：把GBK编码的文档转换成UTF8编码的。此扩展很久没有更新了，可能有严重的bugC++ Intellisense：用的是gtags，本文第一个版本的选择。效果非常非常一般Include Autocomplete：提供头文件名字的补全，现在cpptools和vscode-clangd都已经自带这个功能了，所以不用装C/C++ Snippets：Snippets即重用代码块，效果自己百度；这个扩展安装量虽高，不过个人感觉用处实在不大，cpptools和clangd也自带一些；你也可以选择其他的Snippets扩展甚至自己定义补充知识编译器是把源代码变成可执行文件的，编辑器是你打字的软件。记事本就是一个编辑器，VSC也是编辑器。编辑器是无法编译运行程序的，因为那是编译器的工作MinGW是gcc在Windows下的移植，gcc是世界上最流行的C/C++编译器组合。但gcc这个名字也指编译C语言的那个程序，g++才是C++编译器。即gcc程序和g++程序包含在gcc套件以及MinGW里，当只说gcc时要根据语境自己区分其实MinGW和MinGW-w64只是名字像，它们是两个不同的项目。为了方便，本文中的MinGW指的其实都是MinGW-w64。<del>MinGW本身已经很久没有更新了，不使用它</del>哎呀，原来MinGW是活着的，但它只能产生32位程序扩展是extension，插件是plugin，VSC用的是前者这种称呼。大部分文章都是混用两者的，不严谨但是能理解就行，要学会抓主要矛盾。当然本文用的都是正确的可选阅读：[原创][科普]MinGW vs MinGW-W64及其它、What is difference between sjlj vs dwarf vs seh?2. 配置几个.json文件创建一个你打算存放代码的文件夹，称作工作区文件夹；路径不能含有中文和引号，最好不要有空格，我用的是C:\VS-Code-C。C和C++需要分别建立不同的文件夹，除非用虚拟工作区。不要选上一段建出来的文件夹，源代码和编译器要分开放。打开VSC，选打开文件夹；最好不要选“添加工作区文件夹”，这个就是虚拟工作区，我没用过，不保证没问题。点新建文件夹，名称为.vscode。不在资源管理里新建的原因是Windows的Explorer不允许创建的文件夹第一个字符是点（1903后才支持）。然后创建 launch.json，tasks.json，settings.json（不是setting.json） 放到.vscode文件夹下。效果图：<img src="https://pic4.zhimg.com/50/v2-5525fd32ffff65fab4977715ee49f067_hd.jpg" data-size="normal" data-rawwidth="341" data-rawheight="196" data-default-watermark-src="https://pic3.zhimg.com/50/v2-6aedeb779acabeff2812d5716de02031_hd.jpg" class="content_image" width="341"/>一定要在.vscode里，别变成平行的了这几个文件的内容见下。复制以下代码出来后，知乎会自动在前面加上几行保留所有权利的字，实际使用的时候肯定要删了的。有些地方可选修改，自己对照着注释看吧。注意：如果是写C++，tasks.json的一个地方必须要修改。launch.json代码externalConsole可根据自己喜好修改；cwd可以是程序运行时的相对路径，如有需要可以改为${fileDirname}（感谢 @xhx）。lldb我没用过就不多说了。type和request不变色是正常现象。// https://github.com/Microsoft/vscode-cpptools/blob/master/launch.md
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
}tasks.json代码如果是编写C++，编译器需改成g++；如果不想要额外警告，把-Wall那一条删去；-std根据自己需要修改；Linux下不需要加-fexec-charset。反正这些我都加了注释，还看不懂，百度gcc使用教程。reveal控制编译时是否跳转到终端面板。可根据自己喜好修改；即使设为never，也只是不自动跳转，手动点进去还是可以看到信息。// https://code.visualstudio.com/docs/editor/tasks
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
}settings.json代码把这个文件里的东西放到“用户设置”里可以覆盖全局设置，否则只在当前工作区才有效。这两点各有自己的优势。Code Runner的命令行和某些选项可以根据自己的需要在此处修改，想自定义或者想知道是什么意思还是参见此扩展的文档和百度gcc使用教程。如果终端用的是cmd（Win7默认）需要改用注释掉的，或者把terminal.integrated.shell.windows改为PowerShell；Win10默认就是PS就不用改。感谢 @Wellin Boss 提到的snippetSuggestions；不过用top有时还是有点问题的，所以改成可选。{
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
}c_cpp_properties.json如果你确定不需要使用别人的库，则现在的版本（0.18.0之后）不需要创建这个文件了，cpptools会自动使用默认的设置。所以本文也不再包含此文件的配置。如果你自己编写了头文件又不在workspaceFolder下，或是使用别人的库，就需要手动创建这个文件放到.vscode下了。模板可以参考：Microsoft/vscode-cpptools。一些曾经的经验：库的路径要加到includePath和browse里如果需要递归包含，末尾加/**。这个json不允许有注释，其实按照json标准本来就不能有compilerPath好像必需是MinGW的完整路径，精确到gcc.exe，否则会提示找不到头文件；Linux下是/usr/bin/gcc；但我很久没有测试过了Windows下的目录分隔符为反斜杠，原本应使用两个反斜杠来转义，但直接用斜杠这里也接受除了配置这个文件，还需要进行别的操作。一部分可以参考下文的“多文件编译”补充知识json是一种数据交换格式，<del>大部分是JavaScript的子集</del>现在变成完全子集了。在这里就是用作配置文件。VSC和各个扩展会读取json中的条目，来决定某些功能和行为。这么多条目哪里来的呢？这其实和API差不多。扩展开发者会把允许修改的选项“告诉”VSC，各个扩展的安装页面都有写。作为使用者，输入的时候VSC会提示你哪些是可用的，所以其实很容易写。为什么要往json里写这么多的东西？因为VSC本身并没有对C语言特别优待，对其他许多语言也一样。而且最关键的编译命令和VSC是没有关系的，这就是上面提到过的编辑器和编译器的事。VSC不负责、无法、不能编译C语言。以$开头的是VSC预定义的变量，具体参见：Variables Reference。比如$file在实际运行时会替换成当前打开的文件名。3. 写代码，编译，调试新建文件后就可以写代码了，c语言源代码后缀是.c，c++是.cpp或.C或.cxx（这也要我教吗……）。代码文件在保存工作区内都可以，可以自己建立文件夹，不必放到.vscode文件夹里，但路径里(包括文件名)不要含有中文和引号，最好不要有空格。主要是许多符号是有效的shell语法，不然试试Linux下用rm删除一个叫做-rf的文件？没查过绝对写不出来。按Alt+Shift+F（或者用右键菜单）可以格式化代码，出现Intellisense的时候按tab可以补全代码。打出snippets时会出现多个跳转点，按tab可以跳到下一个去。停止输入一小段时间（一秒）后就会有Lint，扩展会给一些建议性的warning（比如声明了变量但不使用），自己清楚就行。如果觉得不爽，也有方法不让它提示，比如去掉-Wall就会少一些。如果还想去掉更多的警告，我提示一下：-Wno-...。找好参数后可以用#pragma GCC diagnostic ignored或者加到各种Flags里。总之自己研究。接下来说说运行的事。首先，编译是从源代码生成可执行文件的过程。而调试其实是一种特殊的运行，是能控制程序运行，方便之后修改的一种手段。这是两个不同的阶段，可能出现编译通过但调试失败，也可能直接编译就失败，还有可能编译还没开始就失败了。如果你只说“运行失败”，别人是看不出是哪个阶段出了问题的。如果确定某个阶段通过了，那就不用管那个阶段了，就能专注于解决别的阶段的问题。按Ctrl+Shift+B单纯编译，按F5为编译加调试；本来ctrl+F5为运行但不调试，但现在cpptools暂不支持，还是会调试。Follow: Support "Run without debugging" · Issue #1201 · microsoft/vscode-cpptools在写程序初期，我强烈建议不要把f5当作编译来使用，因为有的bug只会产生警告，不会阻止编译，但这些东西越早期解决越好。编译信息会在底下的“终端”面板里，如果代码有错误，点进去可以看编译器报的信息；不过因为有Lint了，平常的错误可以马上被发现和修改，写代码就轻松很多。加断点在列号前面点一下就行，右键可以加条件断点。如果想从一开始就停下来，可以加在main函数那里，或者launch.json中有个设置。开始调试后，按f11可以一步一步进行，箭头所指的那行代码就是下一步要运行的代码；f5是一直运行到下一个断点，右键某一行代码可以选择一直运行到指定的那一行。左边有个调试栏，可以看到变量的值，自动栏没有的可以手动添加：在代码里选中要监视的表达式，点右键有选项可以直接添加到Watch里，复杂的才需要手打。把鼠标放到变量上可以看到变量的值，但是只能识别简单的表达式。栈帧对于观察递归很有用。栈溢出和段错误时还可以抓取“异常”，自动跳转到出错的行。特别的，对于数组：C语言的数组经过函数传递以后会退化为指针，直接添加表达式就只能看到第一个元素。此时可以强制转换成指向固定大小的数组指针再解引：例如int arr[10]传进函数里后就变成了int* arr，在Watch里添加*(int(*)[10])arr，这样就能看到完整的数组了。但长度必须是写死的，自己小心越界。或者简单的程序用全局变量数组就能一直看到了。另一种只对gdb且是非void*有效的写法：*arr@10。快捷键：vscode: Visual Studio Code 常用快捷键 - 志文工作室。英文文档中当然有快捷键的说明，还有Cheet Sheet可以看，而且英文文档会更新。这个单独列出来仅给初学者。如果遇到错误，先看底下的“某些可能出现的错误”以及看评论区。Code Runner如果你不需要调试，可以直接右键选run code，或者点右上角的播放按钮。如果在终端里运行，可以输入数据，但是少了显示时间的功能；在“输出”中则上面两项相反。在终端中按Ctrl + C可以终止程序运行，下一次运行前必须保证当前程序已经终止了（对于task也是一样的）。如果你想要复制，选中内容后直接按一下右键就可以了；粘贴则是在未选中时按右键；这个操作仅限于Win10，ctrl+c也可以复制但可能一不小心就把程序终止了。用它还可以在非工作区内编译运行程序，不过默认用的是gcc，除非把executorMap放到全局设置里。按照我的配置，task和Code Runner还有一点不同：working directory。前者是你打开的文件夹，后者是文件所在的文件夹。当然它们也都可以自己修改。其实Code Runner只是代替你手动输命令，功能并不强，算是适用场景不同吧。不要以为run code跑个Hello World很简单，Code Runner就很强、前面那么多配置都是垃圾了。另外，楼下的答主韩骏就是此扩展作者，有事统统找他（滑稽）。多文件编译如果你想进行少量的多文件编译，C语言直接用gcc 源文件1.c 源文件2.c 头文件1.h这样就好，C++用g++。默认生成a.exe，加-o可指定输出文件名，其余选项百度gcc使用教程。如果需要多次编译可以写一个批处理。 如果你想进行大量的多文件编译，请学习如何写makefile或使用cmake。然后把tasks的命令改成调用make等。如果你想使用别人的库，比如ffmpeg，可能需要在命令中指定-I、-l（小写的L）、-L。具体参数阅读那个库的文档。还可能需要把路径添加到c_cpp_properties.json里来配置Intellisense。这些情况下可以考虑单独建一个工作区，不要和单文件编译的共用。其实不新建工程(Project)、只是单文件就能调试，是不利于以后使用和理解大型IDE的。不过初学也不用掌握那么多，不要觉得建工程很麻烦、不建工程就能编译很强就是了。总之这些和VSC无关，用其它IDE或是手动编译也会遇到差不多的问题，也有点复杂。本文就不多讨论这些了，自行解决。保存文件夹以后写代码必须打开之前那个建立好的文件夹才能写，否则所有的Intellisense都没有，只有Code Runner能用。（主要是需要那四个json，新建其它文件夹需把那几个json复制过去就也能用）可以创建一个快捷方式（右键新建），把工作区路径作为参数传给VSC主程序，记得打双引号；还可以加个图标。1.18有了真正的虚拟工作区，可以一个窗口包含多个不在一起的文件夹，“文件”菜单里也有“保存工作区”这个功能，但是我都没试过，不保证没问题。<img src="https://pic4.zhimg.com/50/v2-ef8da0bfd7f5e03cfd9ed0354c7c19ec_hd.jpg" data-caption="" data-size="normal" data-rawwidth="542" data-rawheight="316" data-default-watermark-src="https://pic1.zhimg.com/50/v2-670bee049e796527501ab8e1dde75a8f_hd.jpg" class="origin_image zh-lightbox-thumb" width="542" data-original="https://pic4.zhimg.com/v2-ef8da0bfd7f5e03cfd9ed0354c7c19ec_r.jpg"/>清理临时文件按照这样配置，长期编译代码下来肯定有一大堆的exe，还可能分散在不同的文件夹里。可以考虑修改一下json文件，把生成文件的目录指定到一个专门放exe的文件夹里；如果不会，百度gcc使用教程以及看我的json里的注释。或者资源管理器右上角搜索*.exe然后手动删除。也可也写个bat，放到工作区里，要用的时候右键Run Code：del *.exe /q /s
del tempCodeRunnerFile.c /q /s
del a.out /q /s
del *.o /q /s添加纯英文输入法Windows 10，默认输入法只有一个微软拼音，按一次shift就能进行中英转换；为了保持兼容，按ctrl加空格也能进行中英转换，但这个快捷键正是强制触发Intellisense的快捷键。所以，我强烈建议手动添加“英语”语言输入法，正常代码时切换到纯英文输入法（win+空格），在需要频繁中文注释或者在字符串里写中文时才用中文输入法的英文模式。这样也可以解决某些游戏需要用到shift键但同样快捷键冲突的问题。具体操作可以自己百度，也可以看我写的这篇有点复杂的文章：Windows 切换显示语言与添加纯英文输入法。某些可能出现的错误为了阅读的连贯性，这一部分移到了“A. 一些其它可能出现的错误”。遇到问题优先查看那里是否已经提了。4. 其他设置我的一些其他的设置，用在全局settings.json里，根据自己的情况调整，不需要全部照着我的写。写完一个以后要打逗号；最外面的那个大括号我没加，就别弄丢了。现在的VSC用的是可视化的设置界面，其实原本是手动编辑且出现两列设置的。点击右上角那个花括号就能手动编辑。"editor.fontFamily": "等距更纱黑体 SC", // 控制编辑器字体
"editor.fontSize": 16, // 同上
"editor.fontLigatures": true, // 连体字，效果不太好形容，见 https://typeof.net/Iosevka 最后一部分
"editor.minimap.enabled": false, // 我个人不用minimap，就是右边那个东西
"editor.dragAndDrop": false, // 选中文字后，可以拖动它们调整位置。我是不需要
"editor.cursorSmoothCaretAnimation": true, // 移动光标时变得平滑
"editor.smoothScrolling": true, // 滚动平滑，不过效果很微弱
 
"files.trimTrailingWhitespace": true, // 保存时，删除每一行末尾的空格
"files.insertFinalNewline": true, // 保存后文件最末尾加一整行空行，Linux下的习惯
"files.autoGuessEncoding": false, // 启用后，会在打开文件时尝试猜测字符集编码。我关闭的理由见6，默认也是禁用的

"workbench.colorTheme": "One Dark Pro", // 主题
"workbench.colorCustomizations": {
      "activityBar.foreground": "#39C5BB" // 自定义颜色；想定义其它位置参见官方文档
},
"workbench.settings.useSplitJSON": true, // 恢复手动编辑时的两列设置
"window.zoomLevel": 0.2, // 整体放大

"git.enabled": false, // 如果你不用git，可以考虑关闭它
"git.ignoreMissingGitWarning": true, // 同上

"[c]": {
    // "files.encoding": "gbk" // 这样的格式可以对指定后缀的文件应用设置，如果你实在想用gbk，就这样设置吧。cpp同理。
},更纱黑体是楼下B神做的字体，特点是标点好看（误）：be5invis/Sarasa-GothicConsolas虽然是Windows自带字体中还算行的，但它只有英文字体；微软雅黑虽然是非衬线字体，但它不是等距的，这一点非常不适合编程，等线也不等距；中易宋体……告辞。不下新的字体，其他两大系统我不清楚，Windows下简直没有编程可用的字体。Consolas加雅黑嘛，也还行吧，不过能用更好的干嘛不用呢。6. 关于中文和乱码VS Code输出中文会出现乱码，很多人都遇到过。这是因为源代码默认是UTF-8编码，cmd/PowerShell是GBK编码。直接编译，会把“你好”输出成“浣犲ソ”。Linux就没有这个问题。一种解决方法是用gcc，编译时用-fexec-charset=GBK这个参数（目前的配置是有的），生成的程序就是GBK编码的，源文件仍是UTF8。而clang的execution-charset supports only UTF-8，所以用clang就无解。另一种方法是用宽字符输出，有点复杂，见：C语言与中文的一些测试 (Win, UTF8源码) 。此文也提到了chcp 65001的事。直接修改非Unicode程序的语言为UTF8(beta)会导致所有用GBK的程序乱码，这是不可接受的。当然，如果你不打算坚持用UTF8作为源代码的编码，那直接用GBK编码也行。如果是打开已有的以GBK编码的文件，VS Code默认会以UTF-8编码打开（除非你设置了猜测编码），这样编辑器内的中文就会乱码，此时要点右下角的GBK，选“通过编码重新打开”，选UTF-8即可。那为什么不打开自动猜测编码呢？可以参见我的这个回答：VS Code 中文注释显示乱码怎么办？。如果你不担心，那就开吧。如果把代码文件发给其他用Windows的人，最好转成GBK，否则别人用记事本打开有可能会乱码（1803后的记事本改善了一些，联通已经不会乱码了）。对于调试，无论怎么做，gdb都无法调试路径中存在中文的程序。这个貌似是gdb的bug，但是优先级极低：[gdb] cannot not open source file with Chinese/Unicode characters in path when debugging · Issue #602 · microsoft/vscode-cpptools总之，对于Windows，这些问题没什么好办法，因为本文用的这一套就是从Linux搬过来的。用Linux应该就没有这些问题了。7. 找不到头文件的错误<img src="https://pic4.zhimg.com/50/v2-877058be4208e6b3648a1da6cec6205e_hd.jpg" data-caption="" data-size="normal" data-rawwidth="334" data-rawheight="93" class="content_image" width="334"/>gcc不在Path里。回去看上面的验证那一步手动配置了c_cpp_properties.json且包含的路径不正确。如果没有创建此文件就不用管重启试试如果你保证这几点都符合要求，那我也没什么好办法……要不就换其它答主的教程吧。注意这句话是终极Fallback，如果你确信你没有操作错误，那就不用问我了，我是无法解决的。

转载自[知乎文章](https://www.zhihu.com/question/30315894/answer/154979413)