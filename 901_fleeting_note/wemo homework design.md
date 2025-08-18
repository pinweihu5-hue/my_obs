[[wemo_interview, wemo candidate]]

---
# req
* 公司實作
  - 同一個 user 同時只能租一台車, 同一台車只能同時被一個 user 租賃
    - rent will record 這筆 rent is active or not (via his_flag or history flag)
    - rent 表有記錄 userId
    - 因此我們可以撈出不是 history flag 的車子，就是 active rent
	    - 然後看這個 userId 是否在裡面 
		    - 如果有，表示這個 user 正在租借中 -> throw err
    - rent 表也有記錄 scooter ID 
	    - if this scooter 在 rent 表有 active rent → throw err
  - 紀錄使用者租車的時間（起迄時間）
    - 目前公司實作
    - rent 表記錄起迄時間

# api
- POST user/register
	- {name: xx, password: xx} -> {result: success/fail}
- POST user/login
	- {name: xx, password: xx} -> {result: success/fail, {accesstoken: xxx}}
- GET rent/:id
	- need valid token in header
	- res: { rent_table_data_here}
- POST rent
	- need valid token in header
	- {type: rent, scooterId: xxx} -> {rentId: xxx}
- PATCH rent/:id
	- need valid token in header
	- {type:return} -> {result: success/fail, rentAmount: xxx}

# work flow
- user register
	- POST user/register
	- payload username and pass
	- if the same username already in db -> throw user_name_duplicate_err
	- hash pass + salt -> hashedPass
	- create a user
		- name
		- hashedPass
	- return success
	- [ ] other idea:
		- Oauth
		- more complicated auth, like wemo currently does
- user login
	- POST user/login
	- payload username and pass
	- hash pass + salt -> hashedPass
	- user userName to get the user entity from DB, check againt the hasehedPass
	- if found -> gen jwt token and return success with jwt token
	- if not found -> throw no_user_found_err
	- [ ] other idea:
		- if we want to revoke token, we can save into db/redis
- user rent a scooter
	- POST rent
	- need to bring token
		- with token -> get user id
		- get lock to ensure cocurrent issue
			- use dist-lock if multiple instance, or use in-mem lock for poc
		- check user is not in any isActive rent
		- check scooter is not in any isActive rent
		- create a rent
			- use scooterId, userId
			- isActive to true
			- beginAt = now
		- above db ops in a tx, to ensure atomic behavior
	- [ ] other idea:
		- check current wemo code and think about what is good to add
- user return a scooter
	- PATCH rent/:id
	- need to bring token
	- also get lock to ensure cocurrent 
		- shall be only one user (one proc) run, but client may retry and lead to cocurrent situation
	- calculate price (poc project, can just hardcoded to use $3 per min)
	- update rent table by rentId
		- rentAmont
		- isActive to false
		- endAt to now
	- [ ] other idea:
		- check current wemo code and think about what is good to add

# table
- scooter
	- id
	- plate
- rent
	- id
	- isActive(t/f)
	- beginAt(utc)
	- endAt(utc)
	- scooterId
	- userId
	- rentAmount
- user
	- id
	- name
	- hashedPass
		- user pass will hash + salt and put here
		- name + pass as user unqiue





## on db
- use lib to write entity code
- use lib_cli_tool to gen SQL against eneity code
- those SQL need to write into db (or automate via lib_cli_migration_tool)
- then you need to gen some data (or called seed)
- then you can use lib to query those data

lib could be typeorm, drizzle
example: [Use Drizzle ORM with Bun | Bun Examples](https://bun.sh/guides/ecosystem/drizzle)



一個比較複雜的 bun + elysia + drizzle + sqlite example
[GitHub - remuspoienar/bun-elysia-drizzle-sqlite: ElysiaJS + Bun + DrizzleORM (with bun:sqlite for sync/async database) codebase containing real world examples (CRUD, auth, advanced patterns, etc) that adheres to the RealWorld spec and API.](https://github.com/remuspoienar/bun-elysia-drizzle-sqlite)
