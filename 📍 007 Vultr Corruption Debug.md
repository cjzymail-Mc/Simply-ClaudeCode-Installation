*!!!  Emergency Case：VS Vultr Corruption  !!!*
2026-06-10

排查思路：
1、查 [本地] → [vultr]
------- 本地：powershell tracert 测试 -------
tracert 45.76.70.224
Test-NetConnection 45.76.70.224 -Port 22



2、查 [世界各地] → [vultr]  //  全球测试！！！
https://check-host.net/check-tcp?host=45.76.70.224:22



3、查 [vultr] →  [google / baidu] 
--------- 服务器端：先重点测 google(国际) / baidu(国内) 是否可以联通 ----------
curl -I https://www.google.com
curl -I https://www.baidu.com




4、挂上XH的梯子后，再查  [本地] → [vultr]
# 测试能否访问 Google（验证代理是否生效）
curl.exe -x socks5h://127.0.0.1:10808 https://www.google.com -I
# 测试你的服务器 443 端口（最常用）
curl.exe -x socks5h://127.0.0.1:10808 -v http://45.76.70.224:443
# 测试你的新 VLESS 节点端口（如果想看握手情况）
curl.exe -x socks5h://127.0.0.1:10808 -v https://45.76.70.224:443






--------- 服务器端：修改端口  ----------
nano /etc/ssh/sshd_config	#使用nano
找到：#Port 22
改成：Port 48271 	#注意，删掉 # 符号！！！
Crtl + O 保存 
Enter 确认
Ctrl +X 退出
systemctl restart ssh 	# 重启SSH
       # 如何查看端口 ？？？？？？？
# 方法一：查看当前 SSH 正在监听的端口
ss -tlnp | grep sshd
# 方法二：直接查看 SSH 配置文件
cat /etc/ssh/sshd_config | grep -E "^Port|^#Port"


------- 本地：powershell proxy 一次性设置 -------
# 设置代理（当前窗口有效）// 但
$env:HTTP_PROXY = "socks5://127.0.0.1:10808"
$env:HTTPS_PROXY = "socks5://127.0.0.1:10808"




--------- 服务器端：一键安装（2026-06-10 仍旧可用）  ----------

wget -P /root -N --no-check-certificate "https://raw.githubusercontent.com/mack-a/v2ray-agent/master/install.sh" && chmod 700 /root/install.sh && /root/install.sh
