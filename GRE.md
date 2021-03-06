### Giao thức GRE
1. **Giới thiệu**
  - GRE – Generic Routing Encapsulation là giao thức tunneling được phát triển bởi Cisco có thể đóng với các giao thức mạng.  
  
  - GRE là giao thức giữa lớp 2 và lớp 3.  
  
  - Giao thức này sẽ đóng gói một số kiểu gói tin vào bên trong các IP tunnels để tạo thành các kết nối điểm-điểm 
  (point-to-point) ảo. Các IP tunnel chạy trên hạ tầng mạng công cộng.
  
  - GRE thêm vào tối thiểu 24 byte vào gói tin, trong đó bao gồm 20-byte IP header mới, 4 byte còn lại là GRE header. 
  GRE có thể tùy chọn thêm vào 12 byte mở rộng để cung cấp tính năng tin cậy như: checksum, key chứng thực, sequence number.
  <img src= http://i.imgur.com/JGhvtfQ.jpg >
  
  - GRE là công cụ tạo tunnel khá đơn giản nhưng hiệu quả. Nó có thể tạo tunnel cho bấy kì giao thức lớp 3 nào.
  
  - GRE cho phép những giao thức định tuyến hoạt động trên kênh truyền của mình.

  - GRE không có cơ chế bảo mật tốt. Trong khi đó, IPSec cung cấp sự tin cậy cao. Do đó nhà quản trị thường kết hợp GRE với 
  IPSec để tăng tính bảo mật, đồng thời cũng hỗ trợ IPSec trong việc định tuyến và truyền những gói tin có địa chỉ IP 
  Muliticast.
  
 2. **Phân loại GRE**
 
  - GRE là giao thức có thể đóng gói bất kì gói tin nào của lớp network. GRE cung cấp khả năng có thể định tuyến giữa những 
  mạng riêng (private network) thông qua môi trường Internet bằng cách sử dụng các địa chỉ IP đã được định tuyến.

  - GRE truyền thống là point-to-point, còn mGRE là sự mở rộng khái niệm này bằng việc cho phép một tunnel có thể đến được 
  nhiều điểm đích, mGRE tunnel là thành phần cơ bản nhất trong DMVPN.
  
    2.1. Point-to-Point GRE
    
    <img src=http://i.imgur.com/MRTOG2K.jpg>
    
  - Đối với các tunnel GRE point-to-point thì trên mỗi router spoke (R2 & R3) cấu hình một tunnel chỉ đến HUB (R1) ngược lại,
  trên router HUB cũng sẽ phải cấu hình hai tunnel, một đến R2 và một đến R3. Mỗi tunnel như vậy thì cần một địa chỉ IP. Giả 
  sử mô hình trên được mở rộng thành nhiều spoke, thì trên R1 cần phải cấu hình phức tạp và tốn không gian địa chỉ IP.
  
  - Trong tunnel GRE point-To-point, điểm đầu và cuối được xác định thì có thể truyền dữ liệu. Tuy nhiên, có một vấn đề phát 
  sinh là nếu địa chỉ đích là một multicast (chẳng hạn 224.0.0.5) thì GRE point-to-point không thực hiện được. Để làm được 
  việc này thì phải cần đến mGRE.
  
    2.2. Point-to-Multipoint GRE (mGRE)
    - mGRE giải quyết được vấn đề đích đến là một địa chỉ multicast. Đây là tính năng chính của mGRE được dùng để thực thi 
    Multicast VPN trong Cisco IOS. Tuy nhiên, trong mGRE, điểm cuối chưa được xác định nên nó cần một giao thức 
    để ánh xạ địa chỉ tunnel sang địa chỉ cổng vật lý. Giao thức này được gọi là NHRP (Next Hop Resolution Protocol)
    
    - Đối với các mGRE Tunnel thì mỗi router chỉ có một Tunnel được cấu hình cùng một subnet logical.
    <img src= http://i.imgur.com/YHuKkUk.jpg>


  3. Mô hình lab


  - Mô hình lab :
  
  <img src=https://i.imgur.com/DHz9C8e.png>
  
  - Gateway client1: 192.168.90.129
  
  - Gateway client2: 172.16.10.128
  
  - Config Host A
  ```
  modprobe ip_gre
  ip tunnel add gre1 mode gre remote 10.0.0.133 local 10.0.0.132 ttl 255
  ip link set gre1 up
  ip addr add 10.10.10.1 dev gre1  
  ```
  - Config host B
  
  ```
  modprobe ip_gre
  ip tunnel add gre1 mode gre remote 10.0.0.132 local 10.0.0.133 ttl 255
  ip link set gre1 up
  ip addr add 10.10.10.2 dev gre1  
  ```
  - ip route trên host1
  
  ```
  ip r ad 172.16.10.0/24 via 10.10.10.1
  ```
  
  <img src= https://i.imgur.com/bEobbRn.png>
  
  - ip route trên host2
  
  ```
  ip r ad 192.168.90.0/24 via 10.10.10.2
  ```
  
  <img src= https://i.imgur.com/pdfRQz1.png>
  
  - ping từ client 1 đến client 2
  
  < img src= https://i.imgur.com/o1mwQqZ.png>
  
  - tcpdumb on gre1
  
  <img src= https://i.imgur.com/SERgilF.png >
  
  - ssh client1-> client2
  
  <img src= https://i.imgur.com/I0tuMO7.png >
  
  -ssh client2 -> client1
  
  <img src= https://i.imgur.com/t7RdZLa.png >
  
  
  
  
