---
obsidianUIMode: preview
---
# Glossary
---
```dataviewjs
(async () => {
  const TAG = "#glossary";
  const FM_FIELD = "_backLinks";
  
  //todo: move this to a yaml file or something
  const TYPES = {
    "#vocab":     { group: "Definitions", heading: "Title", descHeader: "Definition" },
    "#character": { group: "Characters",  heading: "Name",  descHeader: "Background" },
    "#location":  { group: "Locations",   heading: "Name",  descHeader: "Description" },
    "#faction":   { group: "Factions",    heading: "Name",  descHeader: "Description" },
    "#plot":      { group: "Plot",        heading: "Name",  descHeader: "Details" },
    "#resource":  { group: "Resources",   heading: "Name",  descHeader: "Source" }
  };

  const DEFAULT = { group: "Other", heading: "Name", descHeader: "Description" };

  const herePath = dv.current()?.file?.path;
  if (!herePath) return dv.paragraph("Dataview: couldn't resolve current file.");

  const here = app.vault.getAbstractFileByPath(herePath);

  const uniq = (a) => [...new Set((a ?? []).filter(Boolean))];
  const escapeRe = (s) => s.replace(/[.*+?^${}()|[\]\\]/g, "\\$&");

  function typeInfo(p) {
    const tags = p.file?.tags ?? [];
    for (const t of Object.keys(TYPES)) if (tags.includes(t)) return TYPES[t];
    return DEFAULT;
  }

  function section(md, header) {
    const hRe = new RegExp(`^#{1,6}\\s*${escapeRe(header)}\\s*$`, "m");
    const m = md.match(hRe);
    if (!m) return "-";

    const after = md.slice(md.indexOf(m[0]) + m[0].length);
    const next = after.search(/^\s*#{1,6}\s+/m);
    return (next === -1 ? after : after.slice(0, next)).trim() || "-";
  }

  function wiki(p) {
    const path = p.file.path.replace(/\.md$/i, "");
    return `[[${path}|${p.file.name}]]`;
  }

  function sameList(a, b) {
    const A = uniq(Array.isArray(a) ? a.map(String) : [String(a ?? "")]).sort();
    const B = uniq((b ?? []).map(String)).sort();
    return A.length === B.length && A.every((v, i) => v === B[i]);
  }

  function writeFrontmatterLinks(links) {
    const next = uniq(links).sort();
    const cur = dv.current()[FM_FIELD];

    if (sameList(cur, next)) return;

    // processFrontMatter is async-ish; don't let failures kill the whole render
    try {
      app.fileManager.processFrontMatter(here, (fm) => {
        fm[FM_FIELD] = next;
      });
    } catch (e) {
      console.error("Frontmatter update failed:", e);
    }
  }

  const pages = dv.pages(TAG);

  const items = await Promise.all(
    pages.map(async (p) => {
      const info = typeInfo(p);
      const md = await dv.io.load(p.file.path);

      const desc =
        info.group === "Resources"
          ? (p.source ?? p["source"] ?? "-")
          : section(md, info.descHeader);

      return { p, info, name: p.file.name, desc, fmLink: wiki(p) };
    })
  );

  writeFrontmatterLinks(items.map(i => i.fmLink));

  const groups = items.reduce((acc, i) => {
    (acc[i.info.group] ??= []).push(i);
    return acc;
  }, {});

  for (const groupName of Object.keys(groups).sort()) {
    const rows = groups[groupName].sort((a, b) => a.name.localeCompare(b.name));
    const info = rows[0]?.info ?? DEFAULT;

    dv.header(4, groupName);
    dv.table([info.heading, info.descHeader], rows.map(r => [r.p.file.link, r.desc]));
    dv.el("hr", "");
  }
})();
```