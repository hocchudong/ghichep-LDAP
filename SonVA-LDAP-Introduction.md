<img src="http://i.imgur.com/wHqJbVR.png">

# I. GIỚI THIỆU VỀ LDAP
## 1. Dịch vụ thư mục (Dicrectory Service) là gì ?
Thư mục (Directory) là một phần của cuộc sống con người :  
```
Danh bạ điện thoại  
TV Guide  
Catalog sản phẩm  
Thẻ thư viện....  
```  
 ==> Offline Directory  
Thư mục giúp chúng ta tìm kiếm thông tin bằng cách tổ chức và mô tả thành phần bên trong : Từ sđt cho đến kênh TV, từ đồ ăn cho đến tài liệu tham khảo...  
Khái niệm thư mục trong IT cũng tương tự nhưng có một số khác biệt khá quan trọng. ==> Online Directory  

Online Directories khác Offline Directories ở :  
- Thư mục online là động (Dynamic)
- Thư mục online linh hoạt hơn (Flexible)
- Thư mục online an toàn hơn (Sercurity)
- Thư mục online riêng tư hơn (Personalized)  

Dựa vào mục đích, chúng ta có thể phân chia Directory thành 4 loại :  
- Application-specific directories: Chúng thường đi kèm hoặc nhúng vào ứng dụng mà đôi khi chúng ta hoàn toàn không nhận ra. Ví dụ : IBM/Lotus Notes Name and Address book, Microsoft Exchange, file aliases trong MTA
- Network operating system (NOS)–based directories: Những thư mục như Novell's eDirectory (NDS), Sun Microsystems Network Information Service (NIS),Banyan's StreetTalk được thiết kế để đáp ứng nhu cầu của một HĐH mạng  
- Purpose-specific directories : Tuy không đi kèm với ứng dụng nhưng những thư mục này thường thiết kế cho mục đích cố định, chuyên hóa. Ví dụ hệ thống DNS, Switchboard.. 
- General-purpose, standards-based directories : Dạng thư mục được phát triển để phục vụ một lượng lớn các ứng dụng khác nhau. (Ví dụ LDAP, X.500)  

Như ta đã trình bày ở trên, theo một cách ngắn gọn và dễ hiểu, thư mục là một loại CSDL đặc biệt.  
**Vậy dịch vụ thư mục (Directory Service) là gì ?**
```
  Đó là một tập hợp các phần mềm, phần cứng, tiến trình, chính sách, thủ tục quản trị nhằm mục đích cung cấp nội dung thư mục của bạn đến cho người dùng
```
Dịch vụ thư mục gồm các yếu tố sau :

- Thông tin chứa trong thư mục
- Phần mềm Server tổ chức thông tin
- Phần mềm Client đóng vai trò người dùng truy xuất các thông tin này
- Phần cứng cho Server và Client
- Phần mềm hỗ trợ (Ví dụ HĐH, Driver..)
- Hạ tầng mạng cho phép kết nối Server-Client, và các kết nối khác.
- Chính sách quản lý (Ai truy cập, cập nhật thông tin thư mục ? Cái gì được lưu trữ...)
- Các thủ tục duy trì và giám sát dịch vụ thư mục
- Các phần mềm dùng để giám sát, duy trì..  
<img src="http://i.imgur.com/ZVLtwmk.png">  

Khi nói tới dịch vụ thư mục , chúng ta không thể không nhắc tới chuẩn X.500  
Vào giữa những năm 1980s, có 2 chuẩn dành cho dịch vụ thư mục do 2 cơ quan quốc tế độc lập ban hành :  
- International Telegraph and Telephone Consultative Committee (CCITT) sau này là International Telecommunication Union (ITU) → Tìm kiếm SĐT và email
- International Organization for Standardization (ISO) → Cung cấp một name service chạy trên 2 lớp network và application trong OSI  

Chuẩn X.500 lần đầu tiên được phê duyệt vào năm 1988 và công bố vào năm 1990 bởi CCITT, liên tục được cập nhật vào 1993,1997, 2001 và tới hôm nay.  
Các đặc điểm kĩ thuật được định nghĩa theo loạt tiêu chuẩn sau :
- X.501 : The models
- X.509: Authentication Framework
- X.511: Abstract Service Definition
- X.518: Procedures for Distributed Operation
- X.519: Protocol Specifications
- X.520: Selected Attribute Types.
- X.525: Replication
- X.530: Systems Management  

Khi được công bố , X.500 là một chuẩn có khá nhiều đặc tính mới :  
- Là một hệ thống dịch vụ thư mục đầu tiên có mục đích chung, nhằm phục vụ yêu cầu của một loạt ứng dụng.
- Cung cấp hệ thống tìm kiếm phong phú và hỗ trợ nhiều cấu trúc truy vấn khác nhau.
- X.500 được thiết kế như là một hệ thống phân phối dịch vụ cao cấp với hệ thống máy chủ, client và admin có thể trải dài toàn cầu.
- X.500 là một tiêu chuẩn mở, không phụ thuộc vào bất cứ hãng phần mềm hay thiết bị nào.  

<img src="http://i.imgur.com/EudsHvx.png">  

## 2. LDAP là gì?
Sớm chấp nhận rằng X.500 (DAP) là giao thức nặng nề , phức tạp và không phù hợp với máy tính để bàn ngày nay. Những người triển khai X.500 đã tìm đường đi để tránh tiếp cận một giao thức DAP kiểu heavyweight.  
Vào khoảng năm 1990, có 2 nhóm độc lập đã phát triển giao thức tương tự, cạnh tranh với DAP, dễ dàng triển khai trên máy tính để bàn thông thường.  
- Client dùng giao thức này để gửi yêu cầu tới máy chủ trung gian qua TCP/IP
- Máy chủ trung gian này sẽ triển khai yêu cầu tới một máy chủ DAP  

Đó là 2 giao thức mang tên Directory Assistance Service (DAS) và Directory Interface to X.500 Implemented Efficiently (DIXIE)  
<img src="http://i.imgur.com/Cq57io2.png">  
Như vậy , LDAP là gì ?   
- LDAP (Lightweight Directory Access Protocol) - Giao thức truy cập nhanh các dịch vụ thư mục : Là một chuẩn mở rộng của giao thức truy cập dịch vụ thư mục (DAP – X.500)
- LDAP là một giao thức chạy trên nền TCP/IP (TCP:389,UDP:389) dưới dạng kết nối Client-Server.
- LDAP là một dạng giao thức Lightweight , tức là một giao thức có tính hiệu quả, đơn giản và dễ dàng cài đặt (trái với Heavyweight như X.500)
- LDAP đã phát triển với v2 và LDAP v3.  
<img src="http://i.imgur.com/jdfLsxl.png">

LDAP tối ưu và đơn giản hóa DAP (tức X.500) ở 4 phương diện :  
- Chức năng (Functionally): LDAP cung cấp hầu hết các chức năng của DAP với chi phí thấp hơn. Các chức năng dự phòng, ít sử dụng bị loại bỏ, đơn giản hóa việc triển khai lên client và server
- Trình diễn dữ liệu (Data Representation): Trong LDAP, phần lớn yếu tố dữ liệu được thực hiện dưới dạng chuỗi văn bản, nhằm đơn giản và tăng cường hiệu suất (tuy nhiên để hiệu quả thì các chuỗi văn bản được gói trong các thông điệp mã hóa nhị phân) 
- Mã hóa (Encoding) : Một tập con các quy tắc mã hóa của X.500 được dùng để mã hóa thông điệp LDAP . Qua đó đơn giản giản hóa việc triển khai.
- Truyền tải (Transport): LDAP chạy trực tiếp trên TCP thay vì yêu cầu một hệ thống phức tạp và khó sử dụng như OSI. Việc thực hiện được đơn giản đi nhiều, hiệu suất được tăng lên, sự phụ thuộc vào OSI bị loại bỏ do đó tối ưu hóa việc triển khai thư mục LDAP  

LDAP qua các thời kỳ :  
<img src="http://i.imgur.com/YbUTxOr.png">
<img src="http://i.imgur.com/7hIOPWs.png">
<img src="http://i.imgur.com/fS26q6B.png">  
**CÁC MÔ HÌNH HOẠT ĐỘNG CHÍNH CỦA LDAP :**  
Ngoài vai trò là một giao thức mạng thì LDAP còn định nghĩa ra 4 mô hình (model) cho phép linh động trong việc tổ chức sắp xếp thư mục :
- LDAP Information : Định nghĩa các loại dữ liệu chứa trong thư mục .
- LDAP Naming : Định nghĩa cách sắp xếp và tham chiếu đến thư mục.
- LDAP Functional : Định nghĩa cách truy cập và cập nhật thông tin trong thư mục
- LDAP Security : Định nghĩa cách mà thư mục được bảo vệ, tránh các truy cập trái phép  

LDAP định nghĩa định dạng file trao đổi dữ liệu LDIF (LDAP Data Interchange Format) dưới dạng văn bản dùng để mô tả thông tin về thư mục.  
LDAP chỉ là giao thức, không hỗ trợ xử lý như cơ sở dữ liệu. Mà nó cần một nơi lưu trữ backend và xử lý dữ liệu tại đó. Vì vậy mà LDAP client kết nối tới LDAP server theo mô hình sau:  
<img src="http://i.imgur.com/Wuzu29G.gif">  
LDAP là giao thức truy cập vì vậy nó theo mô hình dạng cây (Directory Information Tree). LDAP là giao thức truy cập dạng client/server.  
<img src="http://i.imgur.com/bek7M6V.png">
## 3. Phương thức hoạt động của LDAP
LDAP hoạt động theo mô hình client-server. Một hoặc nhiều LDAP server chứa thông tin về cây thư mục (Directory Information Tree – DIT). Client kết nối đến server và gửi yêu cầu. Server phản hồi bằng chính nó hoặc trỏ tới LDAP server khác để client lấy thông tin. Trình tự khi có kết nối với LDAP:  
- Connect (kết nối với LDAP): client mở kết nối tới LDAP server
- Bind (kiểu kết nối: nặc danh hoặc đăng nhập xác thực): client gửi thông tin xác thực
- Search (tìm kiếm): client gửi yêu cầu tìm kiếm
- Interpret search (xử lý tìm kiếm): server thực hiện xử lý tìm kiếm
- Result (kết quả): server trả lại kết quả cho client
- Unbind: client gửi yêu cầu đóng kết nối tới server
- Close connection (đóng kết nối): đóng kết nối từ server  
<img src="http://i.imgur.com/d4yQwZW.png">  
## 4.Database backend của LDAP
Slapd là một “LDAP directory server” có thể chạy trên nhiều platform khác nhau. Bạn có thể sử dụng nó để cung cấp những dịch vụ của riêng mình. Những tính năng mà slapd cung cấp:  
- LDAPv3: slapd hỗ trợ LDAP cả IPv4, IPv6 và Unix IPC.
- Simple Authentication and Security Layer: slapd hỗ trợ mạnh mẽ chứng thực và bảo mật dữ liệu dịch vụ bằng SASL
- Transport Layer Security: slapd hỗ trợ sử dụng TLS hay SSL.
2 database mà SLAPD sử dụng để lưu trữ dữ liệu hiện tại là bdb và hdb. BDB sử dụng Oracle Berkeley DB để lưu trữ dữ liệu. Nó được đề nghị sử dụng làm database backend chính cho SLAPD thông thường. HDB là cũng tương tự như BDB nhưng nó sử dụng database phân cấp nên hỗ trợ cơ sỡ dữ liệu dạng cây. HDB thường được mặc định cấu hình trong SLAPD hiện nay.
## 5. Lưu trữ thông tin của LDAP
Ldif (LDAP Data Interchange Format) là một chuẩn định dang file text lưu trữ thông tin cấu hình LDAP và nội dung thư mục. File LDIF thường dùng để import dữ liệu mới vào trong directory hoặc thay đổi dữ liệu đã có. Dữ liệu trong file LDIF phải tuân theo quy luật có trong schema của LDAP.  
Schema là loại dữ liệu được định nghĩa từ trước. Mọi thành phần được thêm vào hoặc thay đổi trong directory của bạn sẽ được kiểm tra lại trong schema để đảm bảo chính xác.
### 5.1 Cấu trúc tập tin LDIF  

Thông thường file LDIF sẽ có mẫu sau:  
 - Mỗi tập entry khác nhau được phân cách bởi dòng trắng
 - “tên thuộc tính: giá trị”
 - Một tập chỉ dẫn cú pháp để làm sao xử lý thông tin

Những yêu cầu khi khai báo LDIF:
- Lời chú thích được gõ sau dấu # trong 1 dòng
- Thuộc tính được liệt kê bên trái dấu “:” và giá trị được biểu diễn bên phải.
- Thuộc tính dn định nghĩa duy nhất cho một DN xác định trong entry đó.

Ví dụ: thông tin của OU, people, các thư mục bên trong Distinguished Name test.com Thông tin LDAP thể hiện theo dạng cây o: test.com

----------------File LDIF lưu thông tin: ----------------------------------------------  
dn: o=test

objectclass: top

objectclass: organization

o: test.com

dn: ou=People,o=test.com

objectclass: organizationalUnit

ou: People

dn: ou=Server, o=test.com

objectclass: organizationalUnit

ou: Server

dn: ou=IT, ou=People, o=test.com

objectclass: organizationalUnit

ou: IT

dn: cn=sonva, ou=IT, ou=People, o=test.com

objectclass: top

objectclass: organizationalPerson

cn: sonva

sn: vu

givenname: son

uid: sonva

ou: IT
### 5.2 Entry là gì?
Một entry là tập hợp của các thuộc tính, từng thuộc tính này mô tả một nét đặt trưng tiêu biểu của một đối tượng. Một entry bao gồm nhiều dòng  
- DN : distinguished name - là tên của entry thư mục, tất cả được viết trên một dòng.
- Sau đó lần lượt là các thuộc tính của entry, thuộc tính dùng để lưu giữ dữ liệu. Mỗi thuộc tính trên một dòng theo định dạng là “kiểu thuộc tính : giá trị thuộc tính”.  

Một số các thuộc tính cơ bản trong file Ldif:  

|STT|Tên|Mô tả                                 |  
|1  |dn |Distinguished Name, tên gọi phân biệt |   
|2	|c	|country – 2 kí tự viết tắt tên của một nước| 

3	o	organization – tổ chức
4	ou	organization unit – đơn vị tổ chức
5	objectClass	Mỗi giá trị objectClass hoạt động như một khuôn mẫu cho các dữ liệu được lưu giữ trong một entry. Nó định nghĩa một bộ các thuộc tính phải được trình bày trong entry (Ví dụ: entry này có giá trị của thuộc tính objectClass là eperson, mà trong eperson có quy định cần có các thuộc tính là tên, email, uid ,…thì entry này sẽ có các thuộc tính đó)
6	givenName	Tên
7	uid	id người dùng
8	cn	common name – tên thường gọi
9	telephoneNumber	số điện thoại
10	sn	surname – họ
11	userPassword	mật khẩu người dùng
12	mail	địa chỉ email
13	facsimileTelephoneNumber	số phách
14	createTimestamp	thời gian tạo ra entry này
15	creatorsName	tên người tạo ra entry này
16	pwdChangedTime	thời gian thay đổi mật khẩu
17	entryUUID	id của entry