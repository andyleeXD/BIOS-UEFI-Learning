# PEI 深入：PEIM / PPI / HOB

## PEIM
- PEIM = PEI Module
- 中文：PEI模組
- 主要工作：負責在PEI階段執行要做的事情

## PPI
- PPI = PEIM-to-PEIM Interface
- 中文： PEIM-to-PEIM 介面
- 我的理解：PEIM間溝通、呼叫的標準功能介面
- 與 Protocol 的相似點：都是標準的功能介面

## HOB
- HOB = Hand-Off Block
- 中文：交接資料區塊
- 我的理解：在PEI階段會收集各種資料類型的資料，就稱為HOB
- 為什麼需要 HOB：最後會整理成HOB清單供後續DXE階段取得需要的資料，並按照清單執行且不要占用到別人正在使用的資源

## 口訣
> PEIM 做事、PPI 溝通、HOB 交接。

## HOB List
### 為什麼不是只有一個 HOB？
- 因為有多種資料類型，所以會有多個HOB

## HOB 類型
### Resource Descriptor HOB
- 我的理解：資源地圖，可以知道哪些資源正在被使用，哪些可以使用

### Memory Allocation HOB
- 我的理解：可以知道哪些記憶體位址正在被使用，哪些可以使用

### GUID HOB
- GUID = Globally Unique Identifier = 全域唯一識別碼
- 我的理解：自定義的資料區塊，用GUID當作識別碼來找尋資料

## PEI → DXE 交接
### 面試版
- 將記憶體初始化並整理HOB清單交接給DXE，DXE按照清單執行且不要占用別人正在使用的資源

### 我的口語版
> PEI 列清單，DXE 照單做事。

- 
