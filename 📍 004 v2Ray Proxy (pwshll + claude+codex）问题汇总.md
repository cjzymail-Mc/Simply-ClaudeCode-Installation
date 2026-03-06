
---------------------------------------------------
    步骤1：PowerShell -Proxy 配置 for v2Ray - 10808
---------------------------------------------------


安装 wsl / ubuntu / codex / npm / nodejs 之前
首先在powershell中，测试下：
curl -I https://www.google.com

（据宇昂说，似乎不需要 pwshll 连接 google 也能成功安装 wsl / ubuntu）
（总之，安装 codex 过程历经坎坷，⚠️下次换台电脑试试，proxy pwshll方案是否最优⚠️）


如果powershell 连接 google ❌❌connect failed❌❌
说明 powershell proxy 设置不正确
必须先配置powershell。


一键解决方案（在 📌PowerShell📌 中直接复制全部运行）




------------ ⚠️ 从下面开始复制（不要复制这一行！！）--------------


# === PowerShell Proxy 极简一键重置（直接清空整个 Profile）===

$profilePath = $PROFILE

# 确保目录存在
$profileDir = Split-Path $profilePath -Parent
if (-not (Test-Path $profileDir)) { 
    New-Item -ItemType Directory -Path $profileDir -Force | Out-Null 
}

# 直接清空 + 写入全新配置（完全覆盖旧内容）
$newConfig = @'
# ==================== Proxy for PowerShell (Claude + Git 最佳) ====================
$proxy = "http://127.0.0.1:10808"

$env:http_proxy   = $proxy
$env:https_proxy  = $proxy
$env:HTTP_PROXY   = $proxy
$env:HTTPS_PROXY  = $proxy
$env:NO_PROXY     = "localhost,127.0.0.1,::1,.local"

Write-Host "✅ PowerShell Proxy 已加载 → http://127.0.0.1:10808" -ForegroundColor Green

# git 保持 socks5h 最稳定（与 Git Bash 完全一致）
git config --global http.proxy socks5h://127.0.0.1:10808
git config --global https.proxy socks5h://127.0.0.1:10808
# ============================================================================
'@

$newConfig | Set-Content -Path $profilePath -Encoding utf8 -Force

Write-Host "🎉 Profile 已完全清空并写入新配置！" -ForegroundColor Green
Write-Host "请立即运行下面命令让它生效：" -ForegroundColor Cyan
Write-Host ". `$PROFILE" -ForegroundColor Magenta


------------ ⚠️ 复制上面的所有内容（不要复制这一行！！）--------------



# 立即生效 & 测试（复制运行）

. $PROFILE                       # 立即加载
$env:http_proxy                  # 应该显示 http://127.0.0.1:10808
git config --global http.proxy   # 应该显示 socks5h://127.0.0.1:10808
Get-Content $PROFILE             # 应该只看到一份干净配置



# 测试网络（🎯 测试google必须显示：连接成功）
curl -I https://www.google.com  






















------------------------------------------------------
    步骤2：Claude Code -Proxy 配置 for v2Ray - 10808
------------------------------------------------------

Claude Code（claude 指令）官方不支援 socks5 / socks5h
這就是為什麼改成 ALL_PROXY 後它就連不上了（GitHub 能連是因為 git 本身支援 socks5 很好）。
最適合你的方案（claude 恢復正常 + git 保持最穩定）：



一鍵解決（直接複製全部貼到 📌Git Bash📌 執行）


------------ ⚠️ 从下面开始复制（不要复制这一行！！）--------------

# 1. 清除所有舊 proxy 設定
sed -i '/ALL_PROXY=/d' ~/.bashrc
sed -i '/http_proxy=/d' ~/.bashrc
sed -i '/https_proxy=/d' ~/.bashrc
sed -i '/HTTP_PROXY=/d' ~/.bashrc
sed -i '/HTTPS_PROXY=/d' ~/.bashrc


# 2. 追加最適合 claude code 的 http_proxy（10808）
cat >> ~/.bashrc << 'EOF'

# ==================== Proxy for Claude Code + Git (最佳組合) ====================
export http_proxy=http://127.0.0.1:10808
export https_proxy=http://127.0.0.1:10808
export HTTP_PROXY=http://127.0.0.1:10808
export HTTPS_PROXY=http://127.0.0.1:10808
export NO_PROXY="localhost,127.0.0.1,::1,.local"
# ============================================================================
EOF


# 3. 讓 git 單獨走 socks5h（比 http 更穩定）
git config --global http.proxy socks5h://127.0.0.1:10808
git config --global https.proxy socks5h://127.0.0.1:10808

# 4. 立即生效
source ~/.bashrc

# 5. 驗證
echo "Claude proxy: $http_proxy"
echo "Git proxy: $(git config --global http.proxy)"


------------ ⚠️ 复制上面的所有内容（不要复制这一行！！）--------------



# 6. 執行完後立即測試：
执行完之后，重点先测试下🚀google的连接是否正常🚀：
curl -I https://www.google.com


然后再进入claude code试试：
claude --version     # 或你平常用的 claude 指令
git status           # 確認 git 還正常




# 7. 后续如有调整 —— ——
claude 應該立刻恢復正常（因為回到你之前一直能用的 http_proxy 格式）。
以後想改端口（例如又改成 10809），只要把上面腳本裡的 10808 全部改成新端口，再跑一次即可。
這樣就完美了：
claude code 正常工作
git clone / push / pull 最穩定















------------------------------------------------------
    步骤3：GPT Codex -Proxy 配置 for v2Ray - 10808
------------------------------------------------------

GPT专家建议更好（socks5h + ALL_PROXY 更稳定，尤其 git/npm/curl）。

永久更新（cat 方式）：
复制粘贴下面全部运行（在 📌ubuntu 📌 執行）：



------------ ⚠️ 从下面开始复制（不要复制这一行！！）--------------

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


# 立即生效：
source ~/.bashrc
echo $ALL_PROXY

------------ ⚠️ 复制上面的所有内容（不要复制这一行！！）--------------



# 同理，執行完後立即測試：
执行完之后，重点先测试下🚀google的连接是否正常🚀：

curl -I https://www.google.com
