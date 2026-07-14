# S-T0V2 — CHƯƠNG-TRÌNH BUILD: làm lại T0 (Enforceable-Law Constitution)

> Realize **`docs/awf/T0-BLUEPRINT.md`** (6 kind · vòng-đời 4-chặng · 2 đường KNOW/ENFORCE · reuse) + **`docs/awf/T0-V2-SOLUTION.md`** (schema/thuật-toán). Build ĐẦY-ĐỦ một lượt, human-greenlit 2026-07-14.
> **Nhà code:** `~/projects/icpp/awf` (tự-chứa, PRODUCT). **Tracking:** sicp relay + docs. Type-B (engine) trừ S6 = Type-D (e2e).

## Mục-tiêu (blueprint 1 dòng)
Nâng T0 từ *máy-sinh-CLAUDE.md (persona+law)* → *bộ-não-vận-hành đầy-đủ: 6-kind · đồng-chọn-theo-khoảnh-khắc (overlap KNOW / subset ENFORCE) · kiểm-được · tái-dùng · tự-giàu*.

## 7 SLICE + phụ-thuộc

```
S0 ─┐          (S0,S1 KHÔNG dep → chạy song-song NGAY)
S1 ─┼─► S2 ─► S3 ─┐
    └─► S4 ───────┼─► S5 ─► S6
                  ┘
```

### S-T0V2-0 · INTROSPECT (nền domain-check) — Type-B — NO-DEP
- **Goal:** điền `db_table/db_column.is_tenant_key` + tên-bảng + `description_source` từ DB thật (structure-only, KHÔNG data).
- **Tasks:** T01 introspect-script (đọc schema nguồn: cột `tenant_id` / RLS policy → `is_tenant_key=true`) + upsert awf `db_table/db_column` (tenant-scoped) · T02 test + live-smoke trên 1 DB thật.
- **Acceptance:** ≥1 project introspect → tập bảng tenant-scoped đúng (đối-chiếu RLS policy thật); 0 data-row copy.

### S-T0V2-1 · SCHEMA — Type-B — NO-DEP
- **Goal:** schema T0-v2 (§II solution) trên hạ-tầng constitution có sẵn.
- **Tasks:** T01 ALTER `wf_record` (+`enforcement`,+`trigger jsonb`) · T02 bảng mới `wf_check`+`wf_check_fixture`+`wf_detector`+`wf_tag_vocab`+`wf_tag_edge` · T03 RLS (mẫu 002_rls) · T04 seed tag-vocab + ontology (data-path⊃money/auth/tenant).
- **Acceptance:** migrate idempotent + RLS FORCE A⊥B pass + ontology DAG không chu-trình.

### S-T0V2-2 · AUTHOR 6-KIND (chặng ①) — Type-B — dep:S1
- **Goal:** migrate TRỌN 6 kind vào record (không chỉ persona+law).
- **Tasks:** T01 skill (slice-analysis A–E+tier · task-decompose anti-over-hold · backlog-analysis · implement-against-contract · embedded-test-right-tier) từ `ICP_WEB_PLAN §8`+memories · T02 template (prompt-5-lớp · slice · report) · T03 glossary (seam·W-ID·strangler·Type-D…) · T04 memory-import (lesson→record + `[[links]]`→wf_relation) · mỗi record có `applies_when` (role/phase/topic) + `born_from_ref`.
- **Acceptance:** coverage: mọi kind ≥ seed tối-thiểu; gen projection (CLAUDE.md/persona/skill) drift-guard xanh.

> **⚠️ P5.4 (deep-dive pass-5, xác-nhận bằng code):** `engine/context.py` (`wf_context`) + `engine/laws.py` (`law_search`+`applies_when`+scope+precedence+demo-law) + `scoped_connection` (awf_app RLS, KHÔNG superuser) **ĐÃ TỒN-TẠI**. S3/S4 **EXTEND** chúng, **CẤM rebuild song-song**. Overlap-KNOW/subset-ENFORCE + check-runner = lớp THÊM lên `laws.py`/`context.py`.

### S-T0V2-3 · SELECT (chặng ②) — Type-B — dep:S1,S2 — **EXTEND engine/context.py+laws.py**
- **Goal:** đồng-chọn + đồng-ghép theo khoảnh-khắc (LÊN TRÊN law_search đã có).
- **Tasks:** T01 signal 3-trục (who/when/what) + caller-context · T02 `applies_when && signals` (KNOW overlap) + `eval_predicate(trigger,facts)` với ontology-expand (ENFORCE subset) · T03 precedence lattice(core<profile<project)+overrides+flag (bỏ int) · T04 verb `wf context(task,role,phase)` → gói {persona,skill,law,template,glossary,memory,will_enforce,gaps}.
- **Acceptance:** 3 kịch-bản blueprint (planner-analyze/coder-execute/verify) trả đúng gói; overlap≠subset kiểm rõ.

### S-T0V2-4 · CHECK (chặng ③ enforce) — Type-B — dep:S1,S0
- **Goal:** 4 check-engine + 8 enforceable-law.
- **Tasks:** T01 runner grep+ast · T02 runner domain-aware (query `is_tenant_key`) · T03 runner taint (BÓ tenant+money; degrade→domain nếu nhiễu) · T04 wire 8 enforceable (nối 6 guard bash: tenant/runtime-role/migration/facts/commit/parity + 2 mới money-unit/seed) + `wf_check_fixture` bắt-buộc (must-violate/must-pass) · T05 verb `wf verify(diff)`.
- **Acceptance:** mỗi check fixture xanh; verify trên diff mẫu → verdict đúng; taint tenant chứng `withTenant` thống-trị; 0 false-pos trên diff sạch.

### S-T0V2-5 · EPISTEMIC + PIPELINE (chặng ③ + full verb) — Type-B — dep:S3,S4
- **Goal:** gap/conflict + 5 verb khép pipeline.
- **Tasks:** T01 gap-analysis (signal-nổ ∖ signal-phủ) · T02 conflict-check (declared `wf_relation conflicts`) · T03 verb `wf declare/gen/gaps` + tích-hợp drift-guard · T04 `wf context/verify` trả kèm confidence+gap+not_run.
- **Acceptance:** gap báo đúng vùng chưa phủ; declare→kích law-set theo arch; drift-guard xanh.

### S-T0V2-6 · CO-PRODUCE + REUSE + E2E (chặng ④ + reuse + đóng) — **Type-D** — dep:ALL
- **Goal:** tự-giàu + tái-dùng + chứng end-to-end.
- **Tasks:** T01 relay-flush→record mới (④ co-produce) · T02 binding core⊕profile⊕project (`wf.toml [detectors]/[checks]`) · T03 **E2E trên diff ICP THẬT**: tenant-leak · money-unit · migration-missing-rollback · drift → engine bắt đúng + gap-report · T04 reuse smoke: đổi `wf.toml` → chạy project-2 (stub) không sửa core.
- **Acceptance (toàn giải-pháp):** (a) bắt đúng ≥ mọi lỗ enforceable trên 4 diff thật · (b) gap cho vùng chưa phủ · (c) 0 false-pos diff sạch · (d) reuse: project-2 chạy không sửa core.

## ⚠️ GROUNDING RULES (deep-dive lap-A, bắt-buộc mọi slice)
- **G1:** T0-v2 briefing = COMPOSE `law_search`+persona/skill-retrieval+`wf_context`(domain); **CẤM rebuild `engine/context.py`**. Đặt tên `wf brief` khác `wf context`.
- **G2:** retrieval đọc **`kb_node.metadata`+embedding** (laws.py), KHÔNG `wf_record`. Field mới (`trigger`/`enforcement`) **PHẢI project wf_record→kb_node.metadata**. Tag-select chạy trên kb_node.metadata.
- **G3:** ENFORCE (`trigger <@`) = deterministic MỚI (không tiền-lệ). KNOW = semantic(law_search có)+tag-filter(thêm). Cổng cứng KHÔNG semantic.
- **G4:** dùng `scoped_connection` (awf_app RLS, retrieve.py:268) + `_scope()` defense-in-depth; tái-dùng token-budget-trim (context.py) cho briefing-cap; GIỮ precedence (tiebreak laws.py:131).
- **Per-slice (ADR-052):** coder GROUND module cụ-thể tại path:line TRƯỚC code (catalog.py cho domain-check · dispatch.py cho co-produce · graph.py cho kb_edge), ghi CONFIRMED/STALE/CHANGED. CẤM rebuild song-song cái đã có.

## Vận-hành relay
- Slots **11-20** (idle post-AWF-ENGINE); cutover 1-10 giữ parked.
- Song-song: (S0,S1 ngay) → (S2 sau S1 · S4 sau S1+S0) → (S3 sau S2) → S5 → S6.
- Planner **deep-verify** S4 (taint tenant/money = high-consequence) + S6 (e2e diff thật). Coder test trong biên slice.
