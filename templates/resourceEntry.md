<%*  
let title = tp.file.title;
let titleTag = title
let _title = await tp.system.prompt('Source title: ');
let sourceUrl = await tp.system.prompt('Source url');

if (title.startsWith('Untitled')) {  
  title = `${_title}`; // Set and store the new title
  titleTag = title.replace(/\s+/g, '-').toLowerCase();
  await tp.file.rename(title); 
}  
-%>
---
topic: <% titleTag %>
source: <% sourceUrl %>
tags:
  - "#glossary"
  - "#resource"
---
# <% title %>

[!html-fetch] <% sourceUrl %>


