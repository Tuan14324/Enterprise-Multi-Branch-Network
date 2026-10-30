# LUỒNG LƯU LƯỢNG MẠNG (NETWORK FLOW)

## 1. Tổng quan

Tài liệu mô tả cách lưu lượng dữ liệu di chuyển trong hệ thống Enterprise Multi-Branch Network.

Các luồng lưu lượng được phân tích bao gồm:

- Giao tiếp giữa các thiết bị trong cùng VLAN.
- Giao tiếp giữa các VLAN khác nhau.
- Giao tiếp giữa HQ và các chi nhánh.
- Truy cập các dịch vụ DHCP và DNS tập trung.
- Kết nối của người dùng Wi-Fi.

## 2. Tổng quan đường đi của lưu lượng

Đường đi tổng quát của dữ liệu:

```text
THIẾT BỊ ĐẦU CUỐI
        |
        v
  ACCESS SWITCH
        |
        v
 CORE LAYER 3 SWITCH
        |
        +----------------------+
        |                      |
   Cùng VLAN             Khác VLAN
        |                      |
  Chuyển tiếp Layer 2    Định tuyến Layer 3
                               |
                               v
                         SITE WAN ROUTER
                               |
                               v
                           ISP NETWORK
                               |
                               v
                       ROUTER CHI NHÁNH
                               |
                               v
                       CORE SWITCH TỪ XA
                               |
                               v
                       THIẾT BỊ ĐÍCH
```



## 3. Luồng giao tiếp trong cùng VLAN

Ví dụ:

- PC1: 192.168.20.100/24.
- PC2: 192.168.20.101/24.

Cả hai thiết bị đều thuộc VLAN 20 tại HQ.

### Các bước xử lý

1. PC1 xác định PC2 nằm trong cùng mạng IP.
2. PC1 gửi ARP Request để tìm địa chỉ MAC của PC2.
3. Access Switch chuyển tiếp gói tin ARP trong phạm vi VLAN 20.
4. PC2 phản hồi địa chỉ MAC của mình.
5. PC1 gửi các Ethernet Frame trực tiếp đến PC2.
6. Switch chuyển tiếp Frame trong VLAN 20.

Lưu lượng này không cần đi qua chức năng định tuyến Layer 3 của Core Switch.

## 4. Luồng giao tiếp giữa các VLAN (Inter-VLAN Routing)

Ví dụ:

- Thiết bị nguồn: PC thuộc VLAN 20.
- Thiết bị đích: Server thuộc VLAN 10.
- IP nguồn: 192.168.20.100.
- IP đích: 192.168.10.10.

### Các bước xử lý

1. PC kiểm tra địa chỉ IP đích.
2. PC xác định địa chỉ đích không thuộc subnet hiện tại.
3. PC gửi gói tin đến Default Gateway `192.168.20.1`.
4. Access Switch chuyển tiếp Frame qua đường Trunk đến CORE-HQ.
5. CORE-HQ nhận gói tin tại SVI VLAN 20.
6. CORE-HQ tra cứu bảng định tuyến.
7. CORE-HQ định tuyến gói tin sang VLAN 10.
8. Server nhận được gói tin.

Trong luồng này, Core Layer 3 Switch thực hiện chức năng Inter-VLAN Routing.

## 5. Luồng giao tiếp từ HQ đến BR1

Ví dụ:

- Thiết bị nguồn: PC tại HQ, IP `192.168.20.100`.
- Thiết bị đích: PC tại BR1, IP `192.168.60.100`.

### Các bước xử lý

1. PC tại HQ xác định địa chỉ đích thuộc mạng từ xa.
2. PC gửi gói tin đến Default Gateway `192.168.20.1`.
3. CORE-HQ định tuyến gói tin đến HQ Router thông qua mạng transit `10.255.0.0/30`.
4. HQ Router kiểm tra bảng định tuyến và chính sách VPN.
5. Lưu lượng khớp với VPN Selector của đường hầm HQ–BR1.
6. HQ Router mã hóa gói tin bằng IPsec.
7. Gói tin đã mã hóa được chuyển tiếp qua mạng ISP.
8. BR1 Router nhận gói tin đã mã hóa.
9. BR1 Router xác thực và giải mã gói tin IPsec.
10. BR1 Router chuyển tiếp gói tin gốc đến CORE-BR1.
11. CORE-BR1 định tuyến gói tin đến VLAN 60.
12. PC tại BR1 nhận được gói tin.

Lưu lượng phản hồi đi theo chiều ngược lại.

## 6. Luồng truy cập DNS từ BR2 đến HQ

Ví dụ:

- Thiết bị nguồn: PC tại BR2, IP `192.168.70.100`.
- Thiết bị đích: DNS Server, IP `192.168.10.11`.

### Các bước xử lý

1. PC tại BR2 gửi truy vấn DNS đến `192.168.10.11`.
2. PC chuyển gói tin đến Default Gateway `192.168.70.1`.
3. CORE-BR2 định tuyến gói tin đến BR2 Router.
4. BR2 Router kiểm tra VPN Selector.
5. IPsec mã hóa gói tin.
6. Gói tin được truyền qua mạng ISP.
7. HQ Router nhận và giải mã gói tin.
8. HQ Router chuyển tiếp gói tin đến CORE-HQ.
9. CORE-HQ định tuyến gói tin đến VLAN 10.
10. DNS Server xử lý yêu cầu truy vấn.
11. DNS Server gửi phản hồi về BR2 theo đường định tuyến và VPN tương ứng.

## 7. Luồng cấp phát địa chỉ DHCP

DHCP Server được triển khai tại HQ.

Địa chỉ DHCP Server: `192.168.10.10`.

Ví dụ: Một máy tính mới kết nối vào VLAN 60 tại BR1.

### Quy trình DHCP

1. Máy tính gửi bản tin DHCP Discover dưới dạng Broadcast.
2. CORE-BR1 nhận bản tin trên SVI VLAN 60.
3. Cấu hình `ip helper-address 192.168.10.10` tiếp nhận và chuyển tiếp yêu cầu DHCP.
4. Thiết bị chuyển tiếp gửi yêu cầu đến DHCP Server dưới dạng Unicast.
5. Yêu cầu đi qua BR1 Router và đường hầm VPN IPsec đến HQ.
6. DHCP Server lựa chọn Scope tương ứng với VLAN 60.
7. DHCP Server gửi DHCP Offer đến thiết bị Relay.
8. Thiết bị Relay chuyển tiếp Offer đến máy tính.
9. Máy tính hoàn tất quá trình DHCP và nhận địa chỉ IP.

DHCP Server phải có Scope VLAN 60 với Default Gateway và DNS chính xác.

Để DHCP hoạt động qua VPN, cần đảm bảo:
- VPN Selector cho phép lưu lượng cần thiết.
- Có định tuyến hai chiều.
- DHCP Scope được cấu hình đúng.
- Có đường đi phản hồi từ DHCP Server về mạng chi nhánh.

## 8. Luồng kết nối Wi-Fi

Ví dụ: Người dùng kết nối vào mạng Wi-Fi tại HQ.

### Quy trình kết nối

1. Thiết bị không dây tìm kiếm SSID do Access Point phát.
2. Người dùng xác thực và kết nối với mạng Wi-Fi.
3. Access Point chuyển tiếp lưu lượng theo cấu hình mạng không dây.
4. Lưu lượng của người dùng được gán vào VLAN 30 theo thiết kế hiện tại.
5. CORE-HQ cung cấp Default Gateway `192.168.30.1`.
6. CORE-HQ định tuyến lưu lượng đến mạng đích.

Địa chỉ quản lý WLC là `192.168.40.10` thuộc VLAN 40.

Đường đi thực tế giữa AP, WLC và thiết bị người dùng phụ thuộc vào chế độ hoạt động của WLC, AP và cách ánh xạ VLAN.

## 9. Các khái niệm định tuyến quan trọng

### 9.1. Default Gateway

Default Gateway là thiết bị mà máy tính gửi lưu lượng đến khi địa chỉ đích nằm ngoài mạng IP cục bộ.

### 9.2. Routing Table

Router và Layer 3 Switch sử dụng bảng định tuyến để xác định đường đi, Next Hop và cổng ra của gói tin.

### 9.3. IPsec Encryption

IPsec bảo vệ lưu lượng khớp với các VPN Selector đã cấu hình giữa hai VPN Peer.

Lưu lượng không khớp với VPN Selector sẽ không được mã hóa bởi chính sách VPN đó.

### 9.4. Đường đi phản hồi

Để giao tiếp thành công, hệ thống cần có đường đi từ nguồn đến đích và đường đi phản hồi từ đích về nguồn.

Có đường đi một chiều không đồng nghĩa với việc giao tiếp hai chiều thành công.

## 10. Các lệnh kiểm tra

### Trên Core Switch

```cisco
show ip route
show ip interface brief
show ip ospf neighbor
show vlan brief
show interfaces trunk
```

### Trên WAN Router

```cisco
show ip route
show crypto isakmp sa
show crypto ipsec sa
show access-lists
show ip interface brief
```

### Trên máy tính

```text
ipconfig /all
ping 192.168.10.11
tracert 192.168.10.11
nslookup example.local
```

Một số lệnh và kết quả hiển thị có thể khác nhau tùy thiết bị và phiên bản Packet Tracer.

## 11. Quy trình xử lý sự cố

Khi xảy ra lỗi kết nối, nên kiểm tra lần lượt:

1. Kết nối vật lý.
2. Trạng thái Interface và địa chỉ IP.
3. Cấu hình VLAN.
4. Cấu hình Trunk.
5. Default Gateway.
6. Bảng định tuyến nội bộ.
7. Trạng thái OSPF và các tuyến học được.
8. Static Route.
9. Trạng thái VPN Tunnel.
10. VPN Selector và bộ đếm mã hóa IPsec.
11. Định tuyến phía thiết bị đích và đường đi phản hồi.
12. Cấu hình dịch vụ DHCP hoặc DNS.

## 12. Kết luận

Hiểu rõ luồng lưu lượng là kiến thức quan trọng đối với kỹ sư mạng khi triển khai và xử lý sự cố.

Tài liệu này mô tả cách dữ liệu được chuyển tiếp trong các trường hợp giao tiếp nội bộ, định tuyến giữa VLAN, kết nối WAN, VPN và sử dụng dịch vụ mạng tập trung.