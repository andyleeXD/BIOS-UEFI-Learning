# CPU Reset / Reset Vector

## CPU Reset
### 我的理解
- 電腦剛開機CPU通電或執行Reset的動作

## Reset Vector
### 我的理解
- 保存CPU Reset後要執行的程式位址

### 一句話面試版
- 電腦剛開機CPU剛通電或執行Reset後，會至Reset Vector取出第一個要執行程式的位址，再至此位址執行韌體程式

## x86 → BIOS 的交界
CPU Reset  
↓  
Reset Vector  
↓  
Firmware Code  
↓  
UEFI Boot Flow

### 我自己的理解
- 電腦剛開機至Reset Vector取出第一個要執行程式的位址，再去此位址執行韌體程式，然後開始UEFI開機流程
