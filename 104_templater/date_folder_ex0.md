
<%*
const baseFolder = '/00_fleeting_note'
const yr = tp.date.now('yyyy')
const mon = tp.date.now('MM')
const date = tp.date.now('DD')
const newFolder = `${baseFolder}/${yr}_${mon}_${date}/`
await tp.file.move(newFolder)
-%>