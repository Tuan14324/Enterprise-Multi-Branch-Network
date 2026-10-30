
# 05. Wireless Configuration

## 1. Mục tiêu

Triển khai hệ thống mạng Wi-Fi tại HQ để cung cấp kết nối không dây cho người dùng.

Wireless Network sử dụng:
- Wireless LAN Controller (WLC).
- Access Point (AP).
- Wireless Client.
- VLAN 30 dành cho Wireless Client.
- VLAN 40 dành cho WLC Management.

## 2. Wireless Design

| Component | IP Address | VLAN |
|---|---|---|
| WLC | 192.168.40.10/24 | VLAN 40 |
| WLC Gateway | 192.168.40.1 | VLAN 40 |
| Wireless Client | DHCP | VLAN 30 |
| Wireless Client Gateway | 192.168.30.1 | VLAN 30 |

SSID ví dụ:

```text
SSID: HQ-WIFI
Security: WPA2-PSK
Authentication: Pre-Shared Key
```

Mật khẩu trong lab chỉ dùng cho mục đích mô phỏng, không sử dụng lại mật khẩu cá nhân.

## 3. WLC Configuration

Cấu hình địa chỉ quản trị WLC:

```text
Management IP: 192.168.40.10
Subnet Mask:   255.255.255.0
Gateway:       192.168.40.1
VLAN:          40
```

Cấu hình WLAN/SSID:

```text
WLAN Profile: HQ-WIFI
SSID:         HQ-WIFI
Client VLAN:  30
Security:     WPA2-PSK
```

Tên menu và cách cấu hình có thể khác nhau tùy loại WLC được sử dụng trong Packet Tracer.

## 4. Access Point Configuration

- Kết nối AP vào switch theo đúng mô hình.
- Đảm bảo AP có kết nối Layer 2 đến mạng quản trị hoặc VLAN mà mô hình yêu cầu.
- Đảm bảo AP có thể liên lạc với WLC.
- Kiểm tra trạng thái AP trên WLC.

Nếu sử dụng Lightweight AP, AP cần tìm và đăng ký với WLC theo cơ chế mà thiết bị Packet Tracer hỗ trợ.

## 5. Wireless Client Configuration

Trên Laptop:

1. Kiểm tra Wireless Adapter.
2. Chọn danh sách Wireless Networks.
3. Tìm SSID `HQ-WIFI`.
4. Nhập Pre-Shared Key.
5. Kết nối vào mạng.
6. Chọn DHCP để nhận IP.

Thông tin mong đợi:

```text
IP Address:      192.168.30.x
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.30.1
DNS Server:      192.168.10.11
```

## 6. Verification

Kiểm tra:
- WLC có thể truy cập qua mạng quản trị.
- AP đã đăng ký với WLC nếu mô hình sử dụng Lightweight AP.
- Wireless Client kết nối được SSID.
- Wireless Client nhận IP thuộc VLAN 30.
- Wireless Client truy cập được các dịch vụ được phép.

Các lệnh kiểm tra trên thiết bị Cisco có thể bao gồm:

```cisco
show ip interface brief
show vlan brief
show interfaces trunk
```

Các lệnh WLC phụ thuộc vào model và hệ điều hành của WLC.

## 7. Troubleshooting

| Vấn đề | Nguyên nhân có thể | Hướng xử lý |
|---|---|---|
| Không tìm thấy SSID | WLAN chưa được bật hoặc AP chưa phát SSID | Kiểm tra WLAN và AP |
| Không kết nối được Wi-Fi | Sai PSK hoặc sai bảo mật | Kiểm tra WPA2 và mật khẩu |
| Client không nhận IP | DHCP Pool hoặc VLAN 30 lỗi | Kiểm tra DHCP và VLAN |
| AP không đăng ký WLC | Sai kết nối quản trị hoặc discovery | Kiểm tra IP, gateway và khả năng liên lạc |
| Client kết nối nhưng không truy cập mạng | Gateway hoặc routing lỗi | Kiểm tra SVI VLAN 30 và routing |

## 8. Kết quả mong đợi

Wireless Client kết nối thành công vào SSID HQ-WIFI, nhận IP từ DHCP Pool VLAN 30 và truy cập được các dịch vụ mạng theo chính sách được cấu hình.
