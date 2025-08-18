

---
我來用製造執行系統（MES）的生產訂單管理場景為您展示 DDD 概念：

## 實體（Entity）示例

```csharp
public class ProductionOrder
{
    public ProductionOrderId Id { get; private set; }  // 唯一標識
    public ProductCode ProductCode { get; private set; }
    public int PlannedQuantity { get; private set; }
    public int ProducedQuantity { get; private set; }
    public ProductionOrderStatus Status { get; private set; }
    public WorkCenter AssignedWorkCenter { get; private set; }
    public DateTime PlannedStartTime { get; private set; }
    public DateTime PlannedEndTime { get; private set; }
    public DateTime? ActualStartTime { get; private set; }
    public DateTime? ActualEndTime { get; private set; }
    public Priority Priority { get; private set; }
    
    private List<ProductionStep> _steps = new List<ProductionStep>();
    private List<QualityCheck> _qualityChecks = new List<QualityCheck>();
    
    public ProductionOrder(
        ProductionOrderId id,
        ProductCode productCode,
        int plannedQuantity,
        WorkCenter workCenter,
        DateTime plannedStartTime,
        DateTime plannedEndTime,
        Priority priority)
    {
        Id = id;
        ProductCode = productCode;
        PlannedQuantity = plannedQuantity;
        ProducedQuantity = 0;
        Status = ProductionOrderStatus.Created;
        AssignedWorkCenter = workCenter;
        PlannedStartTime = plannedStartTime;
        PlannedEndTime = plannedEndTime;
        Priority = priority;
    }
    
    public void StartProduction(OperatorId operatorId, DateTime startTime)
    {
        if (Status != ProductionOrderStatus.Ready)
            throw new DomainException($"生產訂單狀態必須為就緒才能開始生產，當前狀態：{Status}");
            
        if (startTime < DateTime.Now.AddMinutes(-5)) // 允許5分鐘的時間差
            throw new DomainException("開始時間不能早於當前時間");
            
        Status = ProductionOrderStatus.InProgress;
        ActualStartTime = startTime;
        
        // 領域事件
        DomainEvents.Raise(new ProductionStartedEvent(Id, operatorId, startTime));
    }
    
    public void CompleteProduction(DateTime endTime)
    {
        if (Status != ProductionOrderStatus.InProgress)
            throw new DomainException("只有進行中的生產訂單才能完成");
            
        if (ProducedQuantity == 0)
            throw new DomainException("未生產任何產品，無法完成生產訂單");
            
        Status = ProductionOrderStatus.Completed;
        ActualEndTime = endTime;
        
        DomainEvents.Raise(new ProductionCompletedEvent(Id, ProducedQuantity, endTime));
    }
    
    public void ReportProduction(int quantity, DateTime reportTime)
    {
        if (Status != ProductionOrderStatus.InProgress)
            throw new DomainException("只有進行中的生產訂單才能報工");
            
        if (quantity <= 0)
            throw new DomainException("報工數量必須大於零");
            
        if (ProducedQuantity + quantity > PlannedQuantity)
            throw new DomainException("總生產數量不能超過計劃數量");
            
        ProducedQuantity += quantity;
        
        DomainEvents.Raise(new ProductionReportedEvent(Id, quantity, ProducedQuantity, reportTime));
    }
}

public class Equipment
{
    public EquipmentId Id { get; private set; }
    public string Name { get; private set; }
    public EquipmentType Type { get; private set; }
    public EquipmentStatus Status { get; private set; }
    public WorkCenter WorkCenter { get; private set; }
    public DateTime LastMaintenanceDate { get; private set; }
    public OperatingParameters Parameters { get; private set; }
    
    private List<MaintenanceRecord> _maintenanceHistory = new List<MaintenanceRecord>();
    private List<AlarmRecord> _alarmHistory = new List<AlarmRecord>();
    
    public void StartOperation(OperatorId operatorId, OperatingParameters parameters)
    {
        if (Status != EquipmentStatus.Ready)
            throw new DomainException($"設備狀態必須為就緒才能開始作業，當前狀態：{Status}");
            
        Status = EquipmentStatus.Running;
        Parameters = parameters;
        
        DomainEvents.Raise(new EquipmentOperationStartedEvent(Id, operatorId, parameters));
    }
    
    public void ReportAlarm(AlarmCode alarmCode, AlarmSeverity severity, string description)
    {
        var alarm = new AlarmRecord(
            AlarmId.NewId(),
            alarmCode,
            severity,
            description,
            DateTime.Now
        );
        
        _alarmHistory.Add(alarm);
        
        if (severity == AlarmSeverity.Critical)
        {
            Status = EquipmentStatus.Alarm;
            DomainEvents.Raise(new CriticalAlarmEvent(Id, alarm));
        }
    }
}
```

## 值對象（Value Object）示例

```csharp
public class ProductionOrderId
{
    public string Value { get; private set; }
    
    public ProductionOrderId(string value)
    {
        if (string.IsNullOrWhiteSpace(value))
            throw new ArgumentException("生產訂單號不能為空");
            
        if (!IsValidFormat(value))
            throw new ArgumentException("生產訂單號格式不正確");
            
        Value = value;
    }
    
    private bool IsValidFormat(string orderNumber)
    {
        // 格式：PO + 8位日期 + 4位序號，例如：PO202408100001
        return orderNumber.Length == 14 && 
               orderNumber.StartsWith("PO") && 
               orderNumber.Substring(2).All(char.IsDigit);
    }
    
    public static ProductionOrderId Generate()
    {
        var dateStr = DateTime.Today.ToString("yyyyMMdd");
        var sequence = GenerateSequence(); // 假設有序號生成邏輯
        return new ProductionOrderId($"PO{dateStr}{sequence:D4}");
    }
}

public class OperatingParameters
{
    public Temperature Temperature { get; private set; }
    public Pressure Pressure { get; private set; }
    public Speed Speed { get; private set; }
    public Dictionary<string, decimal> CustomParameters { get; private set; }
    
    public OperatingParameters(
        Temperature temperature, 
        Pressure pressure, 
        Speed speed,
        Dictionary<string, decimal> customParameters = null)
    {
        Temperature = temperature;
        Pressure = pressure;
        Speed = speed;
        CustomParameters = customParameters ?? new Dictionary<string, decimal>();
    }
    
    public bool IsWithinLimits(OperatingLimits limits)
    {
        return Temperature.IsWithinRange(limits.TemperatureRange) &&
               Pressure.IsWithinRange(limits.PressureRange) &&
               Speed.IsWithinRange(limits.SpeedRange);
    }
}

public class Temperature
{
    public decimal Value { get; private set; }
    public TemperatureUnit Unit { get; private set; }
    
    public Temperature(decimal value, TemperatureUnit unit)
    {
        if (unit == TemperatureUnit.Celsius && value < -273.15m)
            throw new ArgumentException("攝氏溫度不能低於絕對零度");
        if (unit == TemperatureUnit.Kelvin && value < 0)
            throw new ArgumentException("開爾文溫度不能為負數");
            
        Value = value;
        Unit = unit;
    }
    
    public bool IsWithinRange(TemperatureRange range)
    {
        var convertedValue = ConvertTo(range.Unit);
        return convertedValue >= range.MinValue && convertedValue <= range.MaxValue;
    }
    
    public decimal ConvertTo(TemperatureUnit targetUnit)
    {
        if (Unit == targetUnit) return Value;
        
        return (Unit, targetUnit) switch
        {
            (TemperatureUnit.Celsius, TemperatureUnit.Kelvin) => Value + 273.15m,
            (TemperatureUnit.Kelvin, TemperatureUnit.Celsius) => Value - 273.15m,
            (TemperatureUnit.Celsius, TemperatureUnit.Fahrenheit) => Value * 9 / 5 + 32,
            (TemperatureUnit.Fahrenheit, TemperatureUnit.Celsius) => (Value - 32) * 5 / 9,
            _ => throw new InvalidOperationException($"不支援的溫度轉換：{Unit} to {targetUnit}")
        };
    }
}

public class WorkCenter
{
    public string Code { get; private set; }
    public string Name { get; private set; }
    public WorkCenterType Type { get; private set; }
    public ProductionCapacity Capacity { get; private set; }
    
    public WorkCenter(string code, string name, WorkCenterType type, ProductionCapacity capacity)
    {
        if (string.IsNullOrWhiteSpace(code))
            throw new ArgumentException("工作中心代碼不能為空");
            
        Code = code;
        Name = name;
        Type = type;
        Capacity = capacity;
    }
}
```

## 聚合（Aggregate）示例

```csharp
// 生產批次聚合，ProductionBatch 是聚合根
public class ProductionBatch  // 聚合根
{
    public BatchId Id { get; private set; }
    public ProductCode ProductCode { get; private set; }
    public int BatchSize { get; private set; }
    public BatchStatus Status { get; private set; }
    public DateTime CreatedDate { get; private set; }
    public Recipe Recipe { get; private set; }
    
    private List<BatchStep> _steps = new List<BatchStep>();
    public IReadOnlyList<BatchStep> Steps => _steps.AsReadOnly();
    
    private List<MaterialConsumption> _materialConsumptions = new List<MaterialConsumption>();
    public IReadOnlyList<MaterialConsumption> MaterialConsumptions => _materialConsumptions.AsReadOnly();
    
    private List<QualityTestResult> _qualityResults = new List<QualityTestResult>();
    public IReadOnlyList<QualityTestResult> QualityResults => _qualityResults.AsReadOnly();
    
    public ProductionBatch(
        BatchId id,
        ProductCode productCode,
        int batchSize,
        Recipe recipe)
    {
        Id = id;
        ProductCode = productCode;
        BatchSize = batchSize;
        Recipe = recipe;
        Status = BatchStatus.Created;
        CreatedDate = DateTime.Now;
        
        InitializeBatchSteps();
    }
    
    private void InitializeBatchSteps()
    {
        foreach (var recipeStep in Recipe.Steps)
        {
            var batchStep = new BatchStep(
                BatchStepId.NewId(),
                recipeStep.StepNumber,
                recipeStep.Operation,
                recipeStep.Parameters,
                recipeStep.ExpectedDuration
            );
            _steps.Add(batchStep);
        }
    }
    
    public void StartStep(int stepNumber, OperatorId operatorId, EquipmentId equipmentId)
    {
        var step = _steps.FirstOrDefault(s => s.StepNumber == stepNumber);
        if (step == null)
            throw new DomainException($"找不到步驟編號：{stepNumber}");
            
        if (stepNumber > 1)
        {
            var previousStep = _steps.FirstOrDefault(s => s.StepNumber == stepNumber - 1);
            if (previousStep?.Status != BatchStepStatus.Completed)
                throw new DomainException("前一步驟尚未完成");
        }
        
        step.Start(operatorId, equipmentId);
        
        if (stepNumber == 1 && Status == BatchStatus.Created)
        {
            Status = BatchStatus.InProgress;
        }
        
        DomainEvents.Raise(new BatchStepStartedEvent(Id, stepNumber, operatorId, equipmentId));
    }
    
    public void CompleteStep(int stepNumber, OperatingParameters actualParameters)
    {
        var step = _steps.FirstOrDefault(s => s.StepNumber == stepNumber);
        if (step == null)
            throw new DomainException($"找不到步驟編號：{stepNumber}");
            
        step.Complete(actualParameters);
        
        // 檢查是否所有步驟都已完成
        if (_steps.All(s => s.Status == BatchStepStatus.Completed))
        {
            Status = BatchStatus.Completed;
            DomainEvents.Raise(new BatchCompletedEvent(Id, BatchSize));
        }
        
        DomainEvents.Raise(new BatchStepCompletedEvent(Id, stepNumber, actualParameters));
    }
    
    public void ConsumeMaterial(MaterialCode materialCode, decimal quantity, string lotNumber)
    {
        if (Status != BatchStatus.InProgress)
            throw new DomainException("只有進行中的批次才能消耗物料");
            
        var consumption = new MaterialConsumption(
            materialCode,
            quantity,
            lotNumber,
            DateTime.Now
        );
        
        _materialConsumptions.Add(consumption);
        
        // 檢查物料消耗是否符合配方要求
        ValidateMaterialConsumption(materialCode, quantity);
    }
    
    public void RecordQualityTest(QualityTestType testType, TestResult result, decimal measuredValue)
    {
        var qualityResult = new QualityTestResult(
            QualityTestId.NewId(),
            testType,
            result,
            measuredValue,
            DateTime.Now
        );
        
        _qualityResults.Add(qualityResult);
        
        if (result == TestResult.Failed)
        {
            Status = BatchStatus.QualityHold;
            DomainEvents.Raise(new BatchQualityFailedEvent(Id, testType, measuredValue));
        }
    }
    
    private void ValidateMaterialConsumption(MaterialCode materialCode, decimal quantity)
    {
        var requiredMaterial = Recipe.Materials.FirstOrDefault(m => m.MaterialCode == materialCode);
        if (requiredMaterial == null)
            throw new DomainException($"配方中未包含物料：{materialCode.Value}");
            
        var totalConsumed = _materialConsumptions
            .Where(mc => mc.MaterialCode == materialCode)
            .Sum(mc => mc.Quantity);
            
        var expectedQuantity = requiredMaterial.Quantity * BatchSize / Recipe.StandardBatchSize;
        var tolerance = expectedQuantity * 0.05m; // 5% 容差
        
        if (totalConsumed > expectedQuantity + tolerance)
        {
            throw new DomainException($"物料 {materialCode.Value} 消耗量超出容許範圍");
        }
    }
}

// 聚合內的實體
public class BatchStep
{
    public BatchStepId Id { get; private set; }
    public int StepNumber { get; private set; }
    public Operation Operation { get; private set; }
    public OperatingParameters PlannedParameters { get; private set; }
    public OperatingParameters ActualParameters { get; private set; }
    public TimeSpan ExpectedDuration { get; private set; }
    public DateTime? StartTime { get; private set; }
    public DateTime? EndTime { get; private set; }
    public BatchStepStatus Status { get; private set; }
    public OperatorId AssignedOperator { get; private set; }
    public EquipmentId AssignedEquipment { get; private set; }
    
    public BatchStep(
        BatchStepId id,
        int stepNumber,
        Operation operation,
        OperatingParameters plannedParameters,
        TimeSpan expectedDuration)
    {
        Id = id;
        StepNumber = stepNumber;
        Operation = operation;
        PlannedParameters = plannedParameters;
        ExpectedDuration = expectedDuration;
        Status = BatchStepStatus.NotStarted;
    }
    
    public void Start(OperatorId operatorId, EquipmentId equipmentId)
    {
        if (Status != BatchStepStatus.NotStarted)
            throw new DomainException($"步驟 {StepNumber} 已經開始或完成");
            
        AssignedOperator = operatorId;
        AssignedEquipment = equipmentId;
        StartTime = DateTime.Now;
        Status = BatchStepStatus.InProgress;
    }
    
    public void Complete(OperatingParameters actualParameters)
    {
        if (Status != BatchStepStatus.InProgress)
            throw new DomainException($"步驟 {StepNumber} 尚未開始");
            
        ActualParameters = actualParameters;
        EndTime = DateTime.Now;
        Status = BatchStepStatus.Completed;
    }
    
    public TimeSpan GetActualDuration()
    {
        if (StartTime.HasValue && EndTime.HasValue)
            return EndTime.Value - StartTime.Value;
        return TimeSpan.Zero;
    }
}

public class MaterialConsumption
{
    public MaterialCode MaterialCode { get; private set; }
    public decimal Quantity { get; private set; }
    public string LotNumber { get; private set; }
    public DateTime ConsumedAt { get; private set; }
    
    public MaterialConsumption(MaterialCode materialCode, decimal quantity, string lotNumber, DateTime consumedAt)
    {
        MaterialCode = materialCode;
        Quantity = quantity;
        LotNumber = lotNumber;
        ConsumedAt = consumedAt;
    }
}
```

## 領域服務（Domain Service）示例

```csharp
public interface IProductionSchedulingService
{
    SchedulingResult ScheduleProductionOrder(ProductionOrder order, IEnumerable<WorkCenter> availableWorkCenters);
    bool CanReschedule(ProductionOrderId orderId, DateTime newStartTime, WorkCenter newWorkCenter);
    void OptimizeSchedule(IEnumerable<ProductionOrder> orders);
}

public class ProductionSchedulingService : IProductionSchedulingService
{
    private readonly IProductionOrderRepository _orderRepository;
    private readonly IWorkCenterRepository _workCenterRepository;
    private readonly IEquipmentRepository _equipmentRepository;
    
    public ProductionSchedulingService(
        IProductionOrderRepository orderRepository,
        IWorkCenterRepository workCenterRepository,
        IEquipmentRepository equipmentRepository)
    {
        _orderRepository = orderRepository;
        _workCenterRepository = workCenterRepository;
        _equipmentRepository = equipmentRepository;
    }
    
    public SchedulingResult ScheduleProductionOrder(
        ProductionOrder order, 
        IEnumerable<WorkCenter> availableWorkCenters)
    {
        // 根據產品類型篩選適合的工作中心
        var suitableWorkCenters = availableWorkCenters
            .Where(wc => CanProduceProduct(wc, order.ProductCode))
            .ToList();
            
        if (!suitableWorkCenters.Any())
        {
            return SchedulingResult.Failed("沒有適合的工作中心可以生產此產品");
        }
        
        // 計算每個工作中心的負荷
        var workloadAnalysis = AnalyzeWorkCenterLoad(suitableWorkCenters, order.PlannedStartTime);
        
        // 選擇負荷最輕且能力最適合的工作中心
        var selectedWorkCenter = SelectOptimalWorkCenter(workloadAnalysis, order);
        
        if (selectedWorkCenter == null)
        {
            return SchedulingResult.Failed("所有工作中心都已滿載");
        }
        
        // 計算實際開始和結束時間
        var scheduledTimes = CalculateProductionTimes(order, selectedWorkCenter);
        
        // 檢查設備可用性
        var requiredEquipment = GetRequiredEquipment(order.ProductCode, selectedWorkCenter);
        if (!IsEquipmentAvailable(requiredEquipment, scheduledTimes.StartTime, scheduledTimes.EndTime))
        {
            return SchedulingResult.Failed("所需設備在計劃時間內不可用");
        }
        
        return SchedulingResult.Success(selectedWorkCenter, scheduledTimes.StartTime, scheduledTimes.EndTime);
    }
    
    public bool CanReschedule(ProductionOrderId orderId, DateTime newStartTime, WorkCenter newWorkCenter)
    {
        var order = _orderRepository.FindById(orderId);
        if (order == null) return false;
        
        // 檢查訂單狀態是否允許重新排程
        if (order.Status == ProductionOrderStatus.InProgress || 
            order.Status == ProductionOrderStatus.Completed)
        {
            return false;
        }
        
        // 檢查新工作中心的產能
        var workload = CalculateWorkCenterLoad(newWorkCenter, newStartTime);
        var orderDuration = EstimateProductionDuration(order.ProductCode, order.PlannedQuantity);
        
        return workload.AvailableCapacity >= orderDuration;
    }
    
    private bool CanProduceProduct(WorkCenter workCenter, ProductCode productCode)
    {
        // 根據工作中心類型和產品特性判斷是否能生產
        var productRequirements = GetProductRequirements(productCode);
        return workCenter.Type.SupportsOperations(productRequirements.RequiredOperations);
    }
    
    private List<WorkCenterLoad> AnalyzeWorkCenterLoad(
        List<WorkCenter> workCenters, 
        DateTime planningStartTime)
    {
        var loads = new List<WorkCenterLoad>();
        
        foreach (var workCenter in workCenters)
        {
            var scheduledOrders = _orderRepository
                .FindByWorkCenterAndTimeRange(workCenter, planningStartTime, planningStartTime.AddDays(7));
                
            var totalScheduledTime = scheduledOrders
                .Sum(o => EstimateProductionDuration(o.ProductCode, o.PlannedQuantity).TotalHours);
                
            var availableHours = workCenter.Capacity.HoursPerWeek;
            var utilizationRate = totalScheduledTime / availableHours;
            
            loads.Add(new WorkCenterLoad(workCenter, utilizationRate, availableHours - totalScheduledTime));
        }
        
        return loads;
    }
    
    private WorkCenter SelectOptimalWorkCenter(List<WorkCenterLoad> workloads, ProductionOrder order)
    {
        // 優先選擇利用率低且效率高的工作中心
        return workloads
            .Where(wl => wl.AvailableCapacity > 0)
            .OrderBy(wl => wl.UtilizationRate)
            .ThenByDescending(wl => GetEfficiencyRating(wl.WorkCenter, order.ProductCode))
            .FirstOrDefault()?.WorkCenter;
    }
    
    private TimeSpan EstimateProductionDuration(ProductCode productCode, int quantity)
    {
        var productInfo = GetProductInfo(productCode);
        var baseTimePerUnit = productInfo.StandardProcessingTime;
        var setupTime = productInfo.SetupTime;
        
        return setupTime.Add(TimeSpan.FromMinutes(baseTimePerUnit.TotalMinutes * quantity));
    }
}

// 另一個重要的領域服務：品質管控
public interface IQualityControlService
{
    QualityInspectionResult InspectBatch(ProductionBatch batch);
    bool RequiresAdditionalTesting(ProductionBatch batch, QualityTestResult testResult);
    QualityDisposition DetermineDisposition(ProductionBatch batch);
}

public class QualityControlService : IQualityControlService
{
    private readonly IQualitySpecificationRepository _specRepository;
    private readonly IQualityHistoryRepository _historyRepository;
    
    public QualityControlService(
        IQualitySpecificationRepository specRepository,
        IQualityHistoryRepository historyRepository)
    {
        _specRepository = specRepository;
        _historyRepository = historyRepository;
    }
    
    public QualityInspectionResult InspectBatch(ProductionBatch batch)
    {
        var specification = _specRepository.GetByProductCode(batch.ProductCode);
        var inspectionPlan = CreateInspectionPlan(batch, specification);
        
        var results = new List<QualityTestResult>();
        
        foreach (var testPlan in inspectionPlan.RequiredTests)
        {
            var testResult = ExecuteQualityTest(batch, testPlan);
            results.Add(testResult);
            
            // 如果關鍵測試失敗，立即停止後續測試
            if (testPlan.IsCritical && testResult.Result == TestResult.Failed)
            {
                return QualityInspectionResult.Failed(results, "關鍵品質測試失敗");
            }
        }
        
        var overallResult = DetermineOverallResult(results, specification);
        return new QualityInspectionResult(overallResult, results);
    }
    
    public QualityDisposition DetermineDisposition(ProductionBatch batch)
    {
        var passedTests = batch.QualityResults.Count(qr => qr.Result == TestResult.Passed);
        var failedTests = batch.QualityResults.Count(qr => qr.Result == TestResult.Failed);
        
        if (failedTests == 0)
            return QualityDisposition.Accept;
            
        // 檢查是否有關鍵測試失敗
        var criticalTestsFailed = batch.QualityResults.Any(qr => 
            qr.Result == TestResult.Failed && IsCriticalTest(qr.TestType));
            
        if (criticalTestsFailed)
            return QualityDisposition.Reject;
            
        // 根據失敗率決定處置方式
        var failureRate = (double)failedTests / (passedTests + failedTests);
        
        return failureRate switch
        {
            <= 0.05 => QualityDisposition.AcceptWithDeviation,
            <= 0.15 => QualityDisposition.Rework,
            _ => QualityDisposition.Reject
        };
    }
    
    private bool IsCriticalTest(QualityTestType testType)
    {
        return testType == QualityTestType.Safety || 
               testType == QualityTestType.Purity ||
               testType == QualityTestType.Potency;
    }
}
```

## 總結

在這個 MES 領域的例子中：

- **實體（ProductionOrder, Equipment）**：有唯一標識的核心業務對象，封裝了生產和設備管理的業務邏輯
- **值對象（ProductionOrderId, OperatingParameters, Temperature, WorkCenter）**：表示領域概念，無唯一標識但具有豐富的行為
- **聚合（ProductionBatch + BatchStep + MaterialConsumption）**：確保生產批次數據的一致性，管理複雜的生產步驟和物料追蹤
- **領域服務（ProductionSchedulingService, QualityControlService）**：處理跨聚合的複雜業務邏輯，如生產排程和品質管控

這個 MES 例子展現了製造業複雜的業務流程如何通過 DDD 的方式進行清晰的建模，每個組件都承載著特定的業務責任，形成了一個完整的製造執行系統核心模型。​​​​​​​​​​​​​​​​
