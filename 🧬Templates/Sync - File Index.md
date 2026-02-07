<%*
/**
 * Folder-index maintainer for Obsidian Templater
 * - Fixes blank line issue
 * - Separates Orphan handling and List updating into robust try/catch blocks
 */

const vault = app.vault;
const notice = (msg) => new Notice(msg, 5000);

// Helper: parse list line to a "name"
const getEntryName = (line) => {
  if (!line) return null;
  const mLink = line.match(/\[\[([^|\]]+)(?:\|[^]]*)?\]\]/);
  if (mLink) return mLink[1].trim();
  const mPlain = line.match(/^\s*\d+\.\s+(.+?)\s*$/);
  return mPlain ? mPlain[1].trim() : null;
};

// ------------------------------------------------------------
// 1. PREPARATION & PARSING
// ------------------------------------------------------------

// Ensure we are in a folder note
const file = tp.file.find_tfile(tp.file.path(true));
if (!file) { notice("Could not resolve current file"); return; }

const pathParts = file.path.split("/");
const folderPath = pathParts.slice(0, -1).join("/");
const folderName = folderPath.split("/").pop() || "";

if (!folderName || file.basename !== folderName) {
  notice("Invalid Trigger: Not Folder Note");
  return;
}

// Get current children
const allFiles = vault.getFiles();
const allFolders = vault.getAllLoadedFiles().filter(f => f instanceof tp.obsidian.TFolder);

const childNotes = allFiles.filter(f => {
  const parts = f.path.split("/");
  const fFolder = parts.slice(0, -1).join("/");
  return fFolder === folderPath && f.basename !== folderName;
});

const childFolders = allFolders.filter(f => {
  const fPath = f.path;
  const parent = fPath.split("/").slice(0, -1).join("/");
  return parent === folderPath;
});

// Current real files on disk
const realNames = new Set([
  ...childNotes.map(f => f.basename),
  ...childFolders.map(f => f.name),
]);

// Read file content
const content = await vault.read(file);
const lines = content.split("\n");

const filesHeaderIdx = lines.findIndex(l => l.trim().toLowerCase() === "# files");
if (filesHeaderIdx === -1) {
  notice("Error: Missing '# Files' Section");
  return;
}

// Determine section bounds
let filesStart = filesHeaderIdx + 1;
let filesEnd = lines.length;
for (let i = filesStart; i < lines.length; i++) {
  if (lines[i].trim().startsWith("# ")) {
    filesEnd = i;
    break;
  }
}

// Extract existing list items
const entryLines = [];
for (let i = filesStart; i < filesEnd; i++) {
  const l = lines[i];
  if (/^\s*\d+\.\s+/.test(l)) {
    entryLines.push({ index: i, text: l });
  }
}

const listNames = [];
for (const e of entryLines) {
  const name = getEntryName(e.text);
  if (name) listNames.push(name);
}

// Shared state for the next two blocks
let updatedListNames = [...listNames];
// We maintain a mutable set of real files to handle "Create Note" actions immediately
const currentRealSet = new Set(realNames);

// ------------------------------------------------------------
// 2. ORPHAN HANDLING BLOCK (Try/Catch)
// ------------------------------------------------------------
try {
  const orphans = listNames.filter(n => !currentRealSet.has(n));

  if (orphans.length > 0) {
    const orphanOptions = [
      "Ignore (Keep links)",
      "Delete from List",
      "Create Notes",
      "Create Directories"
    ];

    const action = await tp.system.suggester(
      orphanOptions,
      [1, 2, 3, 4],
      false,
      "Orphans / ghost links found:"
    );

    if (action != null) {
      if (action === 2) {
        // Delete
        updatedListNames = updatedListNames.filter(n => !orphans.includes(n));
      } else if (action === 3) {
        // Create Notes
        for (const name of orphans) {
          const safeName = name.replace(/[\\\/]/g, "-");
          const fullPath = (folderPath ? folderPath + "/" : "") + safeName + ".md";
          const exists = allFiles.some(f => f.path === fullPath);
          if (!exists) {
            await vault.create(fullPath, "");
          }
          // Ensure it stays in list, and mark as existing
          if (!updatedListNames.includes(name)) updatedListNames.push(name);
          currentRealSet.add(safeName);
        }
      } else if (action === 4) { // FIXED: Was checking '3' again
        // Create Folders
        for (const name of orphans) {
          const safeName = name.replace(/[\\\/]/g, "-");
          const newFolderPath = (folderPath ? folderPath + "/" : "") + safeName;
          const folderExists = allFolders.some(f => f.path === newFolderPath);
          if (!folderExists) {
            await vault.createFolder(newFolderPath);
          }
          const notePath = newFolderPath + "/" + safeName + ".md";
          const noteExists = allFiles.some(f => f.path === notePath);
          if (!noteExists) {
            await vault.create(notePath, "");
          }
          if (!updatedListNames.includes(name)) updatedListNames.push(name);
          currentRealSet.add(safeName);
        }
      } 
      // Action 1 (Ignore) falls through here, doing nothing to updatedListNames
    } else {
        notice("Orphan handling cancelled - proceeding to update list");
    }
  }
} catch (err) {
  console.error(err);
  notice("Error in Orphan Handling (skipped): " + err.message);
}

// ------------------------------------------------------------
// 3. LIST UPDATE & SAVE BLOCK (Try/Catch)
// ------------------------------------------------------------
try {
  // Identify missing files (Real files NOT in the list)
  // We use currentRealSet to account for any files we just created in step 2
  const missingNow = Array.from(currentRealSet).filter(n => !updatedListNames.includes(n));

  if (missingNow.length > 0) {
    updatedListNames.push(...missingNow);
  }

  // Ordering
  const orderOptions = ["A → Z", "Z → A", "Preserve Order (Append)"];
  const orderChoice = await tp.system.suggester(orderOptions, [1, 2, 3], false, "Choose ordering for # Files:");
  
  // If user explicitly cancels ordering, we stop the write to be safe, 
  // OR we can default to '3'. Here we stop to avoid overwriting with bad data.
  if (orderChoice == null) {
    notice("List Update Aborted by User");
  } else {
    let finalNames;
    if (orderChoice === 1) {
      finalNames = Array.from(new Set(updatedListNames)).sort((a, b) => a.localeCompare(b, undefined, { sensitivity: "base" }));
    } else if (orderChoice === 2) {
      finalNames = Array.from(new Set(updatedListNames)).sort((a, b) => b.localeCompare(a, undefined, { sensitivity: "base" }));
    } else {
      // Preserve order (deduplicate only)
      const seen = new Set();
      finalNames = [];
      for (const n of updatedListNames) {
        if (!seen.has(n)) {
          seen.add(n);
          finalNames.push(n);
        }
      }
    }

    // Generate new content
    const newListLines = finalNames.map((name, idx) => `${idx + 1}. [[${name}]]`);

    const before = lines.slice(0, filesStart);
    const between = lines.slice(filesStart, filesEnd);
    const after = lines.slice(filesEnd);

    // Filter preserved lines: remove old list items AND blank lines
    // FIXED: Added `&& l.trim().length > 0` to prevent extra blank lines
    const preservedNonList = between.filter(l => !/^\s*\d+\.\s+/.test(l) && l.trim().length > 0);

    const newFilesBlock = preservedNonList.concat(newListLines);
    
    // Construct final file content
    const newContentLines = before
      .concat(newFilesBlock)
      .concat(after);

    const newContent = newContentLines.join("\n");
    
    await vault.modify(file, newContent);
    notice("File Index Synchronized");
  }

} catch (err) {
  console.error(err);
  notice("Error in List Update: " + err.message);
}
_%>
