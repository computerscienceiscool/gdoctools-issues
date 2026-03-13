Yes. Below is your content converted into **clean Markdown tables** so it will render properly in Markdown viewers, GitHub, or documentation systems.

---

# Google Doc Content Features vs APIs

| Feature                                   | Accessible via API | API                            |
| ----------------------------------------- | ------------------ | ------------------------------ |
| Title / Subtitle / Headings               | Yes                | Google Docs API                |
| Normal text paragraphs                    | Yes                | Google Docs API                |
| Bold / italic / underline / strikethrough | Yes                | Google Docs API                |
| Superscript / subscript                   | Yes                | Google Docs API                |
| Font family / size / color                | Yes                | Google Docs API                |
| Highlight color                           | Yes                | Google Docs API                |
| Paragraph alignment                       | Yes                | Google Docs API                |
| Line spacing / paragraph spacing          | Yes                | Google Docs API                |
| Indentation                               | Yes                | Google Docs API                |
| Blockquotes / paragraph borders           | Yes                | Google Docs API                |
| Bulleted lists                            | Yes                | Google Docs API                |
| Numbered lists                            | Yes                | Google Docs API                |
| Nested lists                              | Yes                | Google Docs API                |
| Checklists                                | Partial            | Google Docs API                |
| Tables                                    | Yes                | Google Docs API                |
| Merge / split table cells                 | Yes                | Google Docs API                |
| Table row / column insertion              | Yes                | Google Docs API                |
| Table header rows                         | Yes                | Google Docs API                |
| Table cell styles                         | Yes                | Google Docs API                |
| Columns (multi-column layout)             | Yes                | Google Docs API                |
| Page breaks                               | Yes                | Google Docs API                |
| Section breaks                            | Yes                | Google Docs API                |
| Headers                                   | Yes                | Google Docs API                |
| Footers                                   | Yes                | Google Docs API                |
| Page numbers                              | Yes                | Google Docs API                |
| Text watermarks                           | Partial            | Google Docs API                |
| Image watermarks                          | Partial            | Google Docs API                |
| Horizontal line                           | Yes                | Google Docs API                |
| Smart separators                          | Yes                | Google Docs API                |
| Inline images                             | Yes                | Google Docs API                |
| Positioned images                         | Yes                | Google Docs API                |
| Image replacement                         | Yes                | Google Docs API                |
| Image crop / transform                    | Yes                | Google Docs API                |
| Drawings                                  | Partial            | Google Docs API                |
| Charts linked to Sheets                   | Partial            | Google Docs API + Sheets API   |
| Embedded Sheets tables                    | Partial            | Google Docs API + Sheets API   |
| Diagrams (add-ons)                        | No                 | External add-ons               |
| Hyperlinks                                | Yes                | Google Docs API                |
| Internal bookmarks                        | Yes                | Google Docs API                |
| Links to headings                         | Yes                | Google Docs API                |
| Rich link previews                        | No                 | Not exposed                    |
| Footnotes                                 | Yes                | Google Docs API                |
| Endnotes                                  | Partial            | Google Docs API                |
| Citations                                 | Partial            | Google Docs API                |
| Equations                                 | Yes                | Google Docs API                |
| Special characters                        | Yes                | Google Docs API                |
| Table of contents                         | Yes                | Google Docs API                |
| Bookmarks                                 | Yes                | Google Docs API                |
| Named ranges                              | Yes                | Google Docs API                |
| Comments                                  | Yes                | Google Drive API               |
| Comment replies                           | Yes                | Google Drive API               |
| Comment mentions                          | Yes                | Google Drive API               |
| Suggested edits                           | Yes                | Google Docs API                |
| Emoji reactions                           | No                 | Not exposed                    |
| Smart chips (people)                      | Yes                | Google Docs API                |
| Smart chips (files)                       | Yes                | Google Docs API                |
| Smart chips (date)                        | Partial            | Google Docs API                |
| Smart chips (calendar events)             | Partial            | Google Docs API + Calendar API |
| Smart chips (finance / stock)             | No                 | Not exposed                    |
| Dropdown chips                            | Partial            | Google Docs API                |
| Variable chips                            | Partial            | Google Docs API                |
| Meeting notes building block              | Partial            | Google Docs API                |
| Email draft building block                | Partial            | Google Docs API                |
| Code block building block                 | Partial            | Google Docs API                |
| AI Summary block                          | No                 | Gemini (not exposed via API)   |
| AI-generated text                         | No                 | Gemini                         |
| AI-generated images                       | No                 | Gemini                         |

---

# Google Docs Feature → Docs API JSON Object → Fields to Parse

| Google Docs Feature                       | Docs API JSON Object                                               | Fields to Parse                                                                                             |
| ----------------------------------------- | ------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------- |
| Document tabs                             | Document.tabs[]; Tab; DocumentTab                                  | tabs[].tabProperties.title; tabs[].tabProperties.tabId; tabs[].childTabs[]; tabs[].documentTab.body.content |
| Body content root                         | Body                                                               | content[]                                                                                                   |
| Normal paragraph text                     | StructuralElement.paragraph → Paragraph → ParagraphElement.textRun | paragraph.elements[].textRun.content                                                                        |
| Title / Subtitle / Heading 1–6            | ParagraphStyle                                                     | paragraph.paragraphStyle.namedStyleType                                                                     |
| Bold / italic / underline / strikethrough | TextRun.textStyle                                                  | bold; italic; underline; strikethrough                                                                      |
| Superscript / subscript                   | TextRun.textStyle                                                  | baselineOffset                                                                                              |
| Font family                               | TextRun.textStyle                                                  | weightedFontFamily.fontFamily                                                                               |
| Font size                                 | TextRun.textStyle                                                  | fontSize.magnitude; fontSize.unit                                                                           |
| Text color                                | TextRun.textStyle                                                  | foregroundColor.color.rgbColor                                                                              |
| Highlight color                           | TextRun.textStyle                                                  | backgroundColor.color.rgbColor                                                                              |
| Hyperlinks                                | TextRun.textStyle.link                                             | link.url; link.bookmarkId; link.headingId; link.tabId                                                       |
| Paragraph alignment                       | ParagraphStyle                                                     | alignment                                                                                                   |
| Line spacing                              | ParagraphStyle                                                     | lineSpacing                                                                                                 |
| Paragraph spacing                         | ParagraphStyle                                                     | spaceAbove; spaceBelow                                                                                      |
| Indentation                               | ParagraphStyle                                                     | indentStart; indentEnd; indentFirstLine                                                                     |
| Paragraph borders                         | ParagraphStyle                                                     | borderTop; borderBottom; borderLeft; borderRight; borderBetween                                             |
| Paragraph shading                         | ParagraphStyle.shading                                             | shading.backgroundColor.color.rgbColor                                                                      |
| Bulleted list item                        | Paragraph.bullet                                                   | bullet.listId; bullet.nestingLevel                                                                          |
| Numbered list item                        | Paragraph.bullet + Document.lists                                  | paragraph.bullet.listId; lists[listId].listProperties                                                       |
| Nested lists                              | Paragraph.bullet + Document.lists                                  | bullet.nestingLevel                                                                                         |
| Tables                                    | StructuralElement.table → Table                                    | tableRows[]; columns; tableStyle                                                                            |
| Table row                                 | TableRow                                                           | tableCells[]; tableRowStyle                                                                                 |
| Table cell                                | TableCell                                                          | content[]; tableCellStyle                                                                                   |
| Table borders                             | TableCellStyle                                                     | borderTop; borderBottom; borderLeft; borderRight                                                            |
| Table cell background                     | TableCellStyle                                                     | backgroundColor.color.rgbColor                                                                              |
| Table of contents                         | StructuralElement.tableOfContents                                  | tableOfContents.content[]                                                                                   |
| Section break                             | StructuralElement.sectionBreak                                     | sectionStyle                                                                                                |
| Header                                    | Header                                                             | headerId; content[]                                                                                         |
| Footer                                    | Footer                                                             | footerId; content[]                                                                                         |
| Page break                                | ParagraphElement.pageBreak                                         | pageBreak element                                                                                           |
| Footnote reference                        | ParagraphElement.footnoteReference                                 | footnoteId                                                                                                  |
| Footnote content                          | Document.footnotes → Footnote                                      | footnotes[footnoteId].content[]                                                                             |
| Named ranges / bookmarks                  | NamedRange                                                         | namedRangeId; name; ranges[]                                                                                |
| Inline image                              | ParagraphElement.inlineObjectElement → InlineObject                | inlineObjectId; inlineObjects[inlineObjectId].inlineObjectProperties.embeddedObject                         |
| Inline image alt text                     | EmbeddedObject                                                     | title; description                                                                                          |
| Inline image size                         | EmbeddedObject                                                     | size.height; size.width                                                                                     |
| Floating image                            | PositionedObject                                                   | positionedObjectId; positionedObjectProperties                                                              |
| Linked Sheets chart                       | EmbeddedObject.linkedContentReference                              | linkedContentReference                                                                                      |
| Horizontal rule                           | ParagraphElement.horizontalRule                                    | horizontalRule element                                                                                      |
| Equation                                  | ParagraphElement.equation                                          | equation element                                                                                            |
| Auto text (page numbers etc.)             | ParagraphElement.autoText                                          | type; style                                                                                                 |
| Suggested text style changes              | TextRun                                                            | suggestedTextStyleChanges                                                                                   |
| Suggested paragraph style changes         | ParagraphStyle                                                     | suggestedParagraphStyleChanges                                                                              |
| Document-wide styles                      | DocumentStyle; NamedStyles                                         | default margins; background; named style definitions                                                        |
| Named style definitions                   | NamedStyles                                                        | namedStyles.styles[]                                                                                        |
| Index positions for parsing               | StructuralElement; ParagraphElement                                | startIndex; endIndex                                                                                        |

---

If you'd like, I can also produce a **much cleaner version specifically for specs**, where:

* the tables are **shorter**
* the features are **grouped logically**
* the API rows match **exact Docs API object hierarchy**

That version is usually much easier for engineers to read.
