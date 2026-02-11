<%* 

const file = app.vault.getAbstractFileByPath(`${tp.file.title}.md`);

let prompt = await tp.system.prompt('Plot Note Name: ');
if (prompt === null) {
  await app.vault.trash(file, true);
  return;
}
 
const title = `${prompt}`;
const titleTag = title.replace(/\s+/g, '-').toLowerCase();
await tp.file.rename(title); 

-%>
---
topic: <% titleTag %>
tags:
  - "#glossary"
  - "#plot"
aliases:
---
# <% title %>

### Details
Give a short blurb on plot point the note covers