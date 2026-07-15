# IWF — GIẢI PHÁP CHO BUSINESS RULE

> Viết lại từ đầu, 2026-07-15. Đọc file này **không cần biết gì trước đó**.
> Mọi con số trong file đều **đã chạy thật** trên Postgres, trong transaction, rollback sạch.

---

# PHẦN 1 — VẤN ĐỀ

Lấy một luật nghiệp vụ có thật: ***"Đơn hàng trên 5 triệu phải xác thực OTP."***

**Hôm nay luật này sống ở đâu trong sicp?**

Không ở đâu cả. Một mẩu trong đầu người, một mẩu trong phần *Acceptance* của một slice — mà slice đóng xong thì bị `mv` vào thư mục `archive/`, tức là **luật đi theo vào nhà xác**. Một mẩu trong đoạn văn của một ADR nào đó.

**Hệ quả:** không ai trả lời được ba câu:

1. *Luật này còn đúng không?*
2. *Có ai vừa làm vỡ nó không?*
3. *Nó có cãi nhau với luật khác không?*

**Bằng chứng đây là bệnh thật, không phải lo xa:** slice `S-RETURNS-01` đóng ngày 28/06/2026 với **mọi task ✅**. Năng lực trả hàng **chưa chạy được**. Không ai đọc BACKLOG mà biết. Vì *"T01..T05 xong"* **có nhà** nên đo được, còn *"trả hàng chạy được"* **không có nhà** nên không ai đo.

---

# PHẦN 2 — ĐIỀU BẤT NGỜ

**sicp đã có một kho rule rồi. Trong đó có 817 cái.**

```bash
cd infra/migrations
grep -ohE 'CHECK *\(' *.sql | wc -l      # 131
grep -ohciE 'references ' *.sql | ...    # 107
grep -ohc 'NOT NULL' *.sql | ...         # 471
grep -ohci 'UNIQUE' *.sql | ...          # 108
```

| Kho | Số lượng | Tình trạng |
|---|---|---|
| **Rule trong SCHEMA** (`CHECK`, khoá ngoại, `NOT NULL`, `UNIQUE`, `RLS`) | **817** | 🔒 **chưa mục một cái nào. Không cần CI. Không cần ai nhớ.** |
| Luật viết bằng văn xuôi (`CLAUDE.md`) | 18 | ☠️ **15/18 chỉ là lời khuyên** |
| ADR | 126 | ☠️ văn xuôi |
| DoD | 15 mục | ☠️ **cưỡng chế thật = 0/15** |

**Và đây là số đo giết mọi tranh luận:**

Tầng cưỡng chế của sicp **đã sụp toàn bộ**: runner offline từ **09/07**, `ci.yml` bị tắt tay **12/07**, không một git hook nào, 36 lần push chưa từng được kiểm.

**Trong suốt cơn sụp đó, 817 rule kia chưa ngừng chạy một giây nào.**

> ## Khác biệt **không phải** cái nào quan trọng hơn.
> ## Khác biệt là **NÓ ĐƯỢC VIẾT Ở ĐÂU.**

`CHECK (total >= 0)` là một business rule. Nó sống vì **Postgres TỪ CHỐI câu ghi vi phạm**. Không có đường vòng. Không có *"quên chạy"*.

*"Đơn trên 5 triệu phải OTP"* cũng là một business rule — quan trọng hơn nhiều. Nó chết vì nó **được viết bằng văn xuôi trong một file**.

---

# PHẦN 3 — Ý TƯỞNG

> ## **Đưa business rule vào chỗ mà rule ĐÃ hoạt động rồi.**
>
> **Đừng xây rule engine. Postgres LÀ rule engine — đã trả tiền, chạy 30 năm, và không tắt được.**
> **IWF chỉ xây hai thứ: cái SỔ (rule sống ở đâu, vì sao có nó) và cái ỐNG (rule tới tay coder).**

Đây **không phải một đề xuất mới**. Đây là **quan sát một thứ đã đúng sẵn, 817 lần, trong chính repo này, ngay lúc này** — rồi hỏi: **tại sao chỉ có 817 cái đó được cưỡng chế?**

---

# PHẦN 4 — RULE LÀ GÌ

Không dùng thuật ngữ. Một hình ảnh:

> ### **Rule = một cái LỌC.**
> ### **Khai một rule = vứt bỏ bớt một số trạng thái mà hệ thống được phép ở trong.**

```
   Tất cả trạng thái tưởng tượng được
 ┌──────────────────────────────────────────┐
 │                                          │
 │   ┌──────────────────────────┐           │
 │   │  trạng thái HỢP LỆ       │  ← mỗi rule cắt bớt một miếng
 │   │                          │           │
 │   └──────────────────────────┘           │
 │                                          │
 └──────────────────────────────────────────┘
```

**Luật kiểu "phải LÀM gì đó" thì sao?** Đổi thành lọc rất tự nhiên:

> *"Đơn trên 5 triệu **phải** OTP"* ⟺ **cấm** trạng thái *"đơn trên 5 triệu mà chưa OTP"*
> *"Trên 10 sản phẩm thì **tặng** 3"* ⟺ **cấm** trạng thái *"trên 10 sản phẩm mà số quà ≠ 3"*

**Mọi "phải làm X" đều là "cấm trạng thái mà X chưa được làm".** Không có ngoại lệ.

---

# PHẦN 5 — MỘT RULE, TỪ ĐẦU TỚI CUỐI

Bám đúng ***"đơn trên 5 triệu phải OTP"***.

## 5.1 Viết nó

```
NOT (grand_total > 5000000) OR otp_verified
```

Đọc thành tiếng: ***"Không được tồn tại đơn nào mà tổng trên 5 triệu mà lại chưa OTP."***

**Không ngôn ngữ mới.** Đây là câu SQL. Ai biết SQL đều đọc được.

## 5.2 Đưa Postgres xem — nó trả lời **bốn** câu, không lấy tiền

**Câu 1 — "Viết đúng không?"**
Gõ bậy thì nó **không compile**. Bạn **không thể** viết rác mà vẫn qua. *(Chỗ này khác hẳn một cái cổng bắt điền form — form thì điền chữ "xong" là qua. Còn cái này phải **phân giải được vào schema thật**.)*

**Câu 2 — "Rule này đụng cột nào?"**

```sql
SELECT array_agg(a.attname) FROM pg_constraint c, unnest(c.conkey) k, pg_attribute a
WHERE c.conname='r_ord_017' AND a.attrelid=c.conrelid AND a.attnum=k;
```
```
{grand_total, otp_verified}
```

**Postgres tự tính ra.** Không ai phải khai *"rule này nói về cột nào"*. Đây là chuyện lớn — vì *"người viết rule quên khai nó đụng cột gì"* là lỗ hổng lớn nhất của mọi thiết kế kiểu này. **Ở đây lỗ đó không tồn tại, vì đó không còn là việc của con người.**

**Câu 3 — "Rule này đắt hay rẻ?"**
- Postgres **nhận** ⟹ rẻ ⟹ nó **tự cưỡng chế mãi mãi**
- Postgres **từ chối** ⟹ nó **nói lý do**, và lý do cho biết rule rơi vào bậc nào

**Câu 4 — "Ai cưỡng chế?"**
**Chính nó.** Không CI, không runner, không ai phải nhớ.

## 5.3 Bắt nó chứng minh **có RĂNG**

Rule phải đi kèm **hai dòng dữ liệu**:

| | Dòng | Phải ra gì | Đã chạy |
|---|---|---|---|
| **dòng phải NHẬN** | `(4.000.000, chưa OTP)` | nhận | ✅ |
| **dòng phải CHẶN** | `(9.000.000, chưa OTP)` | bị chặn | ✅ |

```
ERROR:  new row for relation "t_ord" violates check constraint "r_ord_017"
DETAIL:  Failing row contains (1, 9000000, f).
```

Nhìn dòng lỗi đó: nó **gọi đích danh rule** (`r_ord_017`) và **in ra dòng dữ liệu vi phạm**. Đó là **bản báo cáo QC, miễn phí, do DB in ra**.

**Vì sao bắt buộc phải có dòng thứ hai:** ai đó có thể viết `CHECK (true)` — **compile ngon lành, chẳng chặn gì cả**. Nhưng nó **không chặn nổi dòng thứ hai** ⟹ **lộ ngay lúc khai, không phải 6 tháng sau**.

Và phải bắt nó **gọi ĐÚNG TÊN**. Nếu dòng thứ hai bị chặn bởi **một rule khác** thì đó là **răng giả** — đỏ **sai lý do**. Postgres in tên constraint ra, nên bẫy này **bắt được bằng máy**.

## 5.4 Gắn vào hệ **đang sống**, dữ liệu **đã bẩn**

Đây là chỗ suýt giết cả ý tưởng:

```
ERROR:  check constraint "r_ord_017" of relation "t_ord" is violated by some row
```

Dữ liệu cũ đã có đơn 9 triệu chưa OTP ⟹ **migration chết** ⟹ ý tưởng **chỉ dùng được cho dự án mới** ⟹ **vô dụng cho ICP**.

**Lối thoát: thêm hai chữ `NOT VALID`.** Đã chạy:

```sql
ALTER TABLE t_ord ADD CONSTRAINT r_ord_017
  CHECK (NOT (grand_total > 5000000) OR otp_verified) NOT VALID;
-- gắn được lên data bẩn                          → OK
INSERT INTO t_ord VALUES (7000000, false);
-- ERROR: violates check constraint "r_ord_017"   → rác MỚI vẫn bị chặn
```

| | |
|---|---|
| rác **CŨ** | **được ghi nợ** — vẫn nằm đó, **đếm được**, dọn sau |
| rác **MỚI** | **không vào được nữa** — chặn ngay từ giây gắn rule |

> **Cầm máu hôm nay. Dọn nợ sau. Và món nợ là một CON SỐ, không phải một cảm giác.**

Đây là thứ làm ý tưởng dùng được cho **ICP thật**, không chỉ dự án mới.

---

# PHẦN 6 — MÂU THUẪN

Đây là ví dụ của human, và nó là ví dụ **hay nhất** vì nó đánh trúng chỗ yếu.

```
Rule 1 (khai tháng trước) :  "đơn trên 10 sản phẩm thì TẶNG 3 sản phẩm"
Rule 2 (khai hôm nay, quên mất Rule 1) : "đơn trên 10 sản phẩm thì KHÔNG tặng"
```

## 6.1 Câu hỏi SAI: *"hệ này có nghiệm không?"*

Trực giác tự nhiên: gộp hai rule thành một hệ, nếu **có nghiệm** thì OK. **Đã chạy:**

```
>>> HỆ CÓ NGHIỆM: item_count=5, gift_count=0 — CẢ HAI RULE ĐỀU THOẢ
```

**Hệ CÓ nghiệm.** Vậy theo cách thử này, hai luật **hợp lệ**. **Nhưng chúng mâu thuẫn thật.**

**Vì sao:** cả hai luật đều là *"NẾU trên 10 THÌ…"*. Nếu **không có đơn nào trên 10 sản phẩm** thì **cả hai đều đúng rỗng**. Hệ **thoát khỏi mâu thuẫn bằng cách giữ mọi đơn ≤ 10.**

> ## **Hai luật không cãi nhau. Chúng LẶNG LẼ XOÁ SỔ mọi đơn trên 10 sản phẩm.**
> ## **Và hệ vẫn "có nghiệm", vẫn xanh, không ai hay.**

## 6.2 Câu hỏi ĐÚNG: *"Rule mới vừa GIẾT MẤT cái gì?"*

Đếm số trạng thái hợp lệ, **trước** và **sau** khi thêm Rule 2. **Đã chạy:**

```
số sản phẩm trong đơn │ trạng thái hợp lệ TRƯỚC │ trạng thái hợp lệ SAU
─────────────────────┼─────────────────────────┼──────────────────────
         9           │            6            │          6
        10           │            6            │          6
        11           │            1            │          0    ◄── CHẾT
        12           │            1            │          0    ◄── CHẾT
        13           │            1            │          0    ◄── CHẾT
```

**Không ai khai *"đơn 12 sản phẩm phải khả thi"*.** Máy **tự tính ra**: trước khi có Rule 2, đơn 11+ sản phẩm có **đúng 1 cách hợp lệ** (tặng 3 quà — vì Rule 1 bắt vậy). Sau khi có Rule 2: **0 cách.**

## 6.3 Cái máy nói với bạn, ngay giây bạn gõ Rule 2

> 🔴 **Rule bạn vừa khai làm MỌI ĐƠN TỪ 11 SẢN PHẨM TRỞ LÊN không tồn tại được nữa.**
>
> **Trước:** mỗi đơn như vậy có **đúng 1** cách hợp lệ — *tặng 3 quà* — do **`r1_tren10_tang3`** bắt buộc *(quyết định **ADR-0xx, 12/06/2026**, lý do: "kích đơn lớn")*.
> **Sau:** **0** cách.
>
> **Bạn có định như vậy không?**
> **[a] Có** — đơn trên 10 sản phẩm bị cấm. Tôi ghi nhận.
> **[b] Không** — vậy rule mới **mâu thuẫn `r1`**. Chọn: **thay thế `r1`**, hay **sửa rule mới**?

**Đây đúng thứ human muốn:** phát hiện **lúc khai** · **human ra quyết định** chọn rule nào · **không AI**.

## 6.4 Chỗ hay nhất: **câu trả lời của bạn CHÍNH LÀ dữ liệu**

Khi bạn bấm **[b] "Không — đơn 12 sản phẩm PHẢI được"**, câu trả lời đó **được ghi lại vĩnh viễn**.

Từ giờ, **bất kỳ rule tương lai nào giết đơn 12 sản phẩm đều bị chặn TỰ ĐỘNG** — không ai phải nhớ gì nữa.

> **Bạn không phải nhớ khai *"đơn 12 sản phẩm phải được"*. Máy TỰ HỎI bạn, đúng lúc, kèm bằng chứng.**
> **Và bạn không đi tiếp được nếu không trả lời.** Nên nó **luôn được ghi** — không phải vì kỷ luật, mà vì **không có đường nào khác để đi tiếp**.

## 6.5 Thuật toán — 5 bước, không bước nào cần AI

```
① rule mới khai        → nó đụng cột nào?  {item_count, gift_count}   (Postgres cho)
② rule cũ nào cùng đụng mấy cột đó?  → r1                              (một câu JOIN)
③ đếm trạng thái hợp lệ TRƯỚC và SAU
④ có LỚP nào tụt về 0 → DỪNG, hỏi human, kèm bằng chứng                ◄ human quyết
⑤ human trả lời       → ghi lại vĩnh viễn                              ◄ không hỏi lại lần hai
```

---

# PHẦN 7 — CÁI THANG

Không phải rule nào cũng xuống được bậc rẻ nhất.

| Rule | Ai cưỡng chế | Tắt được? |
|---|---|---|
| *"đơn > 5tr phải OTP"* | **`CHECK` của Postgres** — mỗi lần `INSERT` | 🔒 **không** |
| *"paid không được quay về pending"* | **`TRIGGER`** — nó thấy cả giá trị cũ lẫn mới | 🔒 **không** |
| *"merchant không thấy đơn merchant khác"* | **`RLS`** — mỗi lần `SELECT` | 🔒 **không** |
| *"không được có bảng nào kiểu `platform_owned_stock`"* | truy vấn `information_schema` | ⚠️ phải có ai chạy |
| *"tổng đơn = tổng các dòng hàng"* | ⚠️ **một truy vấn** — `CREATE ASSERTION` **Postgres chưa cài** | ⚠️ phải có ai chạy |
| *"giá hiển thị phải realtime, không cache"* | ⚠️ đây là chuyện **CODE**, không phải data — cần quét mã nguồn | ⚠️ phải có ai chạy |

> ## **LUẬT DUY NHẤT: đẩy mọi rule xuống thấp nhất mà nó chịu xuống.**
> Ba dòng đầu **sống sót qua ngày sicp sụp hoàn toàn**. Ba dòng cuối **chết đúng ngày ai đó tắt CI để tiết kiệm tiền** — và **3 ngày sau không ai hay**.

## 7.1 🔑 **BẬC không phải tính chất cố định của rule. Nó là hệ quả của THIẾT KẾ SCHEMA.**

Cùng một rule, hai cách bày cột:

| Bày thế nào | *"đơn > 5tr phải OTP"* rơi vào bậc |
|---|---|
| `otp_verified` **cùng dòng** với `grand_total` | **rẻ nhất** — `CHECK`. Miễn phí, không tắt được. |
| OTP nằm ở **bảng riêng** | **đắt** — liên bảng ⟹ **phải có ai chạy** ⟹ **tắt được** |

> **Bạn chọn cột nằm ở đâu, là bạn chọn luôn rule đó có tự sống được hay không.**
> **Thiết kế schema không phải chuyện lưu trữ. Nó là chuyện quyết định luật nào tự sống, luật nào phải nuôi.**

---

# PHẦN 8 — CLAUDE ĐỨNG Ở ĐÂU

| Khoảnh khắc | Ai quyết | Claude? |
|---|---|---|
| Dịch *"đơn lớn phải OTP"* → biểu thức SQL | **Claude đề xuất, người phê** | ✅ **có** |
| Biểu thức có hợp lệ không | **Postgres** | ❌ |
| Rule đụng cột nào | **Postgres** | ❌ |
| Rule có răng không | **Postgres** | ❌ |
| Hai rule có mâu thuẫn không | **Postgres / Z3** | ❌ |
| 🔴 **Rule ĐANG bị vi phạm không** | **Postgres — TỪ CHỐI CÂU GHI** | ❌ **KHÔNG. BAO GIỜ.** |
| Rule có chết chưa *(nghiệp vụ đã đổi)* | **người** — thấy check đỏ rồi phán *"không phải bug, rule chết rồi"* | ✅ có |

> ### **Claude đứng ở HAI ĐẦU — lúc rule ra đời, và lúc rule chết.**
> ### **Ở GIỮA — suốt đời rule, hàng triệu lần ghi — Claude KHÔNG CÓ MẶT.**

**Vì sao tuyệt đối không được để Claude ở giữa:**

- **tốn tiền mỗi lần kiểm** ⟹ gom theo lô ⟹ bỏ bớt ⟹ **mục**
- **không lặp lại được** ⟹ cùng rule, hôm nay bảo ổn, mai bảo không
- **không nằm trên đường tới hạn** ⟹ **tuỳ chọn** ⟹ mục

**Có bằng chứng lịch sử cho đúng cái này:** năm 1997 IBM + OMG chuẩn hoá **OCL** — một ngôn ngữ viết ràng buộc nghiệp vụ, gắn với UML. Nó cần **một trình kiểm tuỳ chọn** chạy. Sau **29 năm**, tài liệu ACM ghi: *"việc sử dụng OCL trong công nghiệp **gần như bằng không** — kể cả trong chính cộng đồng phát triển ứng dụng nghiệp vụ mà nó được thiết kế cho."*

**18 luật văn xuôi của sicp CHÍNH LÀ OCL** — cần một con người đọc và phán, trình kiểm tuỳ chọn. Và nó nhận **đúng kết quả của OCL: 15/18 chỉ là lời khuyên**.

## 8.1 Khi máy **bí** thì sao?

**Đừng hỏi Claude *"có mâu thuẫn không?"*** — được một câu trả lời **có thể sai, không lặp lại được, không có bằng chứng, và trông giống hệt câu trả lời của máy**.

**Hỏi Claude: *"viết lại rule này thế nào để MÁY giải được?"***

Ví dụ — *"giá phải realtime, không cache"*. Máy bí, vì "được-cache" không phải một cột.
> Claude nói: *"rule này nói về cấu trúc code. Muốn máy giải được thì thêm cột `price_source ∈ {live, cached}`, rồi `CHECK (price_source = 'live')`."*
> ⟹ **Rule tụt từ bậc đắt nhất xuống bậc rẻ nhất. Nợ TRẢ XONG.**

**Tầng rơi phải TRẢ NỢ, không phải GIẤU NỢ.**

Và nếu Claude **không kéo nổi** ⟹ rule mang **nhãn vĩnh viễn, đếm được, và già đi**:
```
r_cat_004   CHƯA CHỨNG MINH — Claude đoán 15/07/2026
```
**Nhãn đó tuyệt đối không được trông giống một rule đã chứng minh.**

---

# PHẦN 9 — NỐI VÀO SLICE / TASK

> ## **BẬC là thứ ĐẺ RA VIỆC.**

```
rule chưa có gì kiểm     ⟹ đó LÀ việc phải làm      ⟹ sinh ra task
rule phải-có-ai-chạy     ⟹ còn nợ                    ⟹ sinh ra task
rule ở bậc Postgres tự lo ⟹ XONG. Không đẻ việc nữa. Vĩnh viễn.
```

> **TASK** = một nước đi **đẩy MỘT rule xuống bậc thấp hơn**
> **SLICE** = lời hứa **đẩy MỘT NHÓM rule xuống bậc có bằng chứng**
> **XONG** = mọi rule slice đó hứa **đều đã có bằng chứng sống**

⟹ **Backlog thôi là một danh sách.** Nó là câu hỏi: *"rule nào còn chưa xuống được bậc có bằng chứng?"* — **một câu truy vấn**.

⟹ **`S-RETURNS-01` chết đúng ở đây:** rule của nó **chưa từng được khai** ⟹ slice đó **về mặt logic KHÔNG THỂ xong**. Nhưng sicp đo **ô tick**, nên nó **xanh**.

> **Tiến bộ không phải "bao nhiêu ô đã tick". Là "bao nhiêu rule đã tụt xuống bậc mà KHÔNG AI CÒN PHẢI CHĂM NÓ NỮA".**

---

# PHẦN 10 — MASTER vs CLIENT

**Cùng một cỗ máy. Khác nhau ở chỗ: biểu thức được kiểm vào SCHEMA NÀO.**

| | **client-planner / client-coder** | **master-planner / master-coder** |
|---|---|---|
| Viết rule về | **nghiệp vụ của khách** | **chính workflow** (cái sicp gọi là *"luật"*) |
| Biểu thức kiểm vào | schema của khách | **schema của chính IWF** |
| Ví dụ | `NOT (grand_total > 5e6) OR otp_verified` | `verdict.author ≠ task.coder` |

> Luật sicp ***"coder không tự chấm bài mình"*** hôm nay là **văn xuôi, advisory, sống bằng kỷ luật**.
> Viết dạng này, nó là một **trigger** — tra `task.coder`, thấy trùng thì **từ chối**. **Không tắt được.**
> **Cùng cỗ máy phục vụ rule của khách sẽ cưỡng chế luật của chính IWF — không thêm một dòng cơ chế nào.**
> **Đó là nội dung thật của "IWF tự phát triển IWF".**

**Ba việc chỉ master làm được** *(vì cần toàn bộ kho rule, và không cần code)*:

1. **Dịch văn xuôi → biểu thức.** *"KHÔNG backorder"* → neo thật là `inventory.available` + `orders.status`. **Chữ trong câu không hề chứa hai cột đó.** Máy so chuỗi **câm**. Cần một Claude **đọc câu + đọc schema**.
2. **Nhìn N project.** *"47 tenant khác cũng vấp đúng xung đột tặng-quà này. 44 chọn thay rule cũ."* Client **không thể biết** — nó chỉ thấy một project. **Đây là flywheel.**
3. **Viết luật cho chính IWF.**

**Một điều KHÔNG đổi:** **cưỡng chế luôn xảy ra ở nơi có DATA.** Rule của khách chạy trong **DB của khách**. Migration giao xuống **một lần**, rồi **master tắt cũng chẳng sao**.

⟹ **Master chỉ cần sống để KHAI và BIÊN DỊCH. Không cần sống để CƯỠNG CHẾ.**

---

# PHẦN 11 — CÁI NÀY **KHÔNG** LÀM ĐƯỢC

Nói thẳng, vì bạn sẽ tự phát hiện ra.

### 11.1 `CHECK` gác BẢN GHI, không gác HÀNH VI

*"Đơn > 5tr phải OTP"* dưới dạng lọc thật ra nói: *"không được có **DÒNG** nào total>5tr mà `otp_verified=false`"*.

**Code hoàn toàn có thể ghi đại `otp_verified = true` mà chưa bao giờ hỏi OTP.** Lọc **xanh**. **Không ai bị hỏi OTP.**

**Vá được một phần:** đặt trigger — `otp_verified` chỉ được lật lên `true` **nếu tồn tại một dòng `otp_challenge` đã xác thực**. **Lời nói dối thành bất khả.**
**Nhưng:** code lại có thể ghi đại vào `otp_challenge`. Đẩy tiếp: chỉ dịch vụ OTP mới được `GRANT` ghi bảng đó. Rồi lại hỏi ai gác dịch vụ OTP…

> **Không xoá được lòng tin. Chỉ dồn được nó về MỘT chỗ, thu nhỏ lại, và GỌI TÊN nó.**
> Vẫn tốt hơn hôm nay — hôm nay lòng tin **rải khắp mọi dòng code và không ai biết nó nằm đâu**.

### 11.2 Rule phi tuyến thì bó tay

`c * g > 100` (nhân **hai biến**) ⟹ **không có thuật toán nào quyết được** (kết quả toán học, Hilbert bài 10).
⟹ **Ngôn ngữ rule phải CẤM nhân hai biến.** Đó là **cái giá thật** của việc "luôn trả lời được", và nó **không thương lượng**.

### 11.3 `CREATE ASSERTION` không tồn tại

```
ERROR:  CREATE ASSERTION is not yet implemented     -- PostgreSQL 16.14
```
SQL:1999 **đã đặc tả** cấu trúc này cho đúng loại rule liên bảng. **Postgres — và gần như mọi RDBMS — chưa từng cài.**
⟹ *"tổng = tổng dòng hàng"* **không thể miễn phí**. Đây là **lỗ hổng 25 năm của cả ngành**, không phải khuyết điểm của thiết kế này.

### 11.4 Vét cạn có biên

Bảng đếm ở Phần 6 vét **126 trạng thái** (`0..20` sản phẩm × `0..5` quà). Miền thật (`bigint` = 4 tỉ giá trị) **không vét nổi**.

| Ca | Máy | |
|---|---|---|
| miền nhỏ / luật nêu hằng số cụ thể | **Postgres tự vét** | miễn phí |
| tổng quát | **Z3** | **không vét — CHỨNG MINH**, và chỉ đích danh luật nào cãi nhau |

**Cả hai đều là công nghệ đã thắng, không phải ý tưởng của tôi:** vét-có-biên **chính là Alloy** · dùng solver **chính là Cedar** — ngôn ngữ phân quyền của AWS, **đang chạy production**, mà tài liệu ghi rõ: *"symbolic compilation… reduces policies to SMT formulas… sound, complete, and **decidable**, allowing **proof that two sets of policies are equivalent**."*

### 11.5 Lỗ tôi **chưa** vá

Trạng thái mà nghiệp vụ **đòi phải có**, **chưa từng xảy ra**, và **không ai nghĩ ra để khai**. Dữ liệu thật không có, seed không có.
**Cơ chế "vừa giết mất cái gì" ở Phần 6 vá được PHẦN LỚN** — vì nó **tự tính ra** vùng chết. Nhưng nó chỉ tính được **trong không gian nó biết**.

---

# PHẦN 12 — NƯỚC ĐI KẾ, NHỎ VÀ **SAI ĐƯỢC**

Đừng xây gì cả.

**Lấy MỘT business rule THẬT của ICP** → viết thành biểu thức → `NOT VALID` nó lên DB thật → **xem cái gì vỡ**.

Vì có một câu **chưa ai biết câu trả lời**, và nó quyết định mọi thứ:

> ## **Dữ liệu thật của ICP đang vi phạm bao nhiêu luật mà không ai biết?**

- Gắn được sạch ⟹ ý tưởng **đứng**
- Lòi ra hàng nghìn dòng vi phạm ⟹ ta vừa học được **điều quan trọng nhất về ICP** — và nó **rẻ hơn mọi tài liệu thiết kế**

*(`icp-postgres` đang tắt 33 giờ. Bật lại được bất cứ lúc nào.)*

---

# PHỤ LỤC — Mọi thứ trong file này **đã chạy thật**

| Khẳng định | Bằng chứng |
|---|---|
| Rule thành `CHECK`, đỏ đúng lý do, gọi tên | `violates check constraint "r_ord_017"` · `Failing row contains (1, 9000000, f)` |
| Postgres tự tính neo | `conkey` → `{grand_total, otp_verified}` |
| Postgres tự xếp bậc | `ERROR: cannot use subquery in check constraint` |
| Rule về schema cũng là SQL, răng 2 chiều | `information_schema` → `t` · thêm bảng cấm → `f` |
| `CREATE ASSERTION` không có | `ERROR: CREATE ASSERTION is not yet implemented` (PG 16.14) |
| Data bẩn chặn migration | `ERROR: is violated by some row` |
| `NOT VALID` gắn được + vẫn chặn rác mới | gắn OK · `INSERT (7tr, false)` → chặn |
| Hệ 2 rule mâu thuẫn **CÓ nghiệm** | `item_count=5, gift_count=0` — cả hai thoả |
| Đếm vùng chết | `11→0` · `12→0` · `13→0` |
| sicp đã có 817 rule đang chạy | `131 CHECK` · `107 REFERENCES` · `471 NOT NULL` · `108 UNIQUE` |

**Mọi thí nghiệm chạy trong transaction + `ROLLBACK`. Không đụng một dòng dữ liệu nào.**
