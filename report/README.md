# Thư mục báo cáo LaTeX (`report/`) — Chuẩn CO5151 (6–8 trang Conference Style)

Thư mục này chứa mã nguồn LaTeX cho **Final Report D3** của môn **CO5151 — Advanced Agentic Artificial Intelligence (Application Track)** theo đúng mẫu tại [`project-guideline.pdf`](project-guideline.pdf) và trang môn học (`https://lexuanbach.github.io/5151/project.html`): **6–8 trang conference style** (`10pt`, không bìa/mục lục chiếm trang ở bản chính) + **Tài liệu tham khảo** + **Phụ lục A–D**.

## 1. Cấu trúc tệp và quy trình nộp `v1` (29/11) → `v2` (11/12)

| Tệp / Thư mục | Mục tiêu theo CO5151 Table 6 (Application Track) | Phụ trách chính |
|---|---|---|
| `main.tex` | File gốc (`10pt`, `a4paper`), khai báo `\reportstage` (`D3 Final Report (v1)` / `v2`) | Cả nhóm |
| `preamble.tex` | Layout conference (`20mm`/`22mm`), header `\makereportheader`, `vntex`, `listings`, `hyperref` | Cả nhóm |
| `sections/00_title.tex` | Trang bìa HCMUT tùy chọn (mặc định comment trong `main.tex` để giữ đúng khung 6–8 trang) | Cả nhóm |
| `sections/00_abstract.tex` | Tóm tắt nội tuyến & từ khóa | Cả nhóm |
| `sections/01_introduction.tex` | **§1 Introduction:** Người dùng (`AT1`), bài toán, lý do cần Agent (`workflows-vs-agents`) | Cả nhóm |
| `sections/02_background.tex` | **§2 Background & Related Systems:** Vì sao prompt guardrails & table-RBAC chưa đủ | Học + Khang |
| `sections/03_system_design.tex` | **§3 System Design & Rationale:** Luồng kiến trúc (`R1`), quyền MCP (`R2`), Memory (`R3`), $\ge 3$ axes (`R4`) | Trần Thái Học |
| `sections/04_implementation.tex` | **§4 Implementation, Robustness & Guarded Actions:** Framework vs tự code (`§8`), `AT2`, Audit Trail (`AT4`), User Walkthrough (`AT1`) | Học + Khang + Kiệt |
| `sections/05_evaluation.tex` | **§5 Evaluation:** Benchmark (`R5`), Baselines `B0`--`B2`, Ablation, Cost/Actual Spend (`§8`), Operational Envelope (`AT3`), Error Analysis | Tiêu Trí Kiệt |
| `sections/06_security_analysis.tex` | **§6 Security & Safety Analysis:** Threat Model (`R6`), $\ge 10$ Injection Direct/Indirect, Naive vs SQLGuard, Findings Acted On (`C3`) | Nguyễn Văn Hoàng Khang |
| `sections/07_limitations_validity.tex` | **§7 Limitations, Threats to Validity & Deployment Path:** Giới hạn (`F10`, PII...), Internal/External/Construct validity (`C4`) | Cả nhóm |
| `sections/08_ethics.tex` | **§8 Ethics & Responsible Use:** Giấy phép BIRD (`NOTICE`), dữ liệu tổng hợp, an toàn Attack Lab | Cả nhóm |
| `sections/09_conclusion.tex` | **§9 Conclusion:** Giá trị thực tế mang lại & hướng phát triển | Cả nhóm |
| `sections/99_appendix.tex` | **Phụ lục A–D:** (A) Reproducibility & Actual Spend, (B) Contribution Statement, (C) AI-Use Statement, (D) Task & Error Catalog | Kiệt + Khang |
| `tables/*.tex` | 5 bảng tách riêng (`permission_matrix`, `main_comparison`, `benchmark_summary`, `injection_comparison`, `cost_latency`) | Kiệt + Khang |
| `figures/` | `hcmut.png`, `architecture.png`, `evaluation.png` | Cả nhóm |
| `references.bib` | 7 nguồn trích dẫn chuẩn BibTeX (`ieeetr`) | Cả nhóm |

## 2. Hướng dẫn biên dịch PDF

```bash
cd report
latexmk -pdf main.tex
```

Hoặc chạy tuần tự `pdflatex main.tex` $\rightarrow$ `bibtex main` $\rightarrow$ `pdflatex main.tex` $\times 2$. (Hoặc mở `report/main.tex` trên VS Code LaTeX Workshop / Overleaf với compiler **pdfLaTeX**).

## 3. Lưu ý trước khi xuất bản `D3 v1` (29/11) & `D3 v2` (11/12)

- Giữ phần nội dung chính từ **§1 đến hết §9 trong đúng 6–8 trang** theo yêu cầu `R9` (`project-guideline.pdf`). Nếu vượt quá 8 trang, ưu tiên rút gọn bảng/diễn giải ở §5–§6 và đẩy chi tiết phụ sang `99_appendix.tex`.
- Điền đầy đủ toàn bộ các ô `\texttt{[ĐIỀN D3]}` (đặc biệt là tổng chi phí API thực chi ở **§5** & **Phụ lục A**, ghi nhận User Walkthrough ở **§4**, số liệu **B0–B2** và **$\ge 10$ Injection** ở **§5–§6**) từ lần chạy trên commit sạch (`dirty: false`).
- Khi chuẩn bị `D3 v2` sau Round 1 Defense (`02/12`), đổi `\reportstage` thành `D3 Final Report (v2)` trong `main.tex` và bổ sung các chỉnh sửa theo feedback của nhóm phản biện + giảng viên.
