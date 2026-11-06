# Edge Insights Documentation

Public user documentation for Edge Insights, published at docs.cededgeinsights.com. Pages live in `docs/` and build with
MkDocs. See `README.md` for the preview and build commands.

## Tone

Readers are international engineers, and many are not native English speakers.

- Use a technical, factual tone, not marketing.
- Use simple English in short declarative sentences.
- Use American English spelling.
- Do not use rhetorical questions or promotional phrasing, e.g. "No hardware handy?" or "whether you're X or Y".

## Punctuation

- **Label and description:** use a colon, not an em dash. This applies to list items and to standalone bold-label
  paragraphs, e.g. "**Windows**: a Kvaser CAN interface".
- **Em dash in a sentence:** split into two sentences, or use a comma if the clauses are closely related. Do not only
  swap the character.
- **Parentheses around a clause or sentence:** remove them if they add nothing, otherwise rewrite as plain text. Short
  qualifiers can stay in parentheses, e.g. a unit range "(1–60 minutes)", "(the default)", or a command name.
- **Parentheses around examples:** use a comma and "e.g.", without parentheses, e.g. ", e.g. analytics database, uploaded
  DBC files". Use "i.e." the same way for a restatement.
- **Filler clauses:** remove clauses such as "no manual setup needed" when the sentence is complete without them.

Check for remaining em dashes after a pass with `grep -rn "—" docs/`.

## Examples

- Runnable examples, e.g. curl commands and URLs in code blocks, use `http://localhost:36300` so they work when copied.
- Placeholder hosts are for prose that explains URL structure, or for reaching a headless embedded device from another
  machine, e.g. `getting-started/installation-embedded.md`.

## Artifact names

Write release artifact names with a bare `<version>` placeholder, e.g. `edge-insights-<version>-windows-x86_64-setup.exe`.
Do not write `v<version>`, even though real files carry the `v`. The placeholder stands for the full version string. Do
not report the difference as a bug.

## Screenshots

Control display size in Markdown with `attr_list`, e.g. `{ width="500" }`. Do not shrink the source file, because
glightbox zooms to the full-size image.
