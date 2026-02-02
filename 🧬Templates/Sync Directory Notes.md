<%*
// Get current file (the parent note)
const currentFile = app.workspace.getActiveFile();
if (!currentFile) {
    new Notice("❌ No active file");
    return;
}

// Get the folder path from current file
const currentFolder = currentFile.parent;
if (!currentFolder) {
    new Notice("❌ File is not in a folder");
    return;
}

// Get the folder name
const folderName = currentFolder.name;

// Verify this note matches its folder name
if (currentFile.basename !== folderName) {
    new Notice("❌ This note name doesn't match its folder name");
    return;
}

// Get all markdown files in the current folder (not subfolders)
const allFiles = app.vault.getMarkdownFiles()
    .filter(file => {
        const fileFolder = file.parent;
        return fileFolder && fileFolder.path === currentFolder.path;
    })
    .filter(file => file.basename !== folderName)
    .filter(file => file.extension === "md");

// Sort alphabetically
allFiles.sort((a, b) => a.basename.localeCompare(b.basename));

// Read current content
const currentContent = await app.vault.read(currentFile);

// Find existing numbered links
const existingLinksRegex = /^\d+\.\s+\[\[(.+?)\]\]/gm;
const existingMatches = [...currentContent.matchAll(existingLinksRegex)];
const existingNoteNames = existingMatches.map(match => match[1]);

// Find differences
const missingNotes = allFiles.filter(file => 
    !existingNoteNames.includes(file.basename)
);
const deletedNotes = existingNoteNames.filter(noteName =>
    !allFiles.some(file => file.basename === noteName)
);

// Check if sync needed
if (missingNotes.length === 0 && deletedNotes.length === 0) {
    new Notice("✅ Already in sync!");
    return;
}

// Build new list
const allValidNotes = allFiles.map(file => file.basename);
const newNumberedList = allValidNotes
    .map((noteName, index) => `${index + 1}. [[${noteName}]]`)
    .join("\n");

// Replace existing list or append
if (existingMatches.length > 0) {
    const firstMatch = existingMatches[0];
    const lastMatch = existingMatches[existingMatches.length - 1];
    
    const listStart = firstMatch.index;
    const listEnd = lastMatch.index + lastMatch[0].length;
    
    const beforeList = currentContent.substring(0, listStart);
    const afterList = currentContent.substring(listEnd);
    
    const updatedContent = beforeList + newNumberedList + afterList;
    await app.vault.modify(currentFile, updatedContent);
} else {
    const updatedContent = currentContent.trimEnd() + "\n\n" + newNumberedList;
    await app.vault.modify(currentFile, updatedContent);
}

// Show summary
const summary = [];
if (missingNotes.length > 0) summary.push(`✅ Added ${missingNotes.length}`);
if (deletedNotes.length > 0) summary.push(`🗑️ Removed ${deletedNotes.length}`);
summary.push(`📊 Total: ${allValidNotes.length}`);

new Notice(summary.join(" | "), 5000);
-%>
