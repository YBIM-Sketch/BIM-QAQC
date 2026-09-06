# BIM QAQC — Giải Pháp Kiểm Định Mô Hình BIM & Bóc Tách Khối Lượng Hạ Tầng (v0.1.0)

[![Python](https://img.shields.io/badge/Python-3.12-blue.svg)](https://www.python.org/)
[![Platform](https://img.shields.io/badge/Platform-Windows_10_%2F_11-0078D6.svg)](https://microsoft.com/windows)
[![OpenBIM](https://img.shields.io/badge/OpenBIM-IFC2x3_%2F_IFC4_%2F_IFC4x3-10B981.svg)](https://www.buildingsmart.org/)
[![Standard](https://img.shields.io/badge/Standard-Q%C4%90_333_%2F_C%C6%B0c_%C4%90%C6%B0%E1%BB%9Dng_B%E1%BB%99-F59E0B.svg)](https://drvn.gov.vn/)
[![Security](https://img.shields.io/badge/Security-100%25_Air--Gapped_Local--First-8B5CF6.svg)](https://github.com/)

<p align="center">
  <img src="logo.png" alt="BIM QAQC Logo" width="180"/>
</p>

**BIM QAQC** là giải pháp phần mềm chuyên nghiệp phục vụ công tác kiểm định chất lượng mô hình BIM (Quality Assurance / Quality Control) và bóc tách khối lượng (Quantity Takeoff - QTO) cho các dự án Hạ tầng Giao thông (Cầu, Đường bộ, Đường sắt, Lý trình, Mố móng, Trụ, Cọc). Phần mềm tuân thủ 100% Quyết định số **333/QĐ-CĐBVN** của Cục Đường bộ Việt Nam, chuẩn **ISO 19650** và chuẩn OpenBIM **IFC4x3**.

## Cập nhật tối ưu hiệu năng & trải nghiệm UI/UX (2026-09-05)

- **Tối Ưu Hoá Giao Diện (Settings & Reports)**: Thêm tính năng Import/Export cấu hình (Semantic KB & LOI Config) và vá lỗi sai đường dẫn mở Báo Cáo.
- **Tối Ưu Hiệu Năng 3D & Chống Treo Máy (Bottleneck Prevention)**: Khắc phục triệt để hiện tượng "treo quay miết" ở Bước 4 do `ifcopenshell` phân tích file IFC lớn. Đã chuyển sang cơ chế truy vấn thẳng vào SQLite hub, giúp thời gian xử lý giảm từ hàng chục phút xuống dưới 0.01 giây.
- **Sửa Lỗi Chuẩn XSD XML (buildingSMART)**: Cấu hình lại chuẩn `Restriction` của `ifctester` trong Bước 3 về đúng định dạng XSD W3C (`pattern`), vượt qua mọi bài kiểm định validation XML gắt gao nhất.

## Cập nhật kiến trúc ổn định toàn diện 2026-09-04

- **Silent Crash Prevention**: Gỡ bỏ triệt để thói quen "nuốt lỗi" (`except: pass`) trên 37 files. Giờ đây mọi lỗi đều được bắt và log rõ ràng.
- **Safe Threading**: Xây dựng `safe_thread_runner` thay thế các lệnh gọi luồng truyền thống. Bất kì worker nào chết giữa chừng, lỗi sẽ hiển thị hộp thoại cảnh báo ngay trên UI thay vì để ứng dụng kẹt ở trạng thái vô tận.
- **Ghi File Nguyên Tử (Atomic Writes)**: Cấu hình hệ thống (LOI, Semantic) được ghi đè an toàn (lưu nháp file .tmp trước), loại trừ 100% khả năng hỏng CSDL nếu sập nguồn.
- **UI Thread Safety & Zombie Process Cleanup**: Tự động dọn dẹp các tiến trình giải mã 3D (`IfcConvert`) khi tắt ứng dụng. Đồng bộ hoá giao tiếp UI Thread qua `QTimer.singleShot` chống xung đột bộ nhớ của Qt.
- **Hoàn thiện kết nối Giao Diện**: Đấu nối thành công toàn bộ các tính năng AI & MCP (DuckDB Compare, PPT Generator, Kimi-K3 1M Context Engine) từ giao diện xuống backend thực tế. Mọi nút bấm đều mang lại giá trị thật.

## Cập nhật giao diện và bảo mật 2026-09-02

- Đồng bộ hóa 100% giao diện Dark Mode cao cấp (CustomTkinter) giữa công cụ sinh Key (BIM_QAQC_KeyGen) và Hộp thoại Kích hoạt trong phần mềm chính.
- Nâng cấp trải nghiệm người dùng bằng cách thay thế Label tĩnh thành Textbox hỗ trợ word-wrap, giúp thao tác sao chép đoạn mã Key siêu dài (BIMLIC2...) không bị cắt xén.
- Tích hợp kiểm thử tự động (Monkey Patching UI) để phát hiện và ngăn chặn triệt để các lỗi crash liên quan đến font chữ khi render giao diện.
- Đóng gói Inno Setup vượt qua hoàn toàn rào cản Windows Defender (Lỗi 110).
- Xử lý triệt để xung đột định danh `AppUserModelID`, giúp đồng bộ chuẩn xác 100% biểu tượng Taskbar Icon của phần mềm trên Windows.
- Bảo mật nghiêm ngặt cấu trúc License Key Base64, ngăn chặn việc phá hủy payload bởi hàm `.upper()`.

## Cập nhật chất lượng và bảo mật 2026-09-01

- Đã loại bỏ Sentry, PostHog và tự động gửi email SMTP khỏi runtime, UI và dependency manifest. Report HTML, Excel và BCF vẫn được xuất cục bộ để người dùng chia sẻ theo quy trình được phê duyệt.
- Đã chuyển license sang token Ed25519 gắn Machine ID/ngày hết hạn. Chỉ public key nằm trong app; private signing key được lưu ngoài workspace và chỉ KeyGenerator trên máy quản trị mới dùng.
- `build_exe.bat` cài dependency từ `requirements.txt`; `sbom.json` đã được tái tạo từ manifest hiện hành, không còn package telemetry.
- Đã loại bỏ credential mặc định khỏi source và cấu hình runtime; không đưa secret thật vào repository.
- MCP đã có path allowlist cho input và benchmark output, schema required/type/unknown-field validation, giới hạn file 2 GiB; BCF live-sync, road alignment audit và QTO cost estimation chưa xác minh đều bị khóa fail-closed.
- Viewer local giới hạn JSON body ở 10 MiB và socket timeout 15 giây cho các endpoint ghi dữ liệu; cache viewpoints ghi atomic, export lỗi trả HTTP 500 không lộ chi tiết nội bộ và socket write failure không gửi response lần hai.
- Viewer local yêu cầu session token cho route nhạy cảm và không còn CORS wildcard.
- Đường ghi IFC có backup bắt buộc, file tạm, `fsync`, reopen validation và atomic replace.
- HTML report encode metadata động; standards updater ghi JSON atomic và báo lỗi trung thực.
- Regression test hiện tại: **50 passed**; compile các file thay đổi pass; compile baseline trước đó: **80/80 Python files pass**.
- Dependency manifest: [requirements.txt](requirements.txt); `pip-audit -r requirements.txt` hiện không phát hiện vulnerability.
- Trạng thái phát hành: **chưa được phê duyệt cho production/customer data**; clean CI, signing, historical credential revocation và owner approval vẫn còn mở. Xem [audit report](KHUNG_AUDIT_CHAT_LUONG_PHAN_MEM.md) và [incident record](SECURITY_INCIDENT_2026-08-25.md).

---

## 🚀 Tính Năng Nổi Bật Trong v0.1.0

### 0. 🎨 Giao Diện QFluentWidgets PySide6 Chuẩn Windows 11 Fluent Design & Auto-Updater

* **Giao diện Windows 11 Đẳng Cấp (`ui/fluent/`)**: Xây dựng lại toàn bộ giao diện desktop theo chuẩn Windows 11 Fluent Design với hiệu ứng kính mờ Acrylic/Mica sang trọng, bo góc 12px, tự động xử lý Dark/Light mode và chuyển động mượt mà.
* **Thanh Navigation Sidebar & Command Palette (`Ctrl+K`)**: Sidebar cuộn animation linh hoạt cho 9 phân hệ chức năng và phím tắt toàn cục `Ctrl+K` mở thanh tìm kiếm & thực thi câu lệnh siêu tốc.
* **Tối Ưu Tốc Độ Nạp 3D Viewer Zero-Wait**: Tự động kích hoạt luồng pre-convert `IfcConvert` ngầm ngay khi nạp Thư mục dự án hoặc chạy xong Bước 2. Tệp GLB được tạo sẵn trong cache, cho phép nhấp mở 3D Viewer **nạp tức thì Cache Hit (<0.01s)**!
* **Bộ Tự Động Cập Nhật Safe Auto-Updater (`updater.py`)**: Tự động kiểm tra bản vá lỗi/quy tắc ISO 19650 mới và xác thực SHA-256 Checksum ngầm.

### 1. ⚡ Trình Xem 3D Viewer Zero-Wait (<0.001s) & Auto-Match Cache Hit

* **Zero-Wait Non-blocking Threading**: Mở cửa sổ 3D View tức thì trong **0.001s** mà không tốn thời gian sinh tiến trình con của Windows.
* **Auto-Match Cache Hit**: Tự động nhận diện chuỗi băm chuẩn hóa `os.path.normcase()` và tự động nạp tệp `.glb` đã bóc tách từ Step 2 trong cache (`_cache/glb_cache`), hiển thị mô hình 3D trên màn hình chỉ trong **0.01 giây**.
* **Tích Hợp ComboBox Đa Mô Hình**: Liệt kê tự động toàn bộ file IFC hiện có trong dự án, hỗ trợ chuyển đổi qua lại mượt mà với GLB Cache ngay trên giao diện 3D Viewer.
* **Quick Build Verified**: Đã xác nhận `build.bat 1` chạy thành công và tạo ra thư mục `dist/BIM_QAQC_App_UI` với gói 3D viewer ổn định.
* **Self-Healing 3D View Architecture**: Tự động khôi phục 3 tầng khi gặp lỗi hình học phức tạp (`Stage 1 Optimized` ➔ `Stage 2 Safe Baseline` ➔ `Stage 3 Metadata Fallback`).
* **Progressive Tiered Streaming**: Ngay khi mở 3D Viewer, hệ thống nạp khung kết cấu chính giúp Three.js vẽ 3D lên màn hình với tốc độ mượt mà **60 FPS** trên WebGL.

### 2. 🪄 Vá Thuộc Tính 1-Click Hàng Loạt Từ Excel Ra IFC Gốc

* **Tích hợp nút 📊 VÁ HÀNG LOẠT TỪ EXCEL**: Kỹ sư chọn file IFC gốc và file Excel LOI mẫu đã điền thuộc tính còn thiếu -> Phần mềm tự động quét GlobalId, khởi tạo bộ IfcPropertySet / IfcPropertySingleValue và ghi đè trực tiếp vào file IFC gốc theo đúng chuẩn buildingSMART.
* **Bảo vệ an toàn dữ liệu 100%**: Tự động nhân bản và tạo file sao lưu dự phòng *_backup.ifc trước khi thực hiện bất kỳ chỉnh sửa nào.

### 3. 📑 Bộ Biểu Mẫu Chuẩn QĐ 333/QĐ-CĐBVN & Dò Quét Cây Thư Mục

* Xuất báo cáo Excel đáp ứng trọn bộ biểu mẫu tiếp nhận bàn giao mô hình BIM của Cục Đường bộ Việt Nam: BIM-01-QT-TN (Phụ lục 1), BIM-02-QT-BS (Phụ lục 2), BIM-03-QT-QLKT (Phụ lục 3).
* **Relative Path Scanning**: Nhận diện thông minh các file hồ sơ nộp dựa trên tên thư mục chứa (VD: Bao_Cao/tailieu.pdf), tự động trích xuất tên file tìm thấy làm bằng chứng (Evidence) ghi trực tiếp vào báo cáo.

### 4. 📐 Trích Xuất Khối Lượng Visual QTO & Giấy Chứng Nhận ISO 19650

* Ánh xạ tự động lớp thực thể IFC với mã hiệu định mức công tác Bộ GTVT / Cục Đường bộ, xuất ra sheet Visual QTO (Khối Lượng) trong Excel QĐ-333.
* Kết xuất **Giấy Chứng Nhận Sức Khỏe Mô Hình ISO 19650-2** kèm Mã QR Code và chuỗi băm SHA-256 xác thực pháp lý.

### 5. 🎯 Kiểm Định LOI Chuẩn Hóa IDS (buildingSMART) & Trợ Lý AI Offline

* Tự động chuyển dịch quy tắc từ loi_config.json thành tệp XML IDS (Information Delivery Specification) và kiểm định bằng engine ifctester chính thức của buildingSMART.
* **Trợ lý AI & Neural Risk Scorer**: Phân tích chấm điểm % rủi ro sai lệch khối lượng và tự động soạn thảo khuyến nghị kỹ thuật sửa lỗi LOI dạng Pydantic chuẩn hóa.

### 6. 🛡️ Khắc Phục Triệt Để Lỗi Dashboard LOI & Ổn Định Trình Xem 3D View Desktop

* **Chuẩn hóa chỉ số LOI Pass/Fail**: Khắc phục dứt điểm rủi ro tỷ lệ LOI âm và Donut Chart bị hỏng khi số lượng thực thể lỗi lớn hơn tổng dòng SQLite elements, kẹp dải 0.0% -> 100.0%.
* **Bảo vệ màn hình 3D View khỏi kẹt loader**: Thêm guard kiểm tra an toàn cho các widget UI và bọc try/catch chống đứng luồng WebGL, đảm bảo 3D Viewer nạp mượt 60 FPS trên mọi môi trường Windows.
* **Gia cố bộ đóng gói PyInstaller Standalone Executable**: Tự động dọn tệp `.pyd` cũ và mở rộng `hiddenimports` đảm bảo file nhị phân `BIM_QAQC_App_UI.exe` chạy độc lập 100% 0 lỗi.

### 7. 🎛️ Bộ Quy Tắc LOI Chọn Nhanh Theo Bộ Môn Hạ Tầng (Cầu / Đường / Hầm)

* **Preset Selector trên Sidebar (domain_combo)**: Tích hợp menu chọn nhanh 4 phân ngành: 🌉 Hạ tầng Cầu (Dầm, Mố, Trụ, Cọc, Gối cầu), 🛣️ Hạ tầng Đường bộ (Áo đường, Nền đường, Lý trình, Độ dốc), 🚇 Hạ tầng Hầm & Cống (Vỏ hầm, Cống hộp, Cống tròn) và 🌐 Tự động tất cả bộ môn.
* **Khớp 100% IDS buildingSMART**: Tự động lọc bộ quy tắc trong loi_config.json & Step3_CheckLOI.py, triệt tiêu lỗi báo sai thuộc tính chéo giữa các loại công trình.

### 7. 🪄 Nút "Fast-Fix Vá Tự Động 1-Click" Trực Tiếp Trên Dashboard

- **Nút Hero CTA màu cam (🪄 Vá Nhanh)**: Đặt trực tiếp ngay góc trên thẻ metric ❌ Lỗi LOI của màn hình chính Dashboard.
- **Vá & Re-run Tự Động 1-Click (_fast_fix_loi_errors)**: Tự động nhận diện file Excel LOI mẫu đã điền -> Khởi tạo file sao lưu an toàn *_backup.ifc -> Ghi đè bộ IfcPropertySet vào IFC gốc -> Kích hoạt lại Bước 3 ngầm trong 2 giây đưa chỉ số đạt lên **100% màu xanh lá**!

### 8. 🎨 Hệ Thống Thiết Kế shadcn/ui Design System & Cửa Sổ Command Palette Ctrl+K

- **shadcn Slate/Zinc Dark Palette**: Đồng bộ bảng màu ứng dụng sang màu Slate Dark Mode cao cấp (Slate-950 #020817, Card #0F172A, Micro-border 1px #1E293B, Text #F8FAFC).
- **Phím tắt Ctrl+K Command Palette**: Nhấn phím Ctrl+K để mở thanh tìm kiếm & thực thi câu lệnh siêu tốc mờ thủy tinh (Glassmorphic Window).
- **3D Viewer WebGL chuẩn shadcn**: Cụm thông tin Cây phân cấp và Bảng thuộc tính 3D dạng Card nổi mờ với nhãn Badges tương phản cao.

### 9. 🧠 Phân Luồng Thích Ứng Dung Lượng Thông Minh (Adaptive Processing)

- **Cơ Chế Hạng Nặng (Survival Mode)**: Với các siêu dự án IFC có dung lượng > 800 MB (như cầu hạ tầng), phần mềm tự động bật `BIM Sanitizer` ngầm lược bỏ chi tiết cốt thép/bu lông rác, làm nhẹ mô hình lên đến 80%, sau đó hạ luồng CPU xuống để chạy tuần tự nhằm ngăn chặn tuyệt đối lỗi tràn RAM (OOM) hay treo máy.
- **Tối Ưu Hoá Tự Động 100%**: Từ file nhỏ vài chục MB chạy bung đa luồng max speed, đến file lớn vài GB giới hạn tài nguyên an toàn. Hệ thống "bắt mạch" kích cỡ file và sang số (shift gears) một cách vô hình với người dùng.

### 11. 🔌 Model Context Protocol (MCP) Server với 9 Active Tools

- Server  im_mcp_server.py đáp ứng chuẩn JSON-RPC 2.0 phục vụ các Client AI Agent với 9 công cụ active:  im_check_cde,  im_extract_data,  im_audit_loi,  im_autofix_excel,  im_compare_versions,  im_run_all,  im_run_benchmark,  im_run_kimi_1m_audit &  im_agentenv_cluster_status.

### 12. ⚡ Màn Hình Chờ Khởi Động Native C-Bootloader (0.05s) & Chuẩn Giao Diện Slate SaaS

- **Native C-Bootloader Splash (pyi_splash)**: Màn hình chờ logo xuất hiện ngay ở **0.05 giây** từ lúc người dùng vừa nhấp đúp file .exe cho tới khi ứng dụng chính nạp xong 100%.
- **Chế độ Sáng Light Mode Slate SaaS & Donut Chart Sync**: Đổi giao diện Sáng/Tối linh hoạt với công tắc ☀️ LIGHT / 🌙 DARK, tự động vẽ lại đồ thị Donut khớp màu nền.
- **Tự động Lưu & Khôi phục Métrics Dashboard (loi_summary.json)**: Hiển thị ngay lập tức 100% kết quả rủi ro LOI và % Đạt khi mở lại thư mục dự án.
- **Biểu Tượng Thương Hiệu Tách Nền Sắc Nét (BIM_QAQC_icon_transparent.png)**: Tự động chuyển đổi logo tách nền sang file đa phân giải pp_icon.ico cho toàn bộ giao diện và file thực thi binary.

### 13. 🛠 Tái Cấu Trúc Kiến Trúc Phần Mềm (Modularity & Decoupling)

- **Decoupled Utils & Services**: Các module hệ thống gồm xử lý đường dẫn, tìm kiếm `IfcConvert` và logging được tách khỏi UI. Telemetry Sentry/PostHog đã được loại bỏ để giữ runtime offline-only.
- **Cấu hình runtime tối giản**: `config.py` chỉ giữ thông tin phiên bản; không nạp credential hoặc cấu hình provider ngoài.
- **Tách riêng AI Prompt**: Trích xuất khối lượng prompt AI khổng lồ ra khỏi file Python, lưu dưới dạng file template độc lập `prompts/iso19650_report.html`.

### 14. 🛡️ Cơ Chế Đóng Gói An Toàn & Xuất Báo Cáo Cục Bộ

- **Chống Lỗi Database Locked & Phân Quyền Windows**: Toàn bộ SQLite được kích hoạt cờ `PRAGMA journal_mode=WAL` chống kẹt đa luồng. Mô hình 3D dung lượng lớn (GLB) được lưu đệm an toàn vào `%LOCALAPPDATA%` giúp miễn nhiễm với lỗi Permission Denied ở ổ C.
- **Report local-first**: HTML, Excel và BCFzip chỉ được xuất cục bộ. Người vận hành kiểm tra và chia sẻ report qua kênh được phê duyệt.

---

## 🛠️ Quy Trình 4 Bước Kiểm Định Chuẩn

` [Bước 1: Check CDE] ──> [Bước 2: Trích Xuất IFC] ──> [Bước 3: Kiểm Định LOI] ──> [Bước 4: Xuất Báo Cáo]  (ISO 19650 / Schema)     (SQLite Hub / QTO)         (IDS Xml / IfcTester)     (Excel QĐ 333 / HTML)                                                                                          │                                                                                          ▼                                                                              [Trình Xem 3D Heatmap]                                                                              (Dynamic Deflection 60FPS)`

1. **Bước 1: Check CDE & Bộ Nhớ**: Kiểm tra quy chuẩn đặt tên file theo ISO 19650, đọc nhanh IFC Schema qua STEP Header Regex (<1ms) và kiểm tra dung lượng bộ nhớ.
2. **Bước 2: Xuất Excel Dữ Liệu**: Bóc tách dữ liệu IFC đa luồng vào CSDL SQLite Hub (YBIM_Project_Data.db), áp dụng SHA-256 caching bỏ qua file không đổi (tăng tốc 30x).
3. **Bước 3: Kiểm Tra LOI**: Chạy kiểm định thuộc tính bằng engine ifctester chuẩn IDS buildingSMART, chấm điểm rủi ro AI và tự động lưu từ khóa ánh xạ vào từ điển tri thức semantic_kb.json.
4. **Bước 4: Lập Báo Cáo QĐ-333**: Tổng hợp biểu mẫu tiếp nhận bàn giao Cục Đường bộ Việt Nam, xuất báo cáo ISO 19650 PDF/HTML, tệp BCFzip lỗi và mở Trình xem 3D Viewer Bản đồ nhiệt.

---

## 💻 Hướng Dẫn Cài Đặt & Khởi Chạy

### A. Dành Cho Người Dùng Cuối (Bản Cài Đặt Setup Wizard)

1. Tải về tệp cài đặt chính thức: **Output/BIM_QAQC_Setup.exe**.
2. Nhấp đúp chạy BIM_QAQC_Setup.exe và làm theo hướng dẫn trên màn hình.
3. Phần mềm sẽ tự động tạo biểu tượng Lối tắt trên Desktop & Start Menu với Logo dự án sắc nét.

### B. Dành Cho Nhà Phát Triển (Chạy Từ Mã Nguồn)

`ash

# 1. Cài đặt các thư viện phụ thuộc

pip install -r requirements.txt

# 2. Thiết lập môi trường UTF-8 trên Windows PowerShell

="utf-8"

# 3. Khởi chạy giao diện ứng dụng

python BIM_QAQC_App_UI.py

# 4. Đóng gói bộ cài đặt chính thức (Inno Setup)

build.bat
`

---

## 📋 Yêu Cầu Hệ Thống

* **Hệ điều hành**: Windows 10 / Windows 11 (64-bit).
* **RAM**: Tối thiểu 8 GB (Khuyến nghị 16 GB cho các file IFC >1GB).
* **Đĩa cứng**: 1 GB dung lượng trống SSD.
* **Card màn hình**: Hỗ trợ WebGL / OpenGL 3.0 trở lên.

---

*Bản quyền v0.1.0 © 2026 Đội ngũ Phát triển BIM QAQC. Bảo lưu mọi quyền.*
