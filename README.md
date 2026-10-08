# Điều khiển LED bằng nút nhấn – ESP32-S3 + OneButton

Dự án PlatformIO gồm các ví dụ điều khiển LED bằng nút nhấn, kết hợp thư viện OneButton và thư viện LED (thư mục lib/LED). Mỗi ví dụ tương ứng một môi trường (env) độc lập trong tệp platformio.ini, chỉ cần chọn env phù hợp là có thể nạp ngay mà không cần sửa mã nguồn.

## Phần cứng sử dụng

* Board vi điều khiển: ESP32-S3 DevKitC-1 N16R8 (16 MB flash, 8 MB PSRAM)
* 2 LED đơn, 2 điện trở hạn dòng 1k Ω
* 1 nút nhấn nhả (Push button)
* Test board (breadboard) và dây cắm mạch

## Cấu trúc thư mục

* lib/LED/LED.h: Lớp quản lý LED non-blocking (các hàm on, off, flip, blink, loop)
* src/blink.cpp: Kịch bản kiểm tra LED nhấp nháy cơ bản
* src/doublePush.cpp: 1 nút điều khiển 1 LED
* src/twoLeds.cpp: 1 nút luân phiên điều khiển 2 LED
* platformio.ini: Tệp cấu hình các môi trường nạp và định nghĩa GPIO

Mỗi môi trường sử dụng build_src_filter để cô lập file thực thi và dùng build_flags để gán chân GPIO:

* Môi trường blink (file blink.cpp): LED kết nối chân GPIO 4.
* Môi trường double_push (file doublePush.cpp): LED kết nối chân GPIO 4, nút nhấn dùng nút BOOT tích hợp sẵn trên board tại chân GPIO 0.
* Môi trường two_leds (file twoLeds.cpp): LED 1 kết nối chân GPIO 4, LED 2 kết nối chân GPIO 6, nút nhấn kết nối chân GPIO 5.

## Chức năng chi tiết

### blink
LED tự động nhấp nháy theo chu kỳ 500 ms.

### double_push
* Single click (nhấn 1 lần): Bật hoặc tắt LED.
* Double click (nhấn đúp): LED chuyển sang nhấp nháy chu kỳ 200 ms.

### two_leds
Sử dụng một nút nhấn vật lý để điều khiển đồng thời hai LED:

* Double click (nhấn đúp): Chuyển quyền điều khiển giữa hai LED (LED 1 sang LED 2 và ngược lại). LED được chọn sẽ sáng lên để nhận diện, LED còn lại tự động tắt.
* Single click (nhấn 1 lần): Bật hoặc tắt LED đang được chọn. Nếu LED đang ở trạng thái chớp nháy mà nhận lệnh này, LED sẽ dừng nhấp nháy và tắt hẳn.
* Giữ nút (Long press): LED đang được chọn sẽ chuyển sang chế độ nhấp nháy chu kỳ 200 ms.
* Trạng thái khởi tạo: Khi vừa cấp nguồn, hệ thống mặc định chọn LED 1 và bật sáng LED 1.

## Hướng dẫn nạp chương trình

1. Nhìn xuống thanh trạng thái dưới cùng của VS Code, click vào tên môi trường hiện tại và chọn env muốn nạp (ví dụ env:two_leds).
2. Bấm nút Upload (biểu tượng mũi tên sang phải) trên giao diện PlatformIO.

Nếu muốn nạp nhanh bằng dòng lệnh trong Terminal:
pio run -e two_leds -t upload

Lưu ý: Không nên để chế độ Default vì PlatformIO sẽ biên dịch và nạp lần lượt tất cả các môi trường, board sẽ chỉ lưu kịch bản của môi trường được nạp cuối cùng.

## Thư viện tích hợp

* mathertel/OneButton: Nhận diện các sự kiện click, double click, nhấn giữ và lọc rung phím phần cứng.
* lib/LED: Module điều khiển trạng thái bật/tắt/nháy của LED dựa trên hàm millis(), hoàn toàn không chặn luồng CPU (non-blocking).