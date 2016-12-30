# 安裝命令列工具

雖然在 Microsoft Azure 的 Web 管理入口網站上就可以完成許多操作，不過很多人在開發、維運或管理 Microsoft Azure 虛擬機器等資源時喜歡使用命令列工具來操作，這篇文章介紹如何安裝 Azure PowerShell 以及跨平台的命令列工具。

## 命令列工具

由於 Microsoft Azure 有提供 [Service Management REST APIs](https://msdn.microsoft.com/en-us/library/azure/ee460799.aspx)，所以每個人都能自行開發命令列工具，這裡介紹的是以 Microsoft Azure 官方推出的命令列工具為主。

### Azure PowerShell

顧名思義，這是透過 PowerShell 加上管理 Azure 相關的 Cmdlets 的工具，雖然 PowerShell 已經[開源](https://github.com/PowerShell/PowerShell)，不過與 Azure 相關的模組並未完全相容跨平台，所以 Azure PowerShell 目前還是只能在 Windows 作業系統下使用。

安裝的方式非常簡單，你可以：

1. 使用系統管理者身份執行 Windows PowerShell 或 PowerShell ISE，然後執行 `Install-Module AzureRM` 指令，便能完成安裝。
2. 下載 [Azure PowerShell 安裝程式](http://aka.ms/webpi-azps)，透過 Web PI 安裝。

安裝完成後，以後可以直接執行 Azure PowerShell 以啟動載入 Azure 相關 Cmdlets 的 PowerShell 環境來操作。

### Azure CLI （跨平台命令列工具）

Azure CLI 是一套跨平台（Windows, Mac, Linux 等）的命令列工具，目前穩定的版本是[由 Node.js 所開發並開源](https://github.com/Azure/azure-xplat-cli)，而同時也正預覽[以 Python 寫成的下一代版本](https://github.com/Azure/azure-cli)，由於一些較新的功能必須要使用下一代的版本，所以這裡與後續文章會同時介紹這兩個版本的命令列工具。

安裝 Azure CLI 你可以：

1. 安裝以 Node.js 寫成，目前的穩定版本，直接透過 npm 安裝即可：`npm -g install azure-cli`。（當然你得先安裝 nodejs 及 npm。)
2. 安裝以 Python 寫成的下一代預覽版本，可以透過 pip 安裝：`pip install azure-cli`。（同樣的，你得先裝好 Python 以及 pip）

安裝完成後，檢查看看你能不能執行 `azure` \(穩定版\) 或是 `az` \(下一代\) 的命令。

## 設定命令列工具的訂用帳戶

在使用命令列工具之前，你要先設定這個命令列工具要操作的訂用帳號是哪一個。

### Azure PowerShell

1. 打開 PowerShell，執行 `Login-AzureRmAccount`，這時 PowerShell 會開啟一個認證對話視窗要你登入 Microsoft Azure，登入完成後就會將這個帳戶下關聯的訂用帳戶都連結起來。
2. 執行 Get-AzureRmSubscription，就會列出所有連結旳訂用帳號。
3. 如果您有多個 Microsoft 帳戶，可以再執行 `Add-AzureRmAccount` 切換連結其它訂用帳號。

### Azure CLI

1. 執行 `azure login`（或是`az login`），指令會產生一個 code 要你登入 [https://aka.ms/devicelogin](https://aka.ms/devicelogin) 後填入，完成身份驗證後，Azure CLI 便會自行下載連結的訂用帳號資料。
2. 執行 `azure account list` （或是 `az account list`）檢視連結了哪些訂用帳號，並且何者為預設操作的訂用帳號。
3. （選擇性）執行 `azure account set SUBSCRIPTION_ID` （或 az account set --subscription SUBSCRIPTION_ID`）來設定預設操作的訂用帳號。

## 參考資料

* [如何安裝和設定 Azure PowerShell](https://docs.microsoft.com/zh-tw/azure/powershell-install-configure)



