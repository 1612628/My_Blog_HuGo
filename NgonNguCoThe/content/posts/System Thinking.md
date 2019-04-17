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
- **AP** - **A**vailability/**P**artition Tolerance - các responses sẽ trả về những phiên bản dữ liệu gần nhất hiện tại đang có trên node, có thể không phải là phiên bản mới nhất. Trạng thái hệ thống cũng có thể chấp nhận các lệnh Writes(ghi) mà sau đó có thể được xử lý khi tình trạng đứt mạng của các node được phục hồi. **AP** tốt khi hệ thống kinh doanh của bạn cần sự linh động về dữ liệu khi đồng bộ, đáp ứng nhu cầu liên tục của khách hàng. **AP** là một lựa chọn tốt cho hệ thống cần sự không bị gián đoạn chức cho dù xảy ra một số lỗi bên ngoài.
![availability]  

Việc chọn lựa giữa **C**onsistency hay **A**vailability là tùy vào nghiệp vụ và yêu cầu của mô hình kinh doanh. Khi đối mặt với sự ngắt kết nối giữa các node, bạn phải có sự lựa chọn đánh đổi phù hợp với mô hình kinh doanh xây dựng. Dù muốn hay không, việc đứt kết nối mạng là một điều thực tế và không thể tránh khỏi. Bạn cần hiểu và chấp nhận sự *không hoàn hảo* này của mọi hệ thống.  
Trong bài này, mình sẽ tập trung vào giải pháp **AP** vì nó thực tế và đáp ứng được nhiều yêu cầu và mô hình kinh doanh hiện nay. 
### 2. Giải pháp AP



[cap-theorem]: /assets/images_system_thinking/cap-theorem/png  
[partition]: /assets/images_system_thinking/partition.png
[consistency]: /assets/images_system_thinking/consistency.png
[availability]: /assets/images_system_thinking/availability.png

