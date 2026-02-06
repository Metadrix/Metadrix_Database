<%*
// --- CONFIGURATION ---
const vaultId = "Saarthak's_Headspace";
const ignr_dir = ["💻Computing", "🚀Engineering", "🧠Sciences"];
// ---------------------

// 1. Prompt for the new file name // Defaults to the current name (e.g., "Untitled") if you hit Esc 
const promptWithSelection = (app, promptText, defaultValue) => {
    return new Promise((resolve) => {
        // Create a simple modal using Obsidian's API
        const modal = new tp.obsidian.Modal(app);
        
        modal.onOpen = () => {
            modal.contentEl.createEl("h2", { text: promptText });
            
            // Create input field
            const input = modal.contentEl.createEl("input", { 
                type: "text", 
                value: defaultValue 
            });
            input.style.width = "100%";

            // KEY: Focus and Select All text
            input.focus();
            input.select();

            // Handle "Enter" key to submit
            input.addEventListener("keydown", (e) => {
                if (e.key === "Enter") {
                    e.preventDefault();
                    modal.close();
                    resolve(input.value);
                }
            });

            // Handle "Escape" (optional, resolve null)
             input.addEventListener("keydown", (e) => {
                if (e.key === "Escape") {
                    modal.close();
                    resolve(null);
                }
            });
        };
        
        modal.onClose = () => {
            modal.contentEl.empty();
        };
        
        modal.open();
    });
};

// Call the function
const newName = await promptWithSelection(app, "File Name:", tp.file.title);

// 2. Rename the file if the user entered a name 
if (newName && newName !== tp.file.title) { await tp.file.rename(newName); }

// 3. Get the folder path and split it
const path = tp.file.folder(true);
const folders = path.split("/").filter(f => f !== "/");

// 4. Initialize the breadcrumb trail
let breadcrumbParts = [];
breadcrumbParts.push(`[📁 Explore](obsidian://open?vault=${vaultId}&file=📁%20Explore)`);
        

// 5. Build links: Loop through every folder in the path
const parentNote = (folders[folders.length - 1] === newName)? 1 : 0;

folders.forEach((folderName, index) => {
    const isImmediateParent = (index === folders.length - 1 - parentNote);

	if ((index === 0 && ignr_dir.includes(folderName)) || folderName === newName) return;
    const encodedName = folderName.replace(/ /g, "%20").replace(/&/g, "%26");
	const uri = `obsidian://open?vault=${vaultId}&file=${encodedName}`;
	
	breadcrumbParts.push(`[${folderName}](${uri})`);
});

// 4. Output the full trail > Current Title
if (breadcrumbParts.length > 0) {
    tR += `${breadcrumbParts.join(" > ")} > ${newName || tp.file.title}`;
} else {
    tR += `${newName || tp.file.title}`;
}
// ========================================
// 7. AUTO-APPEND TO PARENT DIRECTORY NOTE
// ========================================

// Get the immediate parent directory name
const immediateParentDir = folders[folders.length - 1 - parentNote];

// Only proceed if:
// 1. Parent directory exists
// 2. Current note name is NOT the same as parent directory (avoid self-reference)
// 3. Parent directory is not in ignored list
if (immediateParentDir && 
    immediateParentDir !== (newName || tp.file.title) && 
    !ignr_dir.includes(immediateParentDir)) {
    
    // Try to find a note with the same name as the parent directory
    const parentDirNote = tp.file.find_tfile(immediateParentDir);
    
    if (parentDirNote) {
        try {
            // Read current content of the parent directory note
            const parentContent = await app.vault.read(parentDirNote);
            
            // Count existing numbered list items to calculate next number
            // Matches lines like "1. [[NoteName]]" or "42. [[Another Note]]"
             // Find all numbered list items with their positions
            const numberedListRegex = /^\d+\.\s+\[\[.+?\]\]/gm;
            const matches = [...parentContent.matchAll(numberedListRegex)];
            
            if (matches.length === 0) {
                // No existing list - append at the end
                const nextNumber = 1;
                const finalNoteName = newName || tp.file.title;
                const newLink = `${nextNumber}. [[${finalNoteName}]]`;
                const updatedContent = parentContent.trimEnd() + "\n" + newLink;
                await app.vault.modify(parentDirNote, updatedContent);
            } else {
                // Find the last numbered item
                const lastMatch = matches[matches.length - 1];
                const lastMatchEnd = lastMatch.index + lastMatch[0].length;
                
                // Calculate next number
                const nextNumber = matches.length + 1;
                const finalNoteName = newName || tp.file.title;
                const newLink = `${nextNumber}. [[${finalNoteName}]]`;
                
                // Split content at the end of the last numbered item
                const beforeList = parentContent.substring(0, lastMatchEnd);
                const afterList = parentContent.substring(lastMatchEnd);
                
                // Insert the new link right after the last numbered item
                const updatedContent = beforeList + "\n" + newLink + afterList;
                
                // Write back to parent directory note
                await app.vault.modify(parentDirNote, updatedContent);
            }
            
        } catch (error) {
            console.error("Error appending to parent directory note:", error);
        }
    }
}
%>
# #double #grey


# #double #grey
# References

# #line #grey
# Tags
<%*
// Logic to generate the directory tag
const dirPath = tp.file.folder(true);
const dirParts = dirPath.split("/").filter(d => d !== "/");
const currentTitle = tp.file.title;

// Determine target folder (handling Folder Notes logic)
const isFolderNoteFile = dirParts[dirParts.length - 1] === currentTitle;
const tagIndex = dirParts.length - 1 - (isFolderNoteFile ? 1 : 0);
const targetFolder = dirParts[tagIndex];

if (targetFolder) {
    // 1. Remove Emojis/Symbols from the start (Unicode aware)
    // Matches: Emojis, Pictographs, and any immediate following whitespace
    let cleanName = targetFolder.replace(/^[\p{Emoji}\p{Extended_Pictographic}\s]+/u, "").trim();

    // 2. Convert to Lowercase
    cleanName = cleanName.toLowerCase();

    // 3. Replace spaces with hyphens for valid hashtag format
    const tag = cleanName.replace(/\s+/g, "-");
    
    tR += `#${tag}`;
}
%>