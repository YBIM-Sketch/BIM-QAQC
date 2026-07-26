# BIM QAQC — Giải Pháp Kiểm Định Mô Hình BIM & Bóc Tách Khối Lượng Hạ Tầng (v0.1.0)

[![Python](https://img.shields.io/badge/Python-3.12-blue.svg)](https://www.python.org/)
[![Platform](https://img.shields.io/badge/Platform-Windows_10_%2F_11-0078D6.svg)](https://microsoft.com/windows)
[![OpenBIM](https://img.shields.io/badge/OpenBIM-IFC2x3_%2F_IFC4_%2F_IFC4x3-10B981.svg)](https://www.buildingsmart.org/)
[![Standard](https://img.shields.io/badge/Standard-Q%C4%90_333_%2F_C%C6%B0c_%C4%90%C6%B0%E1%BB%9Dng_B%E1%BB%99-F59E0B.svg)](https://drvn.gov.vn/)
[![Security](https://img.shields.io/badge/Security-100%25_Air--Gapped_Local--First-8B5CF6.svg)](https://github.com/)

**BIM QAQC** là giải pháp phần mềm chuyên nghiệp phục vụ công tác kiểm định chất lượng mô hình BIM (Quality Assurance / Quality Control) và bóc tách khối lượng (Quantity Takeoff - QTO) cho các dự án Hạ tầng Giao thông (Cầu, Đường bộ, Đường sắt, Lý trình, Mố móng, Trụ, Cọc). Phần mềm tuân thủ 100% Quyết định số **333/QĐ-CĐBVN** của Cục Đường bộ Việt Nam, chuẩn **ISO 19650** và chuẩn OpenBIM **IFC4x3**.

---

## 🚀 Tính Năng Nổi Bật Trong v0.1.0

### 1. ⚡ Nạp Mô Hình 3D Siêu Tốc (<0.5s) & Co Giãn Đa Giác Động (Dynamic Deflection)

* **Progressive Tiered Streaming**: Ngay khi mở 3D Viewer, hệ thống sinh file `Tier 1 GLB` (~2MB) chứa khung kết cấu chính (`IfcBeam`, `IfcColumn`, `IfcSlab`, `IfcWall`, `IfcPile`, `IfcBridge`...) giúp Three.js nạp và vẽ 3D lên màn hình trong **< 0.5 giây**. Luồng ngầm tự động nạp tiếp tệp `Full Model` phía sau.
* **Dynamic Triangulation Deflection**: Tự động co giãn tham số `--deflection` dựa trên dung lượng file IFC (File >500MB nén đa giác `--deflection 0.1`), giảm 60%-70% số lượng tam giác đa giác mà không giảm chất lượng quan sát, đảm bảo tốc độ mượt mà **60 FPS** trên WebGL.
* **Bộ đệm nhị phân RAM `mmap` Memory Mapping**: Truyền tải dữ liệu nhị phân GLB từ SSD LocalAppData (`%LOCALAPPDATA%\BIM_QAQC\glb_cache\`) trực tiếp vào WebGL socket với tốc độ GB/giây.

### 2. 🪄 Vá Thuộc Tính 1-Click Hàng Loạt Từ Excel Ra IFC Gốc

* **Tích hợp nút `📊 VÁ HÀNG LOẠT TỪ EXCEL`**: Kỹ sư chọn file IFC gốc và file Excel LOI mẫu đã điền thuộc tính còn thiếu $\rightarrow$ Phần mềm tự động quét `GlobalId`, khởi tạo bộ `IfcPropertySet` / `IfcPropertySingleValue` và ghi đè trực tiếp vào file IFC gốc theo đúng chuẩn buildingSMART.
* **Bảo vệ an toàn dữ liệu 100%**: Tự động nhân bản và tạo file sao lưu dự phòng `*_backup.ifc` trước khi thực hiện bất kỳ chỉnh sửa nào.

### 3. 📑 Bộ Biểu Mẫu Chuẩn QĐ 333/QĐ-CĐBVN & Dò Quét Cây Thư Mục

* Xuất báo cáo Excel đáp ứng trọn bộ biểu mẫu tiếp nhận bàn giao mô hình BIM của Cục Đường bộ Việt Nam: `BIM-01-QT-TN` (Phụ lục 1), `BIM-02-QT-BS` (Phụ lục 2), `BIM-03-QT-QLKT` (Phụ lục 3).
* **Relative Path Scanning**: Nhận diện thông minh các file hồ sơ nộp dựa trên tên thư mục chứa (VD: `Bao_Cao/tailieu.pdf`), tự động trích xuất tên file tìm thấy làm bằng chứng (Evidence) ghi trực tiếp vào báo cáo.

### 4. 📐 Trích Xuất Khối Lượng Visual QTO & Giấy Chứng Nhận ISO 19650

* Ánh xạ tự động lớp thực thể IFC với mã hiệu định mức công tác Bộ GTVT / Cục Đường bộ, xuất ra sheet `Visual QTO (Khối Lượng)` trong Excel QĐ-333.
* Kết xuất **Giấy Chứng Nhận Sức Khỏe Mô Hình ISO 19650-2** kèm Mã QR Code và chuỗi băm SHA-256 xác thực pháp lý.

### 5. 🎯 Kiểm Định LOI Chuẩn Hóa IDS (buildingSMART) & Trợ Lý AI Offline

* Tự động chuyển dịch quy tắc từ `loi_config.json` thành tệp XML IDS (Information Delivery Specification) và kiểm định bằng engine `ifctester` chính thức của buildingSMART.
* **Trợ lý AI & Neural Risk Scorer**: Phân tích chấm điểm % rủi ro sai lệch khối lượng và tự động soạn thảo khuyến nghị kỹ thuật sửa lỗi LOI dạng Pydantic chuẩn hóa.

### 6. ⚖️ Đối Soát Phiên Bản Mô Hình Vectorized (DuckDB Version Comparer)

* Module `DbComparer` sử dụng engine DuckDB in-memory xử lý join bảng vectorized SQL so khớp 2 phiên bản IFC (Cũ vs Mới), phân loại màu sắc 3D (🔴 Xóa, 🟢 Thêm mới, 🟡 Sửa đổi) và xuất báo cáo HTML đối soát thể tích chênh lệch.

### 7. 🔒 Bảo Mật 100% Local-First (Air-Gapped) & License Key Manager

* Chạy 100% offline không cần Internet, phù hợp cho các dự án Mật/An ninh/Quốc phòng.
* **Hệ thống cấp bản quyền SHA-256**: Mã hóa Machine ID kết hợp ngày hết hạn. Tích hợp sẵn công cụ **`BIM_QAQC_KeyGen.exe`** cho Ban Quản trị cấp key linh hoạt.

---

## 🛠️ Quy Trình 4 Bước Kiểm Định Chuẩn

```
 [Bước 1: Check CDE] ──> [Bước 2: Trích Xuất IFC] ──> [Bước 3: Kiểm Định LOI] ──> [Bước 4: Xuất Báo Cáo]
 (ISO 19650 / Schema)     (SQLite Hub / QTO)         (IDS Xml / IfcTester)     (Excel QĐ 333 / HTML)
                                                                                         │
                                                                                         ▼
                                                                             [Trình Xem 3D Heatmap]
                                                                             (Dynamic Deflection 60FPS)
```

1. **Bước 1: Check CDE & Bộ Nhớ**: Kiểm tra quy chuẩn đặt tên file theo ISO 19650, đọc nhanh IFC Schema qua STEP Header Regex (<1ms) và kiểm tra dung lượng bộ nhớ.
2. **Bước 2: Xuất Excel Dữ Liệu**: Bóc tách dữ liệu IFC đa luồng vào CSDL SQLite Hub (`YBIM_Project_Data.db`), áp dụng SHA-256 caching bỏ qua file không đổi (tăng tốc 30x).
3. **Bước 3: Kiểm Tra LOI**: Chạy kiểm định thuộc tính bằng engine `ifctester` chuẩn IDS buildingSMART, chấm điểm rủi ro AI và tự động lưu từ khóa ánh xạ vào từ điển tri thức `semantic_kb.json`.
4. **Bước 4: Lập Báo Cáo QĐ-333**: Tổng hợp biểu mẫu tiếp nhận bàn giao Cục Đường bộ Việt Nam, xuất báo cáo ISO 19650 PDF/HTML, tệp BCFzip lỗi và mở Trình xem 3D Viewer Bản đồ nhiệt.

---

## 💻 Hướng Dẫn Cài Đặt & Phát Hành

### A. Dành Cho Người Dùng Cuối (Bản Cài Đặt Setup Wizard)

1. Tải về tệp cài đặt chính thức: **`Output/BIM_QAQC_Setup.exe`**.
2. Nhấp đúp chạy `BIM_QAQC_Setup.exe` và làm theo hướng dẫn trên màn hình.
3. Phần mềm sẽ tự động tạo biểu tượng Lối tắt trên Desktop & Start Menu với Logo dự án sắc nét.

---

## 📋 Yêu Cầu Hệ Thống

* **Hệ điều hành**: Windows 10 / Windows 11 (64-bit).
* **RAM**: Tối thiểu 8 GB (Khuyến nghị 16 GB cho các file IFC >1GB).
* **Đĩa cứng**: 1 GB dung lượng trống SSD.
* **Card màn hình**: Hỗ trợ WebGL / OpenGL 3.0 trở lên.

---

*Bản quyền v0.1.0 © 2026 Đội ngũ Phát triển BIM QAQC. Bảo lưu mọi quyền.*
