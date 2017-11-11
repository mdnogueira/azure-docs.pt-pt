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
ms.date: 11/09/2017
ms.author: jingwang
ms.openlocfilehash: f7604e251bd62ec382ac9ace3de058e345abb863
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="store-credential-in-azure-key-vault"></a>Armazenar credenciais no Cofre de chaves do Azure

Pode armazenar as credenciais de arquivos de dados de um [Cofre de chaves do Azure](../key-vault/key-vault-whatis.md). O Azure Data Factory obtém as credenciais quando executar uma atividade que utiliza o arquivo de dados. Atualmente, apenas [conector Dynamics](connector-dynamics-crm-office-365.md) e [conector do Salesforce](connector-salesforce.md) suportam esta funcionalidade.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [documentação do Data Factory version1](v1/data-factory-introduction.md).

## <a name="prerequisites"></a>Pré-requisitos

Esta funcionalidade baseia-se na identidade de serviço de fábrica de dados. Saiba como funciona do [identidade de serviço do Data factory](data-factory-service-identity.md) e certifique-se a fábrica de dados associado um.

## <a name="steps"></a>Passos

Para fazer referência a uma credencial armazenada no Cofre de chaves do Azure, tem de:

1. [Obter a identidade de serviço do data factory](data-factory-service-identity.md#retrieve-service-identity) ao copiar o valor de "Serviço de identidade ID da aplicação" gerado juntamente com a fábrica.
2. Conceda o acesso ao serviço de identidade para o seu Cofre de chaves do Azure. No seu Cofre de chaves -> acesso de controlo -> Adicionar -> Esta aplicação de identidade de serviço para adicionar, pelo menos, o ID de pesquisa **leitor** permissão. Permite que esta fábrica designada aceder ao segredo no Cofre de chaves.
3. Crie um serviço ligado aponta para o seu Cofre de chaves do Azure. Consulte [serviço ligado do Cofre de chaves do Azure](#azure-key-vault-linked-service).
4. Crie o serviço ligado do arquivo de dados, no interior de referência de que a chave armazenada no secreta correspondente do cofre. Consulte [referenciar credenciais armazenadas no Cofre de chaves](#reference-credential-stored-in-key-vault).

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
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks pela atividade de cópia no Azure Data Factory, consulte [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats).