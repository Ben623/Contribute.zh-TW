---
title: 如何在文件中使用連結
description: 本文提供在 docs.microsoft.com 內建立內容連結的相關指引。
ms.topic: contributor-guide
ms.prod: non-product-specific
ms.custom: external-contributor-guide
author: gewarren
ms.author: gewarren
ms.date: 10/31/2018
ms.openlocfilehash: 970f80b4e6ce795e0e2f15192d31680d7de6d35b
ms.sourcegitcommit: a812d716b31084926b886b93923f9b84c9b23429
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 12/18/2019
ms.locfileid: "75188335"
---
# <a name="use-links-in-documentation"></a>在文件中使用連結

本文描述如何在 docs.microsoft.com 上裝載的頁面中使用超連結。 使用一些不同的慣例，可以輕易地將連結新增至 Markdown。 連結可將使用者指向相同頁面、其他相鄰頁面，或外部網站與 URL 中的內容。

docs.microsoft.com 網站後端使用「開放式發行服務」(OPS)，可支援透過 [Markdig](https://github.com/lunet-io/markdig) 剖析引擎且符合 [CommonMark](https://commonmark.org/) 規範的 Markdown。 這個 Markdown 變體大多與 [GitHub 變體 Markdown (GFM)](https://help.github.com/categories/writing-on-github/) 相容，因為大多數文件都儲存在 GitHub 中並可在該處編輯。 額外的功能可透過 Markdown 延伸模組新增。

> [!IMPORTANT]
> 只要目標有支援 (絕大部分都會支援) 安全協定，所有連結都必須使用安全協定 (`https` 相對於 `http`)。

## <a name="link-text"></a>連結文字

您包含在連結文字中的字詞應該淺顯易懂。 換句話說，它們應該是簡單的英文單字，或您要連結之網頁的標題。

> [!IMPORTANT]
> 請勿使用「按一下這裡」。 這對搜尋引擎最佳化而言並不是一個好的選擇，且沒有適當地描述目標。

**正確：**

- `For more information, see the [contributor guide index](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).`

- `For more details, see the [SET TRANSACTION ISOLATION LEVEL](https://msdn.microsoft.com/library/ms173763.aspx) reference.`

**不正確：**

- `For more details, see [https://msdn.microsoft.com/library/ms173763.aspx](https://msdn.microsoft.com/library/ms173763.aspx).`

- `For more information, click [here](https://github.com/Azure/azure-content/blob/master/contributor-guide/contributor-guide-index.md).`

## <a name="links-from-one-article-to-another"></a>文章之間的連結

若要在相同的 *docset* 中，建立某個 Docs 技術文章到其他 Docs 技術文章的內嵌連結，請使用下列連結語法：

- 將文章連結到相同目錄中的另一篇文章：

  `[link text](article-name.md)`

- 將文章連結到當前目錄的父目錄中文章：

  `[link text](../article-name.md)`

- 將文章連結到當前目錄的子目錄中文章：

  `[link text](directory/article-name.md)`

- 將文章連結到當前目錄的父目錄中，其他子目錄中文章：

  `[link text](../directory/article-name.md)`

> [!NOTE]
> 上述範例均未在連結中使用 `~/`。 若要連結到從存放庫根開始的絕對路徑，請使用 `/` 來啟動連結。 瀏覽 GitHub 上的原始碼存放庫時，置入 `~/` 會產生無效的連結。 在路徑的開頭使用 `/` 即可正確解決。

若要連結到不同 docset 中的文章，即使檔案位於相同的存放庫中，也請使用下列語法：

`[link text](/docset-root/directory/article-name)`
   
例如，如果其根 URL 為 `https://docs.microsoft.com/dotnet` 的文章所連結文章的根為 `https://docs.microsoft.com/visualstudio`，則連結看起來會像 `[link text](/visualstudio/directory/article-name)`。

> [!TIP]
> 相同 *docset* 中的文章會在 "docs.microsoft.com" 之後具有相同 URL 片段。 例如，`https://docs.microsoft.com/dotnet/core/get-started` 和 `https://docs.microsoft.com/dotnet/framework/install` 位於相同 docset 中，而 `https://docs.microsoft.com/dotnet/core/get-started` 和 `https://docs.microsoft.com/visualstudio/whats-new` 則位於不同 docset 中。

## <a name="links-to-anchors"></a>連結到錨點

您不需要建立錨點。 它們在所有 H2 標題發佈時會自動產生。 您唯一需要做的就是建立 H2 小節的連結。

- 連結到相同文章中的標題：

  `[link](#the-text-of-the-H2-section-separated-by-hyphens)`
  `[Create cache](#create-cache)`

- 連結到另一個文章中的錨點：

  `[link text](../directory/article-name.md#anchor-name)`
  `[Configure your profile](../directory/media-services-create-account.md#configure-your-profile)`

## <a name="links-from-includes"></a>從包含檔案的連結

因為包含檔案位於另一個目錄中，所以您必須使用較長的相對路徑。 若要從包含檔案連結到文章，請使用此格式：

   ```markdown
   [link text](../articles/folder/article-name.md)
   ```

## <a name="links-in-selectors"></a>選取器中的連結

選取器是在文件文章中顯示為下拉式清單的導覽元件。 當讀者在下拉式清單中選取一個值時，瀏覽器會開啟選取的文章。 一般而言，選取器清單會包含密切相關的文章連結，例如多種程式設計語言的相同主題，或密切相關的系列文章。 

如果您擁有內嵌在包含檔案中的選取器，則可以使用下列連結結構：

   ```markdown
   > [AZURE.SELECTOR-LIST (Dropdown1 | Dropdown2 )]
   - [(Text1 | Example1 )](../articles/folder/article-name1.md)
   - [(Text1 | Example2 )](../articles/folder/article-name2.md)
   - [(Text2 | Example3 )](../articles/folder/article-name3.md)
   - [(Text2 | Example4 )](../articles/folder/article-name4.md) -->
   ```

## <a name="reference-style-links"></a>參考風格連結

您可以使用參考風格連結，讓來源內容更容易閱讀。 參考風格連結會將內嵌連結語法取代為簡化語法，允許您將較長的 URL 移動到文章的結尾。 以下是 [Daring Fireball](https://daringfireball.net/projects/markdown/) 的範例：

內嵌文字：

   ```markdown
   I get 10 times more traffic from [Google][1] than from [Yahoo][2] or [MSN][3].
   ```

文章結尾處的連結參考：

   ```markdown
   <!--Reference links in article-->
   [1]: http://google.com/
   [2]: http://search.yahoo.com/
   [3]: http://search.msn.com/
   ```
   
請確定您包含冒號之後的空格 (連結之前)。 當您連結到其他技術文章時，如果忘記包含空格，已發佈的文章中的連結將會中斷。

## <a name="links-to-pages-that-are-not-part-of-the-technical-documentation-set"></a>連結到不屬於技術文件集的頁面

若要連結到 Microsoft 網站上的另一個頁面 (例如定價頁面、SLA 頁面或任何不屬於文件文章的頁面)，請使用絕對 URL，但省略地區設定。 此目的為本連結可於 GitHub 和轉譯的網站上運作：

   ```markdown
   [link text](https://azure.microsoft.com/pricing/details/virtual-machines/)
   ```
   
## <a name="links-to-third-party-sites"></a>連結到協力廠商網站

最佳使用者體驗會減少將使用者傳送到其他網站的情況。 因此，以此資訊作為我們有時需要之任何協力廠商網站連結的基礎：

- **責任**：當所要共用的資訊是協力廠商資訊時，連結到協力廠商內容。 例如，告知大家如何使用 Android 開發人員工具，並不是 Microsoft 的立場，那是 Google 的資訊。 如果有需要，我們可以解釋如何*搭配* Azure 使用 Android 開發人員工具，但 Google 應該說明如何使用其工具。
- **PM 簽核**：要求 Microsoft 簽核協力廠商內容。 透過與之連結，表示我們對其信任以及我們在人們遵循指示時的義務。
- **時效性檢閱**：確定協力廠商資訊仍是最新、正確且相關，且連結並未變更。
- **異地**：讓使用者了解他們將前往另一個網站。 如果上下文不清楚，請加入一個限定詞組。 例如：「先決條件包括 Android 開發人員工具，您可以在 Android Studio 網站上下載」。
- **後續步驟**：舉例來說，您可以在 [後續步驟] 區段中新增一個 MVP 部落格連結。 同樣地，只要確認使用者了解他們將會離開網站。
- **法律**：在所有 ms.com 頁面的**使用規定**頁尾中，**連結至協力廠商網站**規定了相關法律事宜。

## <a name="links-to-azure-powershell-reference-content"></a>連結到 Azure PowerShell 參考內容

自從 2016 年 11 月起，Azure PowerShell 參考內容已經過數次變更。 使用以下指導方針，從 docs.microsoft.com 上其他文章連結到此內容。

URL 的結構：

* 針對 Cmdlet：
  - `/powershell/module/<module-name>/<cmdlet-name>[?view=<moniker-name>]`
* 針對概念性主題：
  - `/powershell/azure/<topic-file-name>[?view=<moniker-name>]`
  - `/powershell/azure/<service-name>/<topic-file-name>[?view=<moniker-name>]`

`<moniker-name>` 部分為選擇性。 如果省略，您將會被導向至最新版本的內容。 `<service-name>` 部分是下列基底 URL 中顯示的範例之一：

- Azure PowerShell (AzureRM) 內容：[https://docs.microsoft.com/powershell/azure/](https://docs.microsoft.com/powershell/azure/)
- Azure PowerShell (ASM) 內容：[https://docs.microsoft.com/powershell/azure/_servicemanagement_](https://docs.microsoft.com/powershell/azure/servicemanagement)
- Azure Active Directory (AzureAD) PowerShell 內容：[https://docs.microsoft.com/powershell/azure/_active-directory_](https://docs.microsoft.com/powershell/azure/active-directory)
- Azure Service Fabric PowerShell：[https://docs.microsoft.com/powershell/azure/_service-fabric_](https://docs.microsoft.com/powershell/azure/service-fabric)
- Azure 資訊保護 PowerShell: [https://docs.microsoft.com/powershell/azure/_aip_](https://docs.microsoft.com/powershell/azure/aip)
- Azure Elastic DB Jobs PowerShell: [https://docs.microsoft.com/powershell/azure/_elasticdbjobs_](https://docs.microsoft.com/powershell/azure/elasticdbjobs)

當您使用這些 URL 時，將會被重新導向至最新版本的內容。 這樣您就不必指定版本 Moniker。 這可避免在版本變更時，必須更新概念性內容連結的問題。

若要建立正確的連結，請在瀏覽器中找到要連結的頁面並複製該 URL，然後移除地區設定代碼；例如，**en-us**。

範例 Markdown：

```markdown
[Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresourcegroup)
[Get-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/get-azurermresourcegroup?view=azurermps-4.1.0)
[New-AzureVM](https://docs.microsoft.com/powershell/module/azure/new-azurevm?view=azuresmps-4.0.0)
[New-AzureRmVM](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvm)
[Install Azure PowerShell for Service Management](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azurerm-ps)
[Install Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)
```
