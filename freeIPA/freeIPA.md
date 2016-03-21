###Cài đặt FreeIPA
####Chuẩn bị

- Hiện tại FreeIPA chỉ hỗ trợ Fedora và RHEL

- FreeIPA yêu cầu nhiều package mới như: LDAP, DNS hay PKI server

- Static hostname

  - Kerberos xác thực dựa vào 1 static hostname, nếu hostname thay đổi , Kerberos xác thực có thể bị break. Vì vậy không nên sử dụng cấu hình hostname động từ DHCP. Cấu hình trong /etc/hostname

- DNS Rules

  - DNS cấu hình đúng là nền tảng của 1 hệ thống FreeIPA.
  ```
  The hostname cannot be localhost or localhost6.
  The hostname must be fully-qualified (ipa.example.com)
  The hostname must be resolvable.
  The reverse of address that it resolves to must match the hostname.
  ```
  - Cấu hình tại /etc/hosts như sau

  `172.16.69.50 ipa.vpntcloud.vn ipa`


####Cài đặt

- Bài lab thực hiện trên Centos 6.7

#####1. Cài đặt FreeIPA Server

`sudo yum install ipa-server bind bind-dyndb-ldap`

#####2. Cấu hình FreeIPA server

- Để cấu hình freeipa từ command line ta dùng lệnh `ipa-server-install --setup-dns`.

- Điền các thông tin về service

- Sau khi hoàn tất các thông tin ta tiến hành mở các port sau

```
TCP Ports:
    * 80, 443: HTTP/HTTPS
    * 389, 636: LDAP/LDAPS
    * 88, 464: kerberos
    * 53: bind
  UDP Ports:
    * 88, 464: kerberos
    * 53: bind
    * 123: ntp
```

- Cấu hình iptables như sau

```
*filter
:INPUT ACCEPT [0:0]
:FORWARD ACCEPT [0:0]
:OUTPUT ACCEPT [0:0]
-A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT
-A INPUT -p icmp -j ACCEPT
-A INPUT -i lo -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 22 -j ACCEPT
-A INPUT -j REJECT --reject-with icmp-host-prohibited
-A FORWARD -j REJECT --reject-with icmp-host-prohibited
-A INPUT -m state --state NEW -m tcp -p tcp --dport 80 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 443 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 389 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 636 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 53 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 88 -j ACCEPT
-A INPUT -m state --state NEW -m tcp -p tcp --dport 464 -j ACCEPT
-A INPUT -m state --state NEW -m udp -p udp --dport 88 -j ACCEPT
-A INPUT -m state --state NEW -m udp -p udp --dport 464 -j ACCEPT
-A INPUT -m state --state NEW -m udp -p udp --dport 53 -j ACCEPT
-A INPUT -m state --state NEW -m udp -p udp --dport 123 -j ACCEPT
COMMIT
```

- Khởi động lại iptables
` service iptables restart`

- Lấy ticket cho admin

`kinit admin`
