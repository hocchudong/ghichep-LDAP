# Chuẩn bị

- Thực hiện thử nghiệm dựng Openstack mitaka sử dụng LDAP cho thành phần identity của keystone.
- Tôi cài đặt bằng 02 cách: 
	- Cài đặt bằng script, và sử dụng một hệ thống LDAP tách biệt
	- Cài đặt bằng devstack, có cài thêm LDAP trên node nội tại luôn.
- Hệ thống máy chủ thực hiện cài đặt chạy trên vmware, các máy chủ đều có 02 interface

# Cài đặt OpenStack và LDAP bằng devstack

- Cập nhật và nâng cấp HĐH
```sh
apt-get update -y && apt-get upgrade -y && apt-get dist-upgrade -y && init 6
```

- Tạo user stack
```sh
apt-get -y install sudo git

adduser stack

echo "stack ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
```

- Chuyển sang tài khoản devstack
```sh
su - stack
```

- Tải gói và cài đặt
```sh
Tải gói mới nhất: 

 git clone https://github.com/openstack-dev/devstack.git

Hoặc chỉ định gói:
 git clone -b stable/mitaka https://github.com/openstack-dev/devstack.git
```

- Tạo tập tin `local.conf` để thiết lập các tham số cấu hình với nội dung sau
```sh
stack@devstack1:~/devstack$ cat local.conf 

[[local|localrc]]
DEST=/opt/stack

# Logging
LOGFILE=$DEST/logs/stack.sh.log
VERBOSE=True
SCREEN_LOGDIR=$DEST/logs/screen
OFFLINE=False

# Controller NODE IP
HOST_IP=172.16.68.133

# Credentials
ADMIN_PASSWORD=openstack
MYSQL_PASSWORD=$ADMIN_PASSWORD
RABBIT_PASSWORD=$ADMIN_PASSWORD
SERVICE_PASSWORD=$ADMIN_PASSWORD
SERVICE_TOKEN=$ADMIN_PASSWORD

MYSQL_HOST=$HOST_IP
RABBIT_HOST=$HOST_IP
CINDER_SERVICE_HOST=$HOST_IP
GLANCE_HOSTPORT=$HOST_IP:9292

##########################
# Khai bao cac project
##########################
ENABLED_SERVICES=key,n-api,n-crt,n-obj,n-cpu,n-cond,n-sch,n-novnc,n-xvnc,n-cauth,horizon,mysql,rabbit,ldap,g-api,g-reg

IDENTITY_API_VERSION=3

## Keystone su dung LDAP
KEYSTONE_IDENTITY_BACKEND=ldap
KEYSTONE_CLEAR_LDAP=yes
LDAP_PASSWORD=9632

##  SWIFT Service
# enable_service s-proxy
# enable_service s-object
# enable_service s-container
# enable_service s-account

## Khia bao tuy chon cho SWIFT
# SWIFT_REPLICAS=1
# SWIFT_HASH=66a3d6b56c1f479c8b4e70ab5c2000f5

##  CINDER Service
enable_service c-api
enable_service c-sch
enable_service c-bak
enable_service c-vol

##  CINDER Service
## Khia bao tuy chon cho CINDER
VOLUME_GROUP="stack-volumes"
VOLUME_NAME_PREFIX="volume-"

## NEUTRON Service
enable_service q-svc
enable_service q-agt
enable_service q-dhcp
enable_service q-meta
enable_service q-l3

## Khai bao cac tham so cho neutron
# ml2
Q_PLUGIN=ml2
Q_AGENT=openvswitch

# vxlan
Q_ML2_TENANT_NETWORK_TYPE=vxlan

# Networking
FLOATING_RANGE=172.16.68.0/24
Q_FLOATING_ALLOCATION_POOL=start=172.16.68.30,end=172.16.68.50
PUBLIC_NETWORK_GATEWAY=172.16.68.1

# Khai bao dai mang private
FIXED_RANGE=192.168.10.0/24
NETWORK_GATEWAY=192.168.10.1

PUBLIC_INTERFACE=eth1

Q_USE_PROVIDERNET_FOR_PUBLIC=True
Q_L3_ENABLED=True
Q_USE_SECGROUP=True
OVS_PHYSICAL_BRIDGE=br-ex
PUBLIC_BRIDGE=br-ex
OVS_BRIDGE_MAPPINGS=public:br-ex

Q_ML2_PLUGIN_PATH_MTU=1454

# Setup phien ban IP se su dung
IP_VERSION=4

# Khong can su dung tempest
disable_service tempest
disable_service n-net
```

- Chạy script để tiến hành cài đặt
```sh
stack@devstack1:~/devstack$ ./stack.sh
```

**Note**: Rất hên xui nhé. nhớ rửa tay trước khi chạy. :D Lỗi chỉ chui ra sao khi script chạy dc khoảng 1h30p.

## Kết quả

Sau khi cài đặt xong devstack với cấu hình local.conf có LDAP, ta sẽ có một hệ thống OpenStack All in One, tức là mọi thứ được cài trên 01 máy tính duy nhất.
Ta kiểm tra lại hệ thống OpenStack vừa dựng bằng các lệnh:

- Kiểm tra cây thư mục LDAP, kết quả là một cây thư mục hoàn chỉnh của OpenStack gồm Projects, Roles, UserGroups, Users
```sh
# slapcat 
```

![ldap-tree](/Images/ldap-tree.png)

- Kiểm tra lại OpenStack bằng các lênh:
```sh
openstack user list
openstack token issue
openstack project list
openstack group list
openstack role list
openstack domain list
```

*Lưu ý*: cần export các biến môi trường để thực hiện chạy các lệnh trên, tạo tập tin biến môi trường với nội dung sau:
```sh
export OS_IDENTITY_API_VERSION=3
export OS_AUTH_URL=http://172.16.68.133:5000/v3
export OS_USERNAME=admin
export OS_PROJECT_NAME=admin
export OS_USER_DOMAIN_NAME=Default
export OS_PASSWORD=openstack
export OS_PROJECT_DOMAIN_NAME=Default
```

sau đó sử dụng lệnh `source openrc` để export các biến này vào phiên shell đang làm việc.

- Hiện tại ta có một hệ thống OpenStack với project keystone chạy trên 02 backend. mặc định là SQL, còn identity sử dụng LDAP.
Có thể xem cấu hình trong keystone để biết rõ hơn.
```sh
root@devstack1:/opt/stack# cat /etc/keystone/keystone.conf |egrep -v "^#|^$"
[DEFAULT]
max_token_size = 16384
logging_exception_prefix = %(asctime)s.%(msecs)03d %(process)d TRACE %(name)s %(instance)s
debug = True
admin_endpoint = http://172.16.68.133/identity_v2_admin
public_endpoint = http://172.16.68.133/identity
transport_url = rabbit://stackrabbit:openstack@172.16.68.133:5672/
member_role_name = _member_
member_role_id = 9fe2ff9ee4384b1894a90878d3e92bab
[assignment]
driver = sql
[cache]
memcache_servers = localhost:11211
backend = oslo_cache.memcache_pool
enabled = True
[credential]
key_repository = /etc/keystone/credential-keys/
[database]
connection = mysql+pymysql://root:openstack@172.16.68.133/keystone?charset=utf8
[fernet_tokens]
key_repository = /etc/keystone/fernet-keys/
[identity]
driver = ldap
[ldap]
user_tree_dn = ou=Users,dc=openstack,dc=org
user_domain_id_attribute = businessCategory
tenant_tree_dn = ou=Projects,dc=openstack,dc=org
tenant_desc_attribute = description
tenant_domain_id_attribute = businessCategory
tenant_attribute_ignore = enabled
user_attribute_ignore = enabled,email,tenants,default_project_id
use_dumb_member = True
suffix = dc=openstack,dc=org
user = cn=Manager,dc=openstack,dc=org
password = 9632
[paste_deploy]
config_file = /etc/keystone/keystone-paste.ini
[resource]
admin_project_name = admin
admin_project_domain_name = Default
driver = sql
[role]
driver = sql
[token]
driver = sql
```

- Ta thực hiện tạo một người dùng trong OpenStack để kiểm tra thông tin người dùng trong keystone được lưu trữ như nào. Thực hiện chạy các lệnh sau:
```sh
openstack project create tannt_project --domain default --description "tannt test project"
openstack user create tannt --email tannt@openstack.com --domain default --description "tannt openstack user account" --password xxxxx
openstack role add Member --project tannt_project --project-domain default --user tannt --user-domain default
```

Người dùng đươc tạo trong một domain mặc định cùng với role mặc định. 03 lệnh trên là: tạo project, tạo người dùng, gán role cho người dùng.

- Một người dùng mới được tạo ra, sẽ có các sự kiện sau:
	- Gán người dùng vào LDAP
	- Gán role người dùng trong bảng `assignment` của MySQL
	- Gán thông tin người dùng vào bảng `nonlocal_user` trong MySQL
	- Gán project mới tạo vào bảng `project` trong MySQL
	- Thêm tên người dùng vào bảng `user` trong MySQL

- Khi người dùng thực hiện đăng nhập vào horizon sẽ sinh ra một token, và token này được ghi vào bảng `token` trong MySQL. 
Check bằng lệnh *SELECT * FROM `token` WHERE user_id = 'f8afdf4455b94bd6ba20bdeef05ba732';* Token có thời gian mặc định là 1 tiếng, sau đó sẽ phải gen lại.

- Kiểm tra cấu trúc lưu trữ user vừa được tạo phía trên trong database MySQL
	- bảng assignment lưu trữ: 
```sh
type		actor_id							target_id							role_id								inherited
UserProject	f8afdf4455b94bd6ba20bdeef05ba732	f931ad962bf444458fa5d39c7d3c8aff	ae7661eaf25f4b45ab3f1120d6b6601c	0
```
	- bảng nonlocal_user:
```sh
domain_id	name	user_id
default		tannt	f8afdf4455b94bd6ba20bdeef05ba732
```
	- Bảng project:
```sh
id									name			extra	description				enabled		domain_id	parent_id	is_domain
f931ad962bf444458fa5d39c7d3c8aff	tannt_project	{}		tannt test project		1			default		default		0
```
	- Bảng user
```sh
id									extra											enabled	default_project_id	created_at			last_active_at
f8afdf4455b94bd6ba20bdeef05ba732	{"description": "tannt openstack user account"}	\N		\N					2017-01-26 20:32:55	\N
```
	- Thông tin trong LDAP tree
```sh
dn: cn=f8afdf4455b94bd6ba20bdeef05ba732,ou=Users,dc=openstack,dc=org
objectClass: person
objectClass: inetOrgPerson
description: tannt openstack user account
cn: f8afdf4455b94bd6ba20bdeef05ba732
userPassword:: dGFuQDEyMysr
sn: tannt
structuralObjectClass: inetOrgPerson
entryUUID: c725f7b2-7850-1036-8fa0-ed18e1693809
creatorsName: cn=Manager,dc=openstack,dc=org
createTimestamp: 20170126202135Z
entryCSN: 20170126202135.119130Z#000000#000#000000
modifiersName: cn=Manager,dc=openstack,dc=org
modifyTimestamp: 20170126202135Z
```

==> Phía trên là thông tin của user thực hiện bằng câu lệnh openstack. thông tin xử lý bởi OpenStack và ghi vào MySQL cùng LDAP.

## Thêm User thủ công vào LDAP và gán quyền trong OpenStack

- Bây giờ ta thử thực hiện tạo username trên LDAP, sau đó gán assignment và role một cách thủ công.

- Tạo tập tin usernew.ldif với nội dung sau:
```sh
dn: cn=hangnt,ou=Users,dc=openstack,dc=org
objectClass: person
objectClass: inetOrgPerson
description: hangnt openstack user account
cn: hangnt
userPassword:: dGFuQDEyMysr
sn: hangnt
```

- Chạy lệnh sau để thêm user vào ldap, passwd LDAP tree khi cái bằng devstack là 9632
```sh
ldapadd -x -D cn=Manager,dc=openstack,dc=org -W -f usernew.ldif
```

- Lệnh xóa user trong trường hợp tạo sai thông tin:
```sh
ldapdelete -x -W -D 'cn=Manager,dc=openstack,dc=org' "cn=hangnt,ou=Users,dc=openstack,dc=org"
```

- Kiểm tra thông tin username vừa tạo:
```sh
# slapcat
...
dn: cn=hangnt,ou=Users,dc=openstack,dc=org
objectClass: person
objectClass: inetOrgPerson
description: hangnt openstack user account
cn: hangnt
userPassword:: dGFuQDEyMysr
sn: hangnt
structuralObjectClass: inetOrgPerson
entryUUID: f6dfd938-786d-1036-8fa1-ed18e1693809
creatorsName: cn=Manager,dc=openstack,dc=org
createTimestamp: 20170126235030Z
entryCSN: 20170126235030.588724Z#000000#000#000000
modifiersName: cn=Manager,dc=openstack,dc=org
modifyTimestamp: 20170126235030Z
```

- Thêm thông tin vào bảng assignment cho user vừa tạo, gán user này với project tannt và role member ứng với user tannt ở trên.
```sh
INSERT INTO `keystone`.`assignment` (`type`, `actor_id`, `target_id`, `role_id`, `inherited`) VALUES ('UserProject', 'hangnt', 'f931ad962bf444458fa5d39c7d3c8aff', 'ae7661eaf25f4b45ab3f1120d6b6601c', '0'); 
```

**Note**: Chỉ thực hiện thêm user vào LDAP tree và gán role, project cho username vào bảng assignment trong MySQL.

# Cài đặt OpenStack bằng script và sử dụng LDAP external

Trong phần này tôi sẽ thực hiện cài OpenStack bằng script, sau đó cấu hình cho keystone sử dụng LDAP.

- Cài đặt OpenStack bằng hướng dẫn [sau](https://github.com/congto/OpenStack-Mitaka-Scripts/tree/master/OPS-Mitaka-OVS-Ubuntu)

- Việc xây dựng LDAP tree không có khó khăn gì. Đầu tiên ta cài đặt LDAP như bình thường theo hướng dẫn 
[sau](https://github.com/TrongTan124/ghichep-LDAP/blob/master/docs/TanNT-LDAP-OpenLDAP.md)

## Cấu hình LDAP tree trên LDAP server

- Trước tiên cần cài đặt và cấu hình LDAP. 

- Để xây dựng được cấu trúc LDAP tree, sau khi cài đặt LDAP xong, ta tạo tập tin cấu trúc như dưới. lưu ý là cn của `ou=Roles` _member_ mặc định
`9fe2ff9ee4384b1894a90878d3e92bab`:
```sh
root@ldapserver:~# cat openstack.ldif 
dn: ou=Groups,dc=vnptdata,dc=vn
objectClass: organizationalUnit
ou: Groups

dn: ou=Users,dc=vnptdata,dc=vn
objectClass: organizationalUnit
ou: Users

dn: ou=Roles,dc=vnptdata,dc=vn
objectClass: organizationalUnit
ou: Roles

dn: ou=Projects,dc=vnptdata,dc=vn
objectClass: organizationalUnit
ou: Projects

dn: cn=9fe2ff9ee4384b1894a90878d3e92bab,ou=Roles,dc=vnptdata,dc=vn
objectClass: organizationalRole
ou: _member_
cn: 9fe2ff9ee4384b1894a90878d3e92bab
```

- Thực hiện import cấu trúc vào LDAP tree
```sh
ldapadd -x -D cn=admin,dc=vnptdata,dc=vn -W -f openstack.ldif
```

- Thực hiện thêm các user của project OpenStack vào LDAP, password là tan124 được thiết lập bằng ldapadmin, method mã hóa SHA1. Tạo tập tin user project
```sh
root@ldapserver:~# cat user2.ldif 
dn: cn=c4f656354aa1437ba17d1275cfa84773,ou=Users,dc=vnptdata,dc=vn
objectClass: person
objectClass: inetOrgPerson
userPassword:: b3BlbnN0YWNr
sn: admin
cn: c4f656354aa1437ba17d1275cfa84773

dn: cn=3ae9bd7d69c84c2787649f2e9119c243,ou=Users,dc=vnptdata,dc=vn
objectClass: person
objectClass: inetOrgPerson
sn: demo
cn: 3ae9bd7d69c84c2787649f2e9119c243

dn: cn=a37554325f454af58a5a133ab734ccb7,ou=Users,dc=vnptdata,dc=vn
objectClass: person
objectClass: inetOrgPerson
cn: a37554325f454af58a5a133ab734ccb7
sn: nova

dn: cn=3d733c4239f440c6b12e67f523c56d2d,ou=Users,dc=vnptdata,dc=vn
objectClass: person
objectClass: inetOrgPerson
userPassword:: b3BlbnN0YWNr
cn: 3d733c4239f440c6b12e67f523c56d2d
sn: glance

dn: cn=2a22bf794cdc45d2b1408d73b58f3307,ou=Users,dc=vnptdata,dc=vn
objectClass: person
objectClass: inetOrgPerson
userPassword:: b3BlbnN0YWNr
cn: 2a22bf794cdc45d2b1408d73b58f3307
sn: neutron
```

- Tạo tập tin người dùng mới như sau:
```sh
root@ldapserver:~# cat newuser.ldif
dn: cn=tannt,ou=Users,dc=vnptdata,dc=vn
objectClass: person
objectClass: inetOrgPerson
description: tannt openstack user account
cn: tannt
userPassword:: dGFuQDEyMysr
sn: tannt
```

- Thực hiện thêm người dùng mới vào LDAP tree. nhớ nhập password của LDAP
```sh
ldapadd -x -D cn=admin,dc=vnptdata,dc=vn -W -f newuser.ldif
```

- Dưới đây là cấu hình LDAP tree mà tôi dựng cho hệ thống OpenStack sử dụng. 
Password đăng nhập của user tannt là xxxxx
```sh
root@ldapserver:~# slapcat
dn: dc=vnptdata,dc=vn
objectClass: top
objectClass: dcObject
objectClass: organization
o: vnptdata.vn
dc: vnptdata
structuralObjectClass: organization
entryUUID: c1f11512-756f-1036-8fc9-fbb896cd667f
creatorsName: cn=admin,dc=vnptdata,dc=vn
createTimestamp: 20170123042547Z
entryCSN: 20170123042547.299721Z#000000#000#000000
modifiersName: cn=admin,dc=vnptdata,dc=vn
modifyTimestamp: 20170123042547Z

dn: cn=admin,dc=vnptdata,dc=vn
objectClass: simpleSecurityObject
objectClass: organizationalRole
cn: admin
description: LDAP administrator
userPassword:: e1NTSEF9MDdGakc4Q1oweHBIUkFpSHY1Sm1yQVFqWnZlZnl1cEQ=
structuralObjectClass: organizationalRole
entryUUID: c1f56716-756f-1036-8fca-fbb896cd667f
creatorsName: cn=admin,dc=vnptdata,dc=vn
createTimestamp: 20170123042547Z
entryCSN: 20170123042547.328026Z#000000#000#000000
modifiersName: cn=admin,dc=vnptdata,dc=vn
modifyTimestamp: 20170123042547Z

dn: ou=Groups,dc=vnptdata,dc=vn
objectClass: organizationalUnit
ou: Groups
structuralObjectClass: organizationalUnit
entryUUID: 3b900720-7570-1036-9634-9148eb18cfbc
creatorsName: cn=admin,dc=vnptdata,dc=vn
createTimestamp: 20170123042911Z
entryCSN: 20170123042911.345695Z#000000#000#000000
modifiersName: cn=admin,dc=vnptdata,dc=vn
modifyTimestamp: 20170123042911Z

dn: ou=Users,dc=vnptdata,dc=vn
objectClass: organizationalUnit
ou: Users
structuralObjectClass: organizationalUnit
entryUUID: 3b9090e6-7570-1036-9635-9148eb18cfbc
creatorsName: cn=admin,dc=vnptdata,dc=vn
createTimestamp: 20170123042911Z
entryCSN: 20170123042911.349226Z#000000#000#000000
modifiersName: cn=admin,dc=vnptdata,dc=vn
modifyTimestamp: 20170123042911Z

dn: ou=Roles,dc=vnptdata,dc=vn
objectClass: organizationalUnit
ou: Roles
structuralObjectClass: organizationalUnit
entryUUID: 3b90fd4c-7570-1036-9636-9148eb18cfbc
creatorsName: cn=admin,dc=vnptdata,dc=vn
createTimestamp: 20170123042911Z
entryCSN: 20170123042911.352000Z#000000#000#000000
modifiersName: cn=admin,dc=vnptdata,dc=vn
modifyTimestamp: 20170123042911Z

dn: ou=Projects,dc=vnptdata,dc=vn
objectClass: organizationalUnit
ou: Projects
structuralObjectClass: organizationalUnit
entryUUID: 3b928bc6-7570-1036-9637-9148eb18cfbc
creatorsName: cn=admin,dc=vnptdata,dc=vn
createTimestamp: 20170123042911Z
entryCSN: 20170123042911.362201Z#000000#000#000000
modifiersName: cn=admin,dc=vnptdata,dc=vn
modifyTimestamp: 20170123042911Z

dn: cn=9fe2ff9ee4384b1894a90878d3e92bab,ou=Roles,dc=vnptdata,dc=vn
objectClass: organizationalRole
ou: _member_
cn: 9fe2ff9ee4384b1894a90878d3e92bab
structuralObjectClass: organizationalRole
entryUUID: 3b92e846-7570-1036-9638-9148eb18cfbc
creatorsName: cn=admin,dc=vnptdata,dc=vn
createTimestamp: 20170123042911Z
entryCSN: 20170123042911.364569Z#000000#000#000000
modifiersName: cn=admin,dc=vnptdata,dc=vn
modifyTimestamp: 20170123042911Z

dn: cn=c4f656354aa1437ba17d1275cfa84773,ou=Users,dc=vnptdata,dc=vn
objectClass: person
objectClass: inetOrgPerson
sn: admin
cn: c4f656354aa1437ba17d1275cfa84773
structuralObjectClass: inetOrgPerson
entryUUID: 69e9dfe6-7594-1036-963a-9148eb18cfbc
creatorsName: cn=admin,dc=vnptdata,dc=vn
createTimestamp: 20170123084810Z
userPassword:: e1NIQX1xaEtFUHpjd1hPSityTlBJemxNaCtSS3J6Nkk9
entryCSN: 20170123085054.094626Z#000000#000#000000
modifiersName: cn=admin,dc=vnptdata,dc=vn
modifyTimestamp: 20170123085054Z

dn: cn=3ae9bd7d69c84c2787649f2e9119c243,ou=Users,dc=vnptdata,dc=vn
objectClass: person
objectClass: inetOrgPerson
sn: demo
cn: 3ae9bd7d69c84c2787649f2e9119c243
structuralObjectClass: inetOrgPerson
entryUUID: 69fd2fce-7594-1036-963b-9148eb18cfbc
creatorsName: cn=admin,dc=vnptdata,dc=vn
createTimestamp: 20170123084811Z
entryCSN: 20170123084811.118541Z#000000#000#000000
modifiersName: cn=admin,dc=vnptdata,dc=vn
modifyTimestamp: 20170123084811Z

dn: cn=a37554325f454af58a5a133ab734ccb7,ou=Users,dc=vnptdata,dc=vn
objectClass: person
objectClass: inetOrgPerson
cn: a37554325f454af58a5a133ab734ccb7
sn: nova
structuralObjectClass: inetOrgPerson
entryUUID: 69fdc5ba-7594-1036-963c-9148eb18cfbc
creatorsName: cn=admin,dc=vnptdata,dc=vn
createTimestamp: 20170123084811Z
entryCSN: 20170123084811.122379Z#000000#000#000000
modifiersName: cn=admin,dc=vnptdata,dc=vn
modifyTimestamp: 20170123084811Z

dn: cn=3d733c4239f440c6b12e67f523c56d2d,ou=Users,dc=vnptdata,dc=vn
objectClass: person
objectClass: inetOrgPerson
cn: 3d733c4239f440c6b12e67f523c56d2d
sn: glance
structuralObjectClass: inetOrgPerson
entryUUID: 69fe51b0-7594-1036-963d-9148eb18cfbc
creatorsName: cn=admin,dc=vnptdata,dc=vn
createTimestamp: 20170123084811Z
userPassword:: e1NIQX1xaEtFUHpjd1hPSityTlBJemxNaCtSS3J6Nkk9
entryCSN: 20170123085031.371224Z#000000#000#000000
modifiersName: cn=admin,dc=vnptdata,dc=vn
modifyTimestamp: 20170123085031Z

dn: cn=2a22bf794cdc45d2b1408d73b58f3307,ou=Users,dc=vnptdata,dc=vn
objectClass: person
objectClass: inetOrgPerson
cn: 2a22bf794cdc45d2b1408d73b58f3307
sn: neutron
structuralObjectClass: inetOrgPerson
entryUUID: 6a000e92-7594-1036-963e-9148eb18cfbc
creatorsName: cn=admin,dc=vnptdata,dc=vn
createTimestamp: 20170123084811Z
userPassword:: e1NIQX1xaEtFUHpjd1hPSityTlBJemxNaCtSS3J6Nkk9
entryCSN: 20170123085123.830620Z#000000#000#000000
modifiersName: cn=admin,dc=vnptdata,dc=vn
modifyTimestamp: 20170123085123Z

dn: cn=tannt,ou=Users,dc=vnptdata,dc=vn
objectClass: person
objectClass: inetOrgPerson
description: tannt openstack user account
cn: tannt
userPassword:: dGFuQDEyMysr
sn: tannt
structuralObjectClass: inetOrgPerson
entryUUID: 227ef85a-8ba4-1036-84cf-d3c4b4dcc487
creatorsName: cn=admin,dc=vnptdata,dc=vn
createTimestamp: 20170220103608Z
entryCSN: 20170220103608.701913Z#000000#000#000000
modifiersName: cn=admin,dc=vnptdata,dc=vn
modifyTimestamp: 20170220103608Z
```

- Mật khẩu người dùng mới có thể thay đổi bằng công cụ [ldapadmin](http://www.ldapadmin.org/)

## Chỉnh sửa cấu hình keystone

- Để keystone sử dụng được LDAP, cần cài đặt bổ sung thư viện python. Trong quá trình test tôi cài đặt khá nhiều gói, nên không rõ gói nào cần thiết nữa. 
Cái này cần thử nghiệm lại để loại bớt gói không cần thiết.
```sh
root@controller1:/etc/keystone# dpkg -l | grep ldap
ii  erlang-eldap                       1:16.b.3-dfsg-1ubuntu2.1              amd64        Erlang/OTP LDAP library
ii  ldap-auth-client                   0.5.3                                 all          meta-package for LDAP authentication
ii  ldap-auth-config                   0.5.3                                 all          Config package for LDAP authentication
ii  ldap-utils                         2.4.31-1+nmu2ubuntu8.3                amd64        OpenLDAP utilities
ii  libaprutil1-ldap:amd64             1.5.3-1                               amd64        Apache Portable Runtime Utility Library - LDAP Driver
ii  libldap-2.4-2:amd64                2.4.31-1+nmu2ubuntu8.3                amd64        OpenLDAP libraries
ii  libldap2-dev:amd64                 2.4.31-1+nmu2ubuntu8.3                amd64        OpenLDAP development libraries
ii  libnet-ldap-perl                   1:0.5800-1                            all          client interface to LDAP servers
ii  libnss-ldap:amd64                  264-2.2ubuntu4.14.04.2                amd64        NSS module for using LDAP as a naming service
ii  libpam-ldap:amd64                  184-8.5ubuntu3                        amd64        Pluggable Authentication Module for LDAP
ii  python-ldap                        2.4.22-0.1~cloud0                     amd64        LDAP interface module for Python
ii  python-ldappool                    1.0-1ubuntu1~cloud0                   all          connection pool for python-ldap
ii  python-ldaptor                     0.0.43+debian1-7                      all          pure-Python library for LDAP operations
```

- Một số lệnh cài đặt như sau:
```sh
apt-get install libnet-ldap-perl
apt-get install python-dev libldap2-dev libsasl2-dev libssl-dev
apt-get -y install libnss-ldap libpam-ldap ldap-utils
apt-get install python-ldap
apt-get install python-ldaptor
apt-get install python-ldappool
```

- Cấu hình identity sử dụng LDAP bên ngoài, cần chỉnh sửa cấu hình trong file `/etc/keystone.conf` như sau:
```sh
root@controller1:/etc/keystone# cat keystone.conf|egrep -v "^$|^#"
[DEFAULT]
debug = True
log_dir = /var/log/keystone
admin_token = tan124
[assignment]
driver = sql
[cache]
memcache_servers = localhost:11211
backend = oslo_cache.memcache_pool
enabled = True
[database]
connection = mysql+pymysql://keystone:tan124@10.10.10.13/keystone
[identity]
default_domain_id = 7a08cab061a8450d868a7ce0399856ad
driver = ldap
[ldap]
user_domain_id_attribute = businessCategory
tenant_tree_dn = ou=Projects,dc=vnptdata,dc=vn
tenant_desc_attribute = description
tenant_domain_id_attribute = businessCategory
tenant_attribute_ignore = enabled
url = ldap://vnptdata.vn
user = cn=admin,dc=vnptdata,dc=vn
password = tan124
suffix = dc=vnptdata,dc=vn
use_dumb_member = true
user_tree_dn = ou=Users,dc=vnptdata,dc=vn
user_attribute_ignore = default_project_id,enabled,email,tenants
[resource]
driver = sql
[role]
driver = sql
[token]
provider = fernet
driver = sql
[extra_headers]
Distribution = Ubuntu
```

- Trong cấu hình của keystone, ta chỉ định một domain mặc định trong quá trình sử dụng bằng biến `default_domain_id`

- User mới thêm vào LDAP tree, cần được gán vào role và project cụ thể mới có thể sử dụng được. 
Thông tin về project_id và role_id lấy từ lệnh của OpenStack
```sh
root@controller1:/etc/keystone# openstack project list;
+----------------------------------+---------+
| ID                               | Name    |
+----------------------------------+---------+
| 275c91cdf760484d90f86ea0c1458334 | demo    |
| b0ddb1b01ba94969b4f4bace011fa1b0 | admin   |
| bf8cfb4b8a5f4940bce3d980fa3d6d41 | service |
+----------------------------------+---------+
root@controller1:/etc/keystone# openstack role list;
+----------------------------------+-------+
| ID                               | Name  |
+----------------------------------+-------+
| 5bde76879ca54795a48c68026b4a4dc7 | admin |
| f31331e443d34968a97f7d39bb771a38 | user  |
+----------------------------------+-------+
```

-  lệnh gán assignment cho user mới vào database keystone.
```sh
INSERT INTO `keystone`.`assignment` (`type`, `actor_id`, `target_id`, `role_id`, `inherited`) 
VALUES ('UserProject', 'tannt', 'b0ddb1b01ba94969b4f4bace011fa1b0', '5bde76879ca54795a48c68026b4a4dc7', '0');
```

# TLS for LDAP

## Server

Trong phần này, tôi sẽ cấu hình LDAP sử dụng tls để mã hóa đường truyền.

Đầu tiên, trên LDAP server ta sử dụng openssl gen key cho việc mã hóa cũng như import vào LDAP tree

- Nếu LDAP server chưa có openssl thì cài gói sau, nhưng hầu hết các distro đều cài mặc định openssl.
```sh
apt-get install openssl
```

- Ta thêm đường dẫn sau vào PATH của hệ điều hành, để có thể sử dụng script CA.sh gen key
```sh
export PATH=$PATH:/usr/lib/ssl/misc
```

- Tùy chỉnh lại các thông tin cấu hình trong file `/usr/lib/ssl/openssl.cnf` như sau
```sh
...
[ req ]
default_bits    = 2048
...
[ req_distinguished_name ]
countryName_default		= VN
stateOrProvinceName_default	= HaNoi
0.organizationName_default	= VNPT DATA
...
```

- Tạo thư mục để lưu các khóa
```sh
mkdir ~/ca && cd ~/ca
```

- Bây giờ ta tạo một file CA mới như sau:
```
ldap1:~/ca# CA.sh -newca
CA certificate filename (or enter to create)

Making CA certificate ...
Generating a 2048 bit RSA private key
............+++
........+++
writing new private key to './demoCA/private/./cakey.pem'
Enter PEM pass phrase: **************
Verifying - Enter PEM pass phrase: **************
...
Country Name (2 letter code) [UA]:VN
State or Province Name (full name) [LV]:HN
Locality Name (eg, city) []:HaNoi
Organization Name (eg, company) [XYZ Co]:VNPT DATA
Organizational Unit Name (eg, section) []:
Common Name (eg, YOUR name) []:vnptdata.vn
Email Address []:nguyentrongtan@vnpt.vn

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:123456
An optional company name []:VNPT DATA
Using configuration from /usr/lib/ssl/openssl.cnf
Enter pass phrase for ./demoCA/private/./cakey.pem: *****
Check that the request matches the signature
Signature ok
Certificate Details:
...
Write out database with 1 new entries
Data Base Updated
```

- Kết quả của lệnh trên sẽ tạo ra một tập tin certificate tên là `cacert.pem` trong thư mục `~/ca/demoCA`. Ta gán lại quyền đọc cho thư mục gen key như sau:
```sh
chmod -R go-rwx ~/ca
```

- Sau khi tạo được tập tin certificate như trên, ta phải thực hiện tiếp 02 bước sau:
	- create certificate request
	- sign request by certificate authority. bước này sử dụng certificate `cacert.pem` vừa tạo được ở trên

- Certificate Request
```sh
ldap1:~/ca# openssl req -new -nodes -keyout newreq.pem -out newreq.pem
Generating a 2048 bit RSA private key
.....................+++
....................................+++
writing new private key to 'newreq.pem'
...
Country Name (2 letter code) [UA]: VN
State or Province Name (full name) [LV]:HN
Locality Name (eg, city) []:HaNoi
Organization Name (eg, company) [XYZ Co]:VNPT DATA
Organizational Unit Name (eg, section) []:
Common Name (eg, YOUR name) []:vnptdata.vn
Email Address []:nguyentrongtan@vnpt

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:123456
An optional company name []:VNPT DATA
```

- sign request
```sh
ldap1:~/ca# /usr/lib/ssl/misc/CA.sh -sign
Using configuration from /usr/lib/ssl/openssl.cnf
Enter pass phrase for ./demoCA/private/cakey.pem: 123456
Check that the request matches the signature
Signature ok
Certificate Details:
...       
Certificate is to be certified until XXX (365 days)
Sign the certificate? [y/n]:y

1 out of 1 certificate requests certified, commit? [y/n]y
Write out database with 1 new entries
Data Base Updated
Certificate:
    ...
Signed certificate is in newcert.pem
```

- Sau 02 bước trên, chúng ta đã tạo ra được 02 tập tin: `newreq.pem` và `newcert.pem`. Chúng ta có thể đổi tên thành ldap-key.pem và ldap-cert.pem để 
sử dụng. hoặc có thể kết hợp 02 tập tin thành một như sau:
```sh
cat newreq.pem newcert.pem > new.pem
```

- Sau khi tạo xong key, bây giờ tới bước gắn key vào LDAP server và cấu hình CA cho client kết nối tới.

- Trên LDAP server, chúng ta cài đặt CA certificate như sau:
```sh
cp ~/ca/demoCA/cacert.pem /etc/ssl/certs/
chmod go+r /etc/ssl/certs/cacert.pem
```

- Copy ldap key and certificate files to /etc/ldap/ssl
```sh
mkdir /etc/ldap/ssl/
cp ~/ca/new*.pem /etc/ldap/ssl/
```

- Secure certificates:
```sh
ldap1:~# chown -R root:openldap /etc/ldap/ssl
ldap1:~# chmod -R o-rwx /etc/ldap/ssl
```

- Chỉnh sửa tập tin cấu hình `/etc/default/slapd` cho phép LDAP sử dụng kết nối bảo mật
```sh
LAPD_SERVICES="ldap://127.0.0.1:389/ ldaps:/// ldapi:///"
```

- Tạo một tập tin cấu hình cho LDAP `tls-config.ldif` nội dung sau:
```sh
dn: cn=config
add: olcTLSCACertificateFile
olcTLSCACertificateFile: /etc/ssl/certs/cacert.pem
-
add: olcTLSCertificateFile
olcTLSCertificateFile: /etc/ldap/ssl/newcert.pem
-
add: olcTLSCertificateKeyFile
olcTLSCertificateKeyFile: /etc/ldap/ssl/newreq.pem
```

- Apply it:
```sh
ldapmodify -QY EXTERNAL -H ldapi:/// -f tls-config.ldif
```

- Restart slapd:
```sh
/etc/init.d/slapd restart
```

- Ensure started:
```sh
netstat -tunlp | grep slapd
tcp        0      0 0.0.0.0:636             0.0.0.0:*               LISTEN      2462/slapd      
tcp        0      0 127.0.0.1:389           0.0.0.0:*               LISTEN      2462/slapd  
```

## Client

Ta gửi file CA certificate `cacert.pem` từ Server sang client.
```sh
root@controller1:/etc/keystone/ssl/certs# ll
-rw-r--r-- 1 keystone keystone 4448 Mar  7 18:46 cacert.pem
```

Trên client, tức controller, ta thực hiện chỉnh sửa lại cấu hình cho keystone sao cho giống với cấu hình sau:
```sh
...
[ldap]
user_domain_id_attribute = businessCategory
tenant_tree_dn = ou=Projects,dc=vnptdata,dc=vn
tenant_desc_attribute = description
tenant_domain_id_attribute = businessCategory
tenant_attribute_ignore = enabled
url = ldaps://vnptdata.vn:636
user = cn=admin,dc=vnptdata,dc=vn
password = tan124
suffix = dc=vnptdata,dc=vn
use_dumb_member = true
user_tree_dn = ou=Users,dc=vnptdata,dc=vn
user_attribute_ignore = default_project_id,enabled,email,tenants
tls_cacertfile = "/etc/keystone/ssl/certs/cacert.pem"
tls_cacertdir = "/etc/keystone/ssl/certs/"
use_tls = false
tls_req_cert = demand

...
```

Ở đây, ta cấu hình tương tự như trường hợp chưa sử dụng tls, chỉ tập trung vào 05 dòng sau:
```sh
url = ldaps://vnptdata.vn:636
tls_cacertfile = "/etc/keystone/ssl/certs/cacert.pem"
tls_cacertdir = "/etc/keystone/ssl/certs/"
use_tls = false
tls_req_cert = demand
```

- Khởi động lại apache2 để apply cấu hình
```sh
# /etc/init.d/apache2 restart
```

- bắt gói tin trên LDAP server để kiểm tra kết nối
```sh
root@ldapserver:~/ca# tcpdump -ne -i any host 172.16.68.13
```

- bật debug và tail log trên controller
```sh
[DEFAULT]
debug = True
log_dir = /var/log/keystone
```
```sh
root@controller1:/var/log/keystone# tail -f keystone-wsgi-admin.log
```

- Kiểm tra lại xem keystone đã hoạt động đúng chưa bằng cách thông thường là sử dụng openstackclient trên controller:
```sh
# source ~/admin-openrc
root@controller1:/etc/keystone/ssl/certs# openstack user list
+----------------------------------+---------+
| ID                               | Name    |
+----------------------------------+---------+
| c4f656354aa1437ba17d1275cfa84773 | admin   |
| 3ae9bd7d69c84c2787649f2e9119c243 | demo    |
| a37554325f454af58a5a133ab734ccb7 | nova    |
| 3d733c4239f440c6b12e67f523c56d2d | glance  |
| 2a22bf794cdc45d2b1408d73b58f3307 | neutron |
| tannt                            | tannt   |
+----------------------------------+---------+
```

# Tham khảo
- [http://www.ibm.com/developerworks/cloud/library/cl-ldap-keystone/](http://www.ibm.com/developerworks/cloud/library/cl-ldap-keystone/)
- [https://mindref.blogspot.com/2010/12/openssl-ca.html](https://mindref.blogspot.com/2010/12/openssl-ca.html)
- [https://mindref.blogspot.com/2010/12/openssl-create-certificates.html](https://mindref.blogspot.com/2010/12/openssl-create-certificates.html)
- [https://mindref.blogspot.com/2010/12/debian-openldap-ssl-tls-encryption.html](https://mindref.blogspot.com/2010/12/debian-openldap-ssl-tls-encryption.html)
