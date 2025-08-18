[[source code reading]]


- [p-retry - npm](https://www.npmjs.com/package/p-retry) → allow promise to retry?
	- 有 abort error 可以直接用
		- 用法？ 特定情境你沒有想要 retry, 類似 404 not found
```tsx fold
import pRetry, {AbortError} from 'p-retry';
import fetch from 'node-fetch';

const run = async () => {
	const response = await fetch('<https://sindresorhus.com/unicorn>');

	// Abort retrying if the resource doesn't exist
	if (response.status === 404) {
		throw new AbortError(response.statusText);
	}

	return response.blob();
};

console.log(await pRetry(run, {retries: 5}));
			```
			
- attemp 有 cb 方便 log 或是做一些記錄
```tsx fold
const run = async () => {
	const response = await fetch('<https://sindresorhus.com/unicorn>');

	if (!response.ok) {
		throw new Error(response.statusText);
	}

	return response.json();
};

const result = await pRetry(run, {
	onFailedAttempt: error => {
		console.log(`Attempt ${error.attemptNumber} failed. There are ${error.retriesLeft} retries left.`);
		// 1st request => Attempt 1 failed. There are 4 retries left.
		// 2nd request => Attempt 2 failed. There are 3 retries left.
		// …
	},
	retries: 5
});
```
	
- 可以根據特定情境才 retry
	```tsx fold
	const result = await pRetry(run, {
		shouldRetry: error => !(error instanceof CustomError); // true 才會 retry
	});
	```
	
- 可以 listen to abort event →類似可以用一個button 來觸發 event → abort retry
	```tsx fold
	import pRetry from 'p-retry';
	
	const run = async () => { … };
	const controller = new AbortController();
	
	cancelButton.addEventListener('click', () => {
		controller.abort(new Error('User clicked cancel button'));
	});
	
	try {
		await pRetry(run, {signal: controller.signal});
	} catch (error) {
		console.log(error.message);
		//=> 'User clicked cancel button'
	}
	```
	
- 裡面包了 `retry` [https://www.npmjs.com/package/retry](https://www.npmjs.com/package/retry)
- 也包了
- 看一下 test..
	- use [delay - npm](https://www.npmjs.com/package/delay)? why use delay?.. 不是很簡單嗎?
		- 多了可以 listen to abort evt to abort
		- ensure cleanup by setup clear up mechanism, save into weakMap
		- 還很貼心的讓你傳自定的 clearTimeout fn and setTimeout fn 進去