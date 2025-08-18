---  
alias:  
creation_date: 2024-05-19 15:19  
tags: 
---  

[[nodejs_idx]]

- goal -> try to speed up hermes
- fail 
	- reason: swc is not support





folder: `/Users/re4388/project/personal/swc-0`


``` bash
# how to run it, compile js side by side to ts
npx swc --ignore node_modules --watch "src/**/*.ts" --out-dir "./"

```







error
```sh

└─> npx swc --ignore node_modules --watch "src/**/*.ts" --out-dir "./"
Cannot find module 'timers/promises'
Require stack:
- /Users/re4388/project/personal/swc-0/node_modules/piscina/dist/src/index.js
- /Users/re4388/project/personal/swc-0/node_modules/piscina/dist/src/main.js
- /Users/re4388/project/personal/swc-0/node_modules/@swc/cli/lib/swc/dir.js
- /Users/re4388/project/personal/swc-0/node_modules/@swc/cli/lib/swc/index.js
- /Users/re4388/project/personal/swc-0/node_modules/@swc/cli/bin/swc.js


```



