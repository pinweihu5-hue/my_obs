---  
alias:  
creation_date: 2024-05-25 10:06  
tags: 
related: [[tool optimize]], [[vscode_idx]]. [[vscode_inteillJ切換]]
---  


# 目前設置

關掉eslint by deault
如果真的會檢查不到應該檢查的東西，那我再寫完code要推上去前一鍵打開，但本質上我寫code不需要用 eslint

寫code時用 oxc

還是需要安裝 oxc and eslint 套件, 另外要安裝 Setting Toggle 套件


need to run
```js

npx oxlint@latest
// pnpm
pnpm dlx oxlint@latest
// deno
deno run oxlint@latest
// bun
bunx oxlint@latest


```

ref:
[為什麼還要另一個 JavaScript linter？ --- Why another JavaScript linter?](https://quick-lint-js.com/blog/why-another-javascript-linter/#oxlint)
[Getting Started | The JavaScript Oxidation Compiler](https://oxc-project.github.io/docs/guide/introduction.html)

