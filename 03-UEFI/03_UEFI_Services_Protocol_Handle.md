# UEFI Services / Protocol / Handle

## Boot Services
### 我的理解
- 在開機階段中的啟動服務，由BIOS執行開機環境與硬體設備的初始化

## ExitBootServices
### 誰呼叫？
- 由OS loader呼叫

### 呼叫後發生什麼？
- 停止Boot Services服務，結束開機階段

## Runtime Services
### 為什麼 OS 接手後還需要？
- 因為OS的某些功能會需要用到，所以必須保留

## Protocol
### 我的理解
- 標準功能介面，不需要知道Driver如何實作，只要找到Protocol後就可以使用其功能

### 重點
- 是標準功能介面
- 使用者不需要知道 Driver 內部實作
- 找到 Protocol 後即可使用其功能

## Handle
### 我的理解
- 是一個物件，上面可以掛一個或多個Protocol

### 與 Protocol 的關係
- 
