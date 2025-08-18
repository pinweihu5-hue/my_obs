---  
alias:  
creation_date: <% tp.file.creation_date() %>  
related:
- 

---  
<%*  
let title;  
if(tp.file.title.startsWith("Untitled")) {  
  title = await tp.system.prompt("Note name", "", true);  
  if (!(title == "")) {
    await tp.file.rename(title);
  } else {};
} else {};
_%>  
