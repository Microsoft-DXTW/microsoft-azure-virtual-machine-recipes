# 從 Azure 命令列工具建立虛擬機器

_作者: Eric ShangKuan @ericsk_

除了可以[從 Web 管理界面建立虛擬機器](create-new-vm-from-portal.md)之外，你也可以使用 Azure 命令列工具來建立虛擬機器。

關於 Azure 命令列工具的安裝可以參考[安裝命令列工具](/ch01/install-command-line-tools.md)範例。

## 瞭解需要建立虛擬機器的資訊

在命令列工具輸入下列指令：

* Azure XPlat CLI (0.x)

    `azure vm create --help`
  
* Azure CLI (new)

    `az vm create --help`

可以看到需要的資訊有： _資源群組名稱_、_虛擬機器名稱_、_部署資料中心位置_以及_作業系統映像檔_等。

## 取得資訊

### 資源群組

因為現在 Azure 上都是透過資源群組來管理建立的資源，所以在建立任何服務前都要先決定放在哪個資源群組中（不論新建或既有的），如果要新建資源群組，可以使用類似這個指令：

* Azure PowerShell

    `New-AzureRmResourceGroup -Name MyGroup -Location "East Asia"`

* Azure XPlat CLI (0.x)

    `azure group create MyGroup eastasia`

* Azure CLI (new)

    `az group create -n MyGroup -l eastasia`

這些指令就會在_東亞（eastasia）_機房中建立一個名為 _MyGroup_ 的資源群組

### 作業系統映像檔

要取得作業系統映像檔的資訊，要先鎖定採用哪個發行者（publisher）提供的作業系統映像檔，例如：Ubuntu 16.04 LTS 的發行者就是 _Canonical_。

要查詢欲部署的資料中心有哪些發行者，可以執行這個指令：

* Azure PowerShell

    `Get-AzureRmVMImagePublisher -Location "East Asia"`

* Azure XPlat CLI (0.x)

    `azure vm image list-publishers -l eastasia`

* Azure CLI (new)

    `az vm image list-publishers -l eastasia`

這就會回傳發行者的列表資訊，接下來你就可以鎖定特定的發行者列出它提供了多少作業系統映像檔，以及這些映像檔的名稱：

* Azure PowerShell

    ```powershell
    Get-AzureRmVMImageOffer -Location "East Asia" -PublisherName Canonical
    
    # (取得 Offer 名稱，例如 UbuntuServer)
    
    Get-AzureRmVMImageSku -Location "East Asia" -PublisherName Canonical -Offer UbuntuServer

    # (取得 Sku 名稱，例如 16.04-LTS)

    Get-AzureRMVMImage -Location "East Asia" -PublisherName Canonical -Offer UbuntuServer -Sku 16.04-LTS

    # (取得映像檔版本，例如 16.04.201612210)
    ```

* Azure XPlat CLI (0.x)

    `azure vm image list -p Canonical -l eastasia`

* Azure CLI (new)

    `az vm image list --publisher Canonical -l eastasia --all`

這檔一來你就會得到 _Canonical_ 在_東亞_機房提供了哪些作業系統映像檔。回傳資訊中的 **URN** 就是用來表示映像檔的名稱。

> Windows Server 的發行者是 _MicrosoftWindowsServer_。

## 建立虛擬機器

有了這些資訊後，就可以使用命令列工具來建立虛擬機器了。虛擬機器的等級我們這裡先設定成 _Standard_A1_ 的等級。

* Azure PowerShell

    (待補)

* Azure XPlat CLI (0.x)

    ```
    azure vm create --resource-group MyGroup --name MyVM --location eastasia --os-type Linux --image-urn Canonical:UbuntuServer:16.10-DAILY:16.10.201612140 --nic-name MyVMNIC --vnet-name MyVNet --vnet-address-prefix 10.0.0.0/16 --vnet-subnet-name default --vnet-subnet-address-prefix 10.0.0.0/24 --public-ip-name MyVMPublicIP
    ```

* Azure CLI (new)

    ```
    az vm create --name MyVM --resource-group MyGroup --image Canonical:UbuntuServer:16.10-DAILY:16.10.201612140 --location eastasia
    ```

如此一來便會在_東亞_機房建立一個 _Standard\_DS1_ 大小的虛擬機器，使用的是 _Ubuntu 16.10-DAILY_ 系列的映像檔；而擁有一個公用 IP 位址，以及虛擬網路的環境是 _10.0.0.0/16_ 及其子網路為 _10.0.0.0/24_。

> 你可以根據自己的需要修改指令中的參數。

> 這個例子會以交談式介面詢問您使用者帳號及密碼，而這些當然也可以直接以參數代入，這樣就不會卡在交談式介面中。

