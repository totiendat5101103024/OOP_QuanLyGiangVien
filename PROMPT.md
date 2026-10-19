# SỨ MỆNH
Xây project C++ "Quản lý giảng viên" (Đề tài 9, môn Lập trình hướng đối tượng, ĐH Sư phạm TP.HCM) cho nhóm 3 sinh viên năm 2 Sư phạm Tin. Nhóm sẽ tự gõ lại code để hiểu và giáo viên chỉ vấn đáp về cấu trúc OOP, không hỏi giao diện. Vì vậy code phải đơn giản, đúng giáo trình, dễ giải thích.

Bạn làm việc trong một HARNESS (khung điều khiển) mà bạn tự dựng ở Giai đoạn 0. Quy tắc tối cao: KHÔNG có bằng chứng từ `scripts/verify` thì KHÔNG được coi là xong.

# ĐẶC TẢ ĐỀ TÀI
- GiangVien (cơ sở): MaGV (mã định danh), HoTen, HeSoLuong, SoTietDay.
- CoHuu: ThamNien, LuongCung, TienDayTrongDinhMuc, TienDayNgoaiDinhMuc (định mức 200 tiết).
- ThinhGiang: MaGVThinhGiang (mã giảng viên), TienMotTiet.
- Đa hình: HienThiThongTin(), ThanhToanLuong().

Nghiệp vụ (đặt trong static const, dễ chỉnh):
- Lương cơ sở 2.340.000; LuongCung = HeSoLuong × lương cơ sở.
- Phụ cấp thâm niên = 1% × LuongCung × số năm, tối đa 40%.
- Đơn giá trong định mức 100.000 đ/tiết (tiết ≤ 200); ngoài định mức 150.000 đ/tiết (phần vượt 200).
- Lương CoHuu = LuongCung + phụ cấp thâm niên + tiền trong định mức + tiền ngoài định mức.
- Lương ThinhGiang = SoTietDay × TienMotTiet.
- MaGVThinhGiang là chuỗi (mã tại đơn vị công tác chính), khác MaGV trong hệ thống.

# RÀNG BUỘC OOP (được kiểm bằng máy, xem oop_lint)
R1. GiangVien trừu tượng: `virtual double ThanhToanLuong() const = 0;`, `virtual void HienThiThongTin() const;`, `virtual ~GiangVien()`.
R2. Thuộc tính lớp cha `protected`; kế thừa `public`; constructor lớp con gọi lớp cha qua initializer list.
R3. Mỗi lớp có: constructor mặc định (tham số có giá trị mặc định), constructor có tham số, constructor sao chép, destructor, getter/setter có kiểm tra hợp lệ (không âm, không rỗng).
R4. getter, HienThiThongTin, ThanhToanLuong là `const`.
R5. `static`: hằng số nghiệp vụ + bộ đếm số giảng viên (static data member + static member function).
R6. `operator<<` và `operator>>` là friend của GiangVien; xuất đa hình qua hàm ảo.
R7. `virtual string LoaiGV() const` trả "CoHuu"/"ThinhGiang" dùng khi ghi/đọc file.
R8. QuanLyGiangVien (lớp bao) giữ `vector<GiangVien*>` bằng con trỏ thô + new/delete; destructor delete từng phần tử; CẤM smart pointer; copy constructor/operator= hoặc khai báo `= delete` kèm chú thích lý do (tránh sao chép nông gây delete hai lần). Chức năng: thêm, xóa theo mã, tìm theo mã/tên, sắp xếp theo lương giảm dần và theo tên (std::sort + lambda), tổng lương, thống kê từng loại, người lương cao nhất, ghi/đọc data/giangvien.txt, cấm trùng mã.
R9. Chỉ dùng: class, kế thừa, virtual, pure virtual, friend, operator overloading, static, const, vector, string, fstream, sort. `dynamic_cast` chỉ khi thật cần và có chú thích. Chuẩn C++14.
R10. Tách .h/.cpp mỗi lớp, có include guard. Không `using namespace std;` trong file .h.
R11. Tên theo đề (MaGV, HoTen, HeSoLuong, SoTietDay, ThamNien, LuongCung...). Console hiển thị đúng tiếng Việt (SetConsoleOutputCP(65001) bọc #ifdef _WIN32).
R12. Comment tiếng Việt kiểu "VÌ SAO viết như vậy" tại: destructor ảo, hàm thuần ảo, initializer list, friend operator, new/delete, static. Code đơn giản hơn là "thông minh hơn".

# CẤU TRÚC THƯ MỤC
QLGV/
├── AGENTS.md              (luật làm việc, tạo ở GĐ0)
├── PROGRESS.md            (nhật ký, cập nhật liên tục)
├── docs/features.json     (danh sách tính năng + trạng thái)
├── scripts/verify.sh (+ verify.ps1 nếu máy Windows không có bash)
├── scripts/oop_lint.sh    (kiểm tra ràng buộc R1-R12 bằng grep)
├── src/   GiangVien, CoHuu, ThinhGiang, QuanLyGiangVien (.h/.cpp), main.cpp
├── tests/ test_luong.cpp, test_quanly.cpp
├── web/   server.cpp, JsonHelper.h, index.html
├── third_party/httplib.h
├── data/giangvien.txt     (6-8 người mẫu: CoHuu ≤200 tiết, >200 tiết, ThinhGiang)
├── Makefile, CMakeLists.txt (target: console, web, test, verify, clean)
├── docs/SoDoLop.md, docs/VanDap.md, docs/KeHoach.md
└── README.md

# GIAI ĐOẠN 0: DỰNG HARNESS (làm trước, chưa viết code nghiệp vụ)
1. Phát hiện môi trường: hệ điều hành, có g++/make/valgrind không. Ghi vào PROGRESS.md. Nếu thiếu g++, dừng lại và báo cách cài; không giả lập kết quả.
2. Tạo AGENTS.md gồm: mục tiêu, R1-R12, quy trình vòng lặp, các điều CẤM (bên dưới).
3. Tạo docs/features.json với mọi tính năng ở dạng:
   {"id":"F01","mo_ta":"...","buoc":"...","passes":false,"bang_chung":""}
   Danh sách tối thiểu:
   F01 GiangVien trừu tượng + destructor ảo
   F02 CoHuu (constructor, getter/setter, ThanhToanLuong, HienThiThongTin)
   F03 ThinhGiang (tương tự)
   F04 operator<< và >> (friend, đa hình)
   F05 static: hằng số + bộ đếm
   F06 test_luong: 6 ca (CoHuu 0 tiết, 200, 201, 250; ThinhGiang 0, 30 tiết), có số tiền kỳ vọng tính tay trong comment
   F07 QuanLyGiangVien: thêm/xóa/tìm, cấm trùng mã
   F08 QuanLyGiangVien: sắp xếp, thống kê, tổng lương, lương cao nhất
   F09 Ghi/đọc file, khôi phục đúng kiểu đối tượng nhờ LoaiGV()
   F10 Menu console + xử lý nhập sai (chữ thay số, số âm, mã trùng, file thiếu) không crash
   F11 Không rò rỉ bộ nhớ (ASan/valgrind)
   F12 Web server: các API (GET/POST/DELETE giangvien, thongke, sapxep) tái dùng lớp C++, không viết lại logic bằng JS
   F13 index.html: bảng, form tự đổi trường theo loại, xóa, tìm, sắp xếp, thống kê, hiển thị công thức lương theo loại (thể hiện đa hình)
   F14 docs/SoDoLop.md (mermaid + bảng kỹ thuật → chương giáo trình → vị trí code)
   F15 docs/VanDap.md (≥25 câu, xem mục "Nội dung VanDap")
   F16 docs/KeHoach.md (phân công 3 người + checklist tự gõ lại)
   F17 Cuối mỗi .h có khối "BÀI TẬP TỰ LÀM LẠI" (2-3 gợi ý)
   F18 README (cách chạy Windows MinGW và Linux/macOS, mục "Giả định")
4. Viết scripts/verify:
   a) Biên dịch tất cả với `g++ -std=c++14 -Wall -Wextra -Werror`.
   b) Chạy toàn bộ tests, in PASS/FAIL từng ca, thoát mã khác 0 nếu có FAIL.
   c) Chạy scripts/oop_lint.
   d) Chạy chương trình với ASan (`-fsanitize=address`) hoặc valgrind trên một kịch bản thêm/xóa/ghi/đọc.
   e) In dòng cuối "VERIFY: PASS" hoặc "VERIFY: FAIL (lý do)".
5. Viết scripts/oop_lint kiểm tra tối thiểu: GiangVien.h có `= 0` và `virtual ~GiangVien`; có `friend` + `operator<<`; không có `unique_ptr|shared_ptr|make_unique|make_shared`; không có `using namespace std` trong .h; có `static` trong GiangVien.h; CoHuu/ThinhGiang kế thừa `public GiangVien`; không còn `TODO|FIXME|NotImplemented` trong src/; getter có `const`.
6. Ghi PROGRESS.md mục đầu: "GĐ0 hoàn thành" kèm kết quả chạy verify trên project rỗng (kỳ vọng FAIL hợp lý vì chưa có code).

# VÒNG LẶP LÀM VIỆC (áp dụng cho mọi tính năng)
Mỗi phiên làm việc, và sau mỗi lần mất ngữ cảnh, BẮT ĐẦU bằng: đọc AGENTS.md → PROGRESS.md → docs/features.json → `git log --oneline -10`.
Rồi lặp:
1. Chọn ĐÚNG MỘT tính năng có passes:false (theo thứ tự id).
2. Viết kế hoạch ngắn (3-5 dòng) vào PROGRESS.md.
3. Viết test trước nếu tính năng có logic (F06-F09), sau đó viết code.
4. Chạy scripts/verify. Nếu FAIL: đọc lỗi, sửa nguyên nhân gốc, chạy lại. Tối đa 3 lần cho cùng một lỗi; quá 3 lần thì ghi blocker vào PROGRESS.md (lỗi gì, đã thử gì) rồi chuyển tính năng khác, không bịa.
5. Chỉ khi verify PASS: đặt passes:true, điền bang_chung (lệnh đã chạy + dòng kết quả quan trọng), cập nhật PROGRESS.md, rồi `git commit -m "F0X: ..."`.
6. Nhắc lại bước 1 cho đến hết danh sách.
Giữ mỗi lần thay đổi nhỏ; không làm nhiều tính năng cùng lúc.

# KIỂM TRA TẦNG WEB (F12, F13)
Chạy `make web`, rồi dùng công cụ trình duyệt của agent mở http://localhost:8080 và thực hiện: thêm 1 CoHuu, thêm 1 ThinhGiang, thêm trùng mã (phải báo lỗi), sắp xếp theo lương, xóa 1 người, xem thống kê. Chụp ảnh hoặc ghi lại kết quả vào bang_chung. Đối chiếu số lương trên web với số lương test_luong tính tay.

# CÁC ĐIỀU CẤM (guardrails)
- CẤM sửa scripts/verify, scripts/oop_lint, hay số kỳ vọng trong tests để "cho qua". Nếu tin rằng test sai, ghi vào PROGRESS.md mục "Đề xuất sửa test" kèm lý do tính tay, và chờ người dùng quyết định.
- CẤM đặt passes:true khi chưa có bằng chứng từ lần chạy verify thực tế.
- CẤM tắt warning, thêm `-w`, bỏ `-Werror`, hoặc dùng `#pragma` để che warning.
- CẤM dùng smart pointer, template phức tạp, lambda đa tầng, macro thông minh: nhóm sinh viên không giải thích được.
- CẤM để TODO, code giả, hoặc hàm rỗng nghiệp vụ.
- CẤM thêm thư viện ngoài ngoài httplib.h. Không đưa file thực thi hay thư mục build vào kết quả cuối.
- Nếu điều gì chưa rõ: chọn giả định hợp lý, ghi vào README mục "Giả định", KHÔNG dừng lại hỏi (trừ khi thiếu trình biên dịch).

# NỘI DUNG docs/VanDap.md (F15)
≥25 câu, đáp án ngắn, mỗi câu chỉ rõ file và hàm trong code thật của project. Bắt buộc có: vì sao GiangVien trừu tượng; bỏ `= 0` thì sao; vì sao destructor ảo và hậu quả nếu không ảo (nêu cụ thể dòng delete trong QuanLyGiangVien); thứ tự constructor/destructor khi tạo/hủy CoHuu; vì sao protected; vì sao operator<< phải là friend; static binding vs dynamic binding (chỉ vị trí trong bài); vì sao vector con trỏ lớp cha; shallow vs deep copy và vì sao QuanLyGiangVien xử lý copy như đã làm; const member function để làm gì; thêm lớp TroGiang phải sửa những file nào; đa hình xảy ra lúc biên dịch hay chạy; friend có phá đóng gói không; overload vs override vs hide; constructor có thể ảo không; vì sao dùng initializer list; static member function không truy cập được gì.

# BÁO CÁO CUỐI (chỉ in khi mọi feature passes:true và VERIFY: PASS)
1. Bảng 18 tính năng + bằng chứng.
2. Kết quả lần chạy verify cuối (dán nguyên).
3. Danh sách file, cách chạy console và web.
4. Các giả định đã dùng và mọi mục còn blocker (nếu có).
5. Gợi ý 5 chỗ trong code mà giáo viên nhiều khả năng sẽ hỏi.
