---
title: Armazenar credenciais no Cofre de chaves do Azure | Microsoft Docs
description: "Saiba como armazenar as credenciais para os arquivos de dados utilizados um cofre de chaves do Azure do Azure Data Factory pode obter automaticamente no tempo de execução."
services: data-factory
author: linda33wj
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2017
ms.author: jingwang
ms.openlocfilehash: 193d7c77f01384106b3e0d932d02ba6cdff9e750
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="store-credential-in-azure-key-vault"></a>Armazenar credenciais no Cofre de chaves do Azure

Pode armazenar as credenciais de arquivos de dados de um [Cofre de chaves do Azure](../key-vault/key-vault-whatis.md). O Azure Data Factory obtém as credenciais quando executar uma atividade que utiliza o arquivo de dados. Atualmente, apenas [conector Dynamics](connector-dynamics-crm-office-365.md) suportam esta funcionalidade.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [documenttion para Data Factory version1](v1/data-factory-introduction.md).

## <a name="steps"></a>Passos

Ao criar uma fábrica de dados, uma identidade de serviço pode ser criada, juntamente com a criação de fábrica. A identidade de serviço é uma aplicação gerida registada para o diretório de atividade do Azure e representa nesta fábrica de dados específica.

- Ao criar a fábrica de dados através de **portal do Azure ou o PowerShell**, uma identidade de serviço será sempre criada automaticamente desde pré-visualização pública.
- Ao criar a fábrica de dados através de **SDK**, será criada uma identidade de serviço só se especificar "identidade = FactoryIdentity() novo" no objeto de fábrica para a criação. Consulte o exemplo a partir do [guia de introdução do .NET - criar fábrica de dados](quickstart-create-data-factory-dot-net.md#create-a-data-factory).
- Ao criar a fábrica de dados através de **REST API**, será criada uma identidade de serviço só se especificar a secção "identity" no corpo do pedido. Consulte o exemplo a partir do [REST início rápido - criar fábrica de dados](quickstart-create-data-factory-rest-api.md#create-a-data-factory).

Para fazer referência a uma credencial armazenada no Cofre de chaves do Azure, tem de:

1. Copie o "serviço de identidade ID da aplicação" gerado juntamente com a fábrica de dados. Consulte [obter a identidade de serviço](#retrieve-service-identity).
2. Conceda o acesso ao serviço de identidade para o seu Cofre de chaves do Azure. No seu Cofre de chaves -> acesso de controlo -> Adicionar -> Este ID de aplicação de identidade de serviço para adicionar a permissão de leitor de pesquisa. Permite que esta fábrica designada aceder ao segredo no Cofre de chaves.
3. Crie um serviço ligado aponta para o seu Cofre de chaves do Azure. Consulte [serviço ligado do Cofre de chaves do Azure](#azure-key-vault-linked-service).
4. Crie o serviço ligado do arquivo de dados, no interior de referência de que a chave armazenada no secreta correspondente do cofre. Consulte [referenciar credenciais armazenadas no Cofre de chaves](#reference-credential-stored-in-key-vault).

## <a name="retrieve-service-identity"></a>Obter a identidade de serviço

Pode obter a identidade de serviço a partir do portal do Azure ou através de programação. As secções seguintes mostram alguns exemplos.

>[!TIP]
> Se não vir a identidade de serviço, [gerar uma identidade de serviço](#generate-service-identity) atualizando a fábrica.

### <a name="using-azure-portal"></a>Com o Portal do Azure

Pode encontrar as informações de identidade de serviço do portal do Azure -> a fábrica de dados -> Definições -> propriedades:

- ID DO SERVIÇO DE IDENTIDADE
- INQUILINO DE IDENTIDADE DO SERVIÇO
- **ID de aplicação do serviço identidade** > copiar este valor para conceder acesso no Cofre de chaves

![Obter a identidade de serviço](media/store-credentials-in-key-vault/retrieve-service-identity-portal.png)

### <a name="using-powershell"></a>Com o PowerShell

A identidade de serviço, ID de principal e o ID de inquilino será devolvida ao obter uma fábrica de dados específicos da seguinte forma:

```powershell
PS C:\WINDOWS\system32> (Get-AzureRmDataFactoryV2 -ResourceGroupName <resourceGroupName> -Name <dataFactoryName>).Identity

PrincipalId                          TenantId
-----------                          --------
765ad4ab-XXXX-XXXX-XXXX-51ed985819dc 72f988bf-XXXX-XXXX-XXXX-2d7cd011db47
```

Copie o ID de principal, em seguida, executar abaixo comandos do Azure Active Directory com o ID de principal como parâmetro para obter o **ApplicationId**, que utiliza para conceder acesso no Cofre de chaves:

```powershell
PS C:\WINDOWS\system32> Get-AzureRmADServicePrincipal -ObjectId 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc

ServicePrincipalNames : {76f668b3-XXXX-XXXX-XXXX-1b3348c75e02, https://identity.azure.net/P86P8g6nt1QxfPJx22om8MOooMf/Ag0Qf/nnREppHkU=}
ApplicationId         : 76f668b3-XXXX-XXXX-XXXX-1b3348c75e02
DisplayName           : ADFV2DemoFactory
Id                    : 765ad4ab-XXXX-XXXX-XXXX-51ed985819dc
Type                  : ServicePrincipal
```

## <a name="azure-key-vault-linked-service"></a>Serviço ligado de Cofre de chaves do Azure

As seguintes propriedades são suportadas para o serviço Cofre de chaves do Azure ligada:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **AzureKeyVault**. | Sim |
| BaseUrl | Especifique o URL do Cofre de chaves do Azure. | Sim |

**Exemplo:**

```json
{
    "name": "AzureKeyVaultLinkedService",
    "properties": {
        "type": "AzureKeyVault",
        "typeProperties": {
        "baseUrl": "https://<azureKeyVaultName>.vault.azure.net"
        }
    }
}
```

## <a name="reference-credential-stored-in-key-vault"></a>Credencial de referência armazenado no Cofre de chaves

As seguintes propriedades são suportadas quando configura um campo no serviço ligado que referencia um segredo do Cofre de chaves:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do campo tem de ser definida: **AzureKeyVaultSecret**. | Sim |
| secretName | O nome do segredo no Cofre de chaves do azure. | Sim |
| secretVersion | A versão do segredo no Cofre de chaves do azure.<br/>Se não for especificado, utiliza sempre a versão mais recente do segredo.<br/>Se for especificado, em seguida,-sticks para a versão especificada.| Não |
| arquivo | Refere-se a um serviço Cofre de chaves do Azure ligado que pode utilizar para guardar a credencial. | Sim |

**Exemplo: (consulte a secção "password")**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "<>",
            "organizationName": "<>",
            "authenticationType": "<>",
            "username": "<>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="generate-service-identity"></a>Gerar uma identidade de serviço

Se encontrar a fábrica de dados não tem uma identidade de serviço associada seguir [obter a identidade de serviço](#retrieve-service-identity) instrução, pode gerar um atualizando a fábrica de dados com o iniciador de identidade através de programação.

> [!NOTE]
> - **Não é possível alterar a identidade de serviço**. Atualizar uma fábrica de dados que já tem uma identidade de serviço não terá qualquer impacto, a identidade de serviço será mantida inalterada.
> - **Não é possível eliminar a identidade de serviço**. Se atualizar uma fábrica de dados que já tem uma identidade de serviço, sem especificar o parâmetro de "identity" no objeto de fábrica ou sem especificar a secção "identity" no corpo do pedido REST, irá receber um erro.

As secções seguintes mostram alguns exemplos na geração de identidade de serviço para a fábrica se não existir.

### <a name="using-powershell"></a>Com o PowerShell

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

### <a name="using-rest-api"></a>Com a API REST

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

### <a name="using-sdk"></a>Utilizando o SDK

Chamada à função de create_or_update de fábrica de dados com a identidade = FactoryIdentity() novo. Código de exemplo através do .NET:

```csharp
Factory dataFactory = new Factory
{
    Location = <region>,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
```

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks pela atividade de cópia no Azure Data Factory, consulte [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).