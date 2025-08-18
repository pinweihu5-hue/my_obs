<%*
let qcFileName = await tp.system.prompt("Note Title")
let titleName = tp.date.now("YYYY MMDD") + " - " + qcFileName
await tp.file.rename(titleName)
let baseFolder = "0_inbox"
let year = tp.date.now('YYYY') 
let month = tp.date.now('MM-MMM') 
let newFolder = `${baseFolder}${year}/${month}/` 
await tp.file.move(newFolder + titleName);
-%>