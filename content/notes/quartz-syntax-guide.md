---
title: Quartz syntax guide
description: A reference of the Markdown features available in this Quartz site.
date: 2026-06-22
tags:
  - reference
  - quartz
---

A quick tour of what you can write in a note. View the raw file
(`content/notes/quartz-syntax-guide.md`) alongside this rendered page to compare.

## Frontmatter

Every note starts with a YAML block between `---` fences. Common fields:

```yaml
---
title: Quartz syntax guide      # shown as the page heading + browser tab
description: A short summary     # used for previews and SEO
date: 2026-06-22                 # displayed under the title
tags:                           # creates tag pages, e.g. /tags/reference
  - reference
  - quartz
---
```

## Links

- Internal (wikilink): `[[growth-stages]]` → [[growth-stages]]
- Internal with custom text: `[[growth-stages|how notes mature]]` → [[growth-stages|how notes mature]]
- External: `[Quartz docs](https://quartz.jzhao.xyz/)` → [Quartz docs](https://quartz.jzhao.xyz/)

## Callouts

> [!note]
> A standard note callout.

> [!tip] Custom title
> Callouts can have their own titles.

> [!warning]
> Use these to flag caveats.

Other types include `info`, `example`, `quote`, `question`, and `danger`.

## Code

Inline `code`, and fenced blocks with syntax highlighting:

```python
def greet(name: str) -> str:
    return f"Hello, {name}!"

print(greet("world"))
```

## Lists & tasks

- A bullet
  - A nested bullet
- [ ] An unchecked task
- [x] A completed task

## Tables

| Feature | Supported |
| --- | :---: |
| Tables | ✅ |
| Footnotes | ✅ |
| Math | ✅ |

## Math

Inline math like $e^{i\pi} + 1 = 0$, and a display block:

$$
\int_0^1 x^2 \, dx = \frac{1}{3}
$$

## Images

Drop an image into `content/` (or a subfolder) and embed it:

```markdown
![alt text](my-image.png)
![[my-image.png]]   <!-- Obsidian-style embed also works -->
```

## Footnotes

Here is a statement with a footnote.[^1]

[^1]: And here is the footnote's content.

---

See the full reference in the [Quartz docs](https://quartz.jzhao.xyz/), or go back to
[[start-here|Start Here]].
