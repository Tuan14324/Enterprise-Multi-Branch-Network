
# 07. Internet Connectivity

## 1. Mục tiêu

Triển khai kết nối WAN giữa HQ, BR1, BR2 và ISP.

Các mục tiêu:
- Kết nối Router tại từng site đến ISP.
- Cấu hình Default Route.
- Đảm bảo các WAN IP có thể liên lạc với nhau.
- Cung cấp đường truyền cho traffic Internet và IPsec VPN.

## 2. WAN Addressing Plan

| Link | Device | Interface IP |
|---|---|---|
| ISP - HQ | ISP | 200.1.0.1/30 |
| ISP - HQ | HQ Router | 200.1.0.2/30 |
| ISP - BR1 | ISP | 200.1.1.1/30 |
| ISP - BR1 | BR1 Router | 200.1.1.2/30 |
| ISP - BR2 | ISP | 200.1.2.1/30 |
| ISP - BR2 | BR2 Router | 200.1.2.2/30 |

## 3. WAN Interface Configuration

### HQ Router

```cisco
interface gigabitEthernet 0/0
 description WAN_TO_ISP
 ip address 200.1.0.2 255.255.255.252
 no shutdown
```

### BR1 Router

```cisco
interface gigabitEthernet 0/0
 description WAN_TO_ISP
 ip address 200.1.1.2 255.255.255.252
 no shutdown
```

### BR2 Router

```cisco
interface gigabitEthernet 0/0
 description WAN_TO_ISP
 ip address 200.1.2.2 255.255.255.252
 no shutdown
```

Thay interface theo đúng sơ đồ kết nối thực tế.

## 4. Default Route Configuration

### HQ Router

```cisco
ip route 0.0.0.0 0.0.0.0 200.1.0.1
```

### BR1 Router

```cisco
ip route 0.0.0.0 0.0.0.0 200.1.1.1
```

### BR2 Router

```cisco
ip route 0.0.0.0 0.0.0.0 200.1.2.1
```

Default Route chuyển traffic không có route cụ thể đến ISP.

Trong thiết kế VPN, traffic đến các mạng LAN được bảo vệ cần được chuyển ra interface có Crypto Map để IPsec xử lý.

## 5. ISP Configuration

ISP cần:
- Cấu hình địa chỉ IP trên các interface kết nối với Router.
- Đảm bảo các interface WAN hoạt động.
- Cho phép các địa chỉ WAN public của HQ, BR1 và BR2 liên lạc với nhau.
- Có đường định tuyến phù hợp đến các mạng WAN public.

ISP không cần định tuyến các mạng LAN private được bảo vệ bên trong IPsec VPN.

## 6. Verification

Kiểm tra interface:

```cisco
show ip interface brief
```

Kiểm tra route:

```cisco
show ip route
```

Kiểm tra kết nối đến ISP Gateway:

```cisco
ping 200.1.0.1
ping 200.1.1.1
ping 200.1.2.1
```

Kiểm tra WAN reachability giữa các Router:

```cisco
ping 200.1.1.2
ping 200.1.2.2
```

Chỉ kiểm tra các địa chỉ có đường đi phù hợp và được ISP cho phép.

## 7. Troubleshooting

| Vấn đề | Nguyên nhân có thể | Hướng xử lý |
|---|---|---|
| WAN interface down | Sai cáp hoặc interface shutdown | Kiểm tra kết nối và `no shutdown` |
| Không ping được ISP Gateway | Sai IP hoặc subnet | Kiểm tra IP Addressing |
| Không có default route | Chưa cấu hình static default route | Kiểm tra `show ip route` |
| Không liên lạc được WAN Peer | ISP thiếu route hoặc ACL chặn | Kiểm tra ISP routing và WAN connectivity |
| VPN không hoạt động | WAN peer không reachable | Kiểm tra WAN trước khi kiểm tra IPsec |

## 8. Design Notes

- ISP đóng vai trò mạng trung gian kết nối các site.
- Các mạng LAN private được bảo vệ bằng IPsec VPN.
- Không quảng bá OSPF qua ISP.
- ISP không cần biết các subnet LAN private nằm sau VPN Router.
- Nếu bổ sung truy cập Internet thực tế cho client, cần triển khai NAT/PAT và chính sách bảo mật phù hợp. Đây là phần mở rộng, không mặc định được coi là đã triển khai trong baseline.
