# Điều khiển hệ thống đa LED bằng OneButton – ESP32-S3

Dự án PlatformIO xây dựng giải pháp điều khiển chuỗi đèn LED bằng nút bấm, tích hợp thư viện [OneButton](https://github.com/mathertel/OneButton) kết hợp cùng module `LED` (nằm trong `lib/LED`). Toàn bộ kịch bản được phân chia theo từng **môi trường (env)** riêng biệt trong tệp `platformio.ini`, giúp chuyển đổi và nạp trực tiếp từng bài toán mà không cần can thiệp mã nguồn.

## Phần cứng

* Board vi điều khiển: **ESP32-S3 DevKitC-1 N16R8** (16 MB Flash, 8 MB PSRAM)
* 2 LED đơn, 2 điện trở hạn dòng 1k Ω
* 1 nút nhấn nhả (Push button 4 chân)
* Test board (Breadboard) và dây cắm mạch

## Cấu trúc dự án
week3_iot/
├── lib/LED/LED.h        # Module LED non-blocking: on(), off(), flip(), blink(ms), loop()
├── src/
│   ├── blink.cpp        # Kịch bản kiểm tra LED nhấp nháy cơ bản
│   ├── doublePush.cpp   # Kịch bản 1 nút điều khiển 1 LED
│   └── twoLeds.cpp      # Kịch bản 1 nút điều khiển luân phiên 2 LED
└── platformio.ini       # Khai báo cấu hình riêng cho từng env

```

Mỗi env áp dụng tham số `build_src_filter` để cô lập file thực thi, đồng thời khai báo chân qua `build_flags`:

| Env | File thực thi | Sơ đồ chân kết nối |
| --- | --- | --- |
| `blink` | `blink.cpp` | LED: GPIO4 |
| `double_push` | `doublePush.cpp` | LED: GPIO4, Nút: GPIO0 (nút BOOT tích hợp) |
| `two_leds` | `twoLeds.cpp` | LED1: GPIO4, LED2: GPIO6, Nút: GPIO5 |

## Chức năng

### blink

LED tự động chớp tắt theo chu kỳ 500 ms.

### double_push

* **Single click:** Đảo trạng thái Bật / Tắt LED
* **Double click:** Chuyển sang chế độ nháy LED chu kỳ 200 ms

### two_leds

Một nút nhấn vật lý quản lý đồng thời hai đèn LED:

| Thao tác | Chức năng chi tiết |
| --- | --- |
| **Double click** | Hoán đổi đèn đang điều khiển (LED1 ⇄ LED2). Đèn được chọn sẽ sáng báo hiệu, đèn còn lại lập tức tắt |
| **Single click** | Bật hoặc tắt đèn đang được chọn |
| **Giữ nút** | Đèn đang chọn chuyển sang chớp nháy liên tục 200 ms một lần |

> Khi khởi động, hệ thống mặc định kích hoạt LED1 và bật sáng LED1. Trường hợp đèn đang chớp nháy mà nhận tín hiệu single click, đèn sẽ ngừng chớp và tắt hẳn.

## Hướng dẫn nạp code

1. Dưới thanh trạng thái của VS Code, click vào tên môi trường hiện tại rồi chuyển sang env cần chạy (ví dụ `env:two_leds`).
2. Bấm nút **Upload** (→) để nạp vào mạch.

> Nếu giữ nguyên cấu hình `Default`, PlatformIO sẽ biên dịch và nạp nối tiếp tất cả các môi trường, board sẽ lưu kịch bản của env cuối cùng. Hãy chọn cụ thể môi trường trước khi bấm nạp.

Dòng lệnh nạp nhanh qua Terminal:

```bash
pio run -e two_leds -t upload

```

## Thư viện sử dụng

* [mathertel/OneButton](https://github.com/mathertel/OneButton) `^2.6.1`: Bắt chuỗi sự kiện click, double click, giữ nút và lọc rung phím phần cứng.
* `lib/LED/LED.h`: Lớp điều khiển đèn bất đồng bộ (non-blocking) dựa trên bộ đếm thời gian thực `millis()`.

