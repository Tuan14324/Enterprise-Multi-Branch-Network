# CẤU HÌNH ACCESS CONTROL LIST (ACL)

## 1. Mục tiêu

Triển khai ACL trên Core Layer 3 Switch để kiểm soát truy cập giữa các VLAN.

Chính sách áp dụng:

| VLAN nguồn | VLAN đích | Chính sách |
|---|---|---|
| HQ VLAN 20 – ADMIN | VLAN 10 – SERVER | Cho phép |
| HQ VLAN 30 – WIFI | VLAN 10 – SERVER | Chỉ cho phép DNS |
| BR1 VLAN 50 – ADMIN | VLAN 10 – SERVER | Chỉ cho phép DNS trong baseline |
| BR1 VLAN 60 – STAFF | VLAN 10 – SERVER | Chỉ cho phép DNS |
| BR2 VLAN 70 – USERS | VLAN 10 – SERVER | Chỉ cho phép DNS |

DNS Server: `192.168.10.11`

DHCP Server: `192.168.10.10`

## 2. Cấu hình ACL tại CORE-HQ

### 2.1. ACL cho VLAN 30 – WIFI

```cisco
ip access-list extended WIFI-IN
 permit udp any eq bootpc any eq bootps
 permit udp 192.168.30.0 0.0.0.255 host 192.168.10.11 eq 53
 permit tcp 192.168.30.0 0.0.0.255 host 192.168.10.11 eq 53
 deny ip 192.168.30.0 0.0.0.255 192.168.10.0 0.0.0.255
 permit ip any any
!
interface vlan 30
 ip access-group WIFI-IN in
```

## 3. Cấu hình ACL tại CORE-BR1

### 3.1. ACL cho VLAN 60 – STAFF

```cisco
ip access-list extended BR1-STAFF-IN
 permit udp any eq bootpc any eq bootps
 permit udp 192.168.60.0 0.0.0.255 host 192.168.10.11 eq 53
 permit tcp 192.168.60.0 0.0.0.255 host 192.168.10.11 eq 53
 deny ip 192.168.60.0 0.0.0.255 192.168.10.0 0.0.0.255
 permit ip any any
!
interface vlan 60
 ip access-group BR1-STAFF-IN in
```

## 4. Cấu hình ACL tại CORE-BR2

### 4.1. ACL cho VLAN 70 – USERS

```cisco
ip access-list extended BR2-USERS-IN
 permit udp any eq bootpc any eq bootps
 permit udp 192.168.70.0 0.0.0.255 host 192.168.10.11 eq 53
 permit tcp 192.168.70.0 0.0.0.255 host 192.168.10.11 eq 53
 deny ip 192.168.70.0 0.0.0.255 192.168.10.0 0.0.0.255
 permit ip any any
!
interface vlan 70
 ip access-group BR2-USERS-IN in
```

## 5. Kiểm tra ACL

Trên các Core Switch:

```cisco
show access-lists
show ip interface vlan 30
show ip interface vlan 60
show ip interface vlan 70
show running-config
```

Kiểm tra từ máy tính:

```text
ipconfig /all
ping 192.168.10.11
nslookup example.local
ping 192.168.10.10
```

## 6. Kết quả mong đợi

| Kiểm tra | Kết quả |
|---|---|
| Wi-Fi truy cập DNS Server | Thành công |
| Wi-Fi truy cập máy chủ khác trong VLAN 10 | Bị chặn |
| BR1 STAFF truy cập DNS Server | Thành công |
| BR1 STAFF truy cập máy chủ khác trong VLAN 10 | Bị chặn |
| BR2 USERS truy cập DNS Server | Thành công |
| BR2 USERS truy cập máy chủ khác trong VLAN 10 | Bị chặn |
| DHCP cấp phát địa chỉ IP | Thành công |

## 7. Lưu ý triển khai

- Cấu hình ACL trên đúng SVI của VLAN nguồn.
- Kiểm tra DHCP Relay trước khi áp dụng ACL.
- Đảm bảo VPN Selector cho phép lưu lượng DNS giữa các chi nhánh và HQ.
- Kiểm tra chiều đi và chiều về.
- Các quy tắc trên là cấu hình baseline; cần kiểm thử trên thiết bị Packet Tracer trước khi xác nhận hoàn thành.

