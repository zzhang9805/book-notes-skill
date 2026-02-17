# Book Notes

An AI agent skill that automatically extracts and organizes book highlights, annotations, and notes from photos into markdown format.

## Overview

Book Notes helps you digitize your book reading notes by analyzing photos of highlighted passages, handwritten margin notes, and annotations. It automatically extracts the highlighted text, page numbers, chapter names, and any handwritten thoughts, then organizes everything into clean markdown files.

## How It Works

1. **Analyze** - You send a photo of your book notes (highlights, annotations, margin notes)
2. **Extract** - The skill identifies highlighted text, page numbers, chapters, and handwritten notes
3. **Organize** - Creates or updates a markdown file with all your highlights in a consistent format

## Installation

### Option 1: Tell Your Agent to Install

Simply tell your AI agent (Claude, GPT, Cursor, etc.):

```
Install the book-notes skill from https://github.com/zzhang9805/book-notes-skill
```

The agent should:
1. Clone this repository
2. Copy the `book-notes/` folder (containing `SKILL.md`) to its skills directory
3. Restart or reload to activate

### Option 2: Manual Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/zzhang9805/book-notes-skill.git
   ```

2. Copy the `book-notes/` folder (containing `SKILL.md`) to your agent's skills directory:
   - **Claude Code**: `~/.claude/skills/book-notes/`
   - **Cursor**: Check Cursor's plugins/skills directory
   - **OpenCode**: `~/.agents/skills/book-notes/`
   - **Other agents**: Check your agent's documentation for the skills directory location

   The resulting path should look like: `{skills-dir}/book-notes/SKILL.md`

3. Restart your agent or start a new session

## Usage

Trigger the skill by:
- Sending a photo of book highlights/annotations
- Asking: "record book notes", "save my reading notes", "process my book highlights"
- Using Chinese: "笔记", "记录读书笔记", or "保存书摘"

The skill will:
1. Analyze the image for highlighted text, page numbers, and chapter info
2. Ask for the book name if not found in image or conversation
3. Create or update `readings/{book_name}_notes.md`

## Features

- **Automatic extraction** - Identifies highlighted text, bold/underlined passages
- **Page tracking** - Records page numbers from the photo
- **Chapter detection** - Finds chapter headings when visible
- **Annotation capture** - Extracts handwritten margin notes and thoughts
- **Context preservation** - Optionally includes surrounding sentences for better understanding
- **Organized storage** - Creates clean markdown files per book

## File Format

Notes are saved in `readings/{book_name}_notes.md`:

```markdown
# Book info
- Author
- Genre
- Abstract
- Reading period
  - Start date: 2026-02-17
  - End date: 2026-02-17

# Highlights
- ***Highlight text here***
  - Page: 45
  - Chapter: Chapter 5
  - Thoughts: Your annotation

> Context: Optional surrounding text (when relevant)

# Insights

```

## Key Rules

- **Exact transcription** - Highlights are copied word-for-word including punctuation
- **Optional fields** - Chapter and thoughts are recorded only when available
- **No AI insights** - Only record insights explicitly provided by user
- **Selective context** - Add surrounding text only when it helps understanding

## Error Handling

- **Unclear image** - Prompts user to retake with better lighting
- **Missing book name** - Asks user for the book title
- **Permission issues** - Informs user of file access problems

## Example

You: [sends photo of book page with highlighted text "It is a truth universally acknowledged..." and handwritten note "Classic opening line"]

Skill: "What's the book name for these notes?"

You: "Pride and Prejudice"

Skill: Creates `readings/Pride-and-Prejudice_notes.md` with:
```markdown
- ***It is a truth universally acknowledged...***
  - Page: 12
  - Chapter: Chapter 1
  - Thoughts: Classic opening line
```

## License

MIT License

## Support

For issues or suggestions, please open an issue on the GitHub repository.
