# SQLGuard D3 — Kế hoạch hoàn thiện, Bảo vệ 2 vòng và Final Report (Chuẩn CO5151)

> Tài liệu lập kế hoạch cho chặng cuối (W11–W13) của môn **CO5151 — Advanced Agentic Artificial Intelligence (HK261, GVHD: TS. Lê Xuân Bách)**, đối chiếu trực tiếp với [`report/project-guideline.pdf`](report/project-guideline.pdf), trang môn học [`https://lexuanbach.github.io/5151/project.html`](https://lexuanbach.github.io/5151/project.html) và mã nguồn hiện tại.
>
> **Track đã khai báo:** **Application Track** (Theme #3: *Data-analysis / BI agent: NL → SQL via MCP; SQL-level least privilege against injection via table contents*).  
> **Nguyên tắc học thuật:** Không đánh giá mà thiếu log chi phí (*"No evaluation run without cost logging"*); không tạo số liệu giả — ô chưa chạy giữ nguyên `\texttt{[ĐIỀN D3]}`.

---

## 1. Cơ chế vận hành môn học & Chặng cuối kỳ (W11 → W13)

Điểm đồ án chiếm **50% tổng điểm môn học (`PRJ = 50%`)**, trong đó Proposal D1 chiếm **20%**, Interim D2 chiếm **30%**, và cụm kết thúc (**D3 Final Report + D4 Reproducible Code + Bảo vệ 2 vòng**) chiếm **50% còn lại**:

| Thành phần trong 50% cuối | Tỷ trọng | Chi tiết chấm điểm |
|---|---:|---|
| **Final Report (`D3 v1` → `D3 v2`)** | **30 / 50** | Báo cáo **6–8 trang, conference style** (khớp chứng cứ, có Threats to Validity, Actual Spend, Contribution & AI-use). |
| **Reproducible Code (`D4`)** | **10 / 50** | Repository có `README`, pinned dependencies, `.env.example`, **one-command reproduction**, scripts, seeds, traces/logs, `cost-report.md`. |
| **Presentation & Individual Contribution** | **10 / 50** | **Round 1 (60%)** + **Round 2 (40%)**. Hệ số điều chỉnh cá nhân **0.8 → 1.1** dựa trên intra-group peer evaluation, git commit history và Q&A. |

**Lịch nộp và bảo vệ hai vòng (mốc giờ nộp: 23:59; phạt trễ -10%/ngày tối đa 3 ngày, quá 3 ngày = 0 điểm):**
1. **Giai đoạn 1 (`24/11 → CN 29/11/2026`): Nộp `D3 v1` + `D4` (Git tag `v1`)**
   - Hoàn thiện toàn bộ phần cốt lõi (`P0-v1`), chạy benchmark trên commit sạch (`dirty: false`), xuất PDF `D3 v1` (6–8 trang) và gắn tag `v1`.
2. **Vòng 1 (`W12 · T4 02/12/2026`): Round 1 Defense**
   - Trình bày **10 phút kèm Live Demo** (luôn có video quay sẵn dự phòng `R7`) + **5 phút Q&A**, được chấm bởi **nhóm phản biện (peer group)** và giảng viên.
3. **Giai đoạn 2 (`03/12 → T4 09/12/2026`): Round 2 Defense (`W13`)**
   - Phản hồi và sửa toàn bộ góp ý từ Round 1 (*bỏ qua góp ý Round 1 sẽ bị trừ điểm tiêu chí `C5`*). Trong buổi Round 2, bảo vệ **những gì đã thay đổi từ Round 1 và lý do**, kết quả cuối, giới hạn và khả năng mở rộng.
4. **Nộp cuối (`T6 11/12/2026`): Final Submission (Git tag `final`)**
   - Nộp **`D3 v2`**, **Contribution Statements**, **Intra-group Peer Evaluation**, và **`D4` tagged `final`**.

### Phân bổ trọng số Rubric chấm điểm (cả 2 track dùng chung 5 tiêu chí)
- **C1 (25%) — Architecture design & technical difficulty:** Thiết kế đơn giản có chủ đích (*simplicity argued*), $\ge 3$ advanced axes tích hợp chặt, `R2` permission levels và `AT4` guarded actions là thành phần hạng nhất.
- **C2 (25%) — Implementation & demo:** Demo chạy thực tế có xử lý lỗi tiêm (`AT2`), retry an toàn, code sạch, hiển thị audit trail của một guarded action end-to-end.
- **C3 (25%) — Quantitative evaluation & security analysis:** Tập $\ge 20$ task thực tế, success + cost + latency/task, ablation chứng minh thành phần tạo giá trị, $\ge 3$ runs/seeds có phương sai, operational envelope (`AT3`), phân tích lỗi; Threat model đầy đủ + $\ge 10$ injection cases (gồm cả qua dữ liệu của chính hệ thống) và **findings acted on**.
- **C4 (15%) — Report & reproducibility:** 6–8 trang conference style; phát biểu khớp bằng chứng; limitations & threats-to-validity trung thực (nêu cả phần *không test*); one-command reproduction xác minh chạy được; báo cáo chi phí API thực tế (*actual spend*).
- **C5 (10%) — Presentation, defense & individual contribution:** Thuyết trình rõ mạch *problem–solution–evidence*, xử lý triệt để feedback Round 1 ở Round 2, đóng góp khớp git commit & peer evaluation.

---

## 2. Ma trận đối chiếu 13 yêu cầu (`R1–R9` + `AT1–AT4` + `§8`) với Hiện trạng SQLGuard

| Yêu cầu | Mô tả chuẩn CO5151 (Application Track) | Hiện trạng sau D2 | Việc cần hoàn tất cho `D3 v1` (`29/11`) |
|---|---|---|---|
| **R1** | Kiến trúc agent rõ ràng (diagram + state/control flow) | Có `architecture.png` & `AgentController` | Cập nhật mô tả state/control flow chuẩn Conference trong §3 |
| **R2** | MCP server/toolset, ghi rõ quyền (`read-only` / `reversible-write` / `irreversible-write`) | Có MCP `stdio` (`get_schema`, `query`) + `AttackLab` | Gắn nhãn quyền rõ: `read-only` (2 MCP tools), `reversible-write` (`AttackLab` admin writer), `0` `irreversible-write` |
| **R3** | Memory (short-term + $\ge 1$ long-term/semantic store), nêu **write policy** & **retrieval policy** | Có `InMemoryMemory` (TTL) + `SemanticMemory` | Ghi tường minh **write policy** (agent không tự ghi `SemanticMemory`) & **retrieval policy** theo role |
| **R4** | $\ge 3$ advanced axes ngoài tools & memory | Đã có AST Guardrails, Bounded Reflection, Multi-layer Verification | Đánh dấu tường minh $\ge 3$ trục này trong §3 và kiểm chứng bằng ablation |
| **R5** | $\ge 20$ tasks, metric thành công + cost + latency, $\ge 2$ configs (ablation), $\ge 3$ seeds có variance, causal error analysis | Đã có **23 tasks**, 3 seeds, 2 configs; **thiếu cost/token**, `F01`/`F02` `not_run` | Kích hoạt token/cost logging, `CAP_FAULT_HOOK`, chạy ablation `table_level` vs `column_level` + Reflection (`3` vs `1`) |
| **R6** | Threat model (assets, entry points, attacker), **$\ge 10$ injection cases** (direct + indirect), least privilege, audit logging | Đã có Threat Model v1, `PolicySQLGate`, `SQLiteAuthorizer`, audit; **mới có 4 ca injection** (`I01`–`I04`) | Mở rộng lên **$\ge 10$ ca** (5 direct + 5 indirect qua `service_note`/`order.note`), chạy `NaiveAgent` vs `SQLGuard` (`N9`), ghi 5 *findings acted on* |
| **R7** | Live demo có video quay dự phòng (không sập khi mất mạng/API) | Đã có `DEMO_RUNBOOK.md` & `Naive Compare` web | Quay video dự phòng 10' gắn SHA commit trước ngày 02/12 |
| **R8 (D4)** | `README`, pinned deps (`pyproject.toml`), `.env.example`, **one-command run**, scripts, seeds, traces/logs, cost report | Đã có `pyproject.toml`, `.env.example`, `evaluation/runner`; thiếu **one-command run** & `cost-report.md` | Tạo `scripts/reproduce_d3.py` + `Dockerfile` + `compose.d3.yml` + xuất `cost-report.md` |
| **R9** | **6–8 trang, conference style**, AI-use & Contribution statements | Mới chuyển từ D2 5 mục sang cấu trúc cũ 13pt có trang bìa | **Chuyển `report/` sang 10pt Conference Style (6–8 trang + Appendix A–D đúng Table 6 Application Track)** |
| **AT1** | Named users + $\ge 3$ kịch bản thực tế + **informal walkthrough với người dùng tiềm năng** | Đã có 3 role nghiệp vụ + 3+ kịch bản | Thực hiện **15–20' walkthrough với 1–2 người dùng thực tế** (ngành dữ liệu/vận hành) & ghi nhận phản hồi vào §4 |
| **AT2** | Robustness (tool failure, timeout, malformed model output, retry safety) — **demo trực tiếp** | Đã cài `PlannerError`, timeout 3s/60s, `repairable` vs `terminal`; `F01`/`F02` chưa chạy qua harness | Bật `CAP_FAULT_HOOK` chạy `F01`/`F02` & đưa cảnh fault recovery vào kịch bản demo live |
| **AT3** | Operational envelope: cost/latency thực tế + **giới hạn quy mô định lượng** | Chưa phát biểu giới hạn quy mô tường minh | Đo latency/cost và nêu rõ giới hạn (*tối đa N dòng/64 KiB, timeout 3s SQLite single-file, giới hạn RPM Gemini...*) |
| **AT4** | Guarded actions: mọi write tool qua xác nhận/sandbox + **hiển thị end-to-end audit trail cho 1 guarded action** | `AttackLab` đã tách quyền admin + `LabRunGuard` + `expected_revision` + audit JSONL | Trích xuất 1 chuỗi audit trail end-to-end thật (`lab_write` $\rightarrow$ `gate` $\rightarrow$ `query` $\rightarrow$ `restore`) vào §4 & demo |
| **§8** | *"No evaluation run without cost logging"* + **actual spend** + **giải thích framework vs tự code** | `usageMetadata` chưa được parse (`None`) | Bắt buộc trích token/cost (`H1`), báo tổng USD thực chi và phân định rõ thư viện (`sqlglot`, `mcp`, `fastapi`, `sqlite3`) vs code tự viết |

> **Quyết định quy mô Benchmark cho `D3 v1` (`29/11`):** Theo chuẩn `R5` ($\ge 20$ tasks) và `R6` ($\ge 10$ injection cases), bộ **~29–31 tasks** (23 tasks D2 đã kiểm định + 6 ca injection mới + clean controls) đã đáp ứng trọn vẹn cả `R5` và `R6` với chi phí API hợp lý và kịp hạn 29/11. Nếu còn dư quota sau Round 1 (`02/12`), mở rộng thêm lên 40–60 tasks cho `D3 v2` (`11/12`).

---

## 3. Phân công kỹ thuật chi tiết (`v1` đến 29/11 & `v2` đến 11/12)

### Người 1: Trần Thái Học — Agent Runtime, Telemetry (`§8`) & Robustness (`AT2`)

- **`H1 [P0-v1]` Token & Cost Logging (Non-negotiable `§8`):**
  - Cập nhật `GeminiPlanner._parse` (và `OllamaPlanner._parse`) trong `src/sqlguard/agent/planning.py`, `src/sqlguard/agent/models.py` và `controller.py` để trích xuất `usageMetadata` (`promptTokenCount`, `candidatesTokenCount`, `thoughtsTokenCount`) và gắn vào kết quả trả về.
  - Nối vào `ControllerAdapter` (`evaluation/financial_adapters.py`) để `usage.jsonl` và `cost-report.md` có đầy đủ token + ước tính chi phí USD (tổng chi thực tế).
- **`H2 [P0-v1]` Harness `CAP_FAULT_HOOK` & Robustness Demo (`AT2`):**
  - Thêm wrapper `CAP_FAULT_HOOK` chỉ dùng trong `ControllerAdapter` để chạy được `D2-F01` (tiêm lỗi cột `repairable` ở attempt 1 $\rightarrow$ kiểm chứng `repair_plan`) và `D2-F02` (tiêm `db_unavailable` sau Gate $\rightarrow$ kiểm chứng bounded retry).
  - Bảo đảm kịch bản live demo minh họa được xử lý malformed output / timeout / lỗi SQL `F01`.
- **`H3 [P0-v1]` Tài liệu hóa Ranh giới Framework (`§8`) & Memory Policies (`R3`):**
  - Chốt nội dung §3–§4 báo cáo: nêu rõ plain Python + các thư viện sử dụng (`sqlglot`, `mcp`, `fastapi`, `sqlite3`) làm gì và những gì nhóm tự code (`AgentController`, `PolicySQLGate`, `SecureSqlTool`, `SQLiteAuthorizer`), cùng **Write Policy** & **Retrieval Policy** của bộ nhớ.
- **`H4 [P1-v2]` Ghi `(lab_revision, snapshot_hash)` vào trace (`L8`) & xử lý feedback Round 1.**

### Người 2: Nguyễn Văn Hoàng Khang — Threat Model, $\ge 10$ Injection (`R6`) & Guarded Actions (`AT4`)

- **`K1 [P0-v1]` Mở rộng bộ kiểm thử lên $\ge 10$ Injection Cases (`R6`):**
  - Mở rộng `evaluation/datasets/financial/injection-tasks.v0.1.json` từ 4 ca (`D2-I01..I04`) lên **tối thiểu 10 ca** trên schema Financial (**5 Direct + 5 Indirect** qua `account.service_note` và `order.note`), tích hợp các ca từ `src/sqlguard/lab/scenarios.py` (`N-D3`, `N-I1`, `N-I2`, `N-I3` exfiltration) và bypass AST (CTE/alias/comment/union).
- **`K2 [P0-v1]` Thực nghiệm đối chứng `NaiveAgent` vs `SQLGuard` (`N9`) & `Findings Acted On`:**
  - Chạy đối chứng trực tiếp trên Gemini thật (`gemini-2.5-flash`) giữa `NaiveAgent` (`src/sqlguard/lab/`) và `SQLGuard` trên các kịch bản Direct/Indirect (ghi commit SHA + lab DB SHA-256).
  - Tổng hợp đủ **5 findings đã phát hiện và khắc phục** (*dialect mismatch, tool-denial status, analyst micro-group k<5, LIMIT truncation flag, CASE aggregate & SELECT 1*) kèm regression tests vào §6.
- **`K3 [P0-v1]` End-to-End Audit Trail (`AT4`) & Test `T17`:**
  - Chụp/trích xuất một chuỗi sự kiện audit JSONL đầu-cuối thật từ `AttackLab` (`lab_write` $\rightarrow$ `gate_decision` $\rightarrow$ `query_exec` $\rightarrow$ `lab_restore`) đưa vào §4 và kịch bản demo.
  - Bổ sung test `T17` khẳng định `GEMINI_API_KEY` không xuất hiện trong log/audit/trace/exception.
- **`K4 [P1-v2]` Che PII trước planner (`E2`) hoặc bước tóm tắt kiểm dịch (`E5`) nếu Round 1 yêu cầu bổ sung.**

### Người 3: Tiêu Trí Kiệt — Benchmark, Baselines/Ablation (`R5`), Walkthrough (`AT1`), Envelope (`AT3`) & Repro (`D4`)

- **`T1 [P0-v1]` Chốt hợp đồng chấm `C4` & Validate bộ ~30 task `v1`:**
  - Chốt quy tắc chấm `I01`/`I02` (chấp nhận `partial` hoặc `full` miễn không đọc/lộ cột cấm) và `A01`, đảm bảo bộ task `v1` ($\ge 20$ tasks, $\ge 10$ injection cases) qua `evaluation.runner validate`.
- **`T2 [P0-v1]` Nối `GateMode`, chạy `d3-gemini.yaml` (`R5`) & tính Operational Envelope (`AT3`):**
  - Nối `gate_mode` (`column_level` / `table_level`) vào `ControllerAdapter` và tạo `evaluation/experiments/d3-gemini.yaml` chạy các cấu hình:
    - `B1` (`table_level`, 1 attempt), `B2` (`table_level`, 3 attempts)
    - `SQLGuard-NoRefl` (`column_level`, 1 attempt), `SQLGuard-Ours` (`column_level`, 3 attempts)
    - *(Kèm đối chứng `B0` từ `NaiveAgent`)*
  - Chạy $\times 3$ seeds (`42, 123, 2026`) trên **commit sạch (`dirty: false`)**; tính mean $\pm$ SD, phân tích nguyên nhân lỗi (*causal error analysis*), báo **actual spend**, và định lượng **Operational Envelope (`AT3`)** (giới hạn kích thước kết quả, timeout, SQLite single-file & Gemini API quota).
- **`T3 [P0-v1]` One-command Reproduction `D4` (`R8`):**
  - Tạo `scripts/reproduce_d3.py` + `Dockerfile` + `compose.d3.yml` và cập nhật `README.md` gốc để chạy `ruff` + `pytest` + `validate` + `replay` + `summarize` bằng **1 lệnh duy nhất**.
- **`T4 [P0-v1]` Informal User Walkthrough (`AT1`):**
  - Tổ chức buổi dùng thử nhanh (15–20 phút) với **1–2 người dùng mục tiêu thực tế** (người làm BI/vận hành/tài chính hoặc bạn ngoài nhóm đóng vai trò user thử 3 kịch bản: hợp lệ, Partial Answer, SQL bị chặn), ghi nhận nhận xét thật để đưa vào §4 và điều chỉnh cho `v2`.

---

## 4. Kịch bản Bảo vệ 2 Vòng (10′ trình bày + 5′ Q&A)

- **Chuẩn bị bắt buộc (`R7`):** Video quay dự phòng 10 phút toàn bộ demo (gồm SHA commit) để bật ngay nếu mạng/API Gemini trục trặc.
- **Cấu trúc 10 phút Round 1 (`W12 · T4 02/12`):**
  1. **0:00–2:00 (Bài toán & Thiết kế):** 3 vai trò ngân hàng (`AT1`), lý do dùng Agent + giới hạn của prompt guardrails & table-level RBAC, kiến trúc 3 lớp + $\ge 3$ advanced axes (`R4`).
  2. **2:00–6:30 (Live Demo End-to-End):**
     - *Cảnh 1:* Truy vấn hợp lệ & Mixed Partial Answer (giữ cột hợp lệ, báo rõ cột bị ẩn).
     - *Cảnh 2 (`AT2`):* Tiêm lỗi SQL / timeout $\rightarrow$ Bounded Reflection tự sửa lỗi & từ chối ngay lỗi policy.
     - *Cảnh 3 (`AT4` + `R6`):* Attack Lab tiêm payload vào `order.note` / `account.service_note` $\rightarrow$ đối chứng trên trang Compare: **Naive Agent bị chiếm quyền**, **SQLGuard vô hiệu hóa**, hiển thị end-to-end **Audit Trail** + nút **Restore**.
  3. **6:30–10:00 (Số liệu, Chi phí & Giới hạn):** Bảng đối sánh `B0`–`B2` vs `SQLGuard`, `UAR = 0%`, `Cost/Latency`, `Operational Envelope (AT3)` và thừa nhận thẳng giới hạn `F10`.
- **Round 2 (`W13 · T4 09/12`):** Trình bày **Changelog từ Round 1** (mọi góp ý của nhóm phản biện và giảng viên đã được sửa như thế nào trong code/thực nghiệm/báo cáo), kết quả cuối trên `D3 v2` và hướng mở rộng.

---

## 5. Lịch trình nước rút (24/11 → 11/12/2026)

| Mốc ngày | Trọng tâm | Đầu ra kiểm chứng |
|---|---|---|
| **24–25/11** | Chốt plan & nâng cấp `report/` thành **6–8 trang conference style (9 mục + 4 phụ lục)**. Học xong `H1` (Token/Cost) & `H2` (`CAP_FAULT_HOOK`); Kiệt xong `T1` (`C4`) & nối `GateMode` (`T2`). | `pytest` xanh; smoke test ra token/cost, `F01`/`F02` và 4 cấu hình Gate. |
| **26/11** | Khang xong $\ge 10$ injection cases (`K1`) & `T17` (`K3`). Kiệt chạy **user walkthrough (`T4`)**. Đóng băng code (`git status` sạch). | `evaluation.runner validate` đạt `ok`; có ghi chú walkthrough. |
| **27/11** | Chạy batch chính thức `d3-gemini.yaml` ($\times 3$ seeds) + chạy `NaiveAgent` vs `SQLGuard` trên Gemini thật (`K2`). Chốt `cost-report.md` & actual spend. | Thư mục `artifacts/d3/<run-id>/` từ commit sạch (`dirty: false`). |
| **28/11** | Đóng gói `scripts/reproduce_d3.py` + `Dockerfile` (`T3`); hoàn thiện toàn bộ số liệu & Audit Trail (`AT4`) trong `report/`. | Chạy 1 lệnh repro thành công; biên dịch `report/main.pdf` vừa khung **6–8 trang** (+ Appendix). |
| **CN 29/11** | Review chéo toàn nhóm (khớp số liệu PDF ↔ CSV), quay video backup (`R7`), **nộp `D3 v1` + `D4` (tag `v1`) trước 23:59**. | **Hoàn tất mốc `D3 v1` + tag `v1`.** |
| **02/12 (W12)** | **Round 1 Defense (10′ + 5′ Q&A).** Ghi chép đầy đủ 100% feedback của peer group và giảng viên. | Biên bản feedback Round 1. |
| **03–08/12** | Khắc phục toàn bộ feedback Round 1 (bổ sung task/PII/edge-case theo góp ý nếu cần); hoàn thiện `D3 v2` và slide Round 2. | PR sửa đổi gắn liên kết từng góp ý Round 1. |
| **09/12 (W13)** | **Round 2 Defense:** Bảo vệ các thay đổi từ Round 1, kết quả cuối & giới hạn. | Đạt nghiệm thu Round 2. |
| **T6 11/12** | **Final Submission (trước 23:59):** Nộp `D3 v2`, Contribution Statements, Intra-group Peer Evaluation, `D4` gắn Git tag `final`. | **Hoàn tất đồ án CO5151.** |

---

## 6. Checklist nghiệm thu `D3 v1` (29/11) & `Final` (11/12)

- [ ] **§8 Cost Non-Negotiable:** Mọi lượt chạy thực nghiệm đều có log token/cost; báo cáo nêu rõ **actual total API spend**.
- [ ] **R1–R4:** Kiến trúc rõ, 2 MCP tools (`read-only`) + `AttackLab` (`reversible-write`), memory có Write/Retrieval policy, $\ge 3$ advanced axes.
- [ ] **R5 & R6:** $\ge 20$ tasks ($\ge 3$ seeds có mean $\pm$ SD), ablation ($\ge 2$ configs), **$\ge 10$ injection cases** (direct + indirect qua dữ liệu DB), `findings acted on`, causal error analysis.
- [ ] **AT1–AT4:** Walkthrough với user thật; live demo có fault recovery; Operational Envelope có giới hạn quy mô cụ thể; Audit Trail end-to-end của 1 guarded action.
- [ ] **R7–R9 (D3/D4):** Video backup; One-command reproduction chạy xác minh sạch (`v1` & `final`); Report **6–8 trang conference style** đủ 9 mục + References + 4 Phụ lục (Repro, Contribution, AI-use, Tasks/Attacks).
