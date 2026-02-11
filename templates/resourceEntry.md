<%*  
let sourceUrl = await tp.system.prompt('Source url');
-%>
---
source: <% sourceUrl %>
tags:
  - "#glossary"
  - "#resource"
---
[!html-fetch] <% sourceUrl %>


