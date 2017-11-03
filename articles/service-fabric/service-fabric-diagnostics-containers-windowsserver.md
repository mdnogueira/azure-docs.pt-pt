---
title: "Contentores de recursos de infraestrutura de serviço do Azure de monitorização e diagnóstico | Microsoft Docs"
description: "Saiba como monitorizar e diagnosticar contentores orquestradas no Microsoft Azure Service Fabric com a solução de contentores do OMS."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/15/2017
ms.author: dekapur
ms.openlocfilehash: 319ee2c0f7492389bc1767aa2669dd273f8cfa1b
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="monitoring-windows-server-containers-with-oms"></a>Monitorização de contentores do Windows Server com o OMS

## <a name="oms-containers-solution"></a>Solução de contentores do OMS

Análise de registos do Operations Management Suite (OMS) tem uma solução de contentores que pode ser utilizada para monitorização contentores. Juntamente com a solução de Service Fabric, esta solução é uma ótima ferramenta para monitorizar implementações de contentores orquestradas no Service Fabric. Eis um exemplo simples de aspeto do dashboard de solução:

![Dashboard do OMS básico](./media/service-fabric-diagnostics-containers-windowsserver/oms-containers-dashboard.png)

Recolhe também diferentes tipos de registos que podem ser consultados na ferramenta de análise de registos do OMS e podem ser utilizados para visualizar as métricas ou eventos que está a ser gerados. Os tipos de registo que são recolhidos são:

1. ContainerInventory: mostra informações sobre imagens, o nome e localização do contentor
2. ContainerImageInventory: informações sobre imagens de implementada, incluindo os IDs ou tamanhos
3. ContainerLog: registos de erro específico, registos de docker (stdout, etc.) e outras entradas
4. ContainerServiceLog: comandos de daemon de docker que foram executados
5. Desempenho: os contadores de desempenho incluindo contentor cpu, memória, o tráfego de rede do disco e/s e métricas personalizadas as máquinas de anfitrião

Este artigo aborda os passos necessários para configurar a monitorização do contentor para o cluster. Para saber mais sobre a solução de contentores do OMS, consulte os respetivos [documentação](../log-analytics/log-analytics-containers.md).

## <a name="1-set-up-a-service-fabric-cluster"></a>1. Configurar um cluster do Service Fabric

Criar um cluster utilizando o modelo Azure Resource Manager encontrado [aqui](https://github.com/ChackDan/Service-Fabric/tree/master/ARM%20Templates/SF%20OMS%20Samples/Windows). Certifique-se de que adiciona um nome exclusivo de área de trabalho OMS. Este modelo também será assumida a implementar um cluster na compilação pré-visualização do Service Fabric (v255.255), que significa que não pode ser utilizado em produção e não pode ser atualizado para uma versão diferente do Service Fabric. Se optar por utilizar este modelo de longa duração ou de produção, utilize, altere a versão para um número de versão estável.

Depois de configurar o cluster, certifique-se de que instalou o certificado adequado e certifique-se conseguir ligar ao cluster.

Confirme que o grupo de recursos está definido corretamente pelo cabeçalho para o [portal do Azure](https://portal.azure.com/) e localizando a implementação. O grupo de recursos deve conter todos os recursos de Service Fabric e também de ter uma solução de análise de registos, bem como uma solução de Service Fabric.

Para modificar um cluster de Service Fabric existente:
* Confirme que o diagnóstico está ativado (se não, ativá-la através de [atualizar o conjunto de dimensionamento de máquina virtual](/rest/api/virtualmachinescalesets/create-or-update-a-set))
* Adicionar uma área de trabalho do OMS através da criação de uma solução de "Serviço análise de recursos de infraestrutura" através do Azure Marketplace
* Editar as origens de dados da solução de Service Fabric para recolher dados das tabelas de armazenamento do Azure adequadas (configuradas por WAD) no grupo de recursos que o cluster está a ser
* Adicionar o agente como um [extensão para o conjunto de dimensionamento de máquina virtual](/powershell/module/azurerm.compute/add-azurermvmssextension) através do PowerShell ou através de atualizar a máquina virtual do conjunto de dimensionamento (ligação mesma conforme apresentado acima, para modificar o modelo do Resource Manager)

## <a name="2-deploy-a-container"></a>2. Implementar um contentor

Depois do cluster está preparado e tiver confirmado que consegue aceder-lhe, implemente um contentor no mesmo. Se optar por utilizar a versão de pré-visualização conforme definido pelo modelo, também pode explorar docker novo do Service Fabric compor funcionalidade. Tenha em consideração que pela primeira vez que uma imagem do contentor é implementada para um cluster demora vários minutos a transferência da imagem, dependendo do seu tamanho.

## <a name="3-add-the-containers-solution"></a>3. Adicionar a solução de contentores

No portal do Azure, crie um recurso de contentores (sob a monitorização + gestão categoria) através do Azure Marketplace. 

![Adicionar a solução de contentores](./media/service-fabric-diagnostics-containers-windowsserver/containers-solution.png)

O passo de criação, pede uma área de trabalho do OMS. Selecione a que foi criado com a implementação acima. Este passo adiciona uma solução de contentores na sua área de trabalho do OMS e é detetado automaticamente pelo agente OMS implementado pelo modelo. O agente irá iniciar a recolha de dados em processos de contentores no cluster e, em cerca de 10 a 15 minutos, deve ver claro solução cópias de segurança com dados como a imagem do dashboard acima.

## <a name="4-next-steps"></a>4. Passos seguintes

OMS oferece várias ferramentas na área de trabalho para Certifique-se mais úteis para si. Explore as opções seguintes para personalizar a solução para as suas necessidades:
- Obter familiarized com o [de registo de pesquisa e consultar](../log-analytics/log-analytics-log-searches.md) funcionalidades disponibilizadas como parte da análise de registos
- Configurar o agente do OMS para recolher contadores de desempenho específicos (vá para a home page de área de trabalho > Definições > dados > contadores de desempenho do Windows)
- Configurar OMS configurar [automatizada alertas](../log-analytics/log-analytics-alerts.md) regras para ajudar a detetar e diagnóstico