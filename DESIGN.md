# IWF — THIẾT KẾ WORKFLOW (bản 1, 2026-07-15)

> Tên `iwf` để **không nhầm** với `sicp` (workflow đang chạy, là NGUỒN Ý-TƯỞNG + NHÓM CHỨNG)
> và `awf` (bản dựng dở, **docs của nó do planner viết khi hiểu sai — không dùng làm nguồn**).
> File này là **nhà của thiết kế IWF**. Không có nhà thứ hai.

---

## §0. VÒNG 0 — chủ thể + bất biến (viết trước, theo `CLAUDE.md §14b`)

**CHỦ THỂ:** IWF = một **workflow-sản-phẩm**: engine ở server + tác-nhân hai phía.
Không phải sicp. Không phải awf. sicp = nguồn ý-tưởng đã-chứng-minh + nhóm chứng. awf = vật liệu, xét sau.

**BẤT BIẾN** — lấy từ **lời human** (`STATE.md §11`, nguyên văn), *không* lấy từ file do planner tự viết:

| # | Bất biến |
|---|---|
| B1 | relay + wf-lock **chạy ở IWF (server)**; client push/get, **client không lưu** — lưu = lộ bí-mật công-nghệ + tổ-chức |
| B2 | Client chỉ giữ **`code + git + hạ-tầng`**; tài-liệu phân-tích **tuyệt đối không ở client**, cần thì gọi API |
| B3 | Mục đích: **nắm trọn vẹn + truy xuất + phân tích + hiểu + lưu business domain thống nhất, và PHẢN BIỆN khi business mâu thuẫn** |
| B4 | **Claude phán, engine phục vụ** |
| B5 | Quan hệ `domain · rule · field · slice · task` ⟹ context **đầy đủ mà không phình vô ích** |
| B6 | **slice/task chỉ là TRUNG GIAN**; chỉ quan tâm **business rule có HOẠT ĐỘNG không**; rule stale → **bỏ, làm rule mới** |
| B7 | **coder không tự chấm bài mình** — coder khai → relay → **planner phán bằng Claude** |
| B8 | Nâng cấp **TỪ sicp** — sicp có basic rất tốt, không vứt |
| B9 | **Không tin file đã tạo trước ở awf** |
| B10 | Đích: **world-class + thương-mại-hoá** |

**VẬT LÝ ĐÃ ĐO** (`STATE.md §8`, 40 vòng, mỗi số có lệnh tái lập) — thiết kế nào cãi mấy dòng này thì **loại ở vòng 0**:

> **Tri thức chỉ sống ở đúng chỗ mà công việc SẼ DỪNG nếu thiếu nó.**
> `dispatch` (không cổng) = **20/20**. `report` (không cổng) = **2/10**. Cùng repo, cùng ngày, cùng người.
> Khác biệt **duy nhất**: coder *không làm được việc* nếu thiếu dispatch; planner vẫn `accepted` được với report rác.
> `verdict` (**có** cổng) = 132/132 có mặt, **76,5% rỗng nghĩa**.
> ⟹ **Cổng mua SỰ CÓ MẶT. Chỉ kẻ tiêu thụ VỠ VÌ SAI mới mua SỰ THẬT.**
> ⟹ `CLAUDE.md` được nạp **21 agent × mỗi phiên** và vẫn mục ở §3/§10/§11/§12. **Được nạp ≠ được đọc ≠ được làm.**

---

## §1. LUẬN ĐỀ — đường biên IP và đường chia trí-tuệ là **CÙNG MỘT ĐƯỜNG**

Đây là lý do IWF tồn tại được như một **sản phẩm**, không phải một cái database có API.

**Nhận định gốc:** cả `B5` (*đầy đủ mà không phình*) lẫn cơn đau truy-xuất (top-k · cosine · cap) **chỉ tồn tại vì một giả định ngầm: chỉ có MỘT chỗ suy luận — cửa sổ context của client.** Dưới giả định đó, *đầy đủ* và *không phình* **thật sự** đối nghịch: phải đoán cái gì đáng gửi, mà đoán thì hoặc gửi thừa hoặc bỏ sót.

**Giả định đó chết ngay khi có tác-nhân suy-luận ở MASTER.** Master không bị trần context của client: nó **liệt kê hết** (map-reduce, lặp, cache, nằm cạnh dữ liệu).

> ### recall = 1 đạt ở **MASTER** bằng **LIỆT KÊ**. precision giao ở **CLIENT** bằng **CHƯNG CẤT**.
> **Đầy đủ ở trên. Gọn ở dưới.** `B5` không còn là đánh đổi — nó là **kiến trúc**.

Và đường biên đó **trùng khít** đường biên IP:

| | Nhìn thấy | Không bao giờ thấy |
|---|---|---|
| **CLIENT** | code của client | corpus / hiến-pháp / graph nội-tại / tenant khác |
| **MASTER** | corpus + graph + hiến-pháp | **code của client** |

⟹ Không bên nào làm nổi việc của bên kia — **vì không bên nào NHÌN THẤY dữ liệu của bên kia.**
⟹ Bảo vệ IP và chia việc suy-luận **là một hành động**, không phải hai. Đó là chỗ `B4` được giữ **hai chiều**:
- **master-claude** trả lời *"luật NÀO đang chơi, và điều client vừa khai có mâu thuẫn với corpus không?"* — cần **corpus**, không cần code.
- **client-claude** trả lời *"với luật này và code này, làm gì / code này sai chỗ nào?"* — cần **code**, không cần corpus.

---

## §2. SÁU TÁC NHÂN — định nghĩa bằng **CÁI NHÌN THẤY**, không bằng chức danh

### MASTER (tại host/server)

| Tác nhân | Là gì | Thấy | Việc |
|---|---|---|---|
| **master-claude** | **thành phần SẢN PHẨM mới** — bộ suy-luận trên TRI-THỨC, chạy lúc runtime, phục vụ mọi tenant | corpus · graph · rule · signature | chưng cất cái client khai → record · **phản biện mâu thuẫn bằng LIỆT KÊ toàn bộ rule** · khử trùng lặp · vun graph · phán QC |
| **master-planner** | **= `client-planner` được cấp `audience=internal`** | thêm: record `internal` | xây chính IWF (self-host) |
| **master-coder** | **= `client-coder` được cấp `audience=internal`** | thêm: record `internal` | xây engine IWF |

> 🔑 **master-planner/coder KHÔNG phải phần mềm thứ hai.** Cùng một binary với client, **khác đúng một GRANT**: cột `audience`.
> Đó cũng là cột `PROGRAM §2 Q2` đã cảnh báo là **thiếu và chặn**, và đo được hôm nay là **thiếu thật** (`wf_record` có `scope`, **không có `audience`**).
> ⟹ **Chính sự phân biệt master/client là thứ ép cột đó ra đời.** Nó không phải chi tiết — nó là cơ chế.

> 🔴 **master-claude là thứ KHÁC HẲN** — không phải planner, không phải coder. Nó **không bao giờ thấy code**.
> Nó là **thành phần duy nhất thực sự MỚI** so với sicp, và là chỗ moat nằm.

### CLIENT (máy khách)

| Tác nhân | Là gì | Thấy | Việc |
|---|---|---|---|
| **client-claude** | **nền** — phiên Claude ở máy khách. Không phải vai, là **chất nền** hai vai dưới chạy trên | code | (nền) |
| **client-planner** | shim rỗng-IP, `/client-iwf-planners` | code + payload API | phân tích nghiệp vụ khách → cắt slice → dispatch · **KHAI quan hệ** (`authored`) |
| **client-coder** | shim rỗng-IP, `/client-iwf-coders` | code + payload API | viết code · chạy check · **KHAI cái nó chạm** |

**Bất biến shim:** file khách **rỗng IP**. Persona · law · task · DoD · rule — **không dòng nào nằm trên đĩa khách**, tất cả xuống theo payload ephemeral. Giữ bằng **lint-lúc-ship** (grep thấy law/persona trong file shipped = fail build).

---

## §3. BẢY TẦNG DATA

> Đặt tên theo **cái nó giữ**, không đánh số — số ở awf từng bị đọc nhầm thành thứ tự build.
> Cột **"Vỡ khi sai"** là cột quan trọng nhất: theo `§8`, tầng nào không có ai vỡ vì nó **sẽ mục**, bất kể schema đẹp cỡ nào.

| Tầng | Giữ gì | Ở đâu | Ai ghi | Ai đọc | **Vỡ khi sai?** |
|---|---|---|---|---|---|
| **1 · CONSTITUTION** *(IP của IWF)* | persona · law · skill · template · glossary. `audience: internal\|client` | **MASTER** | master-planner | mọi tác nhân (payload) | ✅ client-coder **không làm được việc** nếu payload sai/thiếu |
| **2 · DOMAIN** *(IP của KHÁCH)* | business domain · **business rule** · invariant · glossary dự án | **MASTER**, tenant-scoped | client-planner khai · master-claude chưng cất | client-planner · master-claude | ⚠️ **chỉ vỡ nếu rule có CHECK** → §5 |
| **3 · MODEL** *(IP của khách, ảnh gương)* | `db_table` · `db_column` · route · service · symbol. **KHÔNG data thật** | **MASTER**, tenant-scoped | write-during-work + introspect | master-claude · client-planner | ✅ sai field ⟹ join rule sai ⟹ coder làm sai ⟹ bị bác |
| **4 · WORK** *(trung gian — `B6`)* | slice · task · **dispatch** · report · verdict · lock | **MASTER** *(= relay, `B1`)* | cả hai phía | cả hai phía | ✅ **đây là tầng 20/20** — không có nó, không ai làm được gì |
| **5 · EVIDENCE** | **check** · run · **attribution** | định nghĩa ở **MASTER**, **CHẠY Ở CLIENT** | client-coder (kết quả) | master-claude | ✅ đỏ/xanh là nhị phân, không cãi được |
| **6 · GRAPH** | cạnh giữa 1–5. **`authored`** (người BIẾT khai) vs **`derived`** (máy đoán) | **MASTER** | client-planner (`authored`) · master-claude (`derived`, chỉ để **gợi ý chỗ thiếu khai**) | master-claude | ⚠️ **cạnh thiếu = im lặng** → §5 |
| **7 · CODE + GIT + HẠ-TẦNG** | mã nguồn | **CLIENT — MASTER KHÔNG BAO GIỜ THẤY** | client-coder | client-coder | ✅ compile/test |

### Tầng biên — **SIGNATURE** (thứ **DUY NHẤT** đi từ client lên master)

```
client-coder  --[ signature ]-->  master
   field-set touched (bảng.cột)      ← engine chỉ cần FIELD, không cần CODE (B5)
   symbol-set / route-set
   check verdicts (id, đỏ/xanh, lý do)
   rule declarations (client-planner khai)
   ────────────────────────────────────
   ❌ KHÔNG BAO GIỜ: mã nguồn
```

> 🔴 **Đây là tầng chưa tồn tại, và cả thiết kế treo trên nó.**
> **Vì sao bắt buộc:** (a) `B2` — code là của khách; (b) **thương mại**: khách enterprise sẽ **không bao giờ** đẩy source lên server bạn, và *"chúng tôi không bao giờ thấy code của bạn"* là câu **bán được** — nhưng chỉ **đúng** nếu wire là signature, **không phải diff**.
> **Hệ quả cứng:** **check phải CHẠY Ở CLIENT** (code ở đó), nhưng **master quyết CHECK NÀO và VÌ SAO**. Master gửi *"chạy check #17, #22"* — không gửi ruột luật; nhận về *"#17 xanh, #22 đỏ tại `orders.grand_total`"*.
> **Đánh đổi phải gọi tên:** check chạy ở client ⟹ ruột check có thể bị đọc. Trị bằng **scope-per-action + rate-limit + canary + legal** (`PROGRAM §3`), **không phải crypto** — và chấp nhận rằng **ruột một check ≠ moat**; moat là **biết check nào đáng chạy, khi nào, và tại sao** — cái đó ở master.

---

## §4. TẦNG WORK — bê nguyên vật-lý đã thắng của sicp

Bốn cơ chế của sicp **không phải thiết kế, là hệ quả vật lý** (`STATE.md §1`). Chúng **giữ nguyên** ở IWF vì vật lý không đổi:

| Vật lý | Cơ chế | Ở IWF |
|---|---|---|
| planner ⊥ coder = 2 phiên không nói chuyện được | **relay = ống duy nhất** | ống ở **MASTER** (`B1`) |
| phiên **chết** (exit/compact) | **state ở ngoài phiên** | ống = bộ nhớ ngoài, qua API |
| N coder **một** cây git | **wf-lock** | ⚠️ **xem lỗ dưới** |
| **chỉ human mở được terminal** | ▶RUNNABLE + chuông | giữ — **human = SPAWNER (vật lý, không gỡ được)**; **human = CỔNG (ngẫu nhiên, GỠ)** |

**Form 5 lớp** `[BỐI CẢNH][VIỆC][RÀNG BUỘC][OUTPUT][THẤT BẠI]` — **giữ, không chế lại**. Đây là artifact **duy nhất** trong sicp đạt 100% mà **chưa từng có cổng**. Nó thắng vì coder *vỡ* khi thiếu. Ở IWF nó là **schema của payload dispatch**, không phải văn xuôi.

### 🔴 Lỗ vật lý phải quyết: **wf-lock gác một tài nguyên nó không nhìn thấy**
`wf-lock` bảo vệ **cây git của client**. `B1` bắt lock ở master. ⟹ mỗi `git commit` = một vòng lên server; **master chết = client không commit được**.
Và header `wf-lock` đã tự ghi sẵn điều kiện hết hạn của chính nó: *"Single Redis… add fencing tokens/etcd **only if this ever becomes correctness-critical for a product**"*.
> **Chọn mô hình này LÀ kích hoạt điều kiện đó — ngày 1, không phải P2.** Availability của master trở thành **yêu cầu sản phẩm**, vì nó nằm trên đường tới hạn của việc khách commit.

---

## §5. 🔑 CƠ CHẾ CHỐNG MỤC — **ATTRIBUTION**. Đây là xương sống.

Áp `§8` lên **chính thiết kế này** (refute-first):

| Tác nhân | Sai thì ai vỡ? |
|---|---|
| client-coder sai | check đỏ → **vỡ ngay** ✅ |
| client-planner sai | coder không làm được / bị bác → **vỡ** ✅ |
| **master-claude sai** *(nói "không mâu thuẫn" khi CÓ)* | 🔴 **KHÔNG AI VỠ.** Graph tệ đi một chút. Tuần sau tệ thêm chút. |

> ### Tầng MASTER, về mặt cấu trúc, **là phía `report` của sổ cái** — và `report` đo được là **2/10**.
> Nó **sẽ mục theo mặc định**. Đúng cách `memory` (1.774 dòng, index drift, tự vi phạm luật của nó) đã mục.
> Đúng cách `LOG_CATALOG` (31 emit vắng mặt, **0 cổng**) đã mục. Đúng cách `seam` (67, không schema, resolve = tự huỷ) đã mục.
> **Schema đẹp không cứu được. Cổng không cứu được — cổng chỉ mua sự-có-mặt.**

**Vậy phải CHẾ TẠO ra kẻ vỡ.** Cơ chế:

```
serve  → bundle_id  (ghi ĐÚNG những record_id nào đã được phục vụ)
work   → client làm
verdict→ TRỎ NGƯỢC về bundle_id
```

Từ đó, **suy ra bằng query, không phải bằng ý kiến**:
- rule liên tục được phục vụ vào việc **bị rework** ⟹ **rule đó khả nghi** — tự lộ
- rule **chưa từng được phục vụ** ⟹ **xác chết** — tự lộ
- rule được phục vụ mà **không có check** ⟹ **"tôi không biết"** — tự lộ

> ### Đây chính là `gen-facts` **áp cho tầng tri-thức**.
> `gen-facts` thắng **không phải vì nó KIỂM FACTS** — mà vì nó làm **FACTS không thể tồn tại tách rời code**.
> Attribution làm **"rule này tốt không" không thể tồn tại tách rời công việc mà rule đó đã tham gia.**
> **Đừng duy trì. SUY RA.** Đó là 5 kho mà `gen-facts` không với tới (`STATE.md §8`) — và đây là cách với tới.

**Giá: gần bằng 0 nếu làm ngày 1** (một `bundle_id` + một FK). **Gần như không thể retrofit** — retrofit thì không có lịch sử, mà lịch sử **chính là** dữ liệu.

🔴 **Cảnh báo đo được, ngay bây giờ:** `wf_dispatch` hiện có `dispatch_body · report_body · verdict` nhưng **PK = `(project_id, slot)`** ⟹ **UPSERT đè**. Nó đã thừa hưởng đúng chứng quên của sicp (`§10a`: `consume` **XOÁ** dispatch ⟹ **481 câu trả lời giữ / 481 câu hỏi huỷ**). **Không có bundle. Không có đường từ verdict về cái đã phục vụ.** ⟹ tầng WORK phải **append-only**, không đè.

---

## §6. "XONG" ĐO Ở **RULE** — và **"TÔI KHÔNG BIẾT"** là công dân hạng nhất

`B6`: slice/task là trung gian; chỉ quan tâm **rule có hoạt động không**.

**Ba trạng thái, không phải hai:**

| | Nghĩa |
|---|---|
| check **ĐỎ** | chưa xong — **chứng minh được** |
| check **XANH** | đang đứng |
| **không có check** | 🔴 **"TÔI KHÔNG BIẾT"** — **CẤM im lặng** |

**master-claude KHÔNG BAO GIỜ được trả một verdict trần.** Luôn là **`(phán quyết, ĐỘ PHỦ, evidence-ids)`**:
> *"Đã đối chiếu 47/51 rule. 4 rule không có check ⟹ **tôi không chứng minh được** phần đó."*

Vì sao là luật cứng: *"không mâu thuẫn"* mà không kèm độ phủ là **câu không thể phản chứng** — và câu không thể phản chứng **chính là `accepted` trần trụi 76,5%** mọc lại ở tầng cao hơn, chỉ khác là lần này nó nói bằng giọng AI nên **nghe đáng tin hơn**. Đó là kịch bản tệ nhất của cả thiết kế.

**KPI thật của sản phẩm** = **tỉ lệ rule có check sống** (`advisory → enforceable`). Đo ở awf hôm nay: **15/18 còn advisory ⟹ 17%**. Con số này **trung thực và bán được** — và nó tự nhích lên mỗi lần đóng slice, vì QC **dù sao cũng phải làm**, nên biến vài rule từ văn-xuôi → chạy-được là **sản phẩm phụ của việc đằng nào cũng làm**, không phải "dự án viết check" riêng.

> **Tính chất đúng của QC: việc của nó là làm cho CHÍNH NÓ thành thừa.**

---

## §7. VÒNG LẶP IWF — một vòng đầy đủ

```
client-planner  ──/session──►  MASTER: persona + task + scoped-DoD + rule đang chơi   [bundle_id]
      │                         ▲ master-claude LIỆT KÊ toàn corpus → chưng cất → gửi cái ĐÃ CHỨNG MINH liên quan
      │  khai: "task này phục vụ rule R, chạm orders.grand_total"   ──► GRAPH cạnh `authored`
      ▼
client-coder    ──/brief──►    MASTER: task + luật áp cho ĐÚNG task này              [bundle_id]
      │   (không lấy được việc nếu không gọi — ống LOAD-BEARING theo cấu tạo)
      │  làm code · chạy check master chỉ định
      ▼
      ──signature──►  field-set · check verdicts   (KHÔNG code)
                            │
                            ▼
                     master-claude: diff→field→rule neo vào field đó   ⟹ recall = 1 DETERMINISTIC
                            │   (KHÔNG top-k. KHÔNG cosine. LIỆT KÊ.)
                            ▼
client-planner  ──/qc──►   phán (B7: coder không tự chấm) + trả về:
                            (1) rule xong chưa  (2) CHECK nào chứng minh điều đó MÃI MÃI
                            ▼
                     verdict ──TRỎ NGƯỢC──► bundle_id     ⟵ §5. Không có mũi tên này thì mọi thứ trên đều mục.
```

**Vì sao vòng này không mục:** client-coder **không lấy được việc** nếu không gọi `/brief`. Nên mọi thứ **cưỡi trên payload đó** thừa hưởng **20/20 của dispatch — miễn phí**. Đó là toàn bộ mẹo, và nó là mẹo **duy nhất** `§8` cho phép.

---

## §8. TỰ CÔNG THIẾT KẾ NÀY (`§14` refute-first — ghi cả cái bị bác)

| # | Đòn | Trả lời |
|---|---|---|
| 1 | *"Load-bearing chỉ mua được **không thể VẮNG**, không mua được **ĐÚNG**. Engine trả luật SAI, coder vẫn làm sai."* | **ĐÚNG — đòn mạnh nhất.** Dispatch đúng vì `[RÀNG BUỘC]` sai thì coder đẻ ra thứ bị bác → **quay lại**. ⟹ Ống **phải hai chiều từ nhát cắt đầu** (§5). Giao-trước-học-sau = ống đẹp **ship luật stale mãi mãi, một cách tự tin**. |
| 2 | *"Master trên đường tới hạn = master chết thì 20 coder dừng."* | **ĐÚNG, và là cái giá thật.** Hôm nay file trên đĩa ⟹ availability vô hạn. Chọn mô hình này = biến availability thành **yêu cầu sản phẩm ngày 1** (§4). Không né được — chỉ **gọi tên và trả**. |
| 3 | *"Self-host chứng minh được thiết kế này chứ?"* | **KHÔNG.** Self-host **không bao giờ** test được đường giao hàng (file-rỗng + API + lọc `audience`) — master đọc thẳng DB. Và corpus IWF **đồng chất tuyệt đối** (mọi law đều hình "một quy tắc kỹ thuật"), là bàn thử **khắc nghiệt nhất** cho truy xuất, không phải dễ nhất. ⟹ **Chỉ khách thật / sicp-làm-client mới chứng minh được.** |
| 4 | *"`B4` nói **client-Claude** biết mâu thuẫn. `master-claude` có cãi `B4` không?"* | **Không — chia việc theo CÁI NHÌN THẤY (§1).** master-claude: *luật nào đang chơi + có mâu thuẫn corpus không* (cần corpus). client-claude: *với luật này + code này thì sao* (cần code). **Không ai làm nổi việc của ai vì không ai thấy dữ liệu của ai.** ⚠️ Nhưng đây **là** một mở rộng `B4` — **cần human chuẩn thuận tường minh**, không được lặng lẽ suy diễn. |
| 5 | *"Sao không để master-claude đọc luôn diff cho khoẻ?"* | **Vì đó là cửa tử thương mại** (enterprise không đẩy source), **và nó không đủ về kỹ thuật**: seam `wedge-scope-needs-postimage` đã đo — răng sắc nhất cần **CÂY REPO**, không phải diff; giao qua HTTP thì nó **không chạy, và im lặng**. Diff-lên là **sai cả hai hướng**. |
| 6 | *"Đây có phải `luật SỐNG = luật ĐƯỢC NẠP` (đã bị bác ở §12 T5) không?"* | **Không — ngược lại.** T5 bị bác **chính là bằng chứng của tôi**: `CLAUDE.md` nạp 21×/phiên vẫn mục. Tôi không nói *nạp*; tôi nói **CẦN**. Nạp ≠ sống. **Cần** = sống. |
| 7 | *"Master tier có phải chỉ là đội dev của mình khoác áo kiến trúc?"* | **Nửa đúng — và phải tách.** `master-planner/coder` = **client + grant `audience`**, *không phải* thành phần sản phẩm. `master-claude` **là** thành phần sản phẩm, và là **cái duy nhất thực sự mới**. Trộn hai cái = ship đội dev của mình như một tính năng. |

**Giả thuyết BỊ BÁC, ghi lại để không ai đi lại:**
- 🪦 *"Lấy răng của awf cắm vào não của sicp"* — **BÁC bởi `§8`**: răng = cổng = chỉ mua sự-có-mặt. sicp không thiếu răng, nó thiếu **kẻ phụ thuộc**. Đây là **liều thứ hai của ADR-045** và là cái bẫy trung tâm.
- 🪦 *"Truy xuất tốt hơn (rerank/hybrid/graph-DB) giải được `B5`"* — **BÁC**: xếp-hạng-theo-tương-tự **là đoán**. `per_kind_cap` sai không phải vì cap thấp, mà vì **cắt một phỏng đoán thay vì ghi lại một sự thật**. Chữa = `authored` edge, không phải thuật toán.

---

## §9. CHƯA QUYẾT — cần human (`§7`: mỗi lượt một câu)

1. **`B4` mở rộng** — master-claude được **phán về tri-thức** (mâu thuẫn/trùng/độ phủ) trong khi client-claude phán về **code**. Đây là **đường trục** của cả thiết kế → cần bạn gật/lắc **tường minh**.
2. **wf-lock ở master** (`B1`) ⟹ availability = yêu cầu sản phẩm ngày 1 (§4). Nhận, hay tách lock xuống client?
3. **Nhà**: `docs/iwf/` (thiết kế, trong sicp — vì planner/coder sicp đang xây) · `~/projects/icpp/iwf` (code) · `docs/slices/S-IWF-*` (build).
4. **Giữ/bỏ awf** — 🔴 **CHƯA TRẢ LỜI ĐƯỢC, và cố tình để trống.** Human 2026-07-15: *"chưa thiết kế ra được workflow iwf thì làm sao biết cần giữ cần bỏ cái gì"*. **Đúng.** Có §1–§8 rồi thì tiêu chí mới hiện ra, và **tiêu chí là `PROGRAM` luật quyền-uy: quyền-uy kiếm bằng CHỨNG-MINH-QUA-SỬ-DỤNG, không phải bằng ĐƯỢC-VIẾT-RA** ⟹ **code đã chạy + chịu tai nạn thật** xét khác **doc viết hôm kia**. Xét **sau**, theo từng tầng §3, **không xét bây giờ**.

---

## §10. NHẬT KÝ SAI CỦA CHÍNH FILE NÀY

- **2026-07-15 · planner (phiên-3):** được lệnh **thiết kế IWF**, nhưng bỏ đi **đo `awf`** để cãi chuyện giữ/bỏ. Human chặn: *"chưa thiết kế ra được workflow iwf thì làm sao biết cần giữ cần bỏ"* + *"tại sao cãi lời tôi vậy"*. **Cùng một hình với `STATE.md §12 T8 #7`** (phiên-2 lạc chủ thể sang audit `gate-*`, human chặn *"bạn đang làm gì vậy?"*). ⟹ **`§14b` đã ghi sẵn: human hỏi "sao cứ lấy X ra vậy?" = tín hiệu ĐỎ.** Hai phiên liên tiếp vấp cùng chỗ: **lấy ĐO làm chỗ trú khi được yêu cầu THIẾT KẾ.** Đo là an toàn, đo cho ra số, số trông như tiến bộ — nhưng **đo trong khi chưa có khung thì chỉ là khung cũ được sơn lại bằng số.**
