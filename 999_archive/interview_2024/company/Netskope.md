
Hi Ben,
Thank you for taking the time to meet with us and for sharing your experiences and skills during the interview process. We truly appreciated your enthusiasm for technology and your thoughtful approach to problem-solving. Here are some of the highlights and areas where we believe you can further grow:

Strengths:

You showcased solid technical skills, particularly in microservices architecture, Kubernetes, and other relevant technologies.
Your ability to explain technical challenges and your coding performance, especially with test-driven development, were impressive.
We admire your positive attitude, adaptability, and willingness to learn, which make you a strong team player.
Your experience working across diverse teams and environments stood out as a great strength.
Growth Areas:

While your coding solutions were functional, there’s room to simplify them to make them more efficient for certain use cases.
We’d encourage you to focus more on articulating system design and the broader system flow before diving into implementation, as this can help bring more clarity to your thought process.
Strengthening your English communication, particularly when describing your past work experiences, could help convey your expertise more effectively in global settings.


Overall, your technical skills, problem-solving ability, and eagerness to learn leave us confident in your potential. We’re excited to see how you continue to grow and excel in your journey. Thank you again for your time and effort!

Harry Chang

Talent Acquisition



netspoke 沒有上，你的想法是什麼?


- 我覺得成本很大, 包括人資共有7個面試
- 我覺得我判斷失準???
	- 基本上每一次面試我都有解出問題
	- 有兩個可能，就是有更好的 candidate同時跟我在競爭
	- 然後我們兩個都走到最後一關
	- 然後他們只有一個缺，因此選了他
- 但是如果是有更好的 candidate, 那也不是我判斷失準，這是我無法控制的。
- 一個合理的解釋就是，他們有很多人都通過所有的面試，但是有比我更強的競爭對手搶這個位置
- 另一個可能的解釋就是我技術能力不夠，或是表達能力不夠，因此面試後，其實他們有看到 red flag, 因此我最後不給予錄取 -> 但是 unfortunatly, 我無法去 verify 這點
- 覺得很失落，也很不爽。
- 失落是因為跟我的預期有落差，感覺每一關都有關，題目都有解出來，技術的來回問答也沒有太多被問不出來的情況，但是最後卻沒上。
- 不爽是因為這個面試包括人資共有7場面是, 第一場是 10/15 人資開始，都快要兩個月了。
- 很大的打擊
	- 如果是外部因素 -> 那就是運氣不好
	- 如果是我自己的功力不夠 -> 首先我也不確定是不是，但也只能持續加強








# Software Engineer, Risk Insights

at Netskope [(View all jobs)](https://www.netskope.com/company/careers/open-positions/)


**About the role**

_Please note, this team is hiring across all levels and candidates are individually assessed and appropriately leveled based upon their skills and experience._

We develop complex, distributed, hybrid cloud systems that power our Risk Insights services. 

**These services provide customers with a multidimensional view of the applications, devices and users on their network.** We illuminate unsanctioned and unsupported apps and devices (Shadow IT), and we track user behaviors so customers can visualize and create policies to minimize risk. 

**What’s in it for you**

Do you thrive when owning services, products and projects? Do you enjoy designing simple, elegant solutions to complex problems in distributed systems? Are you passionate about building solutions that involve processing high volumes of data? Do you pride yourself on writing high quality code with automated tests as part of project delivery? Do your peers look for your expert advice in designing microservices?

If so, we have a great opportunity for you in the Risk Insights Team at Netskope that creates solutions in the SaaS/IaaS space. You will build the next generation of cloud services to power our cloud security risk assessment services and cloud-managed on-prem products.

**What you will be doing**

- Build horizontally scalable cloud services for processing high volumes of data while extracting unique insights for our customers
- Build cloud services to manage and monitor globally distributed on-prem products
- **Build APIs for natural and intuitive world class user experiences**
- Build a strong CI/CD environment with efficient development and scalable automation frameworks
- Interact with customers while understanding, building and resolving issues with their use cases
- Be a vocal owner of the services/products you work on while focusing on performance, availability, diagnostics, monitoring, security, etc.
- Lead/Mentor your peers as we grow the organization geographically

**Required skills and experience**

- 2+ years of demonstrable experience building services/products
- Effective verbal and written communication
- Comfortable with ambiguity and taking initiative to find and solve problems
- Programming with Python, Go, C/C++, etc.
- **Experience with CI/CD environments**, automation frameworks
- **Good understanding of distributed systems, data-structures and algorithms**
- Strong background in designing scalable services leveraged by millions of clients
- Experience working with and/or building automation frameworks
- **Experience in designing/building RESTful services**
- **Knowledge of network security, SQL/NoSQL databases**, authentication and authorization, etc.
- Familiarity with one or more data storage technologies (e.g. ceph, NFS, etc.)
- **Experience with docker containers, k8s, public clouds (GCP, AWS, Azure, etc.)**
- Experience building and debugging software on Unix/Linux platforms

**Education**

- BSCS or equivalent required, MSCS or equivalent strongly preferred

## Apply for this Job

* Required

Nauman Tahir
 最後一關
```js fold



/*

Input: he(l(lo)wo(rl)d
Output: hel(lo)wo(rl)d

Input: some))otherw((ord
Output: someotherword

Input: apple()()
Output: apple()()


*/


let res1 = removeBracket('he(l(lo)wo(rl)d')
console.assert(res1 === 'hel(lo)wo(rl)d')

// let res2 = removeBracket('some))otherw((ord')
// console.assert(res2 === 'someotherword')

// let res3 = removeBracket('apple()()')
// console.assert(res3 === 'apple()()')

// let res4 = removeBracket('')
// console.assert(res4 === '')


// some))otherw((ord
// 

/**

stack [), )]

 */


function removeBracket(s){
    let stack = []
    // let invalidIdices = new Set()

    for(let i = 0; i < s.length;i++){
        if(s[i] === '('){
            stack.push({char: s[i], index: i})
        } else if( s[i] === ")"){
            if(stack.length === 0){
                stack.push({char: s[i], index: i})
                // invalidIdices.add(i)
            } else { 
                // stack is not empty
                if(stack[stack.length-1].char === '('){
                    stack.pop()
                } else {
                    stack.push({char: s[i], index: i})
                }                
            }
        }
    }

    // while(stack.length > 0){
    //     invalidIdices.add(stack.pop().index)
    // }

    let result = ''
    console.log(stack)
    let invalidIdices = stack.map(obj => obj.index)
    console.log(invalidIdices)
 

    for(let i =0; i<s.length; i++){
        if(!invalidIdices.includes(i)){
            result += s[i]
        }
    }

    return result
}



// remove the the minimal number invalid bracket to make it valid


```




altitude to have:

ask the right Q to ask Person
ask feedback
take it obectively, not a big mistake
shall look for ownership, not your code, not my code, it’s everybody’s code
self-learner
self-starter
good team player
proactive

===

how to help?
relative simple task at begin
have time to ask question
slack channel






netSpoke 考題
[Fetching Title#7olx](https://gist.github.com/re4388/84a5b4ac8fa56aa8b93ae152b2907018)
```js fold

const _ = require('lodash');


// Best algo, minimiz memory usage.
// Q 1:
// 1. Fib 
// 1st:  1, 
// 2nd:  1
//       2, 3, 5, 8, .. 
// What is 100th Fib?

function fib(n){
    if(n < 0) throw new Error('Input shall be a non-negative interger')
    if(n === 0) return BigInt(0).toString()
    if(n === 1)  return BigInt(1).toString()
    
    let a = BigInt(0) 
    let b = BigInt(1)

    for(let i = 2; i<=n;i++){
        const tmp = a + b
        a = b
        b = tmp
    }
    return b.toString()
}

// const res = fib(100)
// console.log(res)
// 354224848179262000000

function runTestForQ1(){
    const testCase = [
        { input:0, expected: `0`},
        { input:1, expected: `1`},
        { input:2, expected: `1`},
        { input:3, expected: `2`},
        { input:4, expected: `3`},
        { input:5, expected: `5`},
        { input:6, expected: `8`},
        { input:40, expected: `102334155`},
        { input:50, expected: `12586269025`},
        { input:100, expected: `354224848179261915075`},
    ]

    testCase.forEach( ({input, expected}) => {
        let result = fib(input)
        console.assert(result === expected)
    })
}

runTestForQ1()




////////  Hi Liu, Question here ^^ ///////////////
// For Q1, Integer can not hold 100th fib, so I return String type, is it okay?







// Q 2:
// [1, 2, 2, 3, 4] sorted
// [2, 2, 3, 3] sorted
// Want: [1, 2, 3, 4] sorted, no-duplication


let a1 = [1, 2, 2, 3, 4]
let a2 = [2, 2, 3, 3]
function combinedAndDedup(arr1, arr2){
    let res  = []
    let i = 0
    let j = 0

    while(i < arr1.length && j < arr2.length){
        if(arr1[i] < arr2[j]){
            // add if it's not duplicate
            if(res.length===0 || res[res.length-1] !== arr1[i]){
                res.push(arr1[i])
            }
            i++
        } else if(arr1[i] > arr2[j]){
            if(res.length===0 || res[res.length-1] !== arr2[j]){
                res.push(arr2[j])
            }
            j++
        } else {
            if(res.length===0 || res[res.length-1] !== arr1[i]){
                res.push(arr1[i])
            }
            i++
            j++
        }
    }

    // add remaining ele from arr1
    while(i < arr1.length){
        if(res.length===0 || res[res.length-1] !== arr1[i]){
            res.push(arr1[i])
        }
        i++
    }



    // add remaining ele from arr2

    while(j < arr2.length){
        if(res.length===0 || res[res.length-1] !== arr2[j]){
            res.push(arr1[i])
        }
        j++
    }



    return res

}



function testForCombinedAndDedupTest1(){
    let a1 = [1, 2, 2, 3, 4]
    let a2 = [2, 2, 3, 3]
    const expected = [1, 2, 3, 4]
    const result = combinedAndDedup(a1, a2)
    console.assert(checkArrayEqual(result,expected))
}
testForCombinedAndDedupTest1()


function testForCombinedAndDedupTest2(){
    let a1 = [1, 2, 2, 3, 4,5,5,5,6,7,9,9,22]
    let a2 = [0,0,0,2, 2, 3, 3,9,9,22,42]
    const expected = [1, 2, 3, 4,5,6,7,9,22]
    const result = combinedAndDedup(a1, a2)
    console.log(result)
    console.assert(checkArrayEqual(result,expected))
}
testForCombinedAndDedupTest2()



function checkArrayEqual(a1, a2){
    if(a1.length !== a2.length) return false

    for(let i = 0; i<a1.length; i ++){
        if(a1[i]!== a2[i]) return false
    }

    return true
}








```





現場考題
- use OOP to write 停車場
- 買股票問題 -> use recusion 解


```js fold



// # Find the longest common prefix from an array
// # Example 1:

// # Input: ["flower","flow","flight"]
// # Output: "fl"
// # Example 2:

// # Input: ["dog","racecar","car"]
// # Output: "None"
    
// # Input = ["cooler", "co","corporation", "comment", "coop"] 
// # Output:”co"

let res1 = longestCommentPrefix(["flower","flow","flight"])
// assert(res1, 'fl')
console.log(res1) // fl

let res2 = longestCommentPrefix(["dog","racecar","car"])
console.log(res2) // None

let res3 = longestCommentPrefix( ["cooler", "co","corporation", "comment", "coop"] )
console.log(res3) // co

let res4 = longestCommentPrefix([])
console.log(res4)

let res5 = longestCommentPrefix("apple")
console.log(res5) // None

let res6 = longestCommentPrefix(["apple"])
console.log(res6) // "apple"


let res7 = longestCommentPrefix(null)
console.log(res7) // None




longestCommentPrefix(["flower","flow","flight"])
longestCommentPrefix(["flower","flow","flight"])

// str is
function longestCommentPrefix(str){
    if(!str || str.length === 0) {
        return  "None"
    }

    // use the first word to be compare
    let prefix = str[0]

    for(let i=0; i<str.length; i++){
        while(str[i].indexOf(prefix) !== 0){
            prefix  = prefix.substring(0, prefix.length-1)
            if(prefix === ""){
                return "None"
            }
        }
    }

    return prefix
}


```

