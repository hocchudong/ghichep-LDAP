# Giới thiệu
----
Trong phần này, tôi sẽ trình bày về OpenLDAP để giúp mọi người có một cái nhìn rõ hơn về giao thức LDAP. 

OpenLDAP là một Open source cung cấp dịch vụ thư mục. Các thư mục có thể được sử dụng để lưu trữ thông tin tập trung, và các thông tin này sử dụng cho việc xác thực.
Các máy trạm có thể kết nối tới OpenLDAP bằng cách sử dụng Lightweight Directory Access Protocol (LDAP). Máy trạm có thể tìm kiếm, chỉnh sửa hoặc lấy các bản ghi trong thư mục. 
LDAP server thường được sử dụng làm dịch vụ xác thực người dùng. Nhưng ngoài ra còn có rất nhiều ứng dụng khác của LDAP, như sử dụng làm danh bạ địa chỉ, DNS database, công cụ tổ chức, 
hay thậm chí là ứng dụng lưu trữ đối tượng mạng.

# Các kiến thức cần nắm về LDAP
----
Để hiểu về LDAP bạn cần phải nắm được các vấn đề sau:
- Directory là gì?
- The structure of a Directory Entry
- A unique Name: The DN
- An Example LDAP Entry
- The Object Class Attribute
- Operational Attributes
- The Directory Infomation Tree

Phần này bạn có thể tham khảo tại [đây](https://github.com/hocchudong/ghichep-LDAP/blob/master/docs/TanNT-LDAP-Understanding.md) để nắm được các khái niệm. 
Tiếp theo tôi sẽ đi sâu vào ứng dụng LDAP

# A Technical Overview of OpenLDAP
----
OpenLDAP có thể được chia thành 4 thành phần:
- Servers: Cung cấp dịch vụ LDAP
- Clients: Lấy dữ liệu LDAP
- Utinities: Hỗ trợ LDAP server
- Libraries: Cung cấp giao diện lập trình cho LDAP

![OpenLdap-basic-1](../images/OpenLdap-basic-1.png)

**The Server**: Thành phần chính trong LDAP là SLAPD (the Stand-Alone LDAP Daemon) cung cấp truy nhập cho một hoặc nhiều cây thư mục.

**Client**: Client thông qua giao thức LDAP để kết nối tới Server

# Installation and Configuration
----
- Daemons (Server): slapd và slurpd
- Client applications: ldapsearch, ldapadd, ldapmodify,...
- Supporting Utilities: slapcat, slapauth,...
- Libraries: libldap

**Note**: Phần cài đặt bạn có thể tham khảo tại [đây](https://github.com/hocchudong/ghichep-LDAP/blob/master/openLDAP/install.md)

## Configuring the SLAPD Server
----
Chúng ta có 02 daemon: SLURPD và SLAPD

SLAPD là một trong những file cấu hình chính cho mọi file cấu hình bổ trợ. đường dẫn tại `/usr/share/slapd/slapd.conf`. 

Trong file cấu hình này, được chia làm 3 phần chính: BASIC, DATABASE và ACLs

BASIC
```sh
include         /etc/ldap/schema/core.schema
include         /etc/ldap/schema/cosine.schema
include         /etc/ldap/schema/nis.schema
include         /etc/ldap/schema/inetorgperson.schema
pidfile         /var/run/slapd/slapd.pid
argsfile        /var/run/slapd/slapd.args
loglevel        none
modulepath      /usr/lib/ldap
moduleload      back_hdb
sizelimit 500
tool-threads 1
backend         hdb
```
Chỉ dẫn cho server lấy các thông tin schema. lưu trữ thông tin tiến trình

DATABASE
```sh
database        hdb
suffix          "dc=test,dc=com"
directory       "/var/lib/ldap"
dbconfig set_cachesize 0 2097152 0
dbconfig set_lk_max_objects 1500
dbconfig set_lk_max_locks 1500
dbconfig set_lk_max_lockers 1500
index           objectClass eq
lastmod         on
checkpoint      512 30
```

ACL
```sh
access to attrs=userPassword,shadowLastChange
        by anonymous auth
        by self write
        by * none
access to dn.base="" by * read
access to *
        by self write
        by * read
```

Cấp quyền truy cập
```sh
access to [resources]
	by [who] [type of access granted]
	by [who] [type of access granted]
	by [who] [type of access granted]
```

## Configuring the LDAP Clients

Chỉnh sửa trong file cấu hình của client cài LDAP `/etc/ldap/ldap.conf` một số nội dung sau
```sh
BASE	dc=test,dc=com
URI	ldap://test.com ldap://test.com:666
BINDDN	cn=admin,dc=test,dc=com
SIZELIMIT	0
TIMELIMIT	0
TLS_CACERT	/etc/ssl/certs/ca-certificates.crt
```

Giải thích một số tham số:
- URI: chỉ cho client kết nối tới server, nhiều địa chỉ thì cách nhau bằng khoảng trắng
- BASE: Chỉ cho client nơi bắt đầu tìm kiếm trong thư mục
- BINDDN: Chỉ định một DN cụ thể khi kết nối tới server
- sizelimit và timelimit: chỉ ra số bản ghi và thời gian đợi server phản hồi.

## Testing to Server

lệnh tham khảo
```sh
# ldapsearch -H ldap://test.com -x -W -D 'cn=admin,dc=test,dc=com' -s base

# ldapsearch -x -w tan124 -H ldap://test.com -D 'cn=admin,dc=test,dc=com' -b 'ou=users,dc=test,dc=com' '(uid=unguyen)'
```

lệnh xóa một entry
```sh
#  ldapdelete -D 'cn=admin,dc=test,dc=com' -w tan124 "uid=tannt,ou=people,dc=test,dc=com"
```

# Tham khảo
- [https://www.google.com.vn/url?sa=t&rct=j&q=&esrc=s&source=web&cd=2&cad=rja&uact=8&ved=0ahUKEwi_uYLi55jRAhUDG5QKHdZoBj4QFgggMAE&url=https%3A%2F%2Ftazlambert.files.wordpress.com%2F2008%2F05%2Fpacktpublishingmasteringopenldapaug20071847191029.pdf&usg=AFQjCNFh_nemlQgtx5FQINp_LbajJ3TtPQ&sig2=RhzCm_KbeMhXKDaNvAbeBw](https://www.google.com.vn/url?sa=t&rct=j&q=&esrc=s&source=web&cd=2&cad=rja&uact=8&ved=0ahUKEwi_uYLi55jRAhUDG5QKHdZoBj4QFgggMAE&url=https%3A%2F%2Ftazlambert.files.wordpress.com%2F2008%2F05%2Fpacktpublishingmasteringopenldapaug20071847191029.pdf&usg=AFQjCNFh_nemlQgtx5FQINp_LbajJ3TtPQ&sig2=RhzCm_KbeMhXKDaNvAbeBw)
