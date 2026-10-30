
# 03. DHCP Configuration

## 1. Mục tiêu

Triển khai DHCP Server tập trung tại HQ để tự động cấp phát địa chỉ IP cho các thiết bị đầu cuối tại HQ, BR1 và BR2.

DHCP giúp:
- Tự động cấp IP Address.
- Cấp Subnet Mask.
- Cấp Default Gateway.
- Cấp DNS Server.
- Giảm cấu hình IP thủ công.

## 2. DHCP Server Design

DHCP Server được triển khai tại HQ:

| Parameter | Value |
|---|---|
| Server IP | 192.168.10.10 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.10.1 |
| DNS Server | 192.168.10.11 |
| VLAN | VLAN 10 - SERVER |

DHCP Server sử dụng IP tĩnh.

## 3. DHCP Pool Design

| Pool | Network | Default Gateway | DNS Server |
|---|---|---|---|
| HQ-ADMIN | 192.168.20.0/24 | 192.168.20.1 | 192.168.10.11 |
| HQ-WIFI | 192.168.30.0/24 | 192.168.30.1 | 192.168.10.11 |
| HQ-WLC | 192.168.40.0/24 | 192.168.40.1 | 192.168.10.11 |
| BR1-ADMIN | 192.168.50.0/24 | 192.168.50.1 | 192.168.10.11 |
| BR1-STAFF | 192.168.60.0/24 | 192.168.60.1 | 192.168.10.11 |
| BR2-USERS | 192.168.70.0/24 | 192.168.70.1 | 192.168.10.11 |

DHCP cấp phát địa chỉ bắt đầu từ .100.

Các địa chỉ từ .1 đến .99 được dành cho gateway, thiết bị hạ tầng, server, printer hoặc các thiết bị sử dụng IP tĩnh theo quy hoạch.

## 4. DHCP Server Configuration

Trong Cisco Packet Tracer:

1. Chọn Server-PT.
2. Vào Desktop > IP Configuration.
3. Cấu hình IP tĩnh:

```text
IP Address:      192.168.10.10
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.10.1
DNS Server:      192.168.10.11
```

4. Chọn Services > DHCP.
5. Bật DHCP Service.
6. Tạo các DHCP Pool theo bảng DHCP Pool Design.

Ví dụ pool HQ-ADMIN:

```text
Pool Name:       HQ-ADMIN
Default Gateway: 192.168.20.1
DNS Server:      192.168.10.11
Start IP:        192.168.20.100
Subnet Mask:     255.255.255.0
Maximum Users:   100
```

Tạo các pool còn lại theo cùng nguyên tắc.

VLAN 10 chứa các server sử dụng IP tĩnh nên không cần DHCP pool trong thiết kế này.

## 5. DHCP Relay Configuration

DHCP Server nằm trong VLAN 10 tại HQ. Các VLAN khác subnet cần sử dụng DHCP Relay để chuyển tiếp DHCP request đến Server.

Cấu hình trên SVI của Core Switch.

### CORE-HQ

```cisco
interface vlan 20
 ip helper-address 192.168.10.10

interface vlan 30
 ip helper-address 192.168.10.10

interface vlan 40
 ip helper-address 192.168.10.10
```

### CORE-BR1

```cisco
interface vlan 50
 ip helper-address 192.168.10.10

interface vlan 60
 ip helper-address 192.168.10.10
```

### CORE-BR2

```cisco
interface vlan 70
 ip helper-address 192.168.10.10
```

Lưu ý:
- Các Core Switch phải có đường định tuyến đến DHCP Server.
- DHCP Server phải có đường trả về các subnet client.
- DHCP traffic từ các chi nhánh phải đi qua VPN nếu được bảo vệ bởi IPsec.
- VPN selectors và ACL phải cho phép các luồng DHCP cần thiết nếu triển khai DHCP Relay qua VPN.
- Cần kiểm thử DHCP sau khi VPN và routing hoạt động.

## 6. DHCP Client Configuration

Trên PC trong Packet Tracer:

1. Chọn PC.
2. Desktop > IP Configuration.
3. Chọn DHCP.
4. Kiểm tra thông tin được cấp phát.

Thông tin mong đợi:
- IP Address thuộc subnet VLAN tương ứng.
- Subnet Mask đúng.
- Default Gateway đúng.
- DNS Server là 192.168.10.11.

## 7. Verification

Trên PC:

```text
ipconfig /all
```

Kiểm tra kết nối:

```text
ping 192.168.10.1
ping 192.168.10.10
ping 192.168.10.11
```

Trên Core Switch:

```cisco
show running-config
show ip interface brief
show ip route
```

## 8. Troubleshooting

| Vấn đề | Nguyên nhân có thể | Hướng xử lý |
|---|---|---|
| Client không nhận IP | DHCP Service chưa bật | Kiểm tra Services > DHCP |
| Client nhận APIPA | Không nhận được DHCP Offer | Kiểm tra VLAN, Relay và đường truyền |
| HQ nhận IP nhưng BR1/BR2 không nhận | Chưa có DHCP Relay hoặc VPN chưa hoạt động | Kiểm tra `ip helper-address`, routing và VPN |
| Client nhận sai Gateway | Pool khai báo sai | Kiểm tra Default Gateway trong DHCP Pool |
| Client nhận sai DNS | Pool khai báo sai DNS | Sửa DNS Server thành 192.168.10.11 |

## 9. Kết quả mong đợi

Các client thuộc những VLAN được triển khai DHCP có thể nhận địa chỉ IP tự động từ DHCP Server tại HQ.

Kết quả phải được xác nhận bằng `ipconfig /all` và kiểm tra kết nối thực tế.
