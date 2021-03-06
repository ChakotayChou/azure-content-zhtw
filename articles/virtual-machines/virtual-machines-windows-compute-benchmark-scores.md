<properties
 pageTitle="Windows VM 的計算基準測試分數 | Microsoft Azure"
 description="比較執行 Windows Server 之 Azure VM 的 SPECint 計算基準測試分數"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="07/18/2016"
 ms.author="danlep"/>

# Windows VM 的計算基準測試分數

下列 SPECInt 基準測試分數顯示執行 Windows Server 的 Azure 高效能 VM 產品陣容的計算效能。也有 [Linux VM](virtual-machines-linux-compute-benchmark-scores.md) 的計算基準測試分數。



## A 系列 - 計算密集型


大小 | vCPU | NUMA 節點 | CPU | 執行 | 平均基本費率 | 標準差
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_A8 | 8 | 1 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz | 10 | 236\.1 | 1\.1
Standard\_A9 | 16 | 2 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz | 10 | 450\.3 | 7\.0
Standard\_A10 | 8 | 1 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz | 5 | 235\.6 | 0\.9
Standard\_A11 | 16 | 2 | Intel Xeon CPU E5-2670 0 @ 2.6 GHz |7 | 454\.7 | 4\.8

## Dv2 系列


大小 | vCPU | NUMA 節點 | CPU | 執行 | 平均基本費率 | 標準差
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_D1\_v2 | 1 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 83 | 36\.6 | 2\.6
Standard\_D2\_v2 | 2 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 27 | 70\.0 | 3\.7
Standard\_D3\_v2 | 4 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 19 | 130\.5 | 4\.4
Standard\_D4\_v2 | 8 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 19 | 238\.1 | 5\.2
Standard\_D5\_v2 | 16 | 2 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 14 | 460\.9 | 15\.4
Standard\_D11\_v2 | 2 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 19 | 70\.1 | 3\.7
Standard\_D12\_v2 | 4 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 2 | 132\.0 | 1\.4
Standard\_D13\_v2 | 8 | 1 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 17 | 235\.8 | 3\.8
Standard\_D14\_v2 | 16 | 2 | Intel Xeon E5-2673 v3 @ 2.4 GHz | 15 | 460\.8 | 6\.5


## G 系列、GS 系列


大小 | vCPU | NUMA 節點 | CPU | 執行 | 平均基本費率 | 標準差
------- | ------ | ---- | -------| ---- | ---- | -----
Standard\_G1、Standard\_GS1 | 2 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 31 | 71\.8 | 6\.5
Standard\_G2、Standard\_GS2 | 4 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 5 | 133\.4 | 13\.0
Standard\_G3、Standard\_GS3 | 8 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 6 | 242\.3 | 6\.0
Standard\_G4、Standard\_GS4 | 16 | 1 | Intel Xeon E5-2698B v3 @ 2 GHz | 15 | 398\.9 | 6\.0
Standard\_G5、Standard\_GS5 | 32 | 2 | Intel Xeon E5-2698B v3 @ 2 GHz | 22 | 762\.8 | 3\.7

## 關於 SPECint

Windows 數字是藉由在 Windows Server 上執行 [SPECint 2006](https://www.spec.org/cpu2006/results/rint2006.html) 來計算出。執行 SPECint 時是使用基本費率選項 (SPECint\_rate2006)，其中每個核心有一個複本。SPECint 是由 12 個個別的測試組成，每個測試都執行三次，系統會從每個測試挑出中間值，然後將它們加權來形成複合分數。這些測試會接著在多個 VM 上執行以提供所顯示的平均分數。


## 後續步驟

* 如需儲存體容量、磁碟詳細資料及其他選擇 VM 大小的考量，請參閱[虛擬機器的大小](virtual-machines-windows-sizes.md)。

<!---HONumber=AcomDC_0727_2016-->