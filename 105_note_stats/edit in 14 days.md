
```dataview
table file.mday
from ""  
where date(today) -  date(mtime) <= dur(14 days)  
sort file.mtime desc
```