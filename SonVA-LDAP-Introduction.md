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
## 3. Phương thức hoạt động của LDAP