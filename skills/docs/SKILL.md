---
name: Formal Document Writing & Editing Guidelines
description: Structural templates, layout standards, Harvard referencing formulas, and tone calibration rules for producing executive-level professional documentation.
---

# Formal Document Writing & Editing: Senior Lead Guidelines

## Role & Framing
Approach every document like a Senior Document Editor / Business Communications Director at a top-tier management consultancy (e.g., McKinsey, BCG style) or research university. Your name is on the line for absolute structural and formatting consistency, not just the raw text content. Your design goals are visual clarity, mathematical alignment, typographic rigor, and analytical integrity. Treat every document layout as a cohesive unit where logic dictates structural hierarchy.

---

## 1. Document Architecture
Every formal document must align with this architectural skeleton to guarantee reader comprehension:

1.  **Title Page (Page 1):** Mandatory for any document over 3 pages. Must clearly list:
    *   Document Title (Highest typographic weight, H1 equivalent).
    *   Document Subtitle (Context setter).
    *   Author Name & Title.
    *   Date of Completion/Revision (ISO format preferred: YYYY-MM-DD).
    *   Organization/Agency affiliation.
2.  **Executive Summary:** Mandatory for reports over 10 pages; highly encouraged for 4–9 pages. Placed immediately preceding the Table of Contents. Maximum 1 page. States the problem, key findings, and recommended actions without referencing section numbers or figures.
3.  **Table of Contents (ToC):** Required for documents exceeding 4 pages. Built using H1 and H2 levels with dot leaders linking to page numbers.
4.  **Numbered Heading Hierarchy:** Apply a decimal numbering system to section dividers:
    *   `1.0 Main Section Category` (H1)
    *   `1.1 Sub-section division` (H2)
    *   `1.1.1 Detail category` (H3)
    Never jump levels (e.g., an H3 must never immediately follow an H1 without an intervening H2).
5.  **Appendices:** Reserve the main document body for analytical reasoning. Relocate raw codebases, secondary surveys, financial data, and mathematical proofs to labeled Appendices (e.g., "Appendix A: Full Dataset Summary") at the end of the document.

---

## 2. Heading & Typography Rules
Do not select fonts or weights on an ad-hoc basis. Fix global system styling properties:

### Typography Scheme
*   **Body Typeface:** Utilize a highly legible, standard system face. Choose either a clean Serif for reading comfort (e.g., Georgia, Times New Roman, Garamond) or a neutral Sans-Serif for technical/modern docs (e.g., Arial, Calibri, Segoe UI). Choose **ONE** face and apply it universally for body copy.
*   **Heading Typeface:** Use a distinct weight or style (e.g., Segoe UI Bold header paired with Georgia body) to define boundaries. Limit font usage to a maximum of 2 typeface families.
*   **Casing System:** Select either **Title Case** or **Sentence Case** for all headings. Never mix casing conventions between sections.

### Prescriptive Type Scale
*   `Document Title`: `24pt` | Bold | Line-spacing: `1.15` | Space-after: `18pt`
*   `H1 (Main Section)`: `18pt` | Bold | Space-before: `18pt` | Space-after: `8pt`
*   `H2 (Sub-section)`: `14pt` | Bold | Space-before: `14pt` | Space-after: `6pt`
*   `H3 (Sub-sub-section)`: `12pt` | Semi-bold/Italic | Space-before: `12pt` | Space-after: `4pt`
*   `Body Text`: `11pt` or `12pt` | Regular weight | Space-after: `6pt` (No double enters)
*   `Line Spacing`: Set between `1.15` and `1.25` for optimal readability.

---

## 3. Alignment & Spacing Rules

### Text Alignment
*   **Left-Aligned (Ragged Right):** Keep all body text left-aligned by default. 
*   *Warning:* Never use full justification (`text-align: justify`) in document layouts unless the engine supports automated hyphenation control. Otherwise, the engine forces arbitrary spacing between words, creating ugly vertical "rivers of white space" that distract the eye.

### Layout Geometry
*   **Margins:** Set to exactly `1.0 inch` (`2.54 cm`) on all four sides.
*   **Paragraph Spacing:** Do not press "Enter" twice to separate paragraphs. Use the layout engine's paragraph settings (`space-after: 6pt` or `8pt`).
*   **List Indentation:** Indent nested bulleted or numbered elements by exactly `0.5 inches` (`1.27 cm`) from the margin boundary. Ensure bullets nest logically:
    *   Level 1: Solid disc (•)
    *   Level 2: Open circle (o)
    *   Level 3: Dash (-)
*   **Keep-with-Next (Orphan Prevention):** Set headers (`H1`, `H2`, `H3`) to "Keep with next" paragraph settings. A heading should never sit alone at the bottom of a page without at least 2 lines of body text underneath.

---

## 4. Citation & Referencing Rules (Harvard Style)

### In-Text Citations
Use the author-date system immediately following cited findings or arguments:
*   Single Author: (Smith, 2021)
*   Dual Authors: (Smith and Jones, 2020)
*   Three or More Authors: (Smith et al., 2019)
*   *Placement:* Always place the citation *inside* the sentence punctuation:
    > "...as established in the quarterly economic report (Government Treasury, 2023)."

### Reference List vs. Bibliography
*   **Reference List:** Alphabetized directory at the end of the document containing **ONLY** the direct sources cited in the body.
*   **Bibliography:** Contains everything in the Reference List plus background reading materials consulted during research but not directly cited.
*   *Verification:* Ensure a perfect 1:1 mapping between in-text citations and the reference list. Every citation must appear in the references, and every reference must be cited in the text.

### Verification of Alternative Styles
If the brief asks for APA, Chicago, or IEEE instead of Harvard, stop and clarify version requirements before generating content.

---

## 5. Tables, Figures & Data

Data presentation sheets must follow rigorous visual layouts:

### Labeling & Captions
Every graphic, chart, or data module requires a unique, numbered identifier and title:
*   **Tables:** Caption placed *above* the table (e.g., "Table 1. Q3 Financial Metrics").
*   **Figures (Charts, Diagrams):** Caption placed *below* the figure (e.g., "Figure 1. Operational workflow paths").
*   **References:** Every table or figure must be explicitly referenced in the body copy prior to its appearance (e.g., "As detailed in Table 1, the projection...").

### Table Construction
*   **Headers:** Set in bold text with a light background fill (e.g., 10% black/gray hue).
*   **Borders:** Use thin, muted horizontal rules (`0.5pt` thickness). Avoid thick grid borders or heavy vertical dividers to prevent visual clutter.
*   **Data Alignment:** Left-align text columns, right-align numeric indicators, and align decimal places.
*   **Source Line:** Always include a source notation at the bottom of the table:
    > *Source: Ministry of Industry Data Repository (2025).*

---

## 6. Tone & Language for Formal Documents

*   **No Contractions:** Never write "don't," "can't," "won't," or "it's." Expand formatting to: "do not," "cannot," "will not," "it is."
*   **Acronym Initialization:** Define all acronyms on first occurrence. E.g., "...according to the World Health Organization (WHO)." Use the acronym alone thereafter.
*   **Terminology Rigor:** Use consistent terminology. Do not swap synonyms (e.g., if you label resources "assets" in Section 2, do not rename them "inventory" or "properties" in Section 4).
*   **Active vs. Passive Voice:** Prefer the active voice for analytical arguments (e.g., "The team designed the trial" rather than "The trial was designed by the team"). Passive voice is acceptable only in methods sections to detail processes:
    > "Water samples were collected and processed at 20°C."

---

## 7. Review & QA Checklist
Execute these checks before submitting any formal document:

*   [ ] **Level Checklist:** Have all heading levels (H1, H2, H3) been formatted sequentially without skipped levels?
*   [ ] **Citation Directory Mapping:** Does every in-text citation match an entry in the Reference List?
*   [ ] **orphan Check:** Are there any headings stranded at the bottom of a page?
*   [ ] **Grid & Padding Check:** Do all tables feature vertical padding (minimum 6pt cell margins) and cleanly aligned numbers?
*   [ ] **Terminology Audit:** Have we used the same exact names for datasets, agencies, and variables across all sections?
*   [ ] **Review standard:** Would this document be accepted by a senior partner at a top management consultancy or a peer-review panel at a top-tier university?
