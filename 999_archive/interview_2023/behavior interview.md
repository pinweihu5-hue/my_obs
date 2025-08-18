[[interview_2023_idx]]

請你舉一個你遇過技術上的困難，然後說說你怎麼解掉的
S.T.A.R應答框架

Situation(背景)：描述此經歷的背景，是在何時發生、在什麼情況發生以及為何會發生。 Task(任務)：描述在這種情況下，你的目標是要完成什麼事情？ Action(執行)：確切的說明採取了什麼措施解決這個問題 Result(結果)：分享執行後的結果

- case 1 aws 前端畫面不一致，後端處裡出問題
    
    # Situation(背景)：描述此經歷的背景，是在何時發生、在什麼情況發生以及為何會發生。
    
    an urgent bug report by PM
    
    two thumbmail img can't not loaded properly in fd viewer
    
    and the errro msg is not show up correctly
    
    it will show thumbanil correct
    
    it will show not-availale
    
    it will show loading
    
    but the error msg showing these two state back and forth 2 times.
    
    it shall show not-availanle OR loading, but shall not show not-available and then loading
    
    it’s not fit to the story
    
    so there’s at least 2 layer:
    
    1. the error msg on thmbnial is not showing a unecpected way
    2. why we have error in the first place
    
    recap, our all is a webapp, which can show patenitent mr/ct img by giving it collection collectid is the query parameter
    
    the 99% of probelm is about a particle collection can't work properly in some way
    
    in this case, the thumbnail is not showup correctly
    
    meaming the backend preporcess (gen thumnamil got situation)
    
    and fd (sebrina team) inform us that this is related to backend
    
    cuz they see the backend error on dev tool
    
    # Task(任務)：描述在這種情況下，你的目標是要完成什麼事情？
    
    PM will provide the diagloig/msg record/info tell us
    
    ### 1. the first thing I will try to do is to see if I can reprocude the probelm
    
    so I go thru the diaglog, msg, report, information, which collection they said they got error and try to use webapp load the same collection, using the same qeury para
    
    if i can't reprocue the problem ,
    
    I need to comminucaite w/ our pm, re-exame the dialogue or bug report to see if anything I missing or misunderstand.
    
    in this case, I can
    
    ## 2. find which component cause error
    
    **open the dev tool check logging**
    
    if it’s backend log or fd log
    
    if it’s fd log if that is linked to backend
    
    if it’s backend log, it’s a gernal kind of log or it is more specfic
    
    in this case, I got a more general backend error
    
    but cuz in the backend, we had a error-collect logic, and this backend error shall tell me where the error come from which component
    
    so at this point, I know something wrong in thie error-collect logic and we need to fix that later
    
    **open more basic monitor sys to check log stream, cloud watch**
    
    so I need to call backend api and see in which component error happen
    
    review the architectie compoment diagam like archievetyre overvew know how the data flow and check component one by one, from up sream to down stream specifkcy, I check cloud watch so find error msg
    
    ## find exactly which line cause error and reason about
    
    finally, I find the error happened, which is a containernized lambda
    
    and i open the soure code and identify exactly which line of code raise the exception and guess what could cause this probelm by reading the log in detail, see the track stack, exception
    
    in this case, the error happen in a place where python call the underlying c++ binding
    
    we use c++ binding to do the heavly lifting of dicom parsing, dicom to thumbinal generation
    
    and I am really not expeince in c++ code and dicom lib related stuff in c++ and I can’t figuure why this part of c++ will raise error when reading this particule dicom file
    
    at this pt, i kind of know what happen and this is not my experiecen can solve by one own
    
    I need help from another team in france,
    
    so I need to contaxt a guy called Mathiue in france
    
    # Action(執行)：確切的說明採取了什麼措施解決這個問題
    
    ## communicate and colarative idenitfy the root case and come out a solution
    
    I provide the raw dicom file to allow him more easily to run up the c++ code to test the file
    
    I garb it from s3 path and provide to it
    
    I show him in which part of python raise error in which python call the binding c++ ccode which he had written
    
    =>
    
    after some check, Matheirun share the info
    
    this dicom is a strange foramt which is not possible to extract the thumbnail
    
    it’s a pdf actually
    
    how he is going to solve?
    
    he will need to create another api to let my python code to check the dicom idenityf and then I can know if this kind of dicom is possibe to extract thumbnail and if not, I just modify the ptuhon code to skip this kind the thumbnail gen process
    
    in short
    
    he will update the base layer of the docker img
    
    and i will update the python code to use some new featute of the c++ and
    
    check if the dicom can extra thumb nial or not
    
    - there's a another complicaiton
        
        there's a another complicaiton when i debug
        
        when this bug happen, we just finish some infra migration and org merge
        
        and we need to use another VPN and
        
        at that time, our team got probelm to connect to the VPN conncected serivce, privtae Gitlab
        
        in our WSL sys
        
        so when I when I try to idenity this bug, and comminucate with franece team on python ccode
        
        I do not have the abilty to run it up independely, modify, print it
        
        I olny can call API and check the log to figure out what’ts the provelm
        
        which make a slower as well
        
    
    ## always keep in mind the big picture, it’s not finished
    
    at this point, we know why the backend fail to precpess
    
    but we didn’t solve the bug
    
    in the report
    
    they also mentoned that the preprocess error got some inconsistent respone which make they error status show up in the wrong way
    
    this is strange due to the time-zone, I and another developer (who is more fimialir with the freond end code base wrt. thumbnail part
    
    and we toggther found:
    
    1. fd directly reply on backedn api result as a fd state mgmt to display some error/loading logic
    2. the fd will show error at stage 1 and loading at stage 2
    3. this is not what they expected ==
    
    the situaiton is like that
    
    fd call api
    
    the frist stage tell fd it’s not avalable (meaning backend said it is fail)
    
    since fd call api, we will retry →
    
    retry will turn fail into “processing”
    
    then the second api from fd
    
    will get the “propcesing”
    
    and fd
    
    use in “procerssing” to show loading img
    
    use “failed” to show “not available” img
    
    so the img show “not available” at first then switch to “loading” when the fd click that thumbnail
    
    (you may think it’s okay wrt. UX, if you click thumnal then it will retry)
    
    but the orignal story think the state shall be only one state from the beining to the end of the fd session
    
    ==
    
    To date, I am not sure how FD mgmt this?
    
    by update story or
    
    or by main snoather state to mgm the thumbnial display
    
    not sure
    
    beofre we do the source code checking, i suggest let's check the network tape and let's see what exactly backend send to fd
    
    and we confirm the preproess stauts indeed show fail at first step and loading in the second stage
    
    ### a little bg for our prepoecess 2 stage sys
    
    1. fist stage: fd load collection data
    2. second stage: it use colelction data, sepeate into sreies data and send req to backend for series data, it only load the data when user click it (lazy-loading)
    3. there’s some default img will show up when you open the website, so for those img, the 1 api call and second api call is run very fast like together.
    4. for each refresh of website, it will retry the loading if last time is at fail state
        1. if it is at sucess state, it won’t need to preprocess , it go s3 to get the cached img
    
    if the past, every work okay, we only get img and thmbil at second stage
    
    at some point, maybe half yr ago, we want to get the thumbnail faster
    
    we also return the thumbail at the first stage, this why strange things happen
    
    and we didn’t overse this when we fist moveing some preprocess result int the first step
    
    if that preprocess is fail
    
    the fd will get the preocess fail status back
    
    and the display will show the img is not avalianle
    
    but at the second api, since the baecend will retry
    
    and retry update the preporcess status to loading
    
    and that status also passed back to fd
    
    and fd will use that stauts to display the img is loading
    
    so the img will firstly appear it fail, and then it will appear it is loading
    
    which is not the what we want to see
    
    for our current mechiansm, if the proecess is fail, then it shall remian fail at this seesion
    
    (we do’t have sotry, for the moemnt to tell fd the img statys is sucess again after loading, the total number of api resp fd got is 2)
    
    so fd team also need to update this part
    
    and we need to commmuciate with them that’s how the current backend behavie
    
    if they want backend to modify or chnage behavior, thaty’s okay
    
    so we setup a meeting and tell them the cureent situaiton
    
    for the moment of writeing, we have have futrue sotry to hadnle this case.
    
    for now, I this the fd team will just use the first api resp as the valid status thru the session.
    
    # Result(結果)：分享執行後的結果
    
    there’s 4 new ticket or more to solve this bug
    
    we firstly fix the backend monitor system to allow us next time to know its python probelm (it turrn our there's logic error to collect ereror in ouor error gathering logic)
    
    the franchch team make a pull request to open another new API to enable our python code
    
    to idenity the probem dicom and we can skip it, no thumbanil/preprocess needed
    
    the pyton infra blocker is still here at the time of this wrting so it is still there, we have not solve the vpn block wsl issue and we need to solve it in another ticket
    
    the fd problem will need to handle in another team, i am not sure this fd bug new ticket will be handle, maybe in this srint in another team
    
    update to architect in case the change is alter the overall artchitectue
    
    - get buy in review and see if we can add something to prevent future re-occur or setup monitor
    
    # multi error
    
    1. log gather component logic fail -> when we update the logging system, for wahatver reason, we didn't update the root-cause component => so in thie case, that error msg cannot be properly handle and send it error log to front end / team channel monitor -> which make the debug slower
    
    2 dicom parse failiure and that code is not maintain by us -> it's about a parse dicom file error which our backend is not support for this particualr format, more precisely, this dicom format is preproatotty, which is usually cannot be handled propelry, in our parse logic, we need to adding another logic to detect this kind of special foramt and no need to parse it. And the difficulty is that our team only have beginer level of kowlegeg of dicom and we need to consult other teeeam and the code which do the parse the dicom are all in another france team, which make parse error not that obvious to us, we need to double check w/ them, proide to them raw data, tell them what we found so far, comminucate w/ them, then we can both idenitfy the error
    
    1. fd/bd api mis-conception -> cuz this backend error, fd will not be able to get the preprocess img and show error but another thing to make it complicated is that we do the preprocess in 2 step: the frist step, we query the whole collection, the first step we will load all collection info the second step we will asseeble the series-level url and get the seires/imgs on demand
    
    and at some stage, cuz we think it's a good idea to get the series/img level-thumb nail information at the first stage respone
    
    and when we do this, we didn't review some duplicated response info between the-original 2 step prepocess cache design
    
    so if preprocess fail at the last time we open the the fd will receive the preporcess error when they send the first api (which this error come from our backend db)
    
    and when we load that series (lazy load/load on demain( fd will fire the req and the and backend will retry that series and the series state about the peproecess state switch from fail -> start and then if at the point, front end get the step2 api response back the front end will get the preprpcess status show start the situation is that fd use different img to show start and show fail
    
    so when u open the app, it will show fail img, which is from step 1 resp and if you double click the img, it will show loading img, which is from step 2 resp and that's all
    
    what we expect is that, it shall show loadding -> then show fail if it will fail and the reason cause this is cuz we move some preporcess logic into step1 and didn't fd didn't update accrodlgy
    
    4 infra is not ready for the root cause component vpn issue which let me can't modify the probelm python code to do trial-and-error during the debug process
    
    ==
    
    so this bug consisit at least 3 area we need to modify, belong to 3 team, in 3 region
    
    ```markdown
    Q: "是否可以請你舉一個你遇過技術上的困難，然後說說你怎麼解掉的"
    
    S.T.A.R應答框架
    讓你在描述一件事情時，可以有條理的描述出來，
    而 S.T.A.R 各代表著：
    Situation(背景)：描述此經歷的背景，是在何時發生、在什麼情況發生以及為何會發生。
    Task(任務)：描述在這種情況下，你的目標是要完成什麼事情？
    Action(執行)：確切的說明採取了什麼措施解決這個問題
    Result(結果)：分享執行後的結果
    
    ==
    # Situation(背景)：描述此經歷的背景，是在何時發生、在什麼情況發生以及為何會發生。
    
    an urgent bug
    pm said there's an urgent bug
    which is report that there's two patinent img can't not loaded correctly
    recap, our all is a webapp, which can show patenitent mr/ct img
    by giving it collection
    collectid is the query parameter
    
    the 99% of probelm is about a particle collection can't work properly
    in some way
    
    in this case, the thumbnail is not showup correctly
    
    and fd (sebrina team) inform us that this is related to backend
    
    # Task(任務)：描述在這種情況下，你的目標是要完成什麼事情？
    
    PM will provide the diagloig/msg record/info tell us
    
    ## the first thing I will try to do is to see if I can reprocude the probelm
    
    so I go thru the diaglog, msg, report, information, which collection they said 
    they got error
    and try to use webapp load the same collection, using the same qeury para
    
    ==
    if i can't reprocue the problem , i need to comminucaite w/ our pm, re-exame the dialogue,
    bug report, to see if anything I missing or misunderstand.
    
    in this case, I can
    
    ## find which component cause error
    
    the first ting
    open the dev tool
    there's some logging msg in place, I check what error it produce
    if there's any backend related error show up
    sometimes, if i get lucky, I can guess by 90% acrury what is the root causue
    by checking the dev conoloe log
    
    ths is cuz our taipei team handle the backend
    
    in this case, no luck, I can't see any key error msg
    only a more general backend error
    => BTW, after this bug fix, we also update our log msg and identify a bug in our backend
    to reflect this error more clearly in our monitor system
    
    anyway, no luck
    
    so I need to call backend api and see in which component error happen
    
    review the architectie compoment diagam
    like archievetyre overvew
    know how the data flow
    and check component one by one, from up sream to down stream
    specifkcy, I check cloud watch so find error msg
    
    ## find exactly where the error arise
    
    finally, I find the error happened, which is a containernized lambda
    
    and i open the soure code and identify exactly which line of code raise the exception
    and guess what could cause this probelm
    
    in this case, the error happen in a place where python call
    a base layer c++ code which handling to generate the thumbail from dicom file
    
    and I am really not expeince in c++ code and dicom lib related stuff in c++
    and why this part of c++ will raise error when reading this particule dicom file
    
    so I need to contaxt a guy called Mathiue in france
    
    Action(執行)：確切的說明採取了什麼措施解決這個問題
    
    ## communicate and colarative idenitfy the root case and come out a solution
    
    Mathieur ask me provide the raw dicom file to allow him locally
    to test the file, I garb it from s3 path and provide to it
    
    I show him in which part of python raise error in which python call the binding c++ ccode
    which he had written
    
    =>
    
    after some check, Matheirun share the info
    the dicom is a strange foramt which is not possible to extract the thumbnail
    
    he will need to create another api to let my python code to check the dicom idenity
    and then I can know if this kind of dicom is possibe to extract thumbnail
    and if not, I just skil this kind the thumbnail gen process
    
    ==
    there's a another complicaiton when i debug, when this bug happen, we just finish 
    some infra migration which is followed by orgnizwd merge, in short, my compnay merged in to
    another bigger compmnay, good thing is that my compnay is top3 in clincal trial
    but our vpn change and we have troble to use WSL when the vpn is open
    and the python lambda, its container, script setup is most built upon on linux env,
    but at that time, this is not solved yet, this will be solved in the next PI(you can
    think in the next seaon or someting since this python lambda is mostly quite stable, so 
    we don't hurry to update it)
    so when I when I try to idenity this bug, and comminucate with franece team
    on python ccode, I do not have the abilty to run it up independely,
    and I don't have ability to modify it(not beofre the infra is fix, which is allow me
    to use WSL uneder company new VPN)
    I olny can send req and check the error
    ===
    
    at this point, we found the root casue.
    but based on the report in the fd,in the report
    they also mentoned that the preprocess error got some inconsistent respone
    which make they error status show up in the wrong way
    this is strange
    due to the time-zone, I and another developer (who is more fimialir with the freond end code
    base wrt. thumbnail part
    and we find out
    1. fd directly reply on backedn api result as a fd state mgmt to display some error/loading
    logic
    and we found that it's strange why the the fd will show error first and flip back to loading 
    status
    , which means backend will first tell fd, the prepoess is fail
    and then tell fd again, the preprocess is loading...
    
    beofre we do the source code checking, i suggest let's check the network tape
    and let's see what exactly backend send what kind of resposne to fd
    and we find out backend do send 2 resp to fd and sometimes the resp is not align
    
    when fd load the collection data, it will also load the default series
    and we will 2-stage prerocess
    at the beingin of our impl of 2-stage prepocess
    the first stage only get all meta data, all collection data
    only seocnd stage send thmbail and img/seroies data back
    
    but after some time, we update the 2-stage process
    we try to move the thmumb generated send back to steag1
    
    so stage1 and stag2  both can get thumbnail response, it can be fail or loading
    
    it will fail, if the last time it fail
    and
    it will loading since our app will retry every time fd refreash
    
    when fd refreash,
    the collection and default series, although 2 step
    it runs quick so the 2 status is mostly alaign, if it is fail, 2 resp both fail
    and fd show fail img, user know this img fail to load, let's it
    
    however, for the img/series not default
    the step 1 show fail (since the last time fail)
    the step 2 show loading (since when app refrssh, it retry, and rety turn the prepocess status 
    in the backend to loading, and no default stack call the backend in a more later time, it's
    on-demad, it could be 10 min later when user click it, and when the 2-stage
    api send back, the resp show loading)
    so the fd recie the loading stauts
    so the img turn from fail -> loading and that's it
    even retry sucess, it is still loading
    there's no thrid api call and there's no server-push or pooling mehciane or somewhat
    so the fd need to update it display logic, it need to update it story to how
    to handle this case, or backend need to update logic, which is more troble
    or fd need to maintain its own state, which is not direcly use backend state
    and if it is fail when the fist api resp, then it is fail in this sessesion
    no need to think of second fail
    at the point of this writring
    the follo-up ticket is not impl, and i don't know what fd will handle this
    
    anyhow, i and my colleuehe have a meeting with anoteh team and discuss this issue
    
    Result(結果)：分享執行後的結果
    
    we work on we can do now
    we firstly fix the backend monitor system
    to allow us next time to know its python probelm
    (it turrn our there's logic error to collect ereror in ouor error gathering logic)
    
    the frach team make a pull request
    and we know i review it and will need to integerate this into python
    
    the pyton infra blocker is still here at the time of this wrting
    so it is still there, we have not solve the vpn block wsl issue
    and we need to solve it in this sprint
    
    the fd problem will need to handle in another team, i am not sure
    this fd bug new ticket will be handle, maybe in this srint
    in another team
    
    === after match
    
    sharing to team, lesson learned
    - update to architect in case the change is alter the overall artchitectue
    	- get buy in
    review and see if we can add something to prevent future re-occur
    or setup monitor
    
    - review at which pt i took a lot of time to find bug
    	- add logger?
    	- improvemnt logging msg
    
    # multi error
    
    1. log gather component logic fail
    -> when we update the logging system, for wahatver reason, we didn't update the root-cause 
    component => so in thie case, that error msg cannot be properly handle and send it error log
    to front end / team channel monitor -> which make the debug slower
    
    2 dicom parse failiure and that code is not maintain by us
    -> it's about a parse dicom file error which our backend is not support for this
    particualr format, more precisely, this dicom format is preproatotty, which is usually
    cannot be handled propelry, in our parse logic, we need to adding another logic to detect
    this kind of special foramt and no need to parse it. And the difficulty is that our team
    only have beginer level of kowlegeg of dicom and we need to consult other teeeam and the 
    code which do the parse the dicom are all in another france team, which make parse error
    not that obvious to us, we need to double check w/ them, proide to them raw data, tell them
    what we found so far, comminucate w/ them, then we can both idenitfy the error
    
    3. fd/bd api mis-conception
    -> cuz this backend error, fd will not be able to get the preprocess img and show error
    but another thing to make it complicated is that
    we do the preprocess in 2 step:
    the frist step, we query the whole collection,  the first step
    we will load all collection info
    the second step we will asseeble the series-level url and get the seires/imgs 
    on demand
    
    and at some stage, cuz we think it's a good idea to get the series/img level-thumb nail
    information at the first stage respone
    
    and when we do this, we didn't review some duplicated response info between
    the-original 2 step prepocess cache design
    
    so if preprocess fail at the last time we open
    the the fd will receive the preporcess error when they send the first api
    (which this error come from our backend db)
    
    and when we load that series (lazy load/load on demain(
    fd will fire the req and the and backend will retry that series
    and the series state about the peproecess state switch from fail -> start
    and then if at the point, front end get the step2 api response back
    the front end will get the preprpcess status show start
    the situation is that fd use different img to show start and show fail
    
    so when u open the app, it will show fail img, which is from step 1 resp
    and if you double click the img, it will show loading img, which is from step 2 resp
    and that's all
    
    what we expect is that, it shall show loadding -> then show fail if it will fail
    and the reason cause this is cuz we move some preporcess logic into step1
    and didn't fd didn't update accrodlgy
    
    4 infra is not ready for the root cause component
    vpn issue which let me can't modify the probelm python code to do trial-and-error
    during the debug process
    
    ==
    
    so this bug consisit at least 3 area we need to modify, belong to 3 team, in 3 region
    
    ```
    
- case 2 how to list all restaurant for a given datetime
    
    I was thinking, maybe I can also use restant table
    
    select restuant ID
    
    - need to setup a opening_hour table, this table have restID (manyToOne)
    
    ```tsx
    // 7 workingHour mapping to one restaurant
      @ManyToOne(() => Restaurant, (restaurant) => restaurant.openingHours)
      restaurant: Restaurant;
    ```
    
    - the idx can be restID+day (day is Mon, Tues…etc)
        
    - query like below
        
        ```tsx
        SELECT distinct restaurantId 
        FROM `opening_hour` `OpeningHour` 
        WHERE weekday = ? 
        AND  
        ( (opens_at <= ? AND closes_at >= ? AND overnight = 0 ) 
        OR 
        ( (closes_at >= ? OR opens_at <= ? ) AND overnight = 1 ) ) 
        -- PARAMETERS: [
        "Sat",
        "2022-07-23 4:00:00", // I think ORM will auto covert "2022-07-23 4:00:00" to Date type (4:00:00) defined in Entity
        "2022-07-23 4:00:00",
        "2022-07-23 4:00:00",
        "2022-07-23 4:00:00"
        ]
        ```


---

- behavior prep
    
    [ref](https://blog.nindalf.com/posts/tech-interview/?utm_source=hackernewsletter&utm_medium=email&utm_term=working#algorithm-interview)
    
    ## **Behavioural Interview 行为访谈**
    
    **How I prepared** - I had cleared the Amazon interview once before so I knew the drill. I knew that each of the 6 interviewers would ask me for a time I displayed 2 of the [14 leadership principles](https://www.amazon.jobs/en/principles). So I'd end up discussing at least 12 of the 14. Ideally I'd be able to think of an instance where I had done these things on the fly, but like I said my memory is poor. It takes me time to remember things. So I sat down and wrote a true anecdote from my experience for each of the 14. When interviewers asked, I told them a relevant anecdote. It was easy because they were fresh in my mind.
    
    我是如何准备的——我以前通过过一次亚马逊的采访，所以我知道该怎么做。我知道6个面试官中的每一个都会要求我展示14条领导原则中的2条。所以我最后会讨论14个中的至少12个。理想情况下，我能够想到一个实例，我在飞行中做了这些事情，但正如我所说，我的记忆力很差。我需要时间来回忆。因此，我坐下来，根据自己的经历，为这14个人中的每一个人写了一个真实的轶事。当面试官问起时，我给他们讲了一个相关的轶事。这很容易，因为它们在我的脑海中是新鲜的。
    
    _Every_ company asks these questions, not just Amazon. They all want to know how well you work with others, if you can commit to an idea you disagree with, if you can take ownership of a problem space, deliver results, mentor others and so on.
    
    每个公司都会问这些问题，不仅仅是亚马逊。他们都想知道你和别人合作得怎么样，你是否能承诺一个你不同意的想法，你是否能掌控一个问题空间，交付结果，指导别人等等。
    
    Some of the answers to these questions did not show me in the best light. I could see (or imagined I could see) the interviewer's body language change negatively when I said those things. But I figured it's better to be honest than make stuff up.
    
    这些问题的一些答案并没有给我最好的印象。当我说这些话的时候，我可以看到(或者想象我可以看到)面试官的肢体语言发生了消极的变化。但我觉得诚实总比胡编乱造要好。
    
    **How I'd recommend others prepare** - Same. Prepare your anecdotes. Be honest.
    
    我会建议其他人如何准备——一样。准备你的轶事。诚实
    
    - [ ] [[面試][人格特質] 當你分享工作經驗時會被問到的種種問題 - iT 邦幫忙：：一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10278526)
        
    - [ ] [[面試][人格特質] 一再被問的經典面試題 - iT 邦幫忙：：一起幫忙解決難題，拯救 IT 人的一天](https://ithelp.ithome.com.tw/articles/10278856)
        
    - reverse interview
        
        [reverse-interview/README.md at master · viraptor/reverse-interview](https://github.com/viraptor/reverse-interview/blob/master/README.md)
        
- wemo bq prep
    
    WeMo Scooter
    
    2016 年上路，
    
    以智慧出行串連智慧生活大小事，提供「自由租 輕鬆停」 - 無特定租還地智慧電動機車即時租借服務，為亞洲最大規模隨租隨還智慧機車服務。
    
    以大台北地區為起點，2019年首度擴區至高雄，
    
    "矢志創造"""以租代買"""的交通新體驗，致力打造台灣為智慧綠能交通頂尖國家""
    
    我們致力於維護系統穩定， 並持續朝微服務架構前進，
    
    若你有大型網站或後端系統架構設計與微服務環境建置開發經驗， 且善於嗅出程式碼壞味道並適時進行重構，WeMo Scooter 需要你的加入一起：
    
    => code quality
    
    開發、維護、整合 WeMo Scooter 核心系統，持續優化、解決問題，並依需求撰寫相關 API 及 API 文件 熟悉雲端 Saas 平台網站維運，能針對複雜商業邏輯及各式產品需求快速進行架構設計，並獨立作業 需求條件
    
    熟悉任一後端程式開發語言 (node.js, c#, java, php, python… etc, node.js 尤佳) 熟悉任一 MVC 框架 (express/koa, [asp.net](http://asp.net/) mvc, spring, laravel, django… etc, express / NestJs 尤佳) 熟悉 Linux (Unix) 工作環境 熟悉 Relational / NoSQL / In Memory 資料庫 熟悉 RESTful 設計規範與 JSON 資料格式操作 熟悉 Git 協作流程 熟悉 CI / CD 及相關整合開發工具 具備測試撰寫 / Code Review 之經驗 Docker (Kubernetes) 虛擬化開發經驗 GCP 或 AWS 雲端服務維運經驗 良好的溝通與獨立思考能力 良好的文件撰寫與口語表達能力 良好的系統規劃能力，能判斷如何實現新功能（含需求收斂、新系統建立、現有系統改善）
    
    加分項目 微服務環境建置與開發經驗 有評估工作流程的經驗，能提出建議並進行改善 (agile) 有評估開發流程的經驗，能提出建議並進行改善 (e.g. IaC) Experience to define software development best practices (i.e. micro service guideline, repository db to access data) Mentor Junior Backend Engineers to allow for skill/knowledge development through advice coaching, and training opportunities.
    
    Intro your self
    
    "Tell me about your journey into tech. How did you get interested in coding, and why was web development (or replace with other job-specific skills) a good fit for you? How is that applicable to our role or company goals?" It is probably not a good idea to spend valuable time talking about things which aren't relevant to the job!
    
    Tell the interviewer why you would make a good hire. Is your experience relevant to the company? Have you used a similar tech stack as the company or built relevant products? What unique talent(s) do you have that may give them confidence about your ability to contribute to the company?
    
    == "Hi I'm XXX and I graduated from National University of Singapore in 2015 with a degree in Computer Science. My interests are in Front End Engineering and I love to create beautiful and performant products with delightful user experiences.
    
    Back in school, I designed and built a web application, NUSMods which solves a huge problem of class and timetable planning every semester. It receives over a million pageviews a month and is used by over 40,000 NUS students and even some professors. It is built using a modern web technology stack - React, Redux, Jest, Babel, Flow, webpack and is mobile-responsive."
    
    I'm interested in the Front End Engineer role at Meta because I have been using Meta Open Source Front End technologies for a while now and am inspired by Meta's mission and Open Source culture.
    
    ==
    
    你好，
    
    我叫湖賓偉 不過你可以叫我Ben就好了。
    
    我簡單的說一下教育和工作經驗好了。 我大學念化工，那時候沒
    
    我目前是在緯創工作的軟體工程師，緯創世人立委外公司，目前我工作的單位是美國公司clario 我們主要建立得是一個基於醫療影像軟體，前端需要顯是一釐畫面和很多一釐圖形的控制，因此採用angleu 架構 後端主要是走sevless 架構，主要在lambda和相關的aws服務上運行。
    
    這個專案大概有2x幾人，包括dev, 前後端，sqa 和 scram master 目前職稱是全端，我們台灣team主要事後端為主，80%。前端大概20%的工作
    
    因為系統比較複查，大概有4個主要的repo,前端有超過30-40個compoplet以上 後端有用到的包括lambda, sqs, ses, s3, cw, dynamo等等。
    
    因此公司對於code qulity, review process unit test, 和 docusmetion都有相對嚴謹的要求。
    
    我也從這個工作中的經驗中受益很多。
    
    ，不過個人個性上比較偏好well dievined, 重要邏輯姓的knowledge, 因此私底下我的個人side project, 進修，也是比較往後端比較多一點。
    
    [](https://www.youtube.com/results?search_query=behavioral+interview+%E4%B8%AD%E6%96%87)[https://www.youtube.com/results?search_query=behavioral+interview+中文](https://www.youtube.com/results?search_query=behavioral+interview+%E4%B8%AD%E6%96%87)
    
    用來展現軟實力和人格特色
    
    要準備 會有壓力 不會忙亂出錯 邏輯會更清楚
    
    三個準備 看公司要的價值，回答要呼應公司價值 準備故事清單 困難和錯誤 => 學到什麼 提昇效率 一定要是做的事情，越多細節越好 以自己為中心去講 要說很多不順利的情況 => 因為要看你如何克服，學到什麼 練習不同情境回答 （ex : tell me about a time when you had to meet a tight deadline) 類似leetocde 越練越好 要寫下來 情境不外呼：衝突，失敗案例，短時限、團隊合作、適應能力、協調能力、溝通能力 用STAR：情境，task, 你要角色， action, 做了什麼，和結果
    
    你問我答 掌握節奏，不會浪費時間在面試官沒有要聽的點，時間控制好
    
    talk about 缺點
    
    sometimes, it's hard for me to chnage my oootion, seocaily something i think it's important or cprincinple I follow this can happen duting the group discusion, archi discusison, code revierw
    
    For now, I think I will be more open there's some techbiqye just don't arument, give muslef more roon maybe if I want to pursude other, leave it tmr and most of the time, I get some time to ahve thinkgin, I will appreicate their persceptive or I will have better idea to presnet my idea
    
    I think the to is to stepo back and think more before
    
    talk about how I benefit from talking to Wade both fpocus on different pt and we both gave astrong option but we will at the end, want to make code better more zy to main more ez to undetsnd perf conisiderun is it a to early to pirside option.
    
    例子一
    
    1. Tell me about how you worked effectively under pressure.
    
    clarify expections woth all stackhoder scrm master and tech lead and collegagess another way to mgmt time presesure (have a simpler impl? maybe it's fine w.o too mych perf conditarion) (resue cetain exist module is helper?) (identiy some potnetola techilocal chellenge and align with team member) (will be update mroe freq )
    
    1. How do you handle a challenge? Give an example.
    2. Have you ever made a mistake? How did you handle it? comnute clear, explor unknown unknown tooling to fix a lot of nimor, automate eveything if we can, consisitent be mroe arew of myself, attitdle, 狀態, be preforfesionall
    3. Give an example of how you set goals.
    4. Give an example of a goal you reached and tell me how you achieved it.
    5. Describe a decision you made that wasn't popular, and explain how you handled implementing it.
    6. Give an example of how you worked on a team.
    7. What do you do if you disagree with someone at work?
    8. Share an example of how you were able to motivate employees or co-workers.
    9. Have you handled a difficult situation? How?
    
    More Behavioral Interview Questions Have you worked on multiple projects? How did you prioritize? How do you handle meeting tight deadlines? How do you handle it when your schedule is interrupted? What do you do if you disagree with a co-worker? Give me an example of when you did or when you didn't listen. What do you do if you disagree with your boss? How do you handle it when there's a conflict among team members? What is your most important career accomplishment? Why?
    
    ==
    
    wemo
    
    公司目前的發展 近期的目標，中期目標
    
    Jean在公司的角色 那些時候你會希望我主動跟你互動
    
    有架構師？有涉入技術？
    
    你覺得公司對工程師最大的吸引力是什麼？
    
    ＝＝ [https://www.linkedin.com/in/jeffrey-wu-34781316/?originalSubdomain=twhttps://www.linkedin.com/in/jean-su-9aa50797/https://www.linkedin.com/in/chien-hung-cho-495b3aa6/](https://www.linkedin.com/in/jeffrey-wu-34781316/?originalSubdomain=twhttps://www.linkedin.com/in/jean-su-9aa50797/https://www.linkedin.com/in/chien-hung-cho-495b3aa6/) dennis [https://www.linkedin.com/in/mou-chun-wang-b1235a17/](https://www.linkedin.com/in/mou-chun-wang-b1235a17/)
    
    好習慣
    
    溝通上討論上跟不上講懶得講
    
    最低標準 自我訓練自我學習看到成果 commitment 要做到
    
- Amazon **Leadership Principles**
    
    ## **Customer Obsession 顾客至上**
    
    - 从客户开始
        
    - 努力工作以赢得客户的信任。
        
    - 关注竞争对手，但更关注顾客。
        
    - Leaders start with the customer and work backwards. They work vigorously to earn and keep customer trust. Although leaders pay attention to competitors, they obsess over customers.
        
    
    ## **Ownership 所有权**
    
    - **Ownership attitube**
    - 着眼长远，不会为了短期的结果而牺牲长远的价值
    - 他们从不说“那不是我的工作”
    - Leaders are owners. They think long term and don’t sacrifice long-term value for short-term results. They act on behalf of the entire company, beyond just their own team. They never say “that’s not my job."
    
    ## **Invent and Simplify 發明与简化**
    
    - 进行创新和发明
        
    - 找到简化的方法。
        
    - 从各个地方寻找新的想法
        
    - 勇於承认我们以前都是錯的，願意創新
        
    - Leaders expect and require innovation and invention from their teams and always find ways to simplify. They are externally aware, look for new ideas from everywhere, and are not limited by “not invented here." As we do new things, we accept that we may be misunderstood for long periods of time.
        
    
    ## **Are Right, A Lot**
    
    - 很强的判断力
        
    - 良好的直觉
        
    - 求不同的观点，并努力否定自己可能過時的信仰
        
    - strong judgment
        
    - good instincts.
        
    - seek diverse perspectives
        
    - work to disconfirm their beliefs.
        
    
    ## **Learn and Be Curious 学习和保持好奇心**
    
    - 从不停止学习
        
    - 寻求提高自己
        
    - 对新的可能性充满好奇，并采取行动去探索它们。
        
    - never done learning
        
    - always seek to improve themselves
        
    - curious about new possibilities and act to explore them.
        
    
    ## **Hire and Develop the Best 雇佣和发展最优秀的人才**
    
    - 對每一次的招募和升遷都用高績效作為標準
        
    - 認可傑出的人才，且願意讓他們在組織中進行輪替。
        
    - 培育人才並進行coach
        
    - 替員工進行職涯發展
        
    - Leaders raise the performance bar with every hire and promotion. They recognize exceptional talent, and willingly move them throughout the organization. Leaders develop leaders and take seriously their role in coaching others. We work on behalf of our people to invent mechanisms for development like Career Choice.
        
    
    ## **Insist on the Highest Standards 坚持最高标准**
    
    - 有着不斷提高的高标准ーー许多人可能认为这些标准高得不合理。
    - 不断地提高标准，推动他们的团队提供高质量的产品、服务和流程。
    - 确保缺陷不会一直持續下去，问题可以得到解决。
    - Leaders have relentlessly high standards — many people may think these standards are unreasonably high. Leaders are continually raising the bar and drive their teams to deliver high quality products, services, and processes. Leaders ensure that defects do not get sent down the line and that problems are fixed so they stay fixed.
    
    ## **Think Big 大胆想想**
    
    - 目光短浅是自我应验预言
    - 會创造和传达一个大胆的方向
    - 大家對於這個方向结果是充滿激勵心情的。
    - 想法与众不同，他们四处寻找服务顾客的方法。
    - Thinking small is a self-fulfilling prophecy. Leaders create and communicate a bold direction that inspires results. They think differently and look around corners for ways to serve customers.
    
    ## **Bias for Action 行动偏见**
    
    - 速度在市場上也是很重要的。
    - 许多决定和行动都是可逆的，不一定都要大量的前期研究
    - 有计划的冒险行为，以快和做為主，是很重要的。
    
    Speed matters in business. Many decisions and actions are reversible and do not need extensive study. We value calculated risk taking.
    
    ## **Frugality 节俭**
    
    - 用更少的資源做更多的事情
    - 限制催生創意和創造。
    
    Accomplish more with less. Constraints breed resourcefulness, self-sufficiency, and invention. There are no extra points for growing headcount, budget size, or fixed expense.
    
    ## **Earn Trust 赢得信任**
    
    认真倾听
    
    坦率地讲话
    
    尊重他人
    
    他们在声音上自我批评，即使这样做是尴尬或尴尬的。
    
    领导者不相信他们或他们团队的体味有香水味。他们将自己和自己的团队与最好的进行比较。
    
    Leaders listen attentively, speak candidly, and treat others respectfully. They are vocally self-critical, even when doing so is awkward or embarrassing. Leaders do not believe their or their team’s body odor smells of perfume. They benchmark themselves and their teams against the best.
    
    ## **Dive Deep 深潜**
    
    领导者在各个层面上运作，与细节保持联系，
    
    经常审计，当指标和轶事有所不同时，他们会持怀疑态度。
    
    没有什么任务是低于他们的。
    
    Leaders operate at all levels, stay connected to the details, audit frequently, and are skeptical when metrics and anecdote differ. No task is beneath them.
    
    ## **Have Backbone; Disagree and Commit 有主心骨; 不同意和承诺**
    
    有义务在他们不同意的时候恭敬地质疑决策，即使这样做会让人感到不舒服或者精疲力尽。
    
    领导者有信念，而且坚韧不拔。
    
    他们不会为了社会凝聚力而妥协。
    
    一旦决定下来，他们就会全身心投入。
    
    Leaders are obligated to respectfully challenge decisions when they disagree, even when doing so is uncomfortable or exhausting. Leaders have conviction and are tenacious. They do not compromise for the sake of social cohesion. Once a decision is determined, they commit wholly.
    
    ## **Deliver Results 交付成果**
    
    專注於关键投入，并以正确的质量和及时的方式提供这些投入。
    
    尽管遇到挫折，他们还是能应付自如，从不妥协。
    
    Leaders focus on the key inputs for their business and deliver them with the right quality and in a timely fashion. Despite setbacks, they rise to the occasion and never settle.
    
    ## **Strive to be Earth's Best Employer 努力成为地球上最好的雇主**
    
    领导者每天都在努力创造一个更安全、更高效、更高效、更多样化和更公正的工作环境。
    
    以同理心领导，在工作中找到乐趣，让别人更容易找到乐趣。
    
    领导者问自己: 我的同事们在成长吗？他们被授权了吗？他们准备好迎接下一步了吗？
    
    无论是在亚马逊还是在其他地方，领导者对员工的个人成功都有远见和承诺。
    
    Leaders work every day to create a safer, more productive, higher performing, more diverse, and more just work environment. They lead with empathy, have fun at work, and make it easy for others to have fun. Leaders ask themselves: Are my fellow employees growing? Are they empowered? Are they ready for what's next? Leaders have a vision for and commitment to their employees' personal success, whether that be at Amazon or elsewhere.
    
    ## **Success and Scale Bring Broad Responsibility 成功和规模带来广泛的责任**
    
    我们从车库开始，但现在已经不在了。
    
    我们很大，我们影响着世界，我们离完美还很远。
    
    我们必须对我们行为的次要影响保持谦逊和深思熟虑。
    
    我们的地方社区、地球和后代需要我们每天都变得更好。
    
    我们必须在每一天开始的时候下定决心，为我们的客户、员工、合作伙伴和整个世界做得更好，做得更好，做得更好。
    
    我们必须在每一天结束的时候知道我们明天可以做得更多。
    
    领导者创造的比他们消费的多，并且总是留下比他们发现的更好的东西。
    
    We started in a garage, but we're not there anymore. We are big, we impact the world, and we are far from perfect. We must be humble and thoughtful about even the secondary effects of our actions. Our local communities, planet, and future generations need us to be better every day. We must begin each day with a determination to make better, do better, and be better for our customers, our employees, our partners, and the world at large. And we must end every day knowing we can do even more tomorrow. Leaders create more than they consume and always leave things better than how they found them.