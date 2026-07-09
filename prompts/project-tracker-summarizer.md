Here's a detailed prompt you can use to generate the same output in a single request:

---

## Prompt for Project Status Summary with AI-Generated Summaries
```md
**Task:** Create a new sheet called "Status Summary" that summarizes project statuses from the 'pivot' sheet. For each project, extract key information and generate concise AI summaries based on the recent dated entries in the Remark column.

**Source Data Location:** 
- Sheet: `pivot`
- Data starts at row 7 (row 6 contains headers)
- Relevant columns:
  - Column A: Client Name
  - Column B: Project Code
  - Column C: Project Name
  - Column D: Project (reference)
  - Column E: Status
  - Column J: Remark (contains dated entries in formats like `[DD-Mon-YYYY]:` or `(DD-Mon-YYYY):`)

**Output Requirements:**

1. **Create a new sheet** named "Status Summary" with the following columns:
   - Column A: **Client Name** - Copy from pivot column A
   - Column B: **Project Code** - Copy from pivot column B
   - Column C: **Project Name** - Copy from pivot column C
   - Column D: **Status** - Copy from pivot column E (current project status)
   - Column E: **Project Summary (AI)** - AI-generated 1-3 sentence summary

2. **For Column E (Project Summary):**
   - Parse the Remark column (J) to extract the **last 5-6 dated entries**
   - Use AI/LLM to analyze these entries and generate a **concise 1-3 sentence summary** that:
     - Describes the current state of the project
     - Highlights what's happening now or what's pending
     - Is easy to read and understand at a glance
     - Avoids listing raw dated entries verbatim
   - If no dated entries exist, write "No project updates available."

3. **Formatting:**
   - Add header row with bold formatting and a colored fill (e.g., blue background with white text)
   - Freeze the header row
   - Enable text wrapping for all data cells, especially the Summary column
   - Set appropriate column widths:
     - Client Name: ~180 pixels
     - Project Code: ~100 pixels
     - Project Name: ~250 pixels
     - Status: ~120 pixels
     - Project Summary: ~500 pixels
   - Apply conditional formatting to the Status column:
     - UAT → Yellow fill
     - Project Closed → Green fill
     - Development → Blue fill
     - PO Received → Purple fill

4. **Data Processing:**
   - Only include rows where Client Name is not empty
   - Filter out any header rows or blank rows from the source
   - Process all projects (typically 15-50 rows depending on filtered data)

**Example Output:**

| Client Name | Project Code | Project Name | Status | Project Summary (AI) |
|-------------|--------------|--------------|--------|---------------------|
| Bank1 Limited | PPD3816 | eAPY account opening... | UAT | UAT testing is in progress for eAPY account opening integration. Currently connecting for deployment and testing PRAN modification issues related to FATCA details fetching. |
| Bank2 Limited | PPD4441 | IDAM Integration... | Project Closed | Project successfully deployed to production and is now live as of June 9, 2026. APPSEC review was completed, followed by UAT sign-off and final deployment. |

**Key Notes:**
- The dated entries follow patterns like `[09-july-2026]:` or `(2-May-2025):` followed by the update text
- Multiple entries are separated by newlines within the same cell
- The AI summary should synthesize information, not just concatenate entries
- Focus on actionable insights: what's the current blocker, what's the next step, what milestone was recently achieved
```
---

This prompt should give you the complete Status Summary sheet with AI-generated summaries in a single request, without needing follow-up prompts for consolidation or AI summarization.
