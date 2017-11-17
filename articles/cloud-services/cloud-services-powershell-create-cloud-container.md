---
title: "Criar um contentor de serviços em nuvem com o PowerShell | Microsoft Docs"
description: "Este artigo explica como criar um contentor de serviços em nuvem com o PowerShell. O contentor aloja funções web e de trabalho."
services: cloud-services
documentationcenter: .net
author: cawaMS
manager: timlt
editor: 
ms.assetid: c8f32469-610e-4f37-a3aa-4fac5c714e13
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: cd703feb7bf5af765fc3a5448464499aa7b48d6a
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Utilizar um comando do PowerShell do Azure para criar um contentor do serviço de nuvem vazio
Este artigo explica como criar rapidamente um contentor de serviços em nuvem utilizando cmdlets do PowerShell do Azure. Siga os passos abaixo:

1. Instalar o cmdlet PowerShell do Microsoft Azure a partir de [transferências do Azure PowerShell](http://aka.ms/webpi-azps) página.
2. Abra a linha de comandos do PowerShell.
3. Utilize o [Add-AzureAccount](https://msdn.microsoft.com/library/dn495128.aspx) para iniciar sessão.

   > [!NOTE]
   > Para obter mais instruções sobre como instalar o cmdlet do PowerShell do Azure e ligar à sua subscrição do Azure, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview).
   >
   >
4. Utilize o **New-AzureService** cmdlet para criar um contentor de serviços em nuvem do Azure vazio.

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```
5. Siga este exemplo para invocar o cmdlet:

   ```
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Para obter mais informações sobre como criar o serviço em nuvem do Azure, execute:

```
Get-help New-AzureService
```

### <a name="next-steps"></a>Passos seguintes
* Para gerir a implementação do serviço em nuvem, consulte o [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [Remove-AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx), e [Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx) comandos. Também pode referenciar a [como configurar os serviços de nuvem](cloud-services-how-to-configure-portal.md) para obter mais informações.
* Para publicar o projeto de serviço cloud do Azure, consulte o **PublishCloudService.ps1** exemplo de código do [entrega contínua do serviço de nuvem no Azure](cloud-services-dotnet-continuous-delivery.md).
