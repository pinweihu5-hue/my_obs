Decorator Pattern（裝飾者模式）是一種結構型設計模式，允許你在不改變物件結構的情況下，動態地為物件添加額外的功能。這種模式通常用於擴展類的功能，並且可以在運行時進行組合。

### 主要組件

Decorator Pattern 通常由以下幾個組件組成：

1. **Component**：這是一個接口或抽象類，定義了裝飾者和具體組件的共同接口。
2. **ConcreteComponent**：這是實現了 `Component` 接口的具體類，表示被裝飾的物件。
3. **Decorator**：這是一個抽象類，實現了 `Component` 接口，並持有一個 `Component` 的引用。它的主要目的是為具體的裝飾者提供一個基礎。
4. **ConcreteDecorator**：這是具體的裝飾者類，擴展了 `Decorator`，並可以添加額外的功能。

### 示例

以下是一個使用 Decorator Pattern 的簡單示例，展示如何為一個飲料類型添加不同的配料。

#### 1. 定義 Component 接口
這是一個接口或抽象類，定義了裝飾者和具體組件的共同接口
getDescription and cost 就是我們想要額外添加的功能
```java

public interface Beverage {
    String getDescription();
    double cost();
}

```

#### 2. 實現 ConcreteComponent
這是實現了 `Component` 接口的具體類，表示被裝飾的物件
```java

public class Coffee implements Beverage {
    @Override
    public String getDescription() {
        return "Coffee";
    }

    @Override
    public double cost() {
        return 2.00;
    }
}

```

#### 3. 實現 Decorator 抽象類
**Decorator**：這是一個抽象類，實現了 `Component` 接口，並持有一個 `Component` 的引用。
它的主要目的是為具體的裝飾者提供一個基礎。
```java

public abstract class CondimentDecorator implements Beverage {
    protected Beverage beverage;

    public CondimentDecorator(Beverage beverage) {
        this.beverage = beverage;
    }

    @Override
    public abstract String getDescription();
}

```

#### 4. 實現 ConcreteDecorator
**ConcreteDecorator**：這是具體的裝飾者類，擴展了 `Decorator`，並可以添加額外的功能。
```java

public class MilkDecorator extends CondimentDecorator {
    public MilkDecorator(Beverage beverage) {
        super(beverage);
    }

    @Override
    public String getDescription() {
        return beverage.getDescription() + ", Milk";
    }

    @Override
    public double cost() {
        return beverage.cost() + 0.50;
    }
}

public class SugarDecorator extends CondimentDecorator {
    public SugarDecorator(Beverage beverage) {
        super(beverage);
    }

    @Override
    public String getDescription() {
        return beverage.getDescription() + ", Sugar";
    }

    @Override
    public double cost() {
        return beverage.cost() + 0.25;
    }
}


```

#### 5. 使用 Decorator
```java

public class Main {
    public static void main(String[] args) {
        Beverage coffee = new Coffee();
        System.out.println(coffee.getDescription() + " $" + coffee.cost());

        // 添加牛奶
        coffee = new MilkDecorator(coffee);
        System.out.println(coffee.getDescription() + " $" + coffee.cost());

        // 添加糖
        coffee = new SugarDecorator(coffee);
        System.out.println(coffee.getDescription() + " $" + coffee.cost());
    }
}

// Coffee $2.0 2
// Coffee, Milk $2.5 3
// Coffee, Milk, Sugar $2.75

```


### 優點
1. **靈活性**：可以在運行時動態地添加或改變物件的行為。
2. **遵循開放-封閉原則**：可以在不改變現有代碼的情況下擴展功能。
3. **避免子類化**：可以通過組合而不是繼承來擴展功能，減少了類的數量。

### 缺點
1. **複雜性**：使用裝飾者模式可能會導致系統的結構變得更加複雜，特別是當有多個裝飾者時。
2. **調試困難**：由於裝飾者的層次結構，調試可能會變得更加困難。

### 總結
Decorator Pattern 是一種強大的設計模式，適合用於需要動態擴展物件功能的場景。
它提供了一種靈活的方式來組合和擴展功能，而不需要改變原有的類結構。