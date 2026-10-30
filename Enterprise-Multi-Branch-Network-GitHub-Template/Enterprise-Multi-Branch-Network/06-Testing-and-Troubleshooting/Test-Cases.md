# Test Cases – Enterprise Multi-Branch Network

## 1. Mục tiêu

Kiểm thử kết nối LAN, VLAN, định tuyến, DHCP, DNS, IPsec VPN và chính sách ACL trên mô hình HQ – BR1 – BR2.

## 2. Test Environment

| Thành phần | Thông tin |
|---|---|
| Simulation Tool | Cisco Packet Tracer |
| HQ | 192.168.10.0/24 – 192.168.40.0/24 |
| BR1 | 192.168.50.0/24 – 192.168.60.0/24 |
| BR2 | 192.168.70.0/24 |
| DHCP Server | 192.168.10.10 |
| DNS Server | 192.168.10.11 |
| VPN | HQ–BR1, HQ–BR2 |

## 3. Test Cases

### TC01 – VLAN Configuration

| Nội dung | Giá trị |
|---|---|
| Mục tiêu | Kiểm tra VLAN trên các Switch |
| Thực hiện | `show vlan brief` |
| Kết quả mong đợi | Các VLAN được tạo đúng theo VLAN Plan |
| Trạng thái | Pending |

### TC02 – Trunk Configuration

| Nội dung | Giá trị |
|---|---|
| Mục tiêu | Kiểm tra kết nối Trunk giữa Core và Access |
| Thực hiện | `show interfaces trunk` |
| Kết quả mong đợi | Trunk hoạt động, Allowed VLAN chính xác |
| Trạng thái | Pending |

### TC03 – Inter-VLAN Routing

| Nội dung | Giá trị |
|---|---|
| Mục tiêu | Kiểm tra định tuyến giữa các VLAN |
| Thực hiện | Ping Default Gateway và thiết bị ở VLAN khác |
| Kết quả mong đợi | Kết nối thành công nếu không bị ACL chặn |
| Trạng thái | Pending |

### TC04 – DHCP Assignment

| Nội dung | Giá trị |
|---|---|
| Mục tiêu | Kiểm tra cấp phát IP tự động |
| Thực hiện | Chuyển PC sang DHCP, chạy `ipconfig /all` |
| Kết quả mong đợi | Nhận đúng IP, Subnet Mask, Gateway và DNS |
| Trạng thái | Pending |

Kiểm tra tại các VLAN có DHCP Scope: 20, 30, 50, 60 và 70.

### TC05 – DNS Resolution

| Nội dung | Giá trị |
|---|---|
| Mục tiêu | Kiểm tra phân giải DNS |
| Thực hiện | `nslookup example.local` |
| Kết quả mong đợi | DNS Server 192.168.10.11 trả về IP tương ứng |
| Trạng thái | Pending |

Cần tạo bản ghi DNS tương ứng trên DNS Server trước khi kiểm thử.

### TC06 – OSPF Neighbor

| Nội dung | Giá trị |
|---|---|
| Mục tiêu | Kiểm tra OSPF giữa Router và Core tại từng Site |
| Thực hiện | `show ip ospf neighbor` |
| Kết quả mong đợi | Router và Core hình thành quan hệ OSPF |
| Trạng thái | Pending |

OSPF chỉ chạy trên kết nối Router–Core tại từng địa điểm.

### TC07 – Routing Table

| Nội dung | Giá trị |
|---|---|
| Mục tiêu | Kiểm tra bảng định tuyến |
| Thực hiện | `show ip route` |
| Kết quả mong đợi | Có các mạng LAN nội bộ, mạng WAN và route cần thiết đến mạng từ xa |
| Trạng thái | Pending |

### TC08 – HQ to BR1 Connectivity

| Nội dung | Giá trị |
|---|---|
| Mục tiêu | Kiểm tra kết nối HQ và BR1 |
| Thực hiện | Ping từ PC HQ đến PC BR1 bằng IP tĩnh |
| Kết quả mong đợi | Ping thành công nếu VPN, route và ACL cho phép |
| Trạng thái | Pending |

### TC09 – HQ to BR2 Connectivity

| Nội dung | Giá trị |
|---|---|
| Mục tiêu | Kiểm tra kết nối HQ và BR2 |
| Thực hiện | Ping từ PC HQ đến PC BR2 bằng IP tĩnh |
| Kết quả mong đợi | Ping thành công nếu VPN, route và ACL cho phép |
| Trạng thái | Pending |

### TC10 – IPsec VPN Status

| Nội dung | Giá trị |
|---|---|
| Mục tiêu | Kiểm tra trạng thái VPN |
| Thực hiện | `show crypto isakmp sa` và `show crypto ipsec sa` |
| Kết quả mong đợi | IKE SA được thiết lập và IPsec encaps/decaps tăng khi có traffic phù hợp |
| Trạng thái | Pending |

### TC11 – DHCP Across VPN

| Nội dung | Giá trị |
|---|---|
| Mục tiêu | Kiểm tra DHCP Relay từ chi nhánh về HQ |
| Thực hiện | Chuyển PC tại BR1/BR2 sang DHCP |
| Kết quả mong đợi | Nhận IP đúng Scope tại HQ |
| Trạng thái | Pending |

Thực hiện sau khi đã xác nhận VPN và kết nối bằng IP tĩnh hoạt động.

### TC12 – ACL Verification

| Nội dung | Giá trị |
|---|---|
| Mục tiêu | Kiểm tra chính sách truy cập |
| Thực hiện | Ping và truy cập DNS/Server từ các VLAN |
| Kết quả mong đợi | Đúng chính sách ACL đã cấu hình |
| Trạng thái | Pending |

### TC13 – Branch-to-Branch Connectivity

| Nội dung | Giá trị |
|---|---|
| Mục tiêu | Kiểm tra phạm vi kết nối giữa BR1 và BR2 |
| Thực hiện | Ping từ PC BR1 đến PC BR2 |
| Kết quả mong đợi | Không kết nối trong mô hình baseline nếu chưa triển khai định tuyến và VPN cho phép lưu lượng này |
| Trạng thái | Pending |

## 4. Tổng hợp kết quả

| Test ID | Hạng mục | Status | Evidence |
|---|---|---|---|
| TC01 | VLAN | Pending | Screenshot |
| TC02 | Trunk | Pending | Screenshot |
| TC03 | Inter-VLAN | Pending | Screenshot |
| TC04 | DHCP | Pending | Screenshot |
| TC05 | DNS | Pending | Screenshot |
| TC06 | OSPF | Pending | Screenshot |
| TC07 | Routing | Pending | Screenshot |
| TC08 | HQ–BR1 | Pending | Screenshot |
| TC09 | HQ–BR2 | Pending | Screenshot |
| TC10 | IPsec VPN | Pending | Screenshot |
| TC11 | DHCP over VPN | Pending | Screenshot |
| TC12 | ACL | Pending | Screenshot |
| TC13 | BR1–BR2 | Pending | Screenshot |

## 5. Evidence cần chụp

- `show vlan brief`
- `show interfaces trunk`
- `show ip interface brief`
- `show ip route`
- `show ip ospf neighbor`
- `show crypto isakmp sa`
- `show crypto ipsec sa`
- `show access-lists`
- `ipconfig /all`
- Ping giữa HQ và các chi nhánh.
- Kết quả `nslookup`.

