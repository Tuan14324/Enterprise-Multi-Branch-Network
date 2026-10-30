# KẾ HOẠCH PHÂN CHIA VLAN (VLAN PLAN)

## 1. Tổng quan

Tài liệu mô tả kế hoạch phân chia VLAN cho dự án Enterprise Multi-Branch Network.

VLAN được sử dụng để phân chia mạng thành các nhóm logic riêng biệt, phục vụ cho người dùng, máy chủ và thiết bị hạ tầng.

Dự án bao gồm ba địa điểm:

- Trụ sở chính (HQ).
- Chi nhánh 1 (BR1).
- Chi nhánh 2 (BR2).

## 2. Mục tiêu thiết kế VLAN

- Phân chia mạng theo phòng ban và nhóm thiết bị.
- Giảm lưu lượng Broadcast không cần thiết giữa các VLAN.
- Tăng khả năng tổ chức và quản lý mạng.
- Tạo nền tảng triển khai các chính sách bảo mật.
- Hỗ trợ định tuyến giữa các VLAN.
- Thuận tiện khi mở rộng hệ thống trong tương lai.

## 3. Bảng phân bổ VLAN

### 3.1. Trụ sở chính – HQ

| VLAN ID | Tên VLAN | Chức năng | Mạng IP | Gateway |
|---|---|---|---|---|
| 10 | SERVER | Máy chủ doanh nghiệp | 192.168.10.0/24 | 192.168.10.1 |
| 20 | ADMIN | Nhân viên hành chính | 192.168.20.0/24 | 192.168.20.1 |
| 30 | WIFI | Người dùng Wi-Fi | 192.168.30.0/24 | 192.168.30.1 |
| 40 | WLC-MGMT | Quản lý WLC | 192.168.40.0/24 | 192.168.40.1 |

### 3.2. Chi nhánh 1 – BR1

| VLAN ID | Tên VLAN | Chức năng | Mạng IP | Gateway |
|---|---|---|---|---|
| 50 | ADMIN | Nhân viên hành chính | 192.168.50.0/24 | 192.168.50.1 |
| 60 | STAFF | Nhân viên | 192.168.60.0/24 | 192.168.60.1 |

### 3.3. Chi nhánh 2 – BR2

| VLAN ID | Tên VLAN | Chức năng | Mạng IP | Gateway |
|---|---|---|---|---|
| 70 | USERS | Người dùng | 192.168.70.0/24 | 192.168.70.1 |

## 4. Phân bổ thiết bị theo VLAN

### 4.1. HQ

| Thiết bị | VLAN | Địa chỉ IP |
|---|---|---|
| DHCP Server | 10 | 192.168.10.10 |
| DNS Server | 10 | 192.168.10.11 |
| Máy tính hành chính | 20 | DHCP |
| Thiết bị Wi-Fi | 30 | DHCP |
| WLC | 40 | 192.168.40.10 |
| Địa chỉ quản lý Access Switch | 40 | Ví dụ: 192.168.40.2 |

### 4.2. BR1

| Thiết bị | VLAN | Địa chỉ IP |
|---|---|---|
| Máy tính hành chính | 50 | DHCP |
| Máy tính nhân viên | 60 | DHCP |
| Địa chỉ quản lý Access Switch | 50 | Ví dụ: 192.168.50.2 |

### 4.3. BR2

| Thiết bị | VLAN | Địa chỉ IP |
|---|---|---|
| Máy tính người dùng | 70 | DHCP |
| Máy in mạng | 70 | 192.168.70.10 |
| Địa chỉ quản lý Access Switch | 70 | Ví dụ: 192.168.70.2 |

Các địa chỉ quản lý Access Switch là địa chỉ tham khảo, cần điều chỉnh theo cấu hình thực tế.

## 5. Thiết kế Default Gateway cho VLAN

Core Layer 3 Switch đảm nhiệm chức năng Default Gateway cho từng VLAN.

Ví dụ cấu hình SVI tại HQ:

| VLAN | Interface SVI | Địa chỉ IP |
|---|---|---|
| 10 | Vlan10 | 192.168.10.1/24 |
| 20 | Vlan20 | 192.168.20.1/24 |
| 30 | Vlan30 | 192.168.30.1/24 |
| 40 | Vlan40 | 192.168.40.1/24 |

Cấu hình mẫu:

```cisco
ip routing

interface vlan 10
 description SERVER
 ip address 192.168.10.1 255.255.255.0
 no shutdown

interface vlan 20
 description ADMIN
 ip address 192.168.20.1 255.255.255.0
 no shutdown

interface vlan 30
 description WIFI
 ip address 192.168.30.1 255.255.255.0
 no shutdown

interface vlan 40
 description WLC-MGMT
 ip address 192.168.40.1 255.255.255.0
 no shutdown
```

Tại BR1 và BR2, triển khai các SVI tương ứng với VLAN đã được quy hoạch cho từng chi nhánh.

Lưu ý: SVI chỉ chuyển sang trạng thái hoạt động khi VLAN tồn tại và có ít nhất một cổng Layer 2 đang hoạt động thuộc VLAN đó, hoặc có điều kiện hoạt động tương ứng trên thiết bị.

## 6. Thiết kế cổng Access

Cổng Access kết nối thiết bị đầu cuối và được gán vào VLAN tương ứng.

Ví dụ phân bổ cổng tại HQ:

| Interface | Loại thiết bị | VLAN |
|---|---|---|
| Fa0/1–Fa0/2 | Máy chủ | 10 |
| Fa0/3–Fa0/8 | Máy tính hành chính | 20 |
| Fa0/9–Fa0/12 | Access Point | 30 |
| Fa0/13 | WLC | 40 |

Ví dụ phân bổ cổng tại BR1:

| Interface | Loại thiết bị | VLAN |
|---|---|---|
| Fa0/1–Fa0/8 | Máy tính hành chính | 50 |
| Fa0/9–Fa0/20 | Máy tính nhân viên | 60 |

Ví dụ phân bổ cổng tại BR2:

| Interface | Loại thiết bị | VLAN |
|---|---|---|
| Fa0/1–Fa0/20 | Máy tính người dùng | 70 |
| Fa0/21 | Máy in | 70 |

Đây là bảng phân bổ cổng tham khảo. Cần điều chỉnh theo số lượng thiết bị và cổng kết nối thực tế trong Packet Tracer.

## 7. Thiết kế cổng Trunk

Đường Trunk được sử dụng để truyền lưu lượng của nhiều VLAN giữa Core Switch và Access Switch.

| Địa điểm | Kết nối Trunk | VLAN được phép |
|---|---|---|
| HQ | CORE-HQ ↔ ACCESS-HQ | 10,20,30,40 |
| BR1 | CORE-BR1 ↔ ACCESS-BR1 | 50,60 |
| BR2 | CORE-BR2 ↔ ACCESS-BR2 | 70 |

Native VLAN phải được cấu hình thống nhất ở cả hai đầu kết nối Trunk.

Thiết kế hiện tại sử dụng Native VLAN mặc định là VLAN 1. Nếu thay đổi sang VLAN khác, cần cập nhật đồng bộ ở cả hai đầu.

## 8. Thiết kế DHCP theo VLAN

DHCP Server được triển khai tập trung tại HQ, thuộc VLAN 10.

| VLAN | DHCP Scope | Default Gateway | DNS |
|---|---|---|---|
| 20 | 192.168.20.0/24 | 192.168.20.1 | 192.168.10.11 |
| 30 | 192.168.30.0/24 | 192.168.30.1 | 192.168.10.11 |
| 40 | 192.168.40.0/24 | 192.168.40.1 | 192.168.10.11 |
| 50 | 192.168.50.0/24 | 192.168.50.1 | 192.168.10.11 |
| 60 | 192.168.60.0/24 | 192.168.60.1 | 192.168.10.11 |
| 70 | 192.168.70.0/24 | 192.168.70.1 | 192.168.10.11 |

VLAN 10 sử dụng IP tĩnh cho các máy chủ.

Các VLAN người dùng cần được cấu hình DHCP Relay trên SVI tương ứng khi DHCP Server nằm ở subnet khác.

## 9. Các nguyên tắc bảo mật VLAN

Các biện pháp được khuyến nghị:

- Vô hiệu hóa các cổng switch không sử dụng.
- Hạn chế sử dụng VLAN 1 cho lưu lượng người dùng thông thường.
- Sử dụng Native VLAN thống nhất trên các đường Trunk.
- Giới hạn danh sách VLAN được phép đi qua Trunk.
- Bật PortFast trên các cổng Access kết nối thiết bị đầu cuối phù hợp.
- Bật BPDU Guard trên các cổng Edge phù hợp.
- Áp dụng ACL khi cần giới hạn giao tiếp giữa các VLAN.
- Giới hạn quyền quản trị thiết bị mạng cho người được phép.

VLAN chỉ cung cấp khả năng phân đoạn mạng. Để kiểm soát truy cập giữa các VLAN cần triển khai thêm ACL hoặc chính sách Firewall.

## 10. Các lệnh kiểm tra

```cisco
show vlan brief
show interfaces trunk
show interfaces switchport
show ip interface brief
show ip route
```

## 11. Danh sách kiểm thử

| STT | Nội dung kiểm tra | Kết quả mong đợi | Trạng thái |
|---|---|---|---|
| 1 | Kiểm tra VLAN | Tất cả VLAN cần thiết đã được tạo | Pending |
| 2 | Kiểm tra cổng Access | Các cổng thuộc đúng VLAN | Pending |
| 3 | Kiểm tra đường Trunk | Các VLAN cần thiết được cho phép | Pending |
| 4 | Kiểm tra SVI | Các Gateway hoạt động | Pending |
| 5 | Kiểm tra DHCP | Máy tính nhận đúng địa chỉ IP | Pending |
| 6 | Kiểm tra Inter-VLAN Routing | Các VLAN được phép có thể giao tiếp | Pending |
| 7 | Kiểm tra kết nối giữa các chi nhánh | Các mạng được VPN bảo vệ có thể giao tiếp | Pending |

## 12. Kết luận

Kế hoạch VLAN cung cấp phương án phân chia mạng logic cho HQ, BR1 và BR2.

Tài liệu xác định VLAN ID, tên VLAN, subnet, Default Gateway, nhóm thiết bị và các kết nối Trunk.

Đây là cơ sở để triển khai Switching, Inter-VLAN Routing, DHCP Relay và các chính sách bảo mật mạng.