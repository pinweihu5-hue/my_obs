

---

Summary of all failing tests
FAIL test/api/v2/users/me/rents.test.js (48.848 s, 1100 MB heap size)
  ● users me rents api › [PATCH] /v3/users/me/rent/{id} type=3(rent) › rent status not reserving. get RENT_NOT_FOUND error
    RENT_NOT_FOUND
      277 |     try {
      278 |       const rent = await this.db.rent.getValidRentingRentById(rentId)
    > 279 |       if (!rent) throw new Error(RENT_NOT_FOUND.type)
          |                        ^
      280 | 
      281 |       const { scooterId, userId } = rent
      282 |       if (userId !== userVO.id) throw new Error(AUTH_NO_VALID_IDENTITY.type)
      at RentAgent.<anonymous> (api/v2/users/service/rents/rent.ts:279:24)
      at fulfilled (api/v2/users/service/rents/rent.js:3472:24)
          at runMicrotasks (<anonymous>)


---


FAIL test/pm2_cron/timePlanReminders.test.js (20.842 s, 1558 MB heap size)

[645](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L645) ● monthly plan › plan will expire within 17 minutes

[646](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L646) Timeout - Async callback was not invoked within the 5000 ms timeout specified by jest.setTimeout.Error: Timeout - Async callback was not invoked within the 5000 ms timeout specified by jest.setTimeout.

[647](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L647) at mapper (node_modules/jest-jasmine2/build/queueRunner.js:27:45)

[648](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L648) ● monthly plan › plan expired time is not within 17 minutes

[649](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L649) Timeout - Async callback was not invoked within the 5000 ms timeout specified by jest.setTimeout.Error: Timeout - Async callback was not invoked within the 5000 ms timeout specified by jest.setTimeout.

[650](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L650) at mapper (node_modules/jest-jasmine2/build/queueRunner.js:27:45)

[651](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L651) ● monthly plan › user is not renting

[652](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L652) Timeout - Async callback was not invoked within the 5000 ms timeout specified by jest.setTimeout.Error: Timeout - Async callback was not invoked within the 5000 ms timeout specified by jest.setTimeout.

[653](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L653) at mapper (node_modules/jest-jasmine2/build/queueRunner.js:27:45)

[654](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L654) ● monthly plan › plan is not active

[655](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L655) Timeout - Async callback was not invoked within the 5000 ms timeout specified by jest.setTimeout.Error: Timeout - Async callback was not invoked within the 5000 ms timeout specified by jest.setTimeout.

[656](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L656) at mapper (node_modules/jest-jasmine2/build/queueRunner.js:27:45)

[657](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L657) ● monthly plan › plan has expired

[658](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L658) Timeout - Async callback was not invoked within the 5000 ms timeout specified by jest.setTimeout.Error: Timeout - Async callback was not invoked within the 5000 ms timeout specified by jest.setTimeout.

[659](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L659) at mapper (node_modules/jest-jasmine2/build/queueRunner.js:27:45)

[660](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L660) ● monthly plan › The remainder of the remaining time divided by 5 is not 1

[661](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L661) Timeout - Async callback was not invoked within the 5000 ms timeout specified by jest.setTimeout.Error: Timeout - Async callback was not invoked within the 5000 ms timeout specified by jest.setTimeout.

[662](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L662) at mapper (node_modules/jest-jasmine2/build/queueRunner.js:27:45)

[663](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L663) ● hourly plan › plan will expire within 17 minutes

[664](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L664) Timeout - Async callback was not invoked within the 5000 ms timeout specified by jest.setTimeout.Error: Timeout - Async callback was not invoked within the 5000 ms timeout specified by jest.setTimeout.

[665](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L665) at mapper (node_modules/jest-jasmine2/build/queueRunner.js:27:45)

[666](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L666) ● hourly plan › The remainder of the remaining time divided by 5 is not 1

[667](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L667) Timeout - Async callback was not invoked within the 5000 ms timeout specified by jest.setTimeout.Error: Timeout - Async callback was not invoked within the 5000 ms timeout specified by jest.setTimeout.

[668](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L668) at mapper (node_modules/jest-jasmine2/build/queueRunner.js:27:45)

[669](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L669)Test Suites: 1 failed, 1 skipped, 540 passed, 541 of 542 total

[670](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L670)Tests: 8 failed, 79 skipped, 6707 passed, 6794 total

[671](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L671)Snapshots: 0 total

[672](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L672)Time: 1944.982 s

[673](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L673)Ran all test suites matching /.\/test\//i.

[674](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L674) % Total % Received % Xferd Average Speed Time Time Time Current

[675](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L675) Dload Upload Total Spent Left Speed

[676](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L676)100 421 0 307 100 114 216 80 0:00:01 0:00:01 --:--:-- 296

[677](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L677){

[678](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L678) "name": "spaces/AAAAKXoxGeU/messages/z_v7-Ekpu6E.z_v7-Ekpu6E",

[679](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L679) "text": "*!!!!!! TESTING FAILED !!!!!!*\n*Branch:* fix/sql-wrong-syntax\n*Commit Title:* fix wrong sql syntax\n",

[680](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L680) "thread": {

[681](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L681) "name": "spaces/AAAAKXoxGeU/threads/z_v7-Ekpu6E"

[682](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L682) },

[683](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L683) "space": {

[684](https://athena.wemoscooter.com/wemo/Hermes-Api/-/jobs/344518#L684) "name": "spaces/AAAAKXoxGeU"