---
title: "Introdução ao Azure Data Lake Analytics com o Azure PowerShell | Microsoft Docs"
description: 'Utilize o Azure PowerShell para criar uma conta do Data Lake Analytics, criar um trabalho do Data Lake Analytics com U-SQL e submeter o trabalho. '
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/04/2017
ms.author: edmaca
ms.openlocfilehash: 4f73e27c733edae658d1ea3bdabe48076328279b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Introdução ao Azure Data Lake Analytics com o Azure PowerShell
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Saiba como utilizar o Azure PowerShell para criar contas do Azure Data Lake Analytics e, em seguida, submeter e executar tarefas de U-SQL. Para mais informações sobre a Data Lake Analytics, consulte [Descrição geral da Análise do Azure Data Lake](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter as seguintes informações:

* **Uma conta do Azure Data Lake Analytics**. Consulte [Introdução ao Data Lake Analytics](https://docs.microsoft.com/en-us/azure/data-lake-analytics/data-lake-analytics-get-started-portal).
* **Uma estação de trabalho com o Azure PowerShell**. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Este tutorial pressupõe que já esteja familiarizado com a utilização do Azure PowerShell. Em especial, precisa de saber como iniciar sessão no Azure. Se precisar de ajuda, consulte [Get started with Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/get-started-azureps) (Introdução ao Azure PowerShell).

Iniciar sessão com um nome de subscrição:

```
Login-AzureRmAccount -SubscriptionName "ContosoSubscription"
```

Em vez do nome de subscrição, também pode utilizar um D de subscrição para iniciar sessão:

```
Login-AzureRmAccount -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

Se for bem-sucedido, a saída desse comando tem o aspeto do seguinte texto:

```
Environment           : AzureCloud
Account               : joe@contoso.com
TenantId              : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionId        : "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
SubscriptionName      : ContosoSubscription
CurrentStorageAccount :
```

## <a name="preparing-for-the-tutorial"></a>Preparação para o tutorial

Os fragmentos de código do PowerShell neste tutorial utilizam estas variáveis para armazenar estas informações:

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeStoreAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Obter informações sobre uma conta do Data Lake Analytics

```
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>Submeter uma tarefa de U-SQL

Crie uma variável do PowerShell para manter o script U-SQL.

```
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();

"@
```

Submeta o script.

```
$job = Submit-AdlJob -AccountName $adla –Script $script
```

Como alternativa, pode guardar o script como um ficheiro e enviar com o seguinte comando:

```
$filename = "d:\test.usql"
$script | out-File $filename
$job = Submit-AdlJob -AccountName $adla –ScriptPath $filename
```


Obtenha o estado de uma tarefa específica. Continue a utilizar este cmdlet até perceber que a tarefa está concluída.

```
$job = Get-AdlJob -AccountName $adla -JobId $job.JobId
```

Em vez de chamar Get-AdlAnalyticsJob repetidamente até que uma tarefa seja concluída, pode utilizar o cmdlet Wait-AdlJob.

```
Wait-AdlJob -Account $adla -JobId $job.JobId
```

Transfira o ficheiro de saída.

```
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "C:\data.csv"
```

## <a name="see-also"></a>Consultar também
* Para ver o mesmo tutorial, utilizando outras ferramentas, clique nos seletores de separador no topo da página.
* Para saber mais sobre U-SQL, consulte [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para tarefas de gestão, veja [Manage Azure Data Lake Analytics using Azure portal (Gerir o Azure Data Lake Analytics com o Portal do Azure)](data-lake-analytics-manage-use-portal.md).
