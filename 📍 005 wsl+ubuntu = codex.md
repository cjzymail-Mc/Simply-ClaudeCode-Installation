
---------------------------------------------------
    历史记录：第一堂公开课 - 如何折腾 codex
---------------------------------------------------




2026-02-28 

---------------------------------------------------------------------------------

确认 CPU 虚拟化是否开启（关键） /// 旭宏说可以花2元淘宝升级 win 11

重启后：

### 打开任务管理器

按：

```
Ctrl + Shift + Esc
```

→ 点击「性能」
→ 点击「CPU」
→ 看右侧：

```
虚拟化：已启用   或   已禁用
```

---

## ❗ 如果显示“已禁用”

那就是 BIOS 没开虚拟化。

你需要：

1. 重启电脑
2. 开机时不停按

   * 华硕 / 联想：F2 或 Delete
   * 惠普：F10
   * 戴尔：F2
3. 找到类似：

```
Intel Virtualization Technology
SVM Mode
Intel VT-x
```

改成：

```
Enabled
```

保存退出。

---


---------------------------------------------------------------------------------

检查 WSL 版本：
PowerShell 输入 
wsl --version
是否安装成功：

输入 wsl --list（看到 Ubuntu 即成功）
输入 wsl（直接进入 Ubuntu 终端即成功）



---------------------------------------------------------------------------------

这说明啥？ 

PS C:\Windows\System32> wsl --version
命令行选项无效: --version

PS C:\Windows\System32> wsl --list
适用于 Linux 的 Windows 子系统没有已安装的分发版。
可以通过访问 Microsoft Store 来安装分发版:
https://aka.ms/wslstore



------------
说明：
WSL 是老版本（不支持 --version）。
没有安装任何 Ubuntu（--list 显示空）。
你下载的 .wsl 文件还没注册成功。

为什么还是老版本：你的 wsl.exe 是 Windows 10 内置的老版（in-box），没升级到 Microsoft Store 版。
安装最新版 WSL（最短流程）：

严格按你贴的“彻底清理”教程做完（尤其是 disable-feature + 必须重启）。
重启后管理员 PowerShell 直接运行：textwsl --install（自动装最新 Store 版 WSL + Ubuntu）
完成后输入 wsl --version 检查（应显示新版本如 2.x）。

你的 ubuntu-24.04.4-wsl-amd64.wsl 文件等装好最新 WSL 后再用 wsl --install --from-file <文件路径> 即可。
现在直接跑第2步试试。

需要先清理旧 WSL（避免冲突）。
清理命令（管理员 PowerShell）：
textwsl --shutdown
wsl --unregister Ubuntu
（报错忽略即可）

---------------------------------------------------------------------------------

wsl --install --from-file "D:\Documents and Settings\xy24\Desktop\Ubuntu+Installer\ubuntu-24.04.4-wsl-amd64.wsl"

结果报错了
正在安装: D:\Documents and Settings\xy24\Desktop\Ubuntu+Installer\ubuntu-24.04.4-wsl-amd64.wsl
Windows 版本 10.0.19044.1288 不支持打包版本的 适用于 Linux 的 Windows 子系统。
通过 Windows 更新或通过以下方式安装所需的更新： https://aka.ms/store-wsl-kb-win10
有关信息，请访问 https://aka.ms/wslinstall
错误代码: Wsl/WSL_E_OS_NOT_SUPPORTED
按任意键即可退出...







推荐选这个（最适合你的 Win10）：
Ubuntu 22.04 LTS (x64)
直接下载链接：
https://aka.ms/wslubuntu2204
下载完 .appx 文件后双击安装（或管理员 PowerShell 运行 Add-AppxPackage 文件名.appx）。
装好后输入 wsl 进入即可。
（先临时关 v2ray）

为啥不是Ubuntu 20.04 LTS (x64, arm64)？

因为 Ubuntu 20.04 已于 2025 年 5 月结束标准支持（无免费安全更新）。
22.04 更推荐（支持到 2027 年，Node.js/Codex 更稳）。
如果你坚持用 20.04：
https://aka.ms/wslubuntu2004 （下载 .appx 双击安装）


---------------------------------------------------------------------------------

这些文件怎么这么大？ 我的wsl（★仅适用于win11★）ubuntu-24.04.4-wsl-amd64.wsl好像只有380Mb。另外，wsl1 和 wsl2 有啥区别？这些区别对我使用codex有什么影响？简答：
为什么文件这么大？

你下载的 .appx 是完整 Ubuntu 根文件系统（已解压状态），体积通常 400~600MB 正常。
你之前的 ubuntu-24.04.4-wsl-amd64.wsl 是新压缩格式，只有 380MB 左右（更小更高效）。

WSL1 vs WSL2 区别（对你最重要）：

项目WSL1WSL2内核共享 Windows 内核真正的 Linux 内核（虚拟机）性能文件访问更快整体更快（CPU/IO/网络）兼容性很多 native 模块报错完美兼容（npm、Docker 等）内存占用很小稍大（动态分配）
对 Codex CLI 的影响：

必须用 WSL2，否则 Codex 安装时很容易卡在 npm 编译 native 模块（sharp、esbuild 等）。
WSL1 基本跑不顺 Codex（官方也只推荐 WSL2）。

结论：
你用 22.04 或 24.04 的 .appx / .wsl 都会自动装成 WSL2（默认就是 WSL2），完全不用担心。
直接双击你下载的 .appx 装完就行，之后 wsl 进入即可继续装 Codex。

---------------------------------------------------------------------------------

陈晶 2-28 13:15:17
老马的方法也搞定了，虽然是老版本，但也能用了[强]

陈晶 2-28 13:15:53
老马说专家（宇昂）的信息是正确的，那个方案更优


你是不是搞错了，我问了专家，专家说 win10可以装最新版的 ubuntu，只要调整方法即可：下面是一套适用于你们这种情况（Win10 2004 老版 wsl.exe 不支持 --install/--status，但可以用 --import）的**最简可复制步骤**。让他按顺序执行即可。

## A. Windows 端（管理员 PowerShell）
### 1）启用 WSL 必要组件
PowerShelldism /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
然后**重启电脑**。
### 2）设置默认使用 WSL2
重启后（普通 PowerShell 也行）：
PowerShellwsl --set-default-version 2
### 3）导入你给他的 Ubuntu 安装包（.wsl）
假设安装包在“下载”目录，文件名为 ubuntu-24.04.4-wsl-amd64.wsl：
PowerShellcd $env:USERPROFILE\Downloads
mkdir C:\WSL\Ubuntu-24.04
wsl --import Ubuntu-24.04 C:\WSL\Ubuntu-24.04 .\ubuntu-24.04.4-wsl-amd64.wsl --version 2
wsl -l -v
### 4）启动 Ubuntu
PowerShellwsl -d Ubuntu-24.04

## B. Ubuntu 里（第一次进入时，默认是 root）
### 5）更新系统
Bashapt update && apt upgrade -y
 我现在装了你推荐了22.04，咋办？ 专家说的对吗？

-----------
专家说的对（--import 完全正确，支持你的 Win10）。
现在咋办（你已装22.04）：

推荐直接用22.04（最简单）：
PowerShell 输入 wsl 进入，然后继续装 Codex 命令。
想换24.04：PowerShellwsl --unregister Ubuntu   # 先卸载22.04（或用 wsl --list 看名字）然后严格按专家第3步导入你的 .wsl 文件。

建议：直接用22.04，Codex 完全没问题，别折腾了。



---------------------------------------------------------------------------------
【ubuntu 终于装好了。。。。。。。。。。】

安装成功！ 🎉
现在直接在终端里依次复制粘贴下面 5 条命令（一行一行回车）：

curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash
source ~/.bashrc
nvm install 22
nvm use 22
npm install -g @openai/codex






---------------------------------------------------------------------------------
【还要折腾 proxy，否则 curl 会安装失败 。。。。。。。。。。。。。。。。。 苦难的中国人】


问题根源：v2rayN 默认只监听 127.0.0.1（Windows 本机），WSL2 连不上 172.xx:10808（Connection refused）。
最快解决（按顺序做）：

Windows 侧（v2rayN）：
打开 v2rayN → 设置 → 核心设置 / OptionSetting
勾选 允许来自局域网的连接（Allow LAN / LAN 连接）
保存并重启 v2rayN

WSL 里（重新设置）：
HOST_IP=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2}')
export http_proxy=socks5://$HOST_IP:10808
export https_proxy=socks5://$HOST_IP:10808

在 WSL 里运行下面命令测试（复制粘贴）：
echo $http_proxy
echo $https_proxy
curl -I https://raw.githubusercontent.com
（看到 HTTP/2 200 就成功）


---------------------------------------------------------------------------------

【★ ★ ★ ★ ★ ★ 宇昂的方法（GPT）更强，推荐使用 ★ ★ ★ ★ ★ ★ ★ 】


## 5) WSL 端：设置代理环境变量（推荐 socks5h）
在同一个 WSL 终端执行：
Bashexport ALL_PROXY="socks5h://$WIN_IP:10808"
export http_proxy="$ALL_PROXY"
export https_proxy="$ALL_PROXY"
export NO_PROXY="localhost,127.0.0.1,::1"
说明：

socks5h 里的 **h** 很重要：域名解析也走代理（CLI 更稳定）
NO_PROXY 避免 localhost 回调/本地服务被错误代理

专家建议这样设置更稳定。但这是临时写入吧。如何用 nano / cat 更新呢？ 专家的建议更好吗？



--------------
专家建议更好（socks5h + ALL_PROXY 更稳定，尤其 git/npm/curl）。

永久更新（cat 方式）：
复制粘贴下面全部运行：

Bash
# 删除旧 proxy 行
sed -i '/http_proxy=/d' ~/.bashrc
sed -i '/https_proxy=/d' ~/.bashrc

# 追加专家推荐（永久）
cat >> ~/.bashrc << 'EOF'

# WSL proxy (socks5h - 专家推荐)
HOST_IP=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2}')
export ALL_PROXY="socks5h://$HOST_IP:10808"
export http_proxy="$ALL_PROXY"
export https_proxy="$ALL_PROXY"
export NO_PROXY="localhost,127.0.0.1,::1"
EOF

立即生效：
Bashsource ~/.bashrc
echo $ALL_PROXY




---------------------------------------------------------------------------------

  Welcome to Codex, OpenAI's command-line coding agent

✓ Signed in with your ChatGPT account

  Before you start:

  Decide how much autonomy you want to grant Codex
  For more details see the Codex docs

  Codex can make mistakes
  Review the code it writes and commands it runs

  Powered by your ChatGPT account
  Uses your plan's rate limits and training data preferences

  Press Enter to continue

---------------------------------------------------------------------------------
