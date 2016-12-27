# Giới thiệu
----

LDAP, hay Lightweight Directory Access Protocol, là một giao thức mở được sử dụng cho việc lưu trữ và gọi lại dữ liệu từ một cấu trúc cây thư mục phân cấp. Thông thường được sử dụng để 
lưu trữ thông tin về một tổ chức và các tài sản cũng như người dùng của tổ chức ấy. LDAP là một giải pháp mềm dẻo cho việc khai báo các loại thực thể và đặc tính của nó.

Đối với nhiều người, LDAP có lẽ rất khó hiểu, vì nó dựa một thuật ngữ cụ thể, sử dụng một số từ viết tắt không phổ biến, và thường được thực thi như một phần trong tương tác của hệ 
thống lớn.

# What is a Directory Service?
----

Một dịch vụ thư mục được sử dụng để lưu trữ, tổ chức và trình diễn dữ liệu theo định dạng key-value. Thư mục được tối ưu cho việc tra cứu, tìm kiếm, và đọc hơn là ghi, vì vậy chúng hoạt động 
tốt với dữ liệu được truy xuất hơn là dữ liệu thường xuyên thay đổi.

Dữ liệu được lưu trữ trong dịch vụ thư mục thường được mô tả một cách tự nhiên và được sử dụng để xác định những đặc tính của một thực thể. Ví dụ một đối tượng vật lý được biểu diễn trong 
dịch vụ thư mục là một sổ địa chỉ. Mỗi người được biểu diễn như một entry trong thư mục, với cặp key-value mô tả thông tin liên hệ, nơi kinh doanh,... Dịch vụ thư mục hữu ích trong nhiều 
kịch bản mà bạn muốn mô tả thông tin truy nhập.

# What is LDAP?
----

LDAP, or lightweight directory access protocol, là một giao thức truyền thông định nghĩa các phương thức mà một dịch vụ thư mục có thể được truy xuất. Nói rộng ra, LDAP định hình cách dữ 
liệu trong một dịch vụ thư mục có thể đại diện cho người dùng, xác định các yêu cầu đối với các thành phần sử dụng tạo lên data entries trong một dịch vụ thư mục, và vạch ra cách mà các 
thành phần khác nhau được sử dụng để tạo ra entry.

LDAP là một giao thức mở, có sẵn nhiều ứng dụng khác nhau đang sử dụng. OpenLDAP project là một trong những biến thể mã nguồn mở tốt nhất hỗ trợ.

# Basic LDAP Data Components
----

Chúng ta đã thảo luận về cách mà giao thức LDAP sử dụng để kết nối với một cơ sử dữ liệu thư mục phục vụ truy vấn, thêm và sửa thông tin. Tuy nhiên, định nghĩa đơn giản này trình bài chưa 
đúng về độ phức tạp của hệ thống hỗ trợ giao thức này. Cách mà LDAP hiển thị dữ liệu người dùng phụ thuộc vào sự tương tác và mối liên hệ giữa các thành phần cấu trúc được định nghĩa.

## Attributes
----

Dữ liệu trong hệ thống LDAP được lưu trữ trong các elements được gọi là thuộc tính. Các thuộc tính là cặp key-value. Không như các hệ thống khác, các key được khai báo tên sẵn trong một 
objectClasses khi chọn entry. Hơn nữa, dữ liệu trong một thuộc tính phải phù hợp với loại được khai báo khi khởi tạo ban đầu. 

Thiết lập giá trị cho một thuộc tính được hoàn thành khi tên thuộc tính và giá trị thuộc tính phân tách bằng dấu hai chấm (:) kèm khoảng trắng. Một ví dụ về thuộc tính `email` khai báo địa 
chỉ email như sau:
```sh
email: admin@example.com
```

Khi tham chiếu tới thuộc tính và dữ liệu của nó (lúc chưa thiết lập), thì hai bên được thay bằng dấu bằng (=)
```sh
mail=example.com
```

## Entries
----

Các thuộc tính khi đứng một mình thì không hữu ích. Để giúp chúng có nghĩa, chúng phải được gắn kết với nhau. Trong LDAP, bạn sử dụng thuộc tính trong một entry. Một entry về cơ bản 
là một tập hợp các thuộc tính với một tên được sử dụng để mô tả gì đó. 

Ví dụ, bạn có một entry cho một người dùng trong hệ thống của bạn hoặc một danh mục trong kho. Đây tương tự như một hàng trong cơ sở dữ liệu hoặc một trang đơn trong sổ địa chỉ. 
Trong khi một thuộc tính khai báo các đặc điểm hoặc tính chất của thứ gì đó, thì một entry mô tả chính nó bằng cách tập hợp các thuộc tính dưới 1 tên.

Một ví dụ về entry trong LDIF (LDAP Data Interchange Format) nhìn như sau:
```sh
dn: sn=Ellingwood,ou=people,dc=digitalocean,dc=com
objectclass: person
sn: Ellingwood
cn: Justin Ellingwood
```

Ví dụ trên là một entry trong hệ thống LDAP

## DIT
----

Khi bạn bắt đầu quen thuộc với LDAP, bạn dễ dàng nhận ra rằng các dữ liệu được định nghĩa bởi thuộc tính chỉ đại diện cho một phần thông tin về đối tượng. Phần còn lại của entry ở bên trong 
hệ thống LDAP và mối quan hệ này là gián tiếp.

Ví dụ, nếu có entry về cả người dùng và danh mục kho, làm cách nào để mọi người giao tiếp với chúng? Một cách để phân biệt các entry với nhau là các mối quan hệ và các nhóm. Điều này là 
một chức năng của nơi mà entry được đặt vào khi nó được tạo ra. Các entry được thêm vào một hệ thống LDAP như nhánh của cây được gọi là Data Information Trees, hay DITs.

Một DIT biểu diễn một cấu trúc tổ chức tương tự với file hệ thống, mỗi entry có đúng một entry cha và có số lượng con bất kỳ bên dưới. Các entry trong cây LDAP có thể biểu diễn về mọi 
thứ. một vài entry được sử dụng cho mục đích tổ chức, tương tự thư mục trong một filesystem.

Theo cách này, bạn có thể có một entry về "people" và một entry về "inventoryItems". Dữ liệu entry của bạn có thể được tạo như là con của một loại nào đó tốt hơn. Tổ chức entry có thể 
định nghĩa cách biểu diễn tốt nhất cho dữ liệu của bạn.

Ví dụ một entry ở mục trước, chúng ta thấy một dấu hiệu của DIT ở trong dòng `dn`
```sh
dn: sn=Ellingwood,ou=people,dc=digitalocean,dc=com
```

Dòng này được gọi là tên phân biệt của entry (distinguished name), được sử dụng để xác định một entry. Nó hoạt động như một đường dẫn đầy đủ tới gốc của DIT. Trong ví dụ trên, chúng 
ta đang tạo một entry gọi là `sn=Ellingwood`. Cha trực tiếp của entry này được gọi là `ou=people`, được sử dụng như một container cho các entry mô tả người. Cha của entry này bắt nguồn 
từ `digitalocean.com` domain name, coi như root của DIT.

# Defining LDAP Data Components
----

Trong phần trước chúng ta thảo luận về cách dữ liệu được biểu diễn bên trong một hệ thống LDAP. Tuy nhiên, chúng ta phải nói thêm về cách thành phần lưu trữ dữ liệu được khai báo. 
Ví dụ, chúng ta đề cập đến dữ liệu phải phù hợp với mỗi thuộc tính đã khai báo. Vậy các khai báo này đến từ đâu?

## Attribute Definitions
----

Các thuộc tính được khai báo sử dụng cú pháp khá phức tạp. Chúng phải ghi tên cho một thuộc tính, mọi tên khác có thể được tham chiếu tới thuộc tính, loại dữ liệu có thể được nhập vào, 
cũng như một loạt các siêu dữ liệu khác. Các siêu dữ liệu này có thể mô tả thuộc tính để nói với LDAP làm thế nào sắp xếp hoặc so sánh giá trị thuộc tính và biết làm thế nào nó liên 
quan tới thuộc tính khác. 

Ví dụ, ở đây định nghĩa một thuộc tính `name`
```sh
attributetype ( 2.5.4.41 NAME 'name' DESC 'RFC4519: common supertype of name attributes'
        EQUALITY caseIgnoreMatch
        SUBSTR caseIgnoreSubstringsMatch
        SYNTAX 1.3.6.1.4.1.1466.115.121.1.15{32768} )
```

`name` là tên của thuộc tính. Số ở dòng đầu tiên là globally unique OID (object ID) gán cho thuộc tính để phân biệt nó từ mọi thuộc tính khác. Phần còn lại của entry định nghĩa cách mà 
entry có thể được so sánh trong quá trình tìm kiếm và có con trỏ chỉ nơi để tìm thông tin cho các loại dữ liệu yêu cầu của thuộc tính.

Một phần quan trọng của một khai báo thuộc tính là liệu thuộc tính có thể được khai báo nhiều hơn trong một entry. Ví dụ, khai báo một surname có thể chỉ được định nghĩa một lần 
cho mỗi entry, nhưng một thuộc tính cho "niece" có thể cho phép thuộc tính được định nghĩa nhiều lần ở một entry đơn. Các thuộc tính là đa gia trị mặc định, và phải chứa cờ SINGLE-VALUE 
nếu chúng chỉ thiết lập một giá trị cho mỗi entry.

Định nghĩa thuộc tính phức tạp hơn nhiều so với sử dụng và thiết lập thuộc tính. May mắn là phần lớn bạn không phải xác định các thuộc tính riêng bởi vì các từ thông dụng nhất 
đã bao gồm trong hầu hết các LDAP và những người khác có thể nhập vào một cách dễ dàng.

## ObjectClass Definitions
----

Các thuộc tính được tổng hợp bên trong các entry được gọi là các objectClass. Các objectClass đơn giản là nhóm các thuộc tính liên quan sử dụng để miêu ta một thứ cụ thể. Ví dụ, 
"person" là một objectClass.

Các entry tăng cường khả năng sử dụng các thuộc tính của objectClass bằng cách thiết lập một thuộc tính đặc biệt gọi là `objectClass`, đặt tên objectClass bạn muốn sử dụng. Trong thực 
tế, `objectClass` là thuộc tính duy nhất bạn thiết lập trong một entry mà không chỉ định thêm objectClass khác.

Vì vậy, nếu bạn tạo một entry mô tả người, bao gồm `objectClass person` cho phép bạn sử dụng tất cả các thuộc tính bên trong objectClass:
```sh
dn: . . .
objectClass: person
``` 

Khi đó bạn có khả năng thiết lập các thuộc tính bên trong entry:
```sh
cn: Common name
description: Human-readable description of the entry
seeAlso: Reference to related entries
sn: Surname
telephoneNumber: A telephone number
userPassword: A password for the user
```

Thuộc tính `objectClass` có thể được sử dụng nhiều lần nếu bạn cần thuộc tính từ các objectClass khác, nhưng có những quy tắc chỉ ra những gì được phép. Các ObjectClass được định nghĩa 
như là một trong vài "type"

Có 2 loại objectClass chính là **structural** hay **auxiliary**. Một entry phải có đúng một tructural class, nhưng có thể không có hoặc có nhiều auxilliary class sử dụng để tăng các 
thuộc tính có sẵn cho class. Một structural objectClass được sử dụng để tạo và định nghĩa entry, trong khi auxilliary objectClass thêm chức năng bổ sung thông qua các thuộc tính thêm vào.

ObjectClass xác định các thuộc tính mà chúng cung cấp phải có (chỉ định bằng `MUST`) hay tùy chọn (chỉ định bằng `MAY`). Nhiều objectClass có thể cung cấp thuộc tính tương tự nhau và MAY hay 
MUST của thuộc tính có thể khác đối với từng objectClass

Ví dụ, objectClass "person" được khai báo như sau:
```sh
objectclass ( 2.5.6.6 NAME 'person' DESC 'RFC2256: a person' SUP top STRUCTURAL
  MUST ( sn $ cn )
  MAY ( userPassword $ telephoneNumber $ seeAlso $ description ) )
```

Đây là định nghĩa cho một structural objectClass, nghĩa là nó có thể được sử dụng để tạo entry. entry tạo phải được thiết lập thuộc tính `surname` và `commonname`, và có thể 
tùy chọn thiết lập `userPassword, telephoneNumber, seeAlso, description`

## Schemas
----

Định nghĩa objectClass và thuộc tính đã xong, tiếp theo, nhóm chúng lại với nhau trong một cấu trúc được gọi là **schema**. Không như cơ sở dữ liệu quan hệ truyền thống, schema trong 
LDAP đơn giản là tổng hợp các objectClass và thuộc tính liên quan. Một DIT đơn có thể có nhiều schema khác nhau, vì vậy nó có thể tạo nhiều entry và nhiều thuộc tính nó cần.

Schema thường gồm các định nghĩa thuộc tính bổ sung và có thể yêu cầu các thuộc tính được định nghĩa trong các schema khác. Ví dụ, objectClass "person" chúng ta thảo luận ở trên 
yêu cầu thuộc tính `surname` hay `sn` được thiết lập cho mọi entry sử dụng objectClass "person". Nếu những điều này không được định nghĩa trong các máy chỉ LDAP, một schema chứa 
các định nghĩa này có thể được sử dụng để thêm các định nghĩa này vào ngữ pháp server.

Định dạng của một schema cơ bản là sự kết hợp của các entry trên:
```sh
. . .

objectclass ( 2.5.6.6 NAME 'person' DESC 'RFC2256: a person' SUP top STRUCTURAL
  MUST ( sn $ cn )
  MAY ( userPassword $ telephoneNumber $ seeAlso $ description ) )

attributetype ( 2.5.4.4 NAME ( 'sn' 'surname' )
  DESC 'RFC2256: last (family) name(s) for which the entity is known by' SUP name )

attributetype ( 2.5.4.4 NAME ( 'cn' 'commonName' )
  DESC 'RFC4519: common name(s) for which the entity is known by' SUP name )

. . .
```

# Data Organization
----





# Tham khảo
----

- [https://www.digitalocean.com/community/tutorials/understanding-the-ldap-protocol-data-hierarchy-and-entry-components](https://www.digitalocean.com/community/tutorials/understanding-the-ldap-protocol-data-hierarchy-and-entry-components)
