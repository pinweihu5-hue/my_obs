
---


use the same way, 
use 用playwrightcli 参数  --headed --persistent，open https://localhost:5173/ 

update request to 
```
{

"a1": 99

}
```


help me update the "decisionTable1" node
use "Edit Table" to edit it
I want to add a rule
when "a1" is 99, the output can be 
{
result: "good"
}

how to use decision table can ref this: [Building decision tables - GoRules Documentation](https://docs.gorules.io/learn/authoring/decision-tables)


---



use playwrightcli  --headed --persistent，open https://localhost:5173/  do work on below task:

step1:
this is the doc to know how to use gorules editor
https://docs.gorules.io
check this doc if you are not sure how to do for the following steps

step2:
help me setup below gorules

request, input
™```
{
"name": "ben",
"value": 42
}

```

my rules is as follow:


also use decision nod to check if name is "ben"
- if it is -> just pass the value to next function node
- if it is not -> value mutiple by 3 and pass the val to the next function node

an then use funciton to use below JS code to get the response to get the title
```javascript
fetch('https://jsonplaceholder.typicode.com/todos/1')
      .then(response => response.json())
      .then(json => console.log(json))
```

the final result:
```
{
 "finalVal": 44
 "title": "xxx" // depend on api response
}
```





























