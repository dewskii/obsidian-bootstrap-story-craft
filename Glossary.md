```dataviewjs

const tagName = "#glossary";

const getSectionContent = (markDownString, sectionName) => {
  const sectionPattern = new RegExp("#{1,5}\\s*" + sectionName);
  const nextSectionPattern = new RegExp("#{1,5}");

  let sectionHeaderLength;
  if (sectionPattern.test(markDownString)) {
    sectionHeaderLength = markDownString.match(sectionPattern)[0].length;
  } else {
    return "<span>-</span>";
  }

  const startingIndex = markDownString.search(sectionPattern);
  const endingIndex = markDownString
    .substring(startingIndex + sectionHeaderLength)
    .search(nextSectionPattern);

  if (endingIndex === -1) {
    return markDownString.substring(startingIndex + sectionHeaderLength).trim();
  }

  return markDownString
    .substring(
      startingIndex + sectionHeaderLength,
      startingIndex + sectionHeaderLength + endingIndex
    )
    .trim();
};

// Group by type (Definitions vs Characters)
const pages = dv.pages(tagName);
const typeGroups = pages.groupBy(note =>
  note.file.tags?.includes("#vocab") ? "Definitions" : "Characters"
);

// Optional: force ordering
const order = ["Characters", "Definitions"];
typeGroups.sort((a, b) => order.indexOf(a.key) - order.indexOf(b.key));

for (let group of typeGroups) {
  const isDefinitions = group.key === "Definitions";
  const sectionName = isDefinitions ? "Title" : "Name";
  const descriptionName = isDefinitions ? "Definition" : "Background";

  const tableEntries = await Promise.all(
    group.rows.map(async (note) => {
      const content = await dv.io.load(note.file.path);
      return {
        sortKey: note.file.name,
        title: note.file.link, // clickable link
        description: getSectionContent(content, descriptionName),
        related: note.related
      };
    })
  );

  dv.header(2, group.key);
  dv.table(
    [sectionName, descriptionName, "Related"],
    tableEntries
      .sort((a, b) => a.sortKey.localeCompare(b.sortKey))
      .map(e => [e.title, e.description, e.related])
  );
}

```