
# 01. VLAN, Trunk và Inter-VLAN Routing

## 1. Mục tiêu

Triển khai phân chia mạng LAN thành các VLAN riêng biệt nhằm:
- Phân tách các phòng ban và nhóm người dùng.
- Giảm broadcast domain.
- Tăng khả năng quản lý và bảo mật.
- Cho phép các VLAN giao tiếp thông qua Layer 3 Switch.

## 2. VLAN Design

### HQ

| VLAN | Name | Network | Default Gateway |
|---|---|---|---|
| 10 | SERVER | 192.168.10.0/24 | 192.168.10.1 |
| 20 | ADMIN | 192.168.20.0/24 | 192.168.20.1 |
| 30 | WIFI | 192.168.30.0/24 | 192.168.30.1 |
| 40 | WLC-MGMT | 192.168.40.0/24 | 192.168.40.1 |

### BR1

| VLAN | Name | Network | Default Gateway |
|---|---|---|---|
| 50 | ADMIN | 192.168.50.0/24 | 192.168.50.1 |
| 60 | STAFF | 192.168.60.0/24 | 192.168.60.1 |

### BR2

| VLAN | Name | Network | Default Gateway |
|---|---|---|---|
| 70 | USERS | 192.168.70.0/24 | 192.168.70.1 |

## 3. VLAN Configuration

Ví dụ cấu hình trên Core Switch tại HQ:

```cisco
enable
configure terminal

vlan 10
 name SERVER
vlan 20
 name ADMIN
vlan 30
 name WIFI
vlan 40
 name WLC-MGMT

end
write memory
```

Tại BR1:

```cisco
configure terminal

vlan 50
 name ADMIN
vlan 60
 name STAFF

end
write memory
```

Tại BR2:

```cisco
configure terminal

vlan 70
 name USERS

end
write memory
```

## 4. Access Port Configuration

Ví dụ kết nối PC vào VLAN 20 tại HQ:

```cisco
interface fastEthernet 0/1
 switchport mode access
 switchport access vlan 20
 spanning-tree portfast
```

Ví dụ kết nối Server vào VLAN 10:

```cisco
interface fastEthernet 0/2
 switchport mode access
 switchport access vlan 10
 spanning-tree portfast
```

Lưu ý: Chỉ sử dụng `spanning-tree portfast` trên cổng kết nối thiết bị đầu cuối, không bật tùy tiện trên đường trunk giữa các switch.

## 5. Trunk Configuration

Ví dụ cấu hình trunk giữa Core Switch và Access Switch:

```cisco
interface gigabitEthernet 0/1
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40
```

Tại BR1:

```cisco
interface gigabitEthernet 0/1
 switchport mode trunk
 switchport trunk allowed vlan 50,60
```

Tại BR2:

```cisco
interface gigabitEthernet 0/1
 switchport mode trunk
 switchport trunk allowed vlan 70
```

Các VLAN được phép trên trunk phải phù hợp với VLAN thực tế cần truyền qua liên kết.

## 6. Inter-VLAN Routing

Core Switch sử dụng chức năng Layer 3 để định tuyến giữa các VLAN.

Ví dụ tại CORE-HQ:

```cisco
configure terminal

ip routing

interface vlan 10
 ip address 192.168.10.1 255.255.255.0
 no shutdown

interface vlan 20
 ip address 192.168.20.1 255.255.255.0
 no shutdown

interface vlan 30
 ip address 192.168.30.1 255.255.255.0
 no shutdown

interface vlan 40
 ip address 192.168.40.1 255.255.255.0
 no shutdown

end
write memory
```

Tại CORE-BR1:

```cisco
ip routing

interface vlan 50
 ip address 192.168.50.1 255.255.255.0
 no shutdown

interface vlan 60
 ip address 192.168.60.1 255.255.255.0
 no shutdown
```

Tại CORE-BR2:

```cisco
ip routing

interface vlan 70
 ip address 192.168.70.1 255.255.255.0
 no shutdown
```

## 7. Verification

Kiểm tra VLAN:

```cisco
show vlan brief
```

Kiểm tra trunk:

```cisco
show interfaces trunk
```

Kiểm tra SVI:

```cisco
show ip interface brief
```

Kiểm tra bảng định tuyến:

```cisco
show ip route
```

Kiểm tra kết nối:

```text
HQ PC VLAN 20 -> 192.168.20.1
BR1 PC VLAN 50 -> 192.168.50.1
BR2 PC VLAN 70 -> 192.168.70.1
```

## 8. Troubleshooting

| Vấn đề | Nguyên nhân có thể | Hướng xử lý |
|---|---|---|
| PC không ping được Gateway | Sai VLAN hoặc SVI down | Kiểm tra VLAN, access port và SVI |
| VLAN không xuất hiện trên switch | Chưa tạo VLAN | Kiểm tra `show vlan brief` |
| Trunk không truyền VLAN | Allowed VLAN chưa đúng | Kiểm tra `show interfaces trunk` |
| SVI down/down | VLAN chưa tồn tại hoặc không có cổng Layer 2 hoạt động trong VLAN | Kiểm tra VLAN và trạng thái các port |
| Các VLAN không liên lạc được | Chưa bật `ip routing` | Bật Layer 3 routing trên Core |

## 9. Kết quả mong đợi

- Các VLAN được tạo đúng theo thiết kế.
- Access port thuộc đúng VLAN.
- Trunk truyền các VLAN cần thiết.
- Core Switch định tuyến được giữa các VLAN.
- Các thiết bị đầu cuối liên lạc được với Default Gateway.
