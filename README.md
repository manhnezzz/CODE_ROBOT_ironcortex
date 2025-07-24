# 🤖 IRONCORTEX – THPT LÊ HỒNG PHONG

## FPTU AI & ROBOTICS CHALLENGE 2025  
**Chủ đề: Robot Nông Nghiệp Bền Vững**

---

### 🧠 Giới thiệu

Đây là repo mã nguồn phần mềm điều khiển robot của **đội IroncorteX**, trường **THPT Lê Hồng Phong**, phục vụ cuộc thi **FPTU AI & Robotics Challenge 2025**.  
Trọng tâm chính của chúng tôi là phát triển **hệ thống điều khiển robot bằng tay cầm PS2 không dây**, phục vụ cho các hành vi chính:

- Di chuyển và điều hướng robot bằng joysticks
- Điều khiển cơ cấu gắp, thả, nhả vật thể (Nông sản, Đất, Hạt giống)
- Kích hoạt chế độ leo Cột cân bằng năng lượng
- Tích hợp cảm biến và servo để robot tương tác với vật thể thông minh

---

### 🔁 Các bước phát triển hệ thống phần mềm

#### ✅ Bước 1: Kế thừa bộ mã nguồn mẫu `makerbot-2025-ps2-motor-test`

Link tham khảo: [https://github.com/makerviet/via-k12maker/tree/release-2025/firmwares/example-ps2/makerbot-2025-ps2-motor-test](https://github.com/makerviet/via-k12maker/tree/release-2025/firmwares/example-ps2/makerbot-2025-ps2-motor-test)

**Lý do lựa chọn:**
- Bộ mã nguồn đã tách module rõ ràng:
  - `PS2_controller.h` – điều khiển tay cầm
  - `motors.h` – điều khiển động cơ
- Hỗ trợ các tính năng quan trọng:
  - Nhấn **PSB_SELECT** để chuyển chế độ tay trái / tay phải
  - Giữ **PSB_R2** để chạy max tốc độ (4095 PWM)

---

#### ✅ Bước 2: Nâng cấp khả năng kết nối ổn định PS2 ↔ VIA

- Tối ưu **kết nối tay cầm**:
  - Mạch VIA dò tìm tay cầm với tối đa 10 lần lặp.
  - Nếu thất bại → tự động reset tối đa 3 lần.
- **Lưu ý**:
  - Phải bật tay cầm PS2 trước khi cấp nguồn cho VIA.
  - Tránh khởi tạo cảm biến/servo trước khi PS2 kết nối thành công.

---

#### ✅ Bước 3: Khắc phục lỗi Joysticks bị lệch trục

- Vấn đề: Joysticks bị đảo trục, robot chỉ tiến khi đẩy cần sang phải.
- Giải pháp:
  - Chỉnh lại giá trị đọc từ `PS2.getAnalog(PSS_LY)` và `PSS_LX`.
  - Ánh xạ đúng trục Y tiến/lùi, trục X rẽ trái/phải.
  - Áp dụng ngưỡng "dead zone" để tránh rung nhẹ gây sai lệch.

---

#### ✅ Bước 4: Tạo thêm module riêng

- **`cambien.h`**: quản lý cảm biến vật cản (IR hoặc siêu âm).
- **`servos.h`**: điều khiển các cơ cấu gắp, nâng, thả bằng servo MG996.
- Các module được viết tách biệt, giúp:
  - Dễ bảo trì
  - Có thể kiểm thử độc lập
  - Giảm độ phức tạp của `main.ino`

---


