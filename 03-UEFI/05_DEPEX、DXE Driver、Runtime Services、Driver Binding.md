---
title: "UEFI 學習筆記｜DEPEX、DXE Driver、Runtime Services、Driver Binding"
date: 2026-11-09
tags:
  - BIOS
  - UEFI
  - DXE
  - Runtime-Services
  - Driver-Binding
---

# UEFI 學習筆記｜DEPEX、DXE Driver、Runtime Services、Driver Binding

> 今日範圍：DEPEX / DXE Dispatcher、DXE Driver / Protocol / Handle、ExitBootServices / Runtime Services / UEFI Variable、Driver Binding Protocol。

---

## 1. DEPEX 與 DXE Dispatcher

### DEPEX

**DEPEX = Dependency Expression = 相依性表達式**

DEPEX 用來描述一個 DXE Driver 在執行前需要滿足哪些先決條件。

重點不是單純「固定排好 Driver 的順序」，而是：

- Driver 有自己的執行條件。
- 條件尚未滿足時，Driver 先不執行。
- 條件滿足後，才可以開始執行。
- 這樣可以避免 Driver 太早執行，使用到尚未準備好的服務或資源。

### DXE Dispatcher

**DXE Dispatcher = DXE 階段的驅動調度者**

DXE Dispatcher 會檢查 Driver 的 DEPEX。

當某個 Driver 的條件滿足時，Dispatcher 就可以安排它載入與執行。

### 關係

```text
DXE Driver
   │
   └── DEPEX：描述「我需要哪些條件」
               │
               ▼
        DXE Dispatcher
               │
               └── 判斷哪些 Driver 現在可以執行
```

### 我的理解

> DEPEX 是 DXE Driver 的先決條件；DXE Dispatcher 讀取這些條件，條件滿足後才讓對應 Driver 執行，避免順序錯亂或使用到還沒準備好的東西。

---

## 2. DXE Driver、Protocol 與 Handle

### DXE Driver 載入後會做什麼？

DXE Driver 被載入執行後，常見工作包括：

1. 初始化硬體或服務。
2. 把自己提供的功能透過 **Protocol** 發布出去。
3. Protocol 會安裝在某個 **Handle** 上。
4. 其他 Driver 可以找到這個 Handle 上的 Protocol，並使用它提供的功能。

---

### Protocol

**Protocol = 標準功能介面**

Protocol 的重點是：

> 使用者只要知道「介面怎麼用」，不需要知道背後 Driver 是怎麼實作的。

例如某個網路 Driver 初始化網路卡後，可以把網路功能透過 Protocol 公開。

其他 Driver 如果需要網路功能，就可以找到這個 Protocol，再呼叫它定義好的函式。

---

### Handle

**Handle = 一個識別物件，上面可以安裝一個或多個 Protocol。**

可以把 Handle 想成：

> 一個掛著多種功能介面的物件。

### 關係

```text
DXE Driver
   │
   ├── 初始化硬體
   │
   └── 發布 Protocol
            │
            ▼
          Handle
            │
            ├── Protocol A
            ├── Protocol B
            └── Protocol C
```

其他 Driver：

```text
找到 Handle
   ↓
找到需要的 Protocol
   ↓
呼叫 Protocol 定義的函式
   ↓
使用功能
```

### 我的理解

> Driver 在初始化的過程中，會把自己的功能用 Protocol 這種標準介面發布，Protocol 會掛在 Handle 上。其他 Driver 要使用功能時，只要找到對應 Handle 上的 Protocol，就能呼叫它，不需要知道背後怎麼實作。

---

## 3. ExitBootServices 與 Runtime Services

### ExitBootServices

當 BDS 找到並啟動 Boot Loader 後，Boot Loader 在準備完成時會呼叫：

**ExitBootServices()**

這是一個重要的分界點。

呼叫之後：

- **Boot Services 結束**
- OS 正式接手系統
- **Runtime Services 仍然保留**

### 關係

```text
BDS
 ↓
Boot Loader
 ↓
ExitBootServices()
 ↓
Boot Services 結束
 ↓
OS 正式接手
 ↓
Runtime Services 繼續保留
```

---

## 4. Runtime Services

**Runtime Services = OS 接手後仍可使用的少數 UEFI 韌體服務。**

常見例子：

### Time Services

OS 可以透過 Runtime Services 存取韌體的時間相關服務。

### Variable Services

OS 可以透過 Runtime Services 讀寫 UEFI Variable。

因此：

> ExitBootServices 之後，不代表所有 BIOS / UEFI 功能都完全消失；少數 Runtime Services 仍然存在供 OS 使用。

---

## 5. UEFI Variable

UEFI Variable 可以保存某些韌體與 OS 需要使用的設定或狀態。

常見例子：

- BootOrder
- BootNext
- 部分平台或韌體設定

### 為什麼需要 UEFI Variable？

有些設定需要在重新開機之後仍然存在。

例如：

```text
BootOrder
Windows Boot Manager
USB
Network
```

重新開機之後，開機順序不能消失，因此可以使用具備 Non-Volatile 屬性的 UEFI Variable 保存。

---

## 6. GetVariable 與 SetVariable

UEFI Runtime Services 提供變數讀寫功能。

### GetVariable

**GetVariable = 讀取 UEFI Variable**

可以理解成：

> 讀設定。

### SetVariable

**SetVariable = 建立、修改或刪除 UEFI Variable**

可以理解成：

> 寫設定 / 改設定。

---

## 7. Variable Name + GUID

UEFI Variable 不是只靠名稱識別。

通常會使用：

```text
Variable Name + Vendor GUID
```

一起定位特定 Variable。

### GUID

**GUID = Globally Unique Identifier = 全域唯一識別碼**

這樣做的原因是：

> 不同模組或廠商可能使用相同的變數名稱，加入 GUID 後可以區分不同用途，避免取錯資料。

---

## 8. UEFI Variable Attributes

今天先學三個常見屬性：

### NV

**NV = Non-Volatile = 非揮發性**

表示：

> 重新開機後資料仍然保留。

---

### BS

**BS = Boot Service Access = Boot Services 階段可存取**

表示：

> ExitBootServices 之前可以存取此 Variable。

---

### RT

**RT = Runtime Access = Runtime 階段可存取**

表示：

> OS 接手後，仍可以透過 Runtime Services 存取此 Variable。

---

### 三個屬性的差異

```text
NV → 重開機後還在不在
BS → Boot Services 階段能不能用
RT → OS 接手後能不能用
```

---

## 9. Driver Binding Protocol

**Driver Binding Protocol = 驅動綁定協定**

它主要處理：

> 某個 UEFI Driver 是否能夠控制某個 Controller / 裝置，以及如何開始或停止控制。

最重要的三個函式：

---

### Supported()

判斷：

> 「這個裝置是不是我這個 Driver 支援、可以控制的？」

重點不是作業系統的「權限」，而是：

- Driver 認不認得這個裝置。
- Driver 是否支援這個裝置。
- Driver 是否適合控制這個 Controller。

口訣：

> 能不能管？

---

### Start()

如果 Supported() 判斷可以控制：

> Driver 開始控制並初始化這個 Controller / 裝置。

口訣：

> 開始管。

---

### Stop()

Driver 不再控制此裝置時：

> 停止管理並解除 Driver 與裝置之間的控制關係。

口訣：

> 停止管。

---

## 10. Driver Binding 的完整流程

```text
Driver
  │
  ▼
Supported()
  │
  ├── 不支援 → 不控制
  │
  └── 支援
       │
       ▼
     Start()
       │
       ▼
初始化 / 控制裝置
       │
       ▼
     Stop()
       │
       ▼
停止控制 / 解除關係
```

---

## 11. 今天完整知識鏈

```text
DEPEX
  ↓
描述 DXE Driver 的先決條件
  ↓
DXE Dispatcher 判斷哪些 Driver 可以執行
  ↓
DXE Driver 載入並初始化硬體
  ↓
Driver 將功能透過 Protocol 發布
  ↓
Protocol 安裝在 Handle 上
  ↓
其他 Driver 找到 Protocol 並呼叫功能
  ↓
BDS 找到 Boot Loader
  ↓
Boot Loader 呼叫 ExitBootServices
  ↓
Boot Services 結束
  ↓
OS 接手
  ↓
Runtime Services 保留
  ↓
OS 仍可使用 Time / Variable Services
  ↓
UEFI Variable 可透過 Name + GUID 定位
  ↓
NV / BS / RT 決定 Variable 的保存與存取階段
  ↓
Driver Binding Protocol
  ↓
Supported → Start → Stop
```

---

## 12. 今日縮寫整理

| 縮寫 | 完整英文 | 中文 / 功能 |
|---|---|---|
| DEPEX | Dependency Expression | 相依性表達式 |
| DXE | Driver Execution Environment | 驅動執行環境 |
| GUID | Globally Unique Identifier | 全域唯一識別碼 |
| NV | Non-Volatile | 非揮發性 |
| BS | Boot Service Access | Boot Services 階段可存取 |
| RT | Runtime Access | Runtime 階段可存取 |

---

