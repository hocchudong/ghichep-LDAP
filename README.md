# Các ghi chép về LDAP


###Các thuộc tính trong LDAP

- dn (Distinguished Name): Tên gọi phân biệt

- c (country): 2 kí tự viết tắt tên của một nước

- o (organization): tổ chức

- ou (organization unit): đơn vị tổ chức

- objectClass: mỗi giá trị objectClass hoạt động như mọt khuôn mẫu cho các dữ liệu được lưu giữ trong một entry (Ví dụ 1 entry có giá trị thuộc tính objectClass là person mà trong person có quy định cần có các thuộc tính là tên, email, uid,... thì entry này sẽ có các thuộc tính đó)

- givenName: Tên

- uid: ID của người dùng

- cn (common name): tên thường gọi

- telephoneNumber: Số điện thoại

- sn (surname): họ

- userPassword: Mật khẩu của người dùng

- mail: Địa chỉ email

- facsimileTelephoneNumber: Số phách

- createTimestamp: Thời gian tạo entry này

- creatorsName: tên người tạo ra entry này

- pwdChangedTime: Thời gian đã thay đổi mật khẩu

- entryUUID: ID của entry
