---
title: "Monitorizar a integração tempo de execução no Azure Data Factory | Microsoft Docs"
description: "Saiba como monitorizar tipos diferentes de tempo de execução de integração no Azure Data Factory."
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
ms.date: 09/23/2017
ms.author: spelluru
ms.openlocfilehash: e1bfb7199ddf9f02297db9de529729ba3833cf8c
ms.sourcegitcommit: c50171c9f28881ed3ac33100c2ea82a17bfedbff
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/26/2017
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Monitorizar um tempo de execução de integração no Azure Data Factory  
**Tempo de execução de integração** é a infraestrutura de computação utilizada pelo Azure Data Factory para fornecer as diversas capacidades de integração de dados entre ambientes de rede diferentes. Existem três tipos de tempos de execução de integração oferecidos pelo Data Factory:

- Runtime de integração do Azure
- Runtime de integração autoalojado
- Runtime de integração de SSIS do Azure

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em pré-visualização. Se estiver a utilizar a versão 1 do serviço Data Factory, que está disponível em geral (GA), veja [Data Factory version 1 documentation](v1/data-factory-introduction.md) (Documentação da versão 1 do Data Factory).

Para obter o estado de uma instância do tempo de execução de integração (IR), execute o seguinte comando do PowerShell: 

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

O cmdlet devolve informações diferentes para diferentes tipos de tempo de execução de integração. Este artigo explica as propriedades e Estados para cada tipo de tempo de execução de integração.  

## <a name="azure-integration-runtime"></a>Runtime de integração do Azure
O recurso de computação de um tempo de execução de integração do Azure é totalmente gerido aprovisionadas no Azure. A tabela seguinte fornece descrições para as propriedades devolvidas pelo **Get-AzureRmDataFactoryV2IntegrationRuntime** comando:

### <a name="properties"></a>Propriedades
A tabela seguinte fornece descrições das propriedades devolvidas pelo cmdlet para um tempo de execução de integração do Azure:

| Propriedade | Descrição |
-------- | ------------- | 
| Nome | Nome do tempo de execução de integração do Azure. |  
| Estado | Estado do tempo de execução de integração do Azure. | 
| Localização | Localização do tempo de execução de integração do Azure. Para obter detalhes sobre a localização de um tempo de execução de integração do Azure, consulte [introdução à integração runtime](concepts-integration-runtime.md). |
| DataFactoryName | Nome da fábrica de dados que pertence o tempo de execução de integração do Azure. | 
| resourceGroupName | Nome do grupo de recursos que pertence a fábrica de dados.  |
| Descrição | Descrição do tempo de execução de integração.  |

### <a name="status"></a>Estado
A tabela seguinte fornece os possíveis Estados de um tempo de execução de integração do Azure:

| Estado | Comentários/cenários | 
| ------ | ------------------ |
| Online | O tempo de execução de integração do Azure está online e pronto a ser utilizado. | 
| Offline | O tempo de execução de integração do Azure está offline devido a um erro interno. |

## <a name="self-hosted-integration-runtime"></a>Runtime de integração autoalojado
Esta secção fornece descrições para as propriedades devolvidas pelo cmdlet Get-AzureRmDataFactoryV2IntegrationRuntime. 

> [!NOTE] 
> O estado e as propriedades devolvidas contêm informações sobre o tempo de execução automática alojada na integração geral e cada nó no tempo de execução.  

### <a name="properties"></a>Propriedades

A tabela seguinte fornece descrições de monitorização de propriedades para **cada nó**:

| Propriedade | Descrição | 
| -------- | ----------- | 
| Nome | Nome do tempo de execução automática alojada integração e nós associados à mesma. O nó é uma máquina no local do Windows que tenha o tempo de execução automática alojada integração instalado no mesmo. |  
| Estado | O estado do tempo de execução automática alojada na integração geral e cada nó. Exemplo: Online/Offline/limitado/etc. Para obter informações sobre estes Estados terem, consulte a secção seguinte. | 
| Versão | A versão do tempo de execução automática alojada integração e cada nó. A versão do tempo de execução automática alojada integração é determinada com base na versão de maioria de nós no grupo. Se existirem nós com versões diferentes do programa de configuração de tempo de execução da integração personalizada alojada, apenas os nós com o mesmo número de versão como lógico autoalojado função de integração de tempo de execução corretamente. Outras pessoas estão no modo de limitado e tem de ser atualizados manualmente (apenas no caso de falha de atualização automática). | 
| Memória disponível | Memória disponível num nó de tempo de execução automática alojada integração. Este valor é um instantâneo em tempo real near. | 
| Utilização da CPU | Utilização da CPU de um nó de tempo de execução automática alojada integração. Este valor é um instantâneo em tempo real near. |
| Funcionamento em rede (na/saída) | Utilização de um nó de tempo de execução automática alojada integração de rede. Este valor é um instantâneo em tempo real near. | 
| Tarefas simultâneas (em execução / limitar) | Número de tarefas ou tarefas em execução em cada nó. Este valor é um instantâneo em tempo real near. Limite representa as máximas tarefas simultâneas para cada nó. Este valor é definido com base no tamanho da máquina. Pode aumentar o limite de aumentar verticalmente a execução da tarefa em simultâneo em cenários avançados, onde CPU/memória/rede é em utilizados, mas as atividades são exceder o tempo limite. Esta capacidade também está disponível com um tempo de execução de integração personalizada alojada de nó único. |
| Função | Existem dois tipos de funções no tempo de execução do integração personalizada alojada com vários nós – emissor e de trabalho. Todos os nós são trabalhadores, o que significa que podem todos ser utilizados para executar tarefas. Não há apenas um nó de emissor, o que é utilizado para extrair as tarefas/tarefas dos serviços cloud e emiti-los a nós de trabalho diferentes. O nó de emissor também é um nó de trabalho. |

Algumas definições das propriedades de fazem sentido mais quando existem dois ou mais nós (cenário de escalamento horizontal) em tempo de execução automática alojada integração. 
  
### <a name="status-per-node"></a>Estado (por nó)
A tabela seguinte fornece os possíveis Estados de um nó de tempo de execução automática alojada integração:

| Estado | Descrição |
| ------ | ------------------ | 
| Online | O serviço Data Factory está ligado o nó. |
| Offline | Nó está offline. |
| A atualizar | O nó está a ser atualizados automaticamente. |
| Limitado | Devido a um problema de conectividade. Pode ser devido ao problema de 8050 da porta HTTP, problema de conectividade do barramento de serviço ou um problema de sincronização de credenciais. |
| Inativos | Nó está a ser uma configuração diferente da configuração dos outros nós da maioria. |

Um nó pode ficar inativo quando não é possível ligar a outros nós.

### <a name="status-overall-self-hosted-integration-runtime"></a>Estado (tempo de execução automática alojada na integração geral)
A tabela seguinte fornece os possíveis Estados de um tempo de execução de integração personalizada alojada. Este estado depende de Estados de todos os nós que pertencem ao tempo de execução. 

| Estado | Descrição |
| ------ | ----------- | 
| Tem de registo | Nenhum nó está registada para este tempo de execução automática alojada integração ainda. |
| Online | Todos os nós estão online. |
| Offline | Nenhum nó está online. |
| Limitado | Nem todos os nós no tempo de execução integração personalizada alojada estão em bom estado de funcionamento. Este estado é um aviso que algumas nós podem estar inativa. Este estado pode ser devido a um problema de sincronização de credenciais no nó emissor/trabalho. |

Utilize o **Get-AzureRmDataFactoryV2IntegrationRuntimeMetric** cmdlet para obter o payload JSON que contém a secção detalhada autoalojado propriedades do tempo de execução de integração e os respetivos instantâneo valores durante a hora de execução do cmdlet.

```powershell
Get-AzureRmDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName  | | ConvertTo-Json 
```

Saída de exemplo (parte do princípio de que existem dois nós associados a este tempo de execução automática alojada integração):

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```


## <a name="azure-ssis-integration-runtime"></a>Runtime de integração de SSIS do Azure
Tempo de execução de integração de SSIS do Azure é um cluster completamente gerido do Azure máquinas virtuais (ou nós) dedicado para executar os pacotes SSIS. Não é executado quaisquer outras atividades do Azure Data Factory. Assim que aprovisionado, pode consultar as respetivas propriedades e monitorizar os respetivos Estados de nó/geral-específicos.

### <a name="properties"></a>Propriedades

| Propriedade/Estado | Descrição |
| --------------- | ----------- |
| CreateTime | A hora UTC quando o tempo de execução de integração do Azure SSIS foi criado. |
| Nós | Os nós alocado/disponíveis do seu tempo de execução de integração de SSIS do Azure com os Estados de nó específicas (Iniciar/disponíveis/Reciclagem/indisponível) e erros acionáveis. |
| OtherErrors | Erros específicos de nó não acionáveis no seu tempo de execução de integração de SSIS do Azure. |
| LastOperation | O resultado da última operação de início/paragem no seu tempo de execução de integração de SSIS do Azure com erros acionáveis se esta falhou. |
| Estado | O estado geral (inicial/iniciar/iniciar/parar/parado) do seu tempo de execução de integração de SSIS do Azure. |
| Localização | A localização do seu tempo de execução de integração de SSIS do Azure. |
| NodeSize | O tamanho de cada nó do seu tempo de execução de integração de SSIS do Azure. |
| NodeCount | O número de nós no seu tempo de execução de integração de SSIS do Azure. |
| MaxParallelExecutionsPerNode | O número de execuções paralelas por nó no seu tempo de execução de integração de SSIS do Azure. |
| CatalogServerEndpoint | O ponto final do seu servidor de instância de base de dados/gerida do SQL do Azure existente ao anfitrião SSISDB. |
| CatalogAdminUserName | O nome de utilizador de administrador do seu servidor de instância de base de dados/gerida do SQL do Azure existente. Serviço de fábrica de dados utiliza estas informações para preparar e gerir SSISDB em seu nome. |
| CatalogAdminPassword | A palavra-passe do administrador do seu servidor de instância de base de dados/gerida do SQL do Azure existente. |
| CatalogPricingTier | O escalão de preço para SSISDB alojada pelo seu servidor de SQL Database do Azure existente.  Não é aplicável a Azure geridos instância do SQL que aloja SSISDB. |
| VNetId | O rede virtual (VNet) ID de recurso para o tempo de execução de integração do Azure SSIS para associar. |
| Subrede | O nome de sub-rede para o tempo de execução de integração do Azure-SSIS associar. |
| ID | O ID de recurso do seu tempo de execução de integração de SSIS do Azure. |
| Tipo | O tipo (gerida/Self-Hosted) do seu tempo de execução de integração de SSIS do Azure. |
| resourceGroupName | O nome do grupo de recursos do Azure, na qual a fábrica de dados e o tempo de execução de integração de SSIS do Azure foram criados. |
| DataFactoryName | O nome da fábrica de dados do Azure. |
| Nome | O nome do seu tempo de execução de integração de SSIS do Azure. |
| Descrição | A descrição do seu tempo de execução de integração de SSIS do Azure. |

  
### <a name="status-per-node"></a>Estado (por nó)

| Estado | Descrição |
| ------ | ----------- | 
| A Iniciar | Este nó está a ser preparado. |
| Disponível | Este nó está pronto para implementar/executar pacotes SSIS. |
| Reciclagem | Este nó está a ser reparado/reiniciar. |
| Não disponível | Este nó não está pronto para implementar/executar pacotes SSIS e tem acionáveis erros/problemas que lhe foi possível resolver. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Estado (global do Azure-SSIS integração tempo de execução)

| Estado geral | Descrição | 
| -------------- | ----------- | 
| Inicial | Os nós do seu tempo de execução de integração do Azure SSIS não foram atribuídos/preparado. | 
| A Iniciar | Os nós do seu tempo de execução de integração do Azure SSIS estão a ser atribuída/preparado e faturação foi iniciado. |
| Foi iniciada | Os nós do seu tempo de execução de integração do Azure SSIS foram alocados/preparado e estas estão prontas para que possa implementar/executar pacotes SSIS. |
| A Parar  | Os nós do seu tempo de execução de integração do Azure SSIS estão a ser libertados. |
| Parada | Os nós do seu tempo de execução de integração do Azure SSIS foram lançados e faturação foi parado. |

Consulte os artigos seguintes para saber mais sobre o tempo de execução do Azure-SSIS integração:

- [Tempo de execução do Azure-SSIS integração](concepts-integration-runtime.md#azure-ssis-integration-runtime). Este artigo fornece informações concetuais sobre tempos de execução de integração em geral, incluindo o IR. SSIS do Azure 
- [Tutorial: implementar pacotes do SSIS no Azure](tutorial-deploy-ssis-packages-azure.md). Este artigo disponibiliza instruções passo a passo para criar um IR Azure-SSIS e utiliza uma base de dados da Base de Dados SQL do Azure para alojar o catálogo do SSIS. 
- [How to: Create an Azure-SSIS integration runtime](create-azure-ssis-integration-runtime.md) (Como criar um runtime de integração do Azure-SSIS). Este artigo é uma continuação do tutorial e mostra instruções sobre como utilizar a Instância Gerida do SQL do Azure (pré-visualização privada) e associar o IR a uma VNet. 
- [Manage an Azure-SSIS IR](manage-azure-ssis-integration-runtime.md) (Gerir um IR Azure-SSIS). Este artigo mostra-lhe como parar, iniciar ou remover um IR Azure-SSIS. Mostra também como aumentá-lo horizontalmente mediante a adição de mais nós ao mesmo. 
- [Join an Azure-SSIS IR to a VNet](join-azure-ssis-integration-runtime-virtual-network.md) (Associar um IR Azure-SSIS a uma VNet). Este artigo disponibiliza informações concetuais sobre como associar um IR Azure-SSIS a uma rede virtual do Azure (VNet). Também descreve os passos para utilizar o portal do Azure para configurar a VNet, de modo a que o IR Azure-SSIS se possa associar à mesma. 

## <a name="next-steps"></a>Passos seguintes
Consulte os artigos seguintes para monitorizar os pipelines de formas diferentes: 

- [Início rápido: criar uma fábrica de dados](quickstart-create-data-factory-dot-net.md).
- [Utilize o Monitor do Azure para monitorizar os pipelines de fábrica de dados](monitor-using-azure-monitor.md)