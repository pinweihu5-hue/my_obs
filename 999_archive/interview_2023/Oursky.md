- https://www.oursky.com/ **Queenie So <queenie@oursky.com>  回信，包括以下資料**
    
    I first learn to "code" in college when I need to use statistical software for analyses, about 15 years ago. 
    
    Then I found python gives me more power, so I began to learn python. 
    
    And 5 years ago, I learn JS when I know I can make my website and build many things on the web. 
    
    I 100% self-learn and I found it's a good idea to discuss with others and learn from each other (like I do at work)


    
    ```sql
    I will present below two small weekend build projects:
    https://github.com/re4388/calories-awareness
    https://github.com/re4388/word-cloud
    ```
    
    ```sql
    In the last two years, I continuously improved my skill in Typescript, Angular, and AWS at work. 
    Off-duty hours, I read The Rust Programming Language,  take  https://pwn.college/  course.
    ```
    
    ```sql
    I like to do side projects to help me facilitate my workflow!
    
    I like to use Notion to organize my learning notes. As a coder, I also like to use GitHub to put my notes. What if every time I post something on GitHub, the post on GitHub auto-updates to my Notion? 
    
    It turns out I can do that! 
    
    See the below code which helps me re-post any Github article I wrote to my Notion database via Notion API.
    
    https://github.com/re4388/re4388/blob/master/src/updateNotionFromTIL.ts#L7
    ```
    
    - job apply




- 3 question test
    
    https://gist.github.com/re4388/b2e99257883b1f862b52cb443d6fef13
    
    - q1
        
        ```js fold
        /**
        Your answer will be evaluated based on:
        
        Correctness of your solutions
        Cleanness of your code
        Expressiveness and clarity of your logic.
        
        Please be reminded correctness of solutions is not the only evaluation criteria,
        especially for experienced candidates. Some common problems include
        misunderstanding the questions, failure to consider edge cases of the problems, and
        obvious redundant logic or data structures. 
        
         * 
         * 
        Write a function that takes two arrays as input, each array contains a list of A-Z; Your
        program should return True if the 2nd array is a subset of 1st array or False if not.
        For example:
        isSubset([A,B,C,D,E], [A,E,D,D]) = true
        isSubset([A,B,C,D,E], [A,D,Z]) = false
        isSubset([A,D,E], [A,A,D,E]) = true
        
        Please explain the computational complexity of your answer in Big-O notation, i.e. O(log
        n) or O(n ^2)?
        
         * 
         * 
         * 
         */
        
        // [A,B,C,D,E]
        // [A,E,D]
        
        // way1 
        // add l1 into set
        // check if l2 ele in l2
        // SC l1
        // TC O(l1+l2)
        
        /**
         * 
         * isSubset:
         * return True if the arr2 is a subset of arr1 or False if not
         * 
         * Space complexity: O(N), N length of arr1
         * Time complexity: O(N+M), N is length of arr1, M is length of arr2
         *
         * To create a set, we need to traversal arr1, which is O(arr1.length)
         * To check arr2, we also need to traversal arr2, which is O(arr2.length)
         * 
         */
        function isSubset(arr1, arr2) {
            let arr1Set = new Set(arr1)
            for (let ele of arr2) {
                if (arr1Set.has(ele) === false) return false
            }
            return true
        }
        
        /**
         * 
         * isSubset version2
         * return True if the arr2 is a subset of arr1 or False if not
         * 
         * Space complexity: O(1)
         * Time complexity: O(N*M), N is length of arr1, M is length of arr2
         *
         * compared to version1:
         * 1. I think it is more expressive:
         *  For every element in arr2, we check if arr1 include it.
         *  The code speak what we want to do
         *  
         * 2. However, we got O(N*M) Time complexity compare to version
         * 
         * 3. Better Space complexity, no extra space needed.
         * 
         */
        function isSubset2(arr1, arr2) {
            return arr2.every(arr2Ele => arr1.includes(arr2Ele))
        }
        
        let r1 = isSubset(["A", "B", "C", "D", "E"], ["A", "E", "D"])
        console.log("r1", r1);
        let r2 = isSubset(["A", "B", "C", "D", "E"], ["A", "D", "Z"])
        console.log("r2", r2);
        let r3 = isSubset(["A", "D", "E"], ["A", "A", "D", "E"])
        console.log("r3", r3);
        
        let r4 = isSubset(["A", "B", "C", "D", "E"], ["A", "E", "D", "D"])
        console.log("r4", r4);
        
        // q1
        // https://gist.github.com/DebugTsang/221ad060589dc4bebf3b2bfde5002a90
        // https://gist.github.com/nweat/c06e0f2f9bb676e0e6386133f7f44a59
        
        // q2
        // https://gist.github.com/nweat/154a8b0be537725877bf580148a188a5
        // https://pastebin.com/MbYqVfyJ
        
        // q3
        // https://stackoverflow.com/questions/69326461/how-to-avoid-using-recursive-function
        // https://dotnetfiddle.net/vXhjcN
        ```
        
    - q2
        
        ```js fold
        /**
         
        Design and implement a data structure for a cache.
        
        get(key):
            Get the value of the key if the key exists in the cache otherwise, return -1
        
        put(key, value, weight):
            Set or insert the value; when the cache reaches its capacity, 
            it should invalidate the least scored key. The score is calculated as:
                when current_time != last_access_time:  
                    weight ∕ ln(current_time - last_access_time + 1)
                else:  
                    weight ∕ -100
        
        Design Overview:
        
        1. we setup a CacheNode class, with key, value, weight and lastAccess fields, which
        can make our code cleaner to store all relevant info in one place.
        
        3. we setup a _KeysArr, which will put all key sorted by scored and this help us
        to easily pop the least-score key when the cache reaches its capacity.
        
        3. we use a _hashmap (via JS object) for a mapping relationship: key -> CacheNode, which
        help us have O(1) for get(key)
        
        4. we maintain a _itemKeys array and sort it to help us get the least-scored key. 
        So, put(key, value, weight) has O(Nlog(N)) since we need to sort it. 
        
        Further improvement:
        We can swap Oject datatype to Map datatype if we want to allow more complex data type as key.
        Map datatype can put more complex data type as key, while Object only allow symbol and string as key.
        
         */
        
        type Key = string
        interface HashMap {
            [key: string]: CacheNode
        }
        
        class CacheNode {
            key: Key
            value: number
            weight: number
            lastAccess: number
            constructor(key: string, value: number, weight: number, lastAccess: number) {
                this.key = key
                this.value = value
                this.weight = weight
                this.lastAccess = lastAccess
            }
        }
        
        class MyCache {
            _hashmap: HashMap
            _KeysArr: Key[]
            _capacity: number
        
            constructor(capacity = 4) {
                this._hashmap = {}
                this._KeysArr = []
                this._capacity = capacity
            }
        
            _now() {
                return Date.now()
            }
        
            get(key: string) {
                if (!(key in this._hashmap)) return -1
        
                let node = this._hashmap[key]
                node.lastAccess = this._now()
                return node.value
            }
        
            put(key: string, value: number, weight: number) {
                console.log(this._KeysArr)
                let newNode = new CacheNode(key, value, weight, this._now())
                this._hashmap[newNode.key] = newNode
                this._KeysArr.push(newNode.key)
        
                if (this._KeysArr.length > this._capacity) this._cacheEvict()
        
            }
        
            _cacheEvict() {
                this._sortItems()
                let droppedKey = this._KeysArr.pop()
                console.log("droppedKey", droppedKey);
                delete this._hashmap[droppedKey!]
        
            }
        
            _sortItems() {
                this._KeysArr.sort((a, b) => this._getScore(b) - this._getScore(a))
            }
        
            _getScore(key: string) {
                let node: CacheNode = this._hashmap[key]
                if (this._now() !== node.lastAccess) {
                    return node.weight / Math.log(this._now() - node.lastAccess + 1)
                } else {
                    return node.weight / -100
                }
            }
        }
        
        // test case
        let c = new MyCache()
        c.put('0', 0, 10000)
        c.put('1', 1, 10000)
        c.put('2', 2, 10000)
        c.put('3', 3, 10000)
        c.put('4', 4, 10000)
        c.put('5', 5, 10000)
        
        console.log(c.get("4"));
        ```
        
    - q3
        
        ```js fold
        function recur(n, cur) {
            if (!cur) cur = 0;
            if (n < 2) throw new Error('Invalid input');
            if (n === 2) return 1 / n + cur;
            return recur(n - 1, cur + 1 / (n * (n - 1)));
        }
        
        function nonRecursiveFunction(n, cur) {
            if (!cur) cur = 0;
            if (n < 2) throw new Error('Invalid input');
        
            // n=3 enter eonce
            while (n > 2) {
                console.log("n", n);
                cur = cur + 1 / (n * (n - 1));
                n--;
            }
            return 1 / n + cur;
        }
        
        // ans
        function iterativeFn(n, cur) {
            if (!cur) cur = 0;
            if (n < 2) throw new Error('Invalid input');
        
            for (let i = n; i > 2; i--) {
                cur = cur + 1 / (n * (n - 1));
                n--
            }
        
            return 1 / n + cur;
        }
        
        // function check() {
        //     for (let i = -100; i < 200; i++) {
        //         for (let j = 100; j < -100; j--) {
        //             if (!(nonRecursiveFunction2(i, j) === recur(i, j))) return false
        //         }
        
        //     }
        //     return true
        
        // }
        
        // check()
        // console.log("check()", check());
        console.log(nonRecursiveFunction(2, -3) === recur(2, -3));
        console.log(iterativeFn(2, -3) === recur(2, -3));
        ```
        
    - ref
        https://gist.github.com/ng-kode/5d2ecc94903c109ba425e450b2bc2e7c