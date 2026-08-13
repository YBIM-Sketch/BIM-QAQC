# HƯỚNG DẪN SỬ DỤNG PHẦN MỀM BIM QAQC (v0.2.0)

Tài liệu này hướng dẫn chi tiết toàn bộ quy trình vận hành phần mềm **BIM QAQC v0.2.0** phục vụ công tác kiểm định chất lượng mô hình BIM, trích xuất khối lượng QTO, lập bộ hồ sơ tiếp nhận bàn giao theo **Quyết định 333/QĐ-CĐBVN** của Cục Đường bộ Việt Nam, tích hợp Trợ lý Giọng nói AI Offline và Trình xem 3D Viewer Zero-Wait.

---

## 📖 Mục Lục

1. [Khởi Động &amp; Chọn Thư Mục Dự Án](#1-khởi-động--chọn-thư-mục-dự-án)
2. [Bước 1: Kiểm Tra Quy Chuẩn CDE &amp; Sync Đám Mây](#2-bước-1-kiểm-tra-quy-chuẩn-cde--sync-đám-mây)
3. [Bước 2: Bóc Tách Khối Lượng IFC sang SQLite Hub](#3-bước-2-bóc-tách-khối-lượng-ifc-sang-sqlite-hub)
4. [Bước 3: Kiểm Định Mức Độ Thông Tin LOI &amp; Vòng Lặp Tự Học](#4-bước-3-kiểm-định-mức-độ-thông-tin-loi--vòng-lặp-tự-học)
5. [Bước 4: Lập Bộ Hồ Sơ Báo Cáo QĐ-333 &amp; ISO 19650](#5-bước-4-lập-bộ-hồ-sơ-báo-cáo-qđ-333--iso-19650)
6. [Bước 5: Trình Xem 3D Viewer Zero-Wait &amp; Bản Đồ Nhiệt](#6-bước-5-trình-xem-3d-viewer-zero-wait--bản-đồ-nhiệt)
7. [Hướng Dẫn Công Cụ Chuyên Gia &amp; Vá Thuộc Tính 1-Click](#7-hướng-dẫn-công-cụ-chuyên-gia--vá-thuộc-tính-1-click)
8. [Hướng Dẫn Kích Hoạt License &amp; Sử Dụng KeyGenerator](#8-hướng-dẫn-kích-hoạt-license--sử-dụng-keygenerator)
9. [Các Câu Hỏi Thường Gặp (FAQs)](#9-các-câu-hỏi-thường-gặp-faqs)

---

## 1. Khởi Động & Chọn Thư Mục Dự Án

### A. Phương thức 1: Bản Thực Thi Độc Lập Quick Build (Khuyên dùng)

1. **Chạy tệp ứng dụng**: Nhấp đúp vào tệp `dist\BIM_QAQC_App_UI\BIM_QAQC_App_UI.exe`.
2. **Khởi chạy tức thì**: Màn hình chờ C-Bootloader hiển thị logo ngay ở **0.05 giây** và nạp ứng dụng mượt mà.

### B. Chọn Thư Mục Làm Việc (Workspace Folder)

1. Click nút **"📁 Chọn Thư Mục Dự Án"** tại góc trên bên trái màn hình chính.
2. Trỏ tới thư mục chứa các tệp tin mô hình `.ifc` / `.rvt` của dự án.
3. **Cơ chế Zero-Wait**: Ngay khi chọn thư mục, hệ thống tự động kích hoạt luồng dịch 3D ngầm (`Zero Wait Pre-processing`), giúp nạp sẵn dữ liệu 3D mà không làm gián đoạn công việc của bạn.

### C. Màn Hình Chờ Khởi Động Tức Thì (C-Bootloader Native Splash Screen)

1. **Hiển thị tức thì ở miligiây 0.05**: Ngay khi nhấp đúp file `BIM_QAQC_App_UI.exe`, màn hình chờ logo thương hiệu xuất hiện ngay lập tức trong 0.05 giây.
2. **Duy trì hiển thị liên tục**: Màn hình chờ giữ nguyên trong suốt quá trình nạp bộ nhớ CSDL SQLite, xác thực license và khởi tạo giao diện ứng dụng.
3. **Cập nhật tiến trình**: Liên tục báo trạng thái nạp (`⚡ Đang xác thực bản quyền...`, `⚡ Đang dựng giao diện...`).
4. **Tự động đóng mượt mà**: Đóng màn hình chờ và mở cửa sổ chính ngay khi ứng dụng đã nạp xong 100%.

---

## 2. Bước 1: Kiểm Tra Quy Chuẩn CDE & Sync Đám Mây

* **Mục đích**: Đảm bảo toàn bộ danh mục tệp tin mô hình tuân thủ quy chuẩn đặt tên ISO 19650 và kiểm tra trạng thái đồng bộ hóa trên các dịch vụ đám mây (OneDrive, ACC).
* **Các thao tác**:
  1. Nhấp nút **"1. 📁 Check CDE & Bộ Nhớ"** trên Sidebar.
  2. Chọn tiêu chuẩn đặt tên (Mặc định: `ISO 19650`).
  3. Nhấn **"Chạy Kiểm Tra CDE"**.
  4. Hệ thống đọc nhanh Header tệp IFC dạng văn bản STEP (<1ms) để trích xuất `FILE_SCHEMA` (`IFC2X3`, `IFC4`, `IFC4x3`) và hiển thị bảng kê chi tiết trạng thái Đạt/Không đạt.

---

## 3. Bước 2: Bóc Tách Khối Lượng IFC sang SQLite Hub

* **Mục đích**: Bóc tách toàn bộ hình học và dữ liệu thuộc tính từ tệp IFC lưu trữ vào CSDL SQLite Hub (`YBIM_Project_Data.db`) phục vụ truy vấn siêu tốc.
* **Cơ chế Caching SHA-256**:
  * **Lần chạy đầu**: Phân tích đa luồng dữ liệu IFC.
  * **Các lần chạy sau**: Nếu file không bị sửa đổi, hệ thống đọc ngay từ đệm SQLite trong **0.005 giây** (tiết kiệm 100% thời gian).
* **Các thao tác**:
  1. Nhấp chọn **"2. 📊 Xuất Excel Dữ Liệu"**.
  2. Nhấn nút **"Bắt Đầu Trích Xuất Dữ Liệu"**.

---

## 4. Bước 3: Kiểm Định Mức Độ Thông Tin LOI & Vòng Lặp Tự Học

* **Mục đích**: Kiểm định tính đầy đủ của thông tin (Level of Information - LOI) của từng cấu kiện theo giai đoạn thiết kế (`LOD 200`, `LOD 350`, `LOD 400`).
* **Chọn Nhanh Bộ Môn Hạ Tầng (`Domain Preset Selector`)**:
  * Chọn bộ môn đặc thù tại ComboBox **`🎛️ BỘ MÔN HẠ TẦNG (PRESET)`** trên Sidebar (`🌉 Hạ tầng Cầu`, `🛣️ Hạ tầng Đường bộ`, `🚇 Hạ tầng Hầm & Cống`). Hệ thống sẽ tự động áp bộ quy tắc thuộc tính chuẩn 100% cho bộ môn đó.
* **Quy trình chuẩn IDS buildingSMART**:
  1. Phần mềm tự động chuyển dịch quy tắc từ `loi_config.json` thành tệp XML chuẩn IDS (Information Delivery Specification).
  2. Engine `ifctester` chạy kiểm định chính xác 100%, loại bỏ lỗi báo sai thuộc tính chéo giữa các loại cấu kiện.
* **Nút "Fast-Fix Vá Tự Động 1-Click" Ngay Trên Dashboard**:
  * Khi Bước 3 kết thúc và báo lỗi tại thẻ metric **`❌ Lỗi LOI`**, nhấp nút màu cam **`🪄 Vá Nhanh`** trực tiếp trên thẻ.
  * Phần mềm tự nhận diện file Excel mẫu đã điền $\rightarrow$ vá trực tiếp thuộc tính vào IFC gốc (kèm file sao lưu `*_backup.ifc`) $\rightarrow$ tự động chạy lại kiểm định trong 2 giây đưa chỉ số đạt lên 100% màu xanh lá!
* **Trợ Lý AI & Neural Risk Scorer**:
  * Chấm điểm % rủi ro sai lệch khối lượng của từng cấu kiện.
  * Tự động sinh lời khuyên chuyên môn định dạng Pydantic chuẩn hóa.
* **Vòng Lặp Tự Học (Semantic Mapper)**:
  * Khi kỹ sư sửa lỗi tên thuộc tính, hệ thống tự động ghi nhận vào `semantic_kb.json` để tự học và áp dụng cho các dự án sau.

---

## 5. Bước 4: Lập Bộ Hồ Sơ Báo Cáo QĐ-333 & ISO 19650

* **Mục đích**: Xuất trọn bộ biểu mẫu tiếp nhận bàn giao mô hình BIM theo quy định của Cục Đường bộ Việt Nam và Giấy chứng nhận ISO 19650.
* **Bộ Biểu Mẫu Xuất Ra (Thư mục `_BIM_QAQC_Reports_`)**:
  1. **`01_CDE_ThongKeFile_*.xlsx`**: Bảng thống kê tệp tin và dung lượng CDE.
  2. **`02_YBIM_Database_*.db`**: Cơ sở dữ liệu SQLite Hub lưu trữ khối lượng.
  3. **`03_BaoCao_LOI_TongQuan_*.html` & `03_BaoCao_LOI_ChiTiet_*.html`**: Báo cáo Dashboard đồ họa trực quan.
  4. **`03_GiayChungNhan_ISO19650_*.html`**: Giấy chứng nhận sức khỏe mô hình kèm mã QR & Hash SHA-256.
  5. **`03_TepLoi_LOI_Issues_*.bcfzip`**: Tệp báo lỗi BCFzip nhập trực tiếp vào Solibri/Navisworks/Revit.
  6. **`04_BieuMau_NghiemThu_QD333_*.xlsx`**: Trọn bộ biểu mẫu `BIM-01-QT-TN`, `BIM-02-QT-BS`, `BIM-03-QT-QLKT` và sheet `Visual QTO (Khối Lượng)`.

---

## 6. Bước 5: Trình Xem 3D Viewer Siêu Tốc & Bản Đồ Nhiệt

* **Cách mở**: Nhấp nút **"🔍 XEM 3D"** tại màn hình chính hoặc từ Báo cáo HTML.
* **Tốc độ mở siêu tốc (<0.5s)**: Hiển thị ngay tệp `Tier 1 GLB` cho phép xoay/pan/zoom lập tức, sau đó nối tiếp `Full Model` ngầm.
* **Thao tác điều khiển**:
  * **Xoay (Orbit)**: Nhấn giữ **Chuột Trái** (Tâm xoay tự động bám vào vị trí con trỏ).
  * **Trượt (Pan)**: Nhấn giữ **Chuột Phải**.
  * **Thu phóng (Zoom)**: Lăn **Con Cuộn Chuột** (Focus chuẩn xác theo vị trí trỏ chuột).
  * **Định vị nhanh (Focus & Model Tree)**: Nhấp chuột vào bất kỳ cấu kiện nào trong cửa sổ 3D hoặc trên Cây phân cấp (Model Tree) để tự động zoom mượt camera tới cấu kiện đó và hiển thị đầy đủ bảng thuộc tính.
* **Trực quan Bản đồ nhiệt (Heatmap)**:
  * 🟢 **Màu Xanh Lục**: Cấu kiện Đạt 100% LOI.
  * 🔴 **Màu Đỏ**: Cấu kiện Không đạt / Thiếu thuộc tính LOI.

---

## 7. Hướng Dẫn Công Cụ Chuyên Gia & Vá Thuộc Tính 1-Click

### A. Vá Thuộc Tính Hàng Loạt Từ Excel (1-Click Patching)

1. Trên thanh Sidebar, chọn mục **`🛠️ CÔNG CỤ CHUYÊN GIA`** $\rightarrow$ Click nút **`🪄 TỰ ĐỘNG VÁ LỖI IFC`**.
2. Chọn tệp tin IFC gốc cần sửa.
3. Click nút **`📊 VÁ HÀNG LOẠT TỪ EXCEL`** và chọn tệp Excel LOI Mẫu đã được điền thuộc tính.
4. Hệ thống tự động quét GUID, ghi đè thuộc tính vào IFC gốc và tạo bản sao lưu an toàn `*_backup.ifc`.

### B. Lọc Làm Sạch Mô Hình (IFC Sanitizer)

* Click nút **`🧹 LỌC LÀM SẠCH IFC`** để lọc bỏ cốt thép/bu-lông chi tiết, giảm 70-90% dung lượng file siêu nặng.

### C. Đối Soát Phiên Bản Mô Hình (Version Comparer)

* Click nút **`⚖️ SO SÁNH PHIÊN BẢN`** để chạy so khớp thể tích DuckDB giữa 2 phiên bản IFC.

---

## 8. Hướng Dẫn Kích Hoạt License & Sử Dụng KeyGenerator

### A. Kích Hoạt Bản Quyền Trên BIM QAQC (Máy Khách Hàng)

1. Khi khởi chạy ứng dụng, nếu chưa kích hoạt, cửa sổ **BIM QAQC License Manager** sẽ xuất hiện.
2. Sao chép dòng **Machine ID** (dạng `XXXX-XXXX-XXXX`) gửi cho Ban Quản trị / Mr. Y.
3. Nhập **License Key** và **Ngày Hết Hạn** nhận được $\rightarrow$ Click **"Kích Hoạt Bản Quyền"**.


## 9. Các Câu Hỏi Thường Gặp (FAQs)

* **Q: File IFC lớn hơn 1GB có mở được trên trình xem 3D không?**
  * *A: Hoàn toàn mượt mà! Nhờ cơ chế Dynamic Triangulation Deflection tự động điều chỉnh nén lưới đa giác theo dung lượng tệp, phần mềm duy trì tốc độ 60 FPS ổn định trên WebGL.*
* **Q: Dữ liệu dự án có bị gửi lên Cloud không?**
  * *A: Không! BIM QAQC hoạt động 100% Air-Gapped Local-First. Toàn bộ dữ liệu được xử lý nội bộ trên máy tính của bạn, đảm bảo tuyệt đối an toàn cho các dự án Mật/An ninh.*

---

*Bản quyền v0.1.0 © 2026 Đội ngũ Phát triển BIM QAQC.*
