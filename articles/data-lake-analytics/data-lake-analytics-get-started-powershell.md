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
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 6985dff332928d704f30e167c3bddb62bcc6cac1
ms.contentlocale: pt-pt
ms.lasthandoff: 05/09/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Tutorial: introdução à Análise do Azure Data Lake ao utilizar o Azure PowerShell
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Saiba como utilizar o Azure PowerShell para criar contas do Azure Data Lake Analytics e, em seguida, submeter e executar tarefas de U-SQL. Para mais informações sobre a Data Lake Analytics, consulte [Descrição geral da Análise do Azure Data Lake](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter as seguintes informações:

* **Uma subscrição do Azure**. Consulte [Obter versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Uma estação de trabalho com o Azure PowerShell**. Consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview).

## <a name="preparing-for-the-tutorial"></a>Preparação para o tutorial
Para criar uma conta do Data Lake Analytics, tem primeiro de definir:

* **Grupo de Recursos do Azure**: uma conta de Data Lake Analytics tem de ser criada dentro de um grupo de Recursos do Azure.
* **Nome da conta de Data Lake Analytics**: o nome da conta de Data Lake Analytics só pode conter letras minúsculas e números.
* **Localização**: um dos centros de dados do Azure que suportem a Data Lake Analytics.
* **Conta do Data Lake Store predefinida**: cada conta do Data Lake Analytics tem uma conta do Data Lake Store predefinida. Estas contas têm de estar na mesma localização.

Os fragmentos de PowerShell neste tutorial utilizam estas variáveis para armazenar estas informações

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="create-a-data-lake-analytics-account"></a>Criar uma conta de Data Lake Analytics

Se ainda não tiver um Grupo de Recursos para utilizar, crie um. 

```
New-AzureRmResourceGroup -Name  $rg -Location $location
```

Cada conta de Data Lake Analytics requer uma conta de Data Lake Store predefinida que utiliza para armazenar registos. Pode reutilizar uma conta existente ou criar uma conta nova. 

```
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

Assim que um Grupo de Recursos e uma conta de Data Lake Store estiverem disponíveis, crie uma conta de Data Lake Analytics.

```
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Obter informações sobre uma conta do Data Lake Analytics

```
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>Submeter uma tarefa de U-SQL

Crie um ficheiro de texto com o seguinte script de U-SQL.

```
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
```

Submeta o script.

```
Submit-AdlJob -AccountName $adla –ScriptPath "d:\test.usql"Submit
```

## <a name="monitor-u-sql-jobs"></a>Monitorizar tarefas de U-SQL

Faça uma lista de todas as tarefas na conta. A saída inclui as tarefas atualmente em execução e as tarefas que foram concluídas recentemente.

```
Get-AdlJob -Account $adla
```

Obtenha o estado de uma tarefa específica.

```
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

Em vez de chamar Get-AdlAnalyticsJob repetidamente até que uma tarefa seja concluída, pode utilizar o cmdlet Wait-AdlJob.

```
Wait-AdlJob -Account $adla -JobId $job.JobId
```

Depois de a tarefa estar concluída, verifique se o ficheiro de saída existe, listando os ficheiros numa pasta.

```
Get-AdlStoreChildItem -Account $adls -Path "/"
```

Verifique a existência de um ficheiro.

```
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

## <a name="uploading-and-downloading-files"></a>Carregar e Descarregar ficheiros

Descarregue a saída do script de U-SQL.

```
Export-AdlStoreItem -AccountName $adls -Path "/data.csv"  -Destination "D:\data.csv"
```


Carregue um ficheiro para ser utilizado como unput de um script de U-SQL.

```
Import-AdlStoreItem -AccountName $adls -Path "D:\data.tsv" -Destination "/data_copy.csv" 
```

## <a name="see-also"></a>Consultar também
* Para ver o mesmo tutorial, utilizando outras ferramentas, clique nos seletores de separador no topo da página.
* Para saber mais sobre U-SQL, consulte [Introdução à linguagem U-SQL da Análise do Azure Data Lake](data-lake-analytics-u-sql-get-started.md).
* Para tarefas de gestão, veja [Manage Azure Data Lake Analytics using Azure portal (Gerir o Azure Data Lake Analytics com o Portal do Azure)](data-lake-analytics-manage-use-portal.md).

