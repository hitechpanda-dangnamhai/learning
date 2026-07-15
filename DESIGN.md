# IWF — KIẾN TRÚC

> # 🔴🔴🔴 TRẠNG THÁI: **ĐANG PHÂN TÍCH DỞ — CẦN PHÂN TÍCH THÊM** 🔴🔴🔴
>
> ## ❌ **KHÔNG phải quyết định cuối cùng.**
> ## ❌ **KHÔNG phải thông tin để dựa vào.**
> ## ❌ **KHÔNG phải nguồn sự thật.**
> ## ⏳ **Đây là một BẢN NHÁP ĐANG MỞ. Human 2026-07-15 nói rõ: chưa chốt gì cả.**
>
> **Viết bởi planner, 2026-07-15, trong một phiên bàn thiết kế. CHƯA CHẠY MỘT NGÀY NÀO.**
> **Bằng chứng file này chưa đáng tin: `§6` liệt kê 11 giả thuyết BỊ BÁC — 4 do human bác, 5 do CHÍNH PLANNER tự bác SAU KHI đã viết chúng ra như kết luận. `§1` và `§2` của bản đầu đã bị XOÁ SỔ hoàn toàn.**
> **CẤM dùng file này làm căn cứ để khẳng định bất cứ điều gì.**
>
> Luật quyền-uy của chương trình (`PROGRAM.md`): **quyền-uy kiếm bằng CHỨNG-MINH-QUA-SỬ-DỤNG, không phải bằng ĐƯỢC-VIẾT-RA.** File này **1 ngày tuổi**. Nó **không thắng** bất cứ thứ gì đang chạy.
>
> **Tiền lệ (đọc trước khi tin file này):** trong **chính phiên viết ra nó**, planner đã bị human bác **6 lần**, và **§1 + §2 của bản đầu bị xoá sổ hoàn toàn**. Xem **§6 MỘ**.
>
> **Nhãn:** `[HUMAN]` = lời human, nguyên văn · `[ĐÃ CHẠY]` = có lệnh tái lập · `[SUY]` = lập luận planner, **có thể sai**.
>
> **Nhà của cơ chế RULE = `RULE.md`.** File này chỉ nói **kiến trúc**.

---

# §0. VÒNG 0 — CHỦ THỂ + BẤT BIẾN

**CHỦ THỂ:** IWF = một **workflow-sản-phẩm** — engine ở server + tác nhân hai phía.
**Không phải sicp** (sicp = nguồn ý tưởng đã-chứng-minh + **nhóm chứng**). **Không phải awf** (bản dựng dở; **docs của nó do planner viết khi hiểu sai** — human: *"không tin file đã tạo trước ở AWF"*).

**BẤT BIẾN** `[HUMAN]` — lấy từ **lời human**, *không* từ file planner tự viết:

| # | |
|---|---|
| **B1** | relay + wf-lock **chạy ở IWF (server)**; client push/get, **client không lưu** — lưu = **lộ bí mật công nghệ + tổ chức** |
| **B2** | Client chỉ giữ **`code + git + hạ tầng`**; **tài liệu phân tích tuyệt đối không ở client**, cần thì gọi API |
| **B3** | Mục đích: **nắm trọn vẹn + truy xuất + phân tích + hiểu + lưu business domain thống nhất, và PHẢN BIỆN khi business mâu thuẫn** |
| **B4** | **Claude phán, engine phục vụ** |
| **B5** | Quan hệ `domain · rule · field · slice · task` ⟹ context **đầy đủ mà không phình vô ích** |
| **B6** | **slice/task chỉ là TRUNG GIAN**; chỉ quan tâm **business rule có HOẠT ĐỘNG không**; rule stale → **bỏ, làm rule mới** |
| **B7** | **coder không tự chấm bài mình** — coder khai → relay → **planner phán bằng Claude** |
| **B8** | Nâng cấp **TỪ sicp** — sicp có basic rất tốt, **không vứt** |
| **B9** | **Không tin file đã tạo trước ở awf** |
| **B10** | Đích: **world-class + thương mại hoá** |

**VẬT LÝ ĐÃ ĐO** `[ĐÃ CHẠY]` (`STATE.md §8`) — thiết kế nào cãi mấy dòng này thì **loại ở vòng 0**:

> **Tri thức chỉ sống ở đúng chỗ mà công việc SẼ DỪNG nếu thiếu nó.**
> `dispatch` (**không cổng**) = **20/20**. `report` (**không cổng**) = **2/10**. Cùng repo, cùng ngày, cùng người.
> Khác biệt **duy nhất**: coder **không làm được việc** nếu thiếu dispatch; planner vẫn `accepted` được với report rác.
> `verdict` (**CÓ cổng**) = **132/132 có mặt, 76,5% rỗng nghĩa**.
> ⟹ **Cổng mua SỰ CÓ MẶT. Chỉ kẻ tiêu thụ VỠ VÌ SAI mới mua SỰ THẬT.**
> ⟹ `CLAUDE.md` nạp **21 agent × mỗi phiên** và vẫn mục ở §3/§10/§11/§12. **Được nạp ≠ được đọc ≠ được làm.**

---

# §1. SÁU TÁC NHÂN

> ⚠️ Bản đầu của file này định nghĩa `master-claude` là **"một thành phần sản phẩm — bộ suy luận trên tri thức"**.
> **Human bác thẳng.** Nó **không phải** vậy. Xem **§6 MỘ #1**.

**Đối xứng đúng — hai NƠI, mỗi nơi một CHẤT NỀN và hai VAI:** `[HUMAN]`

| Nơi | **Chất nền** | Vai chạy trên nó | Nhìn thấy | **Không bao giờ thấy** |
|---|---|---|---|---|
| **máy khách** | **client-claude** | client-planner · client-coder | **code của khách** | corpus · hiến pháp · tenant khác |
| **tại IWF (server)** | **master-claude** | master-planner · master-coder | **corpus · vốn từ vựng · N project** | 🔴 **code của khách** |

`client-claude` phục vụ **đúng project của khách đó**. `master-claude` là **Claude chạy ở IWF**. **Cùng một hình, khác CHỖ ĐỨNG và khác CÁI NHÌN THẤY.**

**master-planner/coder = client-planner/coder + một GRANT.** `[SUY]` Không phải phần mềm thứ hai — **cùng binary**, khác đúng **cột `audience`** (`internal` vs `client`).
`[ĐÃ CHẠY]` Cột đó **thiếu thật**: `wf_record` có `scope`, **không có `audience`**.
⟹ **Chính sự phân biệt master/client là thứ ép cột đó ra đời.**

## 1.1 Vì sao master-planner **mạnh hơn** planner sicp — có cơ chế, không phải lời khen `[SUY]`

| | planner sicp **hôm nay** | master-planner |
|---|---|---|
| Số project nhìn thấy | **1** | **N** |
| Corpus | markdown rải rác · memory **ngoài git** · seam **trong Redis** | **truy vấn được** |
| Cách tra | `grep` | join |
| Tri thức workflow | **1.774 dòng NGOÀI repo** > 856 dòng trong | có nhà |

**`N project > 1 project` là nguồn gốc thật của "tối ưu hơn"** — và là **moat không mua được bằng tiền, chỉ mọc theo thời gian**.

## 1.2 Neo quan trọng nhất `[HUMAN]`

> *"(công việc mà chúng ta đang làm để tạo ra)"*

**Việc human + planner đang làm ngay lúc này CHÍNH LÀ việc của master-planner** — chỉ là đang làm **bằng tay, ở sai chỗ, trên sai chất nền**: một planner ngồi `grep` markdown, đọc memory ngoài git, đọc seam trong Redis, để phân tích một workflow **không được viết ở đâu cả**.

⟹ **Spec của master-planner không trừu tượng. Nó là: làm cho ĐÚNG PHIÊN NÀY rẻ hơn, chắc hơn, ít vấp hơn.** Bằng chứng có sẵn: **§7 dưới đây liệt kê 3 lỗi planner phạm trong chính phiên này**, và phiên trước có **7 lỗi, cả 7 cùng một hình**.

---

# §2. CÁC TẦNG DATA

> Đặt tên theo **cái nó giữ**. Cột **"vỡ khi sai"** là cột quan trọng nhất: tầng nào **không có ai vỡ vì nó** thì **sẽ mục**, bất kể schema đẹp cỡ nào.

| Tầng | Giữ gì | Ở đâu | **Vỡ khi sai?** |
|---|---|---|---|
| **HIẾN PHÁP** *(IP của IWF)* | persona · law · skill · template. `audience: internal\|client` | **MASTER** | ✅ client-coder **không làm được việc** nếu payload sai |
| **VỐN TỪ VỰNG** *(IP của KHÁCH)* | **tình huống có tên** (view) · **rule** · quyết định | **MASTER**, tenant-scoped | ✅ **view CHẠY ĐƯỢC** ⟹ không mục *(xem `RULE.md §3.2`)* |
| **ẢNH GƯƠNG** *(IP của khách)* | schema · route · symbol. **KHÔNG data thật** | **MASTER**, tenant-scoped | ✅ sai field ⟹ biểu thức **không compile** |
| **CÔNG VIỆC** *(trung gian — B6)* | slice · task · **dispatch** · report · verdict · lock | **MASTER** *(= relay, B1)* | ✅ **đây là tầng 20/20** |
| **BẰNG CHỨNG** | check · run · **attribution** | định nghĩa ở **MASTER**, **CHẠY Ở CLIENT** | ✅ đỏ/xanh nhị phân |
| **CODE + GIT + HẠ TẦNG** | mã nguồn | 🔴 **CLIENT — MASTER KHÔNG BAO GIỜ THẤY** | ✅ compile/test |

## 2.1 Đường biên — **SIGNATURE**: thứ **DUY NHẤT** đi từ client lên master `[SUY]`

```
client  ──[ signature ]──►  master
   field-set đã chạm (bảng.cột)      ← engine chỉ cần FIELD, không cần CODE
   symbol-set · route-set
   check verdicts (id · đỏ/xanh · lý do)
   rule / tình huống do planner khai
   ─────────────────────────────────
   ❌ KHÔNG BAO GIỜ: mã nguồn
```

**Vì sao bắt buộc:** (a) `B2` — code là của khách · (b) **thương mại**: khách enterprise **sẽ không bao giờ** đẩy source lên server bạn, và *"chúng tôi không bao giờ thấy code của bạn"* là câu **bán được** — nhưng **chỉ đúng nếu wire là signature, không phải diff**.

**Hệ quả cứng:** **check phải CHẠY Ở CLIENT** (code ở đó), **master quyết CHECK NÀO và VÌ SAO**.

⚠️ **Đây là tầng CHƯA TỒN TẠI, và cả thiết kế treo lên nó.** Và awf hiện đang đi **ngược hướng**: `POST /verify` **pipe diff qua HTTP**. Seam `wedge-scope-needs-postimage` đã đo: **răng sắc nhất cần CÂY REPO, không phải diff** ⟹ giao qua HTTP thì nó **không chạy, và im lặng**. **Diff-lên là sai cả hai hướng.**

## 2.2 🔑 Cưỡng chế được GIAO XUỐNG khách — master tắt vẫn chạy `[SUY]`

Rule bậc thấp compile thành **migration**, giao xuống DB khách **một lần**. Từ đó **nó tự sống**.

⟹ **Giết phản đối nặng nhất của chính planner** (*"master trên đường tới hạn ⟹ master chết là 20 coder dừng"*).
**Master chỉ cần sống để KHAI và BIÊN DỊCH. Không cần sống để CƯỠNG CHẾ.**

---

# §3. TẦNG CÔNG VIỆC — bê nguyên vật lý đã thắng của sicp

Bốn cơ chế của sicp **không phải thiết kế — là hệ quả vật lý**. Vật lý không đổi ⟹ **giữ nguyên**:

| Vật lý | Cơ chế | Ở IWF |
|---|---|---|
| planner ⊥ coder = 2 phiên không nói chuyện được | **relay = ống duy nhất** | ống ở **MASTER** (`B1`) |
| phiên **chết** (exit/compact) | **state ngoài phiên** | ống = bộ nhớ ngoài, qua API |
| N coder **một** cây git | **wf-lock** | ⚠️ xem lỗ dưới |
| **chỉ human mở được terminal** | ▶RUNNABLE + chuông | giữ — **human = SPAWNER (vật lý)**; **human = CỔNG (ngẫu nhiên, GỠ)** |

**Form 5 lớp** `[BỐI CẢNH][VIỆC][RÀNG BUỘC][OUTPUT][THẤT BẠI]` — **giữ, không chế lại.** Artifact **duy nhất** trong sicp đạt **100% mà chưa từng có cổng**. Ở IWF nó là **schema của payload**, không phải văn xuôi.

## 3.1 🔑 Việc thật của IWF ở tầng này `[SUY]`

**Coder sicp hiểu business bằng gì?** Bằng ô `[BỐI CẢNH]` — **một đoạn văn planner GÕ TAY**.
`[ĐÃ CHẠY]` Đọc dispatch thật: slot-13 = *"Bạn là coder AGENTFIX (context nóng, vừa xong T09)"*. **Đó là toàn bộ cơ chế.**

> **Hiểu-biết business của coder = những gì planner NHỚ RA để gõ, tại đúng khoảnh khắc đó.** Mà planner cũng đang **nhớ**, không tra.

⟹ Đó là lý do `dispatch 20/20` **lừa**: con số đó đo **form có đủ 5 lớp không**. Nó **không đo `[BỐI CẢNH]` có ĐÚNG không**. **Form thì máy đếm được. Trí nhớ thì không.**

> ### **Việc của IWF không phải "làm cho coder hiểu". Là: ô `[BỐI CẢNH]` thôi được NHỚ, mà được SUY RA.**
> Ống **đã có sẵn, đã 100%** — coder **không lấy được việc nếu thiếu dispatch** ⟹ **thứ gì cưỡi trên dispatch thì cũng luôn tới nơi.**
> **Không cần phát minh kênh mới. Cần đổi RUỘT của kênh đã thắng.**

Planner vẫn tự viết `[VIỆC]` + `[RÀNG BUỘC]` (**ý định — không suy được**). `[BỐI CẢNH]` business **do engine dựng từ vốn từ vựng**.

## 3.2 Phép đo miễn phí rơi ra `[SUY]`

```
(neo MÁY suy từ diff lúc đóng)  −  (neo PLANNER khai lúc mở)  =  CHỖ PLANNER MÙ
```
Rule hiện ở vế trái mà planner **không khai** ⟹ coder đã code **mù rule đó** ⟹ **rework**. **Planner trả giá THẬT, không phải bằng lời nhắc** — và có **bản đồ chỗ mù của chính nó, cập nhật sau mỗi task**.

## 3.3 🔴 Lỗ vật lý phải quyết `[SUY]`

`wf-lock` bảo vệ **cây git của CLIENT**. `B1` bắt lock ở master ⟹ mỗi `git commit` = **một vòng lên server**; **master chết = client không commit được**.
Header `wf-lock` **đã tự ghi điều kiện hết hạn của chính nó**: *"Single Redis… add fencing tokens/etcd **only if this ever becomes correctness-critical for a product**"*.
> **Chọn mô hình này LÀ kích hoạt điều kiện đó — ngày 1, không phải để sau.**

---

# §4. CƠ CHẾ CHỐNG MỤC — **ATTRIBUTION** `[SUY]`

Áp `§8` lên **chính thiết kế này**:

| | Sai thì ai vỡ? |
|---|---|
| client-coder sai | check đỏ → **vỡ ngay** ✅ |
| client-planner sai | coder không làm được / bị bác → **vỡ** ✅ |
| **tầng master sai** | 🔴 **KHÔNG AI.** Vốn từ vựng tệ đi một chút. Tuần sau tệ thêm chút. |

> **Tầng MASTER, về cấu trúc, LÀ phía `report` của sổ cái — mà `report` đo được là 2/10.**
> Nó **sẽ mục theo mặc định**, đúng cách `memory` (1.774 dòng, ngoài git, tự vi phạm luật của nó), `LOG_CATALOG` (31 emit vắng, **0 cổng**), `seam` (67, không schema, **resolve = tự huỷ**) đã mục.
> **Schema đẹp không cứu. Cổng không cứu — cổng chỉ mua sự-có-mặt.**

**⟹ Phải CHẾ TẠO kẻ vỡ:**
```
serve  → bundle_id  (ghi ĐÚNG những record nào đã phục vụ)
work   → client làm
verdict→ TRỎ NGƯỢC về bundle_id
```
Từ đó **suy ra bằng query, không phải bằng ý kiến**: rule liên tục sinh **rework** ⟹ khả nghi · rule **chưa từng được phục vụ** ⟹ xác chết · rule **không có bằng chứng** ⟹ *"tôi không biết"*.

> **Đây là `gen-facts` áp cho tầng tri thức.** `gen-facts` thắng **không phải vì nó KIỂM FACTS** — mà vì nó làm **FACTS không thể tồn tại tách rời code**. Attribution làm ***"rule này tốt không"* không thể tồn tại tách rời công việc mà nó đã tham gia.**

🔴 `[ĐÃ CHẠY]` **Cảnh báo:** `wf_dispatch` hiện **PK = `(project_id, slot)`** ⟹ **UPSERT ĐÈ**. Nó đã thừa hưởng đúng chứng quên của sicp (`consume` **`DEL`** dispatch ⟹ **481 câu trả lời giữ / 481 câu hỏi huỷ**). ⟹ **Tầng công việc phải APPEND-ONLY.**

**Giá: ≈ 0 nếu làm ngày 1** (một `bundle_id` + một FK). **Gần như không retrofit được** — retrofit thì **không có lịch sử, mà lịch sử CHÍNH LÀ dữ liệu.**

---

# §5. CÁI IWF THÊM MÀ sicp **KHÔNG THỂ CÓ** `[SUY]`

Ở khách, **tài liệu phân tích không nằm trên đĩa** (`B2`). Slice · law · DoD · rule — **không cái nào ở đó**. Đường **duy nhất** để coder lấy việc là **gọi API**.

> ### **Engine nằm trên đường tới hạn THEO CẤU TẠO, không phải theo kỷ luật.**
> Đó chính là vật lý đã đẻ ra `dispatch = 20/20`, **tổng quát hoá cho mọi loại tri thức**.
> **sicp không bao giờ đạt được ở nhà**: file nằm ngay trên đĩa, agent *không cần* `CLAUDE.md` vẫn làm xong task — **nên nó mục**.
> **Nếu CÙNG MỘT ỐNG chở cả task lẫn luật, thì bỏ qua luật = bỏ qua task.**

⚠️ **Và vì thế: self-host KHÔNG BAO GIỜ test được đường giao hàng** (file-rỗng + API + lọc `audience`) — master đọc thẳng DB. **Chỉ khách thật / sicp-làm-client mới chứng minh được.**
⚠️ **Nhóm chứng biến mất đúng lúc cutover:** sicp là thước đo IWF; khi sicp **thành client** thì **không còn sicp-không-IWF để so**. ⟹ **baseline phải đóng gói TRƯỚC** — và `STATE.md` **chính là baseline đó, đã đóng băng**. Đó là thứ 40 vòng của phiên trước thật sự mua được.

---

# §6. 🪦 MỘ — giả thuyết BỊ BÁC **trong chính phiên viết ra file này**

**Đọc mục này TRƯỚC khi tin bất cứ dòng nào ở trên.**

| # | Giả thuyết | Ai bác | Vì sao |
|---|---|---|---|
| **1** | *"`master-claude` = thành phần sản phẩm mới, bộ suy luận trên tri thức, phục vụ runtime"* — **§1+§2 bản đầu** | **HUMAN** | **Sai nền.** `master-claude` = **CHẤT NỀN** (Claude chạy ở IWF), đối xứng với `client-claude` ở máy khách. Planner **đã suy đúng lúc đầu rồi tự vứt đi** để nhét vào một khái niệm human **chưa bao giờ nói**. |
| **2** | *"recall=1 đạt ở master bằng LIỆT KÊ ⟹ B5 hết là đánh đổi"* — **luận đề §1 bản đầu** | **chính planner, 2 lần** | (a) nếu quan hệ **được khai** thì liệt kê là **một câu JOIN** — **join không có trần context** để mà vượt · (b) `master-claude` **không nằm trên đường phục vụ request**. **Chết hai lần.** |
| **3** | *"Lấy RĂNG của awf cắm vào NÃO của sicp"* | số đo `§8` | răng = cổng = **chỉ mua sự-có-mặt**. sicp **không thiếu răng — nó thiếu KẺ PHỤ THUỘC**. Đây là **liều thứ hai của ADR-045** và là **bẫy trung tâm**. |
| **4** | *"Truy xuất tốt hơn (rerank/hybrid/graph-DB) giải được B5"* | — | xếp-hạng-theo-tương-tự **LÀ đoán**. `per_kind_cap` sai không vì cap thấp, mà vì **cắt một PHỎNG ĐOÁN thay vì ghi lại một SỰ THẬT**. |
| **5** | *"rule = một vị từ trên MỘT DÒNG"* | **HUMAN** | business rule thật **nhiều bảng · nhiều tình huống · tình huống này đẻ ra tình huống kia**. `CHECK` **viết không nổi một chữ**. ⟹ **TÌNH HUỐNG** thành khái niệm trung tâm (`RULE.md §3`). |
| **6** | *"`domain` chỉ là NHÃN, cấm gánh việc, vì phân loại-tay thì mục"* | chính planner | **Sai.** `domain` = **vốn từ vựng = tập tình huống có tên = view CHẠY ĐƯỢC** ⟹ **không phải phân loại tay, là CODE, không mục được.** |
| **7** | *"Claude TUYỆT ĐỐI không được ở đường kiểm"* | **HUMAN** | **Quá tuyệt đối.** Có **HAI loại** mâu thuẫn: **LOGIC** (máy, có bằng chứng) và **NGỮ NGHĨA** (*"giá hiển thị"* vs *"giá khách VIP thấy"* — **máy CÂM**). Loại 2 **cần Claude**. |
| **8** | *"nhân chứng = lỗ CUỐI CÙNG, không vá được"* | chính planner | Hỏi **sai câu**. Không hỏi *"hệ có nghiệm không"* — hỏi ***"rule mới vừa giết mất cái gì"***. **Tính được, không cần ai khai trước.** Và **câu trả lời của human CHÍNH LÀ nhân chứng.** |
| **9** | *"neo mục âm thầm khi đổi tên field — không vá được"* | `[ĐÃ CHẠY]` | **view BẢO VỆ cột của nó**: `cannot drop column paid… view don_hoan_thanh depends on it`. **Miễn phí.** |
| **10** | *"recall = 1"* | chính planner | **SAI.** Đúng ra: recall=1 **trong tập rule có neo XUẤT HIỆN TRONG DIFF**. **Hành động từ xa** (cache CDN · `npm update` · seed data) **làm vỡ rule mà không chạm neo** — mù hoàn toàn, im lặng. |
| **11** | *"P5 sập vì fan-out"* | `[ĐÃ CHẠY]` | `orders` = **10 cột**, task chạm 2–4 ⟹ ~10–15 rule, **không phải 200**. `P5` **sống** — nhưng **có điều kiện hết hạn** ở quy mô `ADR-063`. |

> ### **11 giả thuyết bị bác. 4 do human bác. 5 do chính planner tự bác — SAU KHI đã viết chúng ra như kết luận.**
> **Đó là lý do file này mang nhãn "đang phân tích, không phải nguồn sự thật".**

---

# §7. NHẬT KÝ SAI CỦA PLANNER — phiên này

| Lỗi | Human chặn bằng câu |
|---|---|
| Được lệnh **THIẾT KẾ IWF**, bỏ đi **ĐO awf** để cãi chuyện giữ/bỏ | *"chưa thiết kế ra được workflow iwf thì làm sao biết cần giữ cần bỏ cái gì"* + *"tại sao cãi lời tôi vậy"* |
| **Dán tên human lên phỏng đoán của mình** (*"câu này của bạn đã trả lời §9.1 — theo hướng của bạn, không phải của tôi"*) — trong khi câu đó **không chứa một chữ nào về topology** | *"tôi không hiểu bạn nói gì ở đoạn văn này, tôi thấy có vẻ sai"* |
| **Tự đạp đổ luận đề §1 ở tin nhắn kế tiếp mà không nhận ra** | — *(planner tự phát hiện, muộn)* |
| Trình bày bằng ký hiệu `§` + jargon, bắt human tra chéo 10 tin nhắn | *"hoàn toàn không hiểu"* |

**Lỗi 1 CÙNG MỘT HÌNH với `STATE.md §12 T8 #7`** (phiên-2 lạc chủ thể sang audit `gate-*`; human chặn *"bạn đang làm gì vậy?"*).
⟹ `CLAUDE.md §14b` **đã ghi sẵn**: *"human hỏi 'sao cứ lấy X ra vậy?' = **tín hiệu ĐỎ**, không phải câu hỏi tu từ."*
⟹ **Hai phiên liên tiếp vấp cùng một chỗ: lấy ĐO làm chỗ trú khi được yêu cầu THIẾT KẾ.** Đo an toàn, đo ra số, số trông như tiến bộ — **nhưng đo trong khi chưa có khung thì chỉ là khung cũ được sơn lại bằng số.**

---

# §8. CHƯA QUYẾT — cần human

1. **Mở rộng `B4`** — `B4` nói *"Claude **ở client** biết mâu thuẫn"*. Thiết kế này để **Claude phán NGỮ NGHĨA** (cần vốn từ vựng ⟹ **ở master**) và **client-Claude phán CODE**. **Cần human gật/lắc tường minh** — planner **không suy diễn ngầm** chỗ này nữa.
2. **`wf-lock` ở master** (`B1`) ⟹ **availability = yêu cầu sản phẩm NGÀY 1**. Nhận, hay tách lock xuống client?
3. **`RULE.md §10` lỗ #1** — **view-denial không có nhịp chạy tự nhiên**. Đây là **vế "phải có ai chạy"**, và nó là **trái tim của cái thang**. **Planner đã lấp liếm chỗ này suốt phiên.**
4. **Nhà**: `docs/iwf/` (thiết kế, trong sicp) · `~/projects/icpp/iwf` (code) · `docs/slices/S-IWF-*` (build).
5. **Giữ/bỏ awf** — 🔴 **cố tình để trống.** Human: *"chưa thiết kế thì làm sao biết giữ/bỏ"*. **Đúng.** Tiêu chí giờ mới hiện ra: **quyền-uy kiếm bằng CHỨNG-MINH-QUA-SỬ-DỤNG** ⟹ **code đã chạy + chịu tai nạn thật** xét khác **doc viết hôm kia**. `[ĐÃ CHẠY]` awf = **13.280 dòng code + 12.389 dòng test**; kho tri thức thì **rỗng** (`kb_edge`=2, **`authored`=0** · `wf_record scope='project'`=0). **Xét sau, theo từng tầng §2. Không xét bây giờ.**

---

# §9. NƯỚC ĐI KẾ — nhỏ, và **sai được**

**Đừng xây gì.** Lấy **MỘT business rule THẬT của ICP** → viết thành biểu thức → `NOT VALID` lên DB thật → **xem cái gì vỡ**.

> ## **Dữ liệu thật của ICP đang vi phạm bao nhiêu luật mà không ai biết?**

Gắn sạch ⟹ ý tưởng **đứng**. Lòi ra nghìn dòng vi phạm ⟹ vừa học được **điều quan trọng nhất về ICP** — **rẻ hơn mọi tài liệu thiết kế.**

*(`icp-postgres` tắt từ 14/07. Bật lại bất cứ lúc nào.)*
