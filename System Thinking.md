---
date: 2019-04-17T11:09:00-05:00:00
description: "System Design Primer"
tags: ["tecnical-skill","zalopay-edu"]
title: "System Thinking"
---
#System Design Primer
###1. Định lý CAP
![cap-theorem]  
(**C**onsistency - **A**vailability - **P**artition Tolerance)
Được đề xuất bởi tiến sĩ Eric Brewer. Trong một hệ thống phân tán ta chỉ có thể giữ được 2 trong 3 giá trị (tinh chất) nói trên và phải hy sinh 1 tính chất. Ta sẽ nói rõ các tính chất sau đây:
- **C**onsistency (tính nhất quán /đồng nhất): Mỗi lần đọc dữ liệu sẽ nhận được dữ liệu mới nhất hoặc làeorem
- **A**vailability (tính sẵn sàng): Một node không bị lỗi sẽ trả về một response thích hợp (không đảm bảo thông tin là mới nhất) trong một khoảng thời gian thích hợp.
- **P**artition Tolerance (phân vùng): Hệ thống có tiếp tục hoạt động, bất chấp một lượng tùy ý các thông điệp (gói tin) bị mất hoặc trì hoãn do trục trặc hoặc đứt kết nối mạng.  
![partition]
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Đường truyền kết nối mạng thường hay bị đứt và không đoán trước được vì thế lỗi mạng có thể xảy với hệ thống của bạn bất kì lức nào mà bạn không thể lường trước được. Nên khi bạn muốn cung cấp hệ thống phân tán (Partition Tolerance) thì bạn phải đánh đổi hoặc là chọn Consistency hoặc là chọn Availability.  
  
- **CP** - **C**onsistency/**P**artition Tolerance - khi đường dẫn bị đứt việc đợi một response từ node phân tán mà có thể dẫn đến lỗi time out. Hệ thống sẽ có thể chọn trả về lỗi tùy theo mong muốn người xây dựng hệ thống. **CP** tốt trong trường hợp khi yêu cầu hệ thống của bạn phải là atomic reads and writes (tất cả hình thức đọc và ghi phải là luôn đúng với mọi quy trình).
![consistency]
- **AP** - **A**vailability/**P**artition Tolerance - các responses sẽ luôn trả về những phiên bản dữ liệu gần nhất hiện tại đang có trên node, có thể không phải là phiên bản mới nhất, không cho phép trả về lỗi. Trạng thái hệ thống cũng có thể chấp nhận các việc writes(ghi) mà sau đó có thể được xử lý khi tình trạng đứt mạng của các node được phục hồi. **AP** tốt khi hệ thống kinh doanh của bạn cần sự linh động về dữ liệu khi đồng bộ, đáp ứng nhu cầu liên tục của khách hàng. **AP** là một lựa chọn tốt cho hệ thống cần sự không bị gián đoạn chức cho dù xảy ra một số lỗi bên ngoài.
![availability]  

Việc chọn lựa giữa **C**onsistency hay **A**vailability là tùy vào nghiệp vụ và yêu cầu của mô hình kinh doanh. Khi đối mặt với sự ngắt kết nối giữa các node, bạn phải có sự lựa chọn đánh đổi phù hợp với mô hình kinh doanh xây dựng. Dù muốn hay không, việc đứt kết nối mạng là một điều thực tế và không thể tránh khỏi. Bạn cần hiểu và chấp nhận sự *không hoàn hảo* này của mọi hệ thống.  
Trong bài này, mình sẽ tập trung vào giải pháp **AP** vì nó thực tế và đáp ứng được nhiều yêu cầu và mô hình kinh doanh hiện nay.
### 2. Consistency pattern
Ở đây ta sẽ có những phương pháp đồng bộ lại dữ liệu để khách hàng có dữ liệu nhất quán khi có sự cố xảy ra bằng các cách sẽ được bàn luận sau đây:
####2.1 Weak consistency (Nhất quán yếu)
Sau khi việc write, việc read có thể thấy hoặc không thấy hoặc không thấy. Đây là một giải pháp tốt cho các hệ thống real-time như video call, game online. Ví dụ bạn sử dụng video call của zalo có thể có những thời điểm bạn bị mất kết nối trong một vài giây và khi kết nối lại sẽ không nghe hoặc nhìn được những hình ảnh trong quá trình mất kết nối.
####2.2 Eventual consistency (Nhất quán đến cùng)
Trên lý thuyết đảm bảo rằng nếu như không có cập nhật mới cho một thực thể, tất cả việc reads thực thể đó sẽ cuối cùng trả về giá trị được cập nhật mới nhất. DNS là một ví dụ của hệ thống với mô hình eventual consistency, DNS servers không nhất thiết phải trả về những giá trị mới nhất nhưng thay vào đó các giá trị được lưu trữ và nhân bản qua nhiều thư mục trên Internet và được trả về sau đó. Cần thời gian để nhân bản giá trị được thay đổi đến tất cả DNS clients và servers. Tuy vậy, hệ thống 
DNS là một hệ thống thành công, tính khả dụng cao và có thể mở rộng lớn, cho phép tra cứu tên miền trên hàng triệu thiết bị trên thế giới.
![eventual-consistency]
Hình trên đây là ví dụ cho việc nhân bản với mô hình eventual consistency. Theo sơ đồ ta thấy, mặc dù việc nhân bản là luôn sẵn sàng cho việc read, nhưng đôi khi tại một thời điểm nhất định có một số node nhân bản có thể không nhất quán với giá trị mới nhất từ node gốc vì một số tác nhân bên ngoài.
####2.3 Strong consistency (Nhất quán mạnh)
Những hệ thống relational database được xây dựng dựa trên mô hình strong consistency. Tức là, thực thể được read sau khi write(set) phải là nhất quáĐ với giá trị mới nhất. Đây là một tính chất nền tảng để xây dưng nên hệ thống relational database. Tuy nhiên, để giữ được mô hình strong consistency, developer phải suy tính đánh đổi khả năng mở rộng và hiệu năng của ứng dụng.
![strong-consistency]
Đơn giản ta có thể thấy mô hình trên. Khi một tiến trình write giá trị X thì dữ liệu X phải bị khóa trong quá trình cập nhập và thời gian nhân bản ra các node khác để giữ tính nhất quán thực sự, đảm bảo không có tiến trình nào khác có thể thao tác trên X.

### 3. Availability pattern 
Có hai mẫu chính để cung cấp tính sẵn sàng cao: fail-over và replication
#### 3.1 Fail-over 
##### Active-passive
Nhịp kết nối sẽ được duy trì giữa active server và passive server. Nếu nhịp kết nối bị đứt thì passive server sẽ tiếp quản IP của active server và tiếp tục xử lý.
Thời gian phục hồi sẽ phụ thuộc vào passive server được cấu hình là hot standby, warm standby hay cold standby.
Active-passive failover có thể  được coi như <a href="#master-slave">master-slave</a> failover.
#####Active-active
Trong active-active, cả hai server đều xử lý các yêu cầu đến và phân tán chịu tải giữa chúng.
Nếu hai server đều được public, DNS cần biết IP của cả hai server. Nếu hai server xử dụng nội bộ thì phần mềm ứng dụng cần biết cả hai server.
Active-active failover có thế được coi như <a href="#master-master">master-master</a> failover.
### 4. Latency vs Througaput
![latency-throughput]
####4.1 Latency
Là khoảng thời gian cần để thực hiện một vài công việc hoặc tạo ra một vài kết quả. Latency được đo bằng đơn vị thời gian -- hours(giờ), minutes(phút), seconds(giây), nanseconds hoặc khoảng thời gian.
Ví dụ: để đi hết 20km từ Tân Bình đến tòa Flemington Lê Đại Hành cần 20 phút, Latency là 20 phút.
####4.2 Throughput
Là số lượng công việc hoặc kết quả được hoàn thành trong mỗi khoảng thời gian nhất định. Throughput được đo bằng đơn vị của bất kì cái gì đếm được trên một đơn vị thời gian.
Ví dụ: Đứng tại tòa Flemington, cứ 10 phút thì có 100 xe chạy ngang qua, Throughput là 100xe/10phút.

### 5. Phương pháp để scale database
#### 5.1 Phương pháp Replication
Replication có nghĩa là khi bạn có các bản sao của một thực thể được lưu ở nhiều node. Ta gọi Replica-set là một bộ(cụm) các node bao gồm một hoặc nhiều Master(có thể gọi là Primary) và một hoặc nhiều Slave (có thể gọi là Secondary). Việc read có thể được được xử lý bởi Slave và bạn có thể thêm nhiều slave để tăng hiệu suất đọc của hệ thống. Nhưng việc write luôn thực hiện trên Master của Replica-set và sau đó sao chép ra các Slave, vì thế ta không thể tăng tốc độ ghi nhanh hơn.
  
Nhược điểm:
- Vẫn có khả năng mất mát dữ liệu khi Master bị lỗi trước khi dữ liệu được sao chép qua các Slave.
- Việc write sẽ được được hiện lại qua các Slave. Vì thế nếu có quá nhiều hoạt động write, thì các Slave sẽ bị chậm xuống để thực hiện cập nhật dữ liệu và không thể phục vụ hoạt động read nhiều nữa.
- Càng nhiều Slave thì càng nhiều hoạt động sao chép, dẫn đến gây trễ việc sao chép.
- Trong một hệ thống, việc write vào Master có thể sinh nhiều thread, trong khi việc cập nhật dữ liệu trên Slave chỉ cung cấp với một Thread.
- Replication yêu cầu nhiều phần cứng và sự phức tạp hơn.
Có hai hướng tiếp cận cho phương pháp Replication:
#####5.1.1 <a id="master-slave">Master-slave</a> Replication
![master-slave]
Như ý tưởng của Replication, Replica-set sẽ có một Master và một hoặc nhiều Slave để sao chép dữ liệu của Master. Mục tiêu chính của Slave là phục vụ các hoạt động read, và liên tục cập nhật dữ liệu từ Master. Master thì có nhiệm vụ chính là xử lý các hoạt động cập nhật dữ liệu và sao chép dữ liệu cập nhật qua cho các Slave. Khi Master bị lỗi, hệ thống vẫn tiếp tục phục vụ việc read cho đến khi có một Slave được bầu trở thành Master.  
Nhược điểm của master-slave:  
- Cần thêm một node Arbiter để đảm bảo thực hiện quá trình bầu cử đưa một Slave lên Master.
#####5.1.2 <a id="master-master">Master-master</a> Replication
![master-master]
Các master server xử lý cả việc write và read và kết hợp với nhau trong việc write. Nếu một Master bị lỗi thì hệ thống vẫn hoạt động bình thường với các hoạt động write/read.
Nhươc điểm của master-master:
- Bạn cần có một load balancer hoặc bạn cần thay đổi logic của ứng dụng để xác định server để thực hiện việc write.
- Hầu hết hệ thống master-master hoặc là giảm consistency (phi phạm tính chất ACID) hoặc là tăng latency việc write vì quá trình đồng bộ dữ liệu.
- Việc giải quyết xung đột sẽ xuất hiện nhiều hơn khi việc write và latency tăng cao.

### Tài liệu tham khảo
- https://github.com/donnemartin/system-design-primer#eventual-consistency
- https://medium.com/eway/database-101-p1-%C4%91%E1%BB%8Bnh-l%C3%BD-cap-7260adf8b02f
- https://cloud.google.com/datastore/docs/articles/balancing-strong-and-eventual-consistency-with-google-cloud-datastore/
- https://www.quora.com/What-is-the-difference-between-latency-and-throughput
- https://dba.stackexchange.com/questions/52632/difference-between-sharding-and-replication-on-mongodb
- https://www.ibm.com/developerworks/community/blogs/RohitShetty/entry/high_availability_cold_warm_hot?lang=en

[cap-theorem]: /assets/images_system_thinking/cap-theorem.png  
[partition]: /assets/images_system_thinking/partition.png
[consistency]: /assets/images_system_thinking/consistency.png
[availability]: /assets/images_system_thinking/availability.png
[eventual-consistency]: /assets/images_system_thinking/eventual-consistency.png
[strong-consistency]: /assets/images_system_thinking/strong-consistency.png
[latency-throughput]: /assets/images_system_thinking/latency_throughput.jpg
[master-slave]: /assets/images_system_thinking/master-slave.png
[master-master]: /assets/images_system_thinking/master-master.png