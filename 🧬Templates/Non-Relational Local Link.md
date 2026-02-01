<%*
// --- CONFIGURATION ---
const vaultId = "Saarthak's_Headspace";
// ---------------------

// 1. Prompt for the new link name
//const newName = await tp.system.prompt("File Name:", tp.file.title);
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
const encodedName = newName.replace(/ /g, "%20").replace(/&/g, "%26");
const uri = `obsidian://open?vault=${vaultId}&file=${encodedName}`;

tR +=`[${newName}](${uri})`
%>