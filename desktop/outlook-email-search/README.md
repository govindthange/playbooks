# What is the syntax for searching emails on Outlook's search box?

The syntax is based on **Advanced Query Search (AQS)**. Here is a comprehensive list of common filters and how to combine them.

### Basic Syntax Rules

* **Keywords:** Use specific terms like `from:`, `subject:`, `category:`.
* **Case Sensitivity:** Keywords like `from:` and operators like `AND` are **not** case-sensitive, but using uppercase for operators is a common practice for readability.
* **Spaces:** Don't put a space between the keyword and the colon (e.g., `from:`, not `from: `).
* **Quotes:** Use double quotes `""` for phrases or category names that contain spaces (e.g., `"Project Alpha"`).
* **Default Operator:** A space between two keywords or phrases acts as an `AND` operator.

### Logical Operators

Use these to combine or exclude search terms. They must be in **uppercase**.

* `AND`: Includes results that contain **all** specified terms.
    * *Syntax:* `keyword1 AND keyword2`
    * *Example:* `project AND report` (Finds emails with both "project" and "report")

* `OR`: Includes results that contain **at least one** of the specified terms.
    * *Syntax:* `keyword1 OR keyword2`
    * *Example:* `invoice OR receipt` (Finds emails with either "invoice" or "receipt")

* `NOT` or `-`: **Excludes** results that contain a specific term. This is useful for filtering out unwanted emails.
    * *Syntax:* `keyword1 NOT keyword2` or `keyword1 -keyword2`
    * *Example:* `report NOT "Q1"` (Finds emails with "report" but not "Q1")

* `()`: Use parentheses to group clauses and control the order of operations.
    * *Example:* `(from:sales@example.com OR from:marketing@example.com) AND subject:update` (Finds emails from either sales or marketing that also have "update" in the subject.)

### Search Filters (Keywords)

#### People-Related
* **`from:`**: Searches the sender field.
    * *Syntax:* `from:name` or `from:"Full Name"`
    * *Example:* `from:sarah` or `from:"Sarah Connor"`

* **`to:`**: Searches the primary recipient field.
    * *Syntax:* `to:name` or `to:"Full Name"`
    * *Example:* `to:finance` or `to:"Finance Team"`

#### Content and Subject
* **`subject:`**: Searches only the subject line.
    * *Syntax:* `subject:keyword` or `subject:"keyword phrase"`
    * *Example:* `subject:report` or `subject:"quarterly review"`

* **`category:`**: Searches for emails with a specific category (label).
    * *Syntax:* `category:"Category Name"`
    * *Example:* `category:Urgent` or `category:"Finance Reports"`

#### Date and Time
* **`received:`**: Searches by the date an email was received.
    * *Syntax:* `received:date` or `received:"date range"`
    * *Examples for **specific dates** and **ranges**:*
        * `received:09/25/2025` (On a specific date)
        * `received:09/01/2025..09/07/2025` (A specific date range)
    * *Examples for **relative dates**:*
        * `received:today`
        * `received:yesterday`
        * `received:this week`
        * `received:last month`
        * `received:this year`

#### Status and Attributes
* **`hasattachment:`** or **`has:`**: Filters for emails with attachments.
    * *Syntax:* `hasattachment:yes`
    * *Example:* `hasattachment:yes` (Finds all emails with an attachment)

* **`is:`** or **`readstatus:`**: Filters by read or unread status.
    * *Syntax:* `is:unread` or `is:read`
    * *Example:* `is:unread`

### Combined Search Examples

Here's how to use various combinations of the above filters:

| Goal | Syntax Example | Explanation |
| :--- | :--- | :--- |
| **With multiple categories and a date range**| `category:"Urgent" AND category:"Client Work" AND received:09/01/2025..09/30/2025` | Finds emails with both "Urgent" and "Client Work" categories that were received in September 2025. |
| **With an attachment from a specific sender** | `from:marketing@example.com AND hasattachment:yes` | Finds emails from the marketing team that contain an attachment. |
| **From a sender with specific subject keywords** | `from:"John Doe" AND subject:project` | Finds emails from "John Doe" with the word "project" in the subject. |
| **With specific words but excluding others** | `report AND received:last month NOT draft` | Finds emails with "report" received last month, but excludes any that also have the word "draft." |
| **Subject from a sender, but not another**| `subject:"Quarterly Report" AND from:finance@example.com -from:john@example.com` | Finds quarterly reports from the finance department, but excludes those from "john." |
| **Find unread emails from a specific person** | `from:sarah AND is:unread` | Finds all unread emails from "sarah." |
