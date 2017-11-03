---
title: "Nuvem Cruiser e a integração de API de faturação do Microsoft Azure | Microsoft Docs"
description: "Fornece uma perspetiva exclusiva a partir do Microsoft Azure Billing parceiro Cruiser de nuvem, nas suas experiências integrar APIs de faturação do Azure para os seus produtos.  Isto é especialmente útil para os clientes do Azure e nuvem Cruiser que estão interessados nas utilizando/tentar Cruiser da nuvem para o Microsoft Azure Pack."
services: 
documentationcenter: 
author: BryanLa
manager: tonguyen
editor: 
tags: billing
ms.assetid: b65128cf-5d4d-4cbd-b81e-d3dceab44271
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/09/2017
ms.author: mobandyo;sirishap;bryanla
ms.openlocfilehash: 7d66cac98afa72c807f597403b1e2bd278e45cec
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="cloud-cruiser-and-microsoft-azure-billing-api-integration"></a>Nuvem Cruiser e a integração de API de faturação do Microsoft Azure
Este artigo descreve como as informações recolhidas a partir do novo Microsoft Azure Billing APIs podem ser utilizadas na nuvem Cruiser para simulação de custo de fluxo de trabalho e de análise.

## <a name="azure-ratecard-api"></a>API de RateCard do Azure
A API de RateCard fornece informações de velocidade do Azure. Após a autenticação com as credenciais corretas, pode consultar a API para recolher os metadados sobre os serviços disponíveis no Azure, juntamente com as taxas associadas a sua oferta ID.

A resposta de exemplo seguinte é a partir da API de mostrar os preços para o A0 instância (Windows):

    {
        "MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",
        "MeterName": "Compute Hours",
        "MeterCategory": "Virtual Machines",
        "MeterSubCategory": "A0 VM (Windows)",
        "Unit": "Hours",
        "MeterRates":
        {
            "0": 0.029
        },
        "EffectiveDate": "2014-08-01T00:00:00Z",
        "IncludedQuantity": 0.0,
        "MeterStatus": "Active"
    },

### <a name="cloud-cruisers-interface-to-azure-ratecard-api"></a>Na nuvem Interface do Cruiser para Azure RateCard API
Nuvem Cruiser pode utilizar as informações de RateCard API formas diferentes. Para este artigo, mostramos como podem ser utilizada para tornar o IaaS simulação e análise de custos de carga de trabalho.

Para demonstrar a este caso de utilização, imagine uma carga de trabalho de várias instâncias em execução no pacote do Microsoft Azure (WAP). O objetivo é para simular esta mesma carga de trabalho no Azure e estimar os custos de fazer tal migração. Para criar este simulação, existem duas tarefas principais para ser executada:

1. **Importar e processar as informações de serviço recolhidas a partir da API de RateCard.** Esta tarefa também é executada nos livros, onde o extrair a partir da API de RateCard é transformado e publicado um novo plano de taxa. Este novo plano de taxa é utilizado nas simulações para estimar os preços do Azure.
2. **Normaliza serviços WAP e serviços do Azure para o IaaS.** Por predefinição, os serviços WAP baseiam-se nos recursos individuais (CPU, memória de tamanho, tamanho do disco, etc.) enquanto o Azure services são baseados no tamanho de instância (A0 A1, A2, etc.). Esta primeira tarefa pode ser efetuada pelo motor ETL da nuvem Cruiser, denominado livros, onde podem ser incluídos estes recursos em tamanhos de instância, análogo aos serviços de instância do Azure.

### <a name="import-data-from-the-ratecard-api"></a>Importar dados a partir da API de RateCard
Nuvem Cruiser livros proporcionam uma forma automatizada para recolher e processar as informações a partir da API de RateCard.  Os livros do ETL (extração transformação-carga) permitem-lhe configurar a recolha, transformação e a publicação de dados na base de dados Cruiser de nuvem.

Cada livro pode ter uma ou várias coleções, permitindo-lhe correlacionar informações a partir de diferentes origens para complementar ou aumentar os dados de utilização. As capturas de ecrã de dois seguintes mostram como criar um novo *coleção* um livro existente e importar informações para o *coleção* partir da API de RateCard:

![Figura 1 - criar uma nova coleção][1]

![Figura 2 - importar dados da nova coleção][2]

Depois de importar os dados para o livro, é possível criar vários passos e processos de transformação, modificar e os dados de modelo. Para este exemplo, uma vez que vamos só esteja interessados em infraestrutura-como-um-serviço (IaaS), podemos utilizar os passos de transformação para remover linhas desnecessárias ou registos, relacionados com os serviços que não seja o IaaS.

A seguinte captura de ecrã mostra os passos de transformação utilizados para processar os dados recolhidos a partir da API RateCard:

![Figura 3 – passos de transformação para processar os dados recolhidos a partir da API RateCard][3]

### <a name="defining-new-services-and-rate-plans"></a>Definir novos serviços e a taxa de planos
Existem várias formas para definir os serviços em nuvem Cruiser. Uma das opções é importar os serviços de dados de utilização. Este método é usado normalmente quando trabalhar com nuvens públicas, onde os serviços já são definidos pelo fornecedor.

Planear uma taxa é um conjunto de taxas ou os preços que podem ser aplicados a diferentes serviços, com base em datas efetivas, ou grupo de clientes, entre outras opções. Também podem ser utilizados na nuvem Cruiser planos de velocidade para criar simulação ou cenários "What-if", para compreender a forma como as alterações nos serviços podem afetar o custo total de uma carga de trabalho.

Neste exemplo, podemos utilizar as informações de serviço a partir da API de RateCard para definir novos serviços em nuvem Cruiser. Da mesma forma, podemos utilizar as taxas associadas aos serviços para criar um novo plano de taxa Cruiser de nuvem.

No final do processo de transformação, é possível criar um novo passo e publicar os dados a partir da API de RateCard como novos serviços e taxas.

![Figura 4 - publicar os dados a partir da API de RateCard como novos serviços e as taxas de][4]

### <a name="verify-azure-services-and-rates"></a>Certifique-se e taxas de serviços do Azure
Depois de publicar as taxas e serviços, pode verificar a lista de serviços importados da nuvem Cruiser *serviços* separador:

![Figura 5 - verificar os novos serviços][5]

No *taxa planos* separador, pode verificar o novo plano de taxa chamado "AzureSimulation" com as taxas de importados a partir da API de RateCard.

![Figura 6 - verificar o novo plano de taxa e as taxas associadas][6]

### <a name="normalize-wap-and-azure-services"></a>Normaliza WAP e serviços do Azure
Por predefinição, o WAP fornece informações de utilização com base na utilização de computação, memória e recursos de rede. Na nuvem Cruiser, pode definir os serviços com base diretamente na alocação ou utilização limitada destes recursos. Por exemplo, pode definir uma taxa básica para cada hora de utilização da CPU ou cobram GB de memória atribuída a uma instância.

Neste exemplo, para comparar os custos entre WAP e o Azure, é necessário agregar a utilização de recursos no WAP para pacotes, em seguida, podem ser mapeadas para serviços do Azure. Esta transformação pode ser implementada facilmente nos livros:

![A figura 7 - transformar dados WAP para normalizar os serviços][7]

É o último passo no livro para publicar os dados na base de dados Cruiser de nuvem. Durante este passo, os dados de utilização é agora incluídos para os serviços (que mapeiam para os serviços do Azure) e associados a velocidades máximas de predefinido para criar os encargos.

Depois de concluir o livro, pode automatizar o processamento dos dados, a adição de uma tarefa no programador e especificar a frequência e tempo para o livro ser executada.

![Figura 8 - agendamento de livro][8]

### <a name="create-reports-for-workload-cost-simulation-analysis"></a>Criar relatórios para análise de simulação de custo de carga de trabalho
Depois da utilização é recolhida e os encargos são carregados para a base de dados de nuvem Cruiser, podemos utilizar o módulo de nuvem Cruiser Insights para criar a carga de trabalho simulação que iremos pretendidos ao nível de custos.

Para demonstrar a este cenário, criámos o relatório seguinte:

![Custo de comparação][9]

O gráfico superior mostra uma comparação de custo pelos serviços, comparar o preço de executar a carga de trabalho para cada serviço específico entre o WAP (blue escuro) e o Azure (leve azul).

O gráfico de inferior mostra os mesmos dados, mas discriminado por departamento. Os custos para cada departamento executar as respetivas cargas de trabalho no WAP e o Azure, juntamente com a diferença entre eles é apresentado na barra de reduções (verde).

## <a name="azure-usage-api"></a>API de utilização do Azure
### <a name="introduction"></a>Introdução
Recentemente, a Microsoft introduziu a API de utilização do Azure, permitindo que os subscritores através de programação solicita dados de utilização para obter informações acerca da respetiva consumo. Os clientes de Cruiser nuvem podem tirar partido do conjunto mais rico de dados disponível através desta API.

Nuvem Cruiser pode utilizar a integração com a API de utilização de várias formas. A granularidade (hora a hora informações de utilização) e informações de metadados de recursos disponíveis através da API fornecem o conjunto de dados necessário para suportar modelos de análise de custos ou encargos flexíveis. 

Neste tutorial, iremos apresentar um exemplo de como nuvem Cruiser pode beneficiar das informações de API de utilização. Mais especificamente, iremos irá criar um grupo de recursos no Azure, associar etiquetas para a estrutura de conta e descrevem o processo de extrair e processar as informações de tag para Cruiser de nuvem.

O objetivo final é para poder criar relatórios como o seguinte e poderá analisar o custo e o consumo de com base na estrutura de conta preenchida pelo etiquetas.

![A figura 10 - relatório com breakdowns etiquetas a utilizar][10]

### <a name="microsoft-azure-tags"></a>Etiquetas do Microsoft Azure
Os dados disponíveis através da API de utilização do Azure incluem não apenas informações de consumo, mas também os metadados de recursos, incluindo quaisquer etiquetas associadas. As etiquetas proporcionam uma forma fácil para organizar os recursos, mas para poder estar em vigor, tem de garantir que:

* As etiquetas são aplicadas corretamente aos recursos no momento de aprovisionar
* As etiquetas corretamente são utilizadas no processo de análise de custos/estorno associar a utilização de estrutura de conta da organização.

Ambos estes requisitos podem ser um desafio, especialmente quando existe um processo manual de aprovisionar ou charging lado. As etiquetas de minimizados,, mesmo em falta ou incorretas são queixas comuns de clientes, quando utilizar etiquetas e estes erros pode tornar vida no lado charging difícil.

Com a nova API de utilização do Azure, nuvem Cruiser pode solicitar informações de etiquetagem de recursos e através de uma ferramenta ETL sofisticada chamada livros, corrija estes erros de etiquetagem comuns. Através de transformação utilizando expressões regulares e da correlação de dados, nuvem Cruiser pode identificar recursos incorretamente marcados e aplicar etiquetas corretas, garantindo a associação correta dos recursos com o consumidor.

No lado charging, nuvem Cruiser automatiza o processo de análise de custos/estorno e pode utilizar as informações de tag associar a utilização para o consumidor adequado (departamento, divisão, projeto, etc.). Esta automatização fornece uma enorme melhoria e pode certificar-se de um processo charging auditável e consistente.

### <a name="creating-a-resource-group-with-tags-on-microsoft-azure"></a>Criar um grupo de recursos com etiquetas no Microsoft Azure
Neste tutorial, o primeiro passo é criar um grupo de recursos no portal do Azure, em seguida, criar novas etiquetas a associar aos recursos. Neste exemplo, vamos criar as seguintes tags: proprietário de departamento, ambiente, o departamento, projeto.

A seguinte captura de ecrã mostra um exemplo de grupo de recursos com as etiquetas associadas.

![Figura 11 - grupo de recursos com etiquetas associadas no portal do Azure][11]

O passo seguinte é puxar as informações a partir da API de utilização para Cruiser de nuvem. A API de utilização atualmente fornece respostas no formato JSON. Eis um exemplo dos dados obtidos:

    {
      "id": "/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXX/providers/Microsoft.Commerce/UsageAggregates/Daily_BRSDT_20150623_0000",
      "name": "Daily_BRSDT_20150623_0000",
      "type": "Microsoft.Commerce/UsageAggregate",
      "properties":
      {
        "subscriptionId": "bb678b04-0e48-4b44-XXXX-XXXXXXXXX",
        "usageStartTime": "2015-06-22T00:00:00+00:00",
        "usageEndTime": "2015-06-23T00:00:00+00:00",
        "meterName": "Compute Hours",
        "meterRegion": "",
        "meterCategory": "Virtual Machines",
        "meterSubCategory": "Standard_D1 VM (Non-Windows)",
        "unit": "Hours",
        "instanceData": "{\"Microsoft.Resources\":{\"resourceUri\":\"/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXXX/resourceGroups/DEMOUSAGEAPI/providers/Microsoft.Compute/virtualMachines/MyDockerVM\",\"location\":\"eastus\",\"tags\":{\"Department\":\"Sales\",\"Project\":\"Demo Usage API\",\"Environment\":\"Test\",\"Owner\":\"RSE\"},\"additionalInfo\":{\"ImageType\":\"Canonical\",\"ServiceType\":\"Standard_D1\"}}}",
        "meterId": "e60caf26-9ba0-413d-a422-6141f58081d6",
        "infoFields": {},
        "quantity": 8

      },
    },


### <a name="import-data-from-the-usage-api-into-cloud-cruiser"></a>Importar dados a partir da API de utilização para Cruiser de nuvem
Nuvem Cruiser livros proporcionam uma forma automatizada para recolher e processar as informações a partir da API de utilização. Um livro do ETL (extração transformação-carga) permite-lhe configurar a recolha, transformação e a publicação de dados na base de dados Cruiser de nuvem.

Cada livro pode ter uma ou várias coleções. Isto permite-lhe correlacionar informações a partir de diferentes origens para complementar ou aumentar os dados de utilização. Neste exemplo, vamos criar uma nova folha no livro do modelo do Azure (*UsageAPI)* e defina um novo *coleção* para importar as informações a partir da API de utilização.

![Figura 3 - os dados de utilização API importados para a folha de UsageAPI][12]

Tenha em atenção que este livro já tem outras folhas para importar os serviços do Azure (*ImportServices*) e processar as informações de consumo da API de faturação (*PublishData*).

Em seguida, vamos utilizar a API de utilização para preencher o *UsageAPI* folha e correlacionar as informações com os dados de consumo da API de faturação no *PublishData* folha.

### <a name="processing-the-tag-information-from-the-usage-api"></a>Processar as informações de etiqueta a partir da API de utilização
Depois de importar os dados para o livro, criamos passos transformação o *UsageAPI* folha para processar a informação a partir da API. Primeiro passo consiste em utilizar um processador de "Divide JSON" para extrair as etiquetas de um único campo de, em seguida, criar campos para cada um deles (departamento, projeto, proprietário e ambiente).

![Figura 4 - criar novos campos para as informações de etiqueta][13]

Tenha em atenção o serviço de "Redes" está em falta as informações de etiqueta (caixa amarela), mas iremos pode Certifique-se de que faz parte do mesmo grupo de recursos ao observar o *ResourceGroupName* campo. Uma vez que temos as etiquetas para os outros recursos deste grupo de recursos, pode estas informações são utilizadas para aplicar as etiquetas em falta para este recurso posteriormente no processo.

O passo seguinte consiste em criar uma tabela de pesquisa associar as informações de etiquetas para o *ResourceGroupName*. Esta tabela de pesquisa é utilizada no passo seguinte para enriquecer a dados de consumo com informações da etiqueta.

### <a name="adding-the-tag-information-to-the-consumption-data"></a>Adicionar as informações de tag aos dados de consumo
Agora, pode ir para o *PublishData* folha, que processa as informações de consumo da API de faturação e adicione os campos extraídos de etiquetas. Este processo é efetuado observando a tabela de pesquisa criada no passo anterior, utilizando o *ResourceGroupName* como a chave para as pesquisas de com.

![Figura 5 - preencher a estrutura de conta com a informação a partir de pesquisas][14]

Tenha em atenção que os campos de estrutura de conta apropriada para o serviço de "Redes" foram aplicados, corrigir o problema com as etiquetas em falta. Podemos também preenchido os campos de estrutura de conta para os recursos que não seja nosso grupo de recursos de destino com "Outros", para diferenciar os relatórios.

Agora precisamos apenas de adicionar um passo para publicar os dados de utilização. Durante este passo, as taxas de adequadas para cada serviço definido no nosso planear de taxa ser aplicadas para as informações de utilização, com a taxa resultante carregada para a base de dados.

A parte melhor é que só tem de passar por este processo, uma vez. Quando o livro estiver concluído, basta adicioná-lo para o programador e é executada hora a hora ou diariamente à hora agendada. Em seguida, é apenas um fim de criação de novos relatórios ou personalizar existentes, para analisar os dados para obter informações significativas da utilização de nuvem.

### <a name="next-steps"></a>Passos Seguintes
* Para obter instruções detalhadas sobre a criação de relatórios e livros Cruiser de nuvem, consulte da nuvem Cruiser online [documentação](http://docs.cloudcruiser.com/) (início de sessão válido necessário).  Para obter mais informações sobre a nuvem Cruiser, contacte [ info@cloudcruiser.com ](mailto:info@cloudcruiser.com).
* Consulte [obter informações acerca do consumo de recursos do Microsoft Azure](billing-usage-rate-card-overview.md) para uma descrição geral da utilização de recursos do Azure e RateCard APIs.
* Veja o [referência de API de REST de faturação do Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) para obter mais informações sobre as APIs, que fazem parte do conjunto de APIs fornecidas pelo Azure Resource Manager.
* Se gostaria de direito aprofundar o código de exemplo, consulte a nossa Microsoft Azure Billing API exemplos de código no [exemplos de código do Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

### <a name="learn-more"></a>Saiba Mais
* Para saber mais sobre o Azure Resource Manager, consulte o [descrição geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) artigo.

<!--Image references-->

[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "Figura 1 - criar uma nova coleção"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "Figura 2 - importar dados da nova coleção"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "Figura 3 – passos de transformação para processar os dados recolhidos a partir da API RateCard"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "Figura 4 - publicar os dados a partir da API de RateCard como novos serviços e as taxas de"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "Figura 5 - verificar os novos serviços"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "Figura 6 - verificar o novo plano de taxa e as taxas associadas"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "A figura 7 - transformar dados WAP para normalizar os serviços"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "Figura 8 - agendamento de livro"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "Figura 9 - relatório de exemplo para o cenário de comparação de custo de carga de trabalho"
[10]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/1_ReportWithTags.png "A figura 10 - relatório com breakdowns etiquetas a utilizar"
[11]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/2_ResourceGroupsWithTags.png "Figura 11 - grupo de recursos com etiquetas associadas no portal do Azure"
[12]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/3_ImportIntoUsageAPISheet.png "Figura 12 - os dados de utilização API importados para a folha de UsageAPI"
[13]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/4_NewTagField.png "Figura 13 - criar novos campos para as informações de etiqueta"
[14]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/5_PopulateAccountStructure.png "Figura 14 - preencher a estrutura de conta com a informação a partir de pesquisas"
