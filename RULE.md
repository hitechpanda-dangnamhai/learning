# IWF — BUSINESS RULE

> # 🔴🔴🔴 TRẠNG THÁI: **ĐANG PHÂN TÍCH DỞ — CẦN PHÂN TÍCH THÊM** 🔴🔴🔴
>
> ## ❌ **KHÔNG phải quyết định cuối cùng.**
> ## ❌ **KHÔNG phải thông tin để dựa vào.**
> ## ❌ **KHÔNG phải nguồn sự thật.**
> ## ⏳ **Đây là một BẢN NHÁP ĐANG MỞ. Human 2026-07-15 nói rõ: chưa chốt gì cả.**
>
> **Viết bởi planner, 2026-07-15, trong một phiên bàn thiết kế. CHƯA CHẠY MỘT NGÀY NÀO trong thực tế.**
> **Planner tuyên bố thẳng: CHƯA DRY** — 6 vòng gần nhất **vòng nào cũng ra phát hiện mới**, 3 cái là **lớn**. Theo `CLAUDE.md §14`, **chưa được chốt**.
>
> **CẤM dùng file này làm căn cứ để khẳng định bất cứ điều gì.** Nó là **một giả thuyết có bằng chứng từng phần**, không phải một kết luận.
>
> Luật quyền-uy của chương trình này (`PROGRAM.md`): **quyền-uy kiếm bằng CHỨNG-MINH-QUA-SỬ-DỤNG, không phải bằng ĐƯỢC-VIẾT-RA.** File này **1 ngày tuổi**. Nó **không thắng** bất cứ thứ gì đang chạy.
>
> **Trong file, mọi câu đều mang một trong hai nhãn:**
> - **`[ĐÃ CHẠY]`** — có lệnh tái lập, đã chạy thật trên PostgreSQL 16.14, trong transaction + `ROLLBACK`. **Đây là thứ duy nhất tin được.**
> - **`[SUY]`** — lập luận của planner. **CHƯA kiểm chứng. Có thể sai.**
>
> Tiền lệ vì sao phải có nhãn này: phiên trước, planner ghi *"đo live"* cho một **hoá thạch**, và mô hình trung tâm của nó **bị bác bằng số** hai ngày sau.

---

# PHẦN 1 — VẤN ĐỀ `[ĐÃ CHẠY]`

Lấy một luật nghiệp vụ có thật: ***"Đơn hàng trên 5 triệu phải xác thực OTP."***

**Hôm nay luật này sống ở đâu trong sicp?** Không ở đâu cả. Một mẩu trong đầu người. Một mẩu trong *Acceptance* của một slice — mà slice đóng xong thì bị `mv docs/slices/S-XX.md docs/slices/archive/`, tức là **luật đi theo vào nhà xác**. Một mẩu trong một đoạn văn ADR.

**Hệ quả:** không ai trả lời được ba câu — *còn đúng không?* · *có ai vừa làm vỡ không?* · *có cãi nhau với luật khác không?*

**Đây là bệnh thật, có ca tử vong:** slice `S-RETURNS-01` đóng **28/06/2026** với **mọi task ✅**. Năng lực trả hàng **chưa chạy được**. *"T01..T05 xong"* **có nhà** nên đo được; *"trả hàng chạy được"* **không có nhà** nên không ai đo.

---

# PHẦN 2 — ĐIỀU BẤT NGỜ `[ĐÃ CHẠY]`

**sicp đã có một kho rule. Trong đó có 817 cái.**

```bash
cd infra/migrations
grep -ohE 'CHECK *\(' *.sql | wc -l          # 131
grep -ohciE 'references ' *.sql | paste -sd+ | bc   # 107
grep -ohc 'NOT NULL' *.sql | paste -sd+ | bc        # 471
grep -ohci 'UNIQUE' *.sql | paste -sd+ | bc         # 108
```

| Kho | Số | Tình trạng |
|---|---|---|
| **Rule trong SCHEMA** | **817** | 🔒 **chưa mục một cái nào** |
| Luật văn xuôi (`CLAUDE.md`) | 18 | ☠️ **15/18 chỉ là lời khuyên** |
| ADR | 126 | ☠️ văn xuôi |
| DoD | 15 mục | ☠️ **cưỡng chế thật = 0/15** |

**Số đo giết mọi tranh luận:** tầng cưỡng chế của sicp **đã sụp toàn bộ** — runner offline từ **09/07**, `ci.yml` tắt tay **12/07**, 0 git hook, **36 push chưa từng được kiểm**. **Trong suốt cơn sụp đó, 817 rule kia chưa ngừng chạy một giây nào.**

> ## Khác biệt **không phải** cái nào quan trọng hơn. Là **NÓ ĐƯỢC VIẾT Ở ĐÂU.**

`CHECK (total >= 0)` **là** một business rule. Nó sống vì **Postgres TỪ CHỐI câu ghi vi phạm**. *"Đơn trên 5tr phải OTP"* — quan trọng hơn nhiều — chết vì nó **được viết bằng văn xuôi trong một file**.

⟹ **Ý tưởng: `[SUY]` đưa business rule vào chỗ mà rule ĐÃ hoạt động rồi. Không xây rule engine — Postgres LÀ rule engine. IWF xây cái SỔ và cái ỐNG.**

---

# PHẦN 3 — MÔ HÌNH: **TÌNH HUỐNG** là khái niệm trung tâm

Bản trước của file này định nghĩa **rule = một vị từ trên MỘT DÒNG** (`CHECK` constraint). **Human bác, và bác đúng:** business rule thật **liên quan nhiều field, nhiều tình huống, và tình huống này đẻ ra tình huống kia**. Một vị từ-một-dòng **viết không nổi một chữ**.

## 3.1 Ba tầng, không phải một `[ĐÃ CHẠY]`

Lấy rule thật: *"Đơn được hoàn tiền khi **đã hoàn thành** (đã thanh toán VÀ đã trừ kho VÀ đã tạo vận đơn) VÀ trong 7 ngày VÀ không bị khoá. Số tiền hoàn không được vượt tổng đơn."*

```sql
-- ① TÌNH HUỐNG — có TÊN, ghép 3 bảng
CREATE VIEW don_hoan_thanh AS
  SELECT o.id, o.total, o.created_at, o.locked FROM o
  JOIN stock_mv s ON s.order_id=o.id AND s.done
  JOIN ship     h ON h.order_id=o.id AND h.waybill IS NOT NULL
  WHERE o.paid;

-- ② TÌNH HUỐNG dựng TRÊN tình huống ①   ◄── "tình huống này dẫn đến tình huống kia"
CREATE VIEW duoc_hoan_tien AS
  SELECT d.id, d.total FROM don_hoan_thanh d
  WHERE d.created_at > now() - interval '7 days' AND NOT d.locked;

-- ③ RULE (cấm) — một view PHẢI RỖNG
CREATE VIEW vi_pham_r_ref_009 AS
  SELECT r.order_id, r.amount, x.total FROM refund r
  JOIN duoc_hoan_tien x ON x.id = r.order_id
  WHERE r.amount > x.total;
```
**Kết quả chạy thật:** đơn 500k · hoàn 900k ⟹ `so_vi_pham = 1`. **Bắt được, xuyên 4 bảng và 2 tầng tình huống.**

| Human phản đối | Tình huống giải |
|---|---|
| *"liên quan rất nhiều field"* | một tình huống **ghép nhiều bảng** — `JOIN` là bẩm sinh |
| *"nhiều tình huống"* | **mỗi tình huống có một TÊN**. Đó là lý do nó tên là *tình huống*. |
| *"tình huống này dẫn đến tình huống kia"* | **tình huống dựng TRÊN tình huống khác.** Nghĩa đen. |

> ## `[SUY]` `CHECK` constraint **KHÔNG phải mô hình**. Nó là **đích biên dịch RẺ NHẤT** cho phần nhỏ nào vừa lọt.
> ## Mô hình thật: **field → tình huống → tình huống → … → cấm.**

## 3.2 🔑 `domain` = **VỐN TỪ VỰNG** — và nó không mục được `[SUY]`

Bản trước kết luận *"`domain` chỉ là cái nhãn, không được gánh việc, vì phân loại-bằng-tay thì mục"*. **Sai.**

> **`domain` = tập các TÌNH HUỐNG có tên.** *Đơn hoàn thành · Được hoàn tiền · Khách VIP · Đơn quá hạn…*
> **Đó chính là những từ người làm nghiệp vụ nói với nhau.**

Vì mỗi tình huống là **một view CHẠY ĐƯỢC**, nó **không phải phân loại bằng tay** — nó là **code**. **Không mục được.**

**Đây mới là *"business domain THỐNG NHẤT"*:** hỏi 5 người *"đơn hoàn thành nghĩa là gì?"* → **5 câu trả lời khác nhau, không ai sai**. Với tình huống: `SELECT * FROM don_hoan_thanh` → **một định nghĩa duy nhất, và bạn NHÌN THẤY nó**. **Thống nhất không phải vì mọi người đồng ý — mà vì chỉ có MỘT định nghĩa, và nó CHẠY.**

⟹ Planner nạp bề rộng **không phải** *"cho tôi rule gắn nhãn Orders"* mà là **"cho tôi vốn từ vựng của Orders: 12 tình huống có tên"**.

## 3.3 🎁 Tình huống **BẢO VỆ** field của nó `[ĐÃ CHẠY]`

```
ALTER TABLE o DROP COLUMN paid;
ERROR:  cannot drop column paid of table o because other objects depend on it
DETAIL: view don_hoan_thanh depends on column paid of table o
```

**Không ai xoá/đổi tên được một cột đang gánh nghiệp vụ.** Và câu lỗi **gọi tên nghiệp vụ**: *"cột này đang gánh `đơn hoàn thành`"*.

⟹ Lỗ ***"neo mục âm thầm khi đổi schema"*** — planner từng khai là **không vá được** — **đóng, miễn phí, không viết một dòng code**.

## 3.4 Một rule gồm những gì `[SUY]`

**Người điền 3 ô:**

| | |
|---|---|
| **vì sao** | *"Đơn lớn bị gian lận nhiều — quyết 12/06/2026."* → sống ở `decision`, **không** sống trong rule. Nó là **sự kiện quá khứ ⟹ không bao giờ sai ⟹ không cần kiểm.** |
| **biểu thức** | `NOT (grand_total > 5000000) OR otp_verified` — **ô duy nhất phải nghĩ. Gõ bậy thì KHÔNG COMPILE.** |
| **hai dòng dữ liệu** | dòng **phải NHẬN** `(4tr, chưa OTP)` · dòng **phải BỊ CHẶN** `(9tr, chưa OTP)` |

**Máy điền 4 ô — người KHÔNG được gõ:** `[ĐÃ CHẠY]`

| Ô | Máy cho bằng cách nào |
|---|---|
| **neo** *(rule đụng cột nào)* | `pg_constraint.conkey` → **`{grand_total, otp_verified}`** |
| **bậc** *(rẻ hay đắt)* | Postgres nhận ⟹ rẻ · từ chối ⟹ `ERROR: cannot use subquery in check constraint` |
| **câu cưỡng chế** | sinh từ biểu thức |
| **răng** | dòng-phải-chặn bị chặn, **gọi đúng tên** |

**Vì sao BẮT BUỘC có dòng-phải-chặn:** `CHECK (true)` **compile ngon lành và chẳng chặn gì**. Nó **không chặn nổi dòng đó** ⟹ **lộ ngay lúc khai**.
**Và phải gọi ĐÚNG TÊN:** nếu bị chặn bởi rule **khác** ⟹ **răng giả** (đỏ sai lý do). Postgres in tên constraint ra ⟹ **bẫy này bắt được bằng máy**.

---

# PHẦN 4 — CÁI THANG

## 4.1 Bậc = **ai cưỡng chế** `[ĐÃ CHẠY cho 3 dòng đầu]`

| Rule | Ai cưỡng chế | Chạy lúc nào | Tắt được? |
|---|---|---|---|
| *"đơn > 5tr phải OTP"* | **`CHECK`** | mỗi `INSERT` | 🔒 **không** |
| *"paid không quay về pending"* | **`TRIGGER`** *(thấy cả `OLD` lẫn `NEW`)* | mỗi `UPDATE` | 🔒 **không** |
| *"merchant không thấy đơn merchant khác"* | **`RLS`** | mỗi `SELECT` | 🔒 **không** |
| *"không được có bảng `platform_owned_stock`"* | truy vấn `information_schema` | ⚠️ **phải có ai chạy** | có |
| *"tổng đơn = tổng dòng hàng"* | ⚠️ truy vấn — `CREATE ASSERTION` **Postgres chưa cài** | ⚠️ **phải có ai chạy** | có |
| *"giá hiển thị phải realtime"* | ⚠️ chuyện **CODE** — quét mã nguồn | ⚠️ **phải có ai chạy** | có |

**Bằng chứng bức tường:** `ERROR: CREATE ASSERTION is not yet implemented` — PostgreSQL 16.14. SQL:1999 **đã đặc tả** cấu trúc này cho đúng loại rule liên bảng; **Postgres và gần như mọi RDBMS chưa từng cài**. ⟹ **lỗ hổng 25 năm của cả ngành**, không phải khuyết điểm thiết kế này.

> ## `[SUY]` **LUẬT DUY NHẤT: đẩy mọi rule xuống thấp nhất mà nó chịu xuống.**
> Ba dòng đầu **sống sót qua ngày sicp sụp hoàn toàn**. Ba dòng cuối **chết đúng ngày ai đó tắt CI để tiết kiệm tiền** — và **3 ngày sau không ai hay**.

## 4.2 🔑 Bậc là **hệ quả của THIẾT KẾ SCHEMA**, không phải tính chất của rule `[SUY]`

| Bày cột thế nào | *"đơn > 5tr phải OTP"* rơi vào bậc |
|---|---|
| `otp_verified` **cùng dòng** với `grand_total` | **rẻ nhất** — `CHECK`. Miễn phí, không tắt được. |
| OTP nằm ở **bảng riêng** | **đắt** — liên bảng ⟹ **phải có ai chạy** ⟹ **tắt được** |

> **Bạn chọn cột nằm ở đâu, là bạn chọn luôn rule đó có tự sống được hay không.**
> **Thiết kế schema không phải chuyện lưu trữ. Là chuyện quyết định luật nào tự sống, luật nào phải nuôi.**

## 4.3 Gắn vào hệ **đang sống, dữ liệu đã bẩn** `[ĐÃ CHẠY]`

```
ERROR:  check constraint "r_ord_017" of relation "t_ord" is violated by some row
```
Data cũ đã bẩn ⟹ **migration chết** ⟹ ý tưởng **chỉ dùng được cho dự án mới** ⟹ **vô dụng cho ICP**.

**Lối thoát — hai chữ `NOT VALID`:**
```sql
ALTER TABLE t_ord ADD CONSTRAINT r_ord_017 CHECK (...) NOT VALID;  -- gắn được lên data bẩn
INSERT INTO t_ord VALUES (7000000, false);  -- ERROR: violates check constraint "r_ord_017"
```
| rác **CŨ** | **ghi nợ** — vẫn nằm đó, **đếm được**, dọn sau |
|---|---|
| rác **MỚI** | **không vào được nữa** |

> **Cầm máu hôm nay. Dọn nợ sau. Món nợ là một CON SỐ, không phải một cảm giác.**

---

# PHẦN 5 — MÂU THUẪN: **HAI LOẠI, HAI CỖ MÁY**

## 5.1 Loại 1 — **LOGIC**: máy giải, có bằng chứng `[ĐÃ CHẠY]`

Ví dụ của human:
```
Rule 1 (tháng trước):  "đơn trên 10 sản phẩm thì TẶNG 3"
Rule 2 (hôm nay, quên Rule 1):  "đơn trên 10 sản phẩm thì KHÔNG tặng"
```

**Câu hỏi SAI — *"hệ này có nghiệm không?"***
```
>>> HỆ CÓ NGHIỆM: item_count=5, gift_count=0 — CẢ HAI RULE ĐỀU THOẢ
```
**Hệ CÓ nghiệm.** Vì cả hai là *"NẾU trên 10 THÌ…"* — không có đơn nào trên 10 thì **cả hai đúng rỗng**.

> ## **Hai luật không cãi nhau. Chúng LẶNG LẼ XOÁ SỔ mọi đơn trên 10 sản phẩm — và hệ vẫn "có nghiệm", vẫn xanh.**

**Câu hỏi ĐÚNG — *"rule mới vừa GIẾT MẤT cái gì?"***
```
số sản phẩm │ trạng thái hợp lệ TRƯỚC │ SAU
     9      │            6            │  6
    10      │            6            │  6
    11      │            1            │  0   ◄── CHẾT
    12      │            1            │  0   ◄── CHẾT
    13      │            1            │  0   ◄── CHẾT
```
**Không ai khai gì trước.** Máy **tự tính**: trước Rule 2, đơn 11+ có **đúng 1 cách hợp lệ**; sau: **0**.

**⚠️ CÁI NÀY ĐÃ CÓ TÊN TRONG SÁCH — 20 năm trước, và planner tưởng mình vừa nghĩ ra:**

| Tên chuẩn | Nghĩa | Planner gọi là |
|---|---|---|
| **weak satisfiability** | tồn tại **một** thể hiện hợp lệ | *"hệ có nghiệm"* — **phép thử SAI** |
| **strong satisfiability** | tồn tại thể hiện mà **MỌI lớp đều KHÔNG RỖNG** | ***"vùng chết"*** ✅ |
| **redundant constraint** | rule mới **đã được suy ra** từ rule cũ ⟹ thừa | **chưa hề nghĩ tới** |

Nguồn: `UMLtoCSP` / `EMFtoCSP` (2007–2012) — công cụ dịch UML+OCL sang bài toán ràng buộc rồi đưa solver, kiểm **đúng ba tính chất trên**. ⟹ **Không phải phát minh. Phải đi đọc.**

**Cái máy nói với bạn, ngay giây bạn gõ Rule 2:**
> 🔴 **Rule bạn vừa khai làm MỌI ĐƠN TỪ 11 SẢN PHẨM TRỞ LÊN không tồn tại được nữa.**
> **Trước:** đúng **1** cách hợp lệ — *tặng 3 quà* — do `r1_tren10_tang3` *(ADR-0xx, 12/06/2026, lý do: "kích đơn lớn")*. **Sau: 0.**
> **[a] Có, tôi định vậy** — ghi nhận. **[b] Không** — mâu thuẫn `r1`. Chọn: **thay `r1`** hay **sửa rule mới**?

**Và câu trả lời của bạn CHÍNH LÀ dữ liệu:** bấm `[b]` ⟹ *"đơn 12 sản phẩm phải khả thi"* **được ghi vĩnh viễn** ⟹ **mọi rule tương lai giết nó đều bị chặn tự động**.
⟹ **Bạn không phải nhớ khai. Máy TỰ HỎI bạn, đúng lúc, kèm bằng chứng. Và bạn KHÔNG ĐI TIẾP ĐƯỢC nếu không trả lời** ⟹ nó **luôn được ghi**, không phải vì kỷ luật.

## 5.2 Loại 2 — **NGỮ NGHĨA**: máy CÂM, cần Claude `[SUY]`

```
R-A: "giá hiển thị = giá niêm yết"
R-B: "khách VIP thấy giá đã giảm 10%"
```
**Mâu thuẫn — nhưng CHỈ KHI bạn biết *"giá hiển thị"* và *"giá khách VIP thấy"* là MỘT THỨ.** **Không phép toán nào suy ra được điều đó.**

⟹ **Đây là chỗ Claude không thay thế được.** Và tình huống-có-tên **thu nhỏ vấn đề rất nhiều**: nếu `gia_hien_thi` là một view có tên, hai rule **buộc phải trỏ vào cùng một tên** ⟹ **nhập nhằng không còn chỗ nằm**. Phần còn lại — *"hai tình huống này CÓ PHẢI là một không?"* — **là câu hỏi cho Claude**.

**⚠️ Sửa một tuyên bố quá tay của planner:** bản trước viết *"Claude tuyệt đối không được đứng ở đường kiểm"*. **Quá tuyệt đối.** Đúng ra:
> **Claude KHÔNG được đứng ở đường kiểm mâu thuẫn LOGIC** — chỗ đó máy có bằng chứng, Claude chỉ có ý kiến.
> **Claude PHẢI đứng ở đường kiểm mâu thuẫn NGỮ NGHĨA** — chỗ đó máy **câm hoàn toàn**.

## 5.3 Vì sao **KHÔNG** dùng UML `[ĐÃ TRA]`

Human đề xuất chuẩn hoá rule bằng UML rồi cho Claude đọc sơ đồ. **Hai vấn đề:**

**Một — UML không diễn tả nổi ràng buộc.** Đó chính là lý do **OCL** ra đời. Và:
> *"OCL được IBM giới thiệu năm 1997 như một **ngôn ngữ mô hình hoá nghiệp vụ**… nhưng từ khi ra đời, việc sử dụng OCL trong công nghiệp **gần như bằng không — kể cả trong chính cộng đồng phát triển ứng dụng nghiệp vụ mà nó được thiết kế cho**."* — ACM SIGSOFT

**Hai — công cụ "đọc UML tìm mâu thuẫn" hoạt động bằng cách VỨT UML ĐI:**
> *"Công cụ hoạt động bằng cách **DỊCH model UML/OCL thành một bài toán ràng buộc (CSP)** rồi đưa cho **solver**."* — UMLtoCSP

```
UML+OCL ──dịch──► CSP ──giải──► mâu thuẫn        tình huống(view) ──dịch──► CSP ──giải──► mâu thuẫn
   │                │                                   │                     │
   │                └── trí tuệ nằm ĐÂY                  │                     └── CÙNG chỗ
   └── TRÔI khỏi thực tại, không ai hay                  └── KHÔNG TRÔI ĐƯỢC (nó LÀ cái đang chạy)
```
> **Cùng một solver. Cùng phép suy luận. Khác đúng một thứ: MẶT TIỀN — và mặt tiền nào TRÔI.**
> UMLtoCSP: **bạn chạy nó khi bạn nhớ ra.** Cái này: **bạn không khai nổi một rule nếu không đi qua nó.**

**Lấy từ họ ✅:** kỹ thuật dịch-sang-solver · ba tính chất đã có tên · 20 năm kinh nghiệm về cái gì quyết được.
**Không lấy ❌:** UML làm mặt tiền.

---

# PHẦN 6 — CLAUDE ĐỨNG Ở ĐÂU `[SUY]`

| Khoảnh khắc | Ai quyết | Claude? |
|---|---|---|
| Dịch *"đơn lớn phải OTP"* → biểu thức | **Claude đề xuất, người phê** | ✅ |
| Biểu thức hợp lệ không · đụng cột nào · có răng không | **Postgres** | ❌ |
| Mâu thuẫn **LOGIC** | **Postgres / solver** | ❌ |
| Mâu thuẫn **NGỮ NGHĨA** | **Claude** | ✅ |
| 🔴 **Rule ĐANG bị vi phạm không** | **Postgres TỪ CHỐI CÂU GHI** | ❌ **KHÔNG BAO GIỜ** |
| Rule đã chết chưa | **người** — thấy check đỏ rồi phán *"không phải bug, rule chết rồi"* | ✅ |

> **Claude ở HAI ĐẦU — lúc rule ra đời và lúc rule chết. Ở GIỮA, suốt hàng triệu lần ghi, Claude KHÔNG CÓ MẶT.**

**Vì sao tuyệt đối không để Claude kiểm việc máy kiểm được:** tốn tiền mỗi lần ⟹ gom lô ⟹ bỏ bớt ⟹ mục · không lặp lại được · **không nằm trên đường tới hạn ⟹ tuỳ chọn ⟹ đó CHÍNH XÁC là OCL.**

**Khi máy BÍ:** đừng hỏi *"có mâu thuẫn không?"* — **hỏi *"viết lại thế nào để MÁY giải được?"***
> *"giá phải realtime"* → Claude: *"thêm cột `price_source ∈ {live,cached}`, rồi `CHECK (price_source='live')`"* ⟹ **rule tụt từ bậc đắt nhất xuống bậc rẻ nhất. Nợ TRẢ XONG.**
> **Tầng rơi phải TRẢ NỢ, không phải GIẤU NỢ.**

Kéo không nổi ⟹ nhãn **vĩnh viễn, đếm được, già đi**: `r_cat_004  CHƯA CHỨNG MINH — Claude đoán 15/07/2026`. **Nhãn đó tuyệt đối không được trông giống một rule đã chứng minh.**

---

# PHẦN 7 — NỐI VÀO SLICE / TASK `[SUY]`

> ## **BẬC là thứ ĐẺ RA VIỆC.**
> **TASK** = một nước đẩy **MỘT rule xuống bậc thấp hơn** · **SLICE** = lời hứa đẩy **MỘT NHÓM rule xuống bậc có bằng chứng** · **XONG** = mọi rule slice đó hứa **đều có bằng chứng sống**

Rule xuống bậc 1–3 ⟹ **thôi đẻ việc, vĩnh viễn**.
⟹ **Backlog thôi là danh sách.** Nó là truy vấn: *"rule nào chưa xuống được bậc có bằng chứng?"*
⟹ **`S-RETURNS-01` chết đúng đây:** rule của nó **chưa từng được khai** ⟹ slice **về logic KHÔNG THỂ xong**. sicp đo **ô tick** nên nó **xanh**.

> **Tiến bộ không phải "bao nhiêu ô đã tick". Là "bao nhiêu rule đã tụt xuống bậc mà KHÔNG AI CÒN PHẢI CHĂM NÓ".**

---

# PHẦN 8 — MASTER vs CLIENT `[SUY]`

**Cùng một cỗ máy. Khác ở: biểu thức kiểm vào SCHEMA NÀO.**

| | **client-planner / client-coder** | **master-planner / master-coder** |
|---|---|---|
| Viết rule về | **nghiệp vụ khách** | **chính workflow** (sicp gọi là *"luật"*) |
| Kiểm vào | schema khách | **schema của chính IWF** |
| Ví dụ | `NOT (grand_total > 5e6) OR otp_verified` | `verdict.author ≠ task.coder` |

> Luật sicp ***"coder không tự chấm bài mình"*** hôm nay là **văn xuôi, advisory**. Viết dạng này nó là một **trigger** — tra `task.coder`, trùng thì **từ chối**. **Không tắt được.**
> **Cùng cỗ máy phục vụ rule của khách sẽ cưỡng chế luật của chính IWF. Đó là nội dung thật của "IWF tự phát triển IWF".**

**Ba việc chỉ master làm được** *(cần toàn bộ kho, không cần code)*: **① dịch văn xuôi → biểu thức** (*"KHÔNG backorder"* → neo thật là `inventory.available`+`orders.status`; **chữ trong câu không chứa hai cột đó** ⟹ máy so chuỗi **câm**) · **② nhìn N project** (*"47 tenant khác cũng vấp xung đột này; 44 chọn thay rule cũ"* ⟹ **flywheel**) · **③ viết luật cho chính IWF**.

**Không đổi:** **cưỡng chế luôn xảy ra ở nơi có DATA.** Migration giao xuống **một lần**, rồi **master tắt cũng chẳng sao**. ⟹ **Master chỉ cần sống để KHAI và BIÊN DỊCH. Không cần sống để CƯỠNG CHẾ.**

---

# PHẦN 9 — CÁI NÀY **KHÔNG** LÀM ĐƯỢC

**9.1 `[SUY]` Gác BẢN GHI, không gác HÀNH VI.** *"Đơn >5tr phải OTP"* thật ra nói *"không được có DÒNG nào total>5tr mà `otp_verified=false`"*. **Code có thể ghi đại `otp_verified=true` mà chưa bao giờ hỏi OTP.** Xanh. Không ai bị hỏi OTP.
**Vá một phần:** trigger — `otp_verified` chỉ lật `true` nếu có dòng `otp_challenge` đã xác thực. Nhưng code lại ghi đại vào `otp_challenge`… đẩy tiếp bằng `GRANT`… rồi ai gác dịch vụ OTP…
> **Không xoá được lòng tin. Chỉ dồn được về MỘT chỗ, thu nhỏ, và GỌI TÊN.** Vẫn hơn hôm nay — lòng tin **rải khắp mọi dòng code, không ai biết nó nằm đâu**.

**9.2 `[SUY]` Nghiệp vụ không để lại DẤU VẾT ⟹ KHÔNG cơ chế nào kiểm được — kể cả Claude.** *"Khách phải được gọi lại trong 24h"* — nếu cuộc gọi không được ghi, **không có gì để kiểm**.
⟹ **Với nhiều rule, task ĐẦU TIÊN là "bắt đầu GHI LẠI cái đang không được ghi".**

**9.3 `[SUY]` Rule về LỊCH SỬ cần lịch sử tồn tại.** *"Không đổi giá quá 3 lần/tháng"* — `UPDATE` tại chỗ **xoá sạch lịch sử**. Cùng họ 9.2.

**9.4 `[SUY]` Phi tuyến ⟹ bó tay.** `c * g > 100` (nhân **hai biến**) ⟹ **không thuật toán nào quyết được** (Hilbert #10). ⟹ **ngôn ngữ rule phải CẤM nhân hai biến.** Giá thật của *"luôn trả lời được"*, **không thương lượng**.

**9.5 `[ĐÃ CHẠY]` `CREATE ASSERTION` không tồn tại** ⟹ *"tổng = tổng dòng hàng"* **không thể miễn phí**. Lỗ 25 năm cả ngành.

**9.6 `[ĐÃ CHẠY]` Vét cạn có biên.** Bảng đếm ở 5.1 vét **126 trạng thái** (`0..20 × 0..5`). Miền thật (`bigint`) **không vét nổi** ⟹ **cần Z3** (Cedar của AWS đang production trên đúng cơ chế: *"symbolic compilation → SMT… sound, complete, decidable"*).

---

# PHẦN 10 — LỖ CÒN MỞ, PLANNER KHAI THẲNG

1. 🔴 **`[SUY]` View-denial KHÔNG CÓ NHỊP CHẠY TỰ NHIÊN.** `CHECK` chạy mỗi lần ghi. Nhưng *"view này phải RỖNG"* **chạy lúc nào?** Mỗi write vào 4 bảng? (đắt) Đêm? (muộn) Lúc đóng task? **Planner đã lấp liếm chỗ này suốt.** Đây **chính là** vế *"phải có ai chạy"*, và nó **là trái tim của cái thang**.
2. 🔴 **`[SUY]`** Trạng thái nghiệp vụ **đòi phải có**, **chưa từng xảy ra**, **không ai nghĩ ra để khai** — dữ liệu thật không có, seed không có. Cơ chế *"vừa giết mất cái gì"* vá được **phần lớn**, nhưng chỉ trong **không gian nó biết**.
3. **`[SUY]`** Mâu thuẫn ngữ nghĩa: một phần **có thể máy bắt được** (hai view có định nghĩa **chồng lấn**). Chưa đào.
4. **`[SUY]`** *"redundant constraint"* — rule mới **đã được suy ra sẵn**. Chưa thiết kế.

---

# PHỤ LỤC — 12 thí nghiệm `[ĐÃ CHẠY]`

Tất cả trên **PostgreSQL 16.14** (`awf-postgres`), **trong transaction + `ROLLBACK`. Không đụng một dòng dữ liệu nào.**

| # | Khẳng định | Bằng chứng |
|---|---|---|
| 1 | Rule → `CHECK`, đỏ đúng lý do, gọi tên | `violates check constraint "r_ord_017"` · `Failing row contains (1, 9000000, f)` |
| 2 | Postgres tự tính neo | `conkey` → `{grand_total, otp_verified}` |
| 3 | Postgres tự xếp bậc | `ERROR: cannot use subquery in check constraint` |
| 4 | Rule về schema cũng là SQL, răng 2 chiều | `information_schema` → `t` · thêm bảng cấm → `f` |
| 5 | `CREATE ASSERTION` không có | `ERROR: CREATE ASSERTION is not yet implemented` |
| 6 | Data bẩn chặn migration | `ERROR: is violated by some row` |
| 7 | `NOT VALID` gắn được + vẫn chặn rác mới | gắn OK · `INSERT (7tr,false)` → chặn |
| 8 | Nhân chứng không dựng nổi = mâu thuẫn | `(12,3)`→`r2` chặn · `(12,0)`→`r1` chặn |
| 9 | **Hệ 2 rule mâu thuẫn CÓ nghiệm** | `item_count=5, gift_count=0` — cả hai thoả |
| 10 | Đếm vùng chết | `11→0` · `12→0` · `13→0` |
| 11 | **Tình huống chuỗi 4 bảng, 2 tầng** | `so_vi_pham = 1` |
| 12 | **View bảo vệ cột của nó** | `cannot drop column paid… view don_hoan_thanh depends on it` |
| — | sicp đã có 817 rule đang chạy | `131 CHECK · 107 REFERENCES · 471 NOT NULL · 108 UNIQUE` |

---

# NƯỚC ĐI KẾ — nhỏ, và **sai được**

**Đừng xây gì.** Lấy **MỘT business rule THẬT của ICP** → viết thành biểu thức → `NOT VALID` lên DB thật → **xem cái gì vỡ**.

> ## **Dữ liệu thật của ICP đang vi phạm bao nhiêu luật mà không ai biết?**

Gắn sạch ⟹ ý tưởng **đứng**. Lòi ra nghìn dòng vi phạm ⟹ vừa học được **điều quan trọng nhất về ICP**, **rẻ hơn mọi tài liệu thiết kế**.

*(`icp-postgres` tắt từ 14/07. Bật lại bất cứ lúc nào.)*
