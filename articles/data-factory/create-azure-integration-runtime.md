---
title: "Criar o tempo de execução de integração do Azure no Azure Data Factory | Microsoft Docs"
description: "Saiba como criar o tempo de execução de integração do Azure no Azure Data Factory, o que é utilizada para copiar dados e emitir as atividades de transformação."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/15/2017
ms.author: spelluru
ms.openlocfilehash: e4e9416d75d5887de059a6fcfc66683940e3e6fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-and-configure-azure-integration-runtime"></a>Como criar e configurar o Runtime de integração do Azure
O tempo de execução de integração (IR) é a infraestrutura de computação utilizada pelo Azure Data Factory para fornecer capacidades de integração de dados entre ambientes de rede diferentes. Para obter mais informações sobre a resposta a incidentes, consulte [integração runtime](concepts-integration-runtime.md).

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço do Data Factory, o que é geralmente disponível (DG), consulte [documentação do Data Factory V1](v1/data-factory-introduction.md).

Resposta a incidentes do Azure fornece uma computação completamente gerida para efetuar nativamente dados movimento e a distribuição dados atividades de transformação para serviços como o HDInsight de computação. Este é alojada num ambiente do Azure e suporta a ligação a recursos no ambiente de rede pública com pontos finais públicos de acessíveis.

Este documento apresenta a forma como pode criar e configurar o Runtime de integração do Azure. 

## <a name="default-azure-ir"></a>Predefinição IR do Azure
Por predefinição, cada fábrica de dados tem uma resposta a incidentes do Azure no back-end que suporte operações na nuvem de dados armazena e serviços na rede pública de computação. A localização de resposta a incidentes que do Azure é resolver automaticamente. Se **connectVia** propriedade não for especificada na definição do serviço ligado, a resposta a incidentes do Azure especificado é utilizada. Só tem de criar explicitamente uma resposta a incidentes Azure quando pretender definir explicitamente a localização da resposta a incidentes, ou se gostaria de virtualmente as execuções de atividade no IRs diferentes para fins de gestão de grupo. 

## <a name="create-azure-ir"></a>Criar IR do Azure
Tempo de execução de integração pode ser criado utilizando o **conjunto AzureRmDataFactoryV2IntegrationRuntime** cmdlet do PowerShell. Para criar uma resposta a incidentes do Azure, especifique o nome, a localização e o tipo para o comando. Eis um comando de exemplo para criar uma resposta a incidentes do Azure com a localização definida como "Europa Ocidental":

```powershell
Set-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName "SampleV2DataFactory1" -Name "MySampleAzureIR" -ResourceGroupName "ADFV2SampleRG" -Type Managed -Location "West Europe"
```  
Para IR do Azure, o tipo tem de ser definido como **gerido**. Não é necessário especificar detalhes de computação, porque está totalmente gerido aprovisionadas na nuvem. Especifique a computação fornece detalhes sobre como o tamanho de nó e nó contagem quando pretende criar IR. SSIS do Azure Para obter mais informações, consulte [criar e configurar o Azure SSIS IR](create-azure-ssis-integration-runtime.md).

Pode configurar uma resposta a incidentes existente do Azure para alterar a localização, utilizando o cmdlet Set-AzureRmDataFactoryV2IntegrationRuntime PowerShell. Para obter mais informações sobre a localização de uma resposta a incidentes do Azure, consulte [introdução à integração runtime](concepts-integration-runtime.md).

## <a name="use-azure-ir"></a>Utilizar IR do Azure

Quando é criada uma resposta a incidentes do Azure, pode referenciá-lo na sua definição de serviço ligado. Abaixo está um exemplo de como pode referenciar o tempo de execução de integração do Azure criado acima de um serviço ligado do Storage do Azure:  

```json
{
    "name": "MyStorageLinkedService",
    "properties": {
      "type": "AzureStorage",
      "typeProperties": {
        "connectionString": {
          "value": "DefaultEndpointsProtocol=https;AccountName=myaccountname;AccountKey=...",
          "type": "SecureString"
        }
      },
      "connectVia": {
        "referenceName": "MySampleAzureIR",
        "type": "IntegrationRuntimeReference"
      }   
    }
}

```

## <a name="next-steps"></a>Passos seguintes
Consulte os artigos seguintes sobre como criar outros tipos de tempos de execução de integração:

- [Create self-hosted integration runtime](create-self-hosted-integration-runtime.md) (Criar o runtime de integração autoalojado)
- [Criar o tempo de execução de integração de SSIS do Azure](create-azure-ssis-integration-runtime.md)
 
