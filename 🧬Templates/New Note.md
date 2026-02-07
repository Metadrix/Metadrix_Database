<%*
// --- CONFIGURATION ---
const vaultId = "Metadrix_Database";
const ignr_dir = [];
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
const breadcrumbs = breadcrumbParts.length > 0 ? `${breadcrumbParts.join(" > ")} > ${newName}` : newName;

// Get the immediate parent directory name
const immediateParentDir = folders[folders.length - 1 - parentNote];

// ========================================
// DETECT FOLDER NOTE (NEW)
// ========================================
const finalNoteName = newName || tp.file.title;
const isFolderNote = immediateParentDir === finalNoteName;  // Skip auto-append if creating/updating the folder's own note [cite:1][web:22]

// ========================================
// 7. AUTO-APPEND TO PARENT DIRECTORY NOTE
// ========================================
if (!isFolderNote && immediateParentDir && 
    immediateParentDir !== finalNoteName && 
    !ignr_dir.includes(immediateParentDir)) {
    
    const parentDirNote = tp.file.find_tfile(immediateParentDir);
    
    if (parentDirNote) {
        try {
            let parentContent = await app.vault.read(parentDirNote);
            const fileHeader = "# Files";
            const divider = "# #line #grey";
            
            let sectionStartIdx = parentContent.indexOf(fileHeader);
            let updatedContent = parentContent;

            if (sectionStartIdx === -1) {
                // --- CASE: # Files DOES NOT EXIST (DETERMINE PLACEMENT) ---
                let insertPos = -1;
                
                // Priority 1: After all content under "# References"
                const refIdx = parentContent.indexOf("# References");
                if (refIdx !== -1) {
                    const contentAfterRef = parentContent.substring(refIdx + "# References".length);
                    const nextHeadingInRef = contentAfterRef.match(/^#/m);
                    
                    if (nextHeadingInRef) {
                        // Found the next heading (e.g., # Tags), insert right before it
                        insertPos = refIdx + "# References".length + nextHeadingInRef.index;
                    } else {
                        // No heading after # References, append to end
                        insertPos = parentContent.length;
                    }
                } 
                // Priority 2: After second "# #double #grey"
                else {
                    const firstDouble = parentContent.indexOf("# #double #grey");
                    if (firstDouble !== -1) {
                        const secondDouble = parentContent.indexOf("# #double #grey", firstDouble + 1);
                        if (secondDouble !== -1) {
                            const lineEnd = parentContent.indexOf("\\n", secondDouble);
                            insertPos = lineEnd !== -1 ? lineEnd : parentContent.length;
                        }
                    }
                }

                if (insertPos === -1) insertPos = parentContent.length;

                // Create new section with a trailing newline to avoid clashing with the following heading
                const newSection = `\\n\\n${divider}\\n${fileHeader}\\n1. [[${finalNoteName}]]\\n`;
                updatedContent = parentContent.slice(0, insertPos).trimEnd() + newSection + parentContent.slice(insertPos);
                
            } else {
                // --- CASE: # Files EXISTS (SANDBOXED UPDATE) ---
                const searchArea = parentContent.substring(sectionStartIdx + fileHeader.length);
                const nextHeadingMatch = searchArea.match(/^#/m);
                const sectionEndIdx = nextHeadingMatch 
                    ? (sectionStartIdx + fileHeader.length + nextHeadingMatch.index) 
                    : parentContent.length;
                
                const sectionBody = parentContent.substring(sectionStartIdx, sectionEndIdx);
                const escapedName = finalNoteName.replace(/[.*+?^${}()|[\\]\\\\]/g, '\\\\$&');
                const uniquenessRegex = new RegExp(`\\\\[\\\\[${escapedName}(\\\\|.*?)?\\\\]\\\\]`);

                if (!uniquenessRegex.test(sectionBody)) {
                    const numberedListRegex = /^\\d+\\.\\s+\\[\\[.+?\\]\\]/gm;
                    const matches = [...sectionBody.matchAll(numberedListRegex)];
                    
                    const nextNumber = matches.length + 1;
                    const newLink = `${nextNumber}. [[${finalNoteName}]]`;

                    if (matches.length > 0) {
                        const lastMatch = matches[matches.length - 1];
                        const lastItemPos = sectionStartIdx + sectionBody.indexOf(lastMatch[0]) + lastMatch[0].length;
                        updatedContent = parentContent.slice(0, lastItemPos) + "\\n" + newLink + parentContent.slice(lastItemPos);
                    } else {
                        const insertAt = sectionStartIdx + fileHeader.length;
                        updatedContent = parentContent.slice(0, insertAt) + "\\n" + newLink + parentContent.slice(insertAt);
                    }
                } else {
                    return;
                }
            }

            if (updatedContent !== parentContent) {
                await app.vault.modify(parentDirNote, updatedContent);
            }
            
        } catch (error) {
            console.error("Error updating parent note:", error);
        }
    }
}    

	// ========================================
	// 7. AUTO-APPEND Parent Directory Tag
	// ========================================
	let tag = "";
	if (immediateParentDir) {
	    // 1. Remove Emojis (Uses single backslash for the Unicode property)
	    let cleanName = immediateParentDir
	        .replace(/^[\\p{Emoji}\\p{Extended_Pictographic}\\s]+/u, "") 
	        .trim()
	        .toLowerCase();
	
	    // 2. Remove special characters (Keeping only a-z, 0-9, spaces, dashes, underscores)
	    const tagBody = cleanName
	        .replace(/[^a-z0-9\\s\\-_]/g, "") 
	        .trim()
	        .replace(/[\\s\\-]+/g, "-"); // Replaces spaces/dashes with a single hyphen
	    
	    tag = `#${tagBody}`;
	}

// --- ASSEMBLE NEW CONTENT ---
let newContent = `${breadcrumbs}
# #double #grey



# #double #grey
# References

# #line #grey
# Tags
${tag} #incomplete`;
// WRITE TO FILE
tR += newContent;

new Notice("New Note Created!");
%>
