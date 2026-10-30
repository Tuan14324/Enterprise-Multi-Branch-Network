
# 04. DNS Configuration

## 1. Mục tiêu

Triển khai DNS Server tại HQ để phân giải tên miền thành địa chỉ IP.

DNS (Domain Name System) giúp người dùng truy cập dịch vụ bằng tên thay vì phải nhớ địa chỉ IP.

Ví dụ:

```text
server.company.local -> 192.168.10.10
```

## 2. DNS Server Design

| Parameter | Value |
|---|---|
| DNS Server | 192.168.10.11 |
| Subnet Mask | 255.255.255.0 |
| Default Gateway | 192.168.10.1 |
| VLAN | VLAN 10 - SERVER |

DNS Server là máy chủ DNS duy nhất được thể hiện trong topology.

## 3. DNS Server Configuration

Trong Cisco Packet Tracer:

1. Chọn DNS Server.
2. Vào Desktop > IP Configuration.
3. Cấu hình IP tĩnh:

```text
IP Address:      192.168.10.11
Subnet Mask:     255.255.255.0
Default Gateway: 192.168.10.1
```

4. Chọn Services > DNS.
5. Bật DNS Service.
6. Tạo các bản ghi DNS cần thiết.

Ví dụ:

| Name | Type | Address |
|---|---|---|
| server.company.local | A | 192.168.10.10 |
| dns.company.local | A | 192.168.10.11 |

Các bản ghi trên là ví dụ minh họa. Chỉ giữ lại những bản ghi tương ứng với server thực tế trong lab.

## 4. DNS Client Configuration

Các client nhận địa chỉ DNS Server thông qua DHCP:

```text
DNS Server: 192.168.10.11
```

Đối với thiết bị cấu hình IP tĩnh, cần nhập DNS Server thủ công.

## 5. DNS Verification

Trên PC:

```text
ipconfig /all
```

Kiểm tra DNS:

```text
nslookup server.company.local
```

Kiểm tra kết nối:

```text
ping 192.168.10.11
```

Kết quả mong đợi:
- Client sử dụng DNS Server 192.168.10.11.
- DNS Server phản hồi truy vấn.
- Tên miền được phân giải thành IP tương ứng.

## 6. Troubleshooting

| Vấn đề | Nguyên nhân có thể | Hướng xử lý |
|---|---|---|
| Không phân giải được tên | DNS Service chưa bật | Kiểm tra Services > DNS |
| nslookup timeout | Không đến được DNS Server | Kiểm tra routing, ACL và VPN |
| Tên miền trả về sai IP | Sai DNS Record | Kiểm tra bản ghi A |
| Ping IP được nhưng không phân giải tên | Cấu hình DNS client sai | Kiểm tra DNS Server trong DHCP hoặc IP Configuration |

## 7. Lưu ý thiết kế

- Không triển khai DNS nội bộ thứ hai trong topology.
- DNS Server 192.168.10.11 là máy chủ DNS duy nhất được mô hình thể hiện.
- Các chi nhánh cần có kết nối định tuyến và VPN phù hợp để truy cập DNS Server tại HQ.
- Khả năng phân giải tên miền Internet phụ thuộc vào bản ghi và chức năng DNS được mô phỏng trong Packet Tracer.
