
## 實體（Entity）示例

```csharp
public class BankAccount
{
    public AccountNumber AccountNumber { get; private set; }  // 唯一標識
    public CustomerId OwnerId { get; private set; }
    public Money Balance { get; private set; }
    public AccountType Type { get; private set; }
    public DateTime OpenedDate { get; private set; }
    public AccountStatus Status { get; private set; }
    private List<Transaction> _transactions = new List<Transaction>();
    
    public BankAccount(AccountNumber accountNumber, CustomerId ownerId, AccountType type)
    {
        AccountNumber = accountNumber;
        OwnerId = ownerId;
        Type = type;
        Balance = new Money(0, "TWD");
        OpenedDate = DateTime.Now;
        Status = AccountStatus.Active;
    }
    
    public void Deposit(Money amount, string description)
    {
        if (Status != AccountStatus.Active)
            throw new DomainException("非活躍帳戶無法存款");
            
        if (amount.Amount <= 0)
            throw new DomainException("存款金額必須大於零");
            
        Balance = Balance.Add(amount);
        AddTransaction(TransactionType.Deposit, amount, description);
    }
    
    public void Withdraw(Money amount, string description)
    {
        if (Status != AccountStatus.Active)
            throw new DomainException("非活躍帳戶無法提款");
            
        if (amount.Amount <= 0)
            throw new DomainException("提款金額必須大於零");
            
        if (Balance.Amount < amount.Amount)
            throw new DomainException("餘額不足");
            
        Balance = Balance.Subtract(amount);
        AddTransaction(TransactionType.Withdrawal, amount, description);
    }
    
    private void AddTransaction(TransactionType type, Money amount, string description)
    {
        var transaction = new Transaction(
            TransactionId.NewId(),
            type,
            amount,
            Balance,
            description,
            DateTime.Now
        );
        _transactions.Add(transaction);
    }
}
```

**為什麼是實體**：銀行帳戶有唯一的帳號，即使餘額和狀態改變，它仍是同一個帳戶。

## 值對象（Value Object）示例

```csharp
public class AccountNumber
{
    public string Value { get; private set; }
    
    public AccountNumber(string value)
    {
        if (string.IsNullOrWhiteSpace(value))
            throw new ArgumentException("帳號不能為空");
            
        if (!IsValidFormat(value))
            throw new ArgumentException("帳號格式不正確");
            
        Value = value;
    }
    
    private bool IsValidFormat(string accountNumber)
    {
        // 假設帳號格式：3位分行代碼 + 7位序號
        return accountNumber.Length == 10 && accountNumber.All(char.IsDigit);
    }
    
    public override bool Equals(object obj)
    {
        return obj is AccountNumber other && Value == other.Value;
    }
    
    public override int GetHashCode() => Value.GetHashCode();
}

public class Money
{
    public decimal Amount { get; private set; }
    public string Currency { get; private set; }
    
    public Money(decimal amount, string currency)
    {
        if (amount < 0) throw new ArgumentException("金額不能為負數");
        Amount = amount;
        Currency = currency ?? throw new ArgumentNullException(nameof(currency));
    }
    
    public Money Add(Money other)
    {
        EnsureSameCurrency(other);
        return new Money(Amount + other.Amount, Currency);
    }
    
    public Money Subtract(Money other)
    {
        EnsureSameCurrency(other);
        return new Money(Amount - other.Amount, Currency);
    }
    
    private void EnsureSameCurrency(Money other)
    {
        if (Currency != other.Currency)
            throw new InvalidOperationException($"無法操作不同幣別：{Currency} vs {other.Currency}");
    }
}

public class InterestRate
{
    public decimal Rate { get; private set; }
    public RateType Type { get; private set; }
    
    public InterestRate(decimal rate, RateType type)
    {
        if (rate < 0) throw new ArgumentException("利率不能為負數");
        if (rate > 1 && type == RateType.Percentage)
            throw new ArgumentException("百分比利率不能超過 100%");
            
        Rate = rate;
        Type = type;
    }
    
    public Money CalculateInterest(Money principal, int days)
    {
        var dailyRate = Type == RateType.Annual ? Rate / 365 : Rate;
        var interestAmount = principal.Amount * dailyRate * days;
        return new Money(interestAmount, principal.Currency);
    }
}
```

## 聚合（Aggregate）示例

```csharp
// 銀行帳戶聚合，BankAccount 是聚合根
public class BankAccount  // 聚合根
{
    public AccountNumber AccountNumber { get; private set; }
    public CustomerId OwnerId { get; private set; }
    public Money Balance { get; private set; }
    public InterestRate InterestRate { get; private set; }
    
    private List<Transaction> _transactions = new List<Transaction>();
    public IReadOnlyList<Transaction> Transactions => _transactions.AsReadOnly();
    
    private List<AutoTransfer> _autoTransfers = new List<AutoTransfer>();
    public IReadOnlyList<AutoTransfer> AutoTransfers => _autoTransfers.AsReadOnly();
    
    public void SetupAutoTransfer(
        AccountNumber targetAccount, 
        Money amount, 
        AutoTransferFrequency frequency,
        DateTime startDate)
    {
        if (Status != AccountStatus.Active)
            throw new DomainException("非活躍帳戶無法設定自動轉帳");
            
        // 檢查是否已存在相同目標帳戶的自動轉帳
        if (_autoTransfers.Any(at => at.TargetAccount.Equals(targetAccount) && at.IsActive))
            throw new DomainException("已存在至該帳戶的自動轉帳設定");
            
        var autoTransfer = new AutoTransfer(
            AutoTransferId.NewId(),
            targetAccount,
            amount,
            frequency,
            startDate
        );
        
        _autoTransfers.Add(autoTransfer);
    }
    
    public void ExecuteAutoTransfer(AutoTransferId transferId, ITransferService transferService)
    {
        var autoTransfer = _autoTransfers.FirstOrDefault(at => at.Id.Equals(transferId));
        if (autoTransfer == null)
            throw new DomainException("找不到指定的自動轉帳設定");
            
        if (!autoTransfer.ShouldExecuteToday())
            return;
            
        // 使用領域服務執行轉帳
        transferService.Transfer(AccountNumber, autoTransfer.TargetAccount, autoTransfer.Amount);
        autoTransfer.MarkAsExecuted(DateTime.Today);
    }
}

// 聚合內的實體
public class Transaction
{
    public TransactionId Id { get; private set; }
    public TransactionType Type { get; private set; }
    public Money Amount { get; private set; }
    public Money BalanceAfter { get; private set; }
    public string Description { get; private set; }
    public DateTime Timestamp { get; private set; }
    
    public Transaction(
        TransactionId id, 
        TransactionType type, 
        Money amount, 
        Money balanceAfter, 
        string description, 
        DateTime timestamp)
    {
        Id = id;
        Type = type;
        Amount = amount;
        BalanceAfter = balanceAfter;
        Description = description;
        Timestamp = timestamp;
    }
}

// 聚合內的另一個實體
public class AutoTransfer
{
    public AutoTransferId Id { get; private set; }
    public AccountNumber TargetAccount { get; private set; }
    public Money Amount { get; private set; }
    public AutoTransferFrequency Frequency { get; private set; }
    public DateTime StartDate { get; private set; }
    public DateTime? LastExecutedDate { get; private set; }
    public bool IsActive { get; private set; }
    
    public AutoTransfer(
        AutoTransferId id,
        AccountNumber targetAccount,
        Money amount,
        AutoTransferFrequency frequency,
        DateTime startDate)
    {
        Id = id;
        TargetAccount = targetAccount;
        Amount = amount;
        Frequency = frequency;
        StartDate = startDate;
        IsActive = true;
    }
    
    public bool ShouldExecuteToday()
    {
        if (!IsActive || DateTime.Today < StartDate)
            return false;
            
        if (LastExecutedDate == null)
            return DateTime.Today >= StartDate;
            
        return Frequency switch
        {
            AutoTransferFrequency.Daily => LastExecutedDate.Value.Date < DateTime.Today,
            AutoTransferFrequency.Weekly => LastExecutedDate.Value.AddDays(7).Date <= DateTime.Today,
            AutoTransferFrequency.Monthly => LastExecutedDate.Value.AddMonths(1).Date <= DateTime.Today,
            _ => false
        };
    }
    
    public void MarkAsExecuted(DateTime executedDate)
    {
        LastExecutedDate = executedDate;
    }
    
    public void Deactivate()
    {
        IsActive = false;
    }
}
```

## 領域服務（Domain Service）示例

```csharp
public interface ITransferService
{
    void Transfer(AccountNumber fromAccount, AccountNumber toAccount, Money amount);
    bool CanTransfer(AccountNumber fromAccount, AccountNumber toAccount, Money amount);
}

public class TransferService : ITransferService
{
    private readonly IBankAccountRepository _accountRepository;
    private readonly IFraudDetectionService _fraudDetectionService;
    private readonly IExchangeRateService _exchangeRateService;
    
    public TransferService(
        IBankAccountRepository accountRepository,
        IFraudDetectionService fraudDetectionService,
        IExchangeRateService exchangeRateService)
    {
        _accountRepository = accountRepository;
        _fraudDetectionService = fraudDetectionService;
        _exchangeRateService = exchangeRateService;
    }
    
    public void Transfer(AccountNumber fromAccount, AccountNumber toAccount, Money amount)
    {
        // 載入相關聚合
        var sourceAccount = _accountRepository.FindByAccountNumber(fromAccount);
        var targetAccount = _accountRepository.FindByAccountNumber(toAccount);
        
        if (sourceAccount == null || targetAccount == null)
            throw new DomainException("找不到指定的帳戶");
            
        // 欺詐檢測
        if (!_fraudDetectionService.IsTransferSafe(sourceAccount.AccountNumber, targetAccount.AccountNumber, amount))
            throw new DomainException("此轉帳被標記為可疑交易");
            
        // 處理不同幣別的轉換
        var transferAmount = amount;
        if (sourceAccount.Balance.Currency != targetAccount.Balance.Currency)
        {
            var exchangeRate = _exchangeRateService.GetExchangeRate(
                sourceAccount.Balance.Currency, 
                targetAccount.Balance.Currency
            );
            transferAmount = new Money(
                amount.Amount * exchangeRate, 
                targetAccount.Balance.Currency
            );
        }
        
        // 執行轉帳（跨聚合的操作）
        sourceAccount.Withdraw(amount, $"轉出至 {toAccount.Value}");
        targetAccount.Deposit(transferAmount, $"轉入自 {fromAccount.Value}");
        
        // 保存變更
        _accountRepository.Save(sourceAccount);
        _accountRepository.Save(targetAccount);
    }
    
    public bool CanTransfer(AccountNumber fromAccount, AccountNumber toAccount, Money amount)
    {
        var sourceAccount = _accountRepository.FindByAccountNumber(fromAccount);
        
        if (sourceAccount == null)
            return false;
            
        // 檢查餘額是否足夠
        if (sourceAccount.Balance.Amount < amount.Amount)
            return false;
            
        // 檢查每日轉帳限額
        var dailyTransferLimit = GetDailyTransferLimit(sourceAccount.Type);
        var todayTransferAmount = CalculateTodayTransferAmount(sourceAccount);
        
        return (todayTransferAmount + amount.Amount) <= dailyTransferLimit;
    }
    
    private decimal GetDailyTransferLimit(AccountType accountType)
    {
        return accountType switch
        {
            AccountType.Savings => 100000m,
            AccountType.Current => 500000m,
            AccountType.Premium => 1000000m,
            _ => 50000m
        };
    }
    
    private decimal CalculateTodayTransferAmount(BankAccount account)
    {
        var today = DateTime.Today;
        return account.Transactions
            .Where(t => t.Type == TransactionType.Withdrawal && 
                       t.Timestamp.Date == today &&
                       t.Description.Contains("轉出"))
            .Sum(t => t.Amount.Amount);
    }
}

// 另一個領域服務：利息計算
public interface IInterestCalculationService
{
    Money CalculateMonthlyInterest(BankAccount account);
    void ApplyMonthlyInterest(BankAccount account);
}

public class InterestCalculationService : IInterestCalculationService
{
    public Money CalculateMonthlyInterest(BankAccount account)
    {
        if (account.Type == AccountType.Current)
            return new Money(0, account.Balance.Currency); // 活期帳戶無利息
            
        var daysInMonth = DateTime.DaysInMonth(DateTime.Today.Year, DateTime.Today.Month);
        return account.InterestRate.CalculateInterest(account.Balance, daysInMonth);
    }
    
    public void ApplyMonthlyInterest(BankAccount account)
    {
        var interest = CalculateMonthlyInterest(account);
        if (interest.Amount > 0)
        {
            account.Deposit(interest, "月利息入帳");
        }
    }
}
```

## 總結

在這個銀行領域的例子中：

- **實體（BankAccount）**：有唯一的帳號標識，狀態可變（餘額、狀態等），封裝帳戶相關的業務行為
- **值對象（AccountNumber, Money, InterestRate）**：無唯一標識，不可變，代表領域概念
- **聚合（BankAccount + Transaction + AutoTransfer）**：保證帳戶數據的一致性，確保所有交易記錄與餘額同步
- **領域服務（TransferService, InterestCalculationService）**：跨帳戶的轉帳邏輯，以及複雜的利息計算邏輯

這個例子展現了 DDD 如何幫助我們將複雜的金融業務邏輯清晰地組織和表達，每個組件都有明確的職責和邊界。​​​​​​​​​​​​​​​​
