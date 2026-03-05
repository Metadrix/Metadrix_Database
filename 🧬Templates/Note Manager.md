<%*
/**
 * Note Manager - Production (v2.0)
 * 
 * =====================================================================================
 * USAGE GUIDE
 * =====================================================================================
 * Run this script via Templater. It behaves differently based on context:
 * 
 * 1. FOLDER NOTE MODE (Run from a note with same name as parent folder)
 *    Enables batch operations. Enter commands separated by `//`.
 * 
 *    - CREATE: Just type the name.
 *      `MyNote`          -> Creates note "MyNote"
 *      `./MyFolder`      -> Creates folder "MyFolder" + folder note
 * 
 *    - DELETE: Prefix with `\\` (Double Backslash).
 *      `\\MyNote`        -> Deletes "MyNote"
 *      `\\./MyFolder`    -> Deletes "MyFolder"
 * 
 *    - RENAME: Use `||` as separator.
 *      `||NewName`       -> Renames CURRENT folder/note to "NewName"
 *      `Old||New`        -> Renames child "Old" to "New"
 * 
 *    - SYNC: Type `\s` or `\Sync`.
 *      Updates # Files list, handles orphans, and prompts for sorting.
 * 
 *    - TAGS: Type `\st` or `\Tags`.
 *      Aggregates tags from all child notes.
 * 
 *    EXAMPLE BATCH: `Chapter1 // \\Draft // OldName||NewName // \s`
 * 
 * 2. SIMPLE NOTE MODE (Run from any other note)
 *    Prompts for: Convert to Folder Note / Rename / Delete.
 * =====================================================================================
 */

const vaultId = "Metadrix_Database";
const ignr_dir = [];
const DEBUG = false;

// PROMPT CONFIG
const MODAL_WIDTH = "600px";
const MODAL_HEIGHT = "auto";
const CUSTOM_TOP = "7.5vh";
const CUSTOM_LEFT = "";
const PROMPT_HEADER = "Note Manager";
const SUBMIT_BUTTON_LABEL = "Execute";

const notice = (msg, duration = 5000) => new Notice(msg, duration);

// UTILITIES
const validateFilename = (name) => {
    const n = name.startsWith("./") ? name.substring(2) : name;
    if (/[\\/:\*\?"<>\|]/.test(n)) { notice(`Invalid filename '${n}'`); return false; }
    return true;
};

const generateTag = (name) => {
    return `#${name.replace(/^[\p{Emoji}\p{Extended_Pictographic}\s]+/u, "").trim().toLowerCase().replace(/[^a-z0-9\s\-_]/g, "").trim().replace(/[\s\-]+/g, "-")}`;
};

const generateBreadcrumbs = (name, parentPath) => {
    if (!parentPath) return "";
    const parents = parentPath.split("/").filter(p => p);
    let crumbs = [`[📁 Explore](obsidian://open?vault=${vaultId}&file=📁%20Explore)`];
    parents.forEach(p => (!ignr_dir.includes(p) && p !== "" && p !== null && p !== undefined && p !== name) ? crumbs.push(`[${p}](obsidian://open?vault=${vaultId}&file=${encodeURIComponent(p)})`) : null);
    crumbs.push(name);
    return crumbs.join(" > ");
};

const parseEntry = (entry) => {
    try {
        const isDir = entry.startsWith('./');
        const clean = isDir ? entry.substring(2) : entry;
        const match = clean.match(/^\[\[(.+)\]\]$/);
        return { original: entry, isDir, name: match ? match[1] : clean };
    } catch (e) { return { original: entry, isDir: false, name: entry }; }
};

const sortEntries = (entries, sortOrder) => {
    if (sortOrder === "preserve") return entries;
    const sorted = [...entries].sort((a, b) => {
        const nameA = a.name.toLowerCase();
        const nameB = b.name.toLowerCase();
        return nameA.localeCompare(nameB) || a.name.localeCompare(b.name);
    });
    if (sortOrder === "reverse") sorted.reverse();
    return sorted;
};

// SYNC LOGIC
const syncFileList = async (targetFile, targetDirPath, removeNames = [], sortOrder = "preserve") => {
    try {
        let content = await app.vault.read(targetFile);
        const filesMatch = content.match(/^# Files\s*$/m);
        let existing = [];
        if (filesMatch) {
            const start = filesMatch.index + filesMatch[0].length;
            const sub = content.substring(start);
            let end = -1;
            const blank = sub.match(/\n\s*\n/); if (blank) end = blank.index;
            if (end === -1) { const d = sub.match(/\n# #dashed #grey/); if (d) end = d.index; }
            if (end === -1) { const l = sub.match(/\n# #line #grey/); if (l) end = l.index; }
            const sec = end === -1 ? sub : sub.substring(0, end);
            existing = sec.split('\n').map(l => l.trim().match(/^\s*\d+\.\s*(.+)$/)).filter(m => m).map(m => parseEntry(m[1]));
        }

        const toRemove = Array.isArray(removeNames) ? removeNames : [removeNames];
        if (toRemove.length > 0) existing = existing.filter(e => !toRemove.includes(e.name));

        const folder = app.vault.getAbstractFileByPath(targetDirPath);
        if (!folder) return;

        const fsEntries = [
            ...(folder.children || []).filter(f => f instanceof tp.obsidian.TFile && f.extension === 'md' && f.basename !== targetFile.basename).map(f => ({ isDir: false, name: f.basename, original: `[[${f.basename}]]` })),
            ...(folder.children || []).filter(f => f instanceof tp.obsidian.TFolder).map(f => ({ isDir: true, name: f.name, original: `./[[${f.name}]]` }))
        ];

        const merged = existing.map(e => {
            const match = fsEntries.find(f => f.name === e.name && f.isDir === e.isDir);
            return match ? match : e;
        });

        fsEntries.forEach(entry => {
            if (!merged.some(e => e.name === entry.name && e.isDir === entry.isDir)) merged.push(entry);
        });

        const finalEntries = sortEntries(merged, sortOrder);

        const updateSection = (text, entries) => {
            const m = text.match(/^# Files\s*$/m);
            if (!m) return text;
            const start = m.index + m[0].length;
            const sub = text.substring(start);
            let end = -1; let del = "";
            const blank = sub.match(/\n\s*\n/); if (blank) { end = blank.index; del = "\n\n"; }
            const d = sub.match(/\n# #dashed #grey/); if (d && (end === -1 || d.index < end)) { end = d.index; del = "\n# #dashed #grey"; }
            const l = sub.match(/\n# #line #grey/); if (l && (end === -1 || l.index < end)) { end = l.index; del = "\n# #line #grey"; }
            const list = "\n" + entries.map((e, i) => `${i + 1}. ${e.original}`).join("\n");
            return text.substring(0, start) + list + del + (end === -1 ? "" : sub.substring(end + del.length));
        };

        const updated = updateSection(content, finalEntries);
        if (content !== updated) await app.vault.modify(targetFile, updated);
    } catch (e) { notice("Sync Error: " + e.message); }
};

// MODULES
const CreateModule = {
    generateNoteContent: (targetName, basePath, parentFolderName, includeFolderSection = false) => {
        const crumbLine = generateBreadcrumbs(targetName, basePath);
        const tagSafe = parentFolderName.replace(/[^a-z0-9]/gi, "-").toLowerCase();
        const base = `${crumbLine}\n# #double #grey\n\n\n\n# #double #grey\n# References\n\n# #line #grey\n`;
        return includeFolderSection
            ? `${base}# Files\n\n# #dashed #grey\n\n# #line #grey\n# Tags\n#${tagSafe} #incomplete`
            : `${base}# Tags\n#${tagSafe} #incomplete`;
    },
    run: async (name, folderPath, folderName) => {
        const isDir = name.startsWith("./");
        const clean = isDir ? name.substring(2) : name;
        if (!validateFilename(clean)) return false;
        try {
            if (isDir) {
                const path = `${folderPath}/${clean}`;
                if (await app.vault.adapter.exists(path)) { notice(`'${clean}' exists`); return false; }
                await app.vault.createFolder(path);
                await app.vault.create(`${path}/${clean}.md`, CreateModule.generateNoteContent(clean, folderPath, clean, true));
                return true;
            } else {
                const path = `${folderPath}/${clean}.md`;
                if (await app.vault.adapter.exists(path)) { notice(`'${clean}' exists`); return false; }
                await app.vault.create(path, CreateModule.generateNoteContent(clean, folderPath, folderName, false));
                return true;
            }
        } catch (e) { notice(`Create Error: ${e.message}`); return false; }
    },
    convertToFolder: async (file) => {
        const folderPath = file.parent ? file.parent.path : "";
        const name = file.basename;
        const dir = `${folderPath}/${name}`;
        if (await app.vault.adapter.exists(dir)) { notice("Dir exists"); return; }
        try {
            await app.vault.createFolder(dir);
            await app.fileManager.renameFile(file, `${dir}/${name}.md`);
            let content = await app.vault.read(file);
            if (!content.includes("# Files")) {
                const ref = content.indexOf("# References");
                const l = content.indexOf("# #line #grey", ref);
                const sec = `\n# Files\n\n# #dashed #grey\n\n# #line #grey`;
                content = (ref !== -1 && l !== -1) ? content.slice(0, ref + l + 13) + sec + content.slice(ref + l + 13) : content + "\n" + sec;
            }
            const tag = generateTag(name);
            const tIdx = content.indexOf("# Tags");
            content = (tIdx !== -1) ? content.substring(0, tIdx + 6) + `\n${tag}` : content + `\n\n# #line #grey\n# Tags\n${tag}`;
            const crumbs = generateBreadcrumbs(name, folderPath);
            const cReg = /^\[📁 Explore\].*$/m;
            content = cReg.test(content) ? content.replace(cReg, crumbs) : crumbs + "\n" + content;
            await app.vault.modify(file, content);
            if (folderPath && folderPath !== "/") {
                const pNote = app.vault.getAbstractFileByPath(`${folderPath}/${folderPath.split("/").pop()}.md`);
                if (pNote) await syncFileList(pNote, folderPath);
            }
            notice(`Converted '${name}'`);
        } catch (e) { notice("Convert Error: " + e.message); }
    }
};

const DeleteModule = {
    run: async (name, folderPath) => {
        const isDir = name.startsWith("./");
        const clean = isDir ? name.substring(2) : name;
        const item = isDir ? app.vault.getAbstractFileByPath(`${folderPath}/${clean}`) : app.vault.getAbstractFileByPath(`${folderPath}/${clean}.md`);
        if (!item) { notice(`'${clean}' not found to delete.`); return null; }
        try {
            await app.vault.trash(item, true);
            return clean;
        } catch (e) { notice(`Delete Error: ${e.message}`); return null; }
    }
};

const RenameModule = {
    recursiveBreadcrumbUpdate: async (dir, oldD, newD) => {
        const files = app.vault.getFiles().filter(f => f.path.startsWith(dir));
        for (const f of files) {
            try {
                const l = generateBreadcrumbs(f.basename, f.parent ? f.parent.path : "");
                let c = await app.vault.read(f);
                const r = /^\[📁 Explore\].*$/m;
                if (r.test(c) && c.match(r)[0] !== l) await app.vault.modify(f, c.replace(r, l));
            } catch (e) { }
        }
    },
    recursiveTagUpdate: async (dir, oldT, newT) => {
        const files = app.vault.getFiles().filter(f => f.path.startsWith(dir));
        for (const f of files) {
            try {
                let c = await app.vault.read(f);
                if (new RegExp(`${oldT}\\b`, 'g').test(c)) await app.vault.modify(f, c.replace(new RegExp(`${oldT}\\b`, 'g'), newT));
            } catch (e) { }
        }
    },
    run: async (src, tgt, folder) => {
        const folderPath = folder.path;
        if (src === "") {
            const oldN = folder.name;
            try {
                const pNote = folder.parent ? app.vault.getAbstractFileByPath(`${folder.parent.path}/${folder.parent.name}.md`) : null;
                await app.fileManager.renameFile(folder, `${folder.parent.path}/${tgt}`);
                const newPath = `${folder.parent.path}/${tgt}`;
                const fNote = app.vault.getAbstractFileByPath(`${newPath}/${oldN}.md`);
                if (fNote) await app.fileManager.renameFile(fNote, `${newPath}/${tgt}.md`);
                if (pNote) await syncFileList(pNote, folder.parent.path, oldN);
                await RenameModule.recursiveBreadcrumbUpdate(newPath, oldN, tgt);
                await RenameModule.recursiveTagUpdate(newPath, generateTag(oldN), generateTag(tgt));
                return true;
            } catch (e) { notice("Rename Error (Folder): " + e.message); return false; }
        }
        const isDir = src.startsWith("./");
        const cleanSrc = isDir ? src.substring(2) : src;
        if (isDir) {
            const cDir = app.vault.getAbstractFileByPath(`${folderPath}/${cleanSrc}`);
            if (!cDir) { notice("Dir not found: " + cleanSrc); return false; }
            try {
                await app.fileManager.renameFile(cDir, `${folderPath}/${tgt}`);
                const newPath = `${folderPath}/${tgt}`;
                const fNote = app.vault.getAbstractFileByPath(`${newPath}/${cleanSrc}.md`);
                if (fNote) await app.fileManager.renameFile(fNote, `${newPath}/${tgt}.md`);
                await RenameModule.recursiveBreadcrumbUpdate(newPath, cleanSrc, tgt);
                await RenameModule.recursiveTagUpdate(newPath, generateTag(cleanSrc), generateTag(tgt));
                return true;
            } catch (e) { notice("Rename Error (Dir): " + e.message); return false; }
        } else {
            const cFile = app.vault.getAbstractFileByPath(`${folderPath}/${cleanSrc}.md`);
            if (!cFile) { notice("Note not found: " + cleanSrc); return false; }
            try {
                await app.fileManager.renameFile(cFile, `${folderPath}/${tgt}.md`);
                const newFile = app.vault.getAbstractFileByPath(`${folderPath}/${tgt}.md`);
                if (newFile) {
                    let c = await app.vault.read(newFile);
                    const l = generateBreadcrumbs(tgt, folderPath);
                    const r = /^\[📁 Explore\].*$/m;
                    c = r.test(c) ? c.replace(r, l) : `${l}\n${c}`;
                    await app.vault.modify(newFile, c);
                }
                return true;
            } catch (e) { notice("Rename Error (Note): " + e.message); return false; }
        }
    }
};

const OrphanModule = {
    run: async (currentFile, currentDir, parentFolderName) => {
        let content = await app.vault.read(currentFile);
        const filesMatch = content.match(/^# Files\s*$/m);
        if (!filesMatch) return false;
        const start = filesMatch.index + filesMatch[0].length;
        const sub = content.substring(start);
        let end = -1;
        const blank = sub.match(/\n\s*\n/); if (blank) end = blank.index;
        if (end === -1) { const d = sub.match(/\n# #dashed #grey/); if (d) end = d.index; }
        if (end === -1) { const l = sub.match(/\n# #line #grey/); if (l) end = l.index; }
        const sec = end === -1 ? sub : sub.substring(0, end);
        const existing = sec.split('\n').map(l => l.trim().match(/^\s*\d+\.\s*(.+)$/)).filter(m => m).map(m => parseEntry(m[1]));

        const orphans = { notes: [], directories: [] };
        for (const e of existing) {
            if (e.isDir) {
                const p = `${currentDir}/${e.name}`;
                if (!await app.vault.adapter.exists(p) || !await app.vault.adapter.exists(`${p}/${e.name}.md`))
                    orphans.directories.push(e);
            } else {
                if (!await app.vault.adapter.exists(`${currentDir}/${e.name}.md`)) orphans.notes.push(e);
            }
        }
        if (orphans.notes.length === 0 && orphans.directories.length === 0) return true;

        const action = await tp.system.suggester(["Ignore", "Delete from List", "Create Missing Files"], ["ignore", "delete", "create"], false, `Found ${orphans.notes.length + orphans.directories.length} orphans.`);
        if (!action || action === "ignore") return false;

        if (action === "delete") {
            const namesToRemove = [...orphans.notes.map(o => o.name), ...orphans.directories.map(o => o.name)];
            await syncFileList(currentFile, currentDir, namesToRemove);
            notice("Orphans removed.");
            return true;
        }

        if (action === "create") {
            for (const o of orphans.notes) await CreateModule.run(o.name, currentDir, parentFolderName);
            for (const o of orphans.directories) await CreateModule.run(`./${o.name}`, currentDir, parentFolderName);
            notice("Orphans created.");
            return true;
        }
        return false;
    }
};

const TagsModule = {
    run: async (folderNote) => {
        const folderPath = folderNote.parent.path;
        const allFiles = app.vault.getFiles().filter(f => f.path.startsWith(folderPath + "/") && f.path !== folderNote.path);
        const tags = new Set();
        for (const f of allFiles) {
            const c = await app.vault.read(f);
            const m = c.match(/^# Tags\s*$/m);
            if (m) {
                const sub = c.substring(m.index + m[0].length);
                const next = sub.match(/\n#/);
                const block = next ? sub.substring(0, next.index) : sub;
                const found = block.match(/#[\w-]+/g);
                if (found) found.forEach(t => tags.add(t));
            }
        }
        if (tags.size === 0) return;
        const sorted = Array.from(tags).sort().join(" ");
        let content = await app.vault.read(folderNote);
        const filesMatch = content.match(/^# Files\s*$/m);
        if (!filesMatch) return;
        const dash = content.indexOf("# #dashed #grey", filesMatch.index);
        const line = content.indexOf("# #line #grey", dash);
        if (dash !== -1 && line !== -1) {
            const before = content.substring(0, dash + 15);
            const after = content.substring(line);
            await app.vault.modify(folderNote, `${before}\n\n${sorted}\n\n${after}`);
            notice(`Updated ${tags.size} tags.`);
        }
    }
};

class PromptModal extends tp.obsidian.Modal {
    constructor(app, defaultValue, onSubmit) {
        super(app);
        this.defaultValue = defaultValue;
        this.onSubmit = onSubmit;
    }
    onOpen() {
        const { contentEl, modalEl } = this;
        modalEl.style.width = MODAL_WIDTH;
        if (MODAL_HEIGHT !== "auto") modalEl.style.height = MODAL_HEIGHT;
        modalEl.style.backgroundColor = "#202020";
        modalEl.style.border = "2px solid #555";
        modalEl.style.boxShadow = "0 0 15px rgba(0,0,0,0.5)";
        if (CUSTOM_TOP || CUSTOM_LEFT) {
            modalEl.style.margin = "0";
            if (CUSTOM_TOP) { modalEl.style.top = CUSTOM_TOP; modalEl.style.position = "absolute"; }
            if (CUSTOM_LEFT) { modalEl.style.left = CUSTOM_LEFT; modalEl.style.position = "absolute"; }
        }
        const style = document.createElement('style');
        style.innerHTML = `
            .prompt-input-container { display: flex; flex-direction: column; gap: 15px; }
            .prompt-header { margin: 0; color: #ddd; }
            .prompt-input { width: 100% !important; font-size: 1.2em!important; padding: 10px!important; border: 1px solid #666 !important; background-color: #333 !important; color: #fff !important; border-radius: 5px !important; }
            .prompt-footer { display: flex; justify-content: flex-end; }
            .prompt-btn { cursor: pointer; background-color: #483699; color: white; border: none; padding: 8px 16px; border-radius: 4px; }
            .prompt-btn:hover { background-color: #7a6ae6; }
        `;
        contentEl.appendChild(style);
        const container = contentEl.createDiv({ cls: "prompt-input-container" });
        container.createEl("h2", { text: PROMPT_HEADER, cls: "prompt-header" });
        const input = container.createEl("input", { type: "text", cls: "prompt-input", value: this.defaultValue });
        const footer = container.createDiv({ cls: "prompt-footer" });
        const btn = footer.createEl("button", { text: SUBMIT_BUTTON_LABEL, cls: "mod-cta prompt-btn" });
        input.focus(); input.select();
        const submit = () => { this.onSubmit(input.value); this.close(); };
        btn.addEventListener("click", (e) => { e.preventDefault(); submit(); });
        input.addEventListener("keydown", (e) => {
            if (e.key === "Enter") { e.preventDefault(); submit(); } else if (e.key === "Escape") this.close();
        });
    }
    onClose() { this.contentEl.empty(); }
}
const promptInput = (defaultVal) => new Promise(resolve => new PromptModal(app, defaultVal, resolve).open());

// MAIN
const file = tp.file.find_tfile(tp.file.path(true));
if (!file) { notice("Error: Current file not found."); return; }
const folderPath = file.parent ? file.parent.path : "";
const folderName = file.parent ? file.parent.name : "";
const isFolderNote = (file.basename === folderName);

if (isFolderNote) {
    const editor = app.workspace.activeLeaf.view.editor;
    let processedSel = editor ? editor.getSelection() : "";
    if (processedSel) {
        processedSel = processedSel.split("\n").map(l => {
            l = l.trim().replace(/^(\d+\.|[-*+])\s+/, "");
            if (l.startsWith("[[")) l = l.substring(2);
            if (l.endsWith("]]")) l = l.substring(0, l.length - 2);
            return l;
        }).filter(l => l.length > 0).join("//");
    }

    const rawInput = await promptInput(processedSel);
    if (!rawInput) return;

    const tokens = rawInput.split("//").map(s => s.trim()).filter(s => s);
    const renames = []; const deletes = []; const creates = [];
    let runSync = false; let runTags = false; let syncRemovals = [];

    for (const token of tokens) {
        if (/^\\(s|S|Sync|sync)$/.test(token)) { runSync = true; continue; }
        if (/^\\(st|SyncTags|Synctags|sT|St)$/.test(token)) { runTags = true; continue; }
        if (token.startsWith("\\\\")) { deletes.push(token.substring(2)); continue; }
        if (token.includes("||")) {
            const parts = token.split("||");
            renames.push({ src: parts[0].trim(), tgt: parts[1].trim() });
            continue;
        }
        creates.push(token);
    }

    let currentFolderUpdatedPath = folderPath;
    let currentFolderNameUpdated = folderName;

    for (const op of renames) {
        const isCurrentFolderRename = (op.src === "" || (op.src === folderName && !op.src.startsWith("./")));
        let success = await RenameModule.run(op.src, op.tgt, file.parent);
        if (success && isCurrentFolderRename) {
            currentFolderUpdatedPath = `${file.parent.parent.path}/${op.tgt}`;
            currentFolderNameUpdated = op.tgt;
        }
    }

    for (const name of deletes) {
        const removedName = await DeleteModule.run(name, currentFolderUpdatedPath);
        if (removedName) syncRemovals.push(removedName);
    }

    for (const name of creates) await CreateModule.run(name, currentFolderUpdatedPath, currentFolderNameUpdated);

    if (runTags) {
        const updatedFile = app.vault.getAbstractFileByPath(`${currentFolderUpdatedPath}/${currentFolderNameUpdated}.md`);
        if (updatedFile) await TagsModule.run(updatedFile);
    }

    const finalFolderNote = app.vault.getAbstractFileByPath(`${currentFolderUpdatedPath}/${currentFolderNameUpdated}.md`);
    if (finalFolderNote) {
        let sortChoice = "preserve";
        if (runSync) {
            await OrphanModule.run(finalFolderNote, currentFolderUpdatedPath, finalFolderNote.basename);
            const userSort = await tp.system.suggester(["Alphabetical (A → Z)", "Reverse (Z → A)", "Preserve Order"], ["alphabetical", "reverse", "preserve"], false, "Sort Files?");
            if (userSort) sortChoice = userSort;
        }
        await syncFileList(finalFolderNote, currentFolderUpdatedPath, syncRemovals, sortChoice);
    }

} else {
    const choice = await tp.system.suggester(["Convert to Folder Note", "Rename", "Delete"], ["convert", "rename", "delete"], false, "Action?");
    if (choice === "convert") {
        await CreateModule.convertToFolder(file);
    } else if (choice === "rename") {
        const newName = await promptInput(file.basename);
        if (newName && newName !== file.basename) await RenameModule.run(file.basename, newName, file.parent);
    } else if (choice === "delete") {
        await DeleteModule.run(file.basename, folderPath);
        const pNote = app.vault.getAbstractFileByPath(`${folderPath}/${folderName}.md`);
        if (pNote) await syncFileList(pNote, folderPath, file.basename);
        notice(`Deleted ${file.basename}`);
    }
}
-%>
