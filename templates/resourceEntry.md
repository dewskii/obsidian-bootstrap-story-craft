<%*

/* Resource entries use the auto-rename plugin.
   Title header is set by html-fetch plugin.
   Race condition means can't easily rename the file after html-fetch is invoked.
 */

const file = app.vault.getAbstractFileByPath(tp.file.title);

let sourceUrl = await tp.system.prompt('Source url');
if (sourceUrl === null) {
  await app.vault.trash(file, true);
  return;
}

-%>
---
source: <% sourceUrl %>
tags:
  - "#glossary"
  - "#resource"
---
[!html-fetch] <% sourceUrl %>

