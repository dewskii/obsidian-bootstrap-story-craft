```dataviewjs
const tagName = "#glossary";

const TYPES = {
  "#vocab":     { group: "Definitions", heading: "Title", description: "Definition" },
  "#character": { group: "Characters",  heading: "Name",  description: "Background" },
  "#location":  { group: "Locations",   heading: "Name",  description: "Description" },
};

const DEFAULT_TYPE = { group: "Other", heading: "Name", description: "Description" };

function getSectionContent(markdown, sectionName) {
  const sectionPattern = new RegExp("#{1,5}\\s*" + sectionName);
  const nextSectionPattern = new RegExp("#{1,5}");

  const match = markdown.match(sectionPattern);
  if (!match) return "-";

  const headerLen = match[0].length;
  const start = markdown.search(sectionPattern) + headerLen;

  const rest = markdown.substring(start);
  const nextIdx = rest.search(nextSectionPattern);

  return (nextIdx === -1 ? rest : rest.substring(0, nextIdx)).trim();
}

function getTypeInfo(page) {
  const tags = page.file.tags ?? [];
  const typeTag = Object.keys(TYPES).find(t => tags.includes(t));
  return typeTag ? TYPES[typeTag] : DEFAULT_TYPE;
}

const pages = dv.pages(tagName);

// group by group label (Characters / Definitions / ...)
const groups = pages.groupBy(p => getTypeInfo(p).group)
  .sort((a, b) => a.key.localeCompare(b.key));

for (const group of groups) {
  const info = getTypeInfo(group.rows[0]);
  const headers = [info.heading, info.description, "Related"];

  const rows = await Promise.all(
    group.rows.map(async (p) => {
      const content = await dv.io.load(p.file.path);
      return {
        sortKey: p.file.name,
        link: p.file.link,
        desc: getSectionContent(content, info.description),
        related: p.related ? p.related : []
      };
    })
  );
  dv.header(2, `${group.key} (${group.rows.length})`);
  dv.table(
    headers,
    rows
      .sort((a, b) => a.sortKey.localeCompare(b.sortKey))
      .map(r => [r.link, r.desc, r.related])
  );
}
```