
[(796) Code Review, you said? - Venkat Subramaniam - YouTube](https://www.youtube.com/watch?v=OLp4yecQp2Y&list=WL&index=2)


TIL (2024_12_08_15_41_05)
- **不要說人家的 code 是 bad and ugly code -> this make other 覺得你是會說這個話的人 -> 你會不太想要給這個人做 code review -> 你會不太想要從這個人拿到 feedback**(like jack said, they have their ctx)
	- **gossip make us poor and not make us better, buz that is not a good reflection of our mind**
- when u find a bug? -> write test to make it fail 
- Go beyond coding styles
- get feedback early
- Review incrementally
- don't tie feature release to code review
- what is agile -> feedback driven development



---

# First Law Of Programming
Lowering quality lengthens development time.

we need quality code to be agile (ez to handle change)


# what is aglie anyway?
it's not standup meeting?
it's scrum?
it's velocity?

it's feedback driven development!


type of feedback? 
- active use of product
- automated test
- code review

code reivew,  dev 會害怕


code review is next best to pair rotation

# benefit
- for writer
	- another pair of eye
	- make u write readable code
	- learm from other
- reviewer
	- help other leran
	- learn things from the code
	- get familir with applicaiton overall
- team
	- reduce trunk factor / collective owerhsip / reduce SPOF 
	- 如果有人被 tunck run over, 會不會 application 就很難開發了?

how to code review
- never review code unless
	- compied
	- test passed
	- linter passed


review start with the tests: (test drvien review XD)
- Are the tests easy to understand
- Are they short?
- Does it have a good structure - AAA (arrange, act, assert)
- Do tests tell the story of design evolution?
- Are there missing tests?


when to review?

# don't tie feature release to code review 
(就是規定說，if you don't review ur code, you cannot merge to dev)

boss come in..
why is this fearure is not release?
why we have little story points?

oh...it's pending on code review..
okay, review it now!
(1 sec later) LGTM!

tdd -> threat driven development

code review is effective when developer ask for it
(you give ppl u want them to check, it's a humbling expereince and you know u will be smarter when they see what you can't see)


# Review incrementally
Show a little code, get a lot of feedback. 
Show a lot of code, get only a little feedback. 



# get feedback early
Ask early and frequently if you want feedback, Ask in the end if you want blessings, this is code blessing XD


Be continuous; not episodic (這邊是說就是變成一個習慣，不是偶發的，這樣大家會比較有默契)
Review in stages, not in features (只要有覺得需要 feedback, 一個階段就可以請人幫看，不一定要全部都做好再叫人 review, 這樣太慢了)



# Go beyond coding styles:
Where are the {lies?
tabs vs. spaces?


# other to find
Get that out of the way first, then
are the variable names good
are the method names descriptive but short
is the code too long
does it have a good level of abstraction
is it easy to understand
look for cohesion and coupling
Check those YAGNls
Keep it DRY



# when u find a bug? -> write test to make it fail 


作者有一次覺得有 bug, 寫 test -> 但沒錯
但是他搞錯了
因此變成，看看是否可以  refactor to make it more ez to understand



# Be constructive
don't say what is terribfe
instead says what can be better, how, and why

use "we" not "u"
can "we" do it? not can "u" do it?


if too slow, 也可以 concall, pair review, can have better interaction

# make it safe to be honest

**不要說人家的 code 是 bad and ugly code -> this make other 覺得你是會說這個話的人 -> 你會不太想要給這個人做 code review -> 你會不太想要從這個人拿到 feedback**
(like jack said, they have their ctx)

> **gossip make us poor and not make us better, buz that is not a good reflection of our mind**


