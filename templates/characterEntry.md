<%*  
let title = tp.file.title;
let _title = await tp.system.prompt('Name: ');

if (title.startsWith('Untitled')) {  
  title = `${_title}`; // Set and store the new title
  await tp.file.rename(title); 
}  
-%>
---
tags:
  - "#glossary"
  - "#character"
related: []
---
## Name


## Background

---

## Related
<% tp.file.related %>