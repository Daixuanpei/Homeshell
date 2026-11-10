
### 如果系统中描述与此文件不符，均以此文档为准。
# HomeShell · 简易终端文件管理器

这是一个只用 Python 标准库编写的简易文件管理系统，放在 `home` 目录下。
双击 **`启动终端.bat`** 后，会打开终端窗口，自动显示 `home` 的当前目录和文件列表。

除了浏览、创建、打开和删除文件，它还支持在终端里编译 C++、运行 Python 脚本（前提是确保你已经有g++编译器及python解释器，同时将路径添加到环境变量Path）。
所有文件操作都被限制在 `home` 目录内，输入 `..`、绝对路径或符号链接也无法跳出这个范围。

## 快速开始

1. 双击 `启动终端.bat`。
2. 在提示符 `~/ >` 后输入命令，例如：

   ```text
   ls
   mkdir 项目
   cd 项目
   write 笔记.txt 今天开始使用 HomeShell
   open 笔记.txt
   cd ..

   build 示例代码/hello.cpp
   run 示例代码/hello.cpp 张三
   run 示例代码/hello.py 李四
   ```

3. 输入 `help` 查看全部命令，输入 `exit` 退出。

如果双击没有反应，可以在 `home` 目录中打开 PowerShell，执行：

```powershell
.\启动终端.bat
```

> 启动脚本优先使用 `bin/Python312/python.exe`，找不到时再尝试 `python`、`py -3`
> 和 DSH 自带的 Python。

## 编译与运行

### C++

```text
build 示例代码/hello.cpp
run 示例代码/hello.cpp 张三
```

- `build` 使用 `bin/g++.exe` 编译，在同目录生成 `hello.exe`。
- 编译参数为 C++17、`-O2`、UTF-8 源文件，并在 Windows 下使用静态链接。
- `run` 会先编译，再运行生成的 exe；也可以直接 `run 示例代码/hello.exe`。
- 编译错误会直接显示在终端里，例如缺少分号、头文件不存在等。

### Python

```text
build 示例代码/hello.py
run 示例代码/hello.py 李四
py 示例代码/hello.py 王五
```

- `build` 使用 `python -m py_compile` 做语法编译，在 `__pycache__` 中生成 `.pyc`。
- `run` / `py` 直接运行 Python 脚本，脚本参数写在文件名后面。
- Python 默认使用 `bin/Python312/python.exe`。旧的 0 字节 `bin/python3`
  不会被使用，`tools` 命令会显示真正的 Python 路径。

### 查看工具链

```text
tools
```

会显示当前使用的 `g++`、MinGW 工具链和 Python 路径。

`bin/g++.exe` 是 Dev-C++/MSYS2 工具链的一个硬链接。单独复制 `g++.exe`
并不足以编译，因为它还需要 `cc1plus`、头文件和库。HomeShell 会自动通过
硬链接来源或常见安装位置寻找完整工具链；如果自动定位失败，可以设置环境变量：

```powershell
$env:HOMESHELL_GCC_ROOT = "C:\Dev-Cpp\msys2\ucrt64"
```

## 启用子系统

`.fileshell/children_home` 是子系统的根目录，里面放有 `mini_fs` 项目。
在 HomeShell 中输入：

```text
启用
```

会启动子系统的主程序：

```text
.fileshell/children_home/mini_fs/src/main.exe
```

启动时的工作目录是 `children_home`，所以子系统的文件视图从该目录开始。
`main.exe` 退出后会回到 HomeShell。

## 命令一览

| 功能 | 命令 | 说明 |
| --- | --- | --- |
| 列出 | `ls` / `ls -a` | 列出当前目录，`-a` 显示隐藏项 |
| 进入 | `cd 目录` | 进入文件夹；`cd ..` 返回上级；`cd ~` 回到 home 根目录；`cd -` 返回上次目录 |
| 当前位置 | `pwd` | 显示相对路径和完整路径 |
| 打开 | `open 目录或文件` | 文件夹会进入并列出内容；文本文件会直接预览 |
| 目录树 | `tree` / `tree 2` | 显示当前目录的树形结构 |
| 编译 C++ | `build 文件.cpp` | 生成同目录下的 exe |
| 编译 Python | `build 文件.py` | 生成 `__pycache__` 字节码 |
| 运行 | `run 文件 [参数...]` | 编译并运行 C++、运行 Python 或 exe |
| 运行 Python | `py 文件.py [参数...]` | 直接用 Python 运行脚本 |
| 工具链 | `tools` | 显示 g++ / MinGW / Python 路径 |
| 启用子系统 | `启用` | 启动 `children_home` 中的 `main.exe` |
| 新建文件夹 | `mkdir 名称` | 支持多级，例如 `mkdir 项目/资料` |
| 新建文件 | `touch 名称` | 新建空文件；文件已存在时更新时间 |
| 写入文本 | `write 文件 内容` | 新建或覆盖 UTF-8 文本文件；内容有空格时加引号 |
| 重命名 | `rename 旧名 新名` | 重命名文件或文件夹 |
| 删除文件 | `rm 文件` | 删除前会确认 |
| 删除文件夹 | `rm -r 文件夹` | 递归删除，删除前会确认 |
| 强制删除 | `rm -rf 路径` | 跳过确认，请谨慎使用 |
| 删除空文件夹 | `rmdir 文件夹` | 仅能删除空文件夹 |
| 系统打开 | `start` / `start 文件` | 用资源管理器或系统默认程序打开 |
| 清屏 | `clear` | 清空终端 |
| 帮助 | `help` | 显示命令帮助 |
| 退出 | `exit` | 退出程序 |

也可以使用中文命令：`列表`、`进入`、`打开`、`编译`、`运行`、
`新建文件夹`、`新建文件`、`删除`、`重命名`、`退出`。

## 目录结构

```text
home/
├── 启动终端.bat          # 双击启动入口
├── 使用说明.md           # 本文档
└── .fileshell/           # 程序文件（系统目录，请勿随意删除）
    ├── fileshell.py      # Python 核心主程序
    ├── children_home_cpp/  # C++ 子系统目录
    │   └── main.cpp / main.exe
    └── children_home_rust/ # Rust 子系统目录
        └── src/main.rs / main.exe
```

`home` 是本系统的根目录，也是唯一允许操作的位置。可以在这里自由创建、
打开、重命名和删除自己的文件或文件夹；`.fileshell` 是程序目录，
`启动终端.bat` 是启动文件，对它们的删除或重命名会被拒绝。

## 小提示

- 名称中有空格时使用引号：`mkdir "我的 项目"` 或 `run "示例代码/我的 程序.py"`。
- 提示符会显示当前所在位置，例如 `~/示例代码 >`。
- 终端启动时会显示当前目录并自动列出内容，不需要先输入 `ls`。
- 编译生成的 exe 位于源文件所在目录；被运行程序的工作目录是当前终端目录。
- 文本预览最多显示前 200 行 / 64 KB；更大的文件可以用 `start` 交给系统程序打开。
- 删除操作默认逐个确认，输入 `y` 才会真正删除。
