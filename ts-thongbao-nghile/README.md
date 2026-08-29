# Tạo ảnh thông báo nghỉ lễ — Trung Sơn

Công cụ nội bộ: nhân viên chọn **nhà thuốc nghỉ lễ (vị trí 1)** + **nhà thuốc
điều hướng khách đến (vị trí 2)**, tải **ảnh chụp Google Maps (vị trí 3)**,
rồi xuất 1 ảnh PNG để đăng Zalo/Facebook hoặc dán tại cửa hàng.

**100% tĩnh (static)** — không backend, không database, không API key,
không đăng nhập. Chạy được cả bằng cách double-click mở file trực tiếp.

## Cấu trúc

```
index.html   trang chính — toàn bộ UI + logic, chạy hoàn toàn trong trình duyệt
stores.js    dữ liệu 181 nhà thuốc (code, tên, địa chỉ, SĐT, giờ hoạt động, toạ độ, trạng thái)
bg-data.js   ảnh nền Nen.jpg nhúng sẵn dạng base64 — trang thực sự dùng file này để vẽ nền,
             KHÔNG tải Nen.jpg qua đường dẫn file (xem giải thích bên dưới)
Nen.jpg      ảnh nền gốc — giữ lại để tham khảo/tái tạo bg-data.js, bản thân trang không đọc file này
```

**Vì sao có `bg-data.js` riêng?** Nếu để trang tải ảnh nền qua đường dẫn
file bình thường (`<img src="Nen.jpg">`), khi mở trực tiếp bằng double-click
(`file://`), Chrome/Edge coi ảnh đó là khác nguồn gốc với trang và "làm bẩn"
canvas — nút **Tải ảnh PNG** sẽ bấm không phản hồi gì (lỗi
`SecurityError: Tainted canvases may not be exported`, không hiện alert gì
cho người dùng thấy). Nhúng ảnh nền thẳng vào JS dạng base64 giải quyết
triệt để vấn đề này, dù mở kiểu gì (double-click hay qua server) đều xuất
ảnh được bình thường.

## 3 vị trí trên banner

1. **Tên chi nhánh nghỉ** — dải mỏng canh giữa, trong khối hướng dẫn phía trên.
2. **Card chi nhánh điều hướng** — khung trắng bên trái: tên trên nền xanh
   thương hiệu, địa chỉ, giờ hoạt động (theo khung lễ, lấy từ `stores.js`),
   số điện thoại — đúng theo mẫu thẻ chi nhánh chuẩn.
3. **Ảnh chụp Google Maps** — khung trắng bên phải: nhân viên tự chụp màn
   hình Google Maps rồi tải lên, kéo để dời vị trí, trượt để phóng to/nhỏ —
   ảnh luôn tự cắt vừa khít khung (không méo, không tràn).

**Chuẩn hiển thị tên**: mọi nơi hiển thị tên chi nhánh (khung chọn của nhân
viên lẫn ảnh banner xuất ra) đều theo định dạng **"Mã - Tên"**, ví dụ
`TS244 - Trung Sơn An Giang 25`, để phân biệt các chi nhánh trùng/gần giống
tên nhau.

## Cách hoạt động

1. Ảnh nền **tự động tải sẵn (nhúng trong `bg-data.js`)** — không cần thao
   tác. Nếu cần đổi ảnh khác tạm thời (không lưu lại), bấm "Dùng ảnh nền khác".
2. Bước 1 → chọn nhà thuốc **nghỉ lễ**.
3. Bước 2 → chọn nhà thuốc **điều hướng đến** (danh sách tự gợi ý theo
   khoảng cách gần nhất tính từ nhà thuốc đã chọn ở bước 1).
4. Bước 3 → tải **ảnh chụp Google Maps**, kéo trực tiếp trong khung 3 ở
   khung xem trước để dời vị trí, dùng thanh trượt để phóng to/thu nhỏ.
5. Bước 4 (tuỳ chọn) → **"Chỉnh vị trí khung 1/2/3"** nếu đổi `Nen.jpg` sang
   mẫu khác có bố cục lệch — kéo/thả để canh lại, lưu vào `localStorage`.
6. **"⬇ Tải ảnh PNG"** → xuất file, đúng độ phân giải ảnh nền gốc
   (1024×1536px với `Nen.jpg` hiện tại).

Nút **"🧭 Mở chỉ đường Google Maps"** mở lộ trình thật (không tốn phí, không
cần API key) để nhân viên kiểm tra đường đi trước khi chụp màn hình cho
khung 3.

**Bảo mật**: trang không có đăng nhập/mật khẩu (theo yêu cầu), URL sau khi
deploy sẽ công khai. Không có dữ liệu nhạy cảm ngoài tên/địa chỉ/SĐT vốn đã
công khai của hệ thống nhà thuốc.

## Chế độ tự kiểm tra (dành cho debug)

Mở `index.html?selftest=1` sẽ tự điền 1 cặp nhà thuốc mẫu + 1 ảnh test vào
khung 3, để xem nhanh bố cục cả 3 khung mà không cần thao tác tay. Không ảnh
hưởng gì khi dùng URL bình thường (không có `?selftest=1`).

## Test local

Vì là file tĩnh, cách nhanh nhất là mở thẳng `index.html` bằng trình duyệt
(double-click file). Nếu trình duyệt chặn script local do mở qua `file://`,
chạy 1 static server tại thư mục này, ví dụ:

```bash
npx --yes http-server . -p 8788
# hoặc
python -m http.server 8788
```

rồi mở `http://127.0.0.1:8788`.

## Đổi ảnh nền vĩnh viễn (đợt lễ khác, mẫu banner khác)

Đổi `Nen.jpg` sang ảnh mới rồi build lại `bg-data.js` (không chỉ thay file
ảnh — trang đọc `bg-data.js`, không đọc `Nen.jpg` trực tiếp, xem giải thích
ở trên):

```js
const fs = require('fs');
const buf = fs.readFileSync('ts-thongbao-nghile/Nen.jpg');
const uri = 'data:image/jpeg;base64,' + buf.toString('base64');
fs.writeFileSync('ts-thongbao-nghile/bg-data.js', 'const BG_DATA_URI = "' + uri + '";\n');
```

Nếu ảnh nền mới có bố cục 3 khung khác `Nen.jpg` gốc, dùng nút "Chỉnh vị trí
khung 1/2/3" trong trang để canh lại (không cần sửa code).

## Deploy lên Cloudflare Pages

### Cách 1 — Kéo-thả trên Dashboard (không cần cài gì)
1. Vào https://dash.cloudflare.com → **Workers & Pages** → **Create** → **Pages** → **Upload assets**.
2. Kéo thả cả thư mục `ts-thongbao-nghile/` vào ô upload (bắt buộc phải có
   `bg-data.js` — file chứa ảnh nền thật; `Nen.jpg` đi kèm chỉ để tham khảo,
   không bắt buộc phải deploy).
3. Đặt tên project → **Deploy**.

### Cách 2 — Dùng Wrangler CLI
```bash
npm install -g wrangler        # nếu chưa có
wrangler login                 # đăng nhập tài khoản Cloudflare (mở trình duyệt)
cd ts-thongbao-nghile
wrangler pages deploy .        # lần đầu sẽ hỏi tên project
```
Các lần sau chỉ cần chạy lại `wrangler pages deploy .` trong thư mục này để
cập nhật bản deploy (URL giữ nguyên).

## Cập nhật dữ liệu nhà thuốc sau này

Danh sách nhà thuốc nằm trong `stores.js`. Nếu trang tra cứu gốc
(`tra-cuu-nha-thuoc-le-2-9.html`) được cập nhật cho đợt lễ khác, chạy lại
đoạn script sau để tái tạo `stores.js` (tại thư mục chứa cả 2 file):

```js
const fs = require('fs');
const html = fs.readFileSync('tra-cuu-nha-thuoc-le-2-9.html', 'utf8');
const stores = JSON.parse(html.match(/const STORES = (\[[\s\S]*?\]);/)[1]);
const slim = stores.map(s => ({
  code: s.code, name: s.name, address: s.address, province: s.province,
  lat: s.lat, lng: s.lng, status: s.status, phone: s.phone, hours: s.hours
}));
fs.writeFileSync('ts-thongbao-nghile/stores.js', 'const STORES = ' + JSON.stringify(slim) + ';\n');
```
