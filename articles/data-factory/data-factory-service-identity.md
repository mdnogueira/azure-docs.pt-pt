---
title: "Identidade de serviço do Azure Data Factory | Microsoft Docs"
description: "Saiba mais sobre a identidade de serviço de fábrica de dados no Azure Data Factory."
services: data-factory
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: jingwang
ms.openlocfilehash: 4b970341f9511a889bfcc288756b1be6a4f4647e
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="azure-data-factory-service-identity"></a>Identidade de serviço do Azure Data Factory

Este artigo ajuda-o a compreender o que é a identidade de serviço do data factory e como funciona.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [documentação do Data Factory version1](v1/data-factory-introduction.md).

## <a name="overview"></a>Descrição geral

Ao criar uma fábrica de dados, uma identidade de serviço pode ser criada, juntamente com a criação de fábrica. A identidade de serviço é uma aplicação gerida registada para o diretório de atividade do Azure e representa nesta fábrica de dados específica.

Identidade de serviço do Data factory é vantajosa as duas seguintes funcionalidades:

- [Armazenar credenciais no Cofre de chaves do Azure](store-credentials-in-key-vault.md), caso em que a identidade de serviço de fábrica de dados é utilizada para autenticação de Cofre de chaves do Azure.
- [Copiar dados de/para o Azure Data Lake Store](connector-azure-data-lake-store.md), caso em que a identidade de serviço de fábrica de dados pode ser utilizada como um dos tipos suportados para a autenticação de Data Lake Store.

## <a name="generate-service-identity"></a>Gerar uma identidade de serviço

Identidade de serviço do Data factory é gerada da seguinte forma:

- Ao criar a fábrica de dados através de **portal do Azure ou o PowerShell**, uma identidade de serviço será sempre criada automaticamente desde ADF V2 pré-visualização pública.
- Ao criar a fábrica de dados através de **SDK**, será criada uma identidade de serviço só se especificar "identidade = FactoryIdentity() novo" no objeto de fábrica para a criação. Consulte o exemplo na [guia de introdução do .NET - criar fábrica de dados](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Ao criar a fábrica de dados através de **REST API**, será criada uma identidade de serviço só se especificar a secção "identity" no corpo do pedido. Consulte o exemplo na [REST início rápido - criar fábrica de dados](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Se encontrar a fábrica de dados não tem uma identidade de serviço associada seguir [obter a identidade de serviço](#retrieve-service-identity) instrução, pode explicitamente gerar um atualizando a fábrica de dados com o iniciador de identidade através de programação:

- [Gerar uma identidade de serviço com o PowerShell](#generate-service-identity-using-powershell)
- [Gerar uma identidade de serviço utilizando a REST API](#generate-service-identity-using-rest-api)
- [Gerar uma identidade de serviço utilizando o SDK](#generate-service-identity-using-sdk)

>[!NOTE]
>- Não é possível alterar a identidade de serviço. Atualizar uma fábrica de dados que já tem uma identidade de serviço não terá qualquer impacto, a identidade de serviço é mantida inalterada.
>- Se atualizar uma fábrica de dados que já tem uma identidade de serviço sem especificar o parâmetro de "identity" no objeto de fábrica ou sem especificar a secção "identity" no corpo do pedido REST, irá receber um erro.
>- Quando elimina uma fábrica de dados, será eliminada a identidade de serviço associado ao longo.

### <a name="generate-service-identity-using-powershell"></a>Gerar uma identidade de serviço com o PowerShell

Chamar **conjunto AzureRmDataFactoryV2** utilizar novamente o comando, em seguida, consulte o "Identity" campos gerados recentemente:

```powershell
PS C:\WINDOWS\system32> Set-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName> -Location <region>

DataFactoryName   : ADFV2DemoFactory
DataFactoryId     : /subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory
ResourceGroupName : <resourceGroupName>
Location          : East US
Tags              : {}
Identity          : Microsoft.Azure.Management.DataFactory.Models.FactoryIdentity
ProvisioningState : Succeeded
```

### <a name="generate-service-identity-using-rest-api"></a>Gerar uma identidade de serviço utilizando a REST API

Chamar abaixo API com a secção "identity" no corpo do pedido:

```
PATCH https://management.azure.com/subscriptions/<subsID>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/<data factory name>?api-version=2017-09-01-preview
```

**Corpo do pedido**: adicionar "identity": {"type": "SystemAssigned"}.

```json
{
    "name": "<dataFactoryName>",
    "location": "<region>",
    "properties": {},
    "identity": {
        "type": "SystemAssigned"
    }
}
```

**Resposta**: identidade de serviço é criada automaticamente e a secção "identity" é preenchida em conformidade.

```json
{
    "name": "ADFV2DemoFactory",
    "tags": {},
    "properties": {
        "provisioningState": "Succeeded",
        "loggingStorageAccountKey": "**********",
        "createTime": "2017-09-26T04:10:01.1135678Z",
        "version": "2017-09-01-preview"
    },
    "identity": {
        "type": "SystemAssigned",
        "principalId": "765ad4ab-XXXX-XXXX-XXXX-51ed985819dc",
        "tenantId": "72f988bf-XXXX-XXXX-XXXX-2d7cd011db47"
    },
    "id": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.DataFactory/factories/ADFV2DemoFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "EastUS"
}
```

### <a name="generate-service-identity-using-sdk"></a>Gerar uma identidade de serviço utilizando o SDK

Chamada à função de create_or_update de fábrica de dados com a identidade = FactoryIdentity() novo. Código de exemplo através do .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="retrieve-service-identity"></a>Obter a identidade de serviço

Pode obter a identidade de serviço a partir do portal do Azure ou através de programação. As secções seguintes mostram alguns exemplos.

>[!TIP]
> Se não vir a identidade de serviço, [gerar uma identidade de serviço](#generate-service-identity) atualizando a fábrica.

### <a name="retrieve-service-identity-using-azure-portal"></a>Obter a identidade de serviço através do portal do Azure

Pode encontrar as informações de identidade de serviço do portal do Azure -> a fábrica de dados -> Definições -> propriedades:

- ID DO SERVIÇO DE IDENTIDADE
- INQUILINO DE IDENTIDADE DO SERVIÇO
- **ID de aplicação do serviço identidade** > copiar este valor

![Obter a identidade de serviço](media/data-factory-service-identity/retrieve-service-identity-portal.png)

### <a name="retrieve-service-identity-using-powershell"></a>Obter a identidade de serviço com o PowerShell

A identidade de serviço, ID de principal e o ID de inquilino será devolvida ao obter uma fábrica de dados específicos da seguinte forma:

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Copie o ID de principal, em seguida, executar abaixo comandos do Azure Active Directory com o ID de principal como parâmetro para obter o **ApplicationId**, que utiliza para conceder acesso:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="next-steps"></a>Passos seguintes
Consulte os tópicos seguintes que introduzem quando e como utilizar a identidade de serviço de fábrica de dados:

- [Armazenar credenciais no Cofre de chaves do Azure](store-credentials-in-key-vault.md)
- [Copiar dados de/para o Azure Data Lake Store utilizando a autenticação de identidade de serviço geridas](connector-azure-data-lake-store.md)