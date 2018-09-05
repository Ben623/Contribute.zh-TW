---
title: 快速入門：從 Azure Key Vault 設定及擷取祕密 | Microsoft Docs
description: 快速入門：從 Azure Key Vault 設定及擷取祕密
services: key-vault
author: syntaxc4
manager: erifkin
ms.date: 07/24/2018
ms.author: cfowler
zone_pivot_groups: keyvault-languages, keyvault-platforms
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: 8b758274203748bb6e04c03dec5de38fb77947b4
ms.sourcegitcommit: b0105f322f91bb4dbde47f6da35b3c12271d5b03
ms.translationtype: HT
ms.contentlocale: zh-TW
ms.lasthandoff: 08/29/2018
ms.locfileid: "43239540"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault"></a><span data-ttu-id="91a50-103">快速入門：從 Azure Key Vault 設定及擷取祕密</span><span class="sxs-lookup"><span data-stu-id="91a50-103">Quickstart: Set and retrieve a secret from Azure Key Vault</span></span>

<span data-ttu-id="91a50-104">此快速入門說明如何將祕密儲存在 Key Vault 中，以及如何使用 Web 應用程式擷取它。</span><span class="sxs-lookup"><span data-stu-id="91a50-104">This quickstart shows you how to store a secret in Key Vault and how to retrieve it using a Web app.</span></span> <span data-ttu-id="91a50-105">若要查看祕密值，您必須在 Azure 上執行此作業。</span><span class="sxs-lookup"><span data-stu-id="91a50-105">To see the secret value you would have to run this on Azure.</span></span> <span data-ttu-id="91a50-106">此快速入門會使用 Node.js 和受控服務識別 (MSI)</span><span class="sxs-lookup"><span data-stu-id="91a50-106">The quickstart uses Node.js and Managed service identities (MSIs)</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="91a50-107">建立 Key Vault。</span><span class="sxs-lookup"><span data-stu-id="91a50-107">Create a Key Vault.</span></span>
> * <span data-ttu-id="91a50-108">將祕密儲存在 Key Vault 中。</span><span class="sxs-lookup"><span data-stu-id="91a50-108">Store a secret in Key Vault.</span></span>
> * <span data-ttu-id="91a50-109">從 Key Vault 擷取祕密。</span><span class="sxs-lookup"><span data-stu-id="91a50-109">Retrieve a secret from Key Vault.</span></span>
> * <span data-ttu-id="91a50-110">建立 Azure Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="91a50-110">Create an Azure Web Application.</span></span>
> * <span data-ttu-id="91a50-111">[啟用受控服務身分識別](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)。</span><span class="sxs-lookup"><span data-stu-id="91a50-111">[Enable managed service identities](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview).</span></span>
> * <span data-ttu-id="91a50-112">授與 Web 應用程式從 Key Vault 讀取資料所需的權限。</span><span class="sxs-lookup"><span data-stu-id="91a50-112">Grant the required permissions for the web application to read data from Key vault.</span></span>

<span data-ttu-id="91a50-113">繼續之前，請先確定您已熟悉[基本概念](https://docs.microsoft.com/azure/key-vault/key-vault-whatis#basic-concepts)。</span><span class="sxs-lookup"><span data-stu-id="91a50-113">Before you proceed make sure that you are familiar with the [basic concepts](https://docs.microsoft.com/azure/key-vault/key-vault-whatis#basic-concepts).</span></span>

>[!NOTE]
<span data-ttu-id="91a50-114">為了解以下教學課程為何是最佳做法，我們需要了解一些概念。</span><span class="sxs-lookup"><span data-stu-id="91a50-114">To understand why the below tutorial is the best practice we need to understand a few concepts.</span></span> <span data-ttu-id="91a50-115">Key Vault 是一個中央存放庫，可透過程式設計方式儲存祕密。</span><span class="sxs-lookup"><span data-stu-id="91a50-115">Key Vault is a central repository to store secrets programmatically.</span></span> <span data-ttu-id="91a50-116">但若要這樣做，應用程式/使用者必須要先向 Key Vault 進行驗證，也就是出示祕密。</span><span class="sxs-lookup"><span data-stu-id="91a50-116">But to do so applications / users need to first authenticate to Key Vault i.e. present a secret.</span></span> <span data-ttu-id="91a50-117">為了遵循安全性最佳做法，第一個祕密也必須定期輪替。</span><span class="sxs-lookup"><span data-stu-id="91a50-117">To follow security best practices this first secret needs to be rotated periodically as well.</span></span> <span data-ttu-id="91a50-118">但透過[受控服務識別](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview)，在 Azure 中執行的應用程式將會獲得一個由 Azure 自動管理的身分識別。</span><span class="sxs-lookup"><span data-stu-id="91a50-118">But with [Managed Service Identity](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) applications that run in Azure are given an identity which is automatically managed by Azure.</span></span> <span data-ttu-id="91a50-119">這有助於解決**祕密導入問題**，如此，使用者/應用程式即可遵循最佳做法，且不需要擔心輪替第一個祕密的問題</span><span class="sxs-lookup"><span data-stu-id="91a50-119">This helps solve the **Secret Introduction Problem** where users / applications can follow best practices and not have to worry about rotating the first secret</span></span>

## <a name="prerequisites"></a><span data-ttu-id="91a50-120">必要條件</span><span class="sxs-lookup"><span data-stu-id="91a50-120">Prerequisites</span></span>

<span data-ttu-id="91a50-121">::: zone pivot="nodejs"</span><span class="sxs-lookup"><span data-stu-id="91a50-121">::: zone pivot="nodejs"</span></span>
* <span data-ttu-id="91a50-122">[Node JS](https://nodejs.org/en/) ::: zone-end</span><span class="sxs-lookup"><span data-stu-id="91a50-122">[Node JS](https://nodejs.org/en/) ::: zone-end</span></span>

<span data-ttu-id="91a50-123">::: zone pivot="dotnet, windows"</span><span class="sxs-lookup"><span data-stu-id="91a50-123">::: zone pivot="dotnet, windows"</span></span>
* <span data-ttu-id="91a50-124">具有下列工作負載的[Visual Studio 2017 15.7.3 版或更新版本](https://www.microsoft.com/net/download/windows)：</span><span class="sxs-lookup"><span data-stu-id="91a50-124">[Visual Studio 2017 version 15.7.3 or later](https://www.microsoft.com/net/download/windows) with the following workloads:</span></span>
  * <span data-ttu-id="91a50-125">ASP.NET 與 Web 開發</span><span class="sxs-lookup"><span data-stu-id="91a50-125">ASP.NET and web development</span></span>
  * <span data-ttu-id="91a50-126">.NET Core 跨平台開發</span><span class="sxs-lookup"><span data-stu-id="91a50-126">.NET Core cross-platform development</span></span>
* <span data-ttu-id="91a50-127">[.NET Core 2.1 SDK 或更新版本](https://www.microsoft.com/net/download/windows) :::zone-end</span><span class="sxs-lookup"><span data-stu-id="91a50-127">[.NET Core 2.1 SDK or later](https://www.microsoft.com/net/download/windows) :::zone-end</span></span>

<span data-ttu-id="91a50-128">::: zone pivot="dotnet, mac"</span><span class="sxs-lookup"><span data-stu-id="91a50-128">::: zone pivot="dotnet, mac"</span></span>
* <span data-ttu-id="91a50-129">請參閱 [Visual Studio for Mac 的新功能](https://visualstudio.microsoft.com/vs/mac/)。</span><span class="sxs-lookup"><span data-stu-id="91a50-129">See [What’s New in Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>
<span data-ttu-id="91a50-130">:::zone-end</span><span class="sxs-lookup"><span data-stu-id="91a50-130">:::zone-end</span></span>

* <span data-ttu-id="91a50-131">Git ([下載](https://git-scm.com/downloads))。</span><span class="sxs-lookup"><span data-stu-id="91a50-131">Git ([download](https://git-scm.com/downloads)).</span></span>
* <span data-ttu-id="91a50-132">Azure 訂用帳戶。</span><span class="sxs-lookup"><span data-stu-id="91a50-132">An Azure subscription.</span></span> <span data-ttu-id="91a50-133">如果您沒有 Azure 訂用帳戶，請在開始前建立 [免費帳戶](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。</span><span class="sxs-lookup"><span data-stu-id="91a50-133">If you don't have an Azure subscription, create a [free account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) before you begin.</span></span>
* <span data-ttu-id="91a50-134">[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) 2.0.4 版或更新版本。</span><span class="sxs-lookup"><span data-stu-id="91a50-134">[Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) version 2.0.4 or later.</span></span> <span data-ttu-id="91a50-135">此工具適用於 Windows、Mac 與 Linux。</span><span class="sxs-lookup"><span data-stu-id="91a50-135">This is available for Windows, Mac, and Linux.</span></span>

## <a name="login-to-azure"></a><span data-ttu-id="91a50-136">登入 Azure</span><span class="sxs-lookup"><span data-stu-id="91a50-136">Login to Azure</span></span>

<span data-ttu-id="91a50-137">若要使用 CLI 登入 Azure，您可以輸入：</span><span class="sxs-lookup"><span data-stu-id="91a50-137">To log in to Azure using the CLI, you can type:</span></span>

```azurecli
az login
```

## <a name="create-resource-group"></a><span data-ttu-id="91a50-138">建立資源群組</span><span class="sxs-lookup"><span data-stu-id="91a50-138">Create resource group</span></span>

<span data-ttu-id="91a50-139">使用 [az group create](/cli/azure/group#az-group-create) 命令建立資源群組。</span><span class="sxs-lookup"><span data-stu-id="91a50-139">Create a resource group with the [az group create](/cli/azure/group#az-group-create) command.</span></span> <span data-ttu-id="91a50-140">Azure 資源群組是可供在其中部署及管理 Azure 資源的邏輯容器。</span><span class="sxs-lookup"><span data-stu-id="91a50-140">An Azure resource group is a logical container into which Azure resources are deployed and managed.</span></span>

<span data-ttu-id="91a50-141">請選取資源群組名稱，並填入預留位置。</span><span class="sxs-lookup"><span data-stu-id="91a50-141">Please select a Resource Group name and fill in the placeholder.</span></span>
<span data-ttu-id="91a50-142">下列範例會在 *eastus* 位置建立名為 <YourResourceGroupName> 的資源群組。</span><span class="sxs-lookup"><span data-stu-id="91a50-142">The following example creates a resource group named *<YourResourceGroupName>* in the *eastus* location.</span></span>

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "East US"
```

<span data-ttu-id="91a50-143">此教學課程中會使用您剛建立的資源群組。</span><span class="sxs-lookup"><span data-stu-id="91a50-143">The resource group you just created is used throughout this tutorial.</span></span>

## <a name="create-an-azure-key-vault"></a><span data-ttu-id="91a50-144">建立 Azure Key Vault</span><span class="sxs-lookup"><span data-stu-id="91a50-144">Create an Azure Key Vault</span></span>

<span data-ttu-id="91a50-145">接下來，您會使用在上一個步驟中建立的資源群組建立 Key Vault。</span><span class="sxs-lookup"><span data-stu-id="91a50-145">Next you create a Key Vault using the resource group created in the previous step.</span></span> <span data-ttu-id="91a50-146">雖然 “ContosoKeyVault” 是作為本文中的 Key Vault 名稱使用，但您必須使用唯一名稱。</span><span class="sxs-lookup"><span data-stu-id="91a50-146">Although “ContosoKeyVault” is used as the name for the Key Vault throughout this article, you have to use a unique name.</span></span> <span data-ttu-id="91a50-147">請提供下列資訊：</span><span class="sxs-lookup"><span data-stu-id="91a50-147">Provide the following information:</span></span>

* <span data-ttu-id="91a50-148">Vault 名稱 - **在這裡選取 Key Vault 名稱**。</span><span class="sxs-lookup"><span data-stu-id="91a50-148">Vault name - **Select a Key Vault Name here**.</span></span>
* <span data-ttu-id="91a50-149">資源群組名稱 - **在這裡選取資源群組名稱**。</span><span class="sxs-lookup"><span data-stu-id="91a50-149">Resource group name - **Select a Resource Group Name here**.</span></span>
* <span data-ttu-id="91a50-150">位置 - **美國東部**。</span><span class="sxs-lookup"><span data-stu-id="91a50-150">The location - **East US**.</span></span>

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "East US"
```

<span data-ttu-id="91a50-151">此時，您的 Azure 帳戶是唯一獲得授權在此新保存庫上執行任何作業的帳戶。</span><span class="sxs-lookup"><span data-stu-id="91a50-151">At this point, your Azure account is the only one authorized to perform any operations on this new vault.</span></span>

## <a name="add-a-secret-to-key-vault"></a><span data-ttu-id="91a50-152">將祕密新增至 Key Vault</span><span class="sxs-lookup"><span data-stu-id="91a50-152">Add a secret to key vault</span></span>

<span data-ttu-id="91a50-153">我們將新增祕密，以協助說明其運作方式。</span><span class="sxs-lookup"><span data-stu-id="91a50-153">We're adding a secret to help illustrate how this works.</span></span> <span data-ttu-id="91a50-154">您可能正在儲存 SQL 連接字串，或任何您必須安全保存但可供您的應用程式使用的其他資訊。</span><span class="sxs-lookup"><span data-stu-id="91a50-154">You could be storing a SQL connection string or any other information that you need to keep securely but make available to your application.</span></span> <span data-ttu-id="91a50-155">在本教學課程中，密碼會稱為 **AppSecret**，並將在其中儲存 **MySecret** 的值。</span><span class="sxs-lookup"><span data-stu-id="91a50-155">In this tutorial, the password will be called **AppSecret** and will store the value of **MySecret** in it.</span></span>

<span data-ttu-id="91a50-156">輸入下列命令，以在 Key Vault 中建立稱為 **AppSecret** 並將儲存 **MySecret** 值的祕密：</span><span class="sxs-lookup"><span data-stu-id="91a50-156">Type the commands below to create a secret in Key Vault called **AppSecret** that will store the value **MySecret**:</span></span>

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

<span data-ttu-id="91a50-157">以純文字檢視包含在祕密中的值：</span><span class="sxs-lookup"><span data-stu-id="91a50-157">To view the value contained in the secret as plain text:</span></span>

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

<span data-ttu-id="91a50-158">此命令會顯示祕密資訊，包括 URI。</span><span class="sxs-lookup"><span data-stu-id="91a50-158">This command shows the secret information including the URI.</span></span> <span data-ttu-id="91a50-159">完成這些步驟之後，您的 Azure Key Vault 中應該會有祕密的 URI。</span><span class="sxs-lookup"><span data-stu-id="91a50-159">After completing these steps, you should have a URI to a secret in an Azure Key Vault.</span></span> <span data-ttu-id="91a50-160">寫下此資訊。</span><span class="sxs-lookup"><span data-stu-id="91a50-160">Write this information down.</span></span> <span data-ttu-id="91a50-161">您在稍後的步驟中需要此資訊。</span><span class="sxs-lookup"><span data-stu-id="91a50-161">You need it in a later step.</span></span>

## <a name="clone-the-repo"></a><span data-ttu-id="91a50-162">複製存放庫</span><span class="sxs-lookup"><span data-stu-id="91a50-162">Clone the Repo</span></span>

<span data-ttu-id="91a50-163">複製存放庫以便製作本機複本，讓您可以透過執行下列命令以編輯來源：</span><span class="sxs-lookup"><span data-stu-id="91a50-163">Clone the repo in order to make a local copy for you to edit the source by running the following command:</span></span>

```bash
git clone https://github.com/Azure-Samples/key-vault-node-quickstart.git
```

<span data-ttu-id="91a50-164">::: zone pivot="nodejs"</span><span class="sxs-lookup"><span data-stu-id="91a50-164">::: zone pivot="nodejs"</span></span>

## <a name="install-dependencies"></a><span data-ttu-id="91a50-165">安裝相依性</span><span class="sxs-lookup"><span data-stu-id="91a50-165">Install dependencies</span></span>

<span data-ttu-id="91a50-166">我們在此安裝相依性。</span><span class="sxs-lookup"><span data-stu-id="91a50-166">Here we install the dependencies.</span></span> <span data-ttu-id="91a50-167">執行下列命令 cd key-vault-node-quickstart npm install</span><span class="sxs-lookup"><span data-stu-id="91a50-167">Run the following commands cd key-vault-node-quickstart npm install</span></span>

<span data-ttu-id="91a50-168">此專案使用了 2 個節點模組：</span><span class="sxs-lookup"><span data-stu-id="91a50-168">This project used 2 node modules:</span></span>

* [<span data-ttu-id="91a50-169">ms-rest-azure</span><span class="sxs-lookup"><span data-stu-id="91a50-169">ms-rest-azure</span></span>](https://www.npmjs.com/package/ms-rest-azure) 
* [<span data-ttu-id="91a50-170">azure-keyvault</span><span class="sxs-lookup"><span data-stu-id="91a50-170">azure-keyvault</span></span>](https://www.npmjs.com/package/azure-keyvault)

## <a name="publish-the-web-application-to-azure"></a><span data-ttu-id="91a50-171">將 Web 應用程式發佈至 Azure</span><span class="sxs-lookup"><span data-stu-id="91a50-171">Publish the web application to Azure</span></span>

<span data-ttu-id="91a50-172">以下是我們必須執行的幾個步驟</span><span class="sxs-lookup"><span data-stu-id="91a50-172">Below are the few steps we need to do</span></span>

* <span data-ttu-id="91a50-173">第 1 個步驟是建立 [Azure App Service](https://azure.microsoft.com/services/app-service/) 方案。</span><span class="sxs-lookup"><span data-stu-id="91a50-173">The 1st step is to create a [Azure App Service](https://azure.microsoft.com/services/app-service/) Plan.</span></span> <span data-ttu-id="91a50-174">您可以在此方案中儲存多個 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="91a50-174">You can store multiple web apps in this plan.</span></span>

    ```azurecli
    az appservice plan create --name myAppServicePlan --resource-group myResourceGroup
    ```
* <span data-ttu-id="91a50-175">我們會接著建立 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="91a50-175">Next we create a web app.</span></span> <span data-ttu-id="91a50-176">在下列範例中，使用全域唯一應用程式名稱 (有效的字元為 a-z、0-9 與 -) 取代 <app_name>。</span><span class="sxs-lookup"><span data-stu-id="91a50-176">In the following example, replace <app_name> with a globally unique app name (valid characters are a-z, 0-9, and -).</span></span> <span data-ttu-id="91a50-177">執行階段是設定為 NODE|6.9。</span><span class="sxs-lookup"><span data-stu-id="91a50-177">The runtime is set to NODE|6.9.</span></span> <span data-ttu-id="91a50-178">若要查看所有支援的執行階段，請執行 az webapp list-runtimes</span><span class="sxs-lookup"><span data-stu-id="91a50-178">To see all supported runtimes, run az webapp list-runtimes</span></span>

    ```azurecli
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app_name> --runtime "NODE|6.9" --deployment-local-git
    ```

    <span data-ttu-id="91a50-179">建立 Web 應用程式後，Azure CLI 會顯示類似下列範例的輸出：</span><span class="sxs-lookup"><span data-stu-id="91a50-179">When the web app has been created, the Azure CLI shows output similar to the following example:</span></span>

    ```json
    {
      "availabilityState": "Normal",
      "clientAffinityEnabled": true,
      "clientCertEnabled": false,
      "cloningInfo": null,
      "containerSize": 0,
      "dailyMemoryTimeQuota": 0,
      "defaultHostName": "<app_name>.azurewebsites.net",
      "enabled": true,
      "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git"
      < JSON data removed for brevity. >
    }
    ```
    <span data-ttu-id="91a50-180">瀏覽至新建立的 Web 應用程式，您應會看到正常運作的 Web 應用程式。</span><span class="sxs-lookup"><span data-stu-id="91a50-180">Browse to your newly created web app and you should see a functioning web app.</span></span> <span data-ttu-id="91a50-181">以唯一應用程式名稱取代 <app_name>。</span><span class="sxs-lookup"><span data-stu-id="91a50-181">Replace <app_name> with a unique app name.</span></span>

    ```text
    http://<app name>.azurewebsites.net
    ```
    <span data-ttu-id="91a50-182">上述命令也會建立具有 Git 功能的應用程式，讓您從本機 Git 部署至 Azure。</span><span class="sxs-lookup"><span data-stu-id="91a50-182">The above command also creates a Git-enabled app which allows you to deploy to azure from your local git.</span></span> 
    <span data-ttu-id="91a50-183">本機 Git 已設定為使用 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git' 的 url</span><span class="sxs-lookup"><span data-stu-id="91a50-183">Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'</span></span>

* <span data-ttu-id="91a50-184">在前一個命令完成後，建立部署使用者，您可以將 Azure 遠端新增至您的本機 Git 存放庫。</span><span class="sxs-lookup"><span data-stu-id="91a50-184">Create a deployment user After the previous command is completed you can add add an Azure remote to your local Git repository.</span></span> <span data-ttu-id="91a50-185">使用從「為應用程式啟用 Git」取得的 Git 遠端 URL 取代 <url>。</span><span class="sxs-lookup"><span data-stu-id="91a50-185">Replace <url> with the URL of the Git remote that you got from Enable Git for your app.</span></span>

    ```bash
    git remote add azure <url>
    ```
<span data-ttu-id="91a50-186">::: zone-end</span><span class="sxs-lookup"><span data-stu-id="91a50-186">::: zone-end</span></span>

<span data-ttu-id="91a50-187">::: zone pivot="dotnet"</span><span class="sxs-lookup"><span data-stu-id="91a50-187">::: zone pivot="dotnet"</span></span>

## <a name="open-and-edit-the-solution"></a><span data-ttu-id="91a50-188">開啟及編輯方案</span><span class="sxs-lookup"><span data-stu-id="91a50-188">Open and edit the solution</span></span>

<span data-ttu-id="91a50-189">編輯 program.cs 檔案，以使用您的特定金鑰保存庫名稱來執行範例：</span><span class="sxs-lookup"><span data-stu-id="91a50-189">Edit the program.cs file to run the sample with your specific key vault name:</span></span>

1. <span data-ttu-id="91a50-190">瀏覽至 key-vault-dotnet-core-quickstart 資料夾。</span><span class="sxs-lookup"><span data-stu-id="91a50-190">Browse to the folder key-vault-dotnet-core-quickstart.</span></span>
2. <span data-ttu-id="91a50-191">在 Visual Studio 2017 中開啟 key-vault-dotnet-core-quickstart.sln 檔案。</span><span class="sxs-lookup"><span data-stu-id="91a50-191">Open the key-vault-dotnet-core-quickstart.sln file in Visual Studio 2017.</span></span>
3. <span data-ttu-id="91a50-192">開啟 Program.cs 檔案，並使用您先前建立的金鑰保存庫名稱更新預留位置 *YourKeyVaultName*。</span><span class="sxs-lookup"><span data-stu-id="91a50-192">Open the Program.cs file and update the placeholder *YourKeyVaultName* with the name of your key vault that you created earlier.</span></span>

<span data-ttu-id="91a50-193">這個方案會使用 [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) 與 [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet 程式庫。</span><span class="sxs-lookup"><span data-stu-id="91a50-193">This solution uses [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) and [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet libraries.</span></span>

## <a name="run-the-app"></a><span data-ttu-id="91a50-194">執行應用程式</span><span class="sxs-lookup"><span data-stu-id="91a50-194">Run the app</span></span>

<span data-ttu-id="91a50-195">在 Visual Studio 2017 的主功能表中，選取 [偵錯] > [啟動但不偵錯]。</span><span class="sxs-lookup"><span data-stu-id="91a50-195">From the main menu of Visual Studio 2017, select **Debug** > **Start** without debugging.</span></span> <span data-ttu-id="91a50-196">在瀏覽器出現時，移至 [關於] 頁面。</span><span class="sxs-lookup"><span data-stu-id="91a50-196">When the browser appears, go to the **About** page.</span></span> <span data-ttu-id="91a50-197">**AppSecret** 的值隨即顯示。</span><span class="sxs-lookup"><span data-stu-id="91a50-197">The value for **AppSecret** is displayed.</span></span>

## <a name="publish-the-web-application-to-azure"></a><span data-ttu-id="91a50-198">將 Web 應用程式發佈至 Azure</span><span class="sxs-lookup"><span data-stu-id="91a50-198">Publish the web application to Azure</span></span>

<span data-ttu-id="91a50-199">將此應用程式發佈至 Azure，以 Web 應用程式的形式即時查看，並確認您可以擷取祕密值：</span><span class="sxs-lookup"><span data-stu-id="91a50-199">Publish this app to Azure to see it live as a web app, and to see that you can fetch the secret value:</span></span>

1. <span data-ttu-id="91a50-200">在 Visual Studio 中，選取 **key-vault-dotnet-core-quickstart** 專案。</span><span class="sxs-lookup"><span data-stu-id="91a50-200">In Visual Studio, select the **key-vault-dotnet-core-quickstart** project.</span></span>
2. <span data-ttu-id="91a50-201">選取 [發佈] > [開始]。</span><span class="sxs-lookup"><span data-stu-id="91a50-201">Select **Publish** > **Start**.</span></span>
3. <span data-ttu-id="91a50-202">建立新的 **App Service**，然後選取 [發佈]。</span><span class="sxs-lookup"><span data-stu-id="91a50-202">Create a new **App Service**, and then select **Publish**.</span></span>
4. <span data-ttu-id="91a50-203">將應用程式名稱變更為 **keyvaultdotnetcorequickstart**。</span><span class="sxs-lookup"><span data-stu-id="91a50-203">Change the app name to **keyvaultdotnetcorequickstart**.</span></span>
5. <span data-ttu-id="91a50-204">選取 [建立]。</span><span class="sxs-lookup"><span data-stu-id="91a50-204">Select **Create**.</span></span>

>[!VIDEO https://sec.ch9.ms/ch9/e93d/a6ac417f-2e63-4125-a37a-8f34bf0fe93d/KeyVault_high.mp4]

<span data-ttu-id="91a50-205">::: zone-end</span><span class="sxs-lookup"><span data-stu-id="91a50-205">::: zone-end</span></span>

## <a name="enable-managed-service-identities"></a><span data-ttu-id="91a50-206">啟用受控服務身分識別</span><span class="sxs-lookup"><span data-stu-id="91a50-206">Enable managed service identities</span></span>

<span data-ttu-id="91a50-207">Azure Key Vault 可安全地儲存認證和其他金鑰及祕密，但是您的程式碼必須向 Azure Key Vault 進行驗證，才能擷取這些項目。</span><span class="sxs-lookup"><span data-stu-id="91a50-207">Azure Key Vault provides a way to securely store credentials and other keys and secrets, but your code needs to authenticate to Azure Key Vault to retrieve them.</span></span> <span data-ttu-id="91a50-208">「受控服務識別」可以輕易地解決此問題，因為 MSI 可在 Azure Active Directory (Azure AD) 中將自動受控身分識別提供給 Azure 服務。</span><span class="sxs-lookup"><span data-stu-id="91a50-208">Managed Service Identity makes this easier by giving Azure services an automatically managed identity in Azure Active Directory (Azure AD).</span></span> <span data-ttu-id="91a50-209">您可以使用此身分識別來完成任何支援 Azure AD 驗證的服務驗證 (包括 Key Vault)，不需要任何您程式碼中的認證。</span><span class="sxs-lookup"><span data-stu-id="91a50-209">You can use this identity to authenticate to any service that supports Azure AD authentication, including Key Vault, without having any credentials in your code.</span></span>

1. <span data-ttu-id="91a50-210">返回 Azure CLI。</span><span class="sxs-lookup"><span data-stu-id="91a50-210">Return to the Azure CLI.</span></span>
2. <span data-ttu-id="91a50-211">執行 assign-identity 命令來建立此應用程式的身分識別：</span><span class="sxs-lookup"><span data-stu-id="91a50-211">Run the assign-identity command to create the identity for this application:</span></span>

   ```azurecli
   az webapp identity assign --name "keyvaultdotnetcorequickstart" --resource-group "<YourResourceGroupName>"
   ```

>[!NOTE]
><span data-ttu-id="91a50-212">此程序中的命令等同於前往入口網站，並在 Web 應用程式屬性中將 [受控服務識別] 切換為 [開啟]。</span><span class="sxs-lookup"><span data-stu-id="91a50-212">The command in this procedure is the equivalent of going to the portal and switching **Managed service identity** to **On** in the web application properties.</span></span>

## <a name="assign-permissions-to-your-application-to-read-secrets-from-key-vault"></a><span data-ttu-id="91a50-213">將權限指派給您的應用程式，以便從 Key Vault 讀取秘密</span><span class="sxs-lookup"><span data-stu-id="91a50-213">Assign permissions to your application to read secrets from Key Vault</span></span>

<span data-ttu-id="91a50-214">當您將應用程式發佈至 Azure 時，請記下輸出。</span><span class="sxs-lookup"><span data-stu-id="91a50-214">Make a note of the output when you publish the application to Azure.</span></span> <span data-ttu-id="91a50-215">其格式應為：</span><span class="sxs-lookup"><span data-stu-id="91a50-215">It should be of the format:</span></span>

```json
        {
          "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "SystemAssigned"
        }
```

<span data-ttu-id="91a50-216">然後，使用金鑰保存庫的名稱和 **PrincipalId** 的值來執行此命令：</span><span class="sxs-lookup"><span data-stu-id="91a50-216">Then, run this command by using the name of your key vault and the value of **PrincipalId**:</span></span>

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get
```

<span data-ttu-id="91a50-217">::: zone pivot="nodejs"</span><span class="sxs-lookup"><span data-stu-id="91a50-217">::: zone pivot="nodejs"</span></span>
## <a name="deploy-the-node-app-to-azure-and-retrieve-the-secret-value"></a><span data-ttu-id="91a50-218">將 Node 應用程式部署至 Azure 並擷取祕密值</span><span class="sxs-lookup"><span data-stu-id="91a50-218">Deploy the Node App to Azure and retrieve the secret value</span></span>

<span data-ttu-id="91a50-219">現在一切都已設定。</span><span class="sxs-lookup"><span data-stu-id="91a50-219">Now that everything is set.</span></span> <span data-ttu-id="91a50-220">執行下列命令以將應用程式部署到 Azure</span><span class="sxs-lookup"><span data-stu-id="91a50-220">Run the following command to deploy the app to Azure</span></span>

```bash
git push azure master
```

<span data-ttu-id="91a50-221">在此之後，當您瀏覽 https://<app_name>.azurewebsites.net 時，您可以看見祕密值。</span><span class="sxs-lookup"><span data-stu-id="91a50-221">After this when you browse https://<app_name>.azurewebsites.net you can see the secret value.</span></span>
<span data-ttu-id="91a50-222">確定您已使用保存庫名稱取代名稱 <YourKeyVaultName> ::: zone-end</span><span class="sxs-lookup"><span data-stu-id="91a50-222">Make sure that you replaced the name <YourKeyVaultName> with your vault name ::: zone-end</span></span>

<span data-ttu-id="91a50-223">現在當您執行應用程式時，您應該會看到擷取的秘密值。</span><span class="sxs-lookup"><span data-stu-id="91a50-223">::: zone pivot="dotnet" Now when you run the application, you should see your secret value retrieved.</span></span>
<span data-ttu-id="91a50-224">::: zone-end</span><span class="sxs-lookup"><span data-stu-id="91a50-224">::: zone-end</span></span>

## <a name="next-steps"></a><span data-ttu-id="91a50-225">後續步驟</span><span class="sxs-lookup"><span data-stu-id="91a50-225">Next steps</span></span>

<span data-ttu-id="91a50-226">::: zone pivot="nodejs"</span><span class="sxs-lookup"><span data-stu-id="91a50-226">::: zone pivot="nodejs"</span></span>
* [<span data-ttu-id="91a50-227">Azure Key Vault 首頁</span><span class="sxs-lookup"><span data-stu-id="91a50-227">Azure Key Vault Home Page</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="91a50-228">Azure Key Vault 文件</span><span class="sxs-lookup"><span data-stu-id="91a50-228">Azure Key Vault Documentation</span></span>](https://docs.microsoft.com/azure/key-vault/)
* [<span data-ttu-id="91a50-229">Azure SDK For Node</span><span class="sxs-lookup"><span data-stu-id="91a50-229">Azure SDK For Node</span></span>](https://docs.microsoft.com/javascript/api/overview/azure/key-vault)
* <span data-ttu-id="91a50-230">[Azure REST API 參考](https://docs.microsoft.com/rest/api/keyvault/) ::: zone-end</span><span class="sxs-lookup"><span data-stu-id="91a50-230">[Azure REST API Reference](https://docs.microsoft.com/rest/api/keyvault/) ::: zone-end</span></span>

<span data-ttu-id="91a50-231">::: zone pivot="dotnet"</span><span class="sxs-lookup"><span data-stu-id="91a50-231">::: zone pivot="dotnet"</span></span>
* [<span data-ttu-id="91a50-232">Azure Key Vault 首頁</span><span class="sxs-lookup"><span data-stu-id="91a50-232">Azure Key Vault home page</span></span>](https://azure.microsoft.com/services/key-vault/)
* [<span data-ttu-id="91a50-233">Azure Key Vault 文件</span><span class="sxs-lookup"><span data-stu-id="91a50-233">Azure Key Vault documentation</span></span>](https://docs.microsoft.com/azure/key-vault/)
* [<span data-ttu-id="91a50-234">Azure SDK For .NET</span><span class="sxs-lookup"><span data-stu-id="91a50-234">Azure SDK For .NET</span></span>](https://github.com/Azure/azure-sdk-for-net)
* <span data-ttu-id="91a50-235">[Azure REST API 參考](https://docs.microsoft.com/rest/api/keyvault/) ::: zone-end</span><span class="sxs-lookup"><span data-stu-id="91a50-235">[Azure REST API reference](https://docs.microsoft.com/rest/api/keyvault/) ::: zone-end</span></span>