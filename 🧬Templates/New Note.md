<%*
// --- CONFIGURATION ---
const vaultId = "Saarthak's_Headspace";
const ignr_dir = ["💻Computing", "🚀Engineering", "🧠Sciences"];
// ---------------------

// 1. Prompt for the new file name // Defaults to the current name (e.g., "Untitled") if you hit Esc 
// const newName = await tp.system.prompt("Name:", tp.file.title);
// Define a custom prompt function
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
    
    //else if (isImmediateParent) //breadcrumbParts.push(`[[${folderName}]]`); // Immediate //Parent -> Wiki Link [[ ]]
    //else {
    //   // Ancestors (Grandparents) -> Obsidian URI Link [ ]( )
    //    // Encodes ' ' to %20 and '&' to %26 as requested
    //    const encodedName = folderName.replace(/ /g, //"%20").replace(/&/g, "%26");
    //    const uri = `obsidian://open?vault=${vaultId}&file=${encodedName}`;
    //    
    //    breadcrumbParts.push(`[${folderName}](${uri})`);
	//}
});

// 4. Output the full trail > Current Title
if (breadcrumbParts.length > 0) {
    tR += `${breadcrumbParts.join(" > ")} > ${newName || tp.file.title}`;
} else {
    tR += `${newName || tp.file.title}`;
}
%>

---


---
# References

# Tags
#incomplete