### CHECK REPORT VỀ CONGESTION (ROUTING)  

**1. Congestion là gì?**  
Congestion = tình trạng quá nhiều dây (routing demand) đi qua cùng một vùng chip > khả năng chứa của vùng đó (routing capacity)  

****2. Vì sao xảy ra congestion?****  
Nguyên nhân:  
- Placement quá dày (cell bị nhét quá sát)  
Logic kết nối phức tạp (fanout lớn)  
Macro/block chặn đường đi  
Clock tree phân bố không đều  



- Ở thư mục ****"reports/routing/"****  
- Mở file ****"26-drt_metrics.json":****  
- {
	"route__net": 10528,  
	"route__net__special": 2,  
	"route__drc_errors__iter:1": 8106,  
	"route__wirelength__iter:1": 455223,  
	"route__drc_errors__iter:2": 3196,  
	"route__wirelength__iter:2": 451660,  
	"route__drc_errors__iter:3": 2885,  
	"route__wirelength__iter:3": 451079,  
	"route__drc_errors__iter:4": 322,  
	"route__wirelength__iter:4": 450521,  
	"route__drc_errors__iter:5": 36,  
	"route__wirelength__iter:5": 450521,  
	"route__drc_errors__iter:6": 7,  
	"route__wirelength__iter:6": 450494,  
	"route__drc_errors__iter:7": 0,  
	"route__wirelength__iter:7": 450484,  
	"route__drc_errors": 0,  
	"route__wirelength": 450484,  
	"route__vias": 80363,  
	"route__vias__singlecut": 80363,  
	"route__vias__multicut": 0,  
	"design__io": 411,  
	"design__die__area": 257852,  
	"design__core__area": 240531,  
	"design__instance__count": 14226,  
	"design__instance__area": 119115,  
	"design__instance__count__stdcell": 14226,  
	"design__instance__area__stdcell": 119115,  
	"design__instance__count__macros": 0,  
	"design__instance__area__macros": 0,  
	"design__instance__utilization": 0.49522,  
	"design__instance__utilization__stdcell": 0.49522,  
	"flow__warnings__count": 10,  
	"flow__errors__count": 0  
}   

### PHÂN TÍCH  
### a. DRC Errors  
route__drc_errors__iter:1 → 8106  
...  
route__drc_errors__iter:7 → 0  
route__drc_errors = 0 ✅  
-> Ban đầu có nhiều lỗi (8106), sau đó tool fix dần rồi drc_errors = 0 (Clean routing).   
-> Kết luận: không có vấn đề về layout  








