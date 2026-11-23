# Báo cáo lab Nhóm 09

**Họ tên:** Vũ Hoàng Long · **MSSV:** 23120058 · **JDK đã dùng** (kết quả `java -version`): OpenJDK 25.0.2 LTS

## Bài 1: Giá trị biên

| Ngày trễ | Hạng | Số tiền bạn điền | Cách tính |
|---:|---|---:|---|
| 24 | NORMAL | 48.000 | 24 × 2.000 = 48.000 |
| 25 | NORMAL | 50.000 | 25 × 2.000 = 50.000, sau đó bị giới hạn tối đa |
| 26 | NORMAL | 50.000 | 26 × 2.000 = 52.000, sau đó bị giới hạn tối đa |
| 2 | VIP | 0 | 2 ngày nằm trong phần ân hạn |
| 3 | VIP | 1.000 | (3 - 2) × 1.000 = 1.000 |
| 51 | VIP | 49.000 | (51 - 2) × 1.000 = 49.000 |
| 52 | VIP | 50.000 | (52 - 2) × 1.000 = 50.000, sau đó bị giới hạn tối đa |
| 53 | VIP | 50.000 | (53 - 2) × 1.000 = 51.000, sau đó bị giới hạn tối đa |

**Vì sao chọn các ngày này mà không chọn ngày 10?**
Các ngày này nằm quanh các điểm biên quan trọng: ngày 24/25/26 kiểm tra ngưỡng mức tối đa 50.000đ của NORMAL; ngày 2/3 kiểm tra hết ân hạn và bắt đầu tính phí của VIP; ngày 51/52/53 kiểm tra ngưỡng mức tối đa của VIP. Ngày 10 là giá trị ở miền trong, không xuất hiện cùng lúc ngưỡng biên hoặc thay đổi hành vi nên ít mạnh hơn trong việc phát hiện lỗi rìa.

## Bài 2a: Coverage

| | Branch coverage của `LoanService` |
|---|---|
| Trước khi viết test | 66% |
| Sau khi viết 2 test | 100% |

**Test `daDatHanMuc` của bạn dùng `countActiveByMemberId` trả về giá trị nào? Vì sao chọn giá trị đó?**
Test dùng giá trị `3L`, đúng ngưỡng biên: thành viên được mượn tối đa 3 cuốn, nên khi đang mượn 3 cuốn hệ thống phải ném `LIMIT_REACHED` và không lưu phiếu mượn.

## Bài 2b: Mutation testing

**Lần chạy PIT đầu tiên:** có bao nhiêu mutant sống sót (SURVIVED)? Nằm ở dòng nào, là loại mutant gì?
Có 1 mutant sống sót ở điều kiện giới hạn mượn trong `LoanService.borrow`, thuộc loại mutant `changed conditional boundary`: phiên bản sai làm điều kiện dùng sai ngưỡng. Sau khi bổ sung test biên, test mới cho biết chính code ban đầu sai ở ngưỡng 3; test và code không thể đồng thời xanh trước khi sửa code.

**Lỗi cài sẵn nằm ở đâu? Bạn phát hiện ra nó bằng cách nào?**
Lỗi cài sẵn nằm ở `LoanService.borrow`, ở điều kiện `countActiveByMemberId(memberId) > 3`. PIT thay đổi điều kiện thành `>= 3`, hoặc ngược lại tùy trạng thái code ban đầu, và test mới kiểm tra đúng ngưỡng 3 chưa phát hiện được trước khi bổ sung test biên.

**Bạn đã sửa test và code như thế nào?**
Viết test `daDatHanMuc` cho `countActiveByMemberId` trả về `3L` và assert mã `LIMIT_REACHED`; viết test `conPhiChuaTra` với nợ dương và assert mã `UNPAID_FINE`. Sau đó đổi điều kiện giới hạn thành `>= 3`, để từ 3 cuốn trở lên đều bị từ chối theo đặc tả.

**Kết quả PIT sau khi sửa** (Killed bao nhiêu / tổng bao nhiêu):
9 / 9 (100%) mutant bị giết; 0 mutant sống sót.

## Câu hỏi tổng kết
**Coverage 100% có nghĩa là code không còn lỗi không? Hãy giải thích dựa trên những gì bạn thấy trong lab.**
Không. Coverage 100% chỉ chứng minh các nhánh đã được thực thi, không chứng minh test kiểm tra đúng dữ liệu biên hoặc hành vi nghiệp vụ. Trong lab, code ban đầu có thể đạt đủ coverage branch nhưng vẫn sai tại ngưỡng 3 cuốn; chỉ khi thêm test biên và chạy PIT thì mutant sai mới bị phát hiện.
