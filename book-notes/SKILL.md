---
name: book-notes
description: Record book reading notes from photos. Use when user sends a photo of book highlights/annotations, or explicitly asks to "record book notes", "save my reading notes", "process my book highlights", "笔记", "记录读书笔记", or "保存书摘".
metadata:
  author: zhaoruizhang
  version: 1.1.0
  created: 2026-02-17
  tags: [reading, notes, productivity, books]
---

# Book Notes

## Overview

Automatically identify highlighted text, annotations, page numbers, and chapter information from user-submitted book notes photos, and record them in markdown format under the readings folder.

## Workflow

### Step 1: Analyze the Image

Scan the user's book notes photo and extract the following information:
- **Original highlight**: Look for underlined text, highlighted sections (usually in yellow/pink), or bold text
- **Context**: Extract the 2 sentences BEFORE the highlight AND the 2 sentences AFTER the highlight for context (total 4 sentences)
- **Page**: Look for page numbers (usually at bottom/top corners of the page, or in page header/footer)
- **Thoughts/Annotations**: Identify handwritten notes in margins, between lines, or sticky notes
- **Book name**: Check for book title (usually on top of page, header, or on cover if visible)
- **Chapter name**: Only record if user explicitly mentions it (do not extract from image or ask proactively)

### Step 2: Determine Book Information

1. **Book name**:
   - If the book name is visible in the image → Use the book name from the image (usually in header, footer, or on cover)
   - If the book name is not in the image → Scan the last 10 messages in the conversation to find if a book name was mentioned
   - If no book name in conversation context → **Must ask the user**: "What's the book name for these notes?"

2. **Chapter name**:
   - **Only record when user explicitly provides it** (mentions in conversation or annotation)
   - **Do NOT extract chapter from image**
   - **Do NOT ask user for chapter**
   - Leave blank if user doesn't mention it

3. **Book info** (optional):
   - Do not proactively ask for author, genre, summary, etc.
   - If user voluntarily provides this info, record it in the file header

### Step 3: Create/Update Notes File

1. Notes file path: `readings/{book_name}_notes.md`
2. Book name can be in Chinese or English
3. If file already exists, append new highlights
4. If file doesn't exist, create a new file with the format template

**Reading period rules**:
- **Start date**: Date when the notes file for this book is first created (yyyy-mm-dd format)
- **End date**: Date when the notes file is last updated (yyyy-mm-dd format, update each time)

**New file requirements**:
- Create the file with the format template structure
- No format explanation comment needed

### Step 4: Formatting Rules

**Core principle: Highlights must be transcribed exactly as written**

Format for each highlight:
```markdown
- {2 sentences before}<u>{original highlight}</u>{2 sentences after}
  - Page: {page number}
  - Chapter: {chapter name, if available}
  - Thoughts: {user's annotation, if available}
```

Notes:
- **Highlights must be transcribed exactly** including punctuation, spaces, and capitalization
- **Context is merged with highlight**: 2 sentences before + <u>highlight</u> + 2 sentences after
- **Only the highlight itself uses `<u>text</u>` format** (underline)
- **Context (before and after) uses plain text** (no formatting)

## File Format Template

Reference the template format provided by the user:

```markdown
# Book info
- Author
- Genre
- Abstract
- Reading period
  - Start date: 2026-02-17
  - End date: 2026-02-17
# Highlights

- 前一句前一句<u>{高亮句子}</u>后一句后一句
  - Page:
  - Chapter:
  - Thoughts:
- 前一句前一句<u>{高亮句子}</u>后一句后一句
  - Page:
  - Chapter:
  - Thoughts:


# Insights

```

## Key Rules

1. **Must ask when**:
   - No book name in image or conversation context → Ask the user

2. **Chapter handling**:
   - Only record chapter when user explicitly provides it
   - Do NOT extract chapter from image
   - Do NOT ask user for chapter
   - Leave blank if not mentioned by user

3. **Do not proactively ask**:
   - Book info (author, genre, summary, etc.) is not mandatory - do not proactively ask

4. **Formatting requirements**:
   - **Highlights must be transcribed exactly** (including punctuation, spaces, capitalization)
   - **Format: `{context before}<u>highlight</u>{context after}`**
   - Only the highlight itself uses `<u>text</u>` format (underline)
   - Context uses plain text (no formatting)
   - Page must be extracted from image
   - Chapter only when user explicitly mentions it
   - Thoughts only recorded when user provides them

5. **Context is mandatory**:
   - Always extract 2 sentences before the highlight
   - Always extract 2 sentences after the highlight
   - Merge context directly into the highlight text

6. **Insights rules**:
   - AI should not proactively generate insights
   - Only record when user explicitly provides them

## Error Handling

### Image unclear or unreadable
- If the image is blurry, dark, or text is illegible → Inform user: "The image quality is insufficient to read the text clearly. Could you please retake the photo with better lighting and focus?"

### Missing required information
- If book name is missing and not found in conversation context → Ask: "What's the book name for these notes?"
- Chapter is NOT required - only record when user explicitly provides it

### File system issues
- If `readings/` folder doesn't exist → Create it first using `mkdir -p readings/`
- If file is locked or permission denied → Inform user: "Unable to save the notes due to file access issue. Please check permissions."

### Multiple highlights on same page
- Extract each highlight separately
- Group them under the same chapter heading
- Maintain page number for each highlight

## Examples

### Example 1: User sends a book highlight photo (with chapter)
User: [sends photo of a book page with highlighted text and handwritten margin notes]

Actions:
1. Analyze the image:
   - Identify highlighted text: "It is a truth universally acknowledged..."
   - Find page number: 12
   - Identify chapter: "Chapter 1"
   - Extract handwritten note: "Classic opening line"
   - Extract 2 sentences before: "Mr. Bennet was among the earliest of those who waited on Mr. Bingley."
   - Extract 2 sentences after: "that a man in possession of a good fortune, must be in want of a wife."
2. Check conversation history for book name → Not found
3. Ask user: "What's the book name for these notes?"
4. User replies: "Pride and Prejudice"
5. Create/update `readings/Pride-and-Prejudice_notes.md` with:
```markdown
# Pride and Prejudice
- Author:
- Genre:
- Abstract:
- Reading period
  - Start date: 2026-02-19
  - End date: 2026-02-19
# Highlights

- Mr. Bennet was among the earliest of those who waited on Mr. Bingley.<u>It is a truth universally acknowledged...</u>that a man in possession of a good fortune, must be in want of a wife.
  - Page: 12
  - Chapter: (only if user explicitly mentions)
  - Thoughts: Classic opening line

# Insights

```
6. Confirm to user: "Successfully recorded your notes from Pride and Prejudice, Page 12."

### Example 2: User sends photo (no chapter mentioned)
User: [sends photo with highlighted text]

Actions:
1. Analyze the image:
   - Identify highlighted text: "Money is a story"
   - Find page number: 88
   - Extract 2 sentences before: "The book is about the soft side of money"
   - Extract 2 sentences after: "that explains why people make irrational decisions about money"
2. Check conversation history → User previously mentioned book name "The Psychology of Money"
3. Do NOT extract chapter from image, do NOT ask for chapter
4. Update `readings/The-Psychology-of-Money_notes.md` (file already exists, append to existing highlights):
```markdown
- The book is about the soft side of money<u>Money is a story</u>that explains why people make irrational decisions about money
  - Page: 88
  - Chapter:
  - Thoughts:
```
5. Confirm to user: "Successfully recorded your notes from The Psychology of Money, Page 88."

### Example 3: User mentions chapter explicitly
User: [sends photo with highlighted text] "This is from Chapter 5 about money psychology"

Actions:
1. Analyze the image:
   - Highlight: "The key is to think of money as"
   - Page: 156
   - 2 sentences before: "Many people think about money as a thing"
   - 2 sentences after: "not as a thing but as a story we tell ourselves"
2. User explicitly mentioned "Chapter 5" → Record it
3. Record with context and chapter:
```markdown
- Many people think about money as a thing<u>The key is to think of money as</u>not as a thing but as a story we tell ourselves
  - Page: 156
  - Chapter: Chapter 5
  - Thoughts:
```

### Example 4: Multiple highlights on same page
User: [sends photo with 3 different highlighted sections on page 45]

Actions:
1. Extract all 3 highlights separately
2. For each highlight, extract 2 sentences before and 2 sentences after
3. Record them all with the same page number
4. Chapter only recorded if user explicitly mentions it
5. Each highlight maintains its own formatting with page number and context
