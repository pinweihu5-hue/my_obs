[[nodejs_idx]]

---


設計目的是解決 NPM 和 Yarn 在處理大型項目時的一些性能和磁碟空間問題

[pnpm install | pnpm](https://pnpm.io/cli/install)


### PNPM 的特點

1. **節省磁碟空間**：
- PNPM 使用硬連結和符號連結來共享相同版本的套件，這樣可以避免重複安裝相同的依賴，從而節省大量磁碟空間。
2. **快速安裝**：
- 由於 PNPM 使用了更高效的依賴解析和安裝機制，它的安裝速度通常比 NPM 和 Yarn 更快。

3. **一致性**： ???
- PNPM 確保在不同的環境中安裝相同的依賴時，生成的 `node_modules` 結構是一致的，這有助於減少 "works on my machine" 類型的問題。

4. **嚴格的依賴管理**：???
- PNPM 使用嚴格的依賴解析策略，確保每個套件只能訪問其明確聲明的依賴，這有助於避免隱式依賴帶來的問題。

5. **多倉庫支持**：???
- PNPM 支持在單個倉庫中管理多個包，這對於 monorepo（單倉庫多包）項目非常有用。

### 安裝 PNPM

你可以通過 NPM 或 Yarn 安裝 PNPM：

```sh

brew install pnpm

# or

npm install -g pnpm

# 或者使用 Yarn

yarn global add pnpm

```

### 基本使用

以下是一些常見的 PNPM 命令：

1. **初始化項目**：

```sh

pnpm init

```

2. **安裝依賴**：

```sh

pnpm install

```

3. **添加依賴**：

```sh

pnpm add <package-name>

```

4. **添加開發依賴**：

```sh

pnpm add -D <package-name>

```

5. **移除依賴**：

```sh

pnpm remove <package-name>

```

6. **更新依賴**：

```sh

pnpm update

```

7. **運行腳本**：

```sh

pnpm run <script-name>

```

### Monorepo 支持

PNPM 對 monorepo 有很好的支持，可以使用 `pnpm-workspace.yaml` 文件來配置工作空間：

1. **創建 `pnpm-workspace.yaml` 文件**：

```yaml

packages:

- 'packages/*'

```

2. **在工作空間中安裝依賴**：

```sh

pnpm install

```

### 範例項目結構

以下是一個簡單的 monorepo 項目結構：

```

my-monorepo/
├── packages/
│ ├── package-a/
│ │ ├── package.json
│ │ └── index.js
│ └── package-b/
│ ├── package.json
│ └── index.js
├── pnpm-workspace.yaml
└── package.json

```



PNPM 使用了一種獨特的方式來管理和存儲套件，以節省磁碟空間和提高安裝速度。以下是 PNPM 如何存儲套件的詳細解釋：

### PNPM 的存儲機制

1. **全局存儲**：

- PNPM 將所有下載的套件存儲在一個全局的存儲目錄中，這個目錄通常位於用戶的主目錄下。

2. **硬連結和符號連結**：

- PNPM 使用硬連結和符號連結來將全局存儲中的套件連結到每個項目的 `node_modules` 目錄中。
- 這樣可以避免重複下載和存儲相同版本的套件，從而節省磁碟空間。

3. **虛擬 `node_modules`**：
- PNPM 創建了一個虛擬的 `node_modules` 結構，其中每個套件都只包含其自身的依賴，而不是所有的依賴。
- 這樣可以確保每個套件只能訪問其明確聲明的依賴，避免隱式依賴帶來的問題。

### 查看全局存儲位置

你可以使用以下命令來查看 PNPM 的全局存儲位置：

```sh

pnpm store path

```

這個命令會輸出全局存儲的路徑


### 自定義全局存儲位置

如果你想自定義 PNPM 的全局存儲位置，可以設置環境變量 `PNPM_HOME` 或在 `.npmrc` 文件中配置 `store-dir`：

1. **使用環境變量**：

```sh

export PNPM_HOME=/path/to/custom/store

```

2. **在 `.npmrc` 文件中配置**：

```ini

store-dir=/path/to/custom/store

```

### 範例

假設你有一個項目目錄結構如下：

```

my-project/
├── node_modules/
├── package.json
└── pnpm-lock.yaml

```

當你運行 `pnpm install` 時，PNPM 會將所有下載的套件存儲在全局存儲目錄中，並在 `my-project/node_modules` 中創建硬連結或符號連結指向全局存儲中的套件。
