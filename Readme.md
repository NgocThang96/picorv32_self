Bài viết này dựa trên nguồn :https://github.com/YosysHQ/picorv32/

PICORV32 - A Size-Optimized RISC-V CPU
============================================================
**⚙️1. PicoRV32 là gì?:**  
🔹Là soft-core CPU → không phải chip vật lý, mà là thiết kế phần cứng (HDL) để bạn nạp vào FPGA.  
🔹Tuân theo chuẩn RISC-V (open-source ISA)  
🔹Tập trung vào:  
-Diện tích nhỏ (ít tài nguyên)  
-Dễ tích hợp  
-Linh hoạt cấu hình  
🔹Bên trong có:  
-ALU (cộng, trừ, logic)  
-Register file  
-Instruction decoder  
-Control logic  
-Memory interface  

**⚙️ 2. Đặc điểm chính**  
🔹 Nhẹ và tối ưu  
-Rất ít LUT/FF khi triển khai trên FPGA  
-Phù hợp cho hệ thống nhúng nhỏ  
🔹 Có thể tùy chỉnh  
Bạn có thể bật/tắt nhiều tính năng:
-Bộ nhân (M extension)  
-Bộ chia  
-Interrupt  
-Memory interface  
🔹 Interface đơn giản  
-Giao tiếp bộ nhớ kiểu đơn giản (valid/ready)  
-Dễ kết nối với RAM, ROM hoặc bus như AXI (qua adapter)  

**🧩 3. PicoRV32 dùng để làm gì?**  
-Học và nghiên cứu kiến trúc CPU  
-Làm đồ án Digital / VLSI / Physical Design  
-Nhúng vào FPGA (Xilinx, Intel…)  
-Xây dựng SoC nhỏ (kết hợp RAM, UART, GPIO…)  

**🏗️ 4. PicoRV32 trong flow thiết kế chip**  
Trong flow như OpenLane / OpenROAD (bạn đang làm):  
-Viết / lấy code Verilog PicoRV32  
-Synthesis (tổng hợp logic)  
-Place & Route  
-STA (timing)  
-Tapeout (nếu làm ASIC)  

**👉 5. PicoRV32 rất hay được dùng vì:**  
-Code gọn  
-Dễ chạy flow từ RTL → GDSII  

**✅ Ưu điểm**  
-Nhẹ, nhanh, dễ dùng  
-Open-source hoàn toàn  
-Cộng đồng RISC-V lớn  

**❌ Nhược điểm**  
-Không mạnh như CPU phức tạp (ARM Cortex-A…)  
-Hiệu năng vừa phải  
-Không có cache phức tạp  

**🧪 6. So sánh nhanh**  

| Tiêu chí                   | PicoRV32                                                           | CPU lớn (ARM, x86) |
| ------------------------ | --------------------------------------------------------------------- |--------------------|
| `Kích thước`               | Rất nhỏ                                                               | Rất lớn |
| `Hiệu năng`                | Trung bình                                                            | Cao|
| `Tùy chỉnh`                           | Cao                                                         |Thấp|
| `Mục đích`            | Nhúng, học tập                                                             | PC, điện thoại |


CÁC BƯỚC CƠ BẢN THỰC HIỆN PROJECT
--------------------------------------------------------------------

**Tổng quan project bạn sẽ làm**  
>PicoRV32 (Verilog RTL)  
>        ↓  
>Synthesis  
>        ↓  
>Floorplan  
>        ↓  
>Placement  
>        ↓  
>CTS  
>        ↓  
>Routing  
>        ↓  
>Timing Analysis  
>        ↓  
>GDSII (layout chip)  

### Chuẩn bị Tools  
- Cài Linux  
- Các các tool ASIC:  
Yosys → synthesis  
OpenROAD → placement/CTS/routing  
OpenSTA → timing  
Magic → layout  

### Bước 1:  Lấy RTL của PicoRV32  
git clone https://github.com/YosysHQ/picorv32.git  

### Bước 2:  Tạo project OpenLane  
Cấu trúc thư mục:  

picorv32_core/  
 ├── config.json  
 ├── src/  
 │    └── picorv32.v  

### Bước 3:  Viết config  
- Tạo file : config.json  
{  
  "DESIGN_NAME": "picorv32",  
  "VERILOG_FILES": "dir::src/picorv32.v",  
  "CLOCK_PORT": "clk",  
  "CLOCK_PERIOD": 10,  
  "FP_CORE_UTIL": 40,  
  "PL_TARGET_DENSITY": 0.60  
}  
- Chú thích:  
  
| Tham số                  | Nghĩa                                                          | 
| ------------------------ | --------------------------------------------------------------------- |
| `CLOCK_PERIOD`               | Chu kỳ clock                                                             | 
| `CORE_UTIL`                | Mật độ Cell                                                            |  
| `TARGET_DENSITY`                | Mật độ lấp đầy mục tiêu                                                          | 


### Bước 4:  Chạy Physical Design flow  
- Vào Openlane nhập lệnh "make mount"
- Chạy flow: "./flow.tcl -design picorv32_core"
- Tool sẽ chạy:  
✅ synthesis  
✅ floorplan  
✅ placement  
✅ CTS  
✅ routing  
✅ STA

### Bước 5: kiểm tra báo cáo  

✅ 1. Timing report  
<img width="302" height="156" alt="image" src="https://github.com/user-attachments/assets/23e30e0c-098b-4622-95d2-a91ee4595abe" />  
- STA là gì?  
STA là Static Timing Analysis
Kiểm tra tín hiệu chạy có kịp clock ?
có Lỗi set up/hold timing không

  





 











