<properties
   pageTitle="使用 Linux VM 擴充功能編寫範本 | Microsoft Azure"
   description="了解如何使用 Linux VM 擴充功能編寫 Azure Resource Manager 範本"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# 使用 Linux VM 擴充功能編寫 Azure Resource Manager 範本

[AZURE.INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

從 Azure CLI，執行下列命令︰

      Azure VM extension list

此命令會傳回發行者名稱、擴充功能名稱和版本，如下所示：

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

這三個屬性分別對應至上述範本程式碼片段中的 "publisher"、"type" 和 "typeHandlerVersion"。

>[AZURE.NOTE]建議一律使用最新的擴充功能版本，以取得最新的功能。

## 識別擴充功能組態參數的結構描述

編寫擴充功能範本的下一個步驟是識別用於提供組態參數的格式。每個延伸模組都支援自己的參數集。

若要查看 Linux 擴充功能的範例組態，請按一下 [Linux 擴充功能範例](virtual-machines-linux-extensions-configuration-samples.md)文件。

請參閱下列項目以取得 VM 擴充功能的完整範本。

[Linux VM 上的自訂指令碼擴充功能。](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

編寫範本之後，您可以使用 Azure CLI 部署它。

<!---HONumber=AcomDC_0601_2016-->