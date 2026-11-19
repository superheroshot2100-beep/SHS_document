# CLAUDE.md

This file guides Claude Code when working in this repository.

## What this repo is

This is **not a code project**. It is a QA workspace for writing test cases for **SHBond**, the bond product of SHS (Saigon–Hanoi Securities). SHBond covers the SHBond back-office Portal and the investor-facing channels **SH Smart App / Web**. The repo holds:

- the source requirements (BRD/SRS exported from Confluence),
- a "Senior QA Kit": the rules, techniques, prompt pipeline and checklist that every test case must follow,
- Excel/CSV templates,
- the test-case deliverables (Markdown + Excel/CSV).

**All deliverables are written in Vietnamese.** Standard English QA terms are kept as they are (Precondition, Expected Result, Priority…).

## Layout

| Path | Contents |
|---|---|
| `00_HUONG_DAN_SU_DUNG.md` | How to use the kit (installed as a claude.ai Project; daily commands) |
| `01_Project_Instructions.md` | Persona (Senior QA/Test Lead), mandatory principles, quick commands `/B1`…`/B5`, `/FULL`, `/REVIEW`, `/EXPORT`, `/BUG` |
| `02_Rules_Viet_Test_Case.md` | **Mandatory** 12-column TC template, content rules, ordering, priority definitions, API TC rules |
| `03_Ky_Thuat_Thiet_Ke_Test.md` | Test design techniques (EP/BVA/DT/ST/UC/EG/PW) + error-guessing checklists per field type |
| `04_Prompt_Pipeline_B1-B5.md` | Exact output format for each pipeline step B1→B5, plus /REVIEW and /BUG |
| `05_Checklist_Review_RTM.md` | Self-review checklist, RTM format, quality targets, test summary format |
| `Prompt/*.txt` | Older standalone prompts (requirement analysis, test design, review); these use `REQ-xx` instead of `BR-xx` |
| `Rule/*.docx` | Company standards for requirement analysis, writing TCs and reviewing TCs |
| `doc/BRD/`, `doc/SRS/` | Requirement sources (see "Reading requirement docs") |
| `Design/` | Figma screenshots (`Bond.zip` holds the same images) |
| `template/` | Excel templates (`Template_TestCase_ChiTiet.xlsx`, `Template_TestCase_Checklist.xlsx`) and sample CSV test-script formats |
| `output/` | Deliverables: `TC_*.md` (full B1→B5 analysis + TCs) and matching `.xlsx`/`.csv` |

Files 01–05 are the source of truth for how test cases are written. Read the ones that apply before producing or reviewing test cases. Do not restate their rules elsewhere; if a rule changes, edit those files.

## Workflow: pipeline B1 → B5

1. **B1 – Requirement analysis:** overview/roles, features, Business Rules table (`BR-01`…, each with its source section), field list, Q&A (`Q-01`…, rated Blocker/Major/Minor, with a proposed assumption), top 5 risks.
2. **B2 – Strategy:** scope, test types, technique per BR, test environment/data, exit criteria.
3. **B3 – Test conditions:** EP/BVA tables, decision tables, state transitions, use cases, error guessing (`TCO-<BR>-<n>`).
4. **B4 – Test cases** in the 12-column template, ordered UI → Positive → Validation → Boundary → Negative → Permission → Security/Integration.
5. **B5 – Self-review:** RTM (every BR covered), checklist results, fixes plus added TCs (marked "Bổ sung B5" in Note), statistics.

Stop for confirmation after each step, unless the user asks for `/FULL`. `/FULL` runs straight through and stops only on Blocker Q&A.

Non-negotiables (from 01/02): use only the provided documents and never invent business logic. Mark gaps as `[GIẢ ĐỊNH]`/`[GĐ]` and link them to a `Q-xx`. One TC tests one objective. Test data must be concrete. Expected results must be measurable; for UI → data → side effect, check each layer that applies. Negative TCs must check both the error **and** that no action happened. Quote error messages verbatim from the source.

## Output conventions (follow existing files in `output/`)

- **Markdown:** `output/TC_<Feature>_<Source>.md`, for example `TC_Danh_muc_Hop_dong_SRS_CONTRACT.APP.01.md` or `TC_Phu_luc_05_SRS11_B1-B5.md`. The header block names the single source document and its version, the author and date, and the legend (`[GĐ]`, `Q-xx`). Shared preconditions and procedures are defined once (`PC-01`, `SOP-01`…) and referenced by ID in the TCs. Multi-line cells use `<br>`.
- **TC ID:** `TC_<MODULE>_<FUNC>_<NNN>`, for example `TC_PL05_UI_001`.
- **Excel:** `"SHS - App - <Feature> (<SRS/BRD ref>) - Test case[ vN].xlsx"` or `"SHBond - 2026 - <Feature> - Test case (<ref>).xlsx"`. Follow `template/Template_TestCase_ChiTiet.xlsx`:
  - row 1: title
  - row 2: project metadata (yellow cells = manual input)
  - row 3: auto-counted Pass/Fail/Blocked/Not Run/% Pass
  - row 5: header; data starts at row 6
  - typical sheets: `Test Case`, `Phân tích B1-B3`, `REQ & RTM`, `Q&A log`, `Test Data`, `Hướng dẫn`
- **Revisions:** create a new versioned file (`v2`, `v2.1`, `(App-API)`…). Do not overwrite an earlier deliverable unless the user asks.
- **CSV test scripts** (UTF-8 with BOM, for import into the test tool): `Name, Status, [Precondition,] Priority, Test Script (Step-by-Step) - Step, Test Script (Step-by-Step) - Expected Result, Result/Test Result, Note, Jira/Bug ID, Evidence`. `Name` uses the form `[Group] Kiểm tra …`. Match the columns of the closest file in `template/`.

## Reading requirement docs

The `.doc` files in `doc/` are **Confluence MHTML exports**: MIME text with quoted-printable HTML, not real Word files. Filenames use `+` for spaces. To read one, decode the quoted-printable part as UTF-8 and strip the HTML tags, for example with Node (put the script in the scratchpad, not the repo):

```js
// node decode.js "<file.doc>"
const fs=require('fs');let s=fs.readFileSync(process.argv[2],'latin1');
s=s.replace(/=\r?\n/g,'').replace(/(=[0-9A-F]{2})+/g,m=>Buffer.from(m.split('=').slice(1).map(h=>parseInt(h,16))).toString('latin1'));
s=Buffer.from(s,'latin1').toString('utf8').replace(/<style[\s\S]*?<\/style>/g,'')
 .replace(/<\/(p|tr|li|h\d)>/g,'\n').replace(/<\/t[dh]>/g,' | ').replace(/<[^>]+>/g,'').replace(/&nbsp;/g,' ').replace(/\n\s*\n+/g,'\n');
console.log(s);
```

SRS naming:
- Channel-facing specs follow `SBOND.<AREA>.<APP|WEB>.<NN>`. Areas: `CONTRACT`, `DOCCONFIRM`, `HISTORY`, `HOLDING`, `ORDER`. App and Web versions usually exist in pairs.
- Portal/back-office specs are numbered by section (`1.x` products/issuers, `2.x` policies, `3.x`–`4.x` inventory/limits, `5.x` customers, `7.x` orders/contracts/liquidation, `9.x` APIs, `10.x`–`11.x` issuance and forms).
- BRDs live in `doc/BRD/`.

Common domain terms: TPRL (privately placed bonds), TCPH (bond issuer), NĐT/NĐTCN (investor/professional investor), HĐ (contract), TSBĐ (collateral), Flex (core securities system). `069P000001` is the SHS proprietary account used in form mappings.

## Tooling notes

- Windows. Use Git Bash or PowerShell. **Python is not installed** (`python3` is the Microsoft Store stub). **Node.js is available.**
- The existing `.xlsx` files were generated with openpyxl. To produce Excel here, either ask the user to install Python + openpyxl, or use a Node library (e.g. `exceljs`) installed in the scratchpad. Reproduce the template's layout and styling either way.
- `.xlsx` files use inline strings (no `sharedStrings.xml`). To inspect one without a library, run `unzip -p file.xlsx xl/worksheets/sheet1.xml` and decode the `&#NNNN;` entities.
- Commits are plain `update <dd/mm>` messages on `main`. Commit only when the user asks.
