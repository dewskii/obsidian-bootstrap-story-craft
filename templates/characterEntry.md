<%* 

const file = app.vault.getAbstractFileByPath(`${tp.file.title}.md`);

let prompt = await tp.system.prompt('Character Name: ');
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
  - "#character"
aliases:
---
# <% title %>

#### Background
Lorem ipsum dolor sit amet, consectetur adipisci elit, sed eiusmod tempor incidunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrum exercitationem ullam corporis suscipit laboriosam, nisi ut aliquid ex ea commodi consequatur. Quis aute iure reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint obcaecat cupiditat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.

