<%*  
let title = tp.file.title;
let titleTag = title
let _title = await tp.system.prompt('Entry Name: ');

if (title.startsWith('Untitled')) {  
  title = `${_title}`; // Set and store the new title
  titleTag = title.replace(/\s+/g, '-').toLowerCase();
  await tp.file.rename(title); 
}  
-%>
---
topic: <% titleTag %>
tags:
  - "#glossary"
  - "#vocab"
aliases:
---
## <% title %>

#### Definition
Lorem ipsum dolor sit amet, consectetur adipisci elit, sed eiusmod tempor incidunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrum exercitationem ullam corporis suscipit laboriosam, nisi ut aliquid ex ea commodi consequatur. Quis aute iure reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint obcaecat cupiditat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum.


