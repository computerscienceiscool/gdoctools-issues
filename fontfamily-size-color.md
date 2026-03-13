Yes — it **is** exposed, but there are two common reasons it looks like it is not:

1. **The style is inherited**, so the `textRun.textStyle` object may omit `weightedFontFamily`, `fontSize`, or `foregroundColor` unless that run overrides the inherited value. Google’s docs explicitly say text style can inherit from the paragraph’s named style, named styles can inherit from “normal text,” and normal text can inherit from the editor default. ([Google for Developers][1])

2. You may be looking at the wrong level. Font family, size, and color are not top-level paragraph fields; they live on **`ParagraphElement.textRun.textStyle`** in the Docs API response. The formatting guide and REST reference both describe character formatting that way. ([Google for Developers][2])

Here is the key mapping:

* **Font family** → `textRun.textStyle.weightedFontFamily.fontFamily`
* **Font size** → `textRun.textStyle.fontSize.magnitude` and `textRun.textStyle.fontSize.unit`
* **Text color** → `textRun.textStyle.foregroundColor.color.rgbColor`
* **Highlight color** → `textRun.textStyle.backgroundColor.color.rgbColor` ([Google for Developers][1])

A typical read path is:

`document.body.content[].paragraph.elements[].textRun.textStyle`

Example JSON shape:

```json
{
  "paragraph": {
    "elements": [
      {
        "textRun": {
          "content": "Hello",
          "textStyle": {
            "weightedFontFamily": {
              "fontFamily": "Arial"
            },
            "fontSize": {
              "magnitude": 11,
              "unit": "PT"
            },
            "foregroundColor": {
              "color": {
                "rgbColor": {
                  "red": 1,
                  "green": 0,
                  "blue": 0
                }
              }
            }
          }
        }
      }
    ]
  }
}
```

What often confuses people is this: if the text is just using the document’s normal inherited style, the run may come back more like this:

```json
{
  "textRun": {
    "content": "Hello",
    "textStyle": {}
  }
}
```

That does **not** mean the text has no font, size, or color. It usually means those values are inherited rather than explicitly set on that run. The Docs reference calls out that inheritance behavior directly. ([Google for Developers][1])

To recover the effective value, you often need to check in this order:

1. `paragraph.elements[].textRun.textStyle`
2. the paragraph’s named style via `paragraph.paragraphStyle.namedStyleType`
3. `document.namedStyles`
4. editor defaults / implicit Docs defaults ([Google for Developers][1])

So in practice:

* If `weightedFontFamily` is present on the run, use it.
* Otherwise, look up the paragraph’s `namedStyleType`.
* Then inspect the matching entry in `document.namedStyles.styles[]`.
* If it is still absent, the value is inherited from Normal Text or the editor default. ([Google for Developers][1])

A second thing to check is whether your API request is using a **partial response mask** that excludes those fields. If you use `fields=...` in `documents.get`, and you do not include `textRun.textStyle.weightedFontFamily`, `fontSize`, or `foregroundColor`, they will not be returned even if present. The Docs API supports partial responses through the standard Google API mechanism. ([Google for Developers][3])

A safe `fields` shape for text styling is along these lines:

```text
body/content/paragraph/elements/textRun(content,textStyle)
,namedStyles
```

Or more explicitly:

```text
body/content/paragraph(elements(textRun(content,textStyle(weightedFontFamily,fontSize,foregroundColor,backgroundColor,bold,italic,underline,strikethrough))))
,body/content/paragraph/paragraphStyle(namedStyleType)
,namedStyles/styles(namedStyleType,textStyle)
```

If you want a reliable parser, treat these as **optional override fields**, not guaranteed fields.

A practical parser rule is:

```text
effectiveTextStyle =
  run.textStyle override
  -> paragraph named style textStyle
  -> normal text named style
  -> Docs default
```

Also, Google’s formatting guide shows that text formatting is commonly written with `UpdateTextStyleRequest`, which is another clue that these properties belong to `TextStyle`. ([Google for Developers][2])

If it helps, I can write you a small Go function that takes a `documents.get` response and resolves the **effective** font family, font size, and color for each text run.

[1]: https://developers.google.com/workspace/docs/api/reference/rest/v1/documents?utm_source=chatgpt.com "REST Resource: documents | Google Docs | Google for Developers"
[2]: https://developers.google.com/workspace/docs/api/how-tos/format-text?utm_source=chatgpt.com "Format Text | Google Docs | Google for Developers"
[3]: https://developers.google.com/workspace/docs/api/reference/rest?utm_source=chatgpt.com "Google Docs API | Google for Developers"
