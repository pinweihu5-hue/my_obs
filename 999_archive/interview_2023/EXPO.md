- wemo db design test
    
    ```JavaScript fold
    customer cna have brand (KFC, MD, BK)
    each brand will have store (KFC 20 stores, MD 15 stores, BK 30 stores)
    
    employee (
      two kind
    	first kind: Chef, server, General Manager: only in 1 store
      seoncd kind: Regional Manager, COO: manage multiple stores
    )
    
    =======
    
    create table employee  {
      id: uuid
      name (Ben)
      employee_title
    }
    
    create table employee_title {
      id: uuid
      name (ex COO)
      employee_type (mgmt_level)
    }
    
    create table employee_type  {
      id: uuid
      typeName: [2 type, staff_level, mgmt_level]
      store_id: 12
    }
    
    create table employee_store  {
      id: uuid
      employee_id
      store_id
    }
    
    
    create table customer  {
      id: uuid
      name
    }
    
    create table brand {
      id: uuid
      name
      customer_id
    }
    
    create table store  {
      id: uuid
      name
      brand_id
    }
    ```
    
- wemo stack ⇒ queue ⇒ stack
    
    ```JavaScript fold
    // stack, queue
    // stack ==> queue
    
    // 1. MyStack
    // 2. MyStack ==> MyQueue
    // 3. MyQueue ==> MyStack2
    
    
    
    
    class MyStack {
      constructor(){
        this.a1 = []
      }
    
      push(val){
        this.a1.push(val)
      }
    
      pop(){
        return this.a1.pop()
      }
    
      empty(){
        if(this.a1.length === 0) return true
        else return false
      }
    }
    
    // const s1 = new MyStack()
    
    // s1.push(1)
    // s1.push(2)
    // s1.push(3)
    // console.log(s1)
    // s1.pop()
    // s1.pop()
    // console.log(s1)
    
    
    class MyQueue {
      constructor(){
        this.s1 = new MyStack()
        this.s2 = new MyStack()
      }
    
      push(val){
        this.s1.push(val)
      }
    
      shift(){
        if(this.s2.empty() === true) this.convert()
        return this.s2.pop()
      }
    
      convert(){
        while(this.s1.empty() === false){
          this.s2.push(this.s1.pop())
        }
      }
    
      empty(){
        if( this.s1.empty() && this.s2.empty() ) return true
        else return false
      }
    }
    
    // let q1 = new MyQueue()
    // q1.push(1)
    // q1.push(2)
    // q1.push(3)
    // console.log(q1) // 1,2,3
    // console.log(q1.shift())
    // console.log(q1.shift())
    // console.log(q1) // 3
    
    // q1.push(4)
    // q1.push(5)
    
    // console.log(q1.shift())
    // console.log(q1.shift())
    // console.log(q1.shift())
    // console.log(q1.shift())
    
    
    
    // queue
    // [1,2,3,4]
    
    // 1 
    // 2 
    // 3 
    // 4
    
    
    // [1,2,3,4]
    
    
    
    
    class MyStack2 {
      constructor(){
        // push, shift
        this.q1 = new MyQueue()
        this.q2 = new MyQueue()
        this.size = 0
        // this.s2 = new MyStack()
      }
    
      // [1, 2, 3, 4]
      push(val){
        this.q1.push(val)
        this.q2.push(val)
      }
    
      // [1,2,3,4]
      pop(){
    
        // let size = 0
        while(this.q1.empty() === false){
          console.log("2")
          this.q1.shift()
          this.size +=1
        }
        
        console.log("size", this.size) // 4
    
    
        this.size -=1
    
        // console.log(size) // 3
        // console.log(this.q2) // 4
    
        while(this.size > 0){
          this.q2.shift()
          this.size -=1
        }
        // console.log(this.q2) // 4
    
        return this.q2.shift()
        // return this.q1.shift()
      }
    
      // convert(){
      //   while(this.s1.empty() === false){
      //     this.s2.push(this.s1.pop())
      //   }
      // }
    
      // empty(){
      //   if(this.s1.empty() && this.s2.empty()) return true
      //   else return false
      // }
    }
    
    
    let s2 = new MyStack2()
    s2.push(1)
    s2.push(2)
    s2.push(3)
    s2.push(4)
    
    console.log(s2.pop()) // 4
    // console.log(s2.pop())
    // console.log(s2.pop())
    ```
    
- 真懂，表示你可以會用，因為會用，才有價值的產生
    
    真懂，表示你可以會用，因為會用，才有價值的產生。
    
    很多東西，並沒有真的懂，沒想到直到面試，被問出來，我才知道我沒有真的很懂。
    
    像是 cache，或你問我normal form的判斷
    
    如果你用背的，沒用，要會用，不會用，就不算懂。
    
    看文章看懂，你可能還是不會用。
    
    你可能懂文章的邏輯，但是文章可能根本沒講到你用到時會出現的盲點，那也不算真懂。
    
    要看你用的場景是否很複雜，是否是 edge case，什麼叫做會用，有時候不好說。
    
    不過如果回歸到面試。面試的問題，大多數還是有一個不至於太edge 或複雜。因此還不用太顧慮edge case（除非就是在問如何處理某個edge case）
    
    總結：
    
    學新觀念：
    
    看多點文章，可以讓你看到不懂的角度，降低盲點。
    
    去實做，純理論沒實做的文章，或是你沒照文章去實做，太多盲點。多數都是看了不會懂。
    
    花時間學了，就要產生價值，如果沒有懂到可以產生價值的程度，不是浪費寶貴的時間了嗎？
    
- second-round algo
    
    - resuive 經驗
        - 標準的作法要把結果放在ret val
        - 關鍵在於何時要往上（ret）, 何時要往下 (call dfs)
    
	    second-round-algo
	    面試流程(Process)：
	    
	    面試題目(Interview Questions)：
	    https://leetcode.com/problems/decode-string/
	    
	    難易度：中等
	    
	    面試自我評分(Result)：
	    5~6分/10分
	    
	    上題要求要iterative 也要求用 recusive 兩種作法解。
	    因為我只練習過iterative, resursive的解法沒做過，
	    面試時沒有做出來@@
	    算是考兩題，一題有解出來，一題沒有。

    
- first round
 面試流程(Process)：
    
    talk about on current working job
    talk what's the gain to expect for new oppty
    
    
    面試題目(Interview Questions)：
    https://leetcode.com/problems/valid-parentheses/
    
    
    難易度：easy
    
    面試自我評分(Result)：
    6分/10分
    (coding test 因為盲點，所以stuck 一下...)
    




- intro
    
    ![[IMG-EXPO-20250810201319973.png|IMG-EXPO-20250810201319973.png]]
    
    cont
    
    ![[IMG-EXPO-20250810201323020.png|IMG-EXPO-20250810201323020.png]]
    
    cont
    
    ![[IMG-EXPO-20250810201323409.png|IMG-EXPO-20250810201323409.png]]
    
      
    
- introduction prep
    
    Hello, I am Ben Hu.
    
    I graduated from National University of Taiwan with a degree in Econamic.  
    Back in scoll, I had a changce to use Python to do some data crunhing and regession analysis that might my first exp on coding.  
    
    Since i gragrade from a more biz bg, I started my carrer from biz side.  
    My first work is at brazil, worink as a internaltonal purcharser, conrdinate b/n china  
    team and Bz team.  
    
    Later I back tp Taiwan, continu my biz career working as a key a/c mgmt, it's a b2b cmpany,  
    we mgf small component in pos/barcode machine. It has been 5 year working at this position, and I started as a entry a/c to finally become sales manager, leading a 4 ppl saels.team  
    
    However, I found out I always have a huge passion on coding, so, I continue pick up  
    my programing skill by build up samll projects, learning database, reading compiter sicnece book, finally, I swithc my carrer to dveloper.  
    
    My first dev work is at a startup,we try to build a medical img viwer. so I kind of like a jack of all trades, I do fd, using JS/Vue, do python for a backend, use sql database and also when the pd is more stable, I begin to work on cicd using Jenkins.
    
    Later I joined another company called calrio, it's more a internaltional team.  
    We have teammeter from inda, framce, uk, and us.  
    clarui is a world leader in clickal trial industry,  
    our's service is a medical img viwer, and in our, we main focus on backend.  
    we use servlerless architecture, using api gatewta, lambda, s3, ses, sqs, sns, dynaomdb  
    like 80% backend , 20 % fd, fd we use angular  
    
    The reason I am intersted in expo is cuz
    
    I think the tech stack is a very good fit, js/ts is my main lang  
    in recen yrs, most of my work is backend, backend archtecture design, api, db design  
    and also I like backend tech and would like to dive in into moree tech details  
    
    My interests are in Front End Engineering and I love to create beautiful and performant products with delightful user experiences.
    
    Back in school, I designed and built a web application, NUSMods which solves a huge problem of class and timetable planning every semester. It receives over a million pageviews a month and is used by over 40,000 NUS students and even some professors. It is built using a modern web technology stack - React, Redux, Jest, Babel, Flow, webpack and is mobile-responsive."
    
    I'm interested in the Front End Engineer role at Meta because I have been using Meta Open Source Front End technologies for a while now and am inspired by Meta's mission and Open Source culture.
    
    ===