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
- **Context**: Extract 1-2 sentences before and after the highlight for context
- **Page**: Look for page numbers (usually at bottom/top corners of the page, or in page header/footer)
- **Thoughts/Annotations**: Identify handwritten notes in margins, between lines, or sticky notes
- **Chapter name**: Look for chapter headings (usually larger/bolder font, or starts with "Chapter X" or "第X章")
- **Book name**: Check for book title (usually on top of page, header, or cover if visible)

### Step 2: Determine Book Information

1. **Book name**:
   - If the book name is visible in the image → Use the book name from the image (usually in header, footer, or on cover)
   - If the book name is not in the image → Scan the last 10 messages in the conversation to find if a book name was mentioned
   - If no book name in conversation context → **Must ask the user**: "What's the book name for these notes?"

2. **Chapter name** (optional):
   - If chapter name is visible in the image → Use the chapter name from the image
   - If chapter name is not in the image → Scan the last 10 messages in conversation
   - **No longer required to ask the user**. If neither image nor conversation has chapter info, leave it blank.

3. **Book info** (optional):
   - Do not proactively ask for author, genre, summary, etc.
   - If user voluntarily provides this info, record it in the file header

### Step 3: Create/Update Notes File

1. Notes file path: `readings/{book_name}_notes.md`
2. Book name can be in Chinese or English
3. If file already exists, append new highlights
4. If file doesn't exist, create a new file

**Reading period rules**:
- **Start date**: Date when the notes file for this book is first created (yyyy-mm-dd format)
- **End date**: Date when the notes file is last updated (yyyy-mm-dd format, update each time)

### Step 4: Formatting Rules

**Core principle: Highlights must be transcribed exactly as written**

Format for each highlight:
```markdown
- ***{original highlight}***
  - Page: {page number}
  - Chapter: {chapter name, if available}
  - Thoughts: {user's annotation, if available}
```

**Context addition rules (optional)**:
- If sentences before/after the highlight help understand the meaning, you may add them selectively
- Format: Add a new line after the highlight, start with "> " to quote the context
- Example:
```markdown
- ***{original highlight}***
  - Page: 45
  - Chapter: Chapter 5
  - Thoughts: User's annotation
> Context: Previous or next sentence that helps understand the highlight
```

Notes:
- **Highlights must be transcribed exactly** including punctuation, spaces, and capitalization
- Highlights must use `***bold italic***` format
- Page is taken directly from the image
- Chapter is optional - record if available, leave blank if not
- Thoughts are not mandatory - only record if user provides them
- **Insights can only be recorded by the user - AI should not generate them**

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
- ***{Highlight 1}***
  - Page:
  - Chapter:
  - Thoughts:
- ***{Highlight 2}***
- ***{Highlight 3}***
  - Page:
  - Chapter:
  - Thoughts:


# Insights

```

## Key Rules

1. **Must ask when**:
   - No book name in image or conversation context → Ask the user

2. **No longer required to ask**:
   - Chapter name is no longer mandatory - leave blank if not found in image or context

3. **Do not proactively ask**:
   - Book info (author, genre, summary, etc.) is not mandatory - do not proactively ask

4. **Formatting requirements**:
   - **Highlights must be transcribed exactly** (including punctuation, spaces, capitalization)
   - Each highlight uses `***text***` format (bold + italic)
   - Page must be extracted from image
   - Chapter is optional
   - Thoughts only recorded when user provides them

5. **Context addition rules**:
   - Only add context when it helps understand the highlight
   - Not every highlight needs context
   - Use "> " format for context

6. **Insights rules**:
   - AI should not proactively generate insights
   - Only record when user explicitly provides them

## Error Handling

### Image unclear or unreadable
- If the image is blurry, dark, or text is illegible → Inform user: "The image quality is insufficient to read the text clearly. Could you please retake the photo with better lighting and focus?"

### Missing required information
- If book name is missing and not found in conversation context → Ask: "What's the book name for these notes?"
- If chapter name is missing → No longer ask. Leave it blank in the notes.

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
2. Check conversation history for book name → Not found
3. Ask user: "What's the book name for these notes?"
4. User replies: "Pride and Prejudice"
5. Create/update `readings/Pride-and-Prejudice_notes.md` with:
```markdown
- ***It is a truth universally acknowledged...***
  - Page: 12
  - Chapter: Chapter 1
  - Thoughts: Classic opening line
```
6. Confirm to user: "Successfully recorded your notes from Pride and Prejudice, Page 12."

### Example 2: User sends photo (no chapter)
User: [sends photo with highlighted text but no chapter visible]

Actions:
1. Analyze the image:
   - Identify highlighted text: "Money is a story"
   - Find page number: 88
   - No chapter visible
2. Check conversation history → User previously mentioned book name "The Psychology of Money"
3. No need to ask for chapter (optional now)
4. Update `readings/The-Psychology-of-Money_notes.md`:
```markdown
- ***Money is a story***
  - Page: 88
  - Chapter:
  - Thoughts:
```
5. Confirm to user: "Successfully recorded your notes from The Psychology of Money, Page 88."

### Example 3: Adding context selectively
User: [sends photo with highlight and surrounding text that provides important context]

Actions:
1. Analyze the image:
   - Highlight: "The key is to think of money as"
   - Page: 156
   - Surrounding text: "not as a thing but as a story we tell ourselves"
   - The context explains the highlight well
2. Add context since it's relevant:
```markdown
- ***The key is to think of money as***
  - Page: 156
  - Chapter:
  - Thoughts:
> Context: not as a thing but as a story we tell ourselves
```

### Example 4: Multiple highlights on same page
User: [sends photo with 3 different highlighted sections on page 45]

Actions:
1. Extract all 3 highlights separately
2. Record them all with the same page number
3. If any have chapter info, record it; if not, leave blank
4. Each highlight maintains its own formatting with page number
