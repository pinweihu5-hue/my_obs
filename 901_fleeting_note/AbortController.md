[[backend_idx]]


---

# when to use it?
- 取消長時間運行的操作 -> timeout
	- like fetch does not have built-in -> can use this to impl
	- axios have built-in for timeout
		- 但可以自定義 timeout 邏輯 if using AbortController
- enable user to cancel the request
- 取消重複的請求:
	- 在某些場景下,可能會發生用戶快速連續觸發相同的請求,比如搜索輸入框。
	- 使用 AbortController 可以取消之前尚未完成的請求,避免出現多餘的網路請求和資源消耗。



# timeout example1
```js

// npm install abort-controller
// npm install abort-controller
const { AbortController, AbortSignal } = require('abort-controller');

const controller = new AbortController();
const { signal } = controller;

const timeoutId = setTimeout(() => {
  controller.abort();
}, 10000); // 10秒鐘超時

try {
  const response = await fetch(externalApiUrl, { signal });
  // 處理response
} catch (error) {
  if (error.name === 'AbortError') {
    console.log('請求已取消或超時');
  } else {
    console.error('請求失敗:', error);
  }
} finally {
  clearTimeout(timeoutId);
}

```


# timeout example2
```js

const timeout = (ms) => new Promise((_, reject) => {
  setTimeout(() => {
    reject(new Error(`Request timed out after ${ms} ms`));
  }, ms);
});

const fetchWithTimeout = (url, ms) => {
  return Promise.race([
    fetch(url),
    timeout(ms)
  ]);
};

fetchWithTimeout('https://example.com', 5000)
  .then(response => {
    // 處理響應
  })
  .catch(error => {
    if (error.message.includes('timed out')) {
      console.error('請求超時');
    } else {
      console.error('請求失敗:', error);
    }
  });

```



# axios ex

```js

const axios = require('axios');
const instance = axios.create({
  timeout: 5000 // 5 秒鐘超時
});

axios.get('https://example.com', {
  timeout: 3000 // 3 秒鐘超時
})
.then(response => {
  // 處理響應
})
.catch(error => {
  if (error.code === 'ECONNABORTED') {
    console.error('請求超時');
  } else {
    console.error('請求失敗:', error);
  }
});

```


# AbortController 取消重複請求的範例
```js

const axios = require('axios');
// npm install abort-controller
const { AbortController, AbortSignal } = require('abort-controller');


// 建立 AbortController 實例
const controller = new AbortController();
let currentRequestId;

// 處理搜索輸入框的事件
const handleSearchInput = async (event) => {
  const query = event.target.value;

  // 如果有未完成的請求,則取消它
  if (currentRequestId) {
    controller.abort();
  }

  try {
    // 設置新的 AbortSignal
    currentRequestId = controller.signal;

    // 發送搜索請求
    const response = await axios.get(`/search?q=${query}`, {
      signal: currentRequestId
    });

    // 處理搜索結果
    console.log(response.data);
  } catch (error) {
    // 檢查是否因為取消而拋出的錯誤
    if (axios.isCancel(error)) {
      console.log('搜索請求已取消');
    } else {
      console.error('搜索請求失敗:', error);
    }
  } finally {
    // 重置 currentRequestId
    currentRequestId = null;
  }
};

// 將事件處理函數綁定到輸入框
const searchInput = document.getElementById('searchInput');
searchInput.addEventListener('input', handleSearchInput);

```