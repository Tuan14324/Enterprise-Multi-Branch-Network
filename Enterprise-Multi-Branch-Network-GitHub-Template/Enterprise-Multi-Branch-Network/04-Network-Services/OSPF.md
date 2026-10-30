
# 02. OSPF Routing

## 1. Mục tiêu

Triển khai OSPF (Open Shortest Path First) để trao đổi thông tin định tuyến giữa Router và Core Layer 3 Switch tại từng địa điểm.

OSPF giúp:
- Tự động trao đổi route giữa Router và Core Switch.
- Giảm cấu hình định tuyến thủ công trong mạng nội bộ.
- Tự động cập nhật đường đi khi topology thay đổi.
- Quảng bá các mạng VLAN từ Core lên Router.

## 2. OSPF Design

OSPF Process ID: 1

Area: 0

### HQ

| Device | Interface | IP Address | Router ID |
|---|---|---|---|
| HQ Router | LAN Transit | 10.255.0.1/30 | 1.1.1.1 |
| CORE-HQ | Router Transit | 10.255.0.2/30 | 1.1.1.2 |

### BR1

| Device | Interface | IP Address | Router ID |
|---|---|---|---|
| BR1 Router | LAN Transit | 10.255.0.5/30 | 2.2.2.2 |
| CORE-BR1 | Router Transit | 10.255.0.6/30 | 2.2.2.3 |

### BR2

| Device | Interface | IP Address | Router ID |
|---|---|---|---|
| BR2 Router | LAN Transit | 10.255.0.9/30 | 3.3.3.3 |
| CORE-BR2 | Router Transit | 10.255.0.10/30 | 3.3.3.4 |

## 3. OSPF Configuration – HQ Router

```cisco
configure terminal

router ospf 1
 router-id 1.1.1.1
 network 10.255.0.0 0.0.0.3 area 0

end
write memory
```

## 4. OSPF Configuration – CORE-HQ

```cisco
configure terminal

router ospf 1
 router-id 1.1.1.2
 passive-interface default
 no passive-interface gigabitEthernet 0/2
 network 10.255.0.0 0.0.0.3 area 0
 network 192.168.10.0 0.0.0.255 area 0
 network 192.168.20.0 0.0.0.255 area 0
 network 192.168.30.0 0.0.0.255 area 0
 network 192.168.40.0 0.0.0.255 area 0

end
write memory
```

Lưu ý:
- Thay `gigabitEthernet 0/2` bằng đúng cổng Layer 3 kết nối đến HQ Router.
- `passive-interface default` ngăn gửi Hello OSPF trên các interface không cần thiết.
- Chỉ bỏ passive trên interface transit kết nối Router.
- Các VLAN được quảng bá vào OSPF nhưng không hình thành neighbor với thiết bị đầu cuối.

## 5. OSPF Configuration – BR1

### BR1 Router

```cisco
router ospf 1
 router-id 2.2.2.2
 network 10.255.0.4 0.0.0.3 area 0
```

### CORE-BR1

```cisco
router ospf 1
 router-id 2.2.2.3
 passive-interface default
 no passive-interface gigabitEthernet 0/2
 network 10.255.0.4 0.0.0.3 area 0
 network 192.168.50.0 0.0.0.255 area 0
 network 192.168.60.0 0.0.0.255 area 0
```

## 6. OSPF Configuration – BR2

### BR2 Router

```cisco
router ospf 1
 router-id 3.3.3.3
 network 10.255.0.8 0.0.0.3 area 0
```

### CORE-BR2

```cisco
router ospf 1
 router-id 3.3.3.4
 passive-interface default
 no passive-interface gigabitEthernet 0/2
 network 10.255.0.8 0.0.0.3 area 0
 network 192.168.70.0 0.0.0.255 area 0
```

Lưu ý: Các ví dụ trên giả định interface transit trên Core là Layer 3 và có IP theo IP Addressing Plan.

## 7. Default Route

Router tại mỗi địa điểm cần có đường đi ra ISP.

Ví dụ tại HQ Router:

```cisco
ip route 0.0.0.0 0.0.0.0 200.1.0.1
```

Nếu muốn quảng bá default route từ Router xuống Core bằng OSPF, có thể sử dụng:

```cisco
router ospf 1
 default-information originate
```

Chỉ sử dụng khi Router đã có default route hợp lệ.

Tại BR1:

```cisco
ip route 0.0.0.0 0.0.0.0 200.1.1.1
```

Tại BR2:

```cisco
ip route 0.0.0.0 0.0.0.0 200.1.2.1
```

## 8. Verification

```cisco
show ip ospf neighbor
show ip ospf interface brief
show ip protocols
show ip route ospf
show ip route
```

Kết quả mong đợi:
- Router và Core tại từng địa điểm hình thành OSPF neighbor.
- Neighbor đạt trạng thái FULL.
- Router học được các mạng VLAN từ Core.
- Core có thể học default route từ Router nếu cấu hình quảng bá default route.

## 9. Troubleshooting

| Vấn đề | Nguyên nhân có thể | Hướng xử lý |
|---|---|---|
| Không hình thành neighbor | Sai IP hoặc subnet | Kiểm tra interface và IP |
| Neighbor không FULL | Sai Area hoặc cấu hình OSPF không tương thích | Kiểm tra OSPF parameters |
| Không học được VLAN route | Chưa quảng bá subnet trên Core | Kiểm tra network statement |
| Core không có default route | Router chưa quảng bá default route | Kiểm tra static default route và `default-information originate` |

## 10. Phạm vi triển khai

OSPF chỉ hoạt động trong từng site:

- HQ Router ↔ CORE-HQ.
- BR1 Router ↔ CORE-BR1.
- BR2 Router ↔ CORE-BR2.

Không triển khai OSPF qua ISP hoặc qua các tunnel IPsec trong thiết kế này.

Các mạng chi nhánh được bảo vệ bằng VPN sử dụng cơ chế định tuyến phù hợp trên WAN Router.
