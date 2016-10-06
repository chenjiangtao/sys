# sys

注意：iptables只能用脚本改，如下：（这个只是个示例，不可以直接用）

cat > /etc/sysconfig/iptables << EOF
# Firewall configuration written by system-config-securitylevel
# Manual customization of this file is not recommended.

过滤器（我不也懂）
*filter
:INPUT DROP [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]
:syn-flood - [0:0]
-A INPUT -i lo -j ACCEPT

开端口
-A INPUT -p tcp -m state --state NEW -m tcp --dport 80 -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 2202 -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 3306 -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 2368 -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 3000 -j ACCEPT
-A INPUT -p tcp -m state --state NEW -m tcp --dport 8848 -j ACCEPT

下面这一段非常重要，如果不加无法下载、看视屏
-A INPUT -p icmp --icmp-type any -j ACCEPT
-A INPUT -s localhost -d localhost -j ACCEPT
-A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
-A OUTPUT -p icmp --icmp any -j ACCEPT
-A OUTPUT -s localhost -d localhost -j ACCEPT
-A OUTPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
-A OUTPUT -p udp --dport 53 -j ACCEPT
-A OUTPUT -p tcp -m state --state NEW --dport 80 -j ACCEPT
-A INPUT -p tcp --dport 443 -j ACCEPT

下面是限制、防攻击（如果配置shadowsocks务必去除下面这段）
-A INPUT -p icmp -m limit --limit 100/sec --limit-burst 100 -j ACCEPT
-A INPUT -p icmp -m limit --limit 1/s --limit-burst 10 -j ACCEPT
-A INPUT -p tcp -m tcp --tcp-flags FIN,SYN,RST,ACK SYN -j syn-flood
-A INPUT -j REJECT --reject-with icmp-host-prohibited
-A syn-flood -p tcp -m limit --limit 3/sec --limit-burst 6 -j RETURN
-A syn-flood -j REJECT --reject-with icmp-port-unreachable
COMMIT
EOF

配置完成要重起iptables
/sbin/service iptables restart

说明：
/sbin/service iptables restart 这个是重起的意思

附配置文件

centos7用firewall配置时可以改这个文件（没有试过）
hosts.deny

