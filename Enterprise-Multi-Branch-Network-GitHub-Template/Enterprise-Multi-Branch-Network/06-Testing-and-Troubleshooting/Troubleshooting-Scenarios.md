# Troubleshooting Scenarios – Enterprise Multi-Branch Network

## 1. Quy trình xử lý sự cố

Thực hiện kiểm tra theo thứ tự:

1. Kiểm tra kết nối vật lý và trạng thái Interface.
2. Kiểm tra IP Address, Subnet Mask và Default Gateway.
3. Kiểm tra VLAN và Trunk.
4. Kiểm tra SVI và Inter-VLAN Routing.
5. Kiểm tra OSPF và Routing Table.
6. Kiểm tra kết nối WAN và IPsec VPN.
7. Kiểm tra ACL.
8. Kiểm tra DHCP và DNS.

## 2. Scenario 01 – PC không nhận được IP từ DHCP

**Hiện tượng:**
- PC không nhận được IP.
- PC nhận địa chỉ APIPA hoặc không có Default Gateway.

**Kiểm tra:**

```cisco
show vlan brief
show interfaces trunk
show ip interface brief
show running-config
```

Trên PC:

```text
ipconfig /all
```

**Nguyên nhân có thể:**
- Cổng Access thuộc sai VLAN.
- Trunk chưa cho phép VLAN cần thiết.
- DHCP Scope chưa được tạo.
- DHCP Server chưa hoạt động.
- Thiếu `ip helper-address`.
- Không có đường đi hoặc đường về giữa chi nhánh và DHCP Server.

**Khắc phục:**
- Kiểm tra VLAN và Trunk.
- Kiểm tra DHCP Pool.
- Cấu hình DHCP Relay trên SVI.
- Kiểm tra route và VPN nếu DHCP Server nằm tại HQ.

## 3. Scenario 02 – PC không ping được Default Gateway

**Hiện tượng:**

```text
Request timed out.
```

**Kiểm tra:**

```cisco
show vlan brief
show interfaces trunk
show ip interface brief
show running-config interface vlan 20
```

**Nguyên nhân có thể:**
- Sai VLAN trên Access Port.
- SVI bị shutdown hoặc chưa hoạt động.
- Sai địa chỉ IP hoặc Subnet Mask.
- Đường Trunk không hoạt động.

**Khắc phục:**
- Đưa Access Port vào đúng VLAN.
- Kiểm tra Allowed VLAN trên Trunk.
- Kiểm tra SVI và IP Gateway.
- Đảm bảo VLAN có ít nhất một cổng hoạt động để SVI lên trạng thái up/up.

## 4. Scenario 03 – OSPF không hình thành Neighbor

**Hiện tượng:**
- Không thấy Neighbor trong bảng OSPF.
- Router và Core không học được route qua OSPF.

**Kiểm tra:**

```cisco
show ip ospf neighbor
show ip ospf interface brief
show ip protocols
show ip interface brief
```

**Nguyên nhân có thể:**
- Sai IP hoặc Subnet Mask trên Transit Link.
- Hai thiết bị không cùng Area.
- Interface bị shutdown.
- OSPF không được kích hoạt trên Transit Interface.
- Sai cấu hình Hello/Dead Timer hoặc xác thực OSPF nếu có.

**Khắc phục:**
- Kiểm tra IP hai đầu Transit.
- Kiểm tra OSPF Process và Area.
- Kiểm tra cấu hình Interface.
- Kiểm tra các tham số OSPF giữa hai thiết bị.

## 5. Scenario 04 – Có route nhưng không ping được mạng chi nhánh

**Hiện tượng:**
- Bảng định tuyến có mạng đích.
- Ping đến thiết bị tại chi nhánh thất bại.

**Kiểm tra:**

```cisco
show ip route
show ip interface brief
traceroute <destination-ip>
```

**Nguyên nhân có thể:**
- Thiếu route chiều về.
- Sai Default Gateway trên PC.
- Sai VPN Selector.
- ACL chặn traffic.
- IPsec Tunnel chưa hoạt động.
- Thiết bị đích không phản hồi ICMP.

**Khắc phục:**
- Kiểm tra route tại cả hai đầu.
- Kiểm tra Default Gateway của PC.
- Kiểm tra ACL hai chiều.
- Kiểm tra VPN Selector và IPsec SA.

## 6. Scenario 05 – IPsec VPN không thiết lập

**Hiện tượng:**
- Không có IKE SA.
- VPN không hình thành sau khi phát sinh traffic phù hợp.

**Kiểm tra:**

```cisco
show crypto isakmp sa
show crypto ipsec sa
show crypto map
show access-lists
show ip route
```

**Nguyên nhân có thể:**
- Sai Peer IP.
- Sai Pre-Shared Key.
- Không khớp IKE Policy.
- Không khớp Transform Set.
- Crypto Map chưa gắn vào Outside Interface.
- Không có route đến Peer.
- ACL Crypto không khớp hai đầu.

**Khắc phục:**
- Kiểm tra cấu hình IKE hai đầu.
- Kiểm tra PSK.
- Kiểm tra Transform Set.
- Kiểm tra Crypto Map và Interface.
- Đối chiếu Crypto ACL hai đầu.
- Tạo traffic phù hợp để kích hoạt VPN.

## 7. Scenario 06 – VPN đã thiết lập nhưng không truyền dữ liệu

**Hiện tượng:**
- IKE SA đã được hình thành.
- Ping qua VPN thất bại hoặc không tăng bộ đếm IPsec.

**Kiểm tra:**

```cisco
show crypto isakmp sa
show crypto ipsec sa
show ip route
show access-lists
```

**Nguyên nhân có thể:**
- Traffic không khớp Crypto ACL.
- Thiếu route đến mạng đích.
- Thiếu route chiều về.
- ACL trên Core chặn traffic.
- Sai địa chỉ mạng trong VPN Selector.

**Khắc phục:**
- Đối chiếu Local/Remote Subnet hai đầu.
- Kiểm tra route hai chiều.
- Kiểm tra ACL trên Core.
- Tạo traffic đúng subnet được khai báo trong VPN Selector.
- Theo dõi Encaps và Decaps.

## 8. Scenario 07 – Client không phân giải được DNS

**Hiện tượng:**
- Ping đến IP Server thành công.
- Truy cập bằng tên miền thất bại.

**Kiểm tra:**

```text
ipconfig /all
nslookup example.local
ping 192.168.10.11
```

**Nguyên nhân có thể:**
- Client nhận sai DNS Server.
- DNS Server chưa bật dịch vụ DNS.
- Chưa tạo DNS Record.
- ACL chặn UDP/TCP port 53.
- Không có route đến DNS Server.

**Khắc phục:**
- Kiểm tra DNS trong DHCP Scope.
- Bật dịch vụ DNS trên Server.
- Tạo bản ghi phù hợp.
- Kiểm tra ACL và route.

## 9. Scenario 08 – ACL chặn nhầm lưu lượng

**Hiện tượng:**
- Người dùng không truy cập được dịch vụ cần thiết.
- DHCP hoặc DNS hoạt động không đúng sau khi áp dụng ACL.

**Kiểm tra:**

```cisco
show access-lists
show ip interface vlan 30
show running-config
```

**Nguyên nhân có thể:**
- Thứ tự ACL không đúng.
- Thiếu Permit Rule.
- ACL áp dụng sai Interface hoặc sai chiều.
- Thiếu rule cho DNS hoặc DHCP Relay.

**Khắc phục:**
- Kiểm tra thứ tự các ACE.
- Bổ sung rule cần thiết trước lệnh Deny.
- Kiểm tra ACL được áp dụng đúng SVI.
- Kiểm tra lại DHCP Relay và DNS sau khi chỉnh sửa.

## 10. Scenario 09 – Native VLAN Mismatch

**Hiện tượng:**

```text
%CDP-4-NATIVE_VLAN_MISMATCH
```

**Kiểm tra:**

```cisco
show interfaces trunk
show running-config interface gigabitEthernet 0/1
```

**Nguyên nhân:**
- Native VLAN trên hai đầu Trunk không giống nhau.

**Khắc phục:**

Cấu hình thống nhất Native VLAN trên cả hai đầu:

```cisco
interface gigabitEthernet 0/1
 switchport mode trunk
 switchport trunk native vlan 99
```

Chỉ sử dụng VLAN 99 nếu VLAN này đã được tạo và được quy hoạch làm Native VLAN trong mô hình.

## 11. Scenario 10 – Interface bị Down

**Hiện tượng:**
- Interface ở trạng thái `administratively down` hoặc `down/down`.

**Kiểm tra:**

```cisco
show ip interface brief
show interfaces
```

**Nguyên nhân có thể:**
- Interface bị shutdown.
- Cáp kết nối sai hoặc bị lỗi.
- Thiết bị đầu kia chưa bật.
- Kết nối vật lý chưa đúng.

**Khắc phục:**

```cisco
interface gigabitEthernet 0/1
 no shutdown
```

Kiểm tra lại cáp và trạng thái Interface ở cả hai đầu.

## 12. Troubleshooting Checklist

| STT | Sự cố | Đã xử lý |
|---|---|---|
| 1 | DHCP không cấp phát IP | Pending |
| 2 | Không ping được Gateway | Pending |
| 3 | OSPF không hình thành Neighbor | Pending |
| 4 | Không truy cập được mạng chi nhánh | Pending |
| 5 | IPsec VPN không thiết lập | Pending |
| 6 | VPN không truyền dữ liệu | Pending |
| 7 | DNS không phân giải | Pending |
| 8 | ACL chặn nhầm traffic | Pending |
| 9 | Native VLAN Mismatch | Pending |
| 10 | Interface Down | Pending |

Chỉ đánh dấu `Completed` sau khi tái hiện được lỗi, xác định nguyên nhân, khắc phục và kiểm tra lại thành công.

