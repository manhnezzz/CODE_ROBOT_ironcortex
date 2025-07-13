# 🤖 Robot Điều Khiển PS2 | FPTU AI & Robotics Challenge 2025

Đây là repository chính thức của đội [Tên đội] cho vòng Chung kết cuộc thi **FPTU AI & Robotics Challenge 2025**.  
Dự án tập trung vào việc **điều khiển robot bằng tay cầm PS2**, sử dụng **mạch VIA Maker** và lập trình điều khiển chính xác các mô-đun robot.

---

## 🎮 Mục tiêu dự án

Lập trình các hành vi robot thông qua tay cầm PS2, sử dụng thư viện `PS2_controller.h`, đồng thời:
- Điều khiển di chuyển bằng Joystick
- Điều khiển tốc độ cao khi nhấn PSB_R2
- Kết nối ổn định giữa tay cầm và mạch VIA
- Phát triển thêm các tính năng cảm biến và servo

---

## 🗂️ Cấu trúc thư mục

```bash
/
├── main.ino                # Chương trình chính
├── PS2_controller.h        # Điều khiển tay cầm PS2
├── motors.h                # Điều khiển động cơ
├── cambien.h               # Điều khiển cảm biến vật cản (mới)
├── servos.h                # Điều khiển servo (mới)
├── README.md               # Hồ sơ GitHub (bạn đang đọc đây)
