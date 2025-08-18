


setup a spring boot project

you can use 3 level architecture
controller -> service -> db level

a user controller to get user data

implement the GET api:
- we need to get user data(see point1)
- save the data into minio
- save the data into mongo DB
- and then return the result



point1:
to get user data, we need to call this API to get the data
https://jsonplaceholder.typicode.com/users
```
a list of below user

{"id": 1,"name": "Leanne Graham","username": "Bret","email": "Sincere@april.biz","address": {"street": "Kulas Light","suite": "Apt. 556","city": "Gwenborough","zipcode": "92998-3874","geo": {"lat": "-37.3159","lng": "81.1496"}},"phone": "1-770-736-8031 x56442","website": "hildegard.org","company": {"name": "Romaguera-Crona","catchPhrase": "Multi-layered client-server neural-net","bs": "harness real-time e-markets"}},
```





===





