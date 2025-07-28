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
<img width="957" height="857" alt="image" src="https://github.com/user-attachments/assets/4c46d4f6-2d46-4fd8-b755-486891d12e42" />
![Uploading image.png…]()

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
## 📑 Phân tích các Thuật toán chính

Để giải quyết các vấn đề thực tiễn, **Ban Lập trình** đã phát triển và áp dụng nhiều thuật toán độc đáo:

---

### 1️⃣ Thuật toán Đảm bảo Kết nối Tay cầm PS2
**Thuật toán:**  
Một quy trình khởi tạo được thiết kế để robot chỉ bắt đầu hoạt động khi đã có kết nối vững chắc với tay cầm. Chương trình sẽ dò tìm tay cầm trong một vòng lặp (10 lần). Nếu sau 10 lần vẫn thất bại, mạch VIA sẽ **tự động reset** và bắt đầu lại toàn bộ quá trình.

**Phân tích:** Đây là một cơ chế **fail-safe**. Nó ngăn chặn tình trạng chương trình bị “treo” hoặc robot chạy mà không có điều khiển – một lỗi rất nguy hiểm trong thi đấu.

- **Điểm mạnh:** Tăng độ tin cậy lên mức tối đa, đảm bảo robot luôn sẵn sàng và có thể điều khiển ngay khi vào trận.  
- **Điểm yếu:** Thời gian khởi động robot có thể lâu hơn một chút nếu kết nối ban đầu không tốt.  
- **Lý do sử dụng:** Trong môi trường thi đấu có nhiều nhiễu sóng, việc đảm bảo kết nối ổn định là yếu tố sống còn, quyết định sự thành bại của robot.

---

### 2️⃣ Thuật toán Điều khiển Servo Mượt mà (`moveServoSmooth`)
**Thuật toán:**  
Thay vì ra lệnh cho servo đi thẳng đến vị trí đích, hàm `moveServoSmooth` **chia nhỏ quãng đường di chuyển** thành nhiều bước. Nó dùng một vòng lặp `for` để tăng/giảm góc quay từng chút (ví dụ: 4°/bước) và chèn một độ trễ ngắn (`delay`) giữa mỗi bước.

**Phân tích:** Kỹ thuật này biến một chuyển động tức thời, giật cục thành **chuyển động mượt mà, có gia tốc**.

- **Điểm mạnh:** Bảo vệ cơ cấu cơ khí khỏi các cú sốc đột ngột, giảm nhiễu điện và sụt áp hệ thống, tăng độ chính xác cho thao tác tinh vi như đẩy nhẹ bóng.  
- **Điểm yếu:** Thời gian thực hiện hành động dài hơn so với lệnh trực tiếp.  
- **Lý do sử dụng:** Cần cho các nhiệm vụ đòi hỏi sự chính xác, nhẹ nhàng, đồng thời tăng tuổi thọ cho servo và cơ cấu cơ khí.

---

### 3️⃣ Thuật toán Hãm Motor bằng Ngắn mạch Mức cao
**Thuật toán:**  
Khi người điều khiển nhả nút nâng/hạ, thay vì chỉ ngắt điện (để motor quay tự do), chương trình thiết lập **cả hai chân điều khiển motor** trên mạch PCA9685 ở mức **logic cao (PWM = 4095)**.

**Phân tích:** Điều này tạo ra trạng thái **ngắn mạch hai đầu motor DC**. Dòng điện cảm ứng (back-EMF) sinh ra khi motor đang quay tạo lực cản từ trường mạnh, giúp motor **dừng lại gần như ngay lập tức**.

- **Điểm mạnh:** Tạo lực hãm cực kỳ hiệu quả mà không cần phanh cơ khí, giúp tiết kiệm chi phí, không gian và độ phức tạp. Giữ cơ cấu nâng hạ không bị trôi xuống.  
- **Điểm yếu:** Có thể gây dòng điện tăng vọt trong chốc lát, đòi hỏi driver và pin phải đủ tốt để chịu được.  
- **Lý do sử dụng:** Đây là giải pháp phần mềm **thông minh và tiết kiệm** để giải quyết bài toán cơ khí khó: **làm sao dừng và giữ một cơ cấu nặng chính xác mà không cần phanh cơ.**

---
