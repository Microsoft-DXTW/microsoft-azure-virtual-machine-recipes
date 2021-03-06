# 運用可用性集建立高穩定（HA）架構

_作者：Eric ShangKuan @ericsk_

虛擬機器不是放上雲端平台就可以高枕無憂，因為機房可能會有預期的維護、系統升級、或是不預期的意外等等造成虛擬機器服務中斷或產生問題。Microsoft Azure 提供免費的**可用性集（availability set）**的功能，讓您能透過它建立起高穩定（HA, high availability）的系統架構，降低系統或服務中斷的風險，而透過可集建立的服務架構，Microsoft Azure 承諾 99.95 的服務水準（參考資料 1\)。

> 根據維基百科 [High Availability](https://en.wikipedia.org/wiki/High_availability) 的頁面可以查詢服務水準的定義。99.95 相當於平均一年 4.38 小時（或平均一個月 21.56 分鐘）的停機時間，目前微軟公司以月為單位來衡量高穩定性服務水準，若 Microsoft Azure 每月停機超過這個時間就會補償用戶，詳情請見參考資料。

## 可用性集的容錯域（Fault Domain）及更新域（Update Domain）

首先，想要建立 HA 架構就是希望能盡可能地減低因為機房、網路、實體設備等發生意外或升級維護時所造成服務中斷的風險，但並不是你任意建立多個虛擬機器就能保證的，這就是為什麼要使用可用性集的緣故，它提供了兩個概念來規劃你建立的虛擬機器：

* **容錯域（Fault Domain）** 在可用性集中，容錯域的數量就代表這個可用性集會把虛擬機器**分佈在幾個不同的機櫃**之中，主要就是避免虛擬機器建立在相同的機櫃中，增加了因為硬體故障就同時發生問題的風險。

* **更新域（Update Domain）** 更新域讓你可以將虛擬機器分成不同的群組，每次新增虛擬機器進可用性集時就會放在另一個更新域。當 Azure 機房在進行系統升級或預定維護作業時，會一次處理一個群組，對使用者而言就是一次只會重啟同一群內的虛擬機器，但順序是不固定的。

舉例來說，假設你有 A, B, C, D, E, F, G 七台虛擬機器，而建了一個有 3 個容錯域、5 個更新域的可用性集把這些虛擬機器放進來，那機器的容錯域及更新域可能會是這樣：

| VM | 容錯域 | 更新域 |
| --- | --- | --- |
| A | 0 | 0 |
| B | 1 | 1 |
| C | 2 | 2 |
| D | 0 | 3 |
| E | 1 | 4 |
| F | 2 | 0 |
| G | 0 | 1 |

這表示 ABC 分別在不同的機櫃（同理：DEF 也是，但 ADG 是同機櫃）；而 Azure 在升級與維護時一次只會重啟一個更新域，所以當 AF 一起被重啟時，其它 VM 不受影響。

> 只有在建立虛擬機器時能設定放在可用性集中，已經建立的 VM 就無法再搬到可用性集中。

## 建立可用性集

### 從 Web 管理界面操作

1. 可用性集可以透過兩種方式建立，第一種就是直接新增資源選**可用性設定組**：

   ![建立可用性設定組](images/azure-creating-as.png)

   另一種方式就是在建立虛擬機器時建立可用性集：

   ![建立可用性設定組](images/azure-vm-creating-as.png)

   建立時除了設定可用性集名稱之外，還可以設定 _更新網域_ 及 _容錯網域_ 的數量，這裡你可以根據需求調整。

   > 可用性集不能跨資料中心，所以也要設定這個可用性集的所在位置。

2. 接下來就可以把虛擬機器放進可用性集中，可用性集會自動分配它的容錯域及更新域的位置。

   ![加入可用性設定組](images/azure-vm-adding-as.png)

3. 從可用性設定組可以看到虛擬機器被分配在哪個容錯域及更新域中，這樣就能安排機器互相備援的搭配方法。

   ![](images/azure-as-settings.png)

### 從命令列工具操作

建立可用性集：

* Azure XPlat CLI \(0.x\)

  ```
  azure availset create --resource-group AzureVMRecipes --location eastasia --name MyHA
  ```

* Azure CLI \(new\)

  ```
  az vm availability-set create --resource-group AzureVMRecipes --location eastasia --name MyHA
  ```

在建立虛擬機器時加入可用性集：

* Azure XPlat CLI \(0.x\)

  ```
  azure vm create ... --availset-name MyHA
  ```

* Azure CLI \(new\)

  ```
  az vm create ... --availability-set MyHA
  ```

> 建立虛擬機器的指令可以參考「[從命令列工具建立虛擬機器](/ch02/create-new-vm-from-cli.md)」一文。

## 參考資料

1. [Azure 虛擬機器的服務水準 \(SLA\) 保證](https://azure.microsoft.com/support/legal/sla/virtual-machines/)
2. [管理虛擬機器的可用性](https://docs.microsoft.com/zh-tw/azure/virtual-machines/virtual-machines-linux-manage-availability)



