###LDAP là gì
- LDAP (Lightweight Directory Access Protocol) là một giao thức phần mềm cho phép mọi người truy cập một tổ chức, cá nhân và các tài nguyên khác như các file và các thiết bị trong hệ thống mạng ở bất cứ nơi nào trên internet hoặc trên một mạng nội bộ của công ty. LDAP là một phiên bản "lightweight" (nhỏ hơn về lượng code) của Directory Access Protocol (DAP), một phần của chuẩn X.500 - một chuẩn cho dịch vụ thư mục trong một mạng. LDAP nhẹ hơn bởi vì phiên bản ban đâu nó không bao gồm các tính năng về bảo mật. (Hay gọi là X.500 Lite)

- LDAP có nguồn gốc tại đại học Michigan và đã được tán thành bở ít nhất 40 công ty. Netscape bao gồm nó trong bộ giao tiếp mới nhất của các sản phầm. M$ cũng bao gồm nó như là một phần mà gọi là Active Directory trong một số sản phần như Outlook Express. Novell's Netware Directory Services tương thích với LDAP. Cisco cũng hỗ trợ nó trong các sản phẩm về mạng.

- Giống X.500 LDAP tổ chức thông tin sử dụng các danh bạ(Directory). Những danh bạ này có thể lưu trữ nhiều dạng thông tin và có thể được sử dụng tương như như Network Information System (NIS) cho phép mọi người truy cập vào tài khoản của họ từ bất kì máy nào trên hệ thống mạng LDAP

- Trong nhiều trường hợp LDAP được sử dụng như 1 virtual phone directory (danh bạ điện thoại ảo) cho phép người dùng dễ dàng truy cập các thông tin liên lạc của những người dùng khác. Nhưng LDAP mềm dẻo hơn một danh bạ điện thoại truyền thống, nó có khả năng chuyển 1 truy vấn tới LDAP server khác trên khắp thế giới, cung cấp một ad-hoc kho toàn cầu (global repo) của thông tin. Tuy nhiên, hiện tại, LDAP thường được sử dụng với một tổ chức cá nhân, như các trường đại học, cơ quan chính phủ và các công ty.

- LDAP là một hệ thống client/server. Server có thể sử dụng nhiều loại database để lưu trữ một directory, mỗi tối ưu hóa cho hoạt động đọc và copy nhanh. Khi một ứng LDAP client kết nối tới một LDAP Server, nó cũng có thể truy vấn tới một thư mục hoặc sửa nó. Trong một sự kiện truy vấn, server cũng trả lời truy vấn hoặc nó có thể chuyển truy vấn tới một LDAP Server mà có nhiệm vụ trả lời. Nếu ứng dụng client có gắng để chỉnh sửa thông tin với một LDAP Directory, server kiểm tra quyền của user (permission) để thay đổi và sau đó thêm hay update thông tin.

- LDAP không giới hạn thông tin liên lạc, hay thậm chí thông tin về người. LDAP được sử dụng để tìm kiếm các chứng chỉ mã hóa, trỏ tới các máy in và các dịch vụ khác trên mạng, và cung cấp "single sign-on" - một password cho một người dùng được chia sẻ giữa nhiều dịch vụ. LDAP thích hợp cho nhiều kiểu thông tin dạng thư mục, phục vụ cho việc tìm kiếm nhanh và tần xuất chỉnh sử ít.


- Như một giao thức, LDAP không định nghĩa làm tế nào các chương trình làm việc cả phía Server hay client. Nó định nghĩa một "language" để cho ứng dụng client nói chuyện với server (server với server ). Bên phía client, một client có thể là một chương trình email, một trình duyệt printer, hay một cuốn sách địa chỉ.

- Hầu hết LDAP Client có thể đọc từ một server. Tìm kiếm khả năng của các client khác nhau. Một số ít có thể ghi hoặc update thông tin, Nhưng LDAP không bao gồm security hay mã hóa, vì vậy các update luôn luôn yêu cầu các bảo vệ bổ sung như mã hóa kết nối bằng SSL tới LDAP Server

<!-- - LDAP cũng định nghĩa:
  - Permission: đc đặt bởi admin để cho phép chỉ những người nhất định được truy cập tới LDAP Database và tùy chọn giữ các dữ liệu nhất định private.
  - Schema: Một cách để mô tả định dạng và thuộc tính của dữ liệu trên server. Ví dụ: Một schema vào một LDAP server có thể được nghĩa một loại mục nhập "groovyPerson" có các thuộc tính "instantMessageAddress" và "cofeeRoastPreference". Các thuộc tính bình thường là name, email address, ...  -->


###Tại sao sử dụng LDAP

- Lợi ích chính của việc sử dụng LDAP là thông tin cho một tổ chức toàn bộ có thể được hợp nhất vào một kho trung tâm. Ví dụ: LDAP có thể được sử dụng như một danh bạ trung tâm truy cập từ bất kỳ nơi đâu trên mạng chứ không phải là quản lý danh sách người sử dụng đối với từng nhóm trong một tổ chức. Và bởi vì LDAP hỗ trợ SSL và TLS do vậy dữ liệu nhạy cảm có thể được bảo vệ tốt hơn.

- LDAP cũng hỗ trợ một số backend database để lưu trữ các danh bạ. Điều này cho phép admin dễ dàng triển khai một CSDL phù hợp nhất với loại thông tin máy chủ phổ biến. Bởi vì LDAP cũng có một định nghĩa tốt về API, Số lượng các ứng dụng hỗ trợ LDAP rất nhiều và ngày càng tăng về chất lượng và số lượng.
