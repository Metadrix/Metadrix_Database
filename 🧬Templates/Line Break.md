<%*
// 1. Define the divider style
const divider = "---";

// 2. Ask which level you want to break (default is 2)
// Or you can hardcode this to your most common level, e.g., const level = 2;
const level = await tp.system.prompt("Header Level to Break (1-6)?", "2");

// 3. Generate the "Empty Header" + Divider
// The empty header (hashes + space) breaks the fold scope.
// The divider sits inside this new invisible section.
if (level) {
    const hashes = "#".repeat(parseInt(level));
    tR += `\n${hashes} \n${divider}\n`;
}
%>