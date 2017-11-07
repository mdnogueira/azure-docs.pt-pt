---
title: Copiar dados de Dynamics CRM e 365 utilizando o Azure Data Factory | Microsoft Docs
description: "Saiba como copiar dados a partir do Dynamics CRM e 365 aos arquivos de dados dependente suportados através da utilização de uma atividade de cópia no pipeline Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/02/2017
ms.author: jingwang
ms.openlocfilehash: 1af330596052a92237469aba4729474e7fe417aa
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-from-dynamics-365dynamics-crm-using-azure-data-factory"></a>Copiar dados de Dynamics 365 / Dynamics CRM utilizando o Azure Data Factory

Este artigo descreve como utilizar a atividade de cópia no Azure Data Factory para copiar dados de Dynamics 365 / Dynamics CRM. Baseia-se no [copiar descrição geral da atividade](copy-activity-overview.md) artigo que apresenta uma descrição geral da atividade de cópia.

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [atividade de cópia no V1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Capacidades suportadas

Pode copiar dados de Dynamics 365 / Dynamics CRM para qualquer arquivo de dados suportados sink. Para obter uma lista dos arquivos de dados suportados como origens/sinks pela atividade de cópia, consulte o [arquivos de dados suportados](copy-activity-overview.md#supported-data-stores-and-formats) tabela.

Especificamente, este conector Dynamics suporta abaixo versões de Dynamics e tipos de autenticação:

| Versões de Dynamics | Tipos de autenticação | Exemplos de serviço ligado |
|:--- |:--- |:--- |
| Dynamics 365 online <br> Dynamics CRM online | Office 365 | [Dynamics Online + autenticação do Office 365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 no local com IFD <br> Dynamics CRM 2016 no local com IFD <br> Dynamics CRM 2015 no local com IFD | IFD | [Dynamics no local com IFD + IFD auth](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

*IFD é curto para Internet com acesso à implementação.*

> [!NOTE]
> Para utilizar o conector de Dynamics, armazene a palavra-passe no Cofre de chaves do Azure e permitem a solicitação de concentre ADF cópia a partir daí, quando efetuar a cópia de dados. Veja como configurar no [ligado propriedades do serviço](#linked-service-properties) secção.

## <a name="getting-started"></a>Introdução

Pode criar um pipeline com atividade de cópia com o .NET SDK, o Python SDK, o Azure PowerShell, o REST API ou o modelo Azure Resource Manager. Consulte [tutorial de atividade de cópia](quickstart-create-data-factory-dot-net.md) para obter instruções passo a passo Criar um pipeline com uma atividade de cópia.

As secções seguintes fornecem detalhes sobre as propriedades que são utilizados para definir o Dynamics entidades do Data Factory específicas.

## <a name="linked-service-properties"></a>Propriedades de serviço ligado

As seguintes propriedades são suportadas para o serviço ligado do Dynamics:

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 e Dynamics CRM Online

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **Dynamics**. | Sim |
| deploymentType | O tipo de implementação da instância do Dynamics. Tem de ser **"Online"** para o Dynamics Online. | Sim |
| OrganizationName | O nome da organização da instância do Dynamics. | Não, deve especificar quando existem várias instâncias de Dynamics associadas ao utilizador. |
| authenticationType | O tipo de autenticação para ligar ao servidor de Dynamics. Especifique **"Office 365"** para Dynamics Online. | Sim |
| o nome de utilizador | Especifique o nome de utilizador para ligar para o Dynamics. | Sim |
| palavra-passe | Especifique a palavra-passe da conta de utilizador especificado para o nome de utilizador. Tem de colocar a palavra-passe no Cofre de chaves do Azure e configurar a palavra-passe como uma "AzureKeyVaultSecret". Para obter mais informações, consulte o [armazenar credenciais no Cofre de chaves](store-credentials-in-key-vault.md) artigo. | Sim |

**Exemplo: Dynamics online através da autenticação do Office 365**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "organizationName": "orga02d9c75",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
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

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 e Dynamics CRM no local com IFD

*Propriedades adicionais a comparação com Dyanmics online são "nome do anfitrião" e "porta".*

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **Dynamics**. | Sim |
| deploymentType | O tipo de implementação da instância do Dynamics. Tem de ser **"OnPremisesWithIfd"** para Dynamics no local com IFD.| Sim |
| **nome de anfitrião** | O nome de anfitrião do servidor de Dynamics no local. | Sim |
| **porta** | A porta do servidor de Dynamics no local. | Não, a predefinição é 443 |
| OrganizationName | O nome da organização da instância do Dynamics. | Sim |
| authenticationType | O tipo de autenticação para ligar ao servidor de Dynamics. Especifique **"Ifd"** para Dynamics no local com IFD. | Sim |
| o nome de utilizador | Especifique o nome de utilizador para ligar para o Dynamics. | Sim |
| palavra-passe | Especifique a palavra-passe da conta de utilizador especificado para o nome de utilizador. Tenha em atenção de que tem de colocar a palavra-passe no Cofre de chaves do Azure e configurar a palavra-passe como uma "AzureKeyVaultSecret". Para obter mais informações, consulte o [armazenar credenciais no Cofre de chaves](store-credentials-in-key-vault.md) artigo. | Sim |

**Exemplo: Dynamics no local com IFD através da autenticação IFD**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
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

## <a name="dataset-properties"></a>Propriedades do conjunto de dados

Para uma lista completa das secções e propriedades disponíveis para definir os conjuntos de dados, consulte o [conjuntos de dados](concepts-datasets-linked-services.md) artigo. Esta secção fornece uma lista de propriedades suportadas por Dynamics conjunto de dados.

Para copiar dados de Dynamics, defina a propriedade de tipo do conjunto de dados para **DynamicsEntity**. São suportadas as seguintes propriedades:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo do conjunto de dados tem de ser definida: **DynamicsEntity** |Sim |
| EntityName | O nome lógico da entidade a obter. | Não (se for especificada "consulta" na origem de atividade) |

> [!IMPORTANT]
> **A secção "estrutura" no conjunto de dados é necessária para o Dynamics**, que define o tipo de dados e o nome de coluna para dados de Dynamics que pretende copiar através de. Saiba mais de [estrutura do conjunto de dados](concepts-datasets-linked-services.md#dataset-structure) e [mapeamento de tipo de dados para o Dynamics](#data-type-mapping-for-dynamics).

**Exemplo:**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "structure": [
            {
                "name": "accountid",
                "type": "Guid"
            },
            {
                "name": "name",
                "type": "String"
            },
            {
                "name": "marketingonly",
                "type": "Boolean"
            },
            {
                "name": "modifiedon",
                "type": "Datetime"
            }
        ],
        "typePoperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propriedades da atividade de cópia

Para uma lista completa das secções e propriedades disponíveis para definir as atividades, consulte o [Pipelines](concepts-pipelines-activities.md) artigo. Esta secção fornece uma lista de propriedades suportado pela origem de Dynamics.

### <a name="dynamics-as-source"></a>Dynamics como origem

Para copiar dados de Dynamics, defina o tipo de origem na atividade de cópia para **DynamicsSource**. As seguintes propriedades são suportadas na atividade de cópia **origem** secção:

| Propriedade | Descrição | Necessário |
|:--- |:--- |:--- |
| tipo | A propriedade de tipo tem de ser definida: **DynamicsSource**  | Sim |
| consulta  | FetchXML é uma linguagem de consulta proprietárias, que é utilizada na Microsoft Dynamics (online & no local). Consulte o exemplo abaixo e obter mais informações de [construir consultas com FeachXML](https://msdn.microsoft.com/en-us/library/gg328332.aspx). | Não (se for especificado "entityName" no conjunto de dados)  |

**Exemplo:**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>Consulta de FetchXML de exemplo

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

## <a name="data-type-mapping-for-dynamics"></a>Tipo de dados de mapeamento para o Dynamics

Quando copiar dados de Dynamics, os seguintes mapeamentos são utilizados Dynamics tipos de dados para tipos de dados intermédio do Azure Data Factory. Consulte [mapeamentos de tipo de esquema e dados](copy-activity-schema-and-type-mapping.md) para saber mais sobre como atividade de cópia mapeia o tipo de esquema e os dados de origem para o sink.

Configurar o tipo de dados do ADF correspondente na estrutura do conjunto de dados com base na sua origem de dados de Dynamics escreva utilizando a tabela de mapeamento abaixo:

| Tipo de dados de Dynamics | Tipo de dados intermédio de fábrica de dados |
|:--- |:--- |
| AttributeTypeCode.BigInt | Longo |
| AttributeTypeCode.Boolean | Valor booleano |
| AttributeType.Customer | GUID |
| AttributeType.DateTime | DateTime |
| AttributeType.Decimal | Decimal |
| AttributeType.Double | duplo |
| AttributeType.EntityName | Cadeia |
| AttributeType.Integer | Int32 |
| AttributeType.Lookup | GUID |
| AttributeType.ManagedProperty | Valor booleano |
| AttributeType.Memo | Cadeia |
| AttributeType.Money | Decimal |
| AttributeType.Owner | GUID |
| AttributeType.Picklist | Int32 |
| AttributeType.Uniqueidentifier | GUID |
| AttributeType.String | Cadeia |
| AttributeType.State | Int32 |
| AttributeType.Status | Int32 |

> [!NOTE]
> O tipo de dados de Dynamics AttributeType.CalendarRules e AttributeType.PartyList não são suportados.


## <a name="next-steps"></a>Passos seguintes
Para obter uma lista dos arquivos de dados suportados como origens e sinks pela atividade de cópia no Azure Data Factory, consulte [arquivos de dados suportados](copy-activity-overview.md##supported-data-stores-and-formats).