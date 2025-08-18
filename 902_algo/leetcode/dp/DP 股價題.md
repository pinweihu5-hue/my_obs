
基本本用下面這樣來 dfs 就通解
- `i, isStockAtHand, dealCount`
	- idx, 目前是否有持股，交易次數


### [121. Best Time to Buy and Sell Stock](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/)
1. 給定一個 price array, ele 是第 i 天的 股價
2. 某天買，未來某一天賣
3. 求最大profit
4. 只能進行一次交易
```ts fold
function maxProfit(prices: number[]): number {

    let memo = {}
    let dfs = (i, isStockAtHand, isOneDealFinish) => {
        let key = i+","+isStockAtHand
        
        if(isOneDealFinish===true) return 0
        
        if(key in memo) return memo[key]
        
        if(i === prices.length) return 0
        
        
        
        if(isStockAtHand === false){
            let buyStock = dfs(i+1, true, false) - prices[i]
            let noBuy = dfs(i+1, isStockAtHand, isOneDealFinish)
            let curProfit = Math.max(buyStock, noBuy)
            memo[key] = curProfit
            return curProfit
        } else {   // cannot buy, meaning you still keek
            let sellStack = dfs(i+1, false, true) + prices[i]
            let noSell = dfs(i+1, isStockAtHand, isOneDealFinish)
            let curProfit = Math.max(sellStack, noSell)
            memo[key] = curProfit
            return curProfit
        }
        
    }
    
    return dfs(0, false, false)

};

```
### [122. Best Time to Buy and Sell Stock II](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-ii/)
1. 給定一個 price array, ele 是第 i 天的 股價
2. 某天買，未來某一天賣
3. 求最大profit
4. 可以進行多次交易  ← **跟 121 的差異**
    1. 不可以同時持有多筆，要賣了才可以買
```ts fold
function maxProfit(prices: number[]): number {
  let memo = {};
  let dfs = (i, isStockAtHand) => {
    let key = i + "," + isStockAtHand;
    if (key in memo) return memo[key];

    if (i === prices.length) return 0;

    // 跟 121 的差異就是可以同時進行多次交易(但不可以同時），因此 dfs 要 track 的狀態反而少一個
    if (isStockAtHand === false) {
      // 買或是不買
      let buyStock = dfs(i + 1, true) - prices[i];
      let noBuy = dfs(i + 1, isStockAtHand);
      let curProfit = Math.max(buyStock, noBuy);
      memo[key] = curProfit;
      return curProfit;
    } else {
      // 賣或是不賣
      let sellStack = dfs(i + 1, false) + prices[i];
      let noSell = dfs(i + 1, isStockAtHand);
      let curProfit = Math.max(sellStack, noSell);
      memo[key] = curProfit;
      return curProfit;
    }
  };

  return dfs(0, false);
}



```

### 123. [Best Time to Buy and Sell Stock III](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iii/description/)
1. 給定一個 price array, ele 是第 i 天的 股價
2. 某天買，未來某一天賣
3. 求最大profit
4. 最多可以進行兩筆交易  (**121 是一筆， 122 是多筆，123 是兩筆 XD**)
    1. 不可以同時進行交易, 一定賣出，才可以買
```ts fold
/**
 * 這一題 leetcode 要求 perf, 需要優化
 */
function maxProfit(prices: number[]): number {
  let mem = new Map(); // use map 優化

  // 我們直接用 交易數量當做變數去追蹤
  let dfs = (i, isStockAtHand, dealCount) => {
    let key = String(i) + String(isStockAtHand) + String(dealCount); // 優化 via use String  <---這個優化其實就夠可以 pass 了 XD 未必要 Map
    if (mem.has(key)) return mem.get(key);

    if (dealCount === 2) return 0; // 兩筆到了， done
    if (i === prices.length) return 0;

    if (isStockAtHand === false) {
      // 買或不買
      let buyStock = dfs(i + 1, true, dealCount) - prices[i];
      let noBuy = dfs(i + 1, isStockAtHand, dealCount);
      let curProfit = Math.max(buyStock, noBuy);
      mem.set(key, curProfit);
      return curProfit;
    } else {
      // 賣或不賣
      // 賣了表示完成一個交易(買->賣) -> dealCount + 1
      let sellStack = dfs(i + 1, false, dealCount + 1) + prices[i];
      let noSell = dfs(i + 1, isStockAtHand, dealCount);
      let curProfit = Math.max(sellStack, noSell);
      mem.set(key, curProfit);
      return curProfit;
    }
  };

  return dfs(0, false, 0);
}


```

### 188. [Best Time to Buy and Sell Stock IV](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-iv/)
1. 給定一個 price array, ele 是第 i 天的 股價
2. 某天買，未來某一天賣
3. 求最大profit
4. 最多可以進行 k 筆交易  **(121 是一筆， 122 是多筆，123 是兩筆,  188是 k 筆)**
    1. 不可以同時進行交易, 一定賣出，才可以買
```ts fold
function maxProfit(k: number, prices: number[]): number {
  // code 跟 122 幾乎一模一樣了, 只差 k 變數的判斷
  let memo = {};
  let dfs = (i, isStockAtHand, dealCount) => {
    if (dealCount === k) return 0;

    if (i === prices.length) return 0;

    let key = i + "," + isStockAtHand + "," + dealCount;
    if (key in memo) return memo[key];

    if (isStockAtHand === false) {
      let buyStock = dfs(i + 1, true, dealCount) - prices[i];
      let noBuy = dfs(i + 1, isStockAtHand, dealCount);
      let curProfit = Math.max(buyStock, noBuy);
      memo[key] = curProfit;
      return curProfit;
    } else {
      let sellStack = dfs(i + 1, false, dealCount + 1) + prices[i];
      let noSell = dfs(i + 1, isStockAtHand, dealCount);
      let curProfit = Math.max(sellStack, noSell);
      memo[key] = curProfit;
      return curProfit;
    }
  };

  return dfs(0, false, 0);
}


```


### 309. [Best Time to Buy and Sell Stock with Cooldown](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

1. 給定一個 price array, ele 是第 i 天的 股價
2. 某天買，未來某一天賣
3. 求最大profit
4. 可以進行多筆交易
    1. 不可以同時進行交易, 一定賣出，才可以買
5. **賣股票後，你無法再下一天買股票 (with Cooldown)**
6. 跟 121..等題目的的差異
	- 沒有交易筆數的限制 → 因此不需要帶上 dealCount 之類的狀態了
	- Cooldown機制

```ts fold

function maxProfit(prices: number[]): number {
  let memo = {};

  function dfs(i, isStockAtHand) {
    let key = i + "," + isStockAtHand;
    if (key in memo) return memo[key];
    if (i >= prices.length) return 0;  // 這邊要大於等於, 會多了 "大於" 是因為我們有可能 i+2, 會跳過 prices.length

    if (isStockAtHand === false) {
      // 買或不買
      let buyProfit = dfs(i + 1, true) - prices[i];
      let noBuy = dfs(i + 1, isStockAtHand);
      let curMaxProfit = Math.max(noBuy, buyProfit);
      memo[key] = curMaxProfit;
      return memo[key];
    } else {
      // 賣或不賣
      // 如果賣了, 需要有一天的 cool down, so i+2
      let sellProfit = dfs(i + 2, false) + prices[i];
      let noSell = dfs(i + 1, isStockAtHand);
      let curMaxProfit = Math.max(noSell, sellProfit);
      memo[key] = curMaxProfit;
      return memo[key];
    }
  }

  return dfs(0, false);
}

```


### 714. [Best Time to Buy and Sell Stock with Transaction Fee](https://leetcode.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)
1. 給定一個 price array, ele 是第 i 天的 股價
2. 某天買，未來某一天賣
3. 求最大profit
4. 可以進行 多筆交易
    1. 不可以同時進行交易, 一定賣出，才可以買
5. **交易會有手續費 `fee`** 
6. 跟上面的差異是
	- 沒有 dealCount 的限制
	- 沒有 cool down
	- 有 `fee`
```ts fold

function maxProfit(prices: number[], fee: number): number {
    
    
    let memo = {}
    let dfs = (i, isStockAtHand) => {
        let key = i+","+isStockAtHand
        if(key in memo) return memo[key]
        
        if(i === prices.length) return 0
        
        if(isStockAtHand === false){
            let buyStock = dfs(i+1, true) - prices[i]
            let noBuy = dfs(i+1, isStockAtHand)
            let curProfit = Math.max(buyStock, noBuy)
            memo[key] = curProfit
            return curProfit
        } else {   // cannot buy, meaning you still keek
            let sellStack = dfs(i+1, false) + prices[i] - fee
            let noSell = dfs(i+1, isStockAtHand)
            let curProfit = Math.max(sellStack, noSell)
            memo[key] = curProfit
            return curProfit
        }
        
    }
    
    return dfs(0, false)
    
    
   
};


```
