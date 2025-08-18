
[[source code reading]]





- [https://github.com/hunterloftis/stoppable/tree/master](https://github.com/hunterloftis/stoppable/tree/master)
	1. Node's `server.close` the way you expected it to work. → 幫你把 node 的 service.close 處理好
		1. Stoppable 停止接受新連線並關閉現有的空閒連線（包括保持活動），而不會終止正在進行的請求。
	2. 維護一個map, socket to req
	3. 可以設定 grace period
	4. 期間內來的 req, 還可以繼續來
	5. idel connection/socket 可以 close 掉






- [sindresorhus/meow: 🐈 CLI app helper](https://github.com/sindresorhus/meow?tab=readme-ov-file) 好像 work on JS only


- [Emma](https://github.com/maticzav/emma-cli) – Interactive CLI to search and install npm packages
- [ntl](https://github.com/ruyadorno/ntl) – Interactive cli menu to list/run npm tasks
- [itrash](https://github.com/ruyadorno/itrash) – Interactively selects files to delete from current folder in the CLI
- [ipt](https://github.com/ruyadorno/ipt) – Interactive Pipe To: The Node.js cli interactive workflow
- wana learn how to make this cli [maticzav/emma-cli: 📦 Terminal assistant to find and install node packages.](https://github.com/maticzav/emma-cli)
- [https://github.com/sindresorhus/is-online](https://github.com/sindresorhus/is-online)
- [https://github.com/sindresorhus/is-reachable](https://github.com/sindresorhus/is-reachable)




- [https://www.npmjs.com/package/copy-file](https://www.npmjs.com/package/copy-file)
2. what is `inode`?
	1. it’s index node, repr linux file and dir
	2. `fs.statSync` can see `ino` , this is inode number
3. a good desing
	1. whenever it use the node.js fs code(more lower layer), it setup a fs.js to encapulate these work and also handle try-catch and throw good err msg at that layer. 
	2. And index.js will more easily focus on copyFile logic and no need to handle many lower-level stuff and try catch stuff..
4. use `pEvent` to wrap many event-based async code
5. we have `import realFSPromises from 'node:fs/promises';` to use…
6. fs.lstat → to get state on symlik (compare to fs.state), use fs.utime to update the modified time of a flle
7.  We have fs.mkdir built-in and its promisfy: `const makeDirectoryP = promisify(fs.mkdir);`
8. how to check if the file is file or dir?
	- code
```tsx fold

// to get file state
export const lstatSync = path => {
	try {
		return fs.statSync(path);
	} catch (error) {
		throw new CopyFileError(
			`stat \\`${path}\\` failed: ${error.message}`, {cause: error});
	}
};

// use ifFile to check
const checkSourceIsFile = (stat, source) => {
	if (!stat.isFile()) {
		throw Object.assign(new CopyFileError(`EISDIR: illegal operation on a directory '${source}'`), {
			errno: -21,
			code: 'EISDIR',
			source,
		});
	}
};
```		
```

1. .npmrc and also use **`package-lock=false`**
	1. so no lock gnerertaed
2. opt to choose dest can be overwritten or not
3. async mode can have a progress and its progress obj to see (percentage, byte written..)
4. defualt nodejs copyFileSync seems have issue about file owenship, chmod → this pkg also solve this by not altering the file ownership
5. COW is not about os support, it’s about file sys support
	1. ext4 is not support
	2. zfs, btrFS, xFS, all of are linux file system, support
	3. on mac, apfs supoort, but hfs and hfs+ are not
6. node support cow and cow-force (cow is copy on write)
	- cow means, copy file share the same file in the file system
7. ‣ 1. 2. by default, open file, read a dir is a async 
	1. op 3. means: 
		1. it fire and will no block other ops 
		2. it will need to wait….and happen 
		3. in nodejs, async fn means it ret a promise 
		4. if u await, it will wait and then go to the next line of the code 
			1. but this is not blocking other async op 
			2. if you don’t await, the next line of the code will conti. run thru 4. and we can make those operations into queue → why? 
				1. since we want to control the rate in which those operation happen 
				2. we don’t wana the underlying resouce (in this case, the file system) take too much req 
				3. EMFILE is the error we could have, it means: a proc can open certain number of file decriptor and when it open too much FD, this err occur





- [sindresorhus/get-stdin: Get stdin as a string or buffer](https://github.com/sindresorhus/get-stdin/tree/main)
- `stdin.isTTY`
	- true → it means the node proc is connected from terminal
	- false → it means the process is connected from another input souce, maybe typing
	- ex
```tsx fold
const getStdinBuffer = async () => {
  // 連接tty, 直接返回 buffer w/ 0 bytes
	if (stdin.isTTY) {
		return Buffer.alloc(0);
	}
  
  // something goes in..
	const result = [];
	let length = 0;

	for await (const chunk of stdin) {
		result.push(chunk);
		length += chunk.length;
	}
  // 要提供給buffer bytes and length
	return Buffer.concat(result, length);
};




- [sindresorhus/conf: Simple config handling for your app or module](https://github.com/sindresorhus/conf)
	- 先跳過….還滿複雜的..
	- `ajv` → zod 的上一代
	- [sindresorhus/crypto-random-string: Generate a cryptographically strong random string](https://github.com/sindresorhus/crypto-random-string#readme)
		- 不太懂…..為何要那樣去組合
	- [fabiospampinato/atomically: Read and write files atomically and reliably.](https://github.com/fabiospampinato/atomically#readme)
		- linux 底層 syscall
			- 提供 fsyncWait
				- fsync 可以讓你非同步更快的寫資料, 但因為是非同步（寫入，可以繼續動作，但真正會有一個bg proc 來背景找時機flush to disk
					- 缺點？如果你馬上就要讀的話…會讀到old data
				- fsyncWait handle 上面的情況，如果要讀剛剛寫入的，會等
				- 因此 fsyncWait 比較安全（因為避免了上面描述的情況）
				- and if you don’t read after write → it is faster, in most case





	- [ ] xo
		- no need to config, unlike eslint (eslint need to config to use? I forget..)
		- more ez to see err output indeed
		- ts support by default → better then eslint
		


- [ ] why this is good? [https://github.com/ai/nanoid](https://github.com/ai/nanoid)






- [https://github.com/theurgi/fzf-ecma](https://github.com/theurgi/fzf-ecma)
	- node ver
		- use node.process.spwan
		- promise ctor to wrap event
			- return changes to resolve
			- use child proc stdout.on(data) to receive data from stdout
		- use `.filter(Boolean)` to filter out empty string
		- this two line to take stdin
```sql  fold
fzf.stdin.write(selections.join('\\n'))
fzf.stdin.end()
```
- bun ver
	- use Bun.spawn instead of spawn
	- use `new Response(fzf.stdout).text()` to get the stdout and covert to txt, no need to use event + promise ctor
	- result: cleaner impl
- find SS’s page on queue job 類似的package, 看看如何實作 job queue in node.js






	
	- [https://github.com/sindresorhus/p-queue](https://github.com/sindresorhus/p-queue)
		- 底層用一個 in mem的 priority queue
			- `/Users/re4388/project/personal/git-clone-pjt/p-queue/source/priority-queue.ts`
		- queue 裡面的 單位
			- 一個 函數, 一個 priority: number
			```tsx fold
			const element = {
				priority: options.priority,
				run: runFunction,
			};
			```
		- 看一下 enqueue 感受一下
			```tsx fold
				// Enqueue a run function with optional priority options
				enqueue(
					runFunction: RunFunction,
					options?: Partial<PriorityQueueOptions>,
				): void {
					// 1. Set default priority to 0 if not provided
					options = {
						priority: 0,
						...options,
					};
			
					// 2. create an element with priority and run function
					const element = {
						priority: options.priority,
						run: runFunction,
					};
			
					// push 入 queue 的條件
					if (
						// 1. If the queue is not empty
						this.size &&
						// 2. the last element has higher or equal priority
						// 意思就是，如果最低的 priority 是 5，那麼 5 以下的都不用繼續往下進行了，直接 push 後 return
						this.#queue[this.size - 1]!.priority! >= options.priority!
					) {
						this.#queue.push(element);
						return;
					}
			
					// Find the correct index to insert the element using lowerBound function
					// lowerBound 用二分兩找到適合的位置插入
					const index = lowerBound(
						this.#queue,
						element,
						(a: Readonly<PriorityQueueOptions>, b: Readonly<PriorityQueueOptions>) =>
							b.priority! - a.priority!,
					);
					// splice 用來插入元素, 第一個參數是 index, 第二個參數是 0, 代表不刪除任何元素, 第三個參數是要插入的元素
					this.#queue.splice(index, 0, element);
				}
			
			```