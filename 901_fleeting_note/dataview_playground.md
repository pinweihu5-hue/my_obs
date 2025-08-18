

# top 50 most outgoing link
```dataview

table length(file.outlinks)
sort length(file.outlinks) desc
limit 10

```



# top 50 most ingoing link
```dataview

table length(file.inlinks)
sort length(file.inlinks) desc
limit 10

```




# 查看新建的檔案有哪些沒有連到其他筆記
```

table length(file.outlinks)
where length(file.outlinks) = 0

```


# 查看新建的檔案沒有被別人連
```


table length(file.inlinks)
where length(file.inlinks) = 0

```



## filter by metaData
```

yaml 有
—  
tag : 思考  
Q : [ChatGPT 如何幫助 SEO？]  
sum : [NLP 格式，關鍵字探勘，內容摘要，改寫]  
—

table Q,sum  
from ""
where tag = "思考"
sort file.mtime DESC



```




## check task
```
task  
from ""
where date(today) – file.cday <= dur(20 days)  
where !completed
```

## 除排特定條件
```
table  
from ""
where date(today) - file.mday <= dur(3 days) and !contains(file.name,"inbox")
sort file.mtime DESC

```

其他語法
```

where !contains(tag,"MindDuo")

from "" and -"400-Archive/journals" and -# 外語

```




# 最近3天修改的筆記
```
table  
from ""
where date(today) - file.mday <= dur(3 days)  
sort file.mtime DESC
```


