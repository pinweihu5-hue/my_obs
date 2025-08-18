
```dataview
table file.mday
from ""  
where date(today) - file.cday <= dur(14 days)  
sort file.ctime desc
```