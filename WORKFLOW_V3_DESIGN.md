# WORKFLOW V3 — THIẾT KẾ ĐẦY ĐỦ (v3.2, product-first, MỘT doc thống nhất)

> **TRỤC CHÍNH = SẢN PHẨM.** Đây là MỘT doc thống nhất (KHÔNG tách). §00 nêu sản phẩm + mô hình kiếm tiền + hào; §21–§22 chi tiết; §0–§20 là NỀN + DOGFOOD (workflow-sicp) dựng và validate chính engine của sản phẩm.
> **Chủ thể:** sản phẩm *"proof-of-correctness cho code do AI sinh, trong phần mềm quản chế"* (§00). Workflow-sicp = bàn thử/dogfood phục vụ nó.
> **Trạng thái:** THIẾT KẾ CHUẨN-HOÁ — chưa cắt slice. Số đo kèm lệnh tái lập; chưa đo ghi "chưa đo"; đã verify ghi "**đã verify**".
> **Gốc:** 2026-07-16 (v3.0) → 2026-07-17 (v3.1: phản biện chuyên gia + verify Claude Code) → **2026-07-17 (v3.2: PRODUCT-FIRST, human trao TOÀN QUYỀN quyết định — mục tiêu KHÔNG-AI-BẮT-CHƯỚC + BÍ-MẬT-KHÔNG-KHÁM-PHÁ-ĐƯỢC + kiếm-tiền-thị-trường-CHƯA-CÓ).** `[HUMAN]` = human chốt · `[DECIDED]` = tôi quyết theo uỷ quyền.

---

## §00. SẢN PHẨM & KIẾM TIỀN — TRỤC CHÍNH [DECIDED — human trao toàn quyền 2026-07-17]

**XÂY CÁI GÌ:** một dịch vụ **CHỨNG MINH code do agent sinh có thoả requirement không** — trong phần mềm **quản chế/an-toàn**. Vòng: requirement (EARS/bán-hình-thức) → **tự hình-thức-hoá** thành property (temporal-logic/SMT) → **chứng minh** code thoả (hoặc phản-chứng có counter-example) → **phát CHỨNG-CHỈ audit-grade, ký số**. Đây chính là chỗ R10 nói *"không ai vỡ khi rule sai nghĩa"* — và là nơi DUY NHẤT máy vá được: **ngách hình-thức-hoá-được.**

**VÌ SAO BÂY GIỜ:** agent sinh code cấp-số-nhân ⟹ nút cổ chai dời từ *viết* sang **chứng minh code đúng**. Ngành quản chế **BẮT BUỘC** chứng nhận (DO-178C · ISO 26262 · IEC 62304) và **ghét** DOORS/Polarion (thủ công · không AI · không chứng minh). Không tool SDD/agent nào phát chứng-chỉ-đúng-đắn. **Lỗ trống × bắt buộc × trả cao.**

**NGÁCH [DECIDED]: phần mềm an-toàn; vertical ĐẦU = ISO 26262 (automotive functional safety).**
- Vì sao automotive trước: thị-trường-phần-mềm-hình-thức-hoá-được **LỚN NHẤT + bùng nổ** (ADAS · EV · software-defined-vehicle); nhiều **Tier-1/Tier-2** supplier ⟹ design-partner **tiếp cận được** (khác avionics do prime độc chiếm); MISRA-C + AUTOSAR đã **bán-hình-thức** (nửa đường tới property).
- Engine **tổng quát hoá** sang họ IEC 61508 (26262 · 62304 · 50128 · DO-178C) — cùng cấu trúc safety-property + traceability ⟹ **hào cộng dồn qua cả họ chuẩn**, chỉ vào automotive TRƯỚC.
- ⚠ Đây là **bet khởi đầu**, xoay được nếu design-partner ngon hơn xuất hiện ở vertical khác — nhưng khung (quản-chế × hình-thức-hoá-được × formal-moat) thì KHÔNG xoay.

**MÔ HÌNH KIẾM TIỀN [DECIDED — thị trường CHƯA CÓ]:** KHÔNG bán ghế (seat) như DOORS. Bán **KẾT QUẢ chứng minh:**

| Model | Bán gì | Vì sao "thị trường chưa có" |
|---|---|---|
| **1. Per-proof / per-verified-requirement** | trả tiền khi engine **CHỨNG MINH** một requirement được code thoả + phát chứng-chỉ vào safety-case | doanh thu **scale theo KHỐI LƯỢNG code AI sinh**, không theo headcount. Không ai định giá kiểu này vì **không sản xuất nổi proof tự động ở agent-scale** |
| **2. Continuous certification (subscription)** | giữ safety-case **LUÔN TƯƠI**: agent đổi code → re-prove tự động → compliance không stale | RTM hiện tại = **snapshot + verify TAY** (big-bang cuối kỳ, đau). "Luôn-chứng-minh-được" là sản phẩm **chưa tồn tại** |
| **3. Compliance-evidence package** | xuất bộ bằng-chứng-audit (proof + trace + counter-example log) nộp cơ quan chứng nhận | ngành PHẢI sản xuất cái này bằng tay — ta tự-động-hoá + chứng-minh-hoá nó |

> Cả 3 model **được MỞ RA bởi năng lực kỹ thuật KHÓ** (chứng minh tự động). Đối thủ không định giá kiểu này được vì **không sản xuất nổi proof** — pricing-model là **hệ quả** của moat, không phải chiêu marketing.

**HÀO — KHÔNG-AI-BẮT-CHƯỚC + BÍ-MẬT-KHÔNG-KHÁM-PHÁ (§22 chi tiết):**
- **Bí mật KHÔNG phải "toán bí mật".** Toán **tái lập được** — hứa giấu-toán là hứa hão, tôi KHÔNG bán bạn moat ảo. Bí mật THẬT = **năng-lực-phụ-thuộc-DỮ-LIỆU**: engine auto-formalize + auto-proof **tuỳ thuộc CORPUS độc quyền** (requirement→property→code→proof→outcome tích luỹ trong ngách). **Lộ thuật toán KHÔNG lộ năng lực** — thiếu corpus thì cùng thuật toán chạy dở.
- **Server-side TUYỆT ĐỐI** (không ship engine) + **flywheel** (mỗi lần dùng → corpus dày → đối thủ luôn chậm N tháng) + **tool-qualification** (DO-330 / ISO 26262-8) — track-record pháp-lý đối thủ mới KHÔNG có.
- **Tuỳ chọn TEE / confidential-computing** cho lớp *"cả nhà-cloud cũng không soi được"* — chống black-box triệt để (§22).

**QUAN HỆ VỚI WORKFLOW-SICP (§0–§20) — MỘT doc, ba lớp:** sicp-workflow là **DOGFOOD** dựng sẵn 3 phôi của sản phẩm: (a) **hạt nhân DB-có-răng** (requirement · business_rule · verdict · FK-bằng-chứng — §5) · (b) **cơ chế probe chân-4** (§7) = phôi thai của auto-proof · (c) **corpus đầu tiên** (rule→proof→outcome của chính sicp). ⟹ **nền → dogfood → sản phẩm**, không rời nhau.

---

## §-1. CHANGELOG v3.0 → v3.2 — CÁI GÌ ĐỔI, VÌ SAO
> C1–C10 = v3.1 (fix chuyên gia + verify) · C11–C14 = v3.2 (product-first, human trao toàn quyền)

| # | Đổi | Lý do | Nguồn |
|---|---|---|---|
| C1 | **node 1-bảng → thin-node danh-tính + bảng chi-tiết theo-kind** | CHECK theo-kind điều-kiện (`kind<>'business_rule' OR ...`) là bùa, không phải răng thật. Bảng con cho `rationale text NOT NULL` **thật** = P1 áp cho chính schema | phản biện 2(f) — **nhận** |
| C2 | **RLS/GUC/3-field multi-tenant → DB-per-project + PG-role-per-user** | workflow-DB là store **DẪN XUẤT** (tái sinh bằng ingest); bài học V011 "thêm sau = đau" áp cho store **NGUỒN**, không phải dẫn xuất. DB riêng mỗi project khớp B8, không GUC, không quên | phản biện 2(e) — **nhận (hoãn, không giết — §22 SaaS)** |
| C3 | **Tự dựng code-graph → MUA (CodeGraph/GitNexus) qua adapter thay-được** | code-graph là commodity + là nửa **dễ lỗi-thời nhất** (hãng lớn sắp ship native indexer). Lớp răng phía trên KHÔNG dính ruột nó | phản biện §3/§4 — **nhận** |
| C4 | **Thêm R10** — không ai VỠ khi rule sai NGHĨA | probe chứng minh *tồn tại*, không chứng minh *đúng-ý*. Correctness-vs-intent bất khả máy-kiểm (khung tổng quát) ⟹ **giữ rule ÍT**, harvest chọn lọc | phản biện 2(d) — **nhận** |
| C5 | **B7 "rollback 0 giây" SỬA** — hook Ⓐ fallback có điều kiện | CLAUDE.md + SessionStart là **role-agnostic dùng chung V1+V3**; flip stub fail-closed làm chết cả V1. Lưới an toàn nối vào thứ nó bảo hiểm | phản biện 2(b) — **nhận** |
| C6 | **cross-check coverage: pre-commit CÓ MỤC TIÊU, toàn cục lúc đóng slice; ingest INCREMENTAL** | coverage toàn bộ 378k dòng mỗi commit × N coder = bất khả; drop+rebuild mỗi commit cũng vậy | phản biện 2(c) — **nhận** |
| C7 | **§19 mắt xích gốc = CÓ (đã verify)** + master-* thành **skill** (`.claude/skills/`) | headless `claude -p "/cmd"` expand: **verified**. Commands & skills cùng tồn tại, skills khuyến nghị cho cái mới | verify Claude Code docs |
| C8 | **Thêm §21 SẢN PHẨM (RTM-MCP) + §22 HÀO** | quyết định thương-mại-hoá vào ngách, hào để đối thủ + user không copy | **[HUMAN 2026-07-17]** |
| C9 | **§17 → V3-lite path + MỘT số đo phủ định** (verdict "có nội dung" 20%→? sau 20 task) | chứng minh nền rẻ trước khi làm to; biết sai sau 3 tuần thay vì 6 tháng | phản biện §4 — **nhận** |
| C10 | **B4 (cây git chung) → item ĐO-LẠI trình human**, KHÔNG "cấm bàn" | worktree giải cô-lập-file, agent-teams giải phối-hợp — nền đã đổi từ lúc B4 ra đời | phản biện 2(g) — **escalate, không tự lật** |
| **C11** | **PRODUCT-FIRST (v3.2): §00 = trục chính sản phẩm; workflow-sicp = dogfood; MỘT doc (không tách)** | human: "ưu tiên sản phẩm, từ đó hoàn thiện workflow" + "không được tách doc" | **[HUMAN 2026-07-17]** |
| **C12** | **NGÁCH + KIẾM TIỀN [DECIDED]:** ISO 26262 automotive; bán proof/certificate + continuous-certification (KHÔNG bán seat) | "kiếm tiền thị trường chưa có" + toàn quyền | **[DECIDED]** |
| **C13** | **BÍ MẬT = năng-lực-phụ-thuộc-corpus + server-side + (tuỳ chọn) TEE, KHÔNG "toán bí mật"** | "không ai khám phá bí mật thuật toán" — trung thực: toán tái lập được, corpus + track-record thì không | **[DECIDED]** |
| **C14** | **Thêm proof-engine (auto-formalize → auto-proof → certificate) vào §21; R13/R14 rủi ro sản phẩm** | §00 vòng chứng minh — vá R10 ở ngách hình-thức-hoá-được | **[DECIDED]** |

**Đẩy lại chuyên gia (không nhận 100%):** (a) "commands merged vào skills v2.1.101" — **SAI** (đã verify: cùng tồn tại, commands vẫn chạy); (b) các số thị trường (sao/benchmark/"6 tháng có native indexer") = **dự đoán chưa verify** — dùng làm định hướng, KHÔNG chân lý; (c) V3-lite đo verdict = đo **KỶ LUẬT**, KHÔNG đo **tính-ĐÚNG** rule — R10 vẫn hở ở khung tổng quát, chỉ vá được ở ngách hình-thức-hoá-được (§22).

---

## §0. VÒNG 0 — CHỦ THỂ + BẤT BIẾN (`CLAUDE.md §14b`)

**CHỦ THỂ:** trục CHÍNH là **sản phẩm** (§00/§21); mục §0 này chốt Vòng-0 cho **workflow-sicp = DOGFOOD** phục vụ sản phẩm — cỗ máy điều phối các phiên Claude để thay đổi repo sicp **và để hiểu phần mềm phải làm đúng cái gì** (chính là năng lực bán ở §00). MỘT doc, KHÔNG tách.

**BẤT BIẾN — không giải pháp nào được vi phạm:**

| # | Bất biến | Nguồn / trạng thái |
|---|---|---|
| B1 | Context Claude **hữu hạn · không chia sẻ · sẽ chết** | sự thật vật lý |
| B2 | **Planner MÙ** — chỉ biết state khi nó chạy lệnh | `scripts/relay` |
| B3 | **Chỉ human mở được terminal coder**; phiên KHÔNG tự spawn phiên (ngoại lệ: wrapper bash ngoài-phiên §19) | harness |
| B4 | **N coder + 1 planner dùng CHUNG 1 cây git** — cố ý; branch-per-slot đã BÁC. **v3.1: chuyển "cấm bàn lại" → "ĐO-LẠI khi worktree/agent-teams rẻ hơn wf-lock" (C10, trình human)** | `wf-lock:33-46` + phản biện 2(g) |
| B5 | **sicp KHÔNG sở hữu toolchain của nó** — `tsc`·`eslint`·`vitest`·`next`·`black`·`pytest`·`git`·harness **đều nói tiếng FILE** | đo |
| B6 | **Sự tuân thủ mua bằng KẺ TIÊU THỤ VỠ**, không mua bằng luật, không bằng cổng | đo — §1. **NỀN CỦA TOÀN BỘ THIẾT KẾ** |
| B7 | **V3 dựng SONG SONG, KHÔNG phá V1** — cũ luôn chạy làm lưới an toàn. **v3.1 SỬA (C5): rollback KHÔNG phải "0 giây gõ tên cũ"** vì CLAUDE.md/SessionStart dùng chung; rollback = hook Ⓐ fallback-có-điều-kiện HOẶC `git revert CLAUDE.md` | **[HUMAN]** + phản biện 2(b) |
| B8 | **Workflow TÁCH TUYỆT ĐỐI khỏi PROJECT** — MCP/CLI/DB/khoá **KHÔNG BAO GIỜ** trong `apps/`·`packages/`, **KHÔNG** dùng chung DB/Redis product | **[HUMAN]** — y như `icp-wf-redis:6380` tách `icp-redis:6379` |
| B9 | **Cô lập tenant.** **v3.1 ĐỔI (C2): hôm nay = DB-per-project + PG-role-per-user** (khớp B8, không GUC). RLS/shared-PG chỉ khi host chung nhiều user không-tin-nhau (§22 SaaS — hoãn, không giết) | **[HUMAN]** sửa bởi phản biện 2(e) |
| B10 | **Stack workflow-space = PYTHON** — khớp MCP SDK + tree-sitter binding + psycopg + pgvector + (sau) bge-reranker; tách sạch pnpm monorepo (B8); boot-CLI gọi được từ bash hook | **[HUMAN 2026-07-17]** |

> **B5 giết vế "file code là sản phẩm cuối, không cần commit".** Kẻ đòi file ở đây **là máy**, không phải người ⟹ lập luận *"Claude không cần file như người"* không gỡ được ràng buộc này.
> **B6 là nền.** Mọi mục dưới đều là biến thể của: *chỉ kẻ VỠ VÌ SAI mới mua được sự THẬT.*

---

## §1. VẤN ĐỀ — ĐO ĐƯỢC, KHÔNG PHẢI CẢM TÍNH

### 1.1 Thang tuân thủ `100 · 95 · 31 · 20 · 2,4`

| Hiện vật | Luật viết? | Cổng máy? | **Kẻ tiêu thụ VỠ nếu sai?** | Tuân thủ |
|---|---|---|---|---|
| `dispatch` 5 lớp | có | **KHÔNG** | **CÓ** — coder tắc ngay | **20/20 = 100%** |
| `commit` subject | có | có (báo động) | không | 1259/1330 = **95%** |
| `report` 6 mục | có | KHÔNG | **KHÔNG** | 151/482 = **31%** |
| `verdict` | — | **CÓ — CỨNG, `exit 2`** | không | 126/126 **có mặt** · 25 **có nội dung** = **20%** |
| slice khai type | có | không | không | **1/31** |
| ADR đối chiếu code | có | không | không | **3/126 = 2,4%** |

**Đọc bảng:** `§6` và `§7` của `CLAUDE.md` **sinh từ ĐÚNG MỘT commit `ea3dc4a3`** → **95% vs 31%** ⟹ không phải "luật viết chưa rõ".
`verdict` có **cổng cứng nhất hệ** (thiếu = `exit 2`) → mua được **100% sự CÓ MẶT**, **20% sự THẬT** (101 lần verdict là đúng một chữ trần `accepted`).
`dispatch` **không validator nào** mà **100%**.

> **⟹ B6: cổng chỉ mua được cái nó ĐO được. Đo hình thức ⟹ nhận hình thức. Chỉ kẻ VỠ VÌ SAI mới mua sự THẬT.**

### 1.2 Planner MÙ business rule — lỗ chí mạng

| File | Nhắc `business rule` |
|---|---|
| `.claude/commands/icp-multi-planners.md` (25 KB) | **0** |
| `.claude/commands/icp-multi-coders.md` (10,6 KB) | **0** |
| `docs/ICP_WEB_PLAN.md §8` (kỹ năng PHÂN TÍCH SLICE) | **0** |
| `CLAUDE.md` (209 dòng) | **1** — và là `§11 Comments` (lúc nào viết comment) |

`§2 Nghi thức MỞ TASK` + `VERIFY-DEPTH high-risk` = toàn **CẤU TRÚC + AN TOÀN**, không có "có đúng luật nghiệp vụ không".

> **⟹ Workflow phân tích CẤU TRÚC, KHÔNG BAO GIỜ phân tích HÀNH VI.** Planner mù cả lúc **cắt** lẫn lúc **verify**. `verdict` 20% không phải vì planner lười — **planner KHÔNG CÓ GÌ ĐỂ ĐỐI CHIẾU.**

### 1.3 Các kho tri thức và bệnh của chúng

| Kho | Số | Bệnh |
|---|---|---|
| `docs/decisions/ADR-*.md` | 126 | có **lý do**, không **răng** — 2,4% từng đối chiếu code |
| `CHECK` trong DB | **121** | có **răng**, **không lý do** — `avg_price>=min_price` truy ra chính nó + `docs/archive-v1/` (kho đã chết) |
| comment `// ADR-0XX` | **2.891 link / 1.254 file = 40% code** | **đồ thị ĐÃ TỒN TẠI**, đang là văn xuôi |
| memory | 70 | hoàn hảo bên trong (70/70 field, 0/163 link gãy) — vì `description` bị `MEMORY.md` **ĂN**; từ ngoài vào bị gọi cụt |
| `seam` | — | **resolve = XOÁ** ⟹ suýt xoá vĩnh viễn ≥7 phán quyết human |
| mũi tên trỏ hư vô | **~24** | `ADR-026/030/131` · `V004/V007` (chưa từng tồn tại, mà `user.repo.ts:142` lấy V007 làm lý do code đang chạy) · 9 script · ~10 memory cụt |

`gen-facts.sh:26` tính `max` KHÔNG đếm ⟹ mù hoàn toàn lỗ V004/V007.

### 1.4 Hai điểm mù ẢNH GƯƠNG

```
ADR            : có LÝ DO,  không RĂNG   (2,4% đối chiếu code)
business rule  : có RĂNG,   không LÝ DO  (121/121 bất khả vi phạm, 0 rationale)
```

### 1.5 Nguyên tắc rút ra: **đừng đẻ CỘT — đẻ KẺ ĂN**

memory hoàn hảo **mà không có CHECK/NOT NULL nào** — vì thiếu `description` thì memory **vô hình** ⟹ hỏng thấy ngay. Trường của ADR **không ai ăn** ⟹ 17/126.

### 1.6 Xác nhận từ thị trường (v3.1) — B6 đúng ở quy mô ngành

Đánh giá 2026 các tool SDD cho đúng hai chế độ chết B6 tiên đoán: **Spec Kit · Kiro** sinh spec kỷ luật lúc mở phiên rồi để code làm nguồn sự thật ngay khi generate — *"spec là thật, nó chỉ thôi không cai trị gì nữa"*. **OpenSpec** giữ living-spec nhưng verify **cố ý KHÔNG chặn**, Given/When/Then tuỳ chọn, validate kiểm cấu-trúc không kiểm hành-vi. **Cursor rules** hướng-dẫn không ép-được. **Tessl** (spec-as-source, 125 triệu USD) engine tái-sinh closed-beta ~9 tháng, chỉ JS, output **không tất định** cùng một spec — bác MDA **bằng tiền**, ủng hộ §15 "không generate CODE".

> **Không tool nào trong nhóm có FK-tới-bằng-chứng.** Đây là **chỗ duy nhất workflow này có lợi thế thật** — và là hạt giống sản phẩm (§21).
> ⚠ Các số/nhận-định thị trường là **do chuyên gia cung cấp, tôi CHƯA verify độc lập** — dùng làm định hướng.

---

## §2. NGUYÊN TẮC THIẾT KẾ

| # | Nguyên tắc | Vì sao |
|---|---|---|
| P1 | **Đẻ KẺ ĂN, không đẻ CỘT** | §1.5 |
| P2 | **`FOREIGN KEY` là kẻ-vỡ hoàn hảo** — không mệt, không quên, không có chế độ im lặng | ~24 mũi tên hư vô |
| P3 | **ĐO thay vì KHAI** — cạnh nào đo được thì cấm khai | khai = mục; đo = tươi mỗi lần |
| P4 | **Semantic cho ỨNG VIÊN — probe QUYẾT** | embedding gần ≠ logic giống |
| P5 | **DRY là điều kiện dừng** (`§14`) | dừng khi *nghe hợp lý* = nguồn mọi lần trượt |
| P6 | **Live-read > generate-rồi-đọc** | file sinh sẵn mà stale ⟹ Claude theo luật chết ⟹ không ai vỡ ⟹ im lặng |
| P7 | **Split-brain CẤM · Composition OK** | cùng 1 fact ở 2 store = vỡ; fact khác ở 2 store = ổn |
| P8 | **Không rewrite thứ đang thắng** | `dispatch` 100% — đụng vào là ngu |
| P9 | **DB chứa TEXT có cấu trúc, KHÔNG chứa mô hình trừu tượng** | mô hình ⟹ generator có **trần** = thứ giết MDA 20 năm |
| **P10** | **MUA commodity, XÂY độc quyền** (v3.1) | 70% khối lượng thiết kế là hàng có tool trưởng thành + dễ bị model mới nuốt; giá trị nằm ở ~20% không ai làm hộ |
| **P11** | **Chứng minh nền rẻ TRƯỚC, làm to SAU** (v3.1) | biết sai sau 3 tuần thay vì 6 tháng; một số đo phủ định (§17) |

---

## §3. TIỀN LỆ THẾ GIỚI — ĐÃ KIỂM

| Ai | Làm gì | Kết cục |
|---|---|---|
| Intentional Programming (Simonyi, 1995) | graph "intentions" → sinh code | Microsoft từ chối productize 2001 |
| **JetBrains MPS** | AST là nguồn, projectional editor | **Sống** — chỉ trong **DSL hẹp** |
| **Unison** | code = AST content-addressed trong DB | **Sống** — nhưng **phải phát minh ngôn ngữ mới** |
| **Darklang** | code trong DB + structured editor | **ĐÃ GIẾT** — *"structured editor không còn hợp lý khi LLM sinh code"* |
| **Glean · Kythe · CodeQL · Sourcegraph** | file là nguồn, DB là **index dẫn xuất** | **Sống, production, quy mô Meta/GitHub** |
| **CodeGraph · GitNexus** (2026, cho AI agent) | tree-sitter → graph → MCP | **Sống** — CodeGraph MIT ~47k sao; GitNexus tích hợp Claude Code sâu (MCP+skill+hook) |
| **DOORS · Polarion · Jama** | **RTM** requirement→code→test | **30 năm**, bắt buộc DO-178C · ISO 26262 · IEC 62304 |
| **Tessl** | spec-as-source, tái sinh code | **$125M**, non-deterministic, JS-only, closed beta — bác MDA bằng tiền |

**Quy luật:** ai **cùng tồn tại với file** thì sống; ai **đòi thay file** thì chết. MPS/Unison sống vì **sở hữu trọn toolchain** — B5 nói mình **không**.

**Số đo (Codebase-Memory, 31 repo):** graph **83%** chất lượng · ~1.000 token · 2,3 call vs file-exploration **92%** · ~10.000 token · 4,8 call. **Graph rẻ 10× nhưng NGU 9 điểm** — tác giả kết luận **dùng cả hai**.
⚠ **Cảnh báo v3.1:** re-validate trên Opus 4.8 cho số THẤP hơn 4.7 — không regression mà vì **baseline native mạnh lên** (grep/read hiệu quả ngay luồng chính). Nhận định phổ biến: hãng lớn tự ship indexer native trong ~6 tháng (chưa verify) ⟹ **nửa code-graph là nửa dễ lỗi-thời nhất** ⟹ **MUA, đừng xây (P10, C3)**. **Nửa tri-thức-có-răng thì không ai làm hộ.**

**⟹ Thiết kế này = RTM (DOORS) + code-graph (MUA) + hạt nhân DB có-răng, cho Claude/agent đọc.**

---

## §4. KIẾN TRÚC TỔNG THỂ (v3.1)

```
 ┌── Ⓐ MỖI `claude` khởi động (human mở terminal, B3) chạy Ⓐ RIÊNG — role-AGNOSTIC ──────────────┐
 │   SessionStart hook: icp-wf law  → LAW CHUNG + INDEX-SCRIPT   ·  icp-wf fact-index → fact-index │
 │   ⚠ v3.1 FALLBACK (C5/B7): PG sống → inject luật-DB · PG CHẾT → inject luật-FILE V1 + cảnh báo to │
 │   KHÔNG cache · DANH TỪ kéo sau bằng wf.*                                                         │
 └──────────────────────────────────────────────────────────────────────────────────────────────┘

 ═══ PLANNER (human mở terminal planner TRƯỚC — chỉ 1) ═══════════════════════════════
   Ⓑ /master-planners (SKILL, C7) → persona planner + memory scope=planner + wf.status
   ① human "tôi muốn X"
   ② VERIFY RULE — VÒNG LẶP §8 tới DRY:  wf.trace→ứng viên→"áp Ở ĐÂU?"→neighbor→lặp
        PROBE: viết test khẳng định rule (throwaway) → ĐỎ=mới · XANH=đã có⟹CẤM ID mới
   ③ TRÌNH A1 (req→business_rule→field→code→test) → ④ HUMAN DUYỆT Ý NGHĨA (cổng — R10!)
   ⑤ cắt slice/task → wf.dispatch(k) → Redis
   ⑥ ▶RUNNABLE + chuông bell×2 → BÁO human (planner MÙ B2 + KHÔNG mở terminal B3)

 ═══ CODER k (human MỞ terminal SAU khi thấy ▶RUNNABLE — Ⓐ RIÊNG) ══════════════
   Ⓑ /master-coders k (SKILL) → persona coder + memory scope=coder + slot k(Redis)
   wf.context(k) → code + tag [impl->BR-042] + test → wf-lock→commit → wf.report → 🔔complete
   pre-commit: tag-scan + coverage CÓ-MỤC-TIÊU (chỉ test mang tag rule bị đụng — C6)

 ═══ PLANNER verify + đóng ══════════════════════════════════════════════════════════
   ⑦ human ping (hoặc LISTEN/NOTIFY ⚠ chưa đo) → đánh thức planner
   ⑧ VERIFY-DEPTH: đối chiếu HÀNH VI vs business_rule (wf.why) — KHÔNG chỉ cấu trúc
   ⑨ wf.consume(verdict, evidence_id): ghi PG TRƯỚC (FK bằng chứng) → lật Redis → git push
   đóng slice: cross-check tag×coverage TOÀN CỤC + ingest incremental (C6)

   ┌──────────────────────────────────────────────┐   ┌─────────────────────────────┐
   │  WORKFLOW-POSTGRES (DB-per-project, C2)       │   │ REDIS — NHỊP (phù du)       │
   │  thin-node(id,kind,project) + bảng-theo-kind  │◄─►│ slot.state·dispatch·pickup  │
   │  law·skill·script·requirement·business_rule   │ ⑨ │ report·consume·macro        │
   │  ·adr·memory·seam·slice·task·wid·verdict      │PG │ wf-lock (khoá git — 1 khoá  │
   │  +pgvector +pg_trgm   (edge FK về node)       │trước│ CHUNG V1+V3, B7 §18)      │
   └───────┬──────────────────────────┬────────────┘   └─────────────────────────────┘
           │ adapter (thay được, C3)  │
   ┌───────▼──────────────┐   ── code-graph = MUA, KHÔNG xây ──────────────────────────
   │ CodeGraph / GitNexus │   function·file·call·coverage ← tree-sitter 20+ ngôn ngữ
   │ (MCP, commodity)     │   resolve theo fqn; workflow-DB giữ cạnh rule↔symbol (fqn, KHÔNG PK)
   └──────────────────────┘

   GIT: 1 cây chung.  apps/ packages/ + scripts/*(body) = file LÀ nguồn (KHÔNG generate)
   ⚠ tri thức (req·rule·adr) THUẦN DB ⟹ git HẾT giữ ⟹ pg_dump = kho cuối (backup sinh tử)
```

**Bất đối xứng có chủ ý:**
- **`.md`** → **DB là nhà, file SINH RA** (round-trip tầm thường) — mua được RĂNG (FK/CHECK) trên tri thức
- **CODE** → **file là nhà; code-graph MUA ngoài; workflow-DB giữ cạnh rule↔symbol** (không xây parser, không content-address)

**LỚP RĂNG — kẻ-ăn giữ cả hệ không mục (B6):**

| Kẻ-ăn | Chặn gì | Chạy ở đâu (**KHÔNG phụ thuộc CI đang chết**) |
|---|---|---|
| **bảng-theo-kind + `NOT NULL` thật + FK** (C1) | rule không lý do · trỏ hư vô · body sai nhà · W-ID mồ côi | Postgres — `INSERT` chết |
| **`PreToolUse` deny** (đã verify) | Claude sửa TAY file generate | harness local, **trước** permission-check, chặn cả `--dangerously-skip-permissions` |
| **`pnpm test` guards** | rule không test · requirement mồ côi · generate≠file · embedding stale | `pnpm test` local |
| **cross-check tag×coverage** (C6) | Claude gắn tag SAI (R1) | **pre-commit CÓ-MỤC-TIÊU** + **đóng-slice TOÀN-CỤC** |
| **`suspect_edge` (review_state, C1)** | rule đổi nghĩa, cạnh chưa duyệt lại | query — Doorstop-style, neo khoá ỔN ĐỊNH |

> **Vì sao KHÔNG ở CI:** `ci.yml` `disabled_manually`, runner offline (32/40 `queued`), chết im lặng. Kẻ-ăn phải sống ở thứ chạy local. **CI hỏng là việc của V1.**

---

## §5. DATABASE — ĐẦY ĐỦ (v3.1: thin-node + bảng-theo-kind)

### 5.1 `node` — bảng DANH TÍNH MỎNG (đích của mọi cạnh)

> **Vì sao thin-node + bảng con (C1):** FK cần **một đích** → `node` giữ danh tính để `edge` FK về (chống ~24 mũi tên hư vô). Nhưng nhét mọi cột vào một bảng ⟹ CHECK theo-kind điều-kiện (`kind<>'business_rule' OR ...`) = **bùa, không phải răng**. Tách bảng con ⟹ `rationale text NOT NULL` là **NOT NULL THẬT**. **Edge vẫn FK về `node` nên KHÔNG tái tạo "src đa hình"** — bảng con chỉ hang off node theo `id` chung. Đây là P1 áp cho chính schema.

```sql
CREATE EXTENSION IF NOT EXISTS vector;
CREATE EXTENSION IF NOT EXISTS pg_trgm;

-- DANH TÍNH: mọi thực thể có đúng 1 dòng ở đây; edge FK về đây
CREATE TABLE node (
  id          bigserial PRIMARY KEY,
  project_id  text NOT NULL,          -- [C2] TENANT nhẹ: cô lập bằng DB-per-project + role, đây chỉ nhãn
  kind        text NOT NULL,
  name        text NOT NULL,          -- code: fqn/path (SCIP) · req/rule: tên planner đặt
  fingerprint text,                   -- hash(nội dung chuẩn hoá) → suspect
  origin      text NOT NULL CHECK (origin IN ('declared','measured','harvested')),
  created_at  timestamptz NOT NULL DEFAULT now(),
  UNIQUE (project_id, kind, name)
);
CREATE INDEX ON node USING gin (name gin_trgm_ops);
```

### 5.2 Bảng CHI TIẾT theo-kind — RĂNG THẬT (C1)

Mỗi kind có-nội-dung một bảng con, PK = FK về `node(id)`. Cột bắt buộc thành `NOT NULL` thật.

```sql
-- Ý ĐỊNH (human viết) — song ngữ + embed để semantic-search (§7 [HUMAN])
CREATE TABLE requirement (
  id           bigint PRIMARY KEY REFERENCES node(id) ON DELETE CASCADE,
  statement_vi text NOT NULL,
  statement_en text NOT NULL,
  rev          int  NOT NULL DEFAULT 1,
  state        text NOT NULL DEFAULT 'draft'
                 CHECK (state IN ('draft','approved','superseded')),
  embedding_vi vector(1024),
  embedding_en vector(1024),
  embedded_fp  text                       -- ≠ node.fingerprint ⟹ re-embed
);

-- LUẬT NGHIỆP VỤ (planner dẫn xuất) — răng: NOT NULL thật
CREATE TABLE business_rule (
  id           bigint PRIMARY KEY REFERENCES node(id) ON DELETE CASCADE,
  statement_vi text NOT NULL,
  statement_en text NOT NULL,
  rationale    text,                       -- xem CHECK dưới: NOT NULL trừ harvest
  rev          int  NOT NULL DEFAULT 1,
  state        text NOT NULL DEFAULT 'draft'
                 CHECK (state IN ('draft','approved','superseded')),
  embedding_vi vector(1024),
  embedding_en vector(1024),
  embedded_fp  text,
  -- rule MỚI phải có lý do; chỉ HARVEST từ 121 CHECK cũ mới được NULL (lý do thật đã mất)
  CONSTRAINT rule_has_reason CHECK (
    (SELECT origin FROM node WHERE node.id = business_rule.id) = 'harvested'
    OR rationale IS NOT NULL)
);
-- Nếu subquery-trong-CHECK không cho ở PG: mang cột origin xuống bảng con hoặc dùng trigger.

-- .md CÓ BODY (DB là nhà)
CREATE TABLE adr    (id bigint PRIMARY KEY REFERENCES node(id) ON DELETE CASCADE,
                     body text NOT NULL, rationale text,
                     state text CHECK (state IN ('Draft','Locked','Superseded','Partially Superseded')));
CREATE TABLE memory (id bigint PRIMARY KEY REFERENCES node(id) ON DELETE CASCADE,
                     body text NOT NULL,
                     description text NOT NULL,               -- móc-câu MEMORY.md (chữa §1.3)
                     mem_type text NOT NULL CHECK (mem_type IN ('user','feedback','project','reference')),
                     scope    text NOT NULL CHECK (scope IN ('shared','coder','planner')));
CREATE TABLE law    (id bigint PRIMARY KEY REFERENCES node(id) ON DELETE CASCADE, body text NOT NULL);
CREATE TABLE skill  (id bigint PRIMARY KEY REFERENCES node(id) ON DELETE CASCADE,
                     body text NOT NULL, scope text NOT NULL CHECK (scope IN ('coder','planner')));
CREATE TABLE slice  (id bigint PRIMARY KEY REFERENCES node(id) ON DELETE CASCADE,
                     body text NOT NULL, slice_type text,
                     state text NOT NULL DEFAULT 'active' CHECK (state IN ('active','closing','closed')));

-- ĐỘNG TỪ (body ở file bash — B5; DB giữ mô tả+path)
CREATE TABLE script (id bigint PRIMARY KEY REFERENCES node(id) ON DELETE CASCADE,
                     locator text NOT NULL,                   -- path bash
                     statement_vi text NOT NULL, statement_en text NOT NULL,  -- LÀM GÌ + KHI NÀO
                     scope text NOT NULL CHECK (scope IN ('shared','coder','planner')));

-- CODE (file là nhà; body NULL; resolve fqn qua CodeGraph)
CREATE TABLE code_symbol (id bigint PRIMARY KEY REFERENCES node(id) ON DELETE CASCADE,
                          locator text NOT NULL,             -- path[:line]
                          symkind text NOT NULL CHECK (symkind IN ('service','file','function','test')));

-- NHỊP đóng-băng (từ Redis, có FK tới bằng chứng — C: chữa verdict 20%)
CREATE TABLE verdict (id bigint PRIMARY KEY REFERENCES node(id) ON DELETE CASCADE,
                      value text NOT NULL CHECK (value IN ('accepted','rework','issue')),
                      note  text,
                      evidence_commit text NOT NULL,          -- FK-mềm: phải là commit CÓ THẬT (guard test)
                      task_id bigint NOT NULL REFERENCES node(id));

CREATE TABLE seam   (id bigint PRIMARY KEY REFERENCES node(id) ON DELETE CASCADE,
                     body text NOT NULL,
                     state text NOT NULL DEFAULT 'open' CHECK (state IN ('open','resolved')));  -- KHÔNG xoá
CREATE TABLE wid    (id bigint PRIMARY KEY REFERENCES node(id) ON DELETE CASCADE,
                     state text NOT NULL DEFAULT 'open' CHECK (state IN ('open','ticked')));

-- pgvector index trên bảng có embedding
CREATE INDEX ON requirement   USING hnsw (embedding_vi vector_cosine_ops);
CREATE INDEX ON requirement   USING hnsw (embedding_en vector_cosine_ops);
CREATE INDEX ON business_rule USING hnsw (embedding_vi vector_cosine_ops);
CREATE INDEX ON business_rule USING hnsw (embedding_en vector_cosine_ops);
```

> **Semantic-search "quét mọi kind trong 1 query" (§7 chân 3):** với bảng-theo-kind, dùng **UNION** các bảng có embedding, HOẶC 1 bảng `embedding(node_id, vec_vi, vec_en, fp)` phụ trợ để 1-query. Chọn bảng-embedding-phụ-trợ nếu số kind embed >3 (tránh UNION dài). Đây là chi tiết implementation — round-trip test không đụng.

### 5.3 `edge` — mọi quan hệ (FK về `node`)

```sql
CREATE TABLE edge (
  src         bigint NOT NULL REFERENCES node(id) ON DELETE CASCADE,
  dst         bigint NOT NULL REFERENCES node(id) ON DELETE CASCADE,
  kind        text NOT NULL,
  origin      text NOT NULL CHECK (origin IN ('declared','measured')),
  PRIMARY KEY (src, dst, kind)
);
CREATE INDEX ON edge (dst, kind);
```

| edge.kind | src → dst | origin | Lấy từ |
|---|---|---|---|
| `refines` | requirement → business_rule | declared | planner, human duyệt A1 |
| `impl` | business_rule → function | declared | tag `[impl->BR-042]` (tree-sitter, MUA CodeGraph resolve) |
| `utest` `itest` | business_rule → test | declared | tag scan |
| `constrains` | business_rule → column | measured | `pg_constraint.conrelid`+`conkey` |
| `covers` | test → function | measured | coverage `fnMap`+`f` (per-lang) |
| `cites` | function→adr · memory→memory | declared | tag / `[[link]]` |
| `supersedes` | adr → adr | declared | ADR |
| `contains` | service→file→function · slice→task | measured | scan |
| `serves` | slice → requirement | declared | planner |
| `blocks` | seam → slice | declared | planner |
| `carries` `ticks` | task→wid · slice→wid | declared | planner |
| `closed_by` | task → commit | declared | report (FK ép commit có thật) |
| `touches` | commit → file | measured | `git log` |
| `next` | migration → migration | measured | scan — **giết lỗ V004/V007** |
| `reads` `writes` | script → table | declared | đăng ký script |
| `emits` `invokes` | script → file/script | declared | đăng ký script |

### 5.4 `review_state` — neo Doorstop vào khoá ỔN ĐỊNH (C1, sửa §20 #1)

> **Vì sao KHÔNG để `reviewed_fp` trên edge:** code node dựng lại mỗi ingest (id mới) → edge `impl`/`utest` CASCADE tái-tạo → `reviewed_fp` mất → suspect hỏng. Neo review-state vào **(rule_id ổn định, target-fqn, tag-kind)** ở bảng riêng, KHÔNG trên cạnh bay-hơi.

```sql
CREATE TABLE review_state (
  rule_id     bigint NOT NULL REFERENCES node(id) ON DELETE CASCADE,  -- ổn định (human-curated)
  target_fqn  text   NOT NULL,                                        -- fqn đích (resolve qua CodeGraph)
  tag_kind    text   NOT NULL,                                        -- impl|utest|itest
  reviewed_fp text   NOT NULL,                                        -- hash(rule.statement) lúc review
  PRIMARY KEY (rule_id, target_fqn, tag_kind)
);

CREATE VIEW suspect_edge AS
  SELECT r.*, n.fingerprint AS current_fp
  FROM review_state r JOIN node n ON n.id = r.rule_id
  WHERE r.reviewed_fp IS DISTINCT FROM n.fingerprint;   -- rule đổi nghĩa, cạnh chưa duyệt lại
```

### 5.5 Kẻ-ăn trong TEST SUITE (`pnpm test` — không phụ thuộc CI)

```
test_every_business_rule_has_verifying_test()   -- rule không test = ĐỎ
test_every_requirement_refines_to_rule()        -- requirement mồ côi = ĐỎ
test_no_dangling_generated_file()               -- generate(DB) ≠ file = ĐỎ
test_embedding_not_stale()                      -- embedded_fp ≠ fingerprint = ĐỎ
test_verdict_evidence_commit_exists()           -- verdict.evidence_commit không có trong git = ĐỎ (chữa 20%)
test_every_script_registered()                  -- script chưa đăng ký / thiếu mô tả = ĐỎ
```

### 5.6 MEMORY — hai trục, ba đường vào context [HUMAN]

`mem_type` (fact loại gì) ⊥ `scope` (AI nào cần). `scope` chỉ điều khiển **ĐẨY chủ động**, KHÔNG điều khiển **TÌM ĐƯỢC** (`wf.trace` tìm mọi scope) ⟹ **mis-scope AN TOÀN** (mất một cú đẩy, không giấu memory); mặc định `shared` khi phân vân.

| `scope` | Kẻ giao vào context | Khi nào |
|---|---|---|
| `shared` | MEMORY.md-STUB → `icp-wf fact_index` (kéo) | Ⓐ mọi phiên — mù vai |
| `coder` | persona pull `icp-wf persona coder` | Ⓑ `/master-coders` |
| `planner` | persona pull `icp-wf persona planner` | Ⓑ `/master-planners` |

**⚠ CHƯA ĐO (chốt ở §17 bước 1):** harness recall đọc theo **MEMORY.md index** hay **quét thư mục memory**? + MEMORY.md dạng stub harness có chấp nhận? Quyết role-memory sinh-file hay chỉ-DB.

### 5.7 SCRIPT — đối tượng THỨ BA + BOOT = ĐỘNG TỪ [HUMAN]

```
BOOT (nhỏ·ổn định·pull):  LAW + INDEX SCRIPT (scope đúng vai) + INDEX FACT NHẸ (tên+móc-câu)
ON-DEMAND (nặng·wf.*):    rule body · memory body · code · fact
```
Mỗi mục index PHẢI có MÔ TẢ (không tên trơ): script = `name·LÀM GÌ·KHI NÀO·vai`; fact = `name·móc-câu`. Danh sách tên trơ = Claude đoán cách dùng = sai.

### 5.8 TENANT — v3.1: DB-per-project + PG-role (C2, thay RLS/GUC)

> **Vì sao đổi (phản biện 2(e)):** workflow-DB là store **DẪN XUẤT** — code-graph, 121 rule (từ `pg_constraint`), git log đều **tái sinh bằng ingest**. Bài học V011 "thêm sau = đau" áp cho store **NGUỒN** (không tái tạo nổi tenant data), KHÔNG áp cho dẫn xuất (đổi schema → re-ingest). Cô lập **rẻ hơn + mạnh hơn RLS**:

```
workflow-Postgres instance riêng (B8, tách icp_app)
  ├── DB  icp_wf_sicp     (role: wf_sicp)      ← project sicp
  └── DB  icp_wf_<other>  (role: wf_<other>)   ← project khác
Redis: wf:{project_id}:slot:k · wf:{project_id}:w:git   (khoá git per-project)
```
- Không GUC, không quên set, **không isolation-test** (cô lập ở tầng DB/role, không app-filter).
- `project_id` trên node = **nhãn**, không phải cơ chế cô lập.
- **RLS/shared-PG chỉ sống lại khi host chung nhiều user không-tin-nhau (§22 SaaS)** — hoãn tới lúc đó, KHÔNG giết ý tưởng.

---

## §6. BA TẦNG — `requirement → business_rule → code` [HUMAN]

```
requirement   "khách mua 10 SKU thì tặng 3"          ← HUMAN viết (song ngữ)
    │ refines
    ├── business_rule  gift_qty = floor(qty/10)*3     ← PLANNER dẫn xuất, tự đặt tên BR-042
    └── business_rule  tặng phải trừ tồn kho              human duyệt A1 (Ý NGHĨA — R10)
             │ impl          │ constrains         │ utest
             ▼               ▼                    ▼
        applyGiftLines()  order_line.gift_qty   test_gift_decrements_stock ──covers[ĐO]──> decrementStock()
```

- **HUMAN** nói *cần gì* → `requirement` · **PLANNER** dẫn xuất → `business_rule` (tự đặt tên) · **CODER** → code + tag.
- **`BR-042` — mã ĐỤC, ổn định, VÔ NGHĨA [HUMAN]:** nhét nghĩa vào danh tính (`~avg-price-floor~`) ⟹ đổi nhãn = mọi tag chết (cùng bài "fqn làm PK vỡ"). Nghĩa sống ở `statement_vi/en` + cạnh. Cấp số: `nextval('br_seq')`, `UNIQUE(kind,name)` — DB atomic, không ai "phán số kế tiếp".
- **`fingerprint` gánh anti-rot, KHÔNG `rev` trong tag:** tag chỉ trỏ `BR-042` (không mang rev). `rev` = nhãn người-đọc. Cơ chế ép = `fingerprint` (máy, tự động) → `suspect_edge` (§5.4).

---

## §7. CƠ CHẾ TÌM RULE — 4 CHÂN, MẠNH DẦN

| # | Chân | Cách | Độ tin |
|---|---|---|---|
| 1 | **Cấu trúc** | rule đụng field nào → mọi rule `constrains` field đó | **cao** — không ML, theo CHỦ THỂ |
| 2 | **Chữ** | `pg_trgm` trên `name`+`statement_vi/en` | trung bình |
| 3 | **Nghĩa** | `pgvector` quét mọi kind (§5.2) | thấp — **ứng viên, không kết luận** |
| 4 | **HÀNH VI** | **viết test khẳng định rule, chạy** | **QUYẾT ĐỊNH** |

**Chân 4 — cái duy nhất chứng minh ĐƯỢC ÉP:**
```sql
BEGIN; INSERT INTO catalog_offer(avg_price,min_price) VALUES (5,10); ROLLBACK;
-- bị từ chối ⟹ rule ĐÃ ép   |   được nhận ⟹ CHƯA ép
```
XANH ⟹ rule tồn tại (dù chưa ai đặt tên). ĐỎ ⟹ thật sự mới. **Phần thưởng HARVEST:** test XANH mà chưa có rule ⟹ luật ngầm không tên → đặt ID → tag → coverage chỉ code.

**⚠ P4 — chân 3 KHÔNG được là kết luận:** `avg_price >= min_price` vs `avg_price <= max_price` cực gần vector, NGƯỢC logic (negation = chỗ embedding dở nhất) + người đọc là Claude *"rất muốn làm bạn hài lòng"*.

**⚠ Chốt chặn:** **121/121 rule hôm nay VÔ HÌNH với semantic** (chỉ là biểu thức SQL, không chữ). ⟹ **Việc đầu KHÔNG phải dựng schema — là VIẾT LỜI cho 121 CHECK (§17 bước 2).**

**⚠ re-ranker (§12.1) HOÃN** — bắt đầu KHÔNG reranker: `pgvector` top-K → planner đọc thẳng. Thêm `bge-reranker-v2-m3` CHỈ KHI đo thấy >~30 ứng-viên/lần hoặc negation-confusion thật. Disambiguate THẬT vẫn là PROBE (chân 4).

**⚠ R10 (§16):** cả 4 chân chứng minh **tồn tại / được-ép**, KHÔNG chứng minh **đúng-ý-human**. Correctness-vs-intent = cổng A1 (khung tổng quát); máy chỉ vá được ở ngách hình-thức-hoá-được (§22).

---

## §8. CƠ CHẾ TRACE — VÒNG LẶP, DỪNG BẰNG DRY [HUMAN]

```
wf.trace("mua 10 tặng 3")
 vòng 1  semantic + trgm + cấu trúc          → ứng viên mơ hồ
 vòng 2  mỗi ứng viên → NÓ ĐANG ÁP Ở ĐÂU?    (rule mồ côi = loại)
 vòng 3  trace lộ neighbor KHÔNG ai hỏi
 DỪNG    probe quyết được  HOẶC  2 vòng LIÊN TIẾP không node mới  (= §14 DRY)
```
`wf.trace` trả **trạng thái vòng lặp** (mới ra gì · xác nhận gì · ĐÃ BÁC gì — §14 "ghi cả giả thuyết bị bác"), không phải danh sách.

**`rule → slice/task` SUY RA:** `rule ─impl→ function ←contains─ file ←touches─ commit ←closed_by─ task ←contains─ slice`. 5 hop, một JOIN. **Cross-check:** tag nói X impl R nhưng test của R không chạm X ⟹ một trong hai SAI (hai chân ĐO kẹp hai chân KHAI).

---

## §9. RANH GIỚI REDIS ↔ POSTGRES [HUMAN: giữ Redis]

**Số:** Redis ~0,1–0,2ms · PG local ~1–5ms + connect 5–20ms. relay chạy vài lần/task; task phút→giờ ⟹ **20ms vô hình. Tốc độ KHÔNG phải lý do.** Lý do thật (P8): **relay KHÔNG có bệnh đo được** (`dispatch` 20/20). Bệnh ở `report`(31%)/`verdict`(20%)/`seam`(xoá) — chữa bằng **FK tới bằng chứng**, không bằng đổi store.

| Redis — NHỊP (chết sau 1 task) | Postgres — TRÍ NHỚ (sống lâu ⟹ có FK) |
|---|---|
| slot.state·dispatch·pickup·report(live)·consume·macro·pending-human·**wf-lock** | law·requirement·business_rule·adr·memory·seam·slice·task·wid·verdict·edge |

**Giao đúng MỘT chỗ — `consume`:** ghi PG trước (verdict + FK bằng chứng) → rồi lật Redis (idempotent, retry được). **P7:** `relay report` set state+ledger là MỘT thao tác — tách 2 store = VỠ. `wf status` đọc slot(Redis)+seam(PG) là fact khác nhau = OK.

---

## §10. VAI TRÒ — TỪNG BƯỚC

### 10.1 HUMAN
1. Nói cần gì → `requirement` (song ngữ) · 2. **Duyệt A1 — Ý NGHĨA của rule (cổng R10)** · 3. Mở terminal coder (B3).
**KHÔNG:** đặt req ID · bump rev · gõ tag · làm thư ký. [HUMAN]

### 10.2 PLANNER — `/master-planners` (SKILL)
```
Ⓐ SessionStart: icp-wf law (fallback file V1 nếu PG chết) + fact_index
Ⓑ /master-planners → persona + memory scope=planner
1 wf.status  2 nhận requirement  3 wf.trace → VÒNG LẶP §8 tới DRY
4 dẫn xuất rule: BR-042 · statement_vi+en · rationale
5 PROBE: test throwaway → ĐỎ=mới (XANH=đã có ⟹ CẤM ID mới)
6 A1 → human duyệt Ý NGHĨA   7 wf.insert(rule) → FK+CHECK ép
8 cắt slice+task, edge(serves)→requirement (slice KHÔNG trỏ requirement ⟹ CẤM cắt)
9 wf.dispatch(k) → Redis   10 ▶RUNNABLE + chuông bell×2
11 human ping → thức   12 VERIFY-DEPTH: wf.why → HÀNH VI vs rule
13 wf.consume(verdict, evidence_id) → PG trước → Redis → push
14 còn task → 9; hết → đóng (ingest thay gen-facts)
```
**Giữ:** không viết source (probe = throwaway) · đầu não = phân-tích+phân-phối+deep-verify · anti-over-hold · anti-idle.

### 10.3 CODER — `/master-coders k` (SKILL)
```
1 wf.context(k) → task+requirement CHA+rule+field+ADR+memory (KHÔNG grep)
2 BƯỚC 0: hiểu rule (không hiểu ⟹ CẤM code)
3 code + tag [impl->BR-042]   4 test + tag [utest->BR-042]
5 wf-lock → commit explicit-path
6 pre-commit: tag-scan + coverage CÓ-MỤC-TIÊU (chỉ test mang tag rule bị đụng — C6)
7 wf.report(k, commit_sha) → FK ép commit CÓ THẬT   8 chuông complete
```
**Giữ:** không push · không `clean`/`stash`/`reset --hard`.

---

## §11. `CLAUDE.md` MỚI + HOOKS (v3.1: verify + fallback)

**`CLAUDE.md` teo ~6 dòng** (P6):
```markdown
# ICP — Luật
Luật THẬT nằm trong workflow DB, inject vào context lúc mở phiên.
KHÔNG thấy luật trong context → DB workflow chết → hook đã fallback luật-file V1 + cảnh báo. Nếu cả file này cũng trống → DỪNG, báo human.
CẤM sửa file này. Sửa luật = sửa trong DB (`icp-wf law edit`).
```

**BỐN file NẠP-VÀO-ĐẦU = STUB MỎNG (KHÔNG gom — mỗi cái ở path harness cần) [HUMAN]:**

| File | Path | Stub trỏ | Nguồn thật |
|---|---|---|---|
| `CLAUDE.md` (~6 dòng) | repo root | `SessionStart` → `icp-wf law` | PG `law` + INDEX SCRIPT |
| `MEMORY.md` (~4 dòng) | `~/.claude/.../memory/` | `SessionStart` → `icp-wf fact_index` | PG — fact-index nhẹ |
| `master-planners` (~5 dòng) | **`.claude/skills/`** (C7) | việc đầu: `icp-wf persona planner` | PG `skill` + memory planner |
| `master-coders k` (~5 dòng) | **`.claude/skills/`** | việc đầu: `icp-wf persona coder --slot=k` | PG `skill` + memory coder + Redis slot |

> **v3.1 (C7): master-* = SKILL** (`.claude/skills/`, khuyến nghị cho mới; commands vẫn chạy). Skill nạp on-invoke, hỗ trợ frontmatter điều-khiển-gọi.

**HOOKS — ĐÃ VERIFY (v3.1):**

| Hook | Chạy | Mua được (verified) |
|---|---|---|
| **`SessionStart`** (Ⓐ) | `icp-wf law` (mù vai) + `fact_index` → `additionalContext` | **inject vào context: XÁC NHẬN.** ⚠ **FALLBACK (C5/B7):** PG sống → luật-DB; PG chết → **luật-file V1 + cảnh báo to** (KHÔNG brick V1). |
| **`UserPromptSubmit`** | `icp-wf state --slot=k` | **inject mỗi lượt: XÁC NHẬN** (chống B2) |
| **`PreToolUse`** `Edit`/`Write` | path file SINH RA → `permissionDecision:"deny"` | **chạy TRƯỚC permission-check, chặn cả `--dangerously-skip-permissions`; hook chỉ SIẾT không NỚI: XÁC NHẬN.** Quy ước → RÀNG BUỘC |

**⚠ PreToolUse KHÔNG chặn `apps/`/`packages/`** (code = file-nguồn). **PostToolUse KHÔNG dùng làm ép** (tool đã chạy).
**PERSONA PHẢI DẠY DÙNG TOOL [HUMAN]:** planner *"wf.trace tới DRY trước khi cắt · wf.exists+probe trước khi đặt BR · wf.why verify HÀNH VI"* · coder *"wf.context (KHÔNG grep) · không hiểu rule ⟹ dừng · tag · wf.scripts"*. Thiếu persona-dạy-tool ⟹ có tool mà quay lại grep.

---

## §12. HAI CỬA TỚI DB — CLI (boot) vs MCP (trong phiên) — CHUNG RUỘT [HUMAN]

Boot là **bash thuần** (SessionStart, chưa có LLM loop) ⟹ KHÔNG gọi MCP được. Hai cửa, cùng một Postgres, **cùng thư viện truy vấn (Python — B10):**

```
CỬA 1 — icp-wf CLI (bash → workflow-PG · BOOT)
   icp-wf law            → luật CHUNG + INDEX SCRIPT      ← CLAUDE.md stub (Ⓐ)
   icp-wf fact_index     → fact-index nhẹ                  ← MEMORY.md stub (Ⓐ)
   icp-wf persona <role> → persona + memory role + status  ← master-* stub (Ⓑ)

CỬA 2 — wf.* MCP (Claude → workflow-MCP → workflow-PG · TRONG PHIÊN)
   ── v3.1: ÍT TOOL (2 dùng 80% thời gian, mỗi schema tốn context mọi phiên) ──
   wf.context(task)   → task+requirement+rule+field+ADR+memory   ← thay GREP  (⭐ dùng nhiều nhất)
   wf.why(node)       → requirement cha+rule+rationale+field+code+test+slice  (⭐ verify HÀNH VI)
   ── phụ (mở khi cần) ──
   wf.trace · wf.exists · wf.harvest · wf.scripts · wf.orphans
   wf.status|dispatch|pickup|report|consume  → thay scripts/relay

Ở đâu — TẤT CẢ workflow-space Python (B8/B10), KHÔNG apps/packages:
   icp-wf CLI · workflow-MCP · thư viện truy vấn · migration workflow-DB · adapter CodeGraph
   → dir `workflow/` (venv riêng) · workflow-Postgres instance riêng (DB-per-project §5.8)
```

> **CHUNG RUỘT:** `icp-wf` CLI (bash→PG, boot) và `wf.*` MCP (Claude→PG, làm) **import cùng thư viện truy vấn Python**. Một logic, hai cửa, **cả hai của WORKFLOW, KHÔNG dính project (B8).**

**Luật cứng:** `wf.exists`/`wf.trace` trả "không tìm thấy" = **ứng viên rỗng, KHÔNG phải bằng chứng vắng mặt**. Chỉ **probe (chân 4)** mới kết luận.

**§12.1 re-ranker = HOÃN** (đã nói §7). Lọc 50→6 ứng viên = **RE-RANK (chấm điểm), KHÔNG reasoning** — nếu cần thì `bge-reranker-v2-m3` (568M, đa ngôn ngữ, CPU), KHÔNG LLM-nhỏ-suy-luận (dính §14-danger). Quyết DRY/KHUNG = PLANNER+PROBE, CẤM model nhỏ.

---

## §13. GENERATE — CO LẠI GẦN BẰNG KHÔNG

Chạy **khi DB ĐỔI** → sinh lại file ảnh hưởng → commit CÙNG commit.

| Kẻ đọc | File | Ghi chú |
|---|---|---|
| harness | `MEMORY.md`=STUB (kéo fact-index) · `memory/*.md` shared = generate **CÓ ĐK** (§5.6 chưa đo) | stub, body shared chỉ sinh nếu recall quét-thư-mục |
| ~~OFT~~ | ~~`doc/req/*.md`~~ | **BỎ** — tag scan tree-sitter (MUA CodeGraph) |
| human | ~~`docs/**`~~ → **hỏi planner "list ..."** | duyệt Ý NGHĨA ở cổng A1 |
| git | requirement/rule/adr **CHỈ DB** ⟹ `pg_dump` là kho cuối | backup sinh tử |

> **Claude KHÔNG trong bảng — Claude HỎI, không đọc file.** Sau bỏ OFT+docs-human+MEMORY-stub: generate ≈ **KHÔNG**, chỉ còn body `memory scope=shared` NẾU recall quét-thư-mục.

---

## §14. INGEST — ĐO, KHÔNG KHAI (v3.1: incremental, MUA code-graph)

| Nguồn | Ra | origin |
|---|---|---|
| `pg_constraint` (`conrelid`+`conkey`) | 121 `business_rule` + `constrains`→column | measured |
| `information_schema` | table · column | measured |
| **tree-sitter tag-scan (MUA CodeGraph, C3)** | `impl`·`utest`·`itest` — quét comment `[impl->BR-042]` | declared |
| **coverage per-lang** | `covers` (test→function) | measured |
| `git log` | commit · touches | measured |
| `infra/migrations/*.sql` | migration · next | measured |
| **CodeGraph/GitNexus (MUA)** | file · function · test (resolve fqn) | measured |
| `scripts/*` đăng ký | script + reads/emits | declared |

**§14.1 v3.1 — MUA code-graph, cắm qua ADAPTER (C3, P10):** KHÔNG tự dựng tree-sitter→graph. Cắm **CodeGraph/GitNexus** qua MCP; workflow-DB giữ **cạnh rule↔symbol** (khoá theo **fqn**, resolve qua CodeGraph, **KHÔNG làm PK** — §15). Adapter **thay được** (CodeGraph có thể bị hãng lớn bóp/bỏ) ⟹ lớp răng KHÔNG dính ruột nó.

**§14.2 v3.1 — CADENCE + ATOMICITY (C6, sửa §20 #2):**
- **pre-commit (coder):** tag-scan + coverage **CÓ MỤC TIÊU** (chỉ chạy test mang tag của rule bị đụng) — **giây, không phút.**
- **đóng-slice (planner):** cross-check tag×coverage **TOÀN CỤC**.
- **ingest INCREMENTAL** (không drop+rebuild toàn bộ): chỉ dựng lại node/edge của file thay đổi (git diff), **trong 1 transaction** (query thấy trước-hoặc-sau, không thấy giữa). Neo review-state ở khoá ổn định (§5.4) nên incremental KHÔNG mất suspect.

**⚠ SCIP định danh theo TÊN** ⟹ resolve, KHÔNG làm PK. Danh tính code không cần ổn định (cạnh đo dựng lại mỗi lần; đổi tên ⟹ node mới, cũ CASCADE).

**⚠ Ngôn ngữ MỚI [HUMAN]:** khai (1) grammar tree-sitter + map comment/function (~20-50 dòng+test), (2) coverage tool → cạnh `covers`. Không viết parser mới. Đường lui: OFT-as-importer riêng nếu tree-sitter gắn-tag khó.

---

## §15. KHÔNG LÀM — VÀ VÌ SAO

| Không làm | Vì sao |
|---|---|
| **generate CODE** | trần MDA. Darklang chết đúng đây, **LLM là lý do họ chết**; Tessl bác bằng $125M non-deterministic |
| **lưu `body` code trong DB** | 2 nhà ⟹ đồng bộ ⟹ lưu `fingerprint` là đủ bắt drift |
| **TỰ DỰNG code-graph (v3.1)** | commodity + dễ lỗi-thời nhất (§3) — MUA CodeGraph/GitNexus (P10/C3) |
| **node một-bảng-mọi-cột (v3.1)** | CHECK theo-kind = bùa; thin-node + bảng-con = NOT NULL thật (C1) |
| **RLS/GUC 3-field cho 1-project (v3.1)** | store dẫn xuất; DB-per-project + role rẻ+mạnh hơn (C2) |
| **`fqn` làm PK** | đổi tên ⟹ mọi cạnh chết im lặng. Giải bằng **đo lại** |
| **Neo4j / vector-DB riêng** | vài triệu cạnh — PG đủ; JOIN vector với rule tránh 2-phase commit |
| **rule trong YAML/Confluence/Notion** | không diff, không CI, **không ép** |
| **OFT (OpenFastTrace)** | buộc requirement thành FILE = bản sao DB = drift + Java dep. Tag-scan tree-sitter thay |
| **bỏ Redis** | P8 — relay không bệnh đo được [HUMAN] |
| **harvest 378k dòng (v3.1)** | R10 — flood cổng human (bottleneck correctness); harvest **CHỌN LỌC** |
| **re-ranker/LLM-nhỏ-lọc NGAY** | chưa cần; probe quyết, không phải reranker |
| **rời Postgres** | chỉ khi ĐO: >20M cạnh & traversal>500ms · >50M vector |

---

## §16. RỦI RO ĐÃ BIẾT — GHI RA TRƯỚC

| # | Rủi ro | Đối phó |
|---|---|---|
| R1 | Claude gắn tag SAI | **cross-check coverage** (cơ chế DUY NHẤT) — §14.2 pre-commit có-mục-tiêu + đóng-slice toàn-cục |
| R2 | coverage cho TẬP CHA (logger/ORM/DI) | xếp hạng theo độ-đặc-hiệu lúc query; chính xác thật = mutation testing (hoãn) |
| R3 | embedding gần ≠ logic (negation) | P4 — semantic ứng viên; probe quyết |
| R4 | 121 rule VÔ HÌNH với semantic | **harvest = việc số 1** (§17 bước 2): đề xuất statement+rationale → human duyệt A1 → probe chắc XANH |
| R5 | `fingerprint` quá nhạy (whitespace) | chuẩn hoá text TRƯỚC hash |
| R6 | rule đổi nghĩa né review | `fingerprint` → `suspect_edge` tự động, độc lập rev (§5.4) |
| R7 | Xây trên cổng đã chết (CI) | kẻ-ăn ở `pnpm test`+pre-commit+`PreToolUse` (chạy local). CI hỏng = việc V1 |
| R8 | `grep` nói dối im lặng (locale) | script ingest ép `LC_ALL=C` |
| R9 | Tôi thiết kế trước khi nhìn (7 lần bị bác/buổi) | mọi khẳng định kèm path/lệnh; chưa đo ghi "chưa đo" |
| **R10** | **KHÔNG AI VỠ khi rule sai NGHĨA (v3.1)** | probe chứng minh *tồn tại*, không *đúng-ý*. rationale sai / statement lệch sắc thái ⟹ FK không cắn, probe vẫn XANH. Kẻ-ăn duy nhất = **cổng A1 human** (đúng hạng-31%). **KHÔNG có vá rẻ ở khung tổng quát** ⟹ (a) **giữ rule ÍT** (human còn sức đọc), (b) harvest **chọn lọc**, (c) **vá thật chỉ ở ngách hình-thức-hoá-được** (§22 — formal-correctness) |
| **R11** | **Phụ thuộc CodeGraph/GitNexus (v3.1)** — có thể bị hãng lớn bóp/bỏ | **adapter thay-được**; lớp răng (rule↔symbol, FK-bằng-chứng) KHÔNG phụ thuộc ruột nó |
| **R12** | **`claude -p` exit-code phân biệt xong/cần-help/crash chưa rõ (v3.1)** | verify: headless expand = CÓ; exit-code **chưa đủ tài liệu** ⟹ **test trong §19 wrapper**; cô lập phiên bằng `--session-id`/`--no-session-persistence` |
| **R13** | **Auto-formalize NL→property có TRẦN (v3.2, sản phẩm)** — không phải requirement nào cũng hình-thức-hoá-được | Khoanh scope vào **lớp hình-thức-hoá-được của ngách** (safety-property·state-machine·biên-số·MISRA/AUTOSAR-contract); phần còn lại rơi về răng-nhẹ + cổng human. **KHÔNG hứa chứng minh mọi thứ.** Đo tỉ lệ formal-hoá-được trên corpus mồi TRƯỚC khi tuyên năng lực |
| **R14** | **Chu kỳ bán + qualification NHIỀU NĂM (v3.2)** — ngách quản-chế chậm, cần track-record | Đây là **chi phí của "uncopyable"** (hào cần thời gian, §22.1-L5) — chấp nhận có chủ ý. Vào bằng **design-partner Tier-1/2** (nhanh hơn prime); dùng continuous-certification tạo doanh thu SỚM trước khi đạt full-qualification |

---

## §17. ĐƯỜNG ĐI — V3-LITE, MỘT SỐ ĐO PHỦ ĐỊNH (v3.1, C9/P11)

**Nguyên tắc:** chứng minh nền rẻ TRƯỚC; **đo đúng MỘT số** rồi mới làm to. V3 dựng CẠNH V1 (§18). **Biết sai sau 3 tuần thay vì 6 tháng.**

**V3-LITE (2–3 tuần, ship + đo được):**
1. **Bước 2 nguyên vẹn — viết lời cho 121 CHECK** (`statement_vi/en`+`rationale`). Giá-trị/rủi-ro tốt nhất; rule đã có răng, chỉ thêm lời.
2. **workflow-DB: DB-per-project, thin-node + bảng-theo-kind** (§5). KHÔNG RLS/GUC.
3. **KHÔNG tự dựng code-graph — cắm CodeGraph/GitNexus qua adapter.** workflow-DB giữ requirement·business_rule·adr·slice·task·verdict + cạnh rule↔symbol (fqn resolve).
4. **`wf.why(rule)` + `wf.context(task)` — 2 tool** (không 10).
5. **Persona planner: verify HÀNH VI vs rule + consume ghi verdict với FK evidence.**
6. **Guard `pnpm test`:** rule không test = ĐỎ · requirement mồ côi = ĐỎ · verdict.evidence không có commit = ĐỎ.

> **SỐ ĐO PHỦ ĐỊNH:** verdict "có nội dung" từ **20% → ?** sau 20 task. **Không nhúc nhích ⟹ §5–§14 không cứu được** — dừng, nghĩ lại. Đây là chân-4/probe áp cho CHÍNH chương trình.
> ⚠ **Đo KỶ LUẬT, KHÔNG đo tính-ĐÚNG rule** (R10). Tính-đúng chỉ đo được ở ngách formal (§22).

**Chi tiết bootstrap (§20 #3 🔴):** populate DB **DƯỚI V1** (icp-multi-*, đọc file) TRƯỚC; **CHỈ flip CLAUDE.md→stub SAU khi DB có law** (+ hook fallback C5).

**Bước sau (khi V3-lite đo tốt):** nạp ADR+slice+BACKLOG · lớp truy-vết đầy đủ (tag scan + cross-check) · §21 sản phẩm.

**Bỏ/hoãn:** B9 RLS (§22 SaaS) · re-ranker (§7) · harvest 378k (R10) · **§19 master-auto bash → thử Dynamic Workflows trước** (nếu nó lo được vòng dispatch→verify→dispatch thì §19+planner-wake tan).

---

## §18. SONG SONG — KHÔNG PHÁ V1 [HUMAN — B7] (v3.1: sửa rollback)

| V1 (giữ nguyên, lưới an toàn) | V3 (dựng cạnh) |
|---|---|
| `.claude/commands/icp-multi-*.md` | `.claude/skills/master-*` (C7) |
| relay Redis | `wf.*` MCP + workflow-DB (Python) |
| `CLAUDE.md` 209 dòng (file) | `CLAUDE.md` teo + SessionStart inject |

**Chuyển bằng cái tên bạn gõ:** `icp-multi-coders` → `master-coders`. Hai bộ đồng thời.

> **⚠ ROLLBACK KHÔNG PHẢI "0 giây" (C5/B7 sửa):** CLAUDE.md + SessionStart Ⓐ **dùng chung V1+V3, role-agnostic**. Flip CLAUDE.md→stub fail-closed ⟹ PG chết thì **V1 CŨNG chết** (lưới nối vào thứ nó bảo hiểm). **Sửa:** hook Ⓐ **fallback có điều kiện** (PG sống→luật-DB · PG chết→luật-file V1 + cảnh báo to). Rollback thật = giữ fallback HOẶC `git revert CLAUDE.md`, **KHÔNG** phải "gõ lại tên cũ".

**⚠ KHOÁ KHÔNG tách được (B4):** V1+V3 ghi **cùng cây git** ⟹ V3 dùng LẠI `wf-lock` Redis của V1 (cùng một khoá), KHÔNG dựng khoá riêng. **Trí nhớ tách đôi được, KHOÁ thì KHÔNG.**

---

## §19. `master-auto` — BỘ LÁI TỰ ĐỘNG (v3.1: mắt xích gốc = CÓ, verified) [HUMAN]

**Chạy happy-path một mình, CHỈ chuông khi cần human.** wrapper **bash** (human chạy 1 LẦN), KHÔNG phải Claude skill (phiên không tự spawn phiên — B3).

**PHẠM VI — chỉ PHA THỰC THI:**
```
PHA THIẾT KẾ (human hands-on)          PHA THỰC THI (master-auto tự lái)
requirement→rule→cắt task→A1 DUYỆT ──► task ĐÃ DUYỆT→code→verify→consume→task kế
  ▲ chốt-khung, judgment cao (§14b)       ▲ lặp lại, ít judgment mới
```

**Ranh giới AUTO ↔ CHUÔNG:**

| AUTO | CHUÔNG → human |
|---|---|
| planner phân tích trong scope ĐÃ duyệt → cắt task | A1 DESIGN (requirement/slice MỚI — cổng ngữ nghĩa) |
| dispatch→code→report→verify→consume→task kế | needs-decision / blocked (mơ hồ · stop-condition §4) |
| rework loop | seam mở · planner không đạt DRY / nghi khung (§14) |
| context-manage (fresh-per-task) | verify high-consequence · crash/exit≠0 |

**Cơ chế:**
```
master-auto (human chạy 1 lần):
  loop poll relay:
    slot 'dispatched'   → spawn: claude -p "/master-coders k"  --session-id <uuid>  (verified expand)
    report chờ consume  → spawn: claude -p "/master-planners"   (verify+consume+dispatch)
    requirement mới     → spawn planner
  marker cần-human (needs-decision·blocked·seam·macro:pending-human) → 🔔 CHUÔNG, DỪNG, chờ human
  + kill-switch + trần số-restart
```

**v3.1 — ĐÃ VERIFY:**
- **"mắt xích gốc" `claude -p "/master-coders k"` expand = CÓ.** Slash command/skill expand trong headless print-mode (ngoại lệ: lệnh TTY-only như `/login`). Spawn loop từ bash **an toàn**.
- **Cô lập phiên:** dùng `--session-id <uuid>` mỗi lần HOẶC `--no-session-persistence` (tránh interleave transcript).
- **⚠ CÒN CHƯA ĐO (R12):** exit-code phân biệt xong/cần-help/crash **chưa đủ tài liệu** ⟹ **test trong wrapper** (map exit-code → hành vi), hoặc dùng marker relay (đã có) thay exit-code.

**Context: mỗi task một phiên SẠCH** ⟹ B1 tan mà không cần đo 85%; state ở Redis/DB nên phiên vứt được (`wf.context` đọc lại); không compact.

**⚠ RANH GIỚI SINH-TỬ:** human ra khỏi vòng ⟹ **lớp kẻ-vỡ (FK-bằng-chứng · cross-check · test) là backstop DUY NHẤT** chống "đóng-dấu lười" (verdict 20%). Kẻ-vỡ yếu = auto nhân lỗi im lặng. **§14b: khung sai thì planner không tự biết** ⟹ **cổng A1 human = chốt-khung chính, KHÔNG BAO GIỜ bỏ.**

**⚠ THAY THẾ CÂN NHẮC:** **Dynamic Workflows** (native: agent/parallel/pipeline/phase, script 0-token, resume, concurrency-cap, schema-validate) lo được vòng dispatch→verify→dispatch **tốt hơn bash** (§19 mất resume/schema/cap). **Thử Dynamic Workflows TRƯỚC khi viết bash master-auto** — nếu đủ thì §19+planner-wake tan. ⚠ Lưu ý chi phí fan-out (memory `avoid-workflow-fanout-cost`).

---

## §20. GUARDRAIL TRIỂN KHAI — bắt lúc CODE (v3.1: 3🔴 đã nuốt vào thân)

| # | Mức | Vấn đề | Guardrail — **trạng thái v3.1** |
|---|---|---|---|
| 1 | ✅ | `reviewed_fp` vỡ khi re-ingest | **ĐÃ SỬA C1/§5.4:** `review_state` neo khoá ổn định `(rule_id, fqn, tag_kind)`, KHÔNG trên edge |
| 2 | ✅ | ingest cadence + atomicity | **ĐÃ SỬA C6/§14.2:** incremental (git diff) trong 1 transaction; pre-commit có-mục-tiêu, cross-check toàn-cục lúc đóng-slice |
| 3 | ✅ | bootstrap chicken-egg | **ĐÃ ĐƯA vào §17:** populate DB dưới V1 TRƯỚC; flip stub SAU; **+ hook fallback C5** (V1 không chết khi PG rỗng) |
| 4 | 🟡 | PreToolUse deny scope | generate ≈ 0 ⟹ deny chỉ cần cho memory-shared body (§5.6) nếu có; nếu generate=0 thì ~không cần |
| 5 | 🟡 | MEMORY.md-stub rủi nhất | Fallback: riêng MEMORY.md revert generated-index (3 stub kia không đụng) — đo ở §17 bước 1 |
| 6 | 🟡 | workflow-DB tự migrate | Migration riêng workflow-DB (tách project, B8); số kế tiếp theo FACTS của workflow-DB |
| 7 | ✅ | B3 scope (master-auto spawn) | Ghi rõ: B3 cấm PHIÊN-spawn-phiên; master-auto (bash ngoài) = ngoại lệ human-uỷ-quyền |
| 8 | ✅ | body_home CHECK permissive | **ĐÃ SỬA C1:** bảng-theo-kind ⟹ `body NOT NULL` thật cho bảng có body; bảng code không có cột body |
| 9 | 🟢 | planner fresh-per-report mất context | state-ở-DB (ledger/verdict); thiếu → planner đọc lại qua `wf.status` |

**⟹ Toàn bộ 3🔴 v3.0 ĐÃ nuốt vào thân v3.1. Không cái nào đổi kiến trúc nền ⟹ SẴN SÀNG triển khai V3-lite.**

---

## §21. SẢN PHẨM — RTM-CÓ-RĂNG + PROOF-ENGINE CHO AGENT [chi tiết của §00]

> **Trục chính (§00), KHÔNG tách doc.** Lớp răng rút ra thành **MCP server + PROOF-ENGINE server-side**. Workflow-sicp (§0–§20) dogfood nó.

**LỖ THỊ TRƯỜNG:** 6 tool SDD (Spec Kit · Kiro · OpenSpec · BMAD · Cursor rules · Augment) **sinh spec nhưng KHÔNG ÉP** — spec thôi cai trị code khi generate bắt đầu. **Không tool nào có FK-tới-bằng-chứng, verify HÀNH VI, hay CHỨNG MINH.** Agent sinh code càng nhanh, nút cổ chai dời sang *chứng minh code thoả requirement*. **Không ai đóng vòng "requirement → code có THẬT SỰ thoả không, kèm PROOF".**

**HÌNH SẢN PHẨM — hạ tầng dưới mọi agent (vendor-neutral) + proof-engine kín:**
```
   Claude Code · Cursor · Copilot · Devin        ← agent nào cũng cắm (MCP, trung lập)
                    │  rtm.*
   ┌────────────────▼──────────────────────────────┐
   │  RTM-ENGINE (ĐỘC QUYỀN, SERVER-SIDE, §22 hào)  │
   │  ┌───────────────┐  ┌───────────────────────┐  │
   │  │ LỚP RĂNG       │  │ PROOF-ENGINE (kín)     │  │  ← lõi uncopyable
   │  │ requirement    │→ │ auto-formalize:        │  │
   │  │ business_rule  │  │  req(EARS)→property     │  │
   │  │ verdict·FK-BC  │  │  (temporal-logic/SMT)   │  │
   │  │ probe·suspect  │  │ auto-proof: code⊨prop?  │  │
   │  │ gaps           │  │  → PROOF | counter-ex   │  │
   │  └───────────────┘  │ CERTIFICATE (ký số)     │  │
   │       ▲ CORPUS độc quyền (flywheel) nuôi cả 2  │  │
   └───────┼───────────────────────┬────────────────┘
           │ adapter (thay được)   │ (tuỳ chọn TEE bọc proof-engine)
   ┌───────▼───────────────┐       │
   │ CodeGraph / GitNexus  │  code·coverage (MUA, commodity)
   └───────────────────────┘
```

**BỀ MẶT TOOL (ít, mỗi cái dùng nhiều):**
| tool | làm gì |
|---|---|
| `rtm.trace(req)` | requirement → rule ứng viên → nơi áp (§8) |
| `rtm.why(rule)` | rule → rationale + field + code + test + slice |
| `rtm.verify(claim, evidence)` | agent tuyên "xong R" → đòi commit/test-run **có thật** (răng nhẹ) |
| `rtm.probe(rule)` | chạy test khẳng định rule → xanh=đã ép (chân 4) |
| **`rtm.certify(req, code)`** | **PROOF-ENGINE: req→property→chứng minh→CHỨNG CHỈ ký số** (hoặc counter-example). Đây là cái BÁN (§00 model 1) |
| `rtm.gaps()` | req không rule · rule không proof · orphan · W-ID chưa tick |

**PROOF-ENGINE — vòng chứng minh (lõi giá trị, vá R10 trong ngách):**
```
requirement (EARS/AUTOSAR bán-hình-thức)
   │ auto-formalize  (LLM + template ngành, tuỳ CORPUS)
   ▼
property  (temporal logic / SMT constraint / contract)
   │ auto-proof  (model-checker · SMT solver · property-based · bounded-MC)
   ▼
PROOF ⊨   ──> CERTIFICATE ký số (đưa vào safety-case)   |   PROOF ⊭ ──> counter-example → trả agent sửa
```
- **Ranh giới trung thực:** chỉ chứng minh được **lớp requirement hình-thức-hoá-được** (safety-property · state-machine · biên số · MISRA/AUTOSAR-contract). Requirement thuần-NL không formal-hoá được ⟹ rơi về răng-nhẹ (`rtm.verify`/probe) + cổng human. **KHÔNG hứa chứng minh mọi thứ** (R13).
- **Vì sao đây là moat, không phải feature:** auto-formalize + auto-proof **chất lượng tuỳ CORPUS** (cặp req→property→proof đã-đúng trong ngách). Đối thủ có thuật toán mà **không có corpus** ⟹ formal-hoá sai/sót ⟹ proof vô dụng. §22.

**RĂNG VỚI AGENT:** agent tuyên "xong R" → engine bắt *"chứng minh"*; nếu formal-hoá được → **PROOF hoặc counter-example**, không phải "tin lời". 6 tool kia không có.

**VÌ SAO BÂY GIỜ:** cưỡi sóng agent (infra dưới TẤT CẢ agent, trung lập). Agent viết càng nhiều → càng cần proof → doanh thu per-proof càng lớn (§00).

**MUA vs XÂY (P10):** MUA code-graph (commodity, dễ lỗi-thời) qua adapter; XÂY lớp răng + **PROOF-ENGINE + CORPUS** (§22 — chỗ không ai làm hộ).

---

## §22. HÀO / DEFENSIBILITY — để đối thủ VÀ user không copy [HUMAN 2026-07-17]

**Sự thật cứng (đã verify tận tay):** MCP protocol + schema + node/edge = **copy dễ**. **Hào KHÔNG ở thuật-toán-bí-mật** (sẽ thất bại). Hào **không-copy-được** là tổ hợp 4 chân, trong đó công nghệ là MỘT chân:

| Chân hào | Copy được? | Là thuật-toán/công-nghệ? |
|---|---|---|
| **Engine AUTO-PROBE / formal-correctness** — từ requirement tiếng-người **tự tổng hợp test/property quyết định code có thoả**; ngách hình-thức-hoá-được thì **chứng minh formal** rule↔code | **Khó** — deep-tech AI+formal, giữ **server-side** | **ĐÚNG — chân thuật-toán thật. VÀ vá R10** |
| **Corpus + flywheel** req→probe→kết-quả-audit tích luỹ chéo khách | **Không scrape được** — mật, chậm | feeds engine trên |
| **Standard / độ-phủ tích hợp** — lớp truy-vết mọi agent cắm vào | Khó khi đã có đà | network effect, first-mover |
| **Tool-qualification (DO-330)** ngách quản chế | **Gần như không** — năm+tiền+pháp-lý | không, nhưng cộng hưởng |

> **Chân thuật-toán uncopyable = engine auto-probe/formal-correctness giữ SERVER-SIDE** (user không rút ruột được → "user không copy"). Vừa là hào bạn đòi, vừa vá R10.

**NGÁCH quyết định hào-thuật-toán CÓ thật hay chỉ switching-cost:**
- **Quản chế/an-toàn** (DO-178C hàng-không · ISO 26262 ô-tô · IEC 62304 y-tế): requirement **hình-thức-hoá-được** ⟹ **formal-correctness khả thi = hào thuật-toán thật**; DOORS/Polarion bị ghét+đắt; qualification = hào sâu. Thị trường hẹp, trả cao. ✅ **[DECIDED §00] CHỌN ĐÂY — vertical đầu = ISO 26262 automotive** (formal-hoá-được nhất + Tier-1/2 tiếp cận được).
- **Fintech/thanh-toán quy chế:** formal một phần; hào từ compliance+corpus hơn formal thuần. (không chọn đầu)
- **Dev-chung:** KHÔNG hình-thức-hoá-được ⟹ **KHÔNG hào formal**, chỉ switching-cost (yếu trước Cursor/Copilot). ❌ **LOẠI** — vi phạm mục tiêu uncopyable.

**Đối kháng (§14, không bán moat ảo):**
- Infra-dưới-agent dễ bị hãng agent tự ship traceability ⟹ **chống bằng ngách quản chế + qualification + corpus** (hãng ngang không thèm đụng thị trường hẹp).
- R10 vẫn cắn ở dev-chung ⟹ **"uncopyable-thuật-toán" ⟺ chọn ngách hình-thức-hoá-được**. Không né được.
- Phụ thuộc CodeGraph ⟹ adapter (R11); nó là nửa dễ lỗi-thời ⟹ mua đúng, miễn lớp răng không dính ruột.

### §22.1 BÍ MẬT KHÔNG-KHÁM-PHÁ-ĐƯỢC — thiết kế cụ thể [DECIDED]

> **Trung thực trước:** không thể giữ bí mật một THUẬT TOÁN thuần (toán tái lập được — ai đủ giỏi sẽ nghĩ lại ra). Hứa "giấu công thức" = hứa hão. **Cái giữ được** là làm cho **NĂNG LỰC** không tái lập được, kể cả khi thuật toán lộ. Bốn lớp, mạnh dần:

| Lớp | Cơ chế | Chống được gì | Điểm yếu |
|---|---|---|---|
| L1 **Server-side tuyệt đối** | engine chỉ chạy trên server ta; client gửi input, nhận output/certificate; **không ship binary** | đọc-code trực tiếp | black-box: đối thủ dò input→output |
| L2 **Năng-lực-phụ-thuộc-CORPUS** | auto-formalize + auto-proof **chất lượng = f(corpus)**; corpus = cặp req→property→proof→outcome đã-đúng, tích luỹ trong ngách | **lộ thuật toán KHÔNG lộ năng lực** — thiếu corpus thì formal-hoá sai/sót | corpus có thể rò nếu nội gián |
| L3 **Flywheel** | mỗi khách dùng → corpus dày + engine tune → đối thủ **luôn chậm N tháng dữ liệu**; càng dùng càng xa | đối thủ đuổi kịp | cần đủ khách để quay |
| L4 **TEE / confidential-computing (tuỳ chọn)** | proof-engine chạy trong enclave (SGX/SEV-SNP); attestation ký; **cả nhà-cloud cũng không soi RAM** | reverse từ hạ tầng · nội gián hạ tầng | phức tạp vận hành; bật khi khách đòi |
| L5 **Tool-qualification track-record** | certificate được cơ quan chứng nhận chấp nhận qua thời gian (DO-330/26262-8) | đối thủ mới **không có lịch sử** dù copy được tech | mất năm để dựng |

> **Kết:** "bí mật không khám phá được" = **L1+L2+L3 bắt buộc** (server-side + corpus-bound + flywheel), **L4 khi khách quản-chế đòi**, **L5 là hào thời-gian**. Đối thủ copy được **hình**, không copy được **năng lực** (thiếu corpus) lẫn **niềm tin** (thiếu qualification). Đây là "uncopyable" đúng nghĩa kỹ thuật, KHÔNG phải khẩu hiệu.

### §22.2 QUYẾT ĐỊNH [DECIDED — human trao toàn quyền], KHÔNG còn treo

| # | Quyết định | Chốt |
|---|---|---|
| 1 | **Ngách** | ✅ Quản-chế/an-toàn; **vertical đầu = ISO 26262 automotive**; engine tổng-quát họ IEC 61508 |
| 2 | **Bán gì** | ✅ **Bán KẾT QUẢ** (proof/certificate + continuous-certification + evidence-package), **KHÔNG bán seat** (§00). Khoá khách chặt, ít bị copy |
| 3 | **Người mua đầu** | ✅ **Design-partner Tier-1/Tier-2 automotive** (nhiều, tiếp cận được); đổi tiền lấy phản hồi + corpus mồi. Cụ-thể-hoá lúc go-to-market |
| 4 | **Bí mật** | ✅ L1+L2+L3 bắt buộc · L4 khi đòi · L5 hào-thời-gian (§22.1) |

**GIÁ TRUNG THỰC (không bán moat ảo):** đây là **deep-tech + pháp-lý NHIỀU NĂM**, KHÔNG phải tool 3 tuần. Formal-hoá tự động từ NL là **bài khó, có TRẦN** (R13) — nên khoanh vào lớp requirement hình-thức-hoá-được của ngách, KHÔNG hứa mọi thứ. Workflow-sicp = **dogfood** validate lớp răng + probe + corpus mồi (§17 V3-lite, đo verdict 20%→?); proof-engine + qualification là **chương trình dài** dựng TRÊN nền đó — cùng MỘT doc, khác nhịp thời gian.

---

## §23. TÓM TẮT — TRẠNG THÁI SẴN SÀNG

- **TRỤC CHÍNH (§00):** sản phẩm = **proof-of-correctness cho code AI sinh, ngách ISO 26262 automotive**; bán **proof/certificate + continuous-certification** (thị trường chưa có); hào **uncopyable** = server-side + corpus-bound + flywheel + (TEE) + qualification (§22.1). MỘT doc thống nhất.
- **Kiến trúc nền (dogfood):** ỔN ĐỊNH. 3🔴 v3.0 đã nuốt; fix chuyên gia (thin-node · DB-per-project · MUA code-graph · incremental · B7 fallback · R10) đã tích hợp.
- **Đã verify:** headless skill expand · hook inject · PreToolUse chặn-trước-permission · commands/skills coexist.
- **Chưa đo (ghi trung thực):** harness recall MEMORY.md-vs-thư-mục (§5.6) · MEMORY.md-stub chấp nhận? · `claude -p` exit-code (R12) · **tỉ lệ auto-formalize-được trên corpus mồi (R13)** · số/nhận-định thị trường (§1.6/§3).
- **Việc số 1 (dogfood):** V3-lite (§17) — viết lời 121 CHECK + DB-per-project + MUA code-graph + 2 tool + guard, **đo verdict 20%→? sau 20 task**. Đồng thời gieo **corpus mồi** (rule→probe→outcome của sicp) = phôi của proof-engine.
- **Chương trình dài (sản phẩm):** proof-engine (auto-formalize→auto-proof→certificate §21) + qualification — dựng TRÊN nền dogfood, khác nhịp thời gian.

**⟹ SẴN SÀNG triển khai V3-lite (dogfood). Ngách + kiếm-tiền + bí-mật đã [DECIDED] (§00/§22.2) — KHÔNG còn quyết định treo. Bước kế của SẢN PHẨM: đo tỉ lệ formal-hoá-được (R13) trên corpus mồi, tìm design-partner Tier-1/2.**
