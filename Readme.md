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

**Kiến trúc tổng quan**  

          +----------------+  
Instruction →  Decoder     |  
          +----------------+  
                    ↓  
              +-----------+  
              |   ALU     |  
              +-----------+  
                    ↓  
              Register File  
                    ↓  
                Memory  
  
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

### ✅ 1. Timing report  
<img width="302" height="156" alt="image" src="https://github.com/user-attachments/assets/23e30e0c-098b-4622-95d2-a91ee4595abe" />  

### STA là gì?  
STA: Static Timing Analysis  
Kiểm tra tín hiệu chạy có kịp clock ?  
có Lỗi set up/hold timing không  

### rcx là gì?  
rcx: Resistance + Capacitance extraction  

Sau khi routing xong, tool sẽ:  

tính điện trở dây (R)  
tính điện dung (C)  
→ tính delay thật của tín hiệu.  

### Đầu tiên, kiểm tra tín hiệu chậm nhất:    

Các điều kiện kiểm tra:  
- R max và C max thì delay lớn nhất,  
- corner: slowest (transistor chậm nhất)  
-> tín hiệu đi chậm nhất  
-> dùng để kiểm tra SETUP Timing, trong trường hợp xấu nhất thì tín hiệu có chạy kịp clock hay không.

  Kiểm tra ****folder 31-sta-rcx_max****, ****file sta-rcx_max.rpt, corner: slowest****
 ### Đánh giá tổng quan:   
  - Có khoảng 400 critical path, setup timing bị ****VIOLATED****  
  - Trong đó critical path có slack âm nhất là từ flip flop Startpoint: _14499_ đến Endpoint: _14561_
  - Đây là Critical Path cụ thể:  
 
Startpoint: _14499_ (rising edge-triggered flip-flop clocked by clk)  
Endpoint: _14561_ (rising edge-triggered flip-flop clocked by clk)  
Path Group: clk  
Path Type: max  
Corner: Slowest  

Fanout     Cap    Slew   Delay    Time   Description  
-----------------------------------------------------------------------------  
                          0.00    0.00   clock clk (rise edge)  
                          0.00    0.00   clock source latency  
     3    0.12    0.83    0.61    0.61 ^ clk (in)  
                                         clk (net)  
                  0.83    0.00    0.61 ^ clkbuf_0_clk/A (sky130_fd_sc_hd__clkbuf_16)  
    21    0.32    0.49    0.86    1.47 ^ clkbuf_0_clk/X (sky130_fd_sc_hd__clkbuf_16)  
                                         clknet_0_clk (net)  
                  0.50    0.03    1.50 ^ clkbuf_4_10_0_clk/A (sky130_fd_sc_hd__clkbuf_8)  
    13    0.14    0.38    0.68    2.18 ^ clkbuf_4_10_0_clk/X (sky130_fd_sc_hd__clkbuf_8)  
                                         clknet_4_10_0_clk (net)  
                  0.38    0.01    2.18 ^ clkbuf_leaf_115_clk/A (sky130_fd_sc_hd__clkbuf_8)  
    13    0.05    0.14    0.44    2.63 ^ clkbuf_leaf_115_clk/X (sky130_fd_sc_hd__clkbuf_8)  
                                         clknet_leaf_115_clk (net)  
                  0.14    0.00    2.63 ^ _14499_/CLK (sky130_fd_sc_hd__dfxtp_2)  
     4    0.01    0.14    0.78    3.41 ^ _14499_/Q (sky130_fd_sc_hd__dfxtp_2)  
                                         count_cycle[0] (net)  
                  0.14    0.00    3.41 ^ _11356_/C (sky130_fd_sc_hd__and4_1)  
     5    0.01    0.25    0.57    3.98 ^ _11356_/X (sky130_fd_sc_hd__and4_1)  
                                         _05344_ (net)  
                  0.25    0.00    3.98 ^ _11365_/A (sky130_fd_sc_hd__and4_1)  
     5    0.02    0.27    0.59    4.58 ^ _11365_/X (sky130_fd_sc_hd__and4_1)  
                                         _05350_ (net)  
                  0.27    0.00    4.58 ^ _11374_/A (sky130_fd_sc_hd__and4_1)  
     4    0.01    0.21    0.55    5.12 ^ _11374_/X (sky130_fd_sc_hd__and4_1)  
                                         _05356_ (net)  
                  0.21    0.00    5.12 ^ _11383_/A (sky130_fd_sc_hd__and4_1)  
     6    0.02    0.31    0.61    5.73 ^ _11383_/X (sky130_fd_sc_hd__and4_1)  
                                         _05362_ (net)  
                  0.31    0.00    5.73 ^ _11392_/A (sky130_fd_sc_hd__and4_1)  
     1    0.01    0.14    0.49    6.22 ^ _11392_/X (sky130_fd_sc_hd__and4_1)  
                                         _05368_ (net)  
                  0.14    0.00    6.22 ^ _11393_/A (sky130_fd_sc_hd__buf_6)  
     8    0.03    0.11    0.26    6.48 ^ _11393_/X (sky130_fd_sc_hd__buf_6)  
                                         _05369_ (net)  
                  0.11    0.00    6.48 ^ _11405_/A (sky130_fd_sc_hd__and4_1)  
     5    0.02    0.38    0.63    7.11 ^ _11405_/X (sky130_fd_sc_hd__and4_1)  
                                         _05377_ (net)  
                  0.38    0.00    7.11 ^ _11415_/A (sky130_fd_sc_hd__and4_1)  
     5    0.01    0.25    0.63    7.75 ^ _11415_/X (sky130_fd_sc_hd__and4_1)  
                                         _05384_ (net)  
                  0.25    0.00    7.75 ^ _11424_/A (sky130_fd_sc_hd__and4_1)  
     5    0.01    0.23    0.56    8.30 ^ _11424_/X (sky130_fd_sc_hd__and4_1)  
                                         _05390_ (net)  
                  0.23    0.00    8.30 ^ _11436_/A (sky130_fd_sc_hd__and4_1)  
     5    0.01    0.25    0.57    8.87 ^ _11436_/X (sky130_fd_sc_hd__and4_1)  
                                         _05399_ (net)  
                  0.25    0.00    8.87 ^ _11445_/A (sky130_fd_sc_hd__and4_1)  
     6    0.02    0.38    0.68    9.55 ^ _11445_/X (sky130_fd_sc_hd__and4_1)  
                                         _05405_ (net)  
                  0.38    0.00    9.55 ^ _11454_/A (sky130_fd_sc_hd__and4_1)  
     5    0.02    0.30    0.67   10.22 ^ _11454_/X (sky130_fd_sc_hd__and4_1)  
                                         _05411_ (net)  
                  0.30    0.00   10.22 ^ _11463_/A (sky130_fd_sc_hd__and4_4)  
     6    0.03    0.20    0.68   10.90 ^ _11463_/X (sky130_fd_sc_hd__and4_4)  
                                         _05417_ (net)  
                  0.20    0.00   10.91 ^ _11472_/A (sky130_fd_sc_hd__and4_1)  
     6    0.02    0.32    0.61   11.52 ^ _11472_/X (sky130_fd_sc_hd__and4_1)  
                                         _05423_ (net)  
                  0.32    0.00   11.52 ^ _11482_/A (sky130_fd_sc_hd__and4_1)  
     6    0.02    0.33    0.67   12.19 ^ _11482_/X (sky130_fd_sc_hd__and4_1)  
                                         _05430_ (net)  
                  0.33    0.00   12.19 ^ _11491_/A (sky130_fd_sc_hd__and4_1)  
     6    0.03    0.41    0.74   12.93 ^ _11491_/X (sky130_fd_sc_hd__and4_1)  
                                         _05436_ (net)  
                  0.41    0.00   12.93 ^ _11504_/A (sky130_fd_sc_hd__and4_1)  
     5    0.02    0.27    0.65   13.58 ^ _11504_/X (sky130_fd_sc_hd__and4_1)  
                                         _05445_ (net)  
                  0.27    0.00   13.58 ^ _11513_/A (sky130_fd_sc_hd__and4_1)  
     6    0.02    0.32    0.64   14.22 ^ _11513_/X (sky130_fd_sc_hd__and4_1)  
                                         _05451_ (net)  
                  0.32    0.00   14.22 ^ _11525_/A (sky130_fd_sc_hd__and4_1)  
     5    0.02    0.30    0.64   14.87 ^ _11525_/X (sky130_fd_sc_hd__and4_1)  
                                         _05459_ (net)  
                  0.30    0.00   14.87 ^ _11535_/A (sky130_fd_sc_hd__and4_1)  
     1    0.01    0.14    0.48   15.35 ^ _11535_/X (sky130_fd_sc_hd__and4_1)  
                                         _05466_ (net)  
                  0.14    0.00   15.35 ^ _11536_/A (sky130_fd_sc_hd__buf_6)  
     6    0.03    0.11    0.25   15.60 ^ _11536_/X (sky130_fd_sc_hd__buf_6)  
                                         _05467_ (net)  
                  0.11    0.00   15.61 ^ _11542_/A (sky130_fd_sc_hd__nand3_4)  
     2    0.01    0.10    0.13   15.74 v _11542_/Y (sky130_fd_sc_hd__nand3_4)  
                                         _05471_ (net)  
                  0.10    0.00   15.74 v _11543_/A (sky130_fd_sc_hd__and3_1)  
     1    0.00    0.07    0.31   16.05 v _11543_/X (sky130_fd_sc_hd__and3_1)  
                                         _05472_ (net)  
                  0.07    0.00   16.05 v _11544_/A (sky130_fd_sc_hd__clkbuf_1)  
     1    0.00    0.05    0.17   16.22 v _11544_/X (sky130_fd_sc_hd__clkbuf_1)  
                                         _00737_ (net)  
                  0.05    0.00   16.22 v _14561_/D (sky130_fd_sc_hd__dfxtp_1)  
                                 16.22   data arrival time  

                         10.00   10.00   clock clk (rise edge)
                          0.00   10.00   clock source latency
     3    0.12    0.83    0.55   10.55 ^ clk (in)
                                         clk (net)
                  0.83    0.00   10.55 ^ clkbuf_0_clk/A (sky130_fd_sc_hd__clkbuf_16)
    21    0.32    0.49    0.78   11.33 ^ clkbuf_0_clk/X (sky130_fd_sc_hd__clkbuf_16)
                                         clknet_0_clk (net)
                  0.50    0.02   11.36 ^ clkbuf_4_10_0_clk/A (sky130_fd_sc_hd__clkbuf_8)
    13    0.14    0.38    0.61   11.97 ^ clkbuf_4_10_0_clk/X (sky130_fd_sc_hd__clkbuf_8)
                                         clknet_4_10_0_clk (net)
                  0.38    0.01   11.98 ^ clkbuf_leaf_119_clk/A (sky130_fd_sc_hd__clkbuf_8)
    13    0.04    0.14    0.40   12.37 ^ clkbuf_leaf_119_clk/X (sky130_fd_sc_hd__clkbuf_8)
                                         clknet_leaf_119_clk (net)
                  0.14    0.00   12.38 ^ _14561_/CLK (sky130_fd_sc_hd__dfxtp_1)
                         -0.25   12.13   clock uncertainty
                          0.21   12.33   clock reconvergence pessimism
                         -0.24   12.09   library setup time
                                 12.09   data required time
-----------------------------------------------------------------------------
                                 12.09   data required time
                                -16.22   data arrival time
-----------------------------------------------------------------------------
                                 -4.13   slack (VIOLATED)    

  **PHÂN TÍCH PATH**    
  **Tổng quan**  
  - Start FF (_14499_) → AND chain (~20 stages) → FF (_14561_)  
  - The data arrival time is ****16.22 ns,  the required time of 12.09 ns****
  - Kết quả: vi phạm setup timing  ****-4.13 ns.****   

**Phân tích theo từng khối**  
 **a. Clock Path:**  
 - clk → clkbuf → clkbuf → clkbuf → _14499_/CLK  
 - Theo như Path trên:
clkbuf_0_clk/X   ****delay 0.86 → time 1.47****  
clkbuf_4_10_0    ****delay 0.68 → time 2.18****  
clkbuf_leaf      ****delay 0.44 → time 2.63****
-> clock đến FF mất 2.63ns  
-> tăng thêm thời gian delay cho required time

   **b. Launch FF**
   Đây là Clock-to-Q delay (C2Q), delay khởi đầu của Path:  
   _14499_/Q   delay 0.78 → time 3.41
   

   **c. Data Path**
   - Bắt đầu từ net:
   count_cycle[0] (net)  
-> Tuy không có delay riêng, nhưng tại thời điểm đó thì:  
  + fanout = 4  
  + cap = 0.01  
→ ảnh hưởng wire delay + slew của stage sau  

**d. các AND Gates**   
Dựa theo Path trên:  
_11356_/X   delay 0.57 → time 3.98  
_11365_/X   delay 0.59 → time 4.58  
_11374_/X   delay 0.55 → time 5.12  
...  

Time: 3.41 → 4.58 → 5.12 → ... → 15.35  
-> Delay cộng dần qua từng stage  

**e. Fanout và Cap tăng**  
Dựa theo Path trên:  
- Fanout: 5 → 6 → 8  
- Cap:    0.01 → 0.03
-> wire delay tăng  
-> cell delay tăng (do load lớn hơn)

  **f. Thêm buffer**
  Dựa theo Path trên:
  - buf_6 delay 0.26  
  - buf_6 delay 0.25
  -> mục đích giảm fanout/ cải thiện slew để giảm delay nhưng không giảm logic depth

    **g. Late-stage logic**
  - Late-stage logic là các gate nằm GẦN endpoint (FF cuối)   
  - Trong Path là :  
nand3_4 → and3_1 → clkbuf_1 → FF (_14561_)
  - Timeline là:
  ...
14.87 ns → AND  
15.35 ns → AND  
15.74 ns → NAND  
16.05 ns → AND  
16.22 ns → FF
    -> các gate này hoạt động ở cuối path
    - Các Late-gate này chỉ gây ra delay rất nhỏ (0.1-0.3ns) so với AND chain trước đó (0.6ns x stage)  
    - Tuy các delay nhỏ nhưng nó chỉ xảy ra sau khi các AND gate trước đó đã tạo ra tổng delay quá lớn

  **KẾT LUẬN**  
- Delay của critical path này bị ảnh hưởng bởi nhiều cổng AND, trong đó mỗi stage (khoảng 20 stage) đóng góp khoảng 0.5–0.7 ns. Tổng data arrival time lên tới 16.22 ns.  

- Mặc dù vẫn có độ trễ dây (wire delay) do fanout và C điện dung tăng lên, nhưng đây không phải là yếu tố chính làm tăng delay lên. Việc thêm buffer giúp cải thiện độ chuyển mức tín hiệu (slew), nhưng không làm giảm logic depth, nên không cải thiện được độ delay của data arrival time.

- Vì vậy, nguyên nhân chính làm timing bị violated là do tổ hợp gate quá lớn. 
   

    

    




 
   
  
  
   


 












  





 











