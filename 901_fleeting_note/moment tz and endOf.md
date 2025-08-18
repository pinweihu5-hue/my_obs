```ts


  const noTZ = moment(new Date())
  console.log('noTZ -->', noTZ.toDate())
  const useTZ = moment(new Date()).tz('Asia/Taipei')
  console.log('useTZ -->', useTZ.toDate())


```
  console.log
    noTZ --> 2024-07-05T02:24:01.180Z
  console.log
    useTZ --> 2024-07-05T02:24:01.180Z



```ts

  const noTZ = moment(new Date()).endOf('day')
  console.log('noTZ -->', noTZ.toDate())
  const useTZ = moment(new Date()).tz('Asia/Taipei').endOf('day')
  console.log('useTZ -->', useTZ.toDate())

```

  console.log
    noTZ --> 2024-07-05T23:59:59.999Z
  console.log
    useTZ --> 2024-07-05T15:59:59.999Z
