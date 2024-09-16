Strangler Fig Pattern là một kỹ thuật dùng trong phát triển phần mềm, đặc biệt là khi chuyển đổi từ một hệ thống cũ (legacy system) sang hệ thống mới. Mô hình này bao gồm việc xây dựng và triển khai các chức năng mới xung quanh hệ thống cũ, dần dần thay thế từng phần của hệ thống cũ mà không làm gián đoạn hoạt động.

Sau một thời gian, hệ thống cũ sẽ bị “nghẹt thở” giống như cách cây đa bóp nghẹt cây chủ, dẫn đến việc hệ thống cũ bị loại bỏ hoàn toàn.


-------------


Để thực hiện Strangler Fig Pattern trên một hệ thống cũ với cơ sở dữ liệu lớn và nhiều phạm vi miền (domain), nhưng không có mô hình rõ ràng và không thể chia module, bạn có thể làm theo các bước sau:

### 1. **Phân tích hệ thống hiện tại**
   - **Xác định các domain và chức năng**: Đầu tiên, phân tích hệ thống hiện tại để xác định các domain và chức năng chính. Dù không có mô hình rõ ràng, cố gắng tìm ra các phần của hệ thống có thể được phân tách ra.

### 2. **Lựa chọn domain để chuyển đổi**
   - **Ưu tiên domain**: Chọn một domain có tác động thấp hoặc ít rủi ro để bắt đầu chuyển đổi. Domain này nên là một phần có thể dễ dàng tách ra và cung cấp giá trị độc lập.

### 3. **Thiết kế kiến trúc mới**
   - **Xây dựng mô hình cho domain mới**: Thiết kế một mô hình rõ ràng và các module riêng biệt cho domain mới, áp dụng các phương pháp như DDD (Domain-Driven Design) nếu có thể.
   - **Lập kế hoạch chuyển đổi**: Xác định cách thức domain mới sẽ tương tác với hệ thống cũ trong thời gian chuyển đổi.

### 4. **Phát triển và triển khai domain mới**
   - **Xây dựng domain mới**: Phát triển các chức năng của domain mới với kiến trúc và công nghệ hiện đại.
   - **Triển khai song song**: Chạy domain mới song song với hệ thống cũ, đảm bảo rằng domain mới hoạt động đúng và không ảnh hưởng đến hệ thống hiện tại.

### 5. **Chuyển hướng traffic**
   - **Thiết lập lớp chuyển tiếp (proxy)**: Sử dụng một lớp chuyển tiếp hoặc proxy để điều hướng traffic từ hệ thống cũ sang hệ thống mới. Chỉ chuyển hướng các yêu cầu liên quan đến domain mới.
   - **Kiểm tra tính tương thích**: Đảm bảo rằng các yêu cầu và dữ liệu được xử lý chính xác giữa hệ thống cũ và hệ thống mới.

### 6. **Tách dần các domain khác**
   - **Lặp lại quy trình**: Sau khi domain đầu tiên đã được chuyển đổi thành công, tiếp tục tách các domain khác bằng cách lặp lại các bước trên.
   - **Cải thiện và tối ưu hóa**: Khi chuyển đổi từng domain, tối ưu hóa cấu trúc và codebase để hệ thống mới có hiệu suất tốt hơn và dễ bảo trì.

### 7. **Loại bỏ hệ thống cũ**
   - **Ngừng hoạt động hệ thống cũ**: Khi tất cả các domain đã được chuyển đổi và hệ thống mới đã ổn định, dừng hoạt động hệ thống cũ và loại bỏ nó khỏi môi trường sản xuất.

### 8. **Tối ưu hóa và bảo trì hệ thống mới**
   - **Giám sát và điều chỉnh**: Theo dõi hệ thống mới để đảm bảo không có lỗi nào phát sinh và điều chỉnh các vấn đề nếu cần thiết.
   - **Cải tiến liên tục**: Tiếp tục cải tiến hệ thống mới, áp dụng các best practice và công nghệ mới khi cần thiết.

-----------

Để thực hiện Strangler Fig Pattern liên quan đến code và database, bạn cần kế hoạch cụ thể để chuyển đổi từ hệ thống cũ mà vẫn duy trì hoạt động liên tục. Dưới đây là cách bạn có thể thực hiện:

### 1. **Phân tách code**
   - **Tách lớp giao diện**: Bắt đầu bằng cách tách lớp giao diện (UI) khỏi logic nghiệp vụ trong hệ thống cũ. Nếu không có mô hình hoặc module rõ ràng, tập trung vào việc cô lập các phần liên quan đến một domain cụ thể.
   - **Xây dựng các dịch vụ mới**: Phát triển các dịch vụ mới với mô hình rõ ràng và áp dụng các kiến trúc hiện đại như microservices hoặc domain-driven design (DDD). Những dịch vụ này sẽ dần thay thế các phần tương ứng trong hệ thống cũ.

### 2. **Phát triển lớp chuyển đổi (Anti-corruption Layer)**
   - **Lớp trung gian**: Xây dựng một lớp chuyển đổi để kết nối các dịch vụ mới với hệ thống cũ, đặc biệt khi dữ liệu hoặc logic nghiệp vụ không tương thích. Lớp này đảm bảo rằng các hệ thống có thể giao tiếp với nhau mà không gây ra sự cố.
   - **Chuyển hướng yêu cầu**: Sử dụng lớp này để dần dần chuyển hướng các yêu cầu từ hệ thống cũ sang các dịch vụ mới mà không ảnh hưởng đến người dùng cuối.

### 3. **Chuyển đổi database**
   - **Database song song**: Thay vì thay đổi trực tiếp cơ sở dữ liệu hiện tại, bạn có thể duy trì một cơ sở dữ liệu song song cho domain mới. Cơ sở dữ liệu này được thiết kế và tối ưu hóa theo mô hình mới.
   - **Đồng bộ dữ liệu**: Xây dựng cơ chế đồng bộ hóa dữ liệu giữa cơ sở dữ liệu cũ và mới. Điều này có thể được thực hiện thông qua các sự kiện hoặc triggers để đảm bảo tính nhất quán trong thời gian chuyển đổi.
   - **Chuyển dần dữ liệu**: Dần dần chuyển dữ liệu từ cơ sở dữ liệu cũ sang cơ sở dữ liệu mới khi các domain mới được triển khai. Khi một domain hoàn tất chuyển đổi, ngừng sử dụng bảng dữ liệu cũ liên quan đến domain đó.

### 4. **Chuyển đổi dữ liệu (Data Migration)**
   - **Lập kế hoạch di chuyển**: Xác định các bảng và schema cần di chuyển và lập kế hoạch chi tiết cho từng bước chuyển đổi dữ liệu. 
   - **Thực hiện di chuyển**: Sử dụng các công cụ di chuyển dữ liệu (ETL) để chuyển dữ liệu từ hệ thống cũ sang hệ thống mới. Thực hiện các bước di chuyển nhỏ để kiểm tra và điều chỉnh khi cần.
   - **Kiểm tra tính toàn vẹn**: Sau khi di chuyển, kiểm tra tính toàn vẹn của dữ liệu và đảm bảo rằng không có lỗi nào phát sinh trong quá trình chuyển đổi.

### 5. **Loại bỏ các thành phần cũ**
   - **Ngừng sử dụng và loại bỏ**: Khi tất cả dữ liệu và logic đã được chuyển đổi thành công sang hệ thống mới, bạn có thể ngừng sử dụng các phần liên quan trong hệ thống cũ và loại bỏ chúng để giảm thiểu sự phức tạp.

### 6. **Tối ưu hóa hệ thống mới**
   - **Tối ưu hóa database**: Khi chỉ còn cơ sở dữ liệu mới, tối ưu hóa các chỉ mục, bảng và quan hệ để đảm bảo hiệu suất tốt nhất.
   - **Cải thiện mã nguồn**: Tiếp tục tối ưu hóa và cải thiện mã nguồn của hệ thống mới, áp dụng các best practice như unit testing, code review và CI/CD.

### Tóm lại:
- Tạo dịch vụ mới và lớp chuyển đổi để cô lập và chuyển hướng các phần từ hệ thống cũ.
- Sử dụng cơ sở dữ liệu song song và đồng bộ hóa dữ liệu để đảm bảo quá trình chuyển đổi suôn sẻ.
- Thực hiện quá trình di chuyển dữ liệu cẩn thận và loại bỏ các thành phần cũ khi hoàn thành.

Cách tiếp cận này giúp bạn có thể chuyển đổi hệ thống mà không cần phải thực hiện một cuộc đại tu toàn diện, tránh được rủi ro và giảm thiểu gián đoạn cho người dùng.