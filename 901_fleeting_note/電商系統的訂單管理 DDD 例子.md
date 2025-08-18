

我來用一個電商系統的訂單管理場景，為您具體展示這些 DDD 概念：

## 實體（Entity）示例

```csharp
public class Order
{
    public OrderId Id { get; private set; }  // 唯一標識
    public CustomerId CustomerId { get; private set; }
    public DateTime CreatedDate { get; private set; }
    public OrderStatus Status { get; private set; }
    public Money TotalAmount { get; private set; }
    private List<OrderItem> _items = new List<OrderItem>();
    
    public void AddItem(Product product, int quantity)
    {
        // 業務邏輯：添加商品項目
    }
    
    public void ConfirmOrder()
    {
        if (Status != OrderStatus.Draft)
            throw new InvalidOperationException("只有草稿狀態的訂單可以確認");
        
        Status = OrderStatus.Confirmed;
    }
}
```

**為什麼是實體**：訂單有唯一的 ID，即使修改了訂單內容（如添加商品、改變狀態），它仍然是同一個訂單。

## 值對象（Value Object）示例

```csharp
public class Money
{
    public decimal Amount { get; private set; }
    public string Currency { get; private set; }
    
    public Money(decimal amount, string currency)
    {
        if (amount < 0) throw new ArgumentException("金額不能為負數");
        if (string.IsNullOrEmpty(currency)) throw new ArgumentException("幣別不能為空");
        
        Amount = amount;
        Currency = currency;
    }
    
    public Money Add(Money other)
    {
        if (Currency != other.Currency)
            throw new InvalidOperationException("不同幣別無法相加");
        
        return new Money(Amount + other.Amount, Currency);
    }
    
    public override bool Equals(object obj)
    {
        if (obj is Money other)
            return Amount == other.Amount && Currency == other.Currency;
        return false;
    }
}

public class Address
{
    public string Street { get; private set; }
    public string City { get; private set; }
    public string PostalCode { get; private set; }
    
    public Address(string street, string city, string postalCode)
    {
        Street = street;
        City = city;
        PostalCode = postalCode;
    }
    
    // 值對象是不可變的，沒有唯一標識
    // 兩個相同內容的地址被視為相同
}
```

**為什麼是值對象**：金額和地址沒有唯一標識，完全由其屬性值定義。兩個金額相同的對象被視為相等。

## 聚合（Aggregate）示例

```csharp
// 訂單聚合，Order 是聚合根
public class Order  // 聚合根
{
    public OrderId Id { get; private set; }
    public CustomerId CustomerId { get; private set; }
    public Address ShippingAddress { get; private set; }
    public OrderStatus Status { get; private set; }
    
    private List<OrderItem> _items = new List<OrderItem>();
    public IReadOnlyList<OrderItem> Items => _items.AsReadOnly();
    
    public void AddItem(ProductId productId, Money unitPrice, int quantity)
    {
        // 業務規則：確認訂單後不能添加商品
        if (Status != OrderStatus.Draft)
            throw new DomainException("已確認的訂單無法修改商品");
            
        var existingItem = _items.FirstOrDefault(x => x.ProductId == productId);
        if (existingItem != null)
        {
            existingItem.ChangeQuantity(existingItem.Quantity + quantity);
        }
        else
        {
            _items.Add(new OrderItem(productId, unitPrice, quantity));
        }
        
        RecalculateTotalAmount();
    }
    
    public void RemoveItem(ProductId productId)
    {
        if (Status != OrderStatus.Draft)
            throw new DomainException("已確認的訂單無法移除商品");
            
        _items.RemoveAll(x => x.ProductId == productId);
        RecalculateTotalAmount();
    }
    
    private void RecalculateTotalAmount()
    {
        TotalAmount = _items.Aggregate(
            new Money(0, "TWD"), 
            (sum, item) => sum.Add(item.GetSubTotal())
        );
    }
}

// 訂單項目（聚合內的實體）
public class OrderItem
{
    public ProductId ProductId { get; private set; }
    public Money UnitPrice { get; private set; }
    public int Quantity { get; private set; }
    
    public OrderItem(ProductId productId, Money unitPrice, int quantity)
    {
        ProductId = productId;
        UnitPrice = unitPrice;
        Quantity = quantity;
    }
    
    public Money GetSubTotal()
    {
        return new Money(UnitPrice.Amount * Quantity, UnitPrice.Currency);
    }
    
    public void ChangeQuantity(int newQuantity)
    {
        if (newQuantity <= 0)
            throw new DomainException("商品數量必須大於 0");
        
        Quantity = newQuantity;
    }
}
```

**為什麼是聚合**：Order 和 OrderItem 是相關的對象集合，它們作為一個整體維護業務不變性（如總金額的正確性）。外部只能通過聚合根 Order 來訪問和修改 OrderItem。

## 領域服務（Domain Service）示例

```csharp
public interface IPricingService
{
    Money CalculateDiscountedPrice(CustomerId customerId, List<OrderItem> items);
}

public class PricingService : IPricingService
{
    private readonly ICustomerRepository _customerRepository;
    private readonly IPromotionRepository _promotionRepository;
    
    public PricingService(
        ICustomerRepository customerRepository, 
        IPromotionRepository promotionRepository)
    {
        _customerRepository = customerRepository;
        _promotionRepository = promotionRepository;
    }
    
    public Money CalculateDiscountedPrice(CustomerId customerId, List<OrderItem> items)
    {
        var customer = _customerRepository.FindById(customerId);
        var applicablePromotions = _promotionRepository.GetActivePromotions();
        
        var originalTotal = items.Aggregate(
            new Money(0, "TWD"), 
            (sum, item) => sum.Add(item.GetSubTotal())
        );
        
        // 計算 VIP 折扣
        if (customer.IsVip)
        {
            originalTotal = ApplyVipDiscount(originalTotal);
        }
        
        // 計算促銷折扣
        foreach (var promotion in applicablePromotions)
        {
            if (promotion.IsApplicable(items, customer))
            {
                originalTotal = promotion.ApplyDiscount(originalTotal);
            }
        }
        
        return originalTotal;
    }
    
    private Money ApplyVipDiscount(Money amount)
    {
        // VIP 享 9 折優惠
        return new Money(amount.Amount * 0.9m, amount.Currency);
    }
}

// 在訂單聚合中使用領域服務
public class Order
{
    public void ApplyPricing(IPricingService pricingService)
    {
        TotalAmount = pricingService.CalculateDiscountedPrice(CustomerId, _items.ToList());
    }
}
```

**為什麼是領域服務**：價格計算邏輯涉及多個聚合（客戶、促銷、訂單），且這個行為不自然地屬於任何單一實體或值對象。它封裝了跨聚合的複雜業務邏輯。

## 總結

- **實體（Order）**：有唯一標識，狀態可變，封裝業務行為
- **值對象（Money, Address）**：無唯一標識，不可變，由屬性值定義相等性
- **聚合（Order + OrderItem）**：保證業務不變性，統一的修改邊界
- **領域服務（PricingService）**：跨聚合的業務邏輯，無狀態的純業務操作

這些構建塊協同工作，形成了一個清晰表達業務意圖的領域模型。​​​​​​​​​​​​​​​​
