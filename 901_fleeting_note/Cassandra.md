
 
開源的
分散式
寬列儲存 (wide-column store) 的 NoSQL 資料庫

它最初由 Facebook 開發，後來成為 Apache 軟體基金會的頂級專案。

主要特性：
* **去中心化架構：** Cassandra 沒有單點故障，所有節點都平等，任何節點都可以處理讀寫請求。這使得 Cassandra 具有高度的容錯性和可用性。
* **可擴展性：** Cassandra 可以輕鬆地水平擴展，只需添加更多節點即可提高容量和性能。
* **高可用性：** Cassandra 的去中心化架構和數據複製機制確保了即使部分節點故障，系統仍然可以繼續運行。
* **容錯性：** Cassandra 可以容忍節點故障，並自動進行數據修復。
* **可調一致性：** Cassandra 提供了**可調一致性，允許你在一致性和可用性之間進行權衡**。
* **高寫入吞吐量：** Cassandra 針對寫入操作進行了優化，可以**處理大量的寫入**請求。
* **多數據中心複製：** Cassandra 支援多數據中心複製，可以將數據複製到全球不同的數據中心，提高數據的可用性和容錯性。
* **CQL (Cassandra Query Language)：** Cassandra 使用 CQL 作為查詢語言，CQL 類似於 SQL，易於學習和使用。

架構：
採用點對點 (peer-to-peer) 架構，所有節點都平等，沒有主從之分。
數據被分佈到叢集中的所有節點上，並通過一致性雜湊 (consistent hashing) 算法進行分配。

**關鍵概念：**
* **節點 (Node):** Cassandra 叢集中的單個伺服器。
* **數據中心 (Data Center):**  地理位置上靠近的一組節點。
* **叢集 (Cluster):**  由多個數據中心組成的 Cassandra 系統。
* **鍵空間 (Keyspace):**  類似於關係型資料庫中的資料庫。
* **表 (Table):**  儲存數據的結構，由行和列組成。
* **主鍵 (Primary Key):**  用於唯一標識表中的每一行數據。
* **列族 (Column Family):**  一組相關的列。


**Cassandra 的優點：**
* **高可用性和容錯性：**  非常適合需要高可用性的應用程式。
* **可擴展性：**  可以輕鬆地處理不斷增長的數據量。
* **高寫入吞吐量：**  適合寫入密集型應用程式。
* **靈活的數據模型：**  可以處理各種數據類型。


**Cassandra 的缺點：**
* **不支援 JOIN 和事務：**  不適合需要複雜查詢和事務的應用程式。
* **數據建模比較複雜：**  需要仔細設計數據模型以獲得最佳性能。
* **除錯和監控比較困難：**  相對於關係型資料庫，除錯和監控 Cassandra 更加複雜。


**Cassandra 的應用場景：**
* **寫入密集型應用程式：** 例如日誌記錄、感測器數據收集等。
* **需要高可用性的應用程式：** 例如線上遊戲、電商平台等。
* **實時數據處理：** 例如線上廣告、欺詐檢測等。

不支援 JOIN 和事務，數據建模也比較複雜，需要根據具體的應用場景 carefully choose 是否使用 Cassandra。



# 常見的安裝 Cassandra 的方式是什麼？

安裝 Cassandra 的方式有很多種，以下列出幾種常見的方式，並說明使用 Kubernetes 和 Helm 的方法：

**1. 使用包管理器 (Package Manager):**

這是最常見也是最簡單的安裝方式，適用於單機或小型叢集。  不同的作業系統有不同的包管理器，例如：

* **Debian/Ubuntu (apt):**
  ```bash
  sudo apt update
  sudo apt install cassandra
  ```

* **Red Hat/CentOS (yum):**
  ```bash
  sudo yum update
  sudo yum install cassandra
  ```

**2. 使用 Docker:**

Docker 提供了 Cassandra 的官方映像，可以方便地在容器中運行 Cassandra。

```bash
docker run --name cassandra -d cassandra:latest
```

**3. 使用 Kubernetes:**

在 Kubernetes 中部署 Cassandra 更為複雜，但可以提供更好的可擴展性、容錯性和管理性。  有幾種方法可以在 Kubernetes 上部署 Cassandra：

* **使用 StatefulSet:**  這是推薦的方法，StatefulSet 可以確保 Cassandra 節點的持久化儲存和有序部署。 你需要自行創建 StatefulSet 的 YAML 文件，並使用 `kubectl apply` 命令部署。  這需要對 Kubernetes 有一定的了解。

* **使用 Helm Chart:**  Helm 是 Kubernetes 的套件管理器，可以簡化應用程式的部署。  DataStax 提供了官方的 Cassandra Helm Chart，可以讓你更方便地在 Kubernetes 上部署 Cassandra。

    ```bash
    helm repo add datastax https://charts.datastax.com/stable
    helm repo update
    helm install my-cassandra datastax/cassandra
    ```

    使用 Helm Chart 的好處是它提供了預先配置好的 Cassandra 設定，並且可以輕鬆地自定義配置。

* **使用 Cassandra 運營商 (Operator):**  Operator 是一種 Kubernetes 的擴展，可以自動化應用程式的部署和管理。  Instaclustr 和 DataStax 都提供了 Cassandra Operator，可以進一步簡化 Cassandra 在 Kubernetes 上的部署和管理。


**4. 使用雲端服務商提供的 Cassandra 服務：**

例如，DataStax Astra 或 AWS Keyspaces 等雲端服務商提供了全託管的 Cassandra 服務，可以讓你無需管理基礎架構即可使用 Cassandra。


**比較：**

| 方法 | 優點 | 缺點 |
|---|---|---|
| 包管理器 | 簡單易用 | 不適合大型叢集 |
| Docker | 方便快捷 |  管理性較差 |
| Kubernetes (StatefulSet) | 可擴展性、容錯性、管理性 | 複雜度較高 |
| Kubernetes (Helm) | 簡化部署、預先配置 | 需要學習 Helm |
| Kubernetes (Operator) | 自動化管理 | 需要選擇合適的 Operator |
| 雲端服務 | 全託管、無需管理基礎架構 | 成本較高 |


**總結：**

對於單機或小型叢集，使用包管理器或 Docker 就足夠了。  對於大型叢集或需要高可用性和容錯性的應用程式，建議使用 Kubernetes 部署 Cassandra。  使用 Helm Chart 或 Operator 可以簡化 Kubernetes 上的部署和管理。  如果不想管理基礎架構，則可以考慮使用雲端服務商提供的 Cassandra 服務。




# aws or gcp 有 managed Cassandra 服務嗎？或是有服務類似於 Cassandra 的嗎？

AWS 和 GCP 都有類似 Cassandra 的 managed 服務，雖然它們不直接提供完全託管的開源 Cassandra，但它們提供了相容 Cassandra API 的服務，或者基於 Cassandra 架構構建的服務，提供類似的功能和優勢。

**AWS:**

* **Amazon Keyspaces (for Apache Cassandra):** 這是 AWS 提供的與 Apache Cassandra 相容的無伺服器資料庫服務。它支援相同的 CQL API，因此你可以使用現有的 Cassandra 應用程式和工具與 Keyspaces 互動，而無需修改程式碼。  Keyspaces 自動處理容量配置、安全性和備份等任務，讓你更專注於應用程式開發。

* **Amazon DynamoDB:** 雖然 DynamoDB 不是直接基於 Cassandra，但它是一個鍵值和文件資料庫，提供了一些與 Cassandra 類似的功能，例如高可用性、可擴展性和低延遲。  如果你正在尋找一個更廣泛的 NoSQL 選項，並且不需要完全的 Cassandra 相容性，DynamoDB 也是一個不錯的選擇。


**GCP:**

* **DataStax Astra DB (在 GCP Marketplace 上提供):**  DataStax Astra DB 是一個基於 Cassandra 的全託管資料庫即服務 (DBaaS)，在 GCP Marketplace 上提供。  它提供了開源 Cassandra 的所有功能，並增加了額外的企業級功能，例如安全、分析和開發工具。  這是一個很好的選擇，如果你需要完全的 Cassandra 相容性和 DataStax 的支援。

* **Google Cloud Spanner:**  Spanner 是一個全託管、可擴展、強一致性的資料庫服務。雖然它的架構與 Cassandra 不同，但它也提供了高可用性、可擴展性和低延遲等特性，可以作為 Cassandra 的替代方案，尤其是在需要強一致性的情況下。

* **Google Cloud Bigtable:**  Bigtable 是一個 NoSQL、寬列儲存資料庫，類似於 Cassandra 和 HBase。它適用於儲存和查詢大量的結構化、半結構化和非結構化數據。  如果你需要一個與 Cassandra 類似的數據模型，並且可以接受最終一致性，Bigtable 也是一個不錯的選擇。


**總結：**

AWS 和 GCP 都提供了與 Cassandra 類似的 managed 服務，可以滿足不同的需求。  如果你需要完全的 Cassandra 相容性，AWS Keyspaces 和 DataStax Astra DB 是最佳選擇。 如果你正在尋找一個更廣泛的 NoSQL 選項，或者需要其他特定的功能，例如強一致性，則可以考慮 DynamoDB、Spanner 或 Bigtable。  選擇哪種服務取決於你的具體應用場景和需求。




# node.js 是否有套件可以和 Cassandra 交互？

是的，Node.js 有幾個套件可以與 Cassandra 數據庫交互。其中最流行和維護得最好的是 `cassandra-driver`。

**`cassandra-driver` 套件：**

這個套件由 DataStax 開發和維護，DataStax 是 Cassandra 的主要商業支持者。它提供了豐富的功能和 API，可以讓你執行各種 Cassandra 操作，例如：

* **連接到 Cassandra 叢集：**  建立與 Cassandra 叢集的連線，並管理連線池。
* **執行 CQL 查詢：**  執行各種 CQL 查詢，例如 `SELECT`、`INSERT`、`UPDATE` 和 `DELETE`。
* **批次操作：**  將多個查詢打包成批次執行，提高效率。
* **準備語句：**  使用準備語句可以提高查詢性能，並防止 SQL 注入攻擊。
* **數據流：**  支援數據流，可以高效地處理大量的數據。
* **用戶自定義類型：**  支援用戶自定義類型，可以更方便地處理複雜的數據結構。
* **錯誤處理：**  提供完善的錯誤處理機制，可以更好地處理各種異常情況。
* **負載平衡和故障轉移：**  支援負載平衡和故障轉移，確保應用程式的可用性。


**如何使用 `cassandra-driver`：**

1. **安裝套件：**

```bash
npm install cassandra-driver
```

2. **連接到 Cassandra 叢集：**

```javascript
const cassandra = require('cassandra-driver');

const client = new cassandra.Client({
  contactPoints: ['cassandra-node1', 'cassandra-node2'], // Cassandra 節點的地址
  localDataCenter: 'datacenter1', // 本地數據中心的名称
  keyspace: 'my_keyspace' // 鍵空間的名稱
});

client.connect()
  .then(() => console.log('Connected to Cassandra'))
  .catch(err => console.error('Error connecting to Cassandra:', err));
```

3. **執行 CQL 查詢：**

```javascript
const query = 'SELECT * FROM users WHERE id = ?';
const params = [123];

client.execute(query, params, { prepare: true })
  .then(result => console.log('Query result:', result.rows))
  .catch(err => console.error('Error executing query:', err));
```


**其他 Node.js Cassandra 套件：**

除了 `cassandra-driver` 之外，還有一些其他的 Node.js Cassandra 套件，例如：

* **`node-cassandra-cql`:**  一個輕量級的套件，提供基本的 Cassandra 操作。
* **`express-cassandra`:**  專為 Express.js 框架設計的套件，可以更方便地在 Express.js 應用程式中使用 Cassandra。


然而，`cassandra-driver` 是目前最受歡迎和推薦的套件，它提供了更豐富的功能、更好的性能和更完善的文檔。  建議優先考慮使用 `cassandra-driver`。



# 使用經驗
- [How Discord Stores Billions of Messages](https://discord.com/blog/how-discord-stores-billions-of-messages)
- [How Discord Stores Trillions of Messages](https://discord.com/blog/how-discord-stores-trillions-of-messages?utm_source=hackernewsletter&utm_medium=email&utm_term=data)