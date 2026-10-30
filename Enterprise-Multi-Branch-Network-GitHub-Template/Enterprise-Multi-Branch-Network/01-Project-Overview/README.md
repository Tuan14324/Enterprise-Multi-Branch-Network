# ENTERPRISE MULTI-BRANCH NETWORK
## Thiết kế và triển khai hệ thống mạng doanh nghiệp đa chi nhánh



## 1. Giới thiệu dự án

Dự án tập trung vào việc thiết kế, xây dựng và triển khai mô hình mạng doanh nghiệp có nhiều địa điểm hoạt động, bao gồm một trụ sở chính (Headquarters – HQ) và hai chi nhánh (Branch 1 – BR1, Branch 2 – BR2).

Các địa điểm được kết nối với nhau thông qua mạng ISP bằng công nghệ Site-to-Site IPsec VPN, cho phép trao đổi dữ liệu giữa trụ sở chính và các chi nhánh một cách an toàn.

Hệ thống được xây dựng trên Cisco Packet Tracer, áp dụng các công nghệ mạng doanh nghiệp phổ biến như VLAN, Inter-VLAN Routing, OSPF, DHCP, DNS, Wireless LAN và IPsec VPN.

Dự án hướng đến việc mô phỏng một hệ thống mạng doanh nghiệp thực tế, đồng thời giúp củng cố kiến thức về thiết kế hạ tầng mạng, cấu hình thiết bị, triển khai dịch vụ, bảo mật và xử lý sự cố mạng.

## 2. Mục tiêu dự án

- Thiết kế mô hình mạng doanh nghiệp gồm trụ sở chính và nhiều chi nhánh.
- Xây dựng hạ tầng mạng LAN có khả năng phân chia và quản lý các nhóm người dùng.
- Triển khai VLAN nhằm phân đoạn mạng theo phòng ban và chức năng.
- Cấu hình Layer 3 Switch và Inter-VLAN Routing để định tuyến giữa các VLAN.
- Triển khai OSPF nhằm trao đổi thông tin định tuyến giữa Router và Core Switch tại từng địa điểm.
- Xây dựng hệ thống DHCP tập trung tại trụ sở chính.
- Triển khai DNS phục vụ phân giải tên miền nội bộ.
- Cấu hình mạng Wi-Fi cho người dùng không dây.
- Thiết lập kết nối WAN giữa các địa điểm thông qua ISP.
- Triển khai Site-to-Site IPsec VPN để bảo vệ lưu lượng trao đổi giữa HQ và các chi nhánh.
- Kiểm tra khả năng kết nối đầu cuối và thực hành xử lý các sự cố mạng thường gặp.

## 3. Phạm vi triển khai

Hệ thống gồm bốn thành phần chính:

| Thành phần | Vai trò |
|---|---|
| HQ – Headquarters | Trụ sở chính, nơi tập trung các dịch vụ mạng và quản lý kết nối VPN |
| BR1 – Branch 1 | Chi nhánh thứ nhất, kết nối về HQ |
| BR2 – Branch 2 | Chi nhánh thứ hai, kết nối về HQ |
| ISP | Mạng nhà cung cấp dịch vụ Internet, cung cấp kết nối WAN giữa các địa điểm |

### 3.1. Trụ sở chính – HQ

HQ là trung tâm của hệ thống, cung cấp các dịch vụ và tài nguyên dùng chung cho các chi nhánh.

Các thành phần chính:
- Router kết nối WAN/ISP.
- Core Layer 3 Switch.
- Access Switch.
- Access Point.
- DHCP Server.
- DNS Server.
- Máy chủ và thiết bị đầu cuối.

### 3.2. Chi nhánh BR1

BR1 được triển khai hạ tầng mạng LAN riêng, phân chia người dùng thành các VLAN khác nhau và kết nối về HQ thông qua Site-to-Site IPsec VPN.

### 3.3. Chi nhánh BR2

BR2 có hạ tầng mạng LAN riêng, cung cấp kết nối cho người dùng và các thiết bị mạng tại chi nhánh. BR2 kết nối về HQ thông qua Site-to-Site IPsec VPN.

## 4. Sơ đồ kiến trúc mạng

### 4.1. Logical Topology

![Logical Topology](02-Network-Design/Logical-Topology.png)

*Hình 1: Sơ đồ kiến trúc logic của hệ thống mạng doanh nghiệp.*

### 4.2. Physical Topology

![Physical Topology](02-Network-Design/Physical-Topology.png)

*Hình 2: Sơ đồ kết nối vật lý giữa các thiết bị mạng.*

## 5. Công nghệ và giao thức sử dụng

| Công nghệ | Mục đích sử dụng |
|---|---|
| VLAN | Phân chia mạng theo phòng ban và chức năng |
| 802.1Q Trunk | Truyền nhiều VLAN trên cùng một kết nối |
| Inter-VLAN Routing | Định tuyến giữa các VLAN |
| Layer 3 Switching | Định tuyến nội bộ trên Core Switch |
| OSPF | Trao đổi thông tin định tuyến nội bộ |
| Static Routing | Định tuyến tĩnh giữa các mạng và hướng WAN |
| Default Routing | Chuyển lưu lượng đến các mạng không có tuyến cụ thể về ISP |
| DHCP | Cấp phát địa chỉ IP tự động |
| DHCP Relay | Chuyển tiếp yêu cầu DHCP đến DHCP Server tập trung |
| DNS | Phân giải tên miền |
| Wireless LAN | Cung cấp kết nối mạng không dây |
| WPA2-PSK | Bảo vệ kết nối Wi-Fi |
| Site-to-Site IPsec VPN | Mã hóa lưu lượng giữa các địa điểm |
| ACL | Kiểm soát lưu lượng truy cập mạng |

## 6. Quy hoạch địa chỉ IP

### 6.1. Địa chỉ WAN

| Kết nối | Thiết bị | Địa chỉ IP |
|---|---|---|
| ISP – HQ | ISP Router | 200.1.0.1/30 |
| ISP – HQ | HQ Router | 200.1.0.2/30 |
| ISP – BR1 | ISP Router | 200.1.1.1/30 |
| ISP – BR1 | BR1 Router | 200.1.1.2/30 |
| ISP – BR2 | ISP Router | 200.1.2.1/30 |
| ISP – BR2 | BR2 Router | 200.1.2.2/30 |

### 6.2. Kết nối Router – Core Switch

| Địa điểm | Router | Core Switch | Subnet |
|---|---|---|---|
| HQ | 10.255.0.1/30 | 10.255.0.2/30 | 10.255.0.0/30 |
| BR1 | 10.255.0.5/30 | 10.255.0.6/30 | 10.255.0.4/30 |
| BR2 | 10.255.0.9/30 | 10.255.0.10/30 | 10.255.0.8/30 |

### 6.3. Quy hoạch VLAN

| Địa điểm | VLAN | Tên VLAN | Subnet | Default Gateway |
|---|---:|---|---|---|
| HQ | 10 | SERVER | 192.168.10.0/24 | 192.168.10.1 |
| HQ | 20 | ADMIN | 192.168.20.0/24 | 192.168.20.1 |
| HQ | 30 | WIFI | 192.168.30.0/24 | 192.168.30.1 |
| HQ | 40 | WLC-MGMT | 192.168.40.0/24 | 192.168.40.1 |
| BR1 | 50 | ADMIN | 192.168.50.0/24 | 192.168.50.1 |
| BR1 | 60 | STAFF | 192.168.60.0/24 | 192.168.60.1 |
| BR2 | 70 | USERS | 192.168.70.0/24 | 192.168.70.1 |

### 6.4. Địa chỉ IP máy chủ và thiết bị hạ tầng

| Thiết bị | Địa chỉ IP | Default Gateway |
|---|---|---|
| DHCP Server | 192.168.10.10/24 | 192.168.10.1 |
| DNS Server | 192.168.10.11/24 | 192.168.10.1 |
| WLC | 192.168.40.10/24 | 192.168.40.1 |
| Printer BR2 | 192.168.70.10/24 | 192.168.70.1 |

Chi tiết quy hoạch địa chỉ IP được trình bày tại: `02-Network-Design/IP-Addressing-Plan.md`.

## 7. Kiến trúc định tuyến

### 7.1. Inter-VLAN Routing

Core Layer 3 Switch đảm nhiệm chức năng định tuyến giữa các VLAN thông qua các giao diện SVI (Switch Virtual Interface).

Mỗi VLAN được cấu hình một địa chỉ gateway tương ứng trên Core Switch.

### 7.2. OSPF Routing

OSPF được triển khai giữa Router và Core Layer 3 Switch tại từng địa điểm.

Mục đích:
- Trao đổi thông tin định tuyến giữa Router và Core Switch.
- Quảng bá các mạng LAN nội bộ.
- Cho phép Core Switch học default route từ Router khi tuyến mặc định hợp lệ được quảng bá.
- Tự động cập nhật bảng định tuyến khi có thay đổi phù hợp trong hệ thống.

OSPF sử dụng Process ID 1 và Area 0 trong thiết kế cơ sở.

**Lưu ý:** OSPF chỉ hoạt động nội bộ tại từng địa điểm, không chạy xuyên qua ISP hoặc các đường hầm IPsec VPN trong thiết kế hiện tại.

## 8. Kiến trúc Site-to-Site IPsec VPN

### 8.1. Mô hình VPN

Dự án sử dụng kiến trúc Hub-and-Spoke Site-to-Site IPsec VPN.

- HQ đóng vai trò Hub.
- BR1 đóng vai trò Spoke thứ nhất.
- BR2 đóng vai trò Spoke thứ hai.
- HQ thiết lập một đường hầm IPsec riêng đến từng chi nhánh.

Các đường hầm:
- HQ ↔ BR1.
- HQ ↔ BR2.

### 8.2. Nguyên lý hoạt động

Khi một thiết bị tại BR1 muốn truy cập tài nguyên tại HQ:

1. Thiết bị gửi dữ liệu đến Default Gateway.
2. Core Switch chuyển tiếp lưu lượng đến Router-BR1.
3. Router-BR1 xác định lưu lượng phù hợp với chính sách VPN.
4. IPsec mã hóa gói tin trước khi gửi qua ISP.
5. Router-HQ nhận và giải mã gói tin.
6. Lưu lượng được chuyển tiếp đến mạng nội bộ tại HQ.
7. Dữ liệu phản hồi đi theo chiều ngược lại.

BR2 hoạt động theo nguyên lý tương tự.

### 8.3. Phạm vi kết nối giữa các chi nhánh

Trong thiết kế cơ sở, BR1 và BR2 không có đường hầm VPN trực tiếp với nhau.

Khả năng giao tiếp BR1 ↔ BR2 phụ thuộc vào việc triển khai thêm định tuyến và chính sách VPN phù hợp tại HQ hoặc bổ sung đường hầm Full-Mesh. Không mặc định coi kết nối này đã hoạt động nếu chưa triển khai và kiểm thử.

## 9. Các dịch vụ mạng

### 9.1. DHCP tập trung

DHCP Server được đặt tại HQ với địa chỉ `192.168.10.10`.

Máy chủ cung cấp địa chỉ IP tự động cho các VLAN người dùng tại HQ, BR1 và BR2 thông qua các DHCP Pool và DHCP Relay được cấu hình trên Core Switch.

Các thông tin cấp phát bao gồm:
- IP Address.
- Subnet Mask.
- Default Gateway.
- DNS Server.

### 9.2. DNS

DNS Server được đặt tại HQ với địa chỉ `192.168.10.11`.

Máy chủ cung cấp chức năng phân giải tên miền cho các thiết bị được phép truy cập.

Thiết kế hiện tại chỉ sử dụng DNS Server này, không triển khai thêm DNS Server nội bộ khác.

### 9.3. Wireless LAN

Hệ thống Wi-Fi được triển khai tại HQ nhằm cung cấp kết nối không dây cho Laptop và các thiết bị di động.

Các thành phần:
- Access Point.
- SSID.
- WPA2-PSK.
- VLAN dành cho Wireless Client.
- DHCP cấp phát IP cho thiết bị không dây.

Thông số cấu hình thực tế được lưu trong thư mục `04-Network-Services/`.

## 10. Bảo mật mạng

Các giải pháp bảo mật được xem xét và triển khai trong phạm vi dự án:

- Phân đoạn mạng bằng VLAN.
- Kiểm soát truy cập giữa các mạng bằng ACL.
- Mã hóa lưu lượng trao đổi giữa các địa điểm bằng IPsec VPN.
- Bảo vệ kết nối Wi-Fi bằng WPA2-PSK.
- Kiểm soát truy cập vào các tài nguyên mạng theo chính sách được thiết kế.


## 11. Kiểm thử và xác minh

Dự án sử dụng các lệnh kiểm tra trên thiết bị Cisco và công cụ mô phỏng Packet Tracer để đánh giá hoạt động của hệ thống.

| Hạng mục | Nội dung kiểm tra | Kết quả |
|---|---|---|
| VLAN | Kiểm tra VLAN được tạo và gán đúng cổng | Chưa cập nhật |
| Trunk | Kiểm tra trạng thái trunk và VLAN được phép | Chưa cập nhật |
| Inter-VLAN Routing | Kiểm tra kết nối giữa các VLAN | Chưa cập nhật |
| OSPF | Kiểm tra trạng thái Neighbor và bảng định tuyến | Chưa cập nhật |
| DHCP | Kiểm tra cấp phát IP tự động tại các địa điểm | Chưa cập nhật |
| DNS | Kiểm tra khả năng phân giải tên miền | Chưa cập nhật |
| Wireless | Kiểm tra Laptop kết nối Wi-Fi và nhận IP | Chưa cập nhật |
| IPsec VPN | Kiểm tra trạng thái IKE/IPsec SA | Chưa cập nhật |
| Connectivity | Kiểm tra kết nối giữa các mạng được phép | Chưa cập nhật |
| Internet | Kiểm tra khả năng truy cập Internet | Chưa cập nhật |

Kết quả chi tiết và bằng chứng kiểm thử được lưu tại:
- `06-Testing-and-Troubleshooting/`
- `07-Screenshots/`
- `08-Test-Results/`

## 12. Xử lý sự cố (Troubleshooting)

Một số tình huống được đưa vào quá trình thực hành:

- Máy tính không nhận được địa chỉ IP từ DHCP Server.
- Thiết bị không thể giao tiếp với Default Gateway.
- VLAN không được truyền qua đường Trunk.
- Native VLAN mismatch.
- OSPF Neighbor không hình thành.
- Bảng định tuyến thiếu đường đi đến mạng đích.
- IPsec VPN không thiết lập được Security Association.
- VPN đã thiết lập nhưng không truyền được dữ liệu.
- Laptop không tìm thấy SSID hoặc không kết nối được Wi-Fi.
- Người dùng không truy cập được tài nguyên tại HQ.

Mỗi tình huống được ghi lại theo quy trình:

1. Xác định vấn đề.
2. Thu thập triệu chứng.
3. Kiểm tra và khoanh vùng nguyên nhân.
4. Xác định nguyên nhân gốc.
5. Thực hiện khắc phục.
6. Kiểm tra lại và lưu bằng chứng.


## 13. Cấu trúc Repository

```text
Enterprise-Multi-Branch-Network/
│
├── README.md
│
├── 01-Project-Overview/
│
├── 02-Network-Design/
│   ├── Physical-Topology.png
│   ├── Logical-Topology.png
│   ├── IP-Addressing-Plan.md
│   ├── VLAN-Plan.md
│   ├── Network-Architecture.md
│   └── Network-Flow.md
│
├── 03-Device-Configurations/
│   ├── ISP/
│   ├── HQ/
│   ├── BR1/
│   └── BR2/
│
├── 04-Network-Services/
│   ├── VLAN-and-Trunk.md
│   ├── Inter-VLAN-Routing.md
│   ├── OSPF.md
│   ├── DHCP-and-Relay.md
│   ├── DNS.md
│   ├── Wireless-Network.md
│   ├── Site-to-Site-IPsec-VPN.md
│   └── Internet-Connectivity.md
│
├── 05-Security/
│
├── 06-Testing-and-Troubleshooting/
│
├── 07-Screenshots/
│
├── 08-Test-Results/
│
└── 09-Project-Report/
```

## 14. Hướng phát triển trong tương lai

Một số nội dung có thể mở rộng sau khi hoàn thành thiết kế cơ sở:

- Bổ sung kết nối VPN trực tiếp giữa các chi nhánh.
- Triển khai dự phòng đường truyền WAN với hai ISP.
- Bổ sung HSRP hoặc VRRP để tăng tính sẵn sàng của Default Gateway.
- Triển khai Firewall và xây dựng chính sách bảo mật nâng cao.
- Bổ sung hệ thống giám sát mạng bằng Zabbix hoặc PRTG.
- Thu thập và phân tích Syslog, SNMP.
- Nghiên cứu QoS để ưu tiên lưu lượng quan trọng.
- Mở rộng mô hình với các dịch vụ doanh nghiệp khác.


