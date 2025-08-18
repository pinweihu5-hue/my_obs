
呼叫方可以不認識被呼叫的人 → 實現 SRP的 方法



# pubsub 例子1
- 當一個東西 A 要去呼叫一個東西 B 的時候，盡量設計成， A 可以越不認識 B (的機制) 越好
    - 類似用 queue or pubsub 類的機制就算是達到 A 幾乎不認識 B
	    - A is producer
	    - B is consumer
    - 認識就表示，A 那邊可能還需要動 code 去做一些判斷才可以呼叫 B  -> 這就是 耦合
    - 下面的 person and wallet 例子， person 不用管 wallet 的機制



# OOP 例子2
- 需求：使用者要使用錢包去花錢
- 建立了 Person class, wallet class
- 違反的寫法：
    - Person get wallet instance and spend_money_operation in person calss

```ts title:lod_違背 fold

class Wallet {
  private money: number;

  constructor(initialMoney: number) {
    this.money = initialMoney;
  }

  getMoney(): number {
    return this.money;
  }
}

class Person {
  private wallet: Wallet;

  constructor(wallet: Wallet) {
    this.wallet = wallet;
  }

  // Violation of LoD: Person should not directly access the money in the wallet.
  spendMoney(amount: number): void {
    const currentMoney = this.wallet.getMoney();
    if (currentMoney >= amount) {
      this.wallet = new Wallet(currentMoney - amount);
      console.log(`Spent ${amount} from the wallet.`);
    } else {
      console.log('Not enough money in the wallet.');
    }
  }
}

// Example of violation
const wallet = new Wallet(100);
const person = new Person(wallet);
person.spendMoney(50);


```


- 應該要：
    - person class and some person method
    - wallet class and some waller method
    - person inject wallet instance
    - and then use wallet method to do spend_money_operation
    - person class is not no need to know how to spend mony, spend_money_operation is encapsulated in wallet class

```ts title:lod遵守 fold

class Wallet {
  private money: number;

  constructor(initialMoney: number) {
    this.money = initialMoney;
  }

  getMoney(): number {
    return this.money;
  }

  spendMoney(amount: number): boolean {
    if (this.money >= amount) {
      this.money -= amount;
      console.log(`Spent ${amount} from the wallet.`);
      return true;
    } else {
      console.log('Not enough money in the wallet.');
      return false;
    }
  }
}

class Person {
  private wallet: Wallet;

  constructor(wallet: Wallet) {
    this.wallet = wallet;
  }

  // Adhering to LoD: Person interacts with the Wallet through its public interface.
  spendMoney(amount: number): void {
    if (this.wallet.spendMoney(amount)) {
      console.log(`Person spent ${amount}.`);
    } else {
      console.log('Person could not spend the money.');
    }
  }
}

// Example adhering to LoD
const wallet = new Wallet(100);
const person = new Person(wallet);
person.spendMoney(50);


```



- 為何要這樣規劃?
    - 職責明確
    - 大家負責自己的
    - Person 不需要認識 Wallet 裡面 money 的操作, money 是 Wallet 的責任


