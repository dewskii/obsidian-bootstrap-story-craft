<%*  
let title = tp.file.title;
let _title = await tp.system.prompt('Title: ');

if (title.startsWith('Untitled')) {  
  title = `${_title}`; // Set and store the new title
  await tp.file.rename(title); 
}  
-%>
---
tags:
  - "#glossary"
  - "#vocab"
related: []
---
## Definition


---

## Related
<% tp.file.related %>