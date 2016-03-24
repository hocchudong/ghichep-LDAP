###Các thành phần của FreeIPA

- Directory Server
- Kerberos
- PKI
- DNS
- Certmonger
- NTP Server
- Web UI
- Trusts
- Client

####Directory Server

- FreeIPA Directory Server được build trên 389DS LDAP Server. Nó là nền tảng của hầu hết các giải pháp quản lý identity. Phục vụ như 1 data backend cho tất cả identity, xác thực (Kerberos) ,cho phép các dịch vụ và các chính sách khác.

- Có thể triển khai một môi trường multi-master

#####DataStorage

- Tất cả FreeIPA identity , policy, cấu hình hay certificates được lưu tại Directory Server. FreeIPA object được lưu trong một hậu tố của tên vùng (ví dụ dc=example , dc=com cho vùng EXAMPLE.COM), certificate được lưu trong hậu tố thứ hai o =ipaca

#####Server Plugin

- Các plugin

  - ipa_pwd_extop: xử lý việc thay đổi password, thực thi chính sách password cho password mới hoặc thay đổi
  - IPA Lockout: móc vào xác thực trên Directory Server để ngăn chặn việc bruteforce password
  - ipa_enrollment_extop: cung cấp hoạt động mở rộng cho các client mới và tạo mới client
  - Schema Compatibility:
  - ipa-winsync: Cho phép đồng bộ user với Active Directory
  - IPA DNS

####PKI
- Tích hợp Dịch vụ PKI được cung cấp bởi project Dogtag. PKI đăng ký và publish certificate cho các host và dịch vụ của FreeIPA. Nó cũng cung cấp dịch vụ CRL và OOCSP cho tất cả các phần mềm.

- Certificate được sử dụng bở clients host và dịch vụ của FreeIPA có hạn chế, cơ sở hạ tầng này cũng cần được xử lý 1 cách tin cậy và làm mới certificates do đó một daemon (Certmonger) được chạy trên tất cả các clients xử lý việc làm mới.
####DNS

- Tích hợp FreeIPA DNS cho phép admin quản lý và phục vụ các DNS record trong một domain sử dụng cùng CLI hay Web UI như khi quản lý các identity và các chính sách.

- FreeIPA DNS dựa trên project bind-dyndb-ldap là một BIND name server nâng cao để có thể dử dụng FreeIPA server như một data backend.


####Certmonger

- Certmonger giám sát các chứng chỉ sắp hết hạn, và có thể làm mới các chứng chỉ sắp hết hạn với sự giúp đỡ của 1 CA.

- Có thể làm việc được với cả OpenSSL hoặc NSS Database

- Có 2 lệnh để kiểm tra cert là `getcert` và `ipa-getcert`. Với `getcert` sẽ quản lý tất cả certificate bạn đang theo dõi. Còn `ipa-getcert` làm việc với IPA CA. `ipa-getcert` thì tương đương với `getcert -c IPA`

- `getcert`

  - Request ID được sử dụng trong các lệnh khác của certmonger
  - status: MONITORING có nghĩa là certificate có hiệu lực và đang được theo dõi bởi certmonger
  - key pair storage: vị trí lưu key và cert trên filesystem
  - CA Type: IPA
  - issuer: là tên của CA mà cấp certiifcate
  - subject: là tên của certificate trong file certificate
  - expiration: ngày hết hạn
  - eku: (Enhanced Key Usage) danh sách các EKU mở rộng của certificate. Mặc định IPA certificate được sử dụng cả trên server và client
  - pre post và post-save command: dịnh nghĩa command được thực thi trước và sau khi tiến trình làm mới. Ví dụ như restart 1 service khi certificate được làm mới

####NTP Server

- Giao thức Kerberos rất nhạy cảm để đồng bộ hóa thời gian giữa KDC và các node authentication. Thời gian khác nhau nhiều hơn vài phút, việc xác thực sẽ không hoạt động. Vì lý do này,cài đặt cấu hình NTP Server trên mỗi FreeIPA Server

- Client và server phát hiện 2 dịch vụ đồng bộ thời gian - `ntpd` và `chrony`. Trên Server FreeIPA yêu cầu ntpd, chrony cung cấp NTP Client service,
####Web UI

- Cung cấp ứng dụng web cho người quản trị FreeIPA.

####Trusts

- Tích hợp Linux system vào Active Directory

####Client

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

######Chuẩn bị

- 1 Server chạy Centos (IP: 172.16.69.35)

- 1 Client chạy centos (IP: 172.16.69.36)
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


- Tạo user

  Tạo 1 user là saphi khai báo First Name, Last Name. Tham số --password để nhập password

`ipa user-add saphi --first=Sa --last=Phi --password`

<img src="">

  Để kiểm tra user đã được tạo ta dùng lệnh `ipa user-find [<tên user>]`. Mục `tên user` có thể không cần. Nếu không nêu tên user trong lệnh trên sẽ hiển thị tất cả user

<img src="">

#####3. Cấu hình trên client
- Update
`yum update -y`

- Cài đặt ipa-client

`yum install -y ipa-client`

- Đặt hostname và sửa hosts, resolv.conf
  - Sửa hostname `client1.vnptcloud.vn`
  - Thêm vào file hosts
    ```
    172.16.69.36 client1.vnptcloud.vn client1
    172.16.69.35 freeipa.vnptcloud.vn vnptcloud.vn
    ```
  - Thêm vào đầu file resolv.conf

    ```
    nameserver 172.16.69.35

    ```
- Thiết lập cấu hình client

Gõ `ipa-client-install --mkhomedir`

- Khi xong ta ssh lại bằng user `saphi` đã tạo ở bên IPA Server
