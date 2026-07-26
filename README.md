# BIM QAQC - Phiên bản v0.1.0

BIM QAQC là giải pháp phần mềm chuyên nghiệp phục vụ công tác kiểm định chất lượng mô hình BIM (Quality Assurance/Quality Control) và bóc tách khối lượng (Quantity Takeoff - QTO). Phần mềm hỗ trợ xử lý dữ liệu từ định dạng IFC, lưu trữ vào SQLite và xuất báo cáo kiểm định LOI kết hợp trình xem 3D tương tác WebGL hiệu năng cao.

---

## 🚀 Tính năng nổi bật trong v0.1.0

1. **Hiệu năng Caching Hai Tầng (SHA-256)**:
   - **Tầng dữ liệu (SQLite)**: Lưu dấu vân tay SHA-256 của file IFC để bỏ qua bóc tách dữ liệu nếu nội dung file không đổi, tăng tốc xử lý lên đến **30x**.
   - **Tầng hình học (Zero Wait GLB Caching)**: Áp dụng cơ chế tiền xử lý ngầm (Background Pre-processing). Ngay khi chọn thư mục dự án, luồng phụ sẽ âm thầm chuyển đổi `.ifc` sang `.glb` siêu nhẹ thông qua `IfcConvert`. Khi người dùng mở trình xem 3D, mô hình đã sẵn sàng ngay lập tức (Zero Wait) mà không cần chờ đợi.
2. **Bộ Ánh Xạ Thuộc Tính Tự Học (Semantic Mapper)**:
   - Quy tắc Regex giải quyết nhanh 80% thuộc tính thông dụng trước khi chạy thuật toán so khớp Fuzzy Match.
   - Vòng phản hồi (Feedback Loop) tự ghi nhận alias do người dùng sửa đổi trực tiếp vào `semantic_kb.json` để tự động học hỏi.
3. **Bảo Mật Chống Dịch Ngược (Cython Compiler)**:
   - Biên dịch các module nghiệp vụ cốt lõi (`semantic_mapper.py`, `viewer_3d_web.py`) thành các file nhị phân `.pyd` dạng mã máy bằng compiler LLVM-MinGW (Clang).
   - Loại bỏ hoàn toàn mã nguồn gốc `.py` trong gói cài đặt cuối cùng để ngăn chặn giải nén và dịch ngược.
4. **Trình Xem 3D WebGL Siêu Tốc (Three.js)**:
   - Tích hợp Engine hiển thị 3D Three.js nhẹ nhàng và tối ưu, đọc trực tiếp dữ liệu `.glb` nguyên bản đã cache. Loại bỏ hoàn toàn giới hạn bộ nhớ và rào cản bảo mật CORS/WASM trên trình duyệt nhúng.
   - Định vị trực quan cấu kiện thiếu LOI bằng bản đồ nhiệt (Heatmap) màu sắc.
   - Tích hợp tính năng tự động tìm tâm xoay (Orbit) và điểm thu phóng (Zoom) bám sát theo vị trí con trỏ chuột trên mô hình, đem lại trải nghiệm tương tác trực quan cao.
   - **Tích hợp Camera AI Auto-Reporting**: Tự động nhận diện tối đa 50 cấu kiện lỗi LOI, tự động dịch chuyển góc quay (Orbit) để chụp nháy 50 bức ảnh cận cảnh và tạo báo cáo HTML chỉ trong vài giây.
5. **Bộ biểu mẫu tuân thủ QĐ 333/QĐ-CĐBVN & Smart Evaluation**:
   - Xuất báo cáo Excel đáp ứng đầy đủ quy định tiếp nhận bàn giao mô hình BIM của Cục Đường bộ Việt Nam (Biểu mẫu BIM-01-QT-TN, BIM-02-QT-BS, BIM-03-QT-QLKT).
   - **Công nghệ dò quét hồ sơ theo cây thư mục (Relative Path Scanning)**: Nhận diện thông minh các file hồ sơ dựa trên tên thư mục chứa nó (VD: `Bao_Cao/tailieu.pdf`), tự động trích xuất tên file tìm thấy làm bằng chứng (Evidence) ghi thẳng vào báo cáo Excel thay vì đánh giá chung chung.
   - Tích hợp kiểm định tự động kích thước file mô hình dưới **500MB** theo quy định bàn giao.
6. **Giao Diện Tương Tác Một Chạm & Chống Clipping (DPI-Aware UI)**:
   - Đồng bộ Icon ứng dụng trên tất cả cửa sổ con của hệ thống (`_open_email_config`, `_open_contact_window`, `_show_ifc_selector`).
   - Cửa sổ "Liên hệ tác giả" hỗ trợ nhấp chuột sao chép nhanh Zalo/Email và mở liên kết trực tiếp Youtube/TikTok.
   - Bố cục chú thích biểu đồ tròn (Pie Chart Legend) sử dụng Label của CustomTkinter để đảm bảo hiển thị sắc nét 100% các chỉ số Đạt/Lỗi dưới mọi cấu hình tỷ lệ DPI trên Windows.
7. **Bộ chuyển đổi giao diện động (Theme Switcher) & Step Indicator**:
   - Công tắc chuyển đổi tức thời giữa giao diện sáng và tối (`Light/Dark mode`) tại sidebar, tự động đồng bộ bảng màu cho Console và biểu đồ Matplotlib thời gian thực.
   - Thanh chỉ số tiến trình (Step Progress Indicator) hiển thị trực quan các bước chạy bằng đèn LED Emerald White nổi bật, nâng tầm mỹ thuật UI.
8. **Công nghệ co giãn thời gian xử lý động (Dynamic Timeout Scaling)**:
   - Loại bỏ lỗi crash timeout 10 phút cũ cho các đại dự án hạ tầng bằng cách tự động nhân rộng thời gian chờ của `IfcConvert` theo công thức: $\text{timeout} = \max(600, \text{dung lượng file (MB)} \times 3)$ (Ví dụ: File 1.2GB tự động được cấp hạn mức 1 giờ xử lý).
9. **Kiểm định LOI theo IFC Class và LOD Giai đoạn**:
   - Tích hợp bộ chọn ComboBox cho phép kiểm định linh hoạt thuộc tính theo 3 cấp độ: `LOD 200 (Thiết kế cơ sở)`, `LOD 350 (Thiết kế bản vẽ thi công)` và `LOD 400 (Hoàn công / Quản lý)`.
   - Phân loại tự động bộ môn dựa trên lớp thực thể IFC (`EntityType`) thực tế của cấu kiện, nâng cao độ chính xác 100% theo các quyết định QĐ 333 và QĐ 347/348.
10. **Tối ưu hóa đa tiến trình, đồng bộ băm và SQLite lowercase (Siêu tốc độ & Toàn vẹn)**:
    - Bóc tách IFC đa luồng thực tế bằng `ProcessPoolExecutor` và lược bỏ Quantity Sets (`psets_only=True`), tăng tốc **4x - 8x** trên CPU đa nhân.
    - Đồng bộ tên cột SQLite chữ thường (case-insensitive) và chuẩn hóa kiểu dữ liệu Pandas (boolean/float), loại bỏ giá trị rỗng khỏi hàm băm SHA256 giúp đối soát độ toàn vẹn khớp 100% không lệch.
    - Áp dụng dictionary cache cho mạng nơ-ron MLP AI và tối ưu hóa vòng lặp DataFrame thành records thuần Python, tăng tốc kiểm định LOI lên **50x - 100x** (chạy dưới 2 giây cho đại dự án).
11. **Trích xuất chọn lọc mô hình siêu nặng (>1GB IFC)**:
    - Chỉ dịch lưới hình học của các class IFC chính cần kiểm tra LOI (`--include entities IfcBeam IfcColumn ...`), giảm dung lượng GLB hơn 90% (từ 1.37GB xuống dưới 50MB) giúp WebView/WebGL hiển thị mượt mà không bị sập.
12. **BIM Awake & BIM File Locksmith (Tự động hóa thông minh)**:
    - Giữ Windows thức ngầm (`SetThreadExecutionState` API) khi đang chạy tác vụ bóc tách nặng.
    - Tự động dò quét và cho phép tắt nhanh các tiến trình đang khóa file dự án (`psutil` integration) trực tiếp từ GUI để gỡ lỗi Permission Denied.
13. **Card-based UI & Stepper Component động**:
    - Thiết kế các bước tiến trình dạng Card bo góc 10px viền mỏng thanh lịch.
    - Stepper thông minh cập nhật trạng thái động của 4 bước theo thời gian thực (`Pending` ➡️ `Running` ➡️ `Success` / `Error`).
14. **Phân Hệ Kiểm Định BIM Chuẩn Quốc Tế & Bảo Mật Local-First (ISO 19650 & OpenBIM)**:
    - **Chuẩn OpenBIM IFC4x3 Cho Hạ Tầng Giao Thông**: Hỗ trợ 100% các lớp thực thể Cầu, Đường bộ, Đường sắt, Lý trình và Địa hình (`IfcRoad`, `IfcRailway`, `IfcAlignment`, `IfcBridge`, `IfcGeographicElement`).
    - **Giấy Chứng Nhận Sức Khỏe Mô Hình ISO 19650 (`iso19650_certificate.py`)**: Kết xuất Giấy Chứng Nhận Sức Khỏe Mô Hình BIM đạt chuẩn ISO 19650-2 kèm Mã QR Code và chuỗi băm SHA-256 xác thực pháp lý.
    - **Bóc Tách Khối Lượng Visual QTO ra Biểu Mẫu QĐ-333**: Ánh xạ tự động loại cấu kiện IFC với mã hiệu định mức công tác Bộ GTVT / Cục Đường bộ Việt Nam, xuất thẳng ra sheet `Visual QTO (Khối Lượng)` trong Excel QĐ-333.
    - **Tô Màu 3D Đối Soát Mô Hình (3D Version Comparer)**: Phân loại đối soát 2 phiên bản IFC bằng DuckDB vectorized SQL siêu tốc và tô màu vật liệu 3D (🔴 Xóa, 🟢 Thêm mới, 🟡 Sửa đổi).
    - **Phát Hành File BCFzip Chuẩn buildingSMART**: Tự động đóng gói gói tệp `.bcfzip` (`LOI_Issues_*.bcfzip`) chứa `markup.bcf`, `viewpoint.bcf` và ảnh Base64 3D snapshot để giao việc trực tiếp trên Solibri, Navisworks, Revit.
    - **Nén Tối Ưu File IFC Siêu Nặng (Model Sanitizer)**: Lọc nén cốt thép/bu-lông giảm 70-90% dung lượng tệp IFC lớn >1GB, chống sập RAM và triệt tiêu lỗi timeout.
    - **Bảo Mật Local-First 100% (Air-Gapped)**: Thiết kế chuyên biệt cho dự án Mật/An ninh/Quốc phòng. Rút dây mạng LAN/Wi-Fi vẫn chạy trọn vẹn 100% tác vụ, mã hóa CSDL SQLite Vault AES-256 và đóng dấu chìm Watermark Kỹ sư / IP Nội bộ chống lọt lộ file.
15. **Kiểm định LOI chuẩn hóa IDS (buildingSMART) qua IfcTester**:
    - Tự động chuyển dịch quy tắc từ `loi_config.json` thành tệp XML IDS (Information Delivery Specification) chuẩn quốc tế.
    - Sử dụng engine `ifctester` để validate trực tiếp file IFC gốc, nâng cao độ chính xác 100%, loại bỏ lỗi báo thuộc tính chéo giữa các lớp thực thể.
    - Xuất báo cáo chi tiết IDS HTML và tệp lỗi BCFzip chuẩn hóa, đảm bảo tương thích ngược 100% với Step 4 (Excel) và Step 5 (3D Viewer).
16. **Tối ưu hóa CDE đọc nhanh IFC Schema bằng Regex**:
    - Đọc nhanh Header (1MB đầu tiên) của tệp IFC dạng văn bản STEP và dùng Regex để tìm `FILE_SCHEMA`.
    - Trích xuất chính xác schema (`IFC2X3`, `IFC4`) trong vòng dưới 1ms, tự động điền vào cột "Phiên bản Phần mềm" của Excel thống kê Step 1.
    - Hoàn toàn không tải hình học, không tốn RAM và không phụ thuộc thư viện ngoài khi đóng gói EXE.
17. **Trình Chỉnh Sửa Cấu Hình LOI & Alias Thuộc Tính**:
    - Tích hợp giao diện quản lý cấu hình tập trung cho quy tắc kiểm định LOI (`loi_config.json`) và aliases từ điển (`semantic_kb.json`).
    - Hỗ trợ cơ chế Undo/Redo bằng các nút bấm phục hồi lịch sử trạng thái thay đổi.
    - Cơ chế Auto-save tự động lưu ngầm các chỉnh sửa lên ổ đĩa mỗi 10 giây nếu có biến động, hiển thị trạng thái đồng bộ real-time.
18. **Đối Soát Thay Đổi Phiên Bản Mô Hình (BIM Version Comparer)**:
    - Module `DbComparer` thực hiện so sánh đối chiếu giữa 2 tệp cơ sở dữ liệu SQLite hoặc 2 tệp mô hình trong cùng một cơ sở dữ liệu.
    - Phát hiện các cấu kiện bị sửa đổi (volume, metadata), thêm mới, hoặc bị xóa dựa trên GUID cấu kiện và đối soát mã băm `sha256_hash` thuộc tính.
    - **Tối ưu hóa DuckDB**: Tích hợp công cụ DuckDB in-memory xử lý so khớp cấu kiện (vectorized table join) trực tiếp trên SQLite, nâng cao hiệu năng tốc độ xử lý 50x và triệt tiêu lỗi deadlock/lock file database.
    - Trích xuất chênh lệch thể tích ròng và xuất báo cáo HTML đối soát trực quan, chuyên nghiệp.
19. **Chuẩn hóa Chẩn đoán AI bằng Instructor & Pydantic**:
    - Sử dụng thư viện `instructor` định hình phản hồi từ LLM thành cấu trúc dữ liệu Pydantic sạch sẽ, chia tách rõ ràng: tóm tắt lỗi, tác động kỹ thuật, khuyến nghị hành động, mức độ nghiêm trọng.
    - Hỗ trợ cơ chế Offline Fallback tự sinh dữ liệu chẩn đoán nội bộ khi chạy ngoại tuyến 100%.
20. **Trình Tự Động Vá Lỗi Thuộc Tính IFC (BIMAutoFixer)**:
    - Tích hợp giao diện `"🪄 TỰ ĐỘNG VÁ LỖI IFC"` tại Công cụ chuyên gia của Sidebar.
    - Sử dụng `ifcopenshell` tự động chèn và cập nhật các tham số thiếu trực tiếp vào file IFC gốc theo chuẩn buildingSMART (như `Pset_BeamCommon` cho cấu kiện dầm).
    - Bảo mật an toàn dữ liệu: Tự động nhân bản mô hình và tạo tệp sao lưu phòng ngừa (`*_backup.ifc`) tại thư mục nguồn trước khi sửa đổi.
21. **Lọc Làm Sạch Mô Hình IFC (IFC Sanitizer)**:
    - Sử dụng `ifcopenshell` lọc bỏ cốt thép (`IfcReinforcingBar`), bu-lông (`IfcFastener`) và cáp dự ứng lực (`IfcTendon`) để giảm dung lượng file xuống dưới 500MB theo Quyết định 333 của Cục Đường bộ.
    - Tự động sao lưu file gốc và hiển thị tỷ lệ phần trăm dung lượng giảm trước/sau khi lọc.
22. **Saved Viewpoints & Lerp Camera (Trình xem 3D)**:
    - Cho phép lưu góc camera 3D kèm cấu kiện highlight, hiển thị dưới dạng danh sách tương tác.
    - Click vào góc nhìn sẽ dịch chuyển camera mượt mà (smooth transition) bằng thuật toán nội suy tuyến tính (Lerping) tự viết trong vòng lặp render Front-end.
23. **Smart Filters & BCF Reader cục bộ**:
    - Lọc cô lập cấu kiện động qua SQLite backend bằng các câu truy vấn động parameterized và đọc trực tiếp tệp `.bcfzip` cục bộ, tự động zoom 3D đến cấu kiện lỗi.
24. **Interactive BI Dashboard (Chart.js)**:
    - Tích hợp biểu đồ tròn và cột của Chart.js trực quan hóa chỉ số đạt/lỗi LOI và lỗi theo loại cấu kiện.
    - Click vào cột biểu đồ tự động lọc Model Tree tương ứng để khoanh vùng cấu kiện lỗi.
25. **Property Excel Template Manager**:
    - Tự động sinh file Excel mẫu có cấu trúc cột chuẩn LOI theo từng bộ môn/loại cấu kiện và Import ngược để vá thuộc tính hàng loạt vào file IFC gốc.
26. **Công Nghệ Mở Mô Hình 3D IFC Siêu Nhanh (<1s) Theo Cơ Chế BIMvision**:
    - **Progressive Tiered Streaming**: Tự động sinh tệp `Tier 1 GLB` (~2MB) chứa khung kết cấu chính (`IfcBeam`, `IfcColumn`, `IfcSlab`, `IfcWall`, `IfcPile`, `IfcBridge`...) giúp Three.js nạp và vẽ 3D lên màn hình trong **< 0.5 giây**, cho phép xoay/pan/zoom ngay lập tức. Sau đó luồng ngầm tự động nạp tiếp tệp `Full Model` mượt mà.
    - **Tối ưu đa luồng CPU & Memory Mapping `mmap`**: Sử dụng `IfcConvert` đa luồng (`--threads N`), tối ưu lưới (`--deflection 0.05`, `--weld-vertices`) kết hợp Memory Mapping (`mmap`) phục vụ bộ đệm nhị phân từ đĩa SSD LocalAppData (`%LOCALAPPDATA%\BIM_QAQC\glb_cache\`) đạt tốc độ truyền tải GB/giây.
    - **Đồng bộ chỉ số & Chuẩn hóa tên file xuất báo cáo (`01_` đến `04_`)**: Đồng bộ 100% chỉ số LOI giữa Màn hình chính và 3D View Window, đánh số tiền tố chuyên nghiệp cho toàn bộ tệp báo cáo đầu ra (`01_CDE_*`, `02_YBIM_*`, `03_BaoCao_*`, `04_BieuMau_*`) và thu gom tệp đệm nội bộ vào thư mục `_cache/`.

---

## 🛠️ Quy Trình 5 Bước Phân Tích

* **Bước 1: Kiểm tra CDE**: Kiểm định quy chuẩn đặt tên file theo ISO 19650, đọc nhanh phiên bản IFC Schema bằng Regex dưới 1ms và kiểm tra tiến độ đồng bộ hóa trên các dịch vụ đám mây (OneDrive, Autodesk Construction Cloud - ACC).
* **Bước 2: Trích xuất IFC**: Đọc file IFC, bóc tách toàn bộ thuộc tính cấu kiện lưu trữ tập trung vào cơ sở dữ liệu SQLite Hub (`YBIM_Project_Data.db`). Kích hoạt Caching SHA-256 bỏ qua các file không đổi.
* **Bước 3: Kiểm tra LOI**: Đánh giá mức độ chi tiết thông tin (Level of Information - LOI) của từng cấu kiện bằng việc tự động dịch cấu hình thành chuẩn XML IDS, chạy kiểm định qua engine `ifctester` chuẩn quốc tế của buildingSMART. Trích xuất lỗi, xuất báo cáo chi tiết IDS HTML, file lỗi BCFzip và map ngược lại cấu trúc cũ để đảm bảo tương thích.
* **Bước 4: Xuất báo cáo**: Xuất báo cáo Excel động tuân thủ QĐ 333/QĐ-CĐBVN và báo cáo tổng quan dạng HTML Dashboard chuyên nghiệp.
* **Bước 5: Trình xem 3D**: Khởi chạy cổng API nội bộ và trình duyệt web nhúng để xem chi tiết cấu kiện, hiển thị bản đồ nhiệt (Heatmap) thể hiện cấu kiện Đạt/Không đạt LOI.

---

## 💻 Hướng Dẫn Cài Đặt Cho Nhà Phát Triển

### Yêu cầu hệ thống:

* Python 3.12 (hoặc tương thích)
* Hệ điều hành Windows 10/11
* Trình biên dịch C++ (MinGW hoặc Clang) có sẵn trong biến môi trường `PATH` để biên dịch Cython.
* Bộ cài đặt Inno Setup 6 (để biên dịch Setup Wizard đóng gói tự động).

### Cài đặt thư viện:

* Cài đặt đầy đủ các gói thư viện Python yêu cầu bằng lệnh:
  ```bash
  pip install -r requirements.txt
  ```

### Chạy ứng dụng từ mã nguồn:

```bash
# Thiết lập mã hóa UTF-8 cho dòng lệnh (tránh lỗi font tiếng Việt trên Windows)
$env:PYTHONIOENCODING="utf-8"

# Khởi chạy giao diện chính
python BIM_QAQC_App_UI.py
```

### Biên dịch và đóng gói bộ cài đặt Setup:

Chạy file batch đóng gói tự động:

```cmd
build.bat
```

Tại màn hình lựa chọn, nhập:

* **`1` (Quick Build)**: Để đóng gói nhanh dạng thư mục `onedir` phục vụ chạy thử.
* **`2` (Full Release)**: Để biên dịch các module Cython bảo mật (.pyd), đóng gói PyInstaller, và tự động gọi Inno Setup tạo file cài đặt chuyên nghiệp `Output/BIM_QAQC_Setup.exe`.

---

*Bản quyền v0.1.0 © 2026 Đội ngũ Phát triển BIM QAQC - Bảo lưu mọi quyền.*
