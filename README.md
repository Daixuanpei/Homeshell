# ------------------简体中文------------------
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

# ------------------English------------------
# HomeShell · Simple Terminal File Manager
This is a simple file management system written using only Python standard libraries, placed in the `home` directory.
Double-click **`Start Terminal.bat`** to open a terminal window, which automatically shows the current directory and file list of `home`.

In addition to browsing, creating, opening and deleting files, it also supports compiling C++ and running Python scripts in the terminal (provided that you have the g++ compiler and Python interpreter installed, and their paths are added to the system Path environment variable).
All file operations are restricted within the `home` directory. Entering `..`, absolute paths or symbolic links cannot escape this scope.

## Quick Start
1. Double-click `Start Terminal.bat`.
2. Enter commands after the prompt `~/ >`, for example:
ls
mkdir Project
cd Project
write note.txt I started using HomeShell today
open note.txt
cd ..

build demo/hello.cpp
run demo/hello.cpp ZhangSan
run demo/hello.py LiSi
3. Enter `help` to view all commands, enter `exit` to quit.

If nothing happens after double-clicking, open PowerShell in the `home` directory and run:
.\Start Terminal.bat

> The startup script prioritizes `bin/Python312/python.exe`. If it cannot be found, it will try `python`, `py -3` and the Python bundled with DSH in sequence.

## Compile & Run
### C++
build demo/hello.cpp
run demo/hello.cpp ZhangSan
- `build` uses `bin/g++.exe` for compilation and generates `hello.exe` in the same directory.
- Compilation flags: C++17, `-O2`, UTF-8 source files, static linking on Windows.
- `run` will compile first and then execute the generated exe; you can also run `run demo/hello.exe` directly.
- Compilation errors are displayed directly in the terminal, such as missing semicolons or missing header files.

### Python
build demo/hello.py
run demo/hello.py LiSi
py demo/hello.py WangWu
- `build` uses `python -m py_compile` for syntax compilation and generates `.pyc` in `__pycache__`.
- `run` / `py` runs Python scripts directly. Script arguments are written after the filename.
- Python uses `bin/Python312/python.exe` by default. The old 0-byte `bin/python3` will not be used. The `tools` command shows the actual Python path.

### View Toolchain
tools
Shows the current paths of `g++`, MinGW toolchain and Python.

`bin/g++.exe` is a hard link to the Dev-C++/MSYS2 toolchain. Copying `g++.exe` alone is not enough for compilation, since it also requires `cc1plus`, header files and libraries. HomeShell automatically locates the full toolchain from the hard link source or common install locations. If auto-detection fails, set the environment variable:
$env:HOMESHELL_GCC_ROOT = "C:\Dev-Cpp\msys2\ucrt64"

## Enable Subsystem
`.fileshell/children_home` is the root directory of the subsystem, containing the `mini_fs` project.
Inside HomeShell, enter:
enable
This launches the main program of the subsystem:
.fileshell/children_home/mini_fs/src/main.exe
The working directory on startup is `children_home`, so the subsystem’s file view starts from this directory.
After `main.exe` exits, you return to HomeShell.

## Command Reference
| Function | Command | Description |
| --- | --- | --- |
| List | `ls` / `ls -a` | List current directory; `-a` shows hidden items |
| Enter | `cd dir` | Enter folder; `cd ..` go back; `cd ~` return to home root; `cd -` go back to last directory |
| Show location | `pwd` | Show relative path and full absolute path |
| Open | `open dir-or-file` | Folders enter and list contents; text files preview directly |
| Directory tree | `tree` / `tree 2` | Show tree structure of current directory |
| Compile C++ | `build file.cpp` | Generate exe in the same folder |
| Compile Python | `build file.py` | Generate bytecode in `__pycache__` |
| Run | `run file [args...]` | Compile and run C++, run Python or exe |
| Run Python | `py file.py [args...]` | Run Python script directly |
| Toolchain info | `tools` | Show g++ / MinGW / Python paths |
| Enable subsystem | `enable` | Launch `main.exe` inside children_home |
| Make folder | `mkdir name` | Supports nested paths e.g. `mkdir Project/Docs` |
| Make file | `touch name` | Create empty file; update timestamp if exists |
| Write text | `write file content` | Create or overwrite UTF-8 text file; quote content with spaces |
| Rename | `rename old new` | Rename file or folder |
| Delete file | `rm file` | Confirm before deletion |
| Delete folder | `rm -r folder` | Recursive delete, confirm before deletion |
| Force delete | `rm -rf path` | Skip confirmation, use carefully |
| Remove empty folder | `rmdir folder` | Only deletes empty directories |
| Open with system | `start` / `start file` | Open with explorer or default system program |
| Clear screen | `clear` | Clear terminal screen |
| Help | `help` | Show command help |
| Exit | `exit` | Exit the program |

Chinese aliases are also available: `列表`, `进入`, `打开`, `编译`, `运行`, `新建文件夹`, `新建文件`, `删除`, `重命名`, `退出`.

## Directory Structure
```text
home/
├── Start Terminal.bat          # Double-click startup entry
├── README.md                  # This document
└── .fileshell/                # Program files (system directory, do not delete)
    ├── fileshell.py           # Python core main program
    ├── children_home_cpp/      # C++ subsystem directory
    │   └── main.cpp / main.exe
    └── children_home_rust/     # Rust subsystem directory
        └── src/main.rs / main.exe
```
`home` is the root of this system and the only writable location. You may freely create, open, rename and delete your files and folders here. `.fileshell` is the program directory, and `Start Terminal.bat` is the launcher; deletion or renaming of these will be rejected.

## Tips
- Use quotes for names with spaces: `mkdir "My Project"` or `run "demo/My Program.py"`.
- The prompt shows your current location, e.g. `~/demo >`.
- The terminal automatically lists contents on startup, no need to run `ls` first.
- Generated exe files are placed beside the source file; the running program inherits the terminal’s current working directory.
- Text preview is limited to the first 200 lines / 64 KB. Larger files can be opened with `start` using your system editor.
- Delete operations require confirmation by default; type `y` to confirm deletion.

# ------------------Français------------------
# HomeShell · Gestionnaire de fichiers de terminal simple
Il s’agit d’un système de gestion de fichiers simple écrit uniquement avec les bibliothèques standard Python, situé dans le répertoire `home`.
Double-cliquez sur **`Lancer le terminal.bat`** pour ouvrir une fenêtre de terminal qui affiche automatiquement le répertoire courant et la liste des fichiers de `home`.

En plus de parcourir, créer, ouvrir et supprimer des fichiers, il permet de compiler du C++ et d’exécuter des scripts Python dans le terminal (à condition que le compilateur g++ et l’interpréteur Python soient installés et que leurs chemins soient ajoutés à la variable d’environnement Path).
Toutes les opérations sur les fichiers sont limitées au répertoire `home`. Saisir `..`, des chemins absolus ou des liens symboliques ne permet pas de sortir de cette zone.

## Démarrage rapide
1. Double-cliquez sur `Lancer le terminal.bat`.
2. Saisissez des commandes après l’invite `~/ >`, par exemple :
ls
mkdir Projet
cd Projet
write note.txt J’ai commencé à utiliser HomeShell aujourd’hui
open note.txt
cd ..

build exemple/hello.cpp
run exemple/hello.cpp ZhangSan
run exemple/hello.py LiSi
3. Saisissez `help` pour afficher toutes les commandes, `exit` pour quitter.

Si rien ne se passe après le double-clic, ouvrez PowerShell dans le répertoire `home` et exécutez :
.\Lancer le terminal.bat

> Le script de lancement utilise en priorité `bin/Python312/python.exe`. S’il ne le trouve pas, il essaie successivement `python`, `py -3` et le Python fourni avec DSH.

## Compilation et exécution
### C++
build exemple/hello.cpp
run exemple/hello.cpp ZhangSan
- `build` utilise `bin/g++.exe` pour compiler et génère `hello.exe` dans le même dossier.
- Options de compilation : C++17, `-O2`, fichiers sources UTF-8, liaison statique sur Windows.
- `run` compile d’abord puis exécute l’exécutable généré ; vous pouvez aussi lancer directement `run exemple/hello.exe`.
- Les erreurs de compilation s’affichent directement dans le terminal, par exemple point-virgule manquant ou fichier d’en-tête introuvable.

### Python
build exemple/hello.py
run exemple/hello.py LiSi
py exemple/hello.py WangWu
- `build` utilise `python -m py_compile` pour la compilation syntaxique et génère des fichiers `.pyc` dans `__pycache__`.
- `run` / `py` exécute directement les scripts Python. Les arguments du script s’écrivent après le nom du fichier.
- Python utilise par défaut `bin/Python312/python.exe`. L’ancien `bin/python3` de 0 octet n’est pas utilisé. La commande `tools` affiche le vrai chemin de Python.

### Afficher la chaîne d’outils
tools
Affiche les chemins actuels de `g++`, de la chaîne d’outils MinGW et de Python.

`bin/g++.exe` est un lien physique vers la chaîne d’outils Dev-C++/MSYS2. Copier seulement `g++.exe` ne suffit pas pour compiler, car il a besoin aussi de `cc1plus`, des fichiers d’en-tête et des bibliothèques. HomeShell cherche automatiquement la chaîne complète depuis la source du lien physique ou les emplacements d’installation courants. Si la détection automatique échoue, définissez la variable d’environnement :
$env:HOMESHELL_GCC_ROOT = "C:\Dev-Cpp\msys2\ucrt64"

## Activer le sous-système
`.fileshell/children_home` est le répertoire racine du sous-système, il contient le projet `mini_fs`.
Dans HomeShell, saisissez :
activer
Cela lance le programme principal du sous-système :
.fileshell/children_home/mini_fs/src/main.exe
Le répertoire de travail au démarrage est `children_home`, donc la vue des fichiers du sous-système commence à cet endroit.
Quand `main.exe` se termine, vous revenez à HomeShell.

## Liste des commandes
| Fonction | Commande | Description |
| --- | --- | --- |
| Lister | `ls` / `ls -a` | Lister le répertoire courant ; `-a` affiche les éléments cachés |
| Entrer | `cd dossier` | Ouvrir un dossier ; `cd ..` revenir en arrière ; `cd ~` retourner à la racine home ; `cd -` revenir au dernier dossier |
| Afficher l’emplacement | `pwd` | Afficher le chemin relatif et le chemin absolu complet |
| Ouvrir | `open dossier-ou-fichier` | Les dossiers sont ouverts et listés ; les fichiers texte sont prévisualisés |
| Arborescence | `tree` / `tree 2` | Afficher l’arborescence du répertoire courant |
| Compiler C++ | `build fichier.cpp` | Générer un exe dans le même dossier |
| Compiler Python | `build fichier.py` | Générer du code octet dans `__pycache__` |
| Exécuter | `run fichier [arguments...]` | Compiler et lancer du C++, lancer du Python ou un exe |
| Lancer Python | `py fichier.py [arguments...]` | Exécuter directement un script Python |
| Chaîne d’outils | `tools` | Afficher les chemins g++ / MinGW / Python |
| Activer sous-système | `activer` | Lancer `main.exe` dans children_home |
| Créer dossier | `mkdir nom` | Prend en charge les chemins imbriqués, exemple `mkdir Projet/Documents` |
| Créer fichier | `touch nom` | Créer un fichier vide ; met à jour l’horodatage s’il existe déjà |
| Écrire texte | `write fichier contenu` | Créer ou écraser un fichier texte UTF-8 ; mettre le contenu entre guillemets s’il contient des espaces |
| Renommer | `rename ancien nouveau` | Renommer un fichier ou dossier |
| Supprimer fichier | `rm fichier` | Demande confirmation avant suppression |
| Supprimer dossier | `rm -r dossier` | Suppression récursive, demande confirmation |
| Supprimer forcé | `rm -rf chemin` | Sans confirmation, utilisez avec prudence |
| Supprimer dossier vide | `rmdir dossier` | Ne supprime que les dossiers vides |
| Ouvrir avec le système | `start` / `start fichier` | Ouvrir avec l’explorateur ou le programme par défaut |
| Effacer l’écran | `clear` | Effacer l’écran du terminal |
| Aide | `help` | Afficher l’aide des commandes |
| Quitter | `exit` | Quitter le programme |

Des alias en chinois sont aussi disponibles : `列表`, `进入`, `打开`, `编译`, `运行`, `新建文件夹`, `新建文件`, `删除`, `重命名`, `退出`.

## Structure des répertoires
```text
home/
├── Lancer le terminal.bat          # Point d’entrée au double-clic
├── README.md                       # Ce document
└── .fileshell/                     # Fichiers du programme (dossier système, ne pas supprimer)
    ├── fileshell.py                # Programme principal Python
    ├── children_home_cpp/          # Dossier du sous-système C++
    │   └── main.cpp / main.exe
    └── children_home_rust/         # Dossier du sous-système Rust
        └── src/main.rs / main.exe
```
`home` est la racine de ce système et le seul emplacement modifiable. Vous pouvez librement créer, ouvrir, renommer et supprimer vos fichiers et dossiers ici. `.fileshell` est le dossier du programme, `Lancer le terminal.bat` est le lanceur ; leur suppression ou renommage sera refusé.

## Petits conseils
- Utilisez des guillemets pour les noms avec espaces : `mkdir "Mon Projet"` ou `run "exemple/Mon Programme.py"`.
- L’invite indique votre position actuelle, exemple `~/exemple >`.
- Le terminal liste automatiquement le contenu au démarrage, pas besoin de taper `ls` d’abord.
- Les exe générés sont placés à côté du fichier source ; le programme exécuté hérite du répertoire de travail du terminal.
- L’aperçu du texte est limité aux 200 premières lignes / 64 Ko. Les fichiers plus gros peuvent être ouverts avec `start` par le programme système.
- Les suppressions demandent une confirmation par défaut ; tapez `y` pour confirmer.

# ------------------Español------------------
# HomeShell · Gestor de archivos de terminal simple
Es un sistema de gestión de archivos sencillo escrito únicamente con las bibliotecas estándar de Python, ubicado en el directorio `home`.
Haz doble clic en **`Iniciar terminal.bat`** para abrir una ventana de terminal, que muestra automáticamente el directorio actual y la lista de archivos de `home`.

Además de navegar, crear, abrir y borrar archivos, permite compilar C++ y ejecutar scripts Python en el terminal (siempre que tengas instalado el compilador g++ y el intérprete Python, y sus rutas estén añadidas a la variable de entorno Path).
Todas las operaciones de archivos están restringidas al directorio `home`. Escribir `..`, rutas absolutas o enlaces simbólicos no permite salir de este límite.

## Inicio rápido
1. Haz doble clic en `Iniciar terminal.bat`.
2. Escribe comandos tras el prompt `~/ >`, por ejemplo:
ls
mkdir Proyecto
cd Proyecto
write nota.txt Hoy empecé a usar HomeShell
open nota.txt
cd ..

build ejemplo/hello.cpp
run ejemplo/hello.cpp ZhangSan
run ejemplo/hello.py LiSi
3. Escribe `help` para ver todos los comandos, `exit` para salir.

Si no ocurre nada al hacer doble clic, abre PowerShell en el directorio `home` y ejecuta:
.\Iniciar terminal.bat

> El script de inicio usa primero `bin/Python312/python.exe`. Si no lo encuentra, prueba sucesivamente `python`, `py -3` y el Python incluido con DSH.

## Compilar y ejecutar
### C++
build ejemplo/hello.cpp
run ejemplo/hello.cpp ZhangSan
- `build` usa `bin/g++.exe` para compilar y genera `hello.exe` en el mismo directorio.
- Parámetros de compilación: C++17, `-O2`, archivos fuente UTF-8, enlace estático en Windows.
- `run` compila primero y luego ejecuta el ejecutable generado; también puedes ejecutar directamente `run ejemplo/hello.exe`.
- Los errores de compilación se muestran directamente en el terminal, como punto y coma faltante o archivos de cabecera no encontrados.

### Python
build ejemplo/hello.py
run ejemplo/hello.py LiSi
py ejemplo/hello.py WangWu
- `build` usa `python -m py_compile` para comprobación sintáctica y genera archivos `.pyc` en `__pycache__`.
- `run` / `py` ejecutan directamente scripts Python. Los argumentos del script se escriben después del nombre del archivo.
- Python usa por defecto `bin/Python312/python.exe`. El antiguo `bin/python3` de 0 bytes no se usa. El comando `tools` muestra la ruta real de Python.

### Ver cadena de herramientas
tools
Muestra las rutas actuales de `g++`, la cadena de herramientas MinGW y Python.

`bin/g++.exe` es un enlace físico a la cadena de herramientas Dev-C++/MSYS2. Copiar solo `g++.exe` no es suficiente para compilar, ya que también necesita `cc1plus`, archivos de cabecera y bibliotecas. HomeShell busca automáticamente la cadena completa desde el origen del enlace físico o ubicaciones habituales de instalación. Si la detección automática falla, define la variable de entorno:
$env:HOMESHELL_GCC_ROOT = "C:\Dev-Cpp\msys2\ucrt64"

## Activar el subsistema
`.fileshell/children_home` es el directorio raíz del subsistema y contiene el proyecto `mini_fs`.
Dentro de HomeShell escribe:
activar
Esto inicia el programa principal del subsistema:
.fileshell/children_home/mini_fs/src/main.exe
El directorio de trabajo al iniciar es `children_home`, así que la vista de archivos del subsistema empieza en esa carpeta.
Cuando `main.exe` termina, vuelves a HomeShell.

## Lista de comandos
| Función | Comando | Descripción |
| --- | --- | --- |
| Listar | `ls` / `ls -a` | Listar directorio actual; `-a` muestra elementos ocultos |
| Entrar | `cd carpeta` | Abrir carpeta; `cd ..` volver atrás; `cd ~` volver a raíz home; `cd -` volver al último directorio |
| Mostrar ubicación | `pwd` | Mostrar ruta relativa y ruta absoluta completa |
| Abrir | `open carpeta-o-archivo` | Las carpetas se abren y listan; los archivos de texto se previsualizan |
| Árbol de directorios | `tree` / `tree 2` | Mostrar árbol del directorio actual |
| Compilar C++ | `build archivo.cpp` | Generar exe en la misma carpeta |
| Compilar Python | `build archivo.py` | Generar código byte en `__pycache__` |
| Ejecutar | `run archivo [argumentos...]` | Compilar y ejecutar C++, ejecutar Python o exe |
| Ejecutar Python | `py archivo.py [argumentos...]` | Ejecutar script Python directamente |
| Cadena de herramientas | `tools` | Mostrar rutas g++ / MinGW / Python |
| Activar subsistema | `activar` | Lanzar `main.exe` dentro de children_home |
| Crear carpeta | `mkdir nombre` | Admite rutas anidadas, ejemplo `mkdir Proyecto/Documentos` |
| Crear archivo | `touch nombre` | Crear archivo vacío; actualizar marca de tiempo si ya existe |
| Escribir texto | `write archivo contenido` | Crear o sobrescribir archivo UTF-8; poner contenido entre comillas si tiene espacios |
| Renombrar | `rename antiguo nuevo` | Renombrar archivo o carpeta |
| Borrar archivo | `rm archivo` | Pide confirmación antes de borrar |
| Borrar carpeta | `rm -r carpeta` | Borrado recursivo, pide confirmación |
| Borrar forzado | `rm -rf ruta` | Sin confirmación, usar con cuidado |
| Borrar carpeta vacía | `rmdir carpeta` | Solo borra carpetas vacías |
| Abrir con el sistema | `start` / `start archivo` | Abrir con explorador o programa predeterminado |
| Limpiar pantalla | `clear` | Limpiar la pantalla del terminal |
| Ayuda | `help` | Mostrar ayuda de comandos |
| Salir | `exit` | Salir del programa |

También hay alias en chino: `列表`, `进入`, `打开`, `编译`, `运行`, `新建文件夹`, `新建文件`, `删除`, `重命名`, `退出`.

## Estructura de directorios
```text
home/
├── Iniciar terminal.bat          # Entrada de doble clic
├── README.md                    # Este documento
└── .fileshell/                  # Archivos del programa (directorio de sistema, no borrar)
    ├── fileshell.py             # Programa principal Python
    ├── children_home_cpp/       # Directorio del subsistema C++
    │   └── main.cpp / main.exe
    └── children_home_rust/      # Directorio del subsistema Rust
        └── src/main.rs / main.exe
```
`home` es la raíz de este sistema y el único lugar editable. Puedes crear, abrir, renombrar y borrar libremente tus archivos y carpetas aquí. `.fileshell` es la carpeta del programa, `Iniciar terminal.bat` es el lanzador; se rechazará su borrado o renombrado.

## Consejos
- Usa comillas para nombres con espacios: `mkdir "Mi Proyecto"` o `run "ejemplo/Mi Programa.py"`.
- El prompt muestra tu ubicación actual, ejemplo `~/ejemplo >`.
- El terminal lista automáticamente el contenido al arrancar, no hace falta escribir `ls` primero.
- Los exe generados se colocan junto al archivo fuente; el programa ejecutado hereda el directorio de trabajo del terminal.
- La previsualización de texto está limitada a las primeras 200 líneas / 64 KB. Los archivos más grandes se pueden abrir con `start` mediante el programa del sistema.
- Los borrados piden confirmación por defecto; escribe `y` para confirmar.
