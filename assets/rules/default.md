---
# Quy tắc mặc định nội dự án (Phiên bản an toàn Phase 1)
#
# Đây chỉ đặt ràng buộc mặc định "cơ khí可 kiểm + ít tranh cãi". Ưu tiên thẩm mỹ
# phi cơ khí (như khuynh hướng phong cách) hiện vẫn do writer.md / editor.md
# gánh, chờ Phase 1.5 (sau khi F1 tay test xác nhận sức ràng buộc
# working_memory) mới quyết định có chuyển vào file này.
#
# Người dùng có thể trong ./.ainovel/rules/ hoặc ~/.ainovel/rules/ (bất kỳ .md
# dưới thư mục) ghi đè trường thường; fatigue_words gộp theo từ, cùng một từ
# do nguồn gần hơn ghi đè ngưỡng.
# Xem ngữ nghĩa trường chi tiết tại rules.md.example gốc dự án.

# Khoảng số chữ chương: lệch <20% cảnh báo; ≥20% lỗi.
chapter_words: 3000-6000

# Danh sách đen cụm từ: xuất hiện ≥1 lần即 lỗi. Checker làm khớp con字面,
# không có ký tự đại diện, nên chỉ đặt "cụm cố định định dài" câu cụ AI
# (ít tranh cãi); mẫu含 biến số (như "không phải X mà là Y")
# 字面 khớp không bắt được, về anti-ai-tone.md tầng ngữ nghĩa.
# Gạch ngang "—" trong đối thoại bị ngắt là hợp pháp, có tranh cãi,
# không vào mặc định nội bộ,留给 ./.ainovel/rules/ tự cấu hình.
forbidden_phrases:
  - "một cách nào đó"
  - "đáng chú ý là"
  - "không hiểu vì sao"
  - "ngũ vị tạp trần"

# Giới hạn mềm từ mỏi: commit_chapter sẽ kiểm tra số lần xuất hiện mỗi chương,
# vượt ngưỡng báo warning.
# Đây là từ过度 sử dụng thường见 trong webnovel/tiểu thuyết, anti-ai-tone.md
# có hướng ngữ nghĩa đồng hướng——tín hiệu hai nguồn nhất quán.
# Sáu条 sau (như một/âm默了/không nói/X hơi) từ thực chứng 196 chương chạy dài:
# câu cụ truyền thống đã bị表 phía trên diệt, nhưng mô hình chuyển sang dùng
# các "từ nhịp" này平均 chương 5-7 lần; ngưỡng đặt rộng dung thứ sử dụng bình thường.
fatigue_words:
  không_khỏi: 1
  đột_nhiên: 1
  như_thể: 2
  ngoài_ra: 1
  tuy_nhiên: 2
  một_chút: 2
  một_nét: 2
  một_luồng: 2
  tựa_như: 1
  không_khỏi_phải: 1
  giống_như_một: 3
  im_lặng: 2
  không_nói_gì: 2
  mấy_hơi: 3
  một_hơi: 3
  vài_hơi: 2
---
