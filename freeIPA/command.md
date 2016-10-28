####Các lệnh cơ bản

#####Các lệnh về user

- Thêm một user

`ipa user-add username --first=FirstName --last=LastName --password`

- Lock hoặc unlock 1 user

`ipa user-disable username`

`ipa user-enable username`

- Tìm 1 user

`ipa user-find username`

- modify một user

`ipa user-mode username`

  - *--setattr=attribute=value*: Sửa hoặc set một attribute đã có
  - *--addattr=attribute=value*: Thêm một attribute mới
  - *--delattr=attribute=value*: Xoá một attribute

- Hiển thị 1 user

`ipa user-show --raw username`

- Xóa một user

`ip user-del username`

#####Các lệnh về group

- Tạo một group

`ipa group-add --desc="Description aboud group" groupname`

- THêm một member vào 1 group

`ipa group-add-member --users=username1,username2 groupname`

- Thêm một group vào 1 group

`ipa group-add-member --groups=group1 groupname`

- Tìm kiếm 1 group

`ipa group-find groupname`

- Xóa 1 group

`ipa group-del groupname`
