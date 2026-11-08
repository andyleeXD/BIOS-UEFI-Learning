# UEFI Boot Flow

## 主線
Reset Vector → SEC → PEI → DXE → BDS → Boot Loader → OS→TSL→RT→AL

## SEC
- SEC = Security
- 中文：安全階段
- 主要工作：準備最基本的環境與平台並將控制權交給PEI階段

## PEI
- PEI = Pre-EFI Initialization
- 中文：EFI前期初始化階段
- 主要工作：把主記憶體初始化並收集資料成數個HOB並將HOB清單交接給DXE階段

## DXE
- DXE = Driver Execution Environment
- 中文：驅動程式執行環境階段
- 主要工作：將大量的硬體設備做初始化

## BDS
- BDS = Boot Device Selection
- 中文：開機裝置選擇階段
- 主要工作：找到開機裝置並執行Boot Loader載入OS

## 四階段一句話版
- SEC：建立最基本環境
- PEI：初始化主記憶體並交接HOB清單
- DXE：初始化大量硬體設備
- BDS：選擇開機裝置並載入OS

