# Đề kiểm tra Hóa 10 – THPT Quang Trung

Website làm bài kiểm tra trực tuyến môn Hóa học lớp 10. Học sinh chọn đề và làm bài có giới hạn thời gian; hệ thống chấm bài ở máy chủ và lưu kết quả để giáo viên theo dõi.

> Repository GitHub: [subap209/de-kiem-tra-hoa-10-thpt-quang-trung](https://github.com/subap209/de-kiem-tra-hoa-10-thpt-quang-trung) (riêng tư).

## Tính năng

### Dành cho học sinh

- Nhập họ tên và lớp, sau đó chọn một trong **25 đề** Hóa học học kỳ I.
- Mỗi đề có thời gian làm bài **30 phút**, đồng hồ đếm ngược và tiến độ trả lời.
- Hỗ trợ ba dạng nội dung: trắc nghiệm một lựa chọn, các mệnh đề đúng–sai và câu trả lời ngắn.
- Có thể nộp bài chủ động hoặc để hệ thống tự nộp khi hết giờ; giao diện xác nhận trước khi nộp chủ động.
- Sau khi nộp, học sinh xem điểm tổng theo thang 10 và điểm từng phần.

Ngân hàng đề hiện có 438 câu trắc nghiệm, 96 câu đúng–sai (gồm 384 mệnh đề) và 146 câu trả lời ngắn. Số câu trong mỗi đề có thể khác nhau.

### Dành cho giáo viên

- Mở khu vực giáo viên tại `/teacher` và đăng nhập bằng mật khẩu được cấu hình phía máy chủ.
- Xem các bài đã nộp, lọc theo đề, tìm theo tên/lớp, xem điểm tổng và điểm từng phần.
- Theo dõi điểm trung bình, điểm cao nhất, phổ điểm và mở phần đối chiếu câu trả lời.
- Danh sách kết quả tự làm mới định kỳ; có nút tải lại thủ công.
- Có thể kết nối Google Sheets qua Apps Script để lưu bản sao kết quả đã nộp, theo dõi trạng thái đồng bộ và đồng bộ lịch sử theo thao tác riêng.
- Mở bảng tính từ dashboard rồi chọn **File → Download → Microsoft Excel (.xlsx)** để xuất; trước khi chuyển lớp, giáo viên có thể xóa dữ liệu của đúng một lớp sau khi xác nhận tên lớp.

Hướng dẫn triển khai Apps Script, cấu hình khóa và xử lý tab đích nằm tại [`integrations/google-sheets/README.md`](integrations/google-sheets/README.md).

### Chấm điểm và dữ liệu

- Đáp án chuẩn nằm trong `server/answerKeys.ts`; API nội dung đề chỉ cung cấp câu hỏi, không gửi khóa đáp án cho trình duyệt.
- Máy chủ ghi nhận thời điểm bắt đầu, kiểm tra thời hạn và chấm bài khi nhận bài nộp.
- Câu trả lời ngắn được chuẩn hóa dấu, ký hiệu số mũ và định dạng số; các đáp án thay thế cùng dung sai được khai báo trong khóa đáp án.
- Kết quả và thông tin lượt thi được lưu trong MySQL qua Drizzle ORM.
- Khi được cấu hình, máy chủ đồng bộ họ tên, lớp, đề, thời điểm, thời lượng và điểm sang Google Sheets; câu trả lời thô và đáp án chuẩn không được gửi. MySQL vẫn là nguồn dữ liệu chính.
- `ANSWER_REVIEW.md` ghi lại việc kiểm tra độc lập một số câu tính toán khó. Tài liệu này **không** khẳng định toàn bộ đáp án của 25 đề đã được kiểm định độc lập.

## Công nghệ

- **Giao diện:** React 19, TypeScript, Vite, Tailwind CSS, Wouter.
- **Máy chủ/API:** Node.js 22, Express, tRPC và TypeScript.
- **Cơ sở dữ liệu:** MySQL, Drizzle ORM và Drizzle Kit.
- **Kiểm thử:** Vitest; kiểm tra kiểu bằng TypeScript.
- **Quản lý gói:** pnpm 10.18.0, phiên bản được khai báo trong `package.json`.

## Cấu trúc thư mục

```text
client/
  src/
    pages/             Màn hình học sinh và bảng kết quả giáo viên
    components/        Thành phần giao diện dùng chung
    lib/               Hàm gọi API và client tRPC
    contexts/, hooks/  Trạng thái giao diện và hooks
  public/              Tài nguyên tĩnh và hình minh họa đề thi
shared/
  examContent.ts       Ngân hàng câu hỏi được phép hiển thị công khai
  types.ts             Kiểu dữ liệu dùng chung
server/
  _core/               Khởi động Express và tích hợp nền tảng
  examRoutes.ts        API đề thi, lượt làm bài và kết quả giáo viên
  googleSheets.ts      Adapter Apps Script và worker retry đồng bộ kết quả
  examScoring.ts       Chuẩn hóa câu trả lời và chấm điểm
  answerKeys.ts        Đáp án/lời giải, chỉ dùng phía máy chủ
  db.ts                Kết nối và truy vấn cơ sở dữ liệu
  *.test.ts            Kiểm thử server và chấm điểm
drizzle/
  schema.ts            Lược đồ MySQL
  *.sql                Các migration đã lưu trong repository
integrations/google-sheets/
  Code.gs              Apps Script bridge cho spreadsheet/tab đã chọn
  README.md            Hướng dẫn triển khai Apps Script và xuất Excel
```

## Yêu cầu

- Node.js 22 (Dockerfile hiện dùng `node:22-bookworm-slim`).
- pnpm 10.18.0 hoặc Corepack tương thích với trường `packageManager` trong `package.json`.
- MySQL tương thích với `mysql2` nếu cần lưu bài làm và sử dụng bảng kết quả giáo viên.

## Cài đặt và chạy local

### 1. Tải mã nguồn và cài dependency

```bash
git clone https://github.com/subap209/de-kiem-tra-hoa-10-thpt-quang-trung.git
cd de-kiem-tra-hoa-10-thpt-quang-trung
corepack enable
corepack prepare pnpm@10.18.0 --activate
pnpm install --frozen-lockfile
```

Nếu máy chưa có Corepack, hãy cài Node.js 22 rồi cài pnpm 10.18.0 theo hướng dẫn chính thức của pnpm.

### 2. Cấu hình biến môi trường

Tạo tệp `.env` tại thư mục gốc (tệp này đã được Git bỏ qua) và thay các giá trị minh họa bằng cấu hình của bạn:

```dotenv
DATABASE_URL="mysql://<user>:<password>@127.0.0.1:3306/hoahoc10"
TEACHER_PASSWORD="<thay-bang-mat-khau-manh-va-rieng-biet>"
PORT="3000"
NODE_ENV="development"
```

| Biến | Bắt buộc khi nào | Mục đích |
| --- | --- | --- |
| `DATABASE_URL` | Khi chạy migration, lưu lượt thi hoặc xem kết quả | Chuỗi kết nối MySQL; không commit giá trị thật. |
| `TEACHER_PASSWORD` | Khi giáo viên đăng nhập | Mật khẩu được kiểm tra ở máy chủ; hãy đặt bằng secret của môi trường triển khai. |
| `GOOGLE_SHEETS_WEB_APP_URL` | Khi bật Google Sheets | URL HTTPS của Apps Script Web app, kết thúc bằng `/exec`; chỉ lưu ở server. |
| `GOOGLE_SHEETS_SHARED_SECRET` | Khi bật Google Sheets | Khóa dài, ngẫu nhiên, trùng Script Property `SYNC_SECRET`; không commit hoặc gửi qua chat. |
| `PORT` | Không bắt buộc | Cổng HTTP; mặc định `3000`. |
| `NODE_ENV` | Không bắt buộc | Đặt `development` để dùng Vite; khi production, Express phục vụ bản build tĩnh. |

Nếu mật khẩu cơ sở dữ liệu có ký tự đặc biệt trong URL, hãy mã hóa URL các ký tự đó. Không đặt mật khẩu thật vào mã nguồn, README, log hoặc GitHub.

Ứng dụng có thể khởi động mà không kết nối được DB, nhưng các thao tác bắt đầu/nộp bài và trang kết quả sẽ báo DB chưa sẵn sàng. Drizzle Kit cần `DATABASE_URL` trong môi trường của tiến trình chạy lệnh migration.

### 3. Tạo cơ sở dữ liệu và áp dụng migration

Tạo một database MySQL trống (ví dụ tên `hoahoc10`), rồi đặt `DATABASE_URL` trong terminal và chạy migration:

```bash
export DATABASE_URL='mysql://<user>:<password>@127.0.0.1:3306/hoahoc10'
pnpm db:migrate
```

`pnpm db:migrate` áp dụng các migration đã có trong thư mục `drizzle/`. Lệnh `pnpm db:push` tạo migration từ thay đổi schema rồi chạy migration; hãy xem lại các tệp được tạo trước khi đưa thay đổi schema lên môi trường dùng dữ liệu thật.

### 4. Chạy ứng dụng

```bash
pnpm dev
```

Mở:

- Học sinh: <http://localhost:3000/>
- Giáo viên: <http://localhost:3000/teacher>
- Health check: <http://localhost:3000/api/health>

Đặt `TEACHER_PASSWORD` trước khi thử đăng nhập giáo viên. Cần database đã sẵn sàng và đã chạy migration để lưu bài làm/xem kết quả.

## Các lệnh thường dùng

| Lệnh | Tác dụng |
| --- | --- |
| `pnpm dev` | Chạy Express và Vite ở chế độ phát triển. |
| `pnpm check` | Kiểm tra kiểu TypeScript (`tsc --noEmit`). |
| `pnpm test` | Chạy bộ kiểm thử Vitest. |
| `pnpm build` | Build frontend và bundle server vào `dist/`. |
| `pnpm start` | Chạy server production từ bản build; cần build trước. |
| `pnpm db:migrate` | Áp dụng migration đã lưu trong repository. |
| `pnpm db:push` | Sinh và áp dụng migration từ schema hiện tại; cần xem lại thay đổi sinh ra. |

Chạy build production bằng:

```bash
pnpm build
NODE_ENV=production pnpm start
```

## API chính

| Phương thức | Đường dẫn | Mục đích | Quyền truy cập |
| --- | --- | --- | --- |
| `GET` | `/api/exams` | Lấy nội dung câu hỏi để hiển thị. | Công khai; không trả khóa đáp án. |
| `POST` | `/api/exam-attempts/start` | Tạo lượt làm bài từ họ tên, lớp và mã đề. | Công khai. |
| `POST` | `/api/exam-attempts/submit` | Gửi câu trả lời; máy chủ chấm và lưu kết quả. | Công khai, cần mã lượt thi hợp lệ. |
| `POST` | `/api/teacher/login` | Đăng nhập bằng `TEACHER_PASSWORD` để nhận token phiên. | Mật khẩu được kiểm tra phía máy chủ. |
| `POST` | `/api/teacher/logout` | Kết thúc phiên giáo viên. | Bearer token. |
| `GET` | `/api/teacher/results` | Lấy kết quả đã nộp; có thể lọc bằng `?examId=...`. | Bearer token giáo viên hợp lệ. |
| `GET` | `/api/teacher/classes` | Lấy số lượt thi theo lớp cho khu vực quản lý dữ liệu. | Bearer token giáo viên hợp lệ. |
| `GET` | `/api/teacher/google-sheets/status` | Trạng thái kết nối, số lượt chờ/lỗi và thông tin tab. | Bearer token giáo viên hợp lệ. |
| `POST` | `/api/teacher/google-sheets/backfill` | Đưa các kết quả cũ chưa đồng bộ vào hàng đợi sau khi giáo viên xác nhận. | Bearer token giáo viên hợp lệ. |
| `POST` | `/api/teacher/classes/delete` | Xóa lượt thi của đúng một lớp khỏi MySQL và Google Sheets sau khi xác nhận tên lớp. | Bearer token giáo viên hợp lệ. |
| `GET` | `/api/health` | Kiểm tra server còn hoạt động. | Công khai. |

## Triển khai

Ứng dụng cần **cả frontend lẫn backend Express** và MySQL để có đủ chức năng. Chỉ tải nội dung `client/` lên hosting tĩnh như GitHub Pages sẽ không chạy được API chấm điểm hoặc lưu bài.

`Dockerfile` hiện dùng Node.js 22, cài dependency theo lockfile, chạy `pnpm build` và khởi động `dist/index.js` tại cổng `3000`. Trước khi nhận lượt thi, môi trường production cần:

1. Cấu hình `DATABASE_URL` và `TEACHER_PASSWORD` bằng secret của nền tảng (không đưa vào image hoặc Git).
2. Cấp MySQL phù hợp, sao lưu dữ liệu và chạy migration như một bước phát hành riêng (`pnpm db:migrate`). Dockerfile hiện **không** tự chạy migration khi khởi động.
3. Định tuyến cổng ứng dụng tới `3000` hoặc truyền `PORT` tương ứng.
4. Dùng endpoint `/api/health` cho kiểm tra tình trạng server.

Việc đưa mã nguồn lên GitHub không đồng nghĩa website đã được triển khai hoặc có URL công khai.

## Lưu ý vận hành và quyền riêng tư

- Hệ thống lưu họ tên, lớp, câu trả lời và điểm bài thi. Chỉ thu thập dữ liệu cần thiết; nhà trường/đơn vị vận hành cần xác định quyền truy cập, thời hạn lưu và quy trình xóa dữ liệu phù hợp.
- Apps Script được triển khai chạy dưới tài khoản chủ bảng tính; Web app cần cho phép website gọi từ server và được bảo vệ bằng `SYNC_SECRET`. Chỉ nhập URL/khóa qua cấu hình bí mật của môi trường, không để lộ khóa ở trình duyệt hay repository. Tab đích được kiểm tra header trước khi ghi; nếu có cấu trúc khác, script từ chối thay đổi.
- Tính năng xóa yêu cầu kết nối Sheet, xóa đúng tên lớp ở cả MySQL và Sheet, đồng thời yêu cầu nhập lại tên lớp; tải bản Excel trước vì thao tác không thể hoàn tác.
- Đáp án phải tiếp tục nằm ở phía server; không import `server/answerKeys.ts` vào mã frontend.
- Bản nháp câu trả lời hiện được giữ trong `sessionStorage` của tab trình duyệt và gửi lên server khi nộp bài; đây **không phải** cơ chế autosave lên server hay khôi phục xuyên thiết bị.
- Phiên giáo viên hiện được giữ trong bộ nhớ server, hết hạn sau 4 giờ; khởi động lại hoặc chạy nhiều instance có thể làm phiên mất hiệu lực/không chia sẻ giữa các instance.
- Trang giáo viên hiện chỉ trả tối đa 500 bài đã nộp gần nhất cho mỗi lần tải. Khi số lượt thi tăng, nên chuyển sang phân trang và lọc trực tiếp ở server.
- Bộ kiểm thử hiện tập trung vào tính nhất quán nội dung/đáp án và một số trường hợp tính điểm. Hãy rà soát đáp án chuyên môn trước khi dùng các đề trong kỳ kiểm tra chính thức.

## Đề xuất tối ưu hóa tiếp theo

1. **Lưu nháp và khôi phục bài thi phía server:** lưu tự động có debounce, hỗ trợ tiếp tục bài khi đổi tab/thiết bị và xử lý rõ trường hợp mất mạng.
2. **Củng cố phiên giáo viên:** chuyển token khỏi `sessionStorage` sang cookie `HttpOnly; Secure; SameSite` phù hợp, lưu phiên trong kho dùng chung và dùng rate limit phân tán thay cho bộ nhớ từng tiến trình.
3. **Nộp bài an toàn khi gọi lặp:** dùng cập nhật có điều kiện/transaction và mã idempotency để hai yêu cầu nộp đồng thời không tạo kết quả không nhất quán.
4. **Tối ưu dashboard khi dữ liệu lớn:** phân trang, lọc/tìm kiếm phía server, thêm chỉ mục theo `status`, `examId`, `submittedAt`, và cân nhắc thay polling 5 giây bằng cơ chế cập nhật phù hợp tải thực tế.
5. **Mở rộng kiểm thử và kiểm soát nội dung:** bổ sung kiểm thử tích hợp API, xác thực dữ liệu đầu vào, tình huống timeout/submit lặp, cùng quy trình đối chiếu độc lập toàn bộ đáp án.
6. **Tối ưu bundle frontend:** production build hiện cảnh báo có chunk lớn hơn 500 kB; phân tích dependency và tách phần biểu đồ/teacher khỏi phần dùng chung để giảm tải ban đầu cho học sinh.
7. **Giảm bề mặt nhận dữ liệu:** giới hạn kích thước JSON theo nhu cầu API bài thi (hiện middleware Express cho phép body tối đa 50 MB) và thêm dọn dẹp/retention cho lượt thi bỏ dở.

---

Mã nguồn hiện dùng giấy phép MIT (xem trường `license` trong `package.json`).
