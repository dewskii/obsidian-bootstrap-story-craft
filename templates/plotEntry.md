<%*  
let title = tp.file.title;
let titleTag = title
let _title = await tp.system.prompt('Note name: ');

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
  - "#plot"
aliases:
---
# <% title %>

### Details
Give a short blurb on plot point the note covers