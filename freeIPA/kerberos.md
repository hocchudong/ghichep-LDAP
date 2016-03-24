####Kerberos là gì?

- Kerberos là một giao thức xác thực mạng. Nó được thiết kế để cung cấp xác thực mạnh cho ứng dụng client/server bằng việc sử dụng mật mã khóa bí mật (secret-key). Kerberos có sẵn trong nhiều sản phẩm thương mại.

- Internet là nơi không an toàn. Nhiều giao thức được sử dụng trong internet không cung cấp bất kỳ phương thức bảo mật nào. Các công vụ để "sniff" password trên mạng thường được sử dụng bởi các hacker. Vì vậy ứng dụng mà gửi một password không được mã hóa thông mạng vô cùng yếu. Tệ hơn nữa, những ứng dụng client/server khác dựa trên các chương trình client là "honnest" (trung thực )về danh tính của người đang sử dụng nó. Các ứng dụng khác dựa trên client để hạn chế các hoạt động của mình cho những người mà nó được phép làm và không có thực thi bởi server.

- Một vài web cố gắng sử dụng firewall để giải quyết vấn đề an toàn mạng. Không may thay, firewall cho rằng "những kẻ xấu" ở bên ngoài, thường là một giả định rất tồi. Hầu hết các sự cố thực sự gây hại của hacker được thực hiện bởi người ở bên trong. Firewall cũng có một bất tiện là họ hạn chế việc người dùng của bạn có thể sử dụng internet. Tại nhiều nơi, hạn chế này chỉ đơn giản là không thực tế và không thể chấp nhận.

- Kerberos được tạo bở MIT như là một giải pháp cho những vấn đề an toàn về mạng. Giao thức Kerberos sử dụng mật mã mạnh vì vậy một client có thể chứng minh danh tính của nó tới một server thông qua một kết nối mạng không an toàn. Sau khi client và server được sử dụng Kerberos để chứng minh danh tính của nhau, Chúng cũng có thể mã hóa tất cả các giao tiếp giữa chúng để chắc chắn sự riêng tư và toàn vẹn dữ liệu.

#####Tổng kết

- Kerberos là một giải pháp cho vấn đề bảo mật mạng của bạn. Nó cung cấp các công cụ để xác thực và mã hóa mạnh thông qua mạng để giúp secure hệ thống thông tin của bạn trên toàn bộ danh nghiệp của bạn.

####Kerberos trong FreeIPA

- Cung cấp dịch vụ xác thực cho toàn thể FreeIPA. Nó là một dịch vụ người dùng và những thành phần khác. Kerberos server là một thành phần cơ bản của FreeIPA Server

#####Hoạt động

- khi bạn chạy command `kinit` bạn gọi một client mà kết nối tới Kerberos server, gọi là KDC. Kết quả cuả xác thực là client nhận một ticket. Ticket này là một pass tạm hoặc gọi là pass-book. Khi một user cố gắng truy cập vào một vài tài nguyên được bảo vệ bởi Kerberos tài nguyên đó yêu cầu tiket của user.

- Khi project SSSD được sử dụng, ticket lấy tự động cho user khi người dùng xác thực trên máy client

#####Data
- MIT Kerberos KDC Server đọc tất cả thông tin định danh user và dịch vụ từ Directory Server. FreeIPA bổ sung ipa-kdb KDC data backend để đọc và ghi tất cả thông tin yêu cầu vào LDAP tree

#####Các thuật ngữ

- Authentication Server (AS): Một server có nhiệm vụ phát ticket cho một dịch vụ nào đó mà user muốn truy cập vào.

- cipher text: dữ liệu bị mã hóa

-  client:

- credentials: Các thông tin của user

- credential cache hay ticket  file: File bao gồm key để mã hóa các giao tiếp giữa user và network services.

- crypt hash: Một cách hash thường sử dụng để xác thực user

- GSS-API: The Generic Security Service API là tập các chức năng mà cung cấp các dịch vụ an toàn.

- hash: hash value

- hash function

- key: được sử dụng khi mã hóa hoặc giải mã dữ liệu khác

- Key Distribution Center (KDC):dịch vụ phát Kerberos tickets

- keytab (keytable):

- kinit: command cho phép admin login để có được và cache ticket-grant ticket ban đầu

- principal (có thể hiểu là người đứng đầu): Là tên duy nhất của một user hay dịch vụ đã cho phép xác thực sử dụng Kerberos. form của principal root[/instance]@REALM

- realm: Một mạng mà sử dụng Kerberos, tập hợp của một hay nhiều server gọi là KDCs.

- service: Là chương trình được truy cập thông qua mạng

- ticket: là tập các thông tin tạm để xác thực định danh của một client.

- ticket-granting server (TGS): một server có nhiệm vuj phát ticket cho một dịch vụ mong muốn được đưa cho user để truy cập vào dịch vụ

- ticket-granting ticket (TGT): là một ticket đặc biệt. Để cho phép client đạt được ticket bổ sung mà ko áp dụng cho họ từ KDC

- unencrypted password
