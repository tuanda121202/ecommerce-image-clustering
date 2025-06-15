# 🛒 Ecommerce Image Clustering

## Quy trình phát hiện, xử lý và gom nhóm ảnh sản phẩm thương mại điện tử dựa trên sản phẩm có trong hình ảnh.

### 📌 Mục tiêu
Trong các ngành hàng TMĐT như kem chống nắng, sữa rửa mặt, toner..., mỗi sản phẩm có thể được bán bởi nhiều nhà bán, mỗi nhà bán lại chụp ảnh sản phẩm và để ảnh bìa theo cách khác nhau. Mục tiêu của dự án là:
- Tự động tải ảnh sản phẩm từ dữ liệu.
- Phát hiện đối tượng chính trong ảnh (tuýp kem chống nắng, chai xịt khoáng,... ).
- Trích xuất đặc trưng hình ảnh bằng mô hình CLIP.
- Gom nhóm các ảnh tương đồng bằng thuật toán clustering để gom những model sản phẩm giống nhau vào 1 nhóm

### 🔍 Chi tiết các bước
1. Tải ảnh sản phẩm từ URL
- Đọc file Excel chứa product_base_id và url_thumbnail.
- Lưu ảnh về thư mục cục bộ theo ID sản phẩm.
- Nếu không tải được ảnh, ghi lại danh sách lỗi.

2. Dịch các lớp phát hiện sang tiếng Anh
- Sử dụng Google Gemini để dịch danh sách tiếng Việt (ví dụ: ['hộp', 'gói', 'chai']) sang tiếng Anh.
- Sinh thêm các từ đồng nghĩa để tăng hiệu quả phát hiện vật thể.
  
3. Phát hiện đối tượng trong ảnh
- Dùng mô hình YOLOWorld (zero-shot).
- Cắt các vật thể phát hiện được và lưu thành các ảnh riêng.
- Nếu ảnh không phát hiện được vật thể hợp lệ → lưu ảnh gốc.
  
4. Trích xuất đặc trưng ảnh
- Dùng CLIP (ViT-bigG) để sinh vector đại diện cho ảnh.
- Xử lý theo batch và lưu trên GPU (CUDA).

5. Gom nhóm ảnh tương đồng
- Dùng DBSCAN (cuml) trên vector CLIP.
- Điều chỉnh eps nếu gom nhóm quá rời hoặc quá chặt.
- Ảnh bị gom vào cluster = -1 được coi là nhiễu và loại bỏ.
  
6. Gắn nhãn gom nhóm trở lại dữ liệu
- Gán cluster_id cho từng product_base_id trong file Excel.
- Lưu file đầu ra kèm nhãn cluster để phục vụ phân tích, đánh giá.
