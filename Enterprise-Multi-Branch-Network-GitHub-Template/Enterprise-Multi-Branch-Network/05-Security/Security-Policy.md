# CHÍNH SÁCH BẢO MẬT MẠNG (SECURITY POLICY)

## 1. Phạm vi áp dụng

Áp dụng cho toàn bộ hệ thống mạng:
- HQ.
- BR1.
- BR2.
- Mạng WAN và VPN.
- Máy chủ DHCP, DNS.
- Các thiết bị Router, Core Switch, Access Switch và WLC.

## 2. Chính sách phân quyền truy cập

| Nguồn | Đích | Quyền truy cập |
|---|---|---|
| HQ ADMIN – VLAN 20 | HQ SERVER – VLAN 10 | Cho phép theo dịch vụ |
| HQ WIFI – VLAN 30 | DNS Server | Cho phép |
| HQ WIFI – VLAN 30 | SERVER còn lại | Từ chối |
| BR1 ADMIN – VLAN 50 | HQ SERVER | Theo dịch vụ được cấp |
| BR1 STAFF – VLAN 60 | DNS Server | Cho phép |
| BR1 STAFF – VLAN 60 | SERVER còn lại | Từ chối |
| BR2 USERS – VLAN 70 | DNS Server | Cho phép |
| BR2 USERS – VLAN 70 | SERVER còn lại | Từ chối |
| Người dùng thông thường | WLC Management | Không cho phép quản trị |
| Quản trị viên | Thiết bị mạng | Cho phép theo quyền |

Thực thi chính sách bằng ACL trên Core Switch.

## 3. Chính sách bảo mật thiết bị mạng

- Sử dụng SSH để quản trị thiết bị.
- Không sử dụng Telnet.
- Đặt mật khẩu quản trị đủ mạnh.
- Không chia sẻ tài khoản quản trị.
- Chỉ cho phép máy quản trị truy cập thiết bị mạng.
- Sao lưu cấu hình trước khi thay đổi.
- Tắt các cổng mạng không sử dụng.

Ví dụ cấu hình SSH trên thiết bị Cisco hỗ trợ:

```cisco
hostname CORE-HQ
ip domain-name enterprise.local
username admin privilege 15 secret StrongPassword123

crypto key generate rsa
ip ssh version 2

line vty 0 4
 login local
 transport input ssh
```

Mật khẩu trên chỉ là giá trị minh họa, cần thay bằng mật khẩu riêng khi triển khai.

## 4. Chính sách bảo mật Layer 2

### Port Security

```cisco
interface fa0/1
 switchport mode access
 switchport port-security
 switchport port-security maximum 2
 switchport port-security violation restrict
```

### PortFast và BPDU Guard

Chỉ áp dụng trên các cổng kết nối thiết bị đầu cuối:

```cisco
interface range fa0/1-20
 spanning-tree portfast
 spanning-tree bpduguard enable
```

### Tắt cổng không sử dụng

```cisco
interface range fa0/22-24
 shutdown
```

Điều chỉnh interface theo sơ đồ thực tế.

DHCP Snooping và Dynamic ARP Inspection có thể bổ sung nếu thiết bị Packet Tracer hỗ trợ và đã xác định đúng các cổng Trusted.

## 5. Chính sách bảo mật VPN

Các kết nối VPN:

| VPN Tunnel | Peer HQ | Peer chi nhánh |
|---|---|---|
| HQ – BR1 | 200.1.0.2 | 200.1.1.2 |
| HQ – BR2 | 200.1.0.2 | 200.1.2.2 |

Yêu cầu:
- Xác thực VPN Peer bằng Pre-Shared Key.
- Sử dụng thuật toán mã hóa và kiểm tra toàn vẹn phù hợp.
- Chỉ cho phép các mạng được khai báo trong VPN Selector.
- Không sử dụng chung khóa VPN giữa các cặp Peer trong môi trường production.
- Kiểm tra trạng thái IKE và IPsec sau khi triển khai.

## 6. Chính sách DHCP và DNS

| Dịch vụ | Địa chỉ | Chính sách |
|---|---|---|
| DHCP Server | 192.168.10.10 | Chỉ cấp phát các Scope đã quy hoạch |
| DNS Server | 192.168.10.11 | Là DNS Server duy nhất trong mô hình |

Yêu cầu:
- Máy chủ sử dụng IP tĩnh.
- Không để DHCP Scope bị trùng lặp.
- Loại trừ IP tĩnh khỏi dải cấp phát DHCP.
- Các VLAN người dùng sử dụng DNS được chỉ định.
- Chỉ cho phép truy vấn DNS đến máy chủ được phê duyệt.

## 7. Chính sách bảo mật Wi-Fi

- Sử dụng WPA2 hoặc WPA3 tùy khả năng thiết bị.
- Sử dụng mật khẩu đủ mạnh.
- Hạn chế truy cập từ Wi-Fi vào VLAN SERVER.
- Chỉ quản trị viên được phép quản lý WLC.
- Tách Guest Wi-Fi sang VLAN riêng nếu triển khai mạng khách.

## 8. Chính sách sao lưu cấu hình

Các thiết bị cần sao lưu:
- HQ Router.
- BR1 Router.
- BR2 Router.
- CORE-HQ.
- CORE-BR1.
- CORE-BR2.
- Các Access Switch.

Yêu cầu:
- Sao lưu sau khi hoàn thành cấu hình.
- Sao lưu trước khi thay đổi lớn.
- Lưu phiên bản cấu hình theo ngày.
- Không đưa mật khẩu hoặc Pre-Shared Key thật lên GitHub.

## 9. Kiểm tra bảo mật

| STT | Nội dung | Kết quả mong đợi |
|---|---|---|
| 1 | Kiểm tra truy cập SSH | Thành công |
| 2 | Kiểm tra Telnet | Không được phép |
| 3 | Kiểm tra ACL | Đúng chính sách |
| 4 | Kiểm tra VPN | Tunnel hoạt động |
| 5 | Kiểm tra truy cập máy chủ | Đúng quyền |
| 6 | Kiểm tra Port Security | Phát hiện vi phạm |
| 7 | Kiểm tra BPDU Guard | Bảo vệ cổng Edge |
| 8 | Kiểm tra DHCP | Cấp phát đúng Scope |
| 9 | Kiểm tra DNS | Phân giải thành công |
| 10 | Kiểm tra bản sao lưu | Có thể khôi phục cấu hình |

## 10. Trạng thái triển khai

Các chức năng chỉ được đánh dấu Completed sau khi đã cấu hình và kiểm thử thành công.

| Hạng mục | Trạng thái |
|---|---|
| VLAN Segmentation | Pending |
| ACL | Pending |
| SSH Management | Pending |
| Port Security | Pending |
| BPDU Guard | Pending |
| VPN Security | Pending |
| DHCP/DNS Security | Pending |
| Configuration Backup | Pending |

