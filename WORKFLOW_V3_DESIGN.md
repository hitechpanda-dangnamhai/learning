# WORKFLOW V3 — THIẾT KẾ ĐẦY ĐỦ

> **Chủ thể:** một workflow MỚI để nâng cấp workflow của sicp (planner · coder · `CLAUDE.md` · memory · relay · wf-lock · gen-facts).
> **Trạng thái:** THIẾT KẾ — chưa cắt slice. Mọi số đo kèm lệnh tái lập. Cái nào chưa đo, ghi "chưa đo".
> Sinh 2026-07-16 sau ~7 vòng discuss với human. Các quyết định human chốt được đánh dấu **[HUMAN]**.

---

## §0. VÒNG 0 — CHỦ THỂ + BẤT BIẾN (`CLAUDE.md §14b`)

**CHỦ THỂ:** cỗ máy điều phối các phiên Claude để thay đổi repo sicp — **và để hiểu phần mềm này phải làm đúng cái gì**.

**BẤT BIẾN — không giải pháp nào được vi phạm:**

| # | Bất biến | Nguồn |
|---|---|---|
| B1 | Context Claude **hữu hạn · không chia sẻ · sẽ chết** | sự thật vật lý |
| B2 | **Planner MÙ** — chỉ biết state khi nó chạy lệnh | `scripts/relay` |
| B3 | **Chỉ human mở được terminal coder** | harness |
| B4 | **N coder + 1 planner dùng CHUNG 1 cây git** — cố ý; branch-per-slot **đã BÁC, cấm bàn lại** | `wf-lock:33-46` |
| B5 | **sicp KHÔNG sở hữu toolchain của nó** — `tsc`·`eslint`·`vitest`·`next`·`black`·`pytest`·`git`·harness **đều nói tiếng FILE** | đo |
| B6 | **Sự tuân thủ mua bằng KẺ TIÊU THỤ VỠ**, không mua bằng luật, không mua bằng cổng | đo — §1 |
| B7 | **V3 dựng SONG SONG, KHÔNG phá V1** — `/master-*` mới cạnh `/icp-multi-*` cũ; cũ luôn chạy được làm lưới an toàn; hư V3 thì gõ lại tên cũ = về nguyên trạng | **[HUMAN 2026-07-16]** — cùng luật §3: ai cùng tồn tại thì sống, ai đòi thay thế thì chết (Darklang) |

> **B5 giết vế "file code là sản phẩm cuối, không cần commit".** Kẻ đòi file ở đây **không phải con người** — nó là máy. Nên lập luận *"Claude không cần file như người"* không gỡ được ràng buộc này.
> **B6 là nền của toàn bộ thiết kế.**

---

## §1. VẤN ĐỀ — ĐO ĐƯỢC, KHÔNG PHẢI CẢM TÍNH

### 1.1 Thang tuân thủ `100 · 95 · 31 · 20 · 3 · 2,4`

| Hiện vật | Luật viết? | Cổng máy? | **Kẻ tiêu thụ VỠ nếu sai?** | Tuân thủ |
|---|---|---|---|---|
| `dispatch` 5 lớp | có | **KHÔNG** | **CÓ** — coder tắc ngay | **20/20 = 100%** |
| `commit` subject | có | có (báo động) | không | 1259/1330 = **95%** |
| `report` 6 mục | có | KHÔNG | **KHÔNG** | 151/482 = **31%** |
| `verdict` | — | **CÓ — CỨNG, `exit 2`** | không | 126/126 **có mặt** · 25 **có nội dung** = **20%** |
| slice khai type | có | không | không | **1/31** |
| ADR đối chiếu code | có | không | không | **3/126 = 2,4%** |

**Đọc bảng:** `§6` và `§7` của `CLAUDE.md` **sinh từ ĐÚNG MỘT commit `ea3dc4a3`** — cùng file, cùng lúc, cùng tác giả → **95% vs 31%**. ⟹ **không phải "luật viết chưa rõ"**.
`verdict` có **cổng cứng nhất hệ** (thiếu = `exit 2`) → mua được **100% sự CÓ MẶT**, **20% sự THẬT**. **101 lần verdict là đúng một chữ trần `accepted`.**
`dispatch` **không có validator nào** mà **100%**.

> **⟹ B6: cổng chỉ mua được cái nó ĐO được. Nó đo hình thức ⟹ nhận về hình thức.**
> **Chỉ kẻ VỠ VÌ SAI mới mua được sự THẬT.**

### 1.2 Planner MÙ business rule — lỗ chí mạng

| File | Nhắc `business rule` |
|---|---|
| `.claude/commands/icp-multi-planners.md` (25 KB) | **0** |
| `.claude/commands/icp-multi-coders.md` (10,6 KB) | **0** |
| `docs/ICP_WEB_PLAN.md §8` — **kỹ năng PHÂN TÍCH SLICE** | **0** |
| `CLAUDE.md` (209 dòng) | **1** — và là `§11 Comments`: *"Inline chỉ khi business rule KHÔNG obvious từ code"* |

`§2 Nghi thức MỞ TASK`: `git log -20` · đọc diff · đọc Acceptance+Stop. **Không dòng nào bắt hiểu luật nghiệp vụ.**
`VERIFY-DEPTH high-risk`: data-path · A⊥B/RLS · security · money · migration · idempotency — **toàn CẤU TRÚC + AN TOÀN, không có "có đúng luật nghiệp vụ không"**.

> **⟹ Workflow phân tích CẤU TRÚC, KHÔNG BAO GIỜ phân tích HÀNH VI.**
> Planner mù ở **cả hai đầu**: mù lúc **cắt** task, mù lúc **verify** task.
> ⟹ `verdict` 20% **không phải vì planner lười — planner KHÔNG CÓ GÌ ĐỂ ĐỐI CHIẾU.**

### 1.3 Các kho tri thức và bệnh của chúng

| Kho | Số | Bệnh |
|---|---|---|
| `docs/decisions/ADR-*.md` | 126 | có **lý do**, không có **răng** — 2,4% từng đối chiếu code; 17/126 có trường `Decision` |
| `CHECK` trong DB | **121** | có **răng** (bất khả vi phạm), **không có lý do** — `avg_price>=min_price` truy ra 2 chỗ: chính nó + `docs/archive-v1/` (**kho đã chết**) |
| comment `// ADR-0XX` | **2.891 link / 1.254 file = 40% code** | **đồ thị ĐÃ TỒN TẠI**, đang là văn xuôi |
| memory | 70 | **hoàn hảo bên trong** (70/70 field, 0/163 link gãy) — vì `description` bị `MEMORY.md` **ĂN**. **Từ ngoài vào thì bị gọi cụt** |
| `seam` | — | **resolve = XOÁ** ⟹ đã suýt xoá vĩnh viễn **≥7 phán quyết human** |
| mũi tên trỏ hư vô | **~24** | `ADR-026`+`ADR-030` (nằm trong chính `INDEX.md`) · `ADR-131` · **`V004`+`V007`** (chưa từng tồn tại, mà `user.repo.ts:142` **lấy V007 làm lý do cho code đang chạy**) · 9 script · ~10 memory cụt |

**`gen-facts.sh:26` tính `max`, KHÔNG đếm** ⟹ `FACTS.md` báo `highest: V079` và **mù hoàn toàn với lỗ V004/V007**.

### 1.4 Hai điểm mù ẢNH GƯƠNG

```
ADR            : có LÝ DO,  không RĂNG   (2,4% đối chiếu code)
business rule  : có RĂNG,   không LÝ DO  (121/121 bất khả vi phạm, 0 rationale)
```

### 1.5 Nguyên tắc rút ra: **đừng đẻ CỘT — đẻ KẺ ĂN**

memory hoàn hảo **mà không có CHECK/NOT NULL/DB nào** — vì thiếu `description` thì memory **vô hình** ⟹ hỏng **thấy ngay**. Trường của ADR **không ai ăn** ⟹ 17/126.

---

## §2. NGUYÊN TẮC THIẾT KẾ

| # | Nguyên tắc | Vì sao |
|---|---|---|
| P1 | **Đẻ KẺ ĂN, không đẻ CỘT** | §1.5 |
| P2 | **`FOREIGN KEY` là kẻ-vỡ hoàn hảo** — không mệt, không quên, không bấm-cho-xong, **không có chế độ im lặng** | ~24 mũi tên hư vô |
| P3 | **ĐO thay vì KHAI** — cạnh nào đo được thì cấm khai | khai = mục; đo = tươi mỗi lần chạy |
| P4 | **Semantic cho ỨNG VIÊN — probe QUYẾT** | embedding gần ≠ logic giống |
| P5 | **DRY là điều kiện dừng** (`§14`) | dừng khi *nghe hợp lý* = nguồn của mọi lần trượt |
| P6 | **Live-read > generate-rồi-đọc** | file sinh sẵn mà stale ⟹ Claude theo luật chết ⟹ **không ai vỡ** ⟹ im lặng |
| P7 | **Split-brain CẤM · Composition OK** | cùng 1 fact ở 2 store = vỡ; fact khác nhau ở 2 store = ổn |
| P8 | **Không rewrite thứ đang thắng** | `dispatch` 100% — đụng vào là ngu |
| P9 | **DB chứa TEXT có cấu trúc, KHÔNG chứa mô hình trừu tượng** | mô hình ⟹ generator có **trần** ⟹ đó là thứ giết MDA 20 năm |

---

## §3. TIỀN LỆ THẾ GIỚI — ĐÃ KIỂM

| Ai | Làm gì | Kết cục |
|---|---|---|
| Intentional Programming (Simonyi, 1995) | graph "intentions" → sinh code | Microsoft từ chối productize 2001; editor GUI riêng *"khó deploy so với tooling text-based"* |
| **JetBrains MPS** | AST là nguồn, projectional editor | **Sống** — chỉ trong **DSL hẹp** (thuế Hà Lan, embedded, biomedical) |
| **Unison** | code = AST content-addressed trong DB | **Sống** — nhưng **phải phát minh ngôn ngữ mới**; vẫn edit bằng **text**, "slurp" vào DB |
| **Darklang** | code trong DB + structured editor | **ĐÃ GIẾT** — *"structured editor không còn hợp lý khi LLM sinh code, và nó tách rời khỏi cách người ta code bằng agent trong Cursor/Copilot"*. Bỏ xong: *"làm được trong 2 tháng nhiều hơn cả 2 năm trước"* |
| **Glean · Kythe · CodeQL · Sourcegraph** | file là nguồn, DB là **index dẫn xuất** | **Sống, production, quy mô Meta/GitHub** |
| **CodeGraph · Codebase-Memory** (2026, cho AI agent) | tree-sitter → graph → MCP | **Sống** — CodeGraph 47,4k sao / 5 tháng |
| **DOORS · Polarion · Jama** | **RTM** requirement→code→test | **30 năm**, bắt buộc trong DO-178C · ISO 26262 · IEC 62304 |

**Quy luật:** ai **cùng tồn tại với file** thì sống; ai **đòi thay file** thì chết. MPS/Unison sống vì **sở hữu trọn toolchain của mình** — B5 nói mình **không**.

**Số đo quyết định (Codebase-Memory, 31 repo thật):** graph **83%** chất lượng · ~1.000 token · 2,3 tool-call · <1ms — vs file-exploration **92%** · ~10.000 token · 4,8 call · 10–30s.
**Graph rẻ hơn 10× nhưng NGU hơn 9 điểm.** Lý do họ nói rõ: *"graph lưu quan hệ nhưng **không lưu source line**"*. Kết luận của tác giả: **dùng cả hai**.

**⟹ Thiết kế này là RTM (DOORS) + code-graph (Glean) + hạt nhân DB, cho Claude đọc — chưa ai làm tử tế.**

---

## §4. KIẾN TRÚC TỔNG THỂ

```
   HUMAN ──"tôi muốn gì"──> requirement          HUMAN ──mở terminal──> CODER k
     │                                                                    │
     └──duyệt A1 DESIGN──> PLANNER (brain) <────LISTEN/NOTIFY────────────┘
                              │                                           │
                    wf.trace/why/exists                            wf.pickup(k)
                              │                                           │
   ┌──────────────────────────▼───────────────────────────────────────────▼────┐
   │  POSTGRES — TRÍ NHỚ (có FK, có răng)                                       │
   │  law·skill·requirement·business_rule·adr·memory·seam                       │
   │  slice·task·wid·service·file·function·test·table·column·migration·commit   │
   │  + pgvector (vi/en) + pg_trgm                                              │
   └───────┬──────────────────────────────────────────────┬────────────────────┘
   generate│                                        ingest │ (ĐO, không khai)
           ▼                                               │
   doc/req/*.md ──> OFT ──> trace.xml ───────────────────>─┤ [declared]
   docs/*.md (human + git diff)                            │
   memory/*.md + MEMORY.md (harness đọc)                   │
                                                           │
   pytest --cov / vitest --coverage ──> coverage.json ────>┤ [measured]
   pg_constraint / information_schema ────────────────────>┤ [measured]
   git log ───────────────────────────────────────────────>┘ [measured]

   REDIS (GIỮ NGUYÊN — không có bệnh đo được):
     slot.state · dispatch · pickup · report · consume · macro · wf-lock

   GIT: 1 cây chung.  apps/ packages/ = file LÀ nguồn (KHÔNG generate code)
```

**Bất đối xứng có chủ ý:**
- **`.md`** → **DB là nhà, file SINH RA** (round-trip tầm thường)
- **CODE** → **file là nhà, DB giữ ĐỒ THỊ + HASH** (generate code = trần MDA = 4 cái xác)

---

## §5. DATABASE — ĐẦY ĐỦ

### 5.1 `node` — một bảng duy nhất

> **Vì sao MỘT bảng:** cạnh phải trỏ **mọi loại** thực thể. Tách `rules`/`symbols` ⟹ `edge.src` **đa hình** ⟹ **không `FK` nổi** ⟹ `INSERT` rác lọt ⟹ **tái tạo đúng bệnh ~24 mũi tên hư vô, bằng SQL**. Một bảng ⟹ `REFERENCES node(id)` chạy cho **toàn hệ**. Đây là lý do kỹ thuật, không phải thẩm mỹ.

```sql
CREATE EXTENSION IF NOT EXISTS vector;
CREATE EXTENSION IF NOT EXISTS pg_trgm;

CREATE TABLE node (
  id            bigserial PRIMARY KEY,
  kind          text NOT NULL,
  name          text NOT NULL,          -- ổn định; planner đặt cho req/rule
  rev           int,                    -- requirement/business_rule: ~1 ~2
  state         text,                   -- enum theo kind (§5.4)

  statement_vi  text,                   -- [HUMAN] song ngữ bắt buộc
  statement_en  text,
  rationale     text,                   -- VÌ SAO — thứ ADR có mà rule không có
  body          text,                   -- .md: nội dung THẬT (đây là nhà). code: NULL

  fingerprint   text,                   -- hash(nội dung chuẩn hoá) → suspect
  origin        text NOT NULL CHECK (origin IN ('declared','measured','harvested')),

  embedding_vi  vector(1024),
  embedding_en  vector(1024),
  embedded_fp   text,                   -- ≠ fingerprint ⟹ embedding stale ⟹ re-embed

  created_at    timestamptz NOT NULL DEFAULT now(),
  UNIQUE (kind, name)
);
```

**`kind` — đầy đủ, không sót:**

| Nhóm | kind | Nguồn |
|---|---|---|
| Luật | `law` `skill` `dod_item` `stop_condition` | `CLAUDE.md` `.claude/commands/*` |
| **Ý định** | **`requirement`** | **HUMAN viết** |
| **Luật nghiệp vụ** | **`business_rule`** | **PLANNER dẫn xuất** + harvest 121 `CHECK` |
| Kiến trúc | `adr` | 126 file |
| Tri thức | `memory` `seam` | 70 file · relay |
| Việc | `slice` `task` `episode` `wid` | 31+87 file · relay |
| Code | `service` `file` `function` `test` | tree-sitter/SCIP + coverage |
| **DB** | **`table` `column`** `migration` | **`information_schema` + `pg_constraint`** |
| Bề mặt | `route` `log_event` `contract` | gen-facts / openapi |
| Lịch sử | `commit` `test_run` | `git log` · output test |

### 5.2 `edge` — mọi quan hệ

```sql
CREATE TABLE edge (
  src    bigint NOT NULL REFERENCES node(id) ON DELETE CASCADE,
  dst    bigint NOT NULL REFERENCES node(id) ON DELETE CASCADE,
  kind   text NOT NULL,
  origin text NOT NULL CHECK (origin IN ('declared','measured')),
  PRIMARY KEY (src, dst, kind)
);
```

| edge.kind | src → dst | origin | Lấy từ đâu |
|---|---|---|---|
| **`refines`** | **requirement → business_rule** | declared | planner, human duyệt A1 |
| `impl` | business_rule → function | declared | **tag OFT** |
| `utest` `itest` | business_rule → test | declared | **tag OFT** |
| **`constrains`** | **business_rule → column** | **measured** | **`pg_constraint.conrelid`+`conkey`** |
| **`covers`** | **test → function** | **measured** | **coverage `fnMap`+`f`** |
| `cites` | function→adr · memory→memory | declared | tag / `[[link]]` |
| `supersedes` | adr → adr | declared | ADR |
| `contains` | service→file→function · slice→task | measured | scan |
| `serves` | slice → requirement | declared | planner |
| `blocks` | seam → slice | declared | planner |
| `carries` `ticks` | task→wid · slice→wid | declared | planner |
| `closed_by` | task → commit | declared | report (**FK ép commit có thật**) |
| `touches` | commit → file | **measured** | **`git log`** |
| `next` | migration → migration | measured | scan — **giết lỗ V004/V007** |
| `governs` | law → dod_item | declared | luật |
| `learned_from` | memory → task | declared | planner |

### 5.3 RĂNG — `CHECK` + trigger + index

```sql
-- [HUMAN] song ngữ bắt buộc cho ý định & luật nghiệp vụ
ALTER TABLE node ADD CONSTRAINT bilingual CHECK (
  kind NOT IN ('requirement','business_rule')
  OR (statement_vi IS NOT NULL AND statement_en IS NOT NULL));

-- rule MỚI phải có lý do. Chỉ rule HARVEST từ 121 CHECK cũ mới được NULL
-- (lý do thật sự đã mất). Planner vừa quyết ⟹ lý do ĐANG BIẾT ngay lúc đó.
ALTER TABLE node ADD CONSTRAINT rule_has_reason CHECK (
  kind <> 'business_rule' OR origin = 'harvested' OR rationale IS NOT NULL);

-- .md: DB là nhà ⟹ phải có body. code: file là nhà ⟹ CẤM có body.
ALTER TABLE node ADD CONSTRAINT body_home CHECK (
  (kind IN ('law','skill','adr','memory','slice','requirement','business_rule')
   AND body IS NOT NULL)
  OR (kind IN ('function','test','column','table','file','commit') AND body IS NULL)
  OR kind NOT IN ('law','skill','adr','memory','slice','requirement',
                  'business_rule','function','test','column','table','file','commit'));

-- rev chỉ cho thứ có revision
ALTER TABLE node ADD CONSTRAINT rev_scope CHECK (
  rev IS NULL OR kind IN ('requirement','business_rule'));

CREATE INDEX ON node USING hnsw (embedding_vi vector_cosine_ops);
CREATE INDEX ON node USING hnsw (embedding_en vector_cosine_ops);
CREATE INDEX ON node USING gin  (name gin_trgm_ops);
CREATE INDEX ON node USING gin  (statement_vi gin_trgm_ops);
CREATE INDEX ON node USING gin  (statement_en gin_trgm_ops);
CREATE INDEX ON edge (dst, kind);
```

**Trigger — slice không đóng được khi còn W-ID mồ côi:**
```sql
CREATE FUNCTION check_wid_orphan() RETURNS trigger AS $$
BEGIN
  IF NEW.kind='slice' AND NEW.state='closed' AND EXISTS (
    SELECT 1 FROM edge c JOIN node t ON t.id=c.dst
    WHERE c.src=NEW.id AND c.kind='contains' AND t.kind='task'
      AND EXISTS (SELECT 1 FROM edge w WHERE w.src=t.id AND w.kind='carries'
                  AND NOT EXISTS (SELECT 1 FROM edge k
                                  WHERE k.src=NEW.id AND k.kind='ticks' AND k.dst=w.dst))
  ) THEN RAISE EXCEPTION 'slice % còn W-ID chưa tick', NEW.name; END IF;
  RETURN NEW;
END $$ LANGUAGE plpgsql;
```

**Kẻ-ăn KHÔNG đặt được vào DB → đặt vào TEST SUITE** (vì `pnpm test` **đang chạy**, còn CI thì **`disabled_manually`**):
```
test_every_business_rule_has_verifying_test()   -- rule không test = ĐỎ
test_every_requirement_refines_to_rule()        -- requirement mồ côi = ĐỎ
test_no_dangling_generated_file()               -- generate(DB) ≠ file = ĐỎ
test_embedding_not_stale()                      -- embedded_fp ≠ fingerprint = ĐỎ
```

### 5.4 `state` theo `kind`

| kind | state |
|---|---|
| `slice` | `active → closing → closed` |
| `task` | `pending → wip → done\|blocked` |
| `seam` | `open → resolved` — **KHÔNG xoá nữa** (chấm dứt việc xoá vĩnh viễn tri thức) |
| `requirement` `business_rule` | `draft → approved → superseded` |
| `adr` | `Draft · Locked · Superseded · Partially Superseded` (**enum THẬT, đã đo — không bịa**) |
| `wid` | `open → ticked` |

---

## §6. BA TẦNG — `requirement → business_rule → code` **[HUMAN]**

```
requirement   "khách mua 10 sản phẩm cùng SKU thì được tặng 3"      ← HUMAN viết (tiếng Việt)
    │ refines
    ├── business_rule  gift_qty = floor(qty/10)*3                    ← PLANNER dẫn xuất
    ├── business_rule  gift SKU phải trùng SKU nguồn                    tự đặt tên [HUMAN]
    └── business_rule  tặng hàng phải trừ tồn kho                       human duyệt A1
             │ impl          │ constrains         │ utest
             ▼               ▼                    ▼
        applyGiftLines()  order_line.gift_qty   test_gift_decrements_stock
                                                     │ covers [ĐO]
                                                     └──> decrementStock()
```

**Ba tầng này = ranh giới VAI, biến thành DỮ LIỆU:**
- **HUMAN** nói *cần gì, muốn gì* → `requirement`
- **PLANNER** dẫn xuất → `business_rule`, **tự đặt tên** (human **không** làm thư ký)
- **CODER** → `code` + tag

OFT hỗ trợ sẵn chuỗi nhiều tầng: `req` → `rule` → `impl` → `utest`.

**Định danh:** `rule~offer-avg-price-not-below-floor~1` — `~1` = **revision**. Sửa nội dung → `~2` → **mọi tag còn trỏ `~1` thành lỗi build**.
**Nhưng bump phụ thuộc NGƯỜI NHỚ ⟹ hạng 31%.** Nên thêm **`fingerprint` kiểu Doorstop**: `hash(statement)` đổi mà `rev` không đổi ⟹ **link tự động `suspect`**. **`rev` = ý định người · `fingerprint` = máy bắt quên.**

---

## §7. CƠ CHẾ TÌM RULE — 4 CHÂN, MẠNH DẦN

**Bài toán:** *"tôi nghĩ ra rule C — đã có chưa?"* Tra tên **vô dụng** (chưa có ID). So text **vô dụng** (khác cách diễn đạt).

| # | Chân | Cách | Độ tin |
|---|---|---|---|
| 1 | **Cấu trúc** | rule C đụng field nào → `SELECT` mọi rule `constrains` field đó | **cao** — chính xác, không ML. Tìm theo **CHỦ THỂ**, không theo **chữ** |
| 2 | **Chữ** | `pg_trgm` trên `name` + `statement_vi/en` | trung bình |
| 3 | **Nghĩa** | `pgvector` — **quét MỌI `kind` trong 1 query** | thấp — **ứng viên, không kết luận** |
| 4 | **HÀNH VI** | **viết test khẳng định rule C, chạy** | **QUYẾT ĐỊNH** |

**Chân 3 — chỗ một bảng `node` trả lãi:** rule C có thể **đang nấp** dưới dạng `business_rule` chưa tên · `adr` · `memory` · **`function` có hành vi đó mà không ai viết ra** · **`test` khẳng định nó mà không có rule** · `law`.
```sql
SELECT kind, name, statement_vi FROM node
ORDER BY LEAST(embedding_vi <=> $q_vi, embedding_en <=> $q_en) LIMIT 10;
```
Một query quét **toàn hệ**. Tách bảng ⟹ phải bắn nhiều query + **không bao giờ tìm ra rule nấp trong `memory`/`adr`**.

**⚠ Vì sao chân 3 KHÔNG được là kết luận (P4):**
```
"giá trung bình không được THẤP hơn giá sàn"   avg_price >= min_price
"giá trung bình không được CAO hơn giá trần"   avg_price <= max_price
```
Hai câu **cực gần** về vector, **NGƯỢC** về logic. Negation là chỗ embedding **dở nhất**. Và người đọc kết quả là Claude — *"rất muốn làm bạn hài lòng"*.

**Chân 4 — cái duy nhất chứng minh được:**
```sql
BEGIN; INSERT INTO catalog_offer(avg_price,min_price) VALUES (5,10); ROLLBACK;
-- bị từ chối ⟹ rule ĐÃ được ép   |   được nhận ⟹ CHƯA được ép
```
Dạng tổng quát: **viết test khẳng định rule C → chạy trên code hiện tại. XANH ⟹ rule ĐÃ TỒN TẠI (dù chưa ai đặt tên). ĐỎ ⟹ thật sự mới.**
Đúng `§14`: *"CHỨNG MINH bằng exploit/repro THẬT. Destructive → transaction + `ROLLBACK`"*.

**Phần thưởng phụ — HARVEST:** test XANH mà chưa có rule ⟹ vừa tìm thấy **luật ngầm không tên**. Đặt ID → tag test → **coverage TỰ chỉ ra hàm nào implement**. ⟹ đường ra cho 378k dòng đầy luật ngầm.

**⚠ Chốt chặn:** **121/121 rule hôm nay VÔ HÌNH với semantic search** — chúng chỉ tồn tại dạng `CHECK (avg_price >= min_price)`, **không có chữ nào**. **Không semantic-search được thứ chưa từng viết ra bằng lời.** ⟹ **Việc đầu tiên KHÔNG phải dựng schema — là VIẾT LỜI cho 121 cái CHECK đó.**

---

## §8. CƠ CHẾ TRACE — VÒNG LẶP, DỪNG BẰNG DRY **[HUMAN]**

Semantic một mình trả *"10 rule nghe giống nhau"* — vô dụng. **Cái TRACE mới khử mơ hồ.**

```
wf.trace("mua 10 tặng 3")
 vòng 1  semantic(vi+en) + trgm + cấu trúc        → 6 ứng viên mơ hồ
 vòng 2  mỗi ứng viên → NÓ ĐANG ÁP Ở ĐÂU?
         rule#3 → order_line.gift_qty · 2 hàm · 4 test · slice S-PROMO-01   ← có thật
         rule#5 → KHÔNG áp ở đâu                                            ← mồ côi, loại
 vòng 3  trace lộ neighbor KHÔNG ai hỏi
         cùng cột gift_qty còn rule#9  ·  applyGiftLines() gọi decrementStock() → rule#11
 vòng 4  → ứng viên mới → quay lại vòng 2
 DỪNG    probe quyết được  HOẶC  2 vòng LIÊN TIẾP không ra node mới
```

> **Điều kiện dừng = `CLAUDE.md §14` DRY.** Không trùng hợp: cả hai chống **đúng một thứ** — *dừng khi vừa nghe được một câu chuyện hợp lý*. **Luật human viết cho Claude trở thành điều kiện dừng của máy.**

`wf.trace` trả về **trạng thái vòng lặp**, không phải danh sách: **mới ra gì · xác nhận gì · ĐÃ BÁC gì** (§14: *"ghi cả giả thuyết BỊ BÁC"*).

**Truy vấn nền — `rule → code + field + test`:**
```sql
SELECT r.statement_vi, r.rationale,
  array_agg(DISTINCT c.name) FILTER (WHERE e.kind='constrains')         AS fields,
  array_agg(DISTINCT f.name) FILTER (WHERE e.kind='impl')               AS code,
  array_agg(DISTINCT t.name) FILTER (WHERE e.kind IN ('utest','itest')) AS tests
FROM node r
LEFT JOIN edge e ON e.src=r.id
LEFT JOIN node c ON c.id=e.dst AND c.kind='column'
LEFT JOIN node f ON f.id=e.dst AND f.kind='function'
LEFT JOIN node t ON t.id=e.dst AND t.kind='test'
WHERE r.kind='business_rule' AND r.name=$1
GROUP BY r.id;
```

**`rule → slice/task` — SUY RA, không ai khai:**
```
rule ──impl──> function <──contains── file <──touches── commit <──closed_by── task <──contains── slice
```
`impl` = tag OFT · `touches` = **`git log` đo** · `closed_by` = report (FK). 5 hop, một `JOIN`.

**Cross-check — cái duy nhất bắt được Claude gắn tag sai:**
```
tag nói X impl rule R,  nhưng test của R KHÔNG BAO GIỜ chạm X   ⟹ một trong hai SAI
test của R chạm Y,      nhưng Y không có tag                     ⟹ ripple chưa ai ghi nhận
```
Hai chân **ĐO** (`constrains`, `covers`) kẹp hai chân **KHAI** (`impl`, `utest`).

---

## §9. RANH GIỚI REDIS ↔ POSTGRES **[HUMAN: giữ Redis]**

**Số thật:** Redis ~0,1–0,2 ms · Postgres local ~1–5 ms + connect 5–20 ms. relay chạy **vài lần/task**; task kéo **phút→giờ**; một lượt Claude **vài giây**. ⟹ **20 ms là vô hình. Tốc độ KHÔNG phải lý do cho cả hai phía.**

**Lý do thật (P8):** **relay KHÔNG có bệnh đo được.** `dispatch` **20/20**. Bệnh nằm ở `report`(31%)/`verdict`(20%)/`seam`(xoá) — và **không cái nào chữa bằng đổi store**; chúng chữa bằng **FK tới bằng chứng**.

| | Redis — **NHỊP** | Postgres — **TRÍ NHỚ** |
|---|---|---|
| giữ | `slot.state` · `dispatch` · `pickup` · `report`(live) · `consume` · `macro` · `pending-human` · **`wf-lock`** | law · requirement · business_rule · adr · memory · seam · slice · task · wid · code-graph · table/column · commit |
| tính chất | chết sau 1 task | phải sống lâu hơn task ⟹ **phải có FK** |

**Giao đúng MỘT chỗ — `consume`:** **ghi PG trước** (verdict + bằng chứng + FK) → **rồi mới lật Redis**. PG là điểm commit; lật Redis retry được, idempotent.

> **P7:** `relay report` set `state`+ledger là **MỘT** thao tác — **tách ra 2 store là VỠ**. `wf status` đọc slot(Redis) + seam(PG) là **fact khác nhau** — **OK**.

**`dispatch` 100% KHÔNG phải nhờ Redis** — nhờ **coder tắc ngay nếu nó rỗng**. Kẻ-vỡ là **coder**, không phải store.

**`wf-lock` giữ nguyên Redis.** Bug đã biết: **TTL 600s hết hạn giữa section 20 phút** → sửa bằng renew/tăng TTL, **không phải bằng đổi DB**.

---

## §10. VAI TRÒ — TỪNG BƯỚC

### 10.1 HUMAN
1. **Nói cần gì, muốn gì** → `requirement` (tiếng Việt)
2. **Duyệt A1 DESIGN** — cổng **đã tồn tại** (`ICP_WEB_PLAN:26`). Duyệt **Ý NGHĨA** của business_rule planner dẫn xuất
3. **Mở terminal coder** (B3)

**KHÔNG làm:** đặt req ID · bump rev · gõ tag · làm thư ký. **[HUMAN 2026-07-16]**

### 10.2 PLANNER — `/icp-multi-planners`
```
1  SessionStart hook → icp-wf law --role=planner → LUẬT inject thẳng vào context
2  wf.status                      → macro · slot · slice · seam
3  nhận requirement từ human
4  wf.trace(requirement)          → VÒNG LẶP §8 → đã có rule chưa?
5  chưa có → dẫn xuất business_rule: đặt tên · statement_vi + statement_en · rationale
6  PROBE: viết test cho rule → chạy → ĐỎ = mới (XANH = đã có ⟹ CẤM đặt ID mới)
7  A1 DESIGN → human duyệt Ý NGHĨA
8  wf.insert(rules)               → FK + CHECK ép (song ngữ, có lý do)
9  cắt slice + task, edge(serves) → requirement.  Slice KHÔNG trỏ requirement ⟹ CẤM cắt
10 wf.dispatch(k, task)           → Redis (giữ nguyên form 5 lớp)
11 ▶ RUNNABLE + chuông bell.oga ×2
12 coder xong → LISTEN/NOTIFY ĐÁNH THỨC planner   ← human hết làm event-bus
13 VERIFY: wf.why(rule) → đối chiếu HÀNH VI, không chỉ cấu trúc
14 wf.consume(k, verdict, evidence_id) → PG trước (FK bằng chứng) → Redis sau
15 git push
```
**Giữ nguyên:** không viết source · đầu não = phân tích + phân phối + deep-verify · anti-over-hold · anti-idle slot.

### 10.3 CODER — `/icp-multi-coders k`
```
1  SessionStart hook → icp-wf law --role=coder --slot=k → LUẬT inject
2  wf.pickup(k) → task + requirement CHA + business_rule + field DB + ADR + memory
                  ⟹ KHÔNG grep để hiểu việc
3  Nghi thức MỞ TASK §2 — BƯỚC 0 MỚI: hiểu rule. Không hiểu ⟹ CẤM viết code
4  code + tag  [impl->rule~<id>~<rev>]
5  test + tag  [utest->rule~<id>~<rev>]
6  wf-lock (Redis, giữ nguyên) → commit explicit-path
7  pre-commit: oft trace || exit 1
8  wf.report(k, commit_sha=...)  → FK ép commit CÓ THẬT
9  chuông complete.oga
```
**Giữ nguyên:** không push · không `clean`/`stash`/`reset --hard`.

---

## §11. `CLAUDE.md` MỚI + HOOKS

**`CLAUDE.md` teo còn ~6 dòng** (P6 — file sinh sẵn mà stale ⟹ Claude theo luật chết ⟹ **không ai vỡ** ⟹ im lặng; live-read **không có chế độ stale**):

```markdown
# ICP — Luật
Luật THẬT nằm trong workflow DB, được inject vào context lúc mở phiên.
Nếu bạn KHÔNG thấy luật trong context → DB workflow chết → DỪNG, báo human. KHÔNG làm gì.
CẤM sửa file này. Sửa luật = sửa trong DB (`icp-wf law edit`).
```

**Hooks — cơ chế, không phải luật:**

| Hook | Chạy gì | Mua được gì |
|---|---|---|
| **`SessionStart`** | `icp-wf law --role=$ROLE` → `additionalContext` (stdout cũng tự thành context) | luật **luôn tươi**, không file, không drift. **Fail-closed**: DB chết ⟹ in `STOP` |
| **`UserPromptSubmit`** | `icp-wf state --slot=k` | state tươi **mỗi lượt** (chống B2) |
| **`PreToolUse`** trên `Edit`/`Write` | path thuộc **file SINH RA** (`docs/**`, `memory/**`, `doc/req/**`) → `permissionDecision: "deny"`, reason *"file này generate từ DB — sửa bằng `icp-wf`"* | **quy ước → RÀNG BUỘC.** Không phải luật (31%) — Claude **không gọi nổi** |

**⚠ `PreToolUse` KHÔNG chặn `apps/`/`packages/`** — code là file-nguồn, coder phải `Edit` bình thường.
**⚠ `PostToolUse` KHÔNG chặn được** (tool đã chạy) ⟹ **không dùng nó làm cơ chế ép**.

---

## §12. MCP — CÔNG CỤ CLAUDE GỌI

```
wf.law(role, slot)          -> luật đúng vai (SessionStart dùng)
wf.context(task)            -> task + requirement + rule + field + ADR + memory   ← thay GREP
wf.trace(mô tả)             -> VÒNG LẶP §8: ứng viên → nơi áp → neighbor → DRY
wf.why(node)                -> requirement cha + rule + rationale + field + code + test + slice
wf.impact(node)             -> mọi thứ VỠ nếu sửa cái này
wf.exists(mô tả)            -> wf.trace + GỢI Ý probe để tự chứng minh
wf.harvest(test_id)         -> test xanh + chưa có rule → đề xuất rule + coverage chỉ code
wf.orphans()                -> W-ID / requirement / rule / memory mồ côi
wf.status | dispatch | pickup | report | consume   -> thay scripts/relay (Redis)
```

**Luật cứng đi kèm:** `wf.exists` trả *"không tìm thấy"* = **ứng viên rỗng, KHÔNG phải bằng chứng vắng mặt**. Chỉ **chân 4 (probe) chạy thật** mới được kết luận.

---

## §13. GENERATE — CHO AI, KHÔNG CHO CLAUDE

**Chạy khi DB ĐỔI** (không phải lúc invoke) → sinh lại file bị ảnh hưởng → **commit CÙNG commit đó**. Đúng nếp `gen-facts` đang chạy (DoD §5).

| Kẻ đọc file | File | Vì sao không query được |
|---|---|---|
| **harness** | `memory/*.md` + `MEMORY.md` | harness tự nạp lúc mở phiên |
| **OFT** | `doc/req/*.md` | tool file-based |
| **human** | `docs/**` | bạn đọc bằng mắt |
| **git** | tất cả | kho cuối + **diff để bạn review** |

> **Claude KHÔNG nằm trong bảng này.** Claude **hỏi**, không đọc. Đó là toàn bộ chỗ mua được token.
> Nếu invoke → generate → Claude đọc `.md`, thì mình xây cả cái DB để sinh ra **đúng đống file đang có sẵn**. **Mua 0 token. Đó là mua công.**

**`test_no_dangling_generated_file()`**: `generate(DB) ≠ file` ⟹ **ĐỎ**. Đây là kẻ-ăn của generate, và nó chạy trong `pnpm test` — **không phụ thuộc CI đang chết**.

---

## §14. INGEST — ĐO, KHÔNG KHAI

| Nguồn | Ra cái gì | origin |
|---|---|---|
| **`pg_constraint`** (`conrelid`+`conkey`) | 121 `business_rule` + `constrains → column` | **measured** |
| `information_schema` | `table` · `column` | **measured** |
| **`oft trace`** → `trace.xml` | `impl` · `utest` · `itest` | declared |
| **coverage** (`vitest --coverage` reporter **`json`** · `pytest --cov --cov-report=json`) | `covers` (test → function) | **measured** |
| `git log` | `commit` · `touches` | **measured** |
| `infra/migrations/*.sql` | `migration` · `next` | **measured** |
| tree-sitter / SCIP | `file` · `function` · `test` | **measured** |

**⚠ Repo hiện dùng `reporter: ['text','json-summary']`** — `json-summary` **chỉ cho tổng số**. Phải đổi thành **`json`** để có `fnMap` (tên hàm) + `f` (số lần gọi). **`pytest-cov` chưa có ở `apps/ai`/`apps/mcp`** — phải thêm.
**⚠ SCIP định danh theo TÊN** (`src/foo.ts/MyClass#method().`) ⟹ dùng SCIP để **resolve**, **KHÔNG** lấy chuỗi SCIP làm PK.
**Danh tính code không cần ổn định** — cạnh **đo** được **dựng lại toàn bộ mỗi lần chạy**; đổi tên hàm ⟹ node mới, cạnh mới, node cũ `CASCADE`. **Không cần content-addressing.**

---

## §15. KHÔNG LÀM — VÀ VÌ SAO

| Không làm | Vì sao |
|---|---|
| **generate CODE** | trần MDA. Unison/MPS sống vì **sở hữu ngôn ngữ** — B5 nói mình **không**. Darklang chết đúng ở đây, **và LLM là lý do họ chết** |
| **lưu `body` của code trong DB** | 2 nhà ⟹ bài toán đồng bộ ⟹ phải chặn `Edit` + parser + round-trip. Lưu **`fingerprint`** là đủ để bắt drift |
| **chặn `Edit` trên `apps/`** | hết cần (không nhân bản body) |
| **`confidence real` trên cạnh** | cạnh xác suất **không ràng buộc được gì**; `CHECK` không cắn được số 0.4. Mơ hồ chỉ nằm ở **VIEW suy ra** |
| **tách `rules`/`symbols` 2 bảng** | ⟹ `edge.src` đa hình ⟹ **không FK nổi** ⟹ tái tạo bệnh ~24 mũi tên hư vô |
| **`fqn` làm PK** | đổi tên ⟹ **mọi cạnh chết im lặng**. Unison giải bằng hash; mình giải bằng **đo lại** |
| **Neo4j** | vài triệu cạnh — `WITH RECURSIVE` thừa. Quyết theo **nhãn** thay vì **số** là đúng cái §14 cấm |
| **vector DB riêng** | phải JOIN vector với rule ⟹ tự tạo 2-phase commit |
| **rule/requirement trong YAML/Confluence/Notion** | không diff, không CI, **không ép được** — đúng hiện trạng đang muốn thoát |
| **bỏ Redis** | P8 — relay không có bệnh đo được **[HUMAN]** |
| **mutation testing** | chính xác nhất (nhân quả, không phải đi ngang qua) nhưng **đắt** — để dành |
| **rời Postgres** | chỉ khi ĐO được: >20M cạnh & traversal >500ms · cần suy diễn bắc cầu (→ Datalog) · >50M vector |

---

## §16. RỦI RO ĐÃ BIẾT — GHI RA TRƯỚC

| # | Rủi ro | Đối phó |
|---|---|---|
| R1 | **Claude gắn tag SAI** — *"nó rất muốn làm bạn hài lòng"* | **cross-check coverage §8** — cơ chế **DUY NHẤT** phát hiện. Không tuỳ chọn |
| R2 | **coverage cho TẬP CHA** — test `@rule-X` cũng chạy qua logger/ORM/DI | xếp hạng theo **độ đặc hiệu** lúc **query** (không lưu). Chính xác thật = mutation testing (hoãn) |
| R3 | **embedding gần ≠ logic giống** (negation) | P4 — semantic chỉ là ứng viên; **probe quyết** |
| R4 | **121 rule hôm nay VÔ HÌNH với semantic** (không có chữ) | **harvest = việc số 1**: Claude đề xuất `statement_vi/en` + `rationale` từ `CHECK`+cột+code quanh → human duyệt A1 → probe **chắc chắn XANH** (Postgres đang ép) ⟹ **an toàn nhất chương trình** |
| R5 | **`fingerprint` quá nhạy** — Doorstop bắt review lại **vì đổi whitespace** | chuẩn hoá text **trước khi** hash |
| R6 | **`rev` bump để chữa cháy** | bump ⟹ tag cũ thành lỗi ⟹ phải sửa từng chỗ; test khẳng định nghĩa CŨ sẽ **ĐỎ** ⟹ lộ trong diff |
| R7 | **Xây trên cổng đã chết** — `ci.yml` `disabled_manually`, guards runner **offline**, 32/40 lần `queued` | mọi kẻ-ăn đặt vào **`pnpm test`** + **pre-commit** + **`PreToolUse`** — thứ **chạy local**, không có chế độ `queued`. **CI hỏng là việc của workflow cũ, không phải luật cho workflow mới** |
| R8 | **`grep` nói dối im lặng** vì locale (4 lần/buổi) | mọi script ingest ép **`LC_ALL=C`**. Và đây là lý do sâu nhất FK đáng giá: **nó không có chế độ im lặng** |
| R9 | **Tôi thiết kế trước khi nhìn** — 7 lần bị dữ liệu bác trong 1 buổi | mọi khẳng định trong file này kèm **path/lệnh tái lập**. Chưa đo thì ghi "chưa đo" |

---

## §17. ĐƯỜNG ĐI — THỨ TỰ CÓ LÝ DO

| # | Việc | Vì sao thứ tự này |
|---|---|---|
| **1** | Schema + `icp-wf` CLI + **ingest 70 memory** → generate lại `memory/*.md` + `MEMORY.md` → **`diff` phải RỖNG** | **nhỏ nhất mà chứng minh nhiều nhất**: round-trip đóng ở memory ⟹ đóng ở **mọi `.md``. memory đang **hoàn hảo** (70/70) ⟹ nếu round-trip vỡ, vỡ vì **thiết kế**, không vì dữ liệu bẩn |
| **2** | **Harvest 121 `CHECK`** → `business_rule` + `statement_vi/en` + `rationale` + `constrains → column` | **R4** — không có bước này thì `pgvector` tìm ra **số không**. Và đây là chỗ **rule đã có răng, chỉ thiếu lời** ⟹ an toàn nhất |
| **3** | `SessionStart` hook + `wf.law` + `CLAUDE.md` teo | luật tươi trước, rồi mới nói tới việc |
| **4** | ingest ADR + slice + BACKLOG → generate → `diff` rỗng | kho lớn hơn, sau khi round-trip đã chứng minh |
| **5** | `wf.context` + `wf.trace` + `wf.why` (MCP) | planner/coder bắt đầu **hỏi thay vì quét** |
| **6** | **`CLAUDE.md §2` bước 0 + `ICP_WEB_PLAN §8` chiều rule + VERIFY-DEPTH thêm hành vi** | **chữa §1.2** — chỉ làm được sau khi có `wf.context` |
| **7** | Hấp thụ `seam` + `verdict` + `report` + ledger → PG (FK bằng chứng) | chữa 31%/20%/xoá-vĩnh-viễn |
| **8** | RTM leg: OFT + coverage `json` + `pytest-cov` + cross-check | **cuối** — view khó nhất, cần mọi thứ trên |

**Bước 1 và 2 độc lập nhau** ⟹ chạy **song song** 2 slot được.

---

## §18. SONG SONG — KHÔNG PHÁ V1 **[HUMAN — B7]**

**Toàn bộ V3 dựng CẠNH V1, chưa bao giờ đè lên nó:**

| V1 (giữ nguyên, luôn chạy được) | V3 (dựng mới cạnh bên) |
|---|---|
| `.claude/commands/icp-multi-planners.md` | `.claude/commands/master-planners.md` |
| `.claude/commands/icp-multi-coders.md` | `.claude/commands/master-coders.md` |
| relay Redis (`scripts/relay`) | `wf.*` MCP + workflow-DB (Postgres) |
| `CLAUDE.md` (209 dòng, file) | `CLAUDE.md` teo + `SessionStart` hook inject |

**Cách chuyển:** hôm nay bạn gõ `icp-multi-coders`; sau khi V3 chạy ổn, bạn gõ **`master-coders`** thay thế. Không có "cutover" một-lần — **hai bộ tồn tại đồng thời**, bạn chọn bằng cái tên bạn gõ.

**Rollback = 0 giây:** V3 hư ⟹ gõ lại `icp-multi-*` ⟹ về nguyên trạng. V1 chưa bị đụng nên **không có gì để khôi phục**.

**⚠ Điều KHÔNG được lẫn:** "skill mới" **không** có nghĩa skill chuyển vào DB — skill vẫn là **file** (`.claude/commands/master-*.md`). Cái đổi là **skill mới gọi `wf.*` (hỏi DB) thay vì `relay` (đọc ống)** + nạp luật qua `SessionStart` hook. "master claude" = phiên Claude nạp skill mới, trỏ workflow-DB — **vẫn là Claude Code**, chỉ khác bộ luật nạp + công cụ gọi.

**⚠ Chỗ hai bộ ĐỤNG nhau — phải cô lập:** cả hai ghi cùng **một cây git** (B4). Nếu chạy V1 và V3 **cùng lúc**, `wf-lock` (Redis) của V1 và lock của V3 phải là **cùng một khoá** — nếu không, hai bộ giẫm chân trên cây git. ⟹ **V3 dùng LẠI `wf-lock` Redis của V1** (đã là lý do §9 giữ Redis), KHÔNG dựng khoá riêng. Đây là ràng buộc cứng: **trí nhớ tách đôi được, KHOÁ thì KHÔNG.**
