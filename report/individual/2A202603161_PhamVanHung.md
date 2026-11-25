# Báo cáo Day 13 — Phạm Văn Hùng, 2A202603161

## 1. Kết luận trước
- Mã số: **2A202603161** · Task: **D13-2A202603161**
- 10 frame: s01f34, s01f59, s02f02, s02f42, s03f21, s03f41, s04f00, s04f52, s05f25, s05f40
- Hệ điều hành: Windows · Trình duyệt: Chrome · CVAT: server 2.74.1 (theo thông báo của `lab.py`)
- MSSV ngồi cạnh: 2A202603168

Tổng cộng **58 cuboid**: 52 Car, 2 Bus, 2 Cyclist, không có Pedestrian. Frame ít nhất là **s02f42 (2 cuboid)**. Cảnh này vắng xe, và các người đi bộ hai bên không xác định được hướng đi nên bị bỏ qua chứ không đoán. `selfcheck` cờ **2/58 cuboid**, cả hai là "điểm sát ngay ngoài mép". Phần tin nhất là xe con cùng chiều ở gần: yaw 270°, đáy khoảng −1,77 m. Phần yếu nhất là vật xa trên 25 m (chỉ còn vài chục điểm), hướng của nhóm xe ngược chiều ở s03f21, và việc không gán người đi bộ nào.

Cách dùng CVAT:
- **Góc nhìn:** lăn chuột để phóng to/thu nhỏ, kéo chuột trái để xoay, kéo chuột phải để dời; các nút mũi tên ở góc khung 3D để nghiêng camera; nháy đúp chỗ trống để về góc mặc định.
- **Cuboid:** vẽ bằng **Shape** (không dùng Track). Chọn hộp thì ba khung Top/Side/Front phóng quanh nó: chỉnh tâm, dài, rộng, yaw ở Top; chỉnh đáy và chiều cao ở Side/Front.
- **Kiểm hướng:** bật **Appearance → Cuboid orientation**, vạch đỏ `+X` phải chỉ về đầu xe. Chạy `python scripts/lab.py preview` để xem cuboid chiếu lên 7 ảnh camera.

## 2. Đọc frame (Bước 2)
Hai frame đọc kỹ. Ảnh: `t1-s01f34-3d.jpg`, `t1-s01f34-top.png`, `t1-s01f59-3d.jpg`, `t1-s01f59-top.png`. Hai ảnh `-top` có đánh dấu phía trước, +x, +y; góc nhìn từ trên cao, hơi nghiêng.

| Frame | Phía trước xe ở cạnh nào của ảnh nhìn từ trên xuống; +x, +y chỉ về đâu | Mặt đường (phẳng/dốc; thấp hơn LiDAR ~ m) | Vật xa nhất đọc được (~ m) | Loại vật thấy | Vật phía sau / hai bên | Cụm chưa đọc được |
|---|---|---|---|---|---|---|
| s01f34 | Phía trước (−y) ở **cạnh trái** ảnh; +x (trái xe) chỉ **xuống dưới**; +y (sau xe) chỉ **sang phải** | Phẳng; thấp hơn LiDAR ~1,7 m (z ≈ −1,72 m) | ~25–30 m (xe ở đường bên kia dải phân cách) | Xe con, hàng xe máy/xe đạp dựng ở quảng trường bên phải, cây, dải phân cách | Xe con ngay phía sau ~9,8 m (cam09) và ~18 m phía sau bên trái; quảng trường bên phải (cam07, cam08) | Cụm điểm cao 2–4 m ở quảng trường bên phải ~20 m: có thể là cây hoặc ki-ốt, chưa chắc |
| s01f59 | Phía trước (−y) ở **cạnh trái** ảnh; +x **xuống dưới**; +y **sang phải** | Phẳng; thấp hơn LiDAR ~1,8 m | ~29 m (xe trong hàng chờ) | Hàng xe con đang chờ cùng chiều, xe đạp có người bên phải, cây, dải phân cách | Xe con sát phía sau ~6,8 m; người đi xe đạp phía sau bên phải trên đường riêng (cam09) | Cụm nhỏ ~19 m phía trước giữa hai xe: có thể là phần xe bị xe trước che |

Chỉ camera cho biết: (1) màu xe, ví dụ xe trắng phía trước bên trái ở s01f34; (2) loại xe và đầu/đuôi xe nhìn qua đèn và kính, ví dụ hàng xe dừng ở s01f59 đều quay đuôi về phía mình; (3) biển chỉ đường màu xanh phía trước và vạch kẻ làn.
Chỉ LiDAR cho biết: (1) khoảng cách theo mét, ví dụ xe sát bên trái ở s01f59 chỉ cách 2,89 m; (2) kích thước thật theo mét, ví dụ xe con dài ~4,3 m; (3) độ cao mặt đường so với cảm biến (~−1,7 m), và vật nằm ở vùng giữa các hình quạt camera.

Tám frame lướt (phía trước luôn là −y, trùng hướng cam01):

| Frame | Phía trước ở cạnh nào của ảnh nhìn từ trên xuống | Điều lạ (nếu có) |
|---|---|---|
| s02f02 | −y (hướng cam01) | Xe buýt sát bên trái che khuất cả một vùng phía sau nó |
| s02f42 | −y (hướng cam01) | Cụm ~12 điểm nằm dưới mặt đường (z ≈ −9,3 m); biển báo trên cao bên phải ~21 m |
| s03f21 | −y (hướng cam01) | Ngã tư; mặt đường thấp hơn bình thường (dốc) |
| s03f41 | −y (hướng cam01) | Đường thẳng, hàng rào bên phải; xe ~35 m chỉ còn ~15 điểm |
| s04f00 | −y (hướng cam01) | Ngã tư, xe minivan sát bên phải |
| s04f52 | −y (hướng cam01) | Đường dẫn nhập làn bên phải; một xe ~12 m có ít điểm bất thường |
| s05f25 | −y (hướng cam01) | Ngã tư; xe máy có người cắt ngang bên phải |
| s05f40 | −y (hướng cam01) | Xe buýt bên trái chỉ thấy được mặt bên phải và đuôi |

## 3. Ba cuboid có hướng dẫn (Bước 3)
Ảnh: `t2-a-car.jpg`, `t2-b-bus-truck.jpg`, `t2-c-ped-cyclist.jpg`.

| Vật | Frame, idx | Class | Mũi tên yaw đúng hướng? | Đáy chạm đường? | Đã kiểm Top/Side/Front + ảnh? | Kích thước trong bảng 4.3? |
|---|---|---|---|---|---|---|
| (a) xe con gần | s01f34, 1 | Car | Có, 270° (cùng chiều, đầu xe về −y) | Có, z đáy −1,77 m | Có; cam01 thấy xe trắng phía trước bên trái | Có: 4,30 × 1,73 × 1,47 m (sedan) |
| (b) xe buýt hoặc xe tải | s02f02, 1 (frame 1 và 2 không có xe buýt hay xe tải) | Bus | Có, 270° | Có, z đáy −1,78 m | Có; cam01, cam05, cam06 thấy xe buýt đỏ | Dài 11,93 m, cao 3,13 m trong khoảng; rộng 2,89 m lớn hơn 2,60 m (hộp tính cả gương chiếu hậu) |
| (c) người đi bộ hoặc xe hai bánh | s01f59, 13 | Cyclist | 270°, giả định đi cùng chiều xe cơ giới | Có, đáy −1,37 m (trên vỉa hè cao hơn mặt đường) | Có; cam07 thấy người đi xe bên phải | Có: 1,90 × 0,80 × 1,44 m |

Vòng hiệu chuẩn: em không tham gia (không tạo task chung `D13-HC485087`).

Vật bỏ qua vì không đủ cơ sở:
- **s01f34:** hàng xe máy/xe đạp dựng ở quảng trường bên phải ~15–23 m. Không có người lái nên theo guideline không vẽ.
- **s01f34:** một cụm ~23 m bên kia dải phân cách trái, mặt đất ở đó cao hơn hẳn mặt đường. Không chắc là xe (UNCERTAIN_CLASS); hộp đã vẽ rồi xoá.
- **s02f42:** 3 người đi bộ hai bên đường ~15–25 m. Không xác định được hướng đi nên không đoán (ATTRIBUTE_CHECK).
- **s03f21:** xe cắt ngang bên phải và người qua đường bên phải. Không xác định được hướng đầu xe.
- **s04f00:** xe bên trái ở ngã tư. Hướng chưa chắc.
- **s05f25:** xe máy có người cắt ngang bên phải và xe SUV bên trái. Không xác định được hướng.
- **s05f25:** cụm ~33 m phía trước bên trái gồm 2 xe dính nhau, rộng 3,34 m. Đã xoá (LOW_VISIBILITY).

## 4. Bốn hiện tượng chất lượng (Bước 4)
Nguyên nhân ghi là **giả thuyết**. Cả bốn hiện tượng đều lấy từ frame của em, không mượn frame.

| Hiện tượng | Frame / tên ảnh / nguồn | Nguyên nhân (giả thuyết) | Thấy gì trên point cloud | Hệ quả khi gán (theo guideline) |
|---|---|---|---|---|
| 1. Bóng che | s02f02 / `t4-1-bong-che.png` / frame của em | Giả thuyết: xe buýt cao ~3,1 m sát bên trái (~3,4 m) chắn tia LiDAR | Phía sau xe buýt (phía +x) gần như không có điểm, trong khi hai bên vùng đó vẫn dày điểm | Vùng trống là **chưa quan sát được**, không phải đường trống. Vật bị che một phần thì bật occluded và vẽ cuboid bao phần suy luận được |
| 2. Thưa ở xa | s03f41 / `t4-2-thua-o-xa.png` / frame của em | Giả thuyết: ở xa, khoảng cách giữa các tia LiDAR lớn nên ít tia chạm vào xe | Xe con #5 ở 35,7 m chỉ còn 15 điểm trong hộp; ba khung Top/Side/Front gần như trống | Thấy khoảng 15–20% thể tích hoặc suy luận được (từ ảnh camera) thì vẽ; không thì bỏ qua và ghi LOW_VISIBILITY |
| 3. Phản xạ thấp / ít điểm bất thường | s04f52 / `t4-3-phan-xa-thap.png` / frame của em | Giả thuyết: sơn tối màu hoặc kính phản xạ yếu, hoặc góc xe chéo so với tia; chưa khẳng định | Xe #4 ở 12,1 m chỉ có 248 điểm. Xe cùng frame ở 8,5 m có 1595 điểm, các xe khác ở 10–20 m có khoảng 500–950 điểm | Ghi là giả thuyết. Không thu nhỏ hộp theo số điểm, dùng ảnh camera và bảng 4.3 để giữ kích thước |
| 4. Điểm lạ | s02f42 / `t4-4-diem-la.png` / frame của em | Giả thuyết: phản xạ nhiều lần (multipath) từ bề mặt kính hoặc kim loại, hoặc nhiễu; chưa rõ | ~12 điểm ở z ≈ −9,3 m, thấp hơn mặt đường ~7,5 m, phía trước bên phải ~20 m. Khung xanh trong ảnh là hộp đánh dấu tạm để chỉ vị trí, đã xoá ngay, không có trong bảng A | Không gán thành vật |

## 5. Số từ cuboid của tôi (Bước 5)
Số liệu lấy từ `submission/D13-2A202603161-bangA.csv`; số điểm lấy từ cột `points_in` của `submission/D13-2A202603161-tukiem.csv`.

| Cuboid (frame, idx, class) | z | h | z đáy = z − h/2 | dist_m | r = √(x²+y²+z²) | yaw_deg | Nhận xét (lơ lửng / chìm / chạm đường) |
|---|---|---|---|---|---|---|---|
| s01f34, 1, Car | −1,035 m | 1,47 m | −1,77 m | 8,64 m | 8,70 m | 270° | Chạm đường (lệch 0,07 m so với −1,7 m) |
| s02f02, 1, Bus | −0,215 m | 3,13 m | −1,78 m | 3,36 m | 3,36 m | 270° | Chạm đường |
| s03f21, 2, Car | −1,325 m | 1,91 m | −2,28 m | 19,93 m | 19,98 m | 90° | Lệch 0,58 m so với −1,7 m. Ở chỗ này mặt đường thấp hơn do dốc; khoảng hở với mặt đường trong phiếu tự kiểm là 0,06 m, nên hộp chạm đường, không chìm |

**(a) Gần nhất và xa nhất:**
- Gần nhất: s01f59 #1 Car, dist_m = 2,89 m, r = 3,06 m.
- Xa nhất: s03f41 #5 Car, dist_m = 35,73 m, r = 35,75 m.

**(b) Kích thước so với bảng 4.3:**
- Xe con s01f34 #1: 4,30 × 1,73 × 1,47 m. Cả ba chiều nằm trong khoảng sedan (dài 3,8–5,0 m; rộng 1,70–1,90 m; cao 1,40–1,60 m).
- Xe máy có người s01f59 #13: 1,90 × 0,80 × 1,44 m. Cả ba chiều nằm trong khoảng (1,8–2,3 m; 0,70–0,95 m; 1,40–1,80 m). Lưu ý: dài và rộng được đặt theo cỡ chuẩn vì cụm điểm lẫn bụi cây bên cạnh.
- Không có người đi bộ nào được gán.

**(c) Yaw:**
- Xe cùng chiều phía trước s01f34 #1: 270°.
- Xe ngược chiều s03f21 #1: 90°.
- Đúng như kỳ vọng 270° và 90°.

Mật độ theo khoảng cách:

| Cuboid (frame, idx, class) | dist_m | Số điểm (`points_in`) |
|---|---|---|
| s01f59, 1, Car | 2,9 m | 3230 |
| s01f59, 4, Car | 10,1 m | 969 |
| s04f00, 4, Car | 24,0 m | 225 |
| s04f52, 6, Car | 33,2 m | 115 |

Quy luật: xa gấp ~3,3 lần (10,1 m → 33,2 m) thì số điểm còn ~1/8 (969 → 115). Số điểm giảm gần theo 1/d². Số liệu gần khớp đường cong ở slide: 969 điểm ở 10,1 m so với 718 ở 10–20 m; 225 điểm ở 24 m so với 214 ở 20–30 m; 115 điểm ở 33 m so với 72 ở 30–40 m. Ở 33 m số điểm cao hơn slide, có thể vì xe nằm ngay trước mặt, không bị che.

Tự kiểm: các cuboid đã sửa sau `selfcheck` (AI sửa theo cảnh báo, xem 6b):
- Xoá 1 hộp Car ở s01f34 (~23 m, bên kia dải phân cách): đáy chìm 0,80 m, không chắc là xe.
- Thu nhỏ s01f59 #13 Cyclist từ 2,78 × 1,68 m xuống 1,90 × 0,80 m.
- Thu nhỏ s02f02 #6 Car từ dài 6,56 m xuống 4,50 m: cụm điểm có thể là 2 xe dính nhau.
- Xoá 1 hộp Car ở s05f25 (~33 m), rộng 3,34 m: 2 xe dính nhau.

Hai cảnh báo còn lại em giữ nguyên:
- s01f59 #13: 50 điểm sát ngoài mép.
- s02f02 #6: 31 điểm sát ngoài mép.

Lý do: điểm ngoài mép là bụi cây hoặc xe bên cạnh, không thuộc vật. Kéo hộp ra để hết cờ sẽ làm hộp sai kích thước.

Đổi kiểm: TODO(học viên): 2A202603168 , .

## 6. Ý nghĩa của nhãn, cách dùng AI và điều còn chưa chắc
**(a)** Model 3D dùng nhãn này để học vị trí và kích thước thật của vật theo mét quanh xe. Đó là thứ hệ thống lái cần để tính khoảng cách an toàn, còn kích thước tính bằng pixel thay đổi theo khoảng cách nên không dùng được. Vật thưa điểm cần quy tắc riêng: nếu đoán bừa hộp cho vài điểm thì model học ra hộp sai, còn bỏ sót vô tội vạ thì model học cách bỏ qua vật thật; quy tắc 15–20% thể tích và LOW_VISIBILITY giữ cho nhãn nhất quán.

**(b)** Có dùng AI, được GV cho phép do deadline rút ngắn.
- Công cụ: Claude (Anthropic).
- Việc chính: hướng dẫn quy trình; tự động gán phần lớn cuboid qua API CVAT, có đối chiếu ảnh camera; sửa 4 hộp theo `selfcheck`; chụp 11 ảnh; soạn nháp báo cáo này từ bảng A và phiếu tự kiểm.
- Cách em dùng kết quả: em kiểm lại trên CVAT, chạy `sync`, và đối chiếu số liệu với bảng A. Chi tiết đã gửi riêng cho GV.

**(c)** Điều còn chưa chắc:
- **Hướng xe ở s03f21:** 5 xe được đặt là xe ngược chiều (yaw 90°) chỉ dựa trên vị trí bên kia làn đường.
- **Xe chỉ thấy một phần:** nhiều hộp xe con được kéo ra theo kích thước chuẩn, khoảng 4,3 × 1,73 m.
- **Người đi bộ:** không gán người đi bộ nào, nên độ phủ với người đi bộ thấp.
- **Hai hiện tượng 3 và 4:** nguyên nhân mới chỉ là giả thuyết.
