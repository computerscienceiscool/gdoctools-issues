Yes. `underline` is part of `TextRun.textStyle`, but it is easy to miss for the same reason as font family and color: **inherited styles are often omitted from the response**. Google’s Docs API says inherited text-style fields are represented as unset, and character formatting like underline is part of text styling on the text run. ([Google for Developers][1])

The field you want is:

* `paragraph.elements[].textRun.textStyle.underline` ([Google for Developers][1])

A run with explicit underline can look like this:

```json
{
  "paragraph": {
    "elements": [
      {
        "textRun": {
          "content": "underlined text",
          "textStyle": {
            "underline": true
          }
        }
      }
    ]
  }
}
```

But if the run is inheriting its formatting, you may see this instead:

```json
{
  "textRun": {
    "content": "underlined text",
    "textStyle": {}
  }
}
```

That does **not necessarily mean** the text is not underlined. It can mean the run is inheriting from its parent style rather than overriding it directly. Google documents that inherited text styles are represented as unset fields. ([Google for Developers][2])

There are four common reasons you might not see `underline`:

1. **It is inherited rather than explicitly set on the run.**
   The Docs API inheritance chain goes from the run to the paragraph’s named style, then to Normal Text, then to the editor default. ([Google for Developers][1])

2. **Your `fields=` mask excludes it.**
   If you are using a partial response on `documents.get`, you must include `textRun.textStyle.underline` or the API will not return it. ([Google for Developers][3])

3. **You are reading suggested changes separately from the base style.**
   If underline is being suggested rather than accepted, it may appear under `suggestedTextStyleChanges` instead of the base `textStyle`. ([Google for Developers][4])

4. **You are checking the paragraph instead of the text run.**
   Underline is character formatting, so it belongs on `TextStyle`, not `ParagraphStyle`. ([Google for Developers][5])

A good `fields` mask for debugging is:

```text
tabs(documentTab(body(content(paragraph(elements(textRun(content,textStyle(underline,bold,italic,strikethrough,weightedFontFamily,fontSize,foregroundColor,backgroundColor))))))),documentTab(namedStyles))
```

Or if you are not using tabs:

```text
body(content(paragraph(elements(textRun(content,textStyle(underline,bold,italic,strikethrough))))))
,namedStyles
```

If you want the **effective underline**, not just explicit overrides, your parser should do this:

1. Check `textRun.textStyle.underline`
2. If absent, check the paragraph’s `namedStyleType`
3. Look up that style in `namedStyles.styles[]`
4. If still absent, treat it as inherited from Normal Text / editor default ([Google for Developers][1])

One more practical detail: if you are seeing `bold`, `italic`, and `strikethrough` but not `underline`, that usually means one of two things:

* the document text is not actually underlined at the API level for those runs, or
* your parser is only looking at explicit run overrides and not inherited values.

If you paste a small sample of the JSON you are getting back for one underlined run, I can help pinpoint exactly why `underline` is missing.

[1]: https://developers.google.com/workspace/docs/api/reference/rest/v1/documents?utm_source=chatgpt.com "REST Resource: documents | Google Docs | Google for Developers"
[2]: https://developers.google.com/resources/api-libraries/documentation/docs/v1/java/latest/com/google/api/services/docs/v1/model/TextStyle.html?utm_source=chatgpt.com "TextStyle (Google Docs API v1 (Rev. 61) 1.25.0)"
[3]: https://developers.google.com/workspace/docs/api/reference/rest?utm_source=chatgpt.com "Google Docs API | Google for Developers"
[4]: https://developers.google.com/resources/api-libraries/documentation/docs/v1/java/latest/com/google/api/services/docs/v1/model/TextRun.html?utm_source=chatgpt.com "TextRun (Google Docs API v1 (Rev. 61) 1.25.0)"
[5]: https://developers.google.com/workspace/docs/api/how-tos/format-text?utm_source=chatgpt.com "Format Text | Google Docs | Google for Developers"
