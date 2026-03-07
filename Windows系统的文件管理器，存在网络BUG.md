# Windows系统的文件管理器，存在网络BUG

---

## 版权声明

© 2026 [Kan Liu]。保留所有权利。

本文为原创作品，未经允许不得转载。可以通过以下方式联系作者：[lkbhg@outlook.com]。

##

---

我必须要说，当在windows系统自带的文件管理器中，添加网络位置等操作，会导致文件/文件夹创建/修改直接卡死，并且回收站也会卡死。
这在windows10和windows11都会广泛存在。

更加可怕的是，如果删除了网络位置，但网络位置中的某个文件夹依然存在于回收站的收集目录，那么更可怕的事情就会发生————回收站会直接卡死

## 解决办法
### 方法一：通过注册表强制删除“幽灵”回收站配置（最直接有效）
Windows 将回收站管理的各个驱动器/文件夹配置储存在注册表的 BitBucket 键值中。我们可以直接去那里删掉失效的记录。

按 Win + R 打开运行窗口，输入 regedit 并回车，打开注册表编辑器。

导航到以下路径（可以直接复制粘贴到顶部地址栏）：
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\BitBucket\Volume
和
HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\BitBucket\KnownFolder

展开左侧的 Volume或KnownFolder 文件夹。你会看到下面有一堆以大括号 { } 包围的字符串（即 GUID，例如 {xxxxxxxx-xxxx...}）。每一个 GUID 代表回收站属性里的一个位置。

如何安全清理：

依次点击这些 {GUID} 文件夹，查看右侧面板的数据，看看能不能认出哪个是那个远程文件夹对应的盘符或路径。

更干脆的做法： 如果你分不清，可以直接右键点击 Volume 这个父文件夹，选择“删除”。（注：这非常安全。删除后，Windows 会自动为你当前正常的本地 C 盘、D 盘等重新生成干净的默认回收站配置，而那个无法访问的远程幽灵路径则会被彻底遗忘。）


删除完毕后，关闭注册表编辑器。

### 方法二：使用命令行强制清理所有隐藏的网络挂载记录
为了防止系统后台还有残留的网络凭据在不断尝试连接，我们可以用命令行执行一次大扫除。

右键点击开始按钮，选择终端管理员（或命令提示符管理员）。

输入以下命令并按回车：
net use * /delete /y
(这个命令会强制断开所有当前记录的网络驱动器连接。如果提示“列表为空”，说明网络层已经没有连接了，重点都在注册表里。)

接着，为了刷新资源管理器，输入以下命令重启它：
taskkill /f /im explorer.exe & start explorer.exe


## 然而，可怕的是，有时候KnownFolder无法被彻底删除，甚至再重启后会恢复

一般来说，这是因为“脱机文件”同步。而且这个无法通过关闭或者启用该功能来恢复。（当系统尝试为一个断开或有冲突的共享文件夹同步脱机缓存时，回收站一旦触碰到这个路径就会无限转圈。）

### 暴力方案一（无法根除）：

暴力重定向（如果删不掉，就让它失效）
如果某个项死活删不掉，我们可以通过修改它的权限，让系统“无法读取”它，从而跳过轮询：

1. 在注册表中右键点击那个会自动恢复的文件夹项。

2. 选择 权限... -> 高级。

3. 点击 禁用继承，并在弹出的窗口选择“从此对象中删除所有已继承的权限”。

4. 这样系统进程（甚至 SYSTEM 账户）都无法访问这个项，回收站扫描到这里时会直接跳过，不再转圈。

### 暴力方案二：
简单来说，Windows 的同步中心在本地硬盘上划出了一块秘密区域（CSC 缓存），把那个远程文件夹的内容镜像了一份。现在服务器关了，但系统底层的“脱机文件”驱动依然认为该文件夹是系统的一部分，并试图在每次打开回收站时去校验它的完整性。因为服务器失联，系统会不断尝试连接直到超时，这就是卡顿的根本原因。

既然常规删除注册表会“复活”，说明 CSC（客户端缓存）驱动程序 正在强制维持这个映射。请按照以下步骤执行“暴力手术”：

1. 清理全局卷 GUID 缓存
Windows 可能会把这个远程文件夹识别为一个虚拟卷（Volume）。
在注册表中导航到：
HKEY_LOCAL_MACHINE\SYSTEM\MountedDevices
在右侧列表中，寻找任何数据（Data）中包含你那个远程路径或服务器名称（如 \\192.168...）的项目。
右键删除这些项。

2. 强制清理 CSC 数据库
这是解决“脱机文件”残留最彻底的方法。我们需要通过注册表给同步驱动下一个“清空所有缓存”的指令。
右键点击“开始”，选择 终端管理员（或命令提示符管理员）。
直接复制并执行以下命令（这会在注册表中添加一个初始化标志）：
reg add "HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\CSC\Parameters" /v FormatDatabase /t REG_DWORD /d 1 /f
关键步骤： 执行完后，立即重启电脑。
注意：重启过程中系统会清理缓存数据库，时间可能比平时稍长。重启后，那个幽灵文件夹应该会彻底从回收站属性和注册表中消失。

3. 移除 SMB 远程凭据残留
即使删除了驱动器，Windows 凭据管理器可能还存着旧的连接信息，导致系统后台不断尝试登录那个已经关闭的服务器。
按下 Win + S 搜索并打开 凭据管理器 (Credential Manager)。
点击 Windows 凭据。
在下方列表中寻找包含那个远程服务器 IP 或名称的条目（例如 TERMSRV/192.168... 或 \\ServerName）。
点击该条目并选择 删除。

4. 删除user shell folders
删除HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders中对应的路径，其中的NameID应该和HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\BitBucket\KnownFolder中的ID一样，并且Data可以显而易见的看到。
或者使用以下命令（小心替换名称）将其重定向回本地：
Set-ItemProperty -Path "HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders" -Name "你的文件夹名称" -Value "%USERPROFILE%\Documents"
