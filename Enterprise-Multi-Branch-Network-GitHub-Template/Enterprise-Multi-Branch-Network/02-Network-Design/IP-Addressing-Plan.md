# KẾ HOẠCH QUY HOẠCH ĐỊA CHỈ IP (IP ADDRESSING PLAN)

## 1. Tổng quan

Tài liệu này mô tả kế hoạch quy hoạch địa chỉ IPv4 cho dự án Enterprise Multi-Branch Network.

Hệ thống bao gồm:
- Trụ sở chính (HQ).
- Chi nhánh 1 (BR1).
- Chi nhánh 2 (BR2).
- Mạng WAN của nhà cung cấp dịch vụ Internet (ISP).

Địa chỉ IP được chia thành hai nhóm:
- Địa chỉ IP công cộng mô phỏng các kết nối WAN giữa các địa điểm.
- Địa chỉ IP riêng được sử dụng cho các mạng LAN nội bộ.

## 2. Quy hoạch địa chỉ IP WAN

Các kết nối WAN được thiết lập giữa router ISP và router tại từng địa điểm.

| Kết nối | Thiết bị | Địa chỉ IP | Subnet Mask |
|---|---|---|---|
| ISP – HQ | ISP Router | 200.1.0.1 | /30 |
| ISP – HQ | HQ Router | 200.1.0.2 | /30 |
| ISP – BR1 | ISP Router | 200.1.1.1 | /30 |
| ISP – BR1 | BR1 Router | 200.1.1.2 | /30 |
| ISP – BR2 | ISP Router | 200.1.2.1 | /30 |
| ISP – BR2 | BR2 Router | 200.1.2.2 | /30 |

Các mạng /30 phù hợp với kết nối điểm-điểm vì mỗi mạng cung cấp hai địa chỉ IP có thể sử dụng cho thiết bị đầu cuối.

## 3. Quy hoạch địa chỉ IP kết nối Router – Core Switch

Mỗi địa điểm sử dụng một mạng /30 riêng để kết nối Router với Core Layer 3 Switch.

| Địa điểm | Thiết bị | Địa chỉ IP | Mạng |
|---|---|---|---|
| HQ | HQ Router | 10.255.0.1/30 | 10.255.0.0/30 |
| HQ | CORE-HQ | 10.255.0.2/30 | 10.255.0.0/30 |
| BR1 | BR1 Router | 10.255.0.5/30 | 10.255.0.4/30 |
| BR1 | CORE-BR1 | 10.255.0.6/30 | 10.255.0.4/30 |
| BR2 | BR2 Router | 10.255.0.9/30 | 10.255.0.8/30 |
| BR2 | CORE-BR2 | 10.255.0.10/30 | 10.255.0.8/30 |

## 4. Quy hoạch IP mạng LAN tại HQ

| VLAN ID | Tên VLAN | Mạng IP | Default Gateway | Subnet Mask |
|---|---|---|---|---|
| 10 | SERVER | 192.168.10.0/24 | 192.168.10.1 | 255.255.255.0 |
| 20 | ADMIN | 192.168.20.0/24 | 192.168.20.1 | 255.255.255.0 |
| 30 | WIFI | 192.168.30.0/24 | 192.168.30.1 | 255.255.255.0 |
| 40 | WLC-MGMT | 192.168.40.0/24 | 192.168.40.1 | 255.255.255.0 |

## 5. Quy hoạch IP mạng LAN tại BR1

| VLAN ID | Tên VLAN | Mạng IP | Default Gateway | Subnet Mask |
|---|---|---|---|---|
| 50 | ADMIN | 192.168.50.0/24 | 192.168.50.1 | 255.255.255.0 |
| 60 | STAFF | 192.168.60.0/24 | 192.168.60.1 | 255.255.255.0 |

## 6. Quy hoạch IP mạng LAN tại BR2

| VLAN ID | Tên VLAN | Mạng IP | Default Gateway | Subnet Mask |
|---|---|---|---|---|
| 70 | USERS | 192.168.70.0/24 | 192.168.70.1 | 255.255.255.0 |

## 7. Quy hoạch IP cho các thiết bị hạ tầng

| Thiết bị | Vị trí | Địa chỉ IP | Default Gateway |
|---|---|---|---|
| DHCP Server | HQ | 192.168.10.10/24 | 192.168.10.1 |
| DNS Server | HQ | 192.168.10.11/24 | 192.168.10.1 |
| WLC | HQ | 192.168.40.10/24 | 192.168.40.1 |
| Printer | BR2 | 192.168.70.10/24 | 192.168.70.1 |

Các thiết bị hạ tầng được gán địa chỉ IP tĩnh để thuận tiện cho việc quản trị và truy cập.

## 8. Quy hoạch cấp phát địa chỉ DHCP

DHCP Server được triển khai tập trung tại HQ với địa chỉ `192.168.10.10`.

Máy chủ DHCP thực hiện cấp phát địa chỉ IP động cho các VLAN người dùng.

| VLAN | Mạng IP | Gateway | Địa chỉ bắt đầu cấp phát | DNS |
|---|---|---|---|---|
| 20 | 192.168.20.0/24 | 192.168.20.1 | 192.168.20.100 | 192.168.10.11 |
| 30 | 192.168.30.0/24 | 192.168.30.1 | 192.168.30.100 | 192.168.10.11 |
| 40 | 192.168.40.0/24 | 192.168.40.1 | 192.168.40.100 | 192.168.10.11 |
| 50 | 192.168.50.0/24 | 192.168.50.1 | 192.168.50.100 | 192.168.10.11 |
| 60 | 192.168.60.0/24 | 192.168.60.1 | 192.168.60.100 | 192.168.10.11 |
| 70 | 192.168.70.0/24 | 192.168.70.1 | 192.168.70.100 | 192.168.10.11 |

VLAN 10 được dành cho máy chủ và sử dụng địa chỉ IP tĩnh.

Các địa chỉ IP đã được sử dụng cho thiết bị tĩnh cần được loại trừ khỏi dải cấp phát DHCP.

Để DHCP hoạt động giữa các chi nhánh và máy chủ tại HQ, cần cấu hình DHCP Relay trên các SVI tương ứng:

```cisco
interface vlan 50
 ip helper-address 192.168.10.10
```

Cấu hình thực tế cần được thực hiện trên các VLAN client tương ứng.

Ngoài ra, DHCP qua VPN yêu cầu:
- Định tuyến hai chiều chính xác.
- VPN Selector cho phép lưu lượng cần thiết.
- DHCP Scope được cấu hình đúng.
- Có đường đi phản hồi từ DHCP Server về mạng chi nhánh.

## 9. Tổng hợp quy hoạch địa chỉ IP

| Nhóm mạng | Dải địa chỉ |
|---|---|
| WAN | 200.1.0.0/30, 200.1.1.0/30, 200.1.2.0/30 |
| Router – Core Transit | 10.255.0.0/30, 10.255.0.4/30, 10.255.0.8/30 |
| HQ LAN | 192.168.10.0/24 – 192.168.40.0/24 |
| BR1 LAN | 192.168.50.0/24 – 192.168.60.0/24 |
| BR2 LAN | 192.168.70.0/24 |

## 10. Nguyên tắc thiết kế địa chỉ IP

- Mỗi VLAN sử dụng một subnet riêng.
- Mỗi địa điểm có một mạng transit riêng giữa Router và Core Switch.
- Các kết nối WAN điểm-điểm sử dụng subnet /30.
- Máy chủ và thiết bị hạ tầng sử dụng IP tĩnh.
- Máy tính người dùng nhận IP thông qua DHCP.
- Các mạng tại HQ, BR1 và BR2 không bị trùng lặp địa chỉ.
- Địa chỉ WAN trong mô hình là địa chỉ mô phỏng phục vụ học tập, không phải kế hoạch cấp phát IP công cộng thực tế.

## 11. Kết luận

Kế hoạch quy hoạch địa chỉ IP cung cấp một cấu trúc IPv4 thống nhất cho toàn bộ hệ thống mạng doanh nghiệp.

Đây là cơ sở để triển khai VLAN, định tuyến nội bộ, kết nối WAN, DHCP, DNS và VPN Site-to-Site.