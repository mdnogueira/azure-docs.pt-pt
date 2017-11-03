---
title: "Criar uma solução de gestão no OMS | Microsoft Docs"
description: "Soluções de gestão de expandam a funcionalidade do Operations Management Suite (OMS), fornecendo os cenários de gestão em pacote que os clientes podem adicionar a sua área de trabalho do OMS.  Este artigo fornece detalhes sobre como pode criar soluções de gestão a ser utilizado no seu próprio ambiente ou disponibilizada aos seus clientes."
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.assetid: 1915e204-ba7e-431b-9718-9eb6b4213ad8
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/20/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6ea959f1e95ea46c07eec3afa9d46bfeb72ca3e4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="design-and-build-a-management-solution-in-operations-management-suite-oms-preview"></a>Conceber e criar uma solução de gestão no Operations Management Suite (OMS) (pré-visualização)
> [!NOTE]
> Esta documentação é de preliminar para criar soluções de gestão no OMS que estão atualmente em pré-visualização. Nenhum esquema descrita abaixo está sujeita a alterações.

[As soluções de gestão](operations-management-suite-solutions.md) expandir a funcionalidade do Operations Management Suite (OMS), fornecendo os cenários de gestão em pacote que os clientes podem adicionar a sua área de trabalho do OMS.  Este artigo apresenta um processo básico para conceber e criar uma solução de gestão que é adequada para os requisitos mais comuns.  Se estiver a criar soluções de gestão, em seguida, pode utilizar este processo como um ponto de partida e, em seguida, tirar partido os conceitos para soluções mais complexas, como os requisitos de evoluem.

## <a name="what-is-a-management-solution"></a>O que é uma solução de gestão?

As soluções de gestão contém OMS e de recursos do Azure que funcionam em conjunto para atingir um determinado cenário de monitorização.  Estão implementados como [modelos de gestão de recursos](../azure-resource-manager/resource-manager-template-walkthrough.md) que contém detalhes sobre como instalar e configurar os recursos contidos quando a solução é instalada.

A estratégia básica é para iniciar a sua solução de gestão ao criar os componentes individuais no seu ambiente do Azure.  Depois de ter a funcionalidade a funcionar corretamente, em seguida, pode iniciar empacotamento-los para um [ficheiro de solução de gestão](operations-management-suite-solutions-solution-file.md). 


## <a name="design-your-solution"></a>Conceba a sua solução
O padrão mais comuns para uma solução de gestão é mostrado no diagrama seguinte.  Neste padrão, os diferentes componentes são abordados no abaixo.

![Descrição geral da solução do OMS](media/operations-management-suite-solutions/solution-overview.png)


### <a name="data-sources"></a>Origens de dados
O primeiro passo para conceber uma solução consiste na determinação os dados que requerem o repositório de análise de registos.  Estes dados poderão ser recolhidos por um [origem de dados](../log-analytics/log-analytics-data-sources.md) ou [outra solução](operations-management-suite-solutions.md), ou a sua solução poderá ter de fornecer o processo de recolha-lo.

Existem várias formas origens de dados que podem ser recolhidas no repositório de análise de registos, conforme descrito em [origens de dados de análise de registos](../log-analytics/log-analytics-data-sources.md).  Isto inclui eventos no registo de eventos do Windows ou geradas pelo Syslog para além dos contadores de desempenho para clientes Windows e Linux.  Também pode recolher dados a partir dos recursos do Azure recolhidos pelo Monitor do Azure.  

Se necessitar de dados que não está acessíveis através de qualquer uma das origens de dados disponíveis, em seguida, pode utilizar o [API de Recoletor de dados de HTTP](../log-analytics/log-analytics-data-collector-api.md) que permite-lhe escrever dados para o repositório de análise de registos a partir de qualquer cliente que possa chamar uma API REST.  O meio mais comuns de recolha de dados personalizada numa solução de gestão é criar um [runbook na automatização do Azure](../automation/automation-runbook-types.md) que recolhe os dados necessários a partir dos recursos do Azure ou externos e utiliza a API de Recoletor de dados para escrever o repositório.  

### <a name="log-searches"></a>Pesquisas de registo
[Inicie sessão pesquisas](../log-analytics/log-analytics-log-searches.md) são utilizados para extrair e analisar dados no repositório de análise de registos.  São utilizados pelas vistas e alertas para além de permitir que o utilizador Efetue uma análise de dados em ad hoc no repositório.  

Deve definir todas as consultas que pensa que irá ser úteis para o utilizador, mesmo que não são utilizados por quaisquer vistas ou os alertas.  Estes estarão disponíveis aos mesmos como pesquisas guardadas no portal e também pode incluir os mesmos num [parte de visualização da lista de consultas](../log-analytics/log-analytics-view-designer-parts.md#list-of-queries-part) na vista personalizada.

### <a name="alerts"></a>Alertas
[Alertas na análise de registos](../log-analytics/log-analytics-alerts.md) identificar problemas através de [pesquisas de registo](#log-searches) contra os dados no repositório.  Estes notificar o utilizador ou que execute automaticamente uma ação na resposta. Deverá identificar condições diferentes de alerta para a sua aplicação e incluir regras de alerta correspondentes no ficheiro de solução.

Se o problema, potencialmente, pode ser corrigido com um processo automatizado, normalmente, irá criar um runbook na automatização do Azure para efetuar esta remediação.  Serviços Azure mais podem ser geridos com [cmdlets](/powershell/azure/overview) que o runbook seria aproveitar para fazer essa funcionalidade.

Se a solução requer funcionalidade externa em resposta a um alerta, em seguida, pode utilizar um [webhook resposta](../log-analytics/log-analytics-alerts-actions.md).  Isto permite-lhe chamar um serviço web externo, enviando informações do alerta.

### <a name="views"></a>Vistas
As vistas na análise de registos são utilizadas para visualizar dados do repositório de análise de registos.  Cada solução, normalmente, irá conter uma única vista com uma [mosaico](../log-analytics/log-analytics-view-designer-tiles.md) que é apresentado no dashboard principal do utilizador.  A vista pode conter qualquer número de [partes de visualização](../log-analytics/log-analytics-view-designer-parts.md) para fornecer visualizações diferentes dos dados recolhidos para o utilizador.

[Criar vistas personalizadas utilizando o estruturador de vistas](../log-analytics/log-analytics-view-designer.md) que mais tarde pode exportar para inclusão no seu ficheiro de solução.  


## <a name="create-solution-file"></a>Criar ficheiro de solução
Depois de ter configurado e testado os componentes que irão fazer parte da sua solução, pode [crie o seu ficheiro de solução](operations-management-suite-solutions-solution-file.md).  Irá implementar os componentes da solução de um [modelo do Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) que inclua um [recursos solução](operations-management-suite-solutions-solution-file.md#solution-resource) com relações com outros recursos no ficheiro.  


## <a name="test-your-solution"></a>Testar a sua solução
Enquanto estiver a desenvolver a solução, terá de instalar e testá-lo na sua área de trabalho.  Isto pode ser feito utilizando qualquer um dos métodos disponíveis para [testar e instalar os modelos do Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="publish-your-solution"></a>Publicar a sua solução
Assim que concluir e testado a sua solução, pode tornar disponível para clientes através de qualquer uma das seguintes origens.

- **Modelos de início rápido do Azure**.  [Modelos de início rápido do Azure](https://azure.microsoft.com/resources/templates/) é um conjunto de modelos do Resource Manager contribuído pela Comunidade através do GitHub.  É possível disponibilizar solução pelos seguintes informações no [guia contribuição](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE).
- **Azure Marketplace**.  O [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/) permite-lhe distribuir e propor a solução para outros programadores, ISVs e profissionais de TI.  Pode saber como publicar a sua solução no Azure Marketplace em [como publicar e gerir uma oferta no Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).



## <a name="next-steps"></a>Passos seguintes
* Saiba como [criar um ficheiro de solução](operations-management-suite-solutions-solution-file.md) para a sua solução de gestão.
* Saiba os detalhes de [modelos Authoring Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).
* Pesquisa [modelos de início rápido do Azure](https://azure.microsoft.com/documentation/templates) exemplos de modelos de Gestor de recursos diferentes.