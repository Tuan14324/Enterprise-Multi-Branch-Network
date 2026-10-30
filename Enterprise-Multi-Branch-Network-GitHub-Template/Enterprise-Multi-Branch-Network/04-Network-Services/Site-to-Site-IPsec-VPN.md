
# 06. Site-to-Site IPsec VPN

## 1. Mục tiêu

Triển khai Site-to-Site IPsec VPN để kết nối an toàn giữa HQ và các chi nhánh BR1, BR2 thông qua mạng ISP.

VPN giúp:
- Mã hóa dữ liệu truyền qua mạng WAN.
- Bảo vệ dữ liệu trao đổi giữa các địa điểm.
- Cho phép người dùng chi nhánh truy cập các tài nguyên được phép tại HQ.
- Kết nối các mạng LAN riêng thông qua Internet/WAN.

## 2. VPN Architecture

Mô hình triển khai: Hub-and-Spoke.

- HQ là Hub.
- BR1 là Spoke 1.
- BR2 là Spoke 2.
- HQ thiết lập một tunnel với BR1 và một tunnel với BR2.
- Không triển khai tunnel trực tiếp giữa BR1 và BR2 trong thiết kế hiện tại.

### Public WAN IP

| Device | WAN IP | ISP Gateway |
|---|---|---|
| HQ Router | 200.1.0.2/30 | 200.1.0.1 |
| BR1 Router | 200.1.1.2/30 | 200.1.1.1 |
| BR2 Router | 200.1.2.2/30 | 200.1.2.1 |

### Private LAN Networks

| Site | Protected Networks |
|---|---|
| HQ | 192.168.10.0/24 |
| HQ | 192.168.20.0/24 |
| HQ | 192.168.30.0/24 |
| HQ | 192.168.40.0/24 |
| BR1 | 192.168.50.0/24 |
| BR1 | 192.168.60.0/24 |
| BR2 | 192.168.70.0/24 |

## 3. VPN Traffic Flow

### HQ to BR1

```text
HQ LAN
  |
CORE-HQ
  |
HQ Router
  |
IPsec Encryption
  |
ISP Network
  |
IPsec Decryption
  |
BR1 Router
  |
CORE-BR1
  |
BR1 LAN
```

### HQ to BR2

```text
HQ LAN
  |
CORE-HQ
  |
HQ Router
  |
IPsec Encryption
  |
ISP Network
  |
IPsec Decryption
  |
BR2 Router
  |
CORE-BR2
  |
BR2 LAN
```

## 4. IPsec Parameters

| Parameter | Value |
|---|---|
| VPN Type | Site-to-Site IPsec |
| Architecture | Hub-and-Spoke |
| IKE Version | IKEv1 |
| Encryption | AES-256 |
| Hash | SHA |
| Authentication | Pre-Shared Key |
| Diffie-Hellman Group | 5 |
| Lifetime | 86400 seconds |
| IPsec Transform Set | TS-VPN |
| Encapsulation | ESP |
| ESP Encryption | AES-256 |
| ESP Authentication | SHA-HMAC |

Pre-Shared Key dùng trong lab:

```text
VPN@12345
```

Đây chỉ là mật khẩu minh họa cho môi trường lab. Không sử dụng mật khẩu này trong hệ thống thực tế.

## 5. Interesting Traffic – Crypto ACL

Crypto ACL xác định traffic nào cần được mã hóa khi đi qua IPsec VPN.

### HQ – HQ to BR1

```cisco
access-list 110 permit ip 192.168.10.0 0.0.0.255 192.168.50.0 0.0.0.255
access-list 110 permit ip 192.168.10.0 0.0.0.255 192.168.60.0 0.0.0.255

access-list 110 permit ip 192.168.20.0 0.0.0.255 192.168.50.0 0.0.0.255
access-list 110 permit ip 192.168.20.0 0.0.0.255 192.168.60.0 0.0.0.255

access-list 110 permit ip 192.168.30.0 0.0.0.255 192.168.50.0 0.0.0.255
access-list 110 permit ip 192.168.30.0 0.0.0.255 192.168.60.0 0.0.0.255

access-list 110 permit ip 192.168.40.0 0.0.0.255 192.168.50.0 0.0.0.255
access-list 110 permit ip 192.168.40.0 0.0.0.255 192.168.60.0 0.0.0.255
```

### HQ – HQ to BR2

```cisco
access-list 120 permit ip 192.168.10.0 0.0.0.255 192.168.70.0 0.0.0.255
access-list 120 permit ip 192.168.20.0 0.0.0.255 192.168.70.0 0.0.0.255
access-list 120 permit ip 192.168.30.0 0.0.0.255 192.168.70.0 0.0.0.255
access-list 120 permit ip 192.168.40.0 0.0.0.255 192.168.70.0 0.0.0.255
```

### BR1 – HQ to BR1

```cisco
access-list 110 permit ip 192.168.50.0 0.0.0.255 192.168.10.0 0.0.0.255
access-list 110 permit ip 192.168.50.0 0.0.0.255 192.168.20.0 0.0.0.255
access-list 110 permit ip 192.168.50.0 0.0.0.255 192.168.30.0 0.0.0.255
access-list 110 permit ip 192.168.50.0 0.0.0.255 192.168.40.0 0.0.0.255

access-list 110 permit ip 192.168.60.0 0.0.0.255 192.168.10.0 0.0.0.255
access-list 110 permit ip 192.168.60.0 0.0.0.255 192.168.20.0 0.0.0.255
access-list 110 permit ip 192.168.60.0 0.0.0.255 192.168.30.0 0.0.0.255
access-list 110 permit ip 192.168.60.0 0.0.0.255 192.168.40.0 0.0.0.255
```

### BR2 – HQ to BR2

```cisco
access-list 120 permit ip 192.168.70.0 0.0.0.255 192.168.10.0 0.0.0.255
access-list 120 permit ip 192.168.70.0 0.0.0.255 192.168.20.0 0.0.0.255
access-list 120 permit ip 192.168.70.0 0.0.0.255 192.168.30.0 0.0.0.255
access-list 120 permit ip 192.168.70.0 0.0.0.255 192.168.40.0 0.0.0.255
```

Lưu ý:
- ACL tại hai đầu phải mô tả cùng một luồng traffic theo hai chiều ngược nhau.
- Không thêm `deny ip any any` vào cuối crypto ACL.
- Các subnet trong ACL phải khớp với mạng thực tế.
- Nếu cần cho phép DHCP Relay hoặc dịch vụ khác qua VPN, phải kiểm tra và bổ sung selector phù hợp.

## 6. IKE Phase 1 Configuration

### HQ Router

```cisco
crypto isakmp policy 10
 encryption aes 256
 hash sha
 authentication pre-share
 group 5
 lifetime 86400

crypto isakmp key VPN@12345 address 200.1.1.2
crypto isakmp key VPN@12345 address 200.1.2.2
```

### BR1 Router

```cisco
crypto isakmp policy 10
 encryption aes 256
 hash sha
 authentication pre-share
 group 5
 lifetime 86400

crypto isakmp key VPN@12345 address 200.1.0.2
```

### BR2 Router

```cisco
crypto isakmp policy 10
 encryption aes 256
 hash sha
 authentication pre-share
 group 5
 lifetime 86400

crypto isakmp key VPN@12345 address 200.1.0.2
```

## 7. IPsec Phase 2 Configuration

### Transform Set

Cấu hình trên cả HQ, BR1 và BR2:

```cisco
crypto ipsec transform-set TS-VPN esp-aes 256 esp-sha-hmac
```

### HQ Crypto Map

```cisco
crypto map VPN-MAP 10 ipsec-isakmp
 set peer 200.1.1.2
 set transform-set TS-VPN
 match address 110

crypto map VPN-MAP 20 ipsec-isakmp
 set peer 200.1.2.2
 set transform-set TS-VPN
 match address 120
```

Áp dụng trên interface WAN của HQ Router:

```cisco
interface gigabitEthernet 0/0
 crypto map VPN-MAP
```

### BR1 Crypto Map

```cisco
crypto map VPN-MAP 10 ipsec-isakmp
 set peer 200.1.0.2
 set transform-set TS-VPN
 match address 110
```

Áp dụng trên interface WAN của BR1 Router:

```cisco
interface gigabitEthernet 0/0
 crypto map VPN-MAP
```

### BR2 Crypto Map

```cisco
crypto map VPN-MAP 10 ipsec-isakmp
 set peer 200.1.0.2
 set transform-set TS-VPN
 match address 120
```

Áp dụng trên interface WAN của BR2 Router:

```cisco
interface gigabitEthernet 0/0
 crypto map VPN-MAP
```

Thay `gigabitEthernet 0/0` bằng interface WAN thực tế của từng Router.

## 8. Routing Requirements

Để traffic đi qua VPN:
- Các Router phải có đường đi đến các mạng LAN cần liên lạc.
- Default route của Router phải hướng về ISP.
- Traffic thuộc Crypto ACL phải được chuyển ra interface WAN có crypto map.
- Thiết bị đích phải có đường trả về nguồn.
- ISP phải cho phép các địa chỉ WAN public liên lạc với nhau.

Trong mô hình này, OSPF không chạy qua ISP hoặc VPN.

## 9. NAT Considerations

Baseline VPN không sử dụng NAT cho traffic giữa các mạng LAN được bảo vệ.

Nếu bổ sung NAT/PAT để truy cập Internet, cần cấu hình NAT exemption hoặc NAT ACL phù hợp để traffic VPN giữ nguyên địa chỉ IP nguồn và đích.

Không NAT traffic giữa HQ và các mạng LAN chi nhánh trước khi mã hóa IPsec.

## 10. Verification

Trên Router:

```cisco
show crypto isakmp sa
show crypto ipsec sa
show crypto map
show access-lists
show ip route
```

Kết quả mong đợi:

- IKE Security Association được hình thành.
- IPsec Security Association được tạo khi có traffic phù hợp.
- Encaps tăng khi gửi traffic được mã hóa.
- Decaps tăng khi nhận traffic giải mã.

Lưu ý: Tunnel có thể chưa xuất hiện trạng thái hoạt động cho đến khi có traffic khớp Crypto ACL.

## 11. VPN Connectivity Testing

### HQ to BR1

Từ một thiết bị thuộc mạng HQ, kiểm tra kết nối đến thiết bị tại:

```text
192.168.50.0/24
192.168.60.0/24
```

### HQ to BR2

Từ một thiết bị thuộc mạng HQ, kiểm tra kết nối đến:

```text
192.168.70.0/24
```

Kiểm tra lại trạng thái IPsec SA trên hai đầu tunnel.

## 12. Troubleshooting

| Vấn đề | Nguyên nhân có thể | Hướng xử lý |
|---|---|---|
| IKE SA không hình thành | Sai PSK, peer IP hoặc IKE policy | Kiểm tra Phase 1 |
| IPsec SA không hình thành | Transform Set hoặc Crypto ACL không khớp | Kiểm tra Phase 2 |
| VPN có SA nhưng không ping được | Routing, ACL hoặc return path lỗi | Kiểm tra đường đi hai chiều |
| Encaps tăng nhưng Decaps không tăng | Peer không nhận hoặc không trả traffic | Kiểm tra WAN, peer và cấu hình đầu xa |
| Không có traffic được mã hóa | Traffic không khớp Crypto ACL | Kiểm tra source/destination subnet |
| VPN hoạt động nhưng DHCP không cấp IP | Thiếu DHCP Relay, selector hoặc đường trả về | Kiểm tra DHCP và VPN traffic |

## 13. Design Limitations

- HQ là Hub, BR1 và BR2 là Spoke.
- BR1 và BR2 không có tunnel trực tiếp với nhau.
- Kết nối BR1 ↔ BR2 không được đảm bảo trong baseline.
- Muốn BR1 truy cập trực tiếp BR2 cần bổ sung thiết kế định tuyến và VPN phù hợp, ví dụ tunnel trực tiếp hoặc cơ chế VPN hairpin tại HQ.
- Khả năng hỗ trợ thuật toán và lệnh IPsec phụ thuộc model Router và phiên bản IOS trong Packet Tracer.

## 14. Security Notes

Các thuật toán và tham số trong lab được lựa chọn để phù hợp với khả năng mô phỏng của thiết bị.

Đối với triển khai thực tế, cần đánh giá lại thuật toán mã hóa, xác thực, Diffie-Hellman Group và chính sách quản lý khóa theo tiêu chuẩn bảo mật hiện hành.
