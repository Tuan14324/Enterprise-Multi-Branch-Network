# THIẾT KẾ KIẾN TRÚC MẠNG (NETWORK ARCHITECTURE)

## 1. Tổng quan

Tài liệu mô tả kiến trúc tổng thể của dự án Enterprise Multi-Branch Network.

Hệ thống kết nối ba địa điểm thông qua mạng của nhà cung cấp dịch vụ Internet (ISP):

- Trụ sở chính (HQ).
- Chi nhánh 1 (BR1).
- Chi nhánh 2 (BR2).

Mô hình sử dụng kiến trúc tập trung tại HQ kết hợp với VPN Site-to-Site IPsec theo mô hình Hub-and-Spoke.

## 2. Mục tiêu thiết kế

- Kết nối các địa điểm của doanh nghiệp.
- Đảm bảo liên lạc an toàn qua mạng ISP.
- Phân chia mạng theo từng phòng ban bằng VLAN.
- Hỗ trợ định tuyến giữa các VLAN.
- Triển khai tập trung dịch vụ DHCP và DNS tại HQ.
- Xây dựng hạ tầng mạng có khả năng mở rộng.
- Tạo môi trường thực hành cấu hình và xử lý sự cố mạng doanh nghiệp.

## 3. Kiến trúc tổng thể

```text
                       ISP NETWORK
                    +---------------+
                    |   ISP Router  |
                    +---------------+
                      /     |     \
                     /      |      \
                    /       |       \
                HQ Router  BR1 RTR  BR2 RTR
                    |        |        |
                 CORE-HQ  CORE-BR1 CORE-BR2
                    |        |        |
                 ACCESS   ACCESS   ACCESS
                    |        |        |
                HQ USERS  BR1 USERS BR2 USERS
```

Router tại HQ thiết lập hai đường hầm VPN IPsec:

- HQ ↔ BR1.
- HQ ↔ BR2.

Trong mô hình này:
- HQ đóng vai trò Hub.
- BR1 và BR2 đóng vai trò Spoke.
- Không triển khai đường hầm VPN trực tiếp giữa BR1 và BR2.

## 4. Kiến trúc phân tầng mạng

### 4.1. Tầng WAN

Tầng WAN đảm nhiệm kết nối giữa các địa điểm doanh nghiệp thông qua mạng ISP.

Các thiết bị:
- ISP Router.
- HQ Router.
- BR1 Router.
- BR2 Router.

Chức năng:
- Kết nối WAN.
- Mô phỏng kết nối giữa các địa điểm.
- Kết thúc các đường hầm VPN IPsec.
- Chuyển tiếp lưu lượng VPN đã mã hóa.

### 4.2. Tầng Core

Mỗi địa điểm sử dụng một Core Layer 3 Switch.

Chức năng:
- Định tuyến giữa các VLAN (Inter-VLAN Routing).
- Cung cấp Default Gateway cho các VLAN.
- Thiết lập OSPF với Router tại cùng địa điểm.
- Chuyển tiếp lưu lượng giữa LAN và Router.
- Chuyển tiếp yêu cầu DHCP thông qua DHCP Relay.

### 4.3. Tầng Access

Access Switch cung cấp kết nối Layer 2 cho các thiết bị đầu cuối.

Các thiết bị kết nối:
- Máy tính.
- Máy chủ.
- Máy in.
- Điểm truy cập không dây (Access Point).
- Các thiết bị người dùng không dây thông qua hệ thống Wi-Fi.

Chức năng:
- Gán cổng vào VLAN.
- Cấu hình cổng Access.
- Cấu hình đường Trunk kết nối Core Switch.
- Chuyển tiếp dữ liệu ở Layer 2.

## 5. Kiến trúc tại từng địa điểm

### 5.1. Trụ sở chính – HQ

HQ là địa điểm trung tâm của hệ thống.

Các thành phần:
- HQ Router.
- CORE-HQ.
- ACCESS-HQ.
- DHCP Server.
- DNS Server.
- Wireless LAN Controller (WLC).
- Wireless Access Points.
- Máy tính nhân viên.
- Máy chủ doanh nghiệp.

Các VLAN tại HQ:

| VLAN | Chức năng |
|---|---|
| 10 | Máy chủ |
| 20 | Nhân viên hành chính |
| 30 | Người dùng Wi-Fi |
| 40 | Quản lý WLC |

Vai trò của HQ:
- Cung cấp các dịch vụ mạng tập trung.
- Là đầu mối VPN Hub.
- Kết nối và trao đổi dữ liệu với các chi nhánh.
- Lưu trữ và cung cấp tài nguyên máy chủ.

### 5.2. Chi nhánh 1 – BR1

Các thành phần:
- BR1 Router.
- CORE-BR1.
- ACCESS-BR1.
- Máy tính bộ phận hành chính.
- Máy tính nhân viên.

Các VLAN tại BR1:

| VLAN | Chức năng |
|---|---|
| 50 | Nhân viên hành chính |
| 60 | Nhân viên |

BR1 thiết lập đường hầm VPN Site-to-Site IPsec đến HQ.

### 5.3. Chi nhánh 2 – BR2

Các thành phần:
- BR2 Router.
- CORE-BR2.
- ACCESS-BR2.
- Máy tính người dùng.
- Máy in mạng.

Các VLAN tại BR2:

| VLAN | Chức năng |
|---|---|
| 70 | Người dùng |

BR2 thiết lập đường hầm VPN Site-to-Site IPsec đến HQ.

## 6. Kiến trúc định tuyến

OSPF được sử dụng để trao đổi thông tin định tuyến giữa Core Switch và Router trong phạm vi từng địa điểm.

| Địa điểm | Giao thức định tuyến | Phạm vi |
|---|---|---|
| HQ | OSPF | Core-HQ và HQ Router |
| BR1 | OSPF | Core-BR1 và BR1 Router |
| BR2 | OSPF | Core-BR2 và BR2 Router |

Các Router tại từng địa điểm sử dụng Static Route để định tuyến đến các mạng LAN từ xa và Default Route hướng về ISP.

OSPF không được triển khai trên mạng ISP hoặc qua các đường hầm VPN trong thiết kế hiện tại.

## 7. Kiến trúc VPN

Dự án sử dụng VPN Site-to-Site IPsec theo mô hình Hub-and-Spoke.

| Đường hầm | Thiết bị Hub | Thiết bị Spoke |
|---|---|---|
| VPN 1 | HQ | BR1 |
| VPN 2 | HQ | BR2 |

VPN cung cấp:
- Mã hóa dữ liệu.
- Đảm bảo tính toàn vẹn dữ liệu.
- Xác thực các thiết bị VPN Peer.
- Bảo vệ dữ liệu khi truyền qua mạng ISP.

Các VPN Selector xác định lưu lượng mạng nội bộ nào được mã hóa.

## 8. Kiến trúc dịch vụ mạng tập trung

HQ triển khai các dịch vụ mạng dùng chung:

| Dịch vụ | Địa chỉ IP | Chức năng |
|---|---|---|
| DHCP Server | 192.168.10.10 | Cấp phát địa chỉ IP động |
| DNS Server | 192.168.10.11 | Phân giải tên miền |

Các VLAN người dùng tại HQ và chi nhánh sử dụng DHCP Relay để gửi yêu cầu đến DHCP Server tại HQ.

DNS Server cung cấp dịch vụ phân giải tên cho các tài nguyên nội bộ.

## 9. Nguyên tắc thiết kế bảo mật

Kiến trúc áp dụng các nguyên tắc cơ bản:

- Phân chia các nhóm thiết bị bằng VLAN.
- Sử dụng VPN IPsec để bảo vệ lưu lượng giữa các địa điểm.
- Tách riêng mạng người dùng và mạng máy chủ.
- Sử dụng IP tĩnh cho máy chủ và thiết bị hạ tầng.
- Triển khai ACL hoặc chính sách Firewall khi cần giới hạn truy cập.
- Không cho phép mạng nội bộ bị truy cập trực tiếp từ mạng ISP.

Lưu ý: VLAN chỉ cung cấp khả năng phân đoạn mạng ở Layer 2. Việc kiểm soát truy cập giữa các VLAN cần có thêm ACL hoặc chính sách bảo mật phù hợp.

## 10. Khả năng mở rộng và cải tiến

Các hướng nâng cấp trong tương lai:

- Triển khai hai đường truyền ISP.
- Dự phòng Router hoặc Firewall.
- Sử dụng HSRP hoặc VRRP để dự phòng Default Gateway.
- Triển khai Firewall High Availability.
- Giám sát mạng bằng Zabbix hoặc PRTG.
- Thu thập Syslog và SNMP.
- Triển khai Network Access Control.
- Bổ sung kết nối VPN trực tiếp giữa các chi nhánh nếu có yêu cầu.

## 11. Kết luận

Kiến trúc mạng được thiết kế nhằm kết nối HQ và hai chi nhánh thông qua mạng ISP.

Mô hình kết hợp Switching Layer 2, Routing Layer 3, OSPF, Static Routing, các dịch vụ mạng tập trung và VPN Site-to-Site IPsec.

Đây là nền tảng để triển khai, kiểm thử và xử lý sự cố trong môi trường mạng doanh nghiệp.