- [2024 年 JavaScript 膨脹@tonsky.me --- JavaScript Bloat in 2024 @ tonsky.me](https://tonsky.me/blog/js-bloat/?utm_source=hackernewsletter&utm_medium=email&utm_term=fav)
    - 看 network tab → see 一些網頁內容，就需要 3~5 mb, 甚至更多1x mb, 覺得很扯
    - 不只是慢
    - 這些也都用到我們電腦本身的記憶體，cpu, 空間資源


- [我如何將開源專案變成一項業務 --- How I turned my open-source project into a business](https://docs.emailengine.app/how-i-turned-my-open-source-project-into/?utm_source=hackernewsletter&utm_medium=email&utm_term=fav)
    - 本來用 agpl (copy left, 就是如果公司要用，一定要開源) → 然後 也有 mit → 但要付費
        - 大公司不用，小公司不管 licence
    - 變成一定要付費使用，不然用 15 min 就 無法
        - 有 node.js package 可以讓人開發，主要是讓人轉化
        - 一年 3萬台幣，訂閱
    - 作者目前一個月收入20萬，覺得很不錯
    - 產品 [EmailEngine Email API](https://emailengine.app/?ref=docs.emailengine.app)


- [問HN：您每天如何學習和研究？ |駭客新聞 --- Ask HN: How do you learn and research every day? | Hacker News](https://news.ycombinator.com/item?id=39510292&utm_source=hackernewsletter&utm_medium=email&utm_term=ask_hn)


- try again 加速 npx tsc 的速度， the most impactful exp for me in work to avoid me to go into flow and let me stop!!!!!!!!!!!!
    - [how to speed up `npx tsc --watch` for a big project](https://www.notion.so/how-to-speed-up-npx-tsc-watch-for-a-big-project-71828572dccd431bb5e5f99192092e0c?pvs=21)
    - 目前靠 intelliJ 幫我編譯緩減

- have a project idea to use ast-grep
    - 任何東西，只要是有架構的，你都可以透過這個工具來精準尋找並輸出 JSON
    - [[快速入門 | ast grep --- Quick Start | ast-grep](https://ast-grep.github.io/guide/quick-start.html)]([https://www.notion.so/ast-grep-Quick-Start-ast-grep-c25b221ee0034752a404b75a8fb6ade3?pvs=21](https://www.notion.so/ast-grep-Quick-Start-ast-grep-c25b221ee0034752a404b75a8fb6ade3?pvs=21))
    - find a 最小影響的 micro service → 升級 typeorm → maybe use ast-grep
        - typeorm 升級影響: ‣


- google sheet as BE
    - [https://www.zerosheets.com/tables/1YIrM_ECiZGGS_pev3PA1Fv0Nrh2O3NT6cd6eU2RfIBM](https://www.zerosheets.com/tables/1YIrM_ECiZGGS_pev3PA1Fv0Nrh2O3NT6cd6eU2RfIBM)
    - google sheet: [https://docs.google.com/spreadsheets/d/1YIrM_ECiZGGS_pev3PA1Fv0Nrh2O3NT6cd6eU2RfIBM/edit#gid=0](https://docs.google.com/spreadsheets/d/1YIrM_ECiZGGS_pev3PA1Fv0Nrh2O3NT6cd6eU2RfIBM/edit#gid=0)
    - 想法
        - 你真的要我做，我也弄的出來
        - 就是幫使用者把excel和 API 都做好
        - 前端 UX 的體驗要夠好
    - HN comment:
        - 大家都在談 excel 陷阱
        - 小公司用這招 → 最後就變成很難migrate 出來 XD