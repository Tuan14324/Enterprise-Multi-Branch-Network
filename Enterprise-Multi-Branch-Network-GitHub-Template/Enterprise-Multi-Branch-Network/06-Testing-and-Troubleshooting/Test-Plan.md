# TEST PLAN – ENTERPRISE MULTI-BRANCH NETWORK

## Tổng quan

- Mô hình: HQ, BR1, BR2, ISP (Cisco Packet Tracer).
- Tổng số test trong phạm vi: **17**.
- Trạng thái trong báo cáo mẫu: **17 PASS / 0 FAIL**.
- Số bài đã có bằng chứng xác minh: **0**.
- Ngoài phạm vi: liên lạc trực tiếp BR1–BR2; truy cập Internet thực / NAT.

## Danh sách kiểm thử

| ID | Hạng mục | Thiết bị | Lệnh / thao tác | Tiêu chí đạt | Status |
|---|---|---|---|---|---|
| T01 | Trạng thái WAN Serial | ISP, HQ, BR1, BR2 | `show ip interface brief` | Các cổng WAN Serial ở trạng thái up/up | PASS |
| T02 | Kết nối WAN đến ISP | HQ, BR1, BR2 | `ping 200.1.0.1 / 200.1.1.1 / 200.1.2.1` | Nhận phản hồi ICMP từ từng ISP peer | PASS |
| T03 | VLAN theo thiết kế | CORE/ACCESS HQ, BR1, BR2 | `show vlan brief` | HQ: 10,20,30,40; BR1: 50,60; BR2: 70 | PASS |
| T04 | Trunk và VLAN allowed | CORE/ACCESS HQ, BR1, BR2 | `show interfaces trunk` | Trunk forwarding các VLAN theo bản thiết kế | PASS |
| T05 | Gateway SVI / Inter-VLAN | CORE-HQ, CORE-BR1, CORE-BR2 | `show ip interface brief; ping <IP đích cùng site>` | SVI hoạt động, liên lạc giữa các VLAN được phép | PASS |
| T06 | OSPF Neighbor FULL | WAN Router và Core từng site | `show ip ospf neighbor` | Router–Core cùng site hình thành FULL adjacency | PASS |
| T07 | Định tuyến OSPF | WAN Router và Core từng site | `show ip route ospf` | Các prefix nội bộ học được đúng như thiết kế | PASS |
| T08 | DHCP HQ VLAN20 | PC0 hoặc PC1 tại HQ | `ipconfig /all` | Nhận 192.168.20.100+; gateway 192.168.20.1 | PASS |
| T09 | DHCP BR1 VLAN60 qua VPN | PC tại BR1 VLAN60 | `ipconfig /all` | Nhận 192.168.60.100+; gateway 192.168.60.1 | PASS |
| T10 | DHCP BR2 VLAN70 qua VPN | PC tại BR2 VLAN70 | `ipconfig /all` | Nhận 192.168.70.100+; gateway 192.168.70.1 | PASS |
| T11 | DNS nội bộ | Client tại HQ | `nslookup intranet.lab.test` | Record trả về 192.168.10.10 nếu được cấu hình | PASS |
| T12 | Wi-Fi VLAN30 | Laptop / Smartphone HQ | `Kết nối SSID; ipconfig; ping 192.168.30.1` | Đăng nhập Wi-Fi, nhận IP 192.168.30.0/24 và ping gateway | PASS |
| T13 | VPN HQ–BR1 | HQ Router, BR1 Router | `show crypto isakmp sa; show crypto ipsec sa` | IKE SA hợp lệ, số encaps/decaps tăng sau khi truyền dữ liệu | PASS |
| T14 | VPN HQ–BR2 | HQ Router, BR2 Router | `show crypto isakmp sa; show crypto ipsec sa` | IKE SA hợp lệ, số encaps/decaps tăng sau khi truyền dữ liệu | PASS |
| T15 | BR1 truy cập dịch vụ HQ | PC BR1 | `ping 192.168.10.10; ping 192.168.10.11` | Hai máy chủ HQ phản hồi qua VPN | PASS |
| T16 | BR2 truy cập DNS HQ | PC BR2 | `ping 192.168.10.11` | DNS HQ phản hồi qua VPN | PASS |
| T17 | BR2 truy cập máy in | PC BR2 | `ping 192.168.70.10` | Printer phản hồi ICMP trong VLAN70 | PASS |




