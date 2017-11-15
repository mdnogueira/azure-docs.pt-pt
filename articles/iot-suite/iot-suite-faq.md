---
title: FAQ do Azure IoT Suite | Microsoft Docs
description: Perguntas mais frequentes sobre o IoT Suite
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: cb537749-a8a1-4e53-b3bf-f1b64a38188a
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/10/2017
ms.author: dobett
ms.openlocfilehash: aa8a053e6b3b6fcf6e026f2fd65942a943e12a08
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2017
---
# <a name="frequently-asked-questions-for-iot-suite"></a>Perguntas mais frequentes sobre o IoT Suite

Além disso, consulte ligado específicos de fábrica [FAQ](iot-suite-faq-cf.md).

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>Onde posso encontrar o código de origem para as soluções pré-configuradas?

O código de origem é armazenado no repositórios do GitHub seguintes:

* [(.NET) de solução pré-configurada de monitorização remota](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet)
* [(Java) de solução pré-configurada de monitorização remota](https://github.com/Azure/azure-iot-pcs-remote-monitoring-java)
* [Solução pré-configurada de manutenção preditiva](https://github.com/Azure/azure-iot-predictive-maintenance)
* [Solução de fábrica ligado pré-configurada](https://github.com/Azure/azure-iot-connected-factory)

### <a name="how-much-does-it-cost-to-provision-the-new-remote-monitoring-solution"></a>Quanto-lo de custos para aprovisionar a solução de monitorização remota novo?

A nova solução pré-configurada oferece duas opções de implementação:

* A *básico* opção concebida para programadores à procura de menor custo de desenvolvimento ou clientes pretende para compilar uma demonstração ou prova de conceito.
* A *padrão* opção concebida para as empresas que pretenda implementar uma infraestrutura pronta para produção.

### <a name="how-can-i-ensure-i-keep-my-costs-down-while-i-develop-my-solution"></a>Como garantir que posso mantenha os meus os custos reduzidos enquanto desenvolva a minha solução?

Além de fornecer duas implementações diferenciadas, a nova solução de monitorização remota tem uma definição para ativar ou desativar a todos os dispositivos simulados a pedido. Desativar a simulação reduz os dados ingeridos na solução e, assim, o custo global.

### <a name="is-the-new-microservices-architecture-available-for-all-the-three-preconfigured-solutions"></a>Nova arquitetura do micro-serviços está disponível para todas as soluções pré-configuradas três?

Atualmente, apenas a solução de monitorização remota utiliza a arquitetura de micro-serviços como abrange o cenário mais amplas.

### <a name="what-advantages-does-the-new-open-sourced-microservices-based-architecture-provide-in-the-new-update"></a>As vantagens que o novo tenham origem open micro-serviços arquitetura baseada em fornecem na nova atualização?

Ao longo dos últimos anos dois, arquitetura na cloud, evoluiu e deu significativamente tem lugar. Serviços micro tem emerged como um excelente padrão para alcançar a escala e a flexibilidade, sem sacrificar a velocidade de desenvolvimento. Neste padrão de arquitetura é utilizado em vários serviços do Microsoft internamente com resultados de escalabilidade e fiabilidade excelente. Estamos a finalizar estes learning na prática, para que os nossos clientes tirar partido dos mesmos.

### <a name="is-the-new-preconfigured-solution-available-in-the-same-geographic-region-as-the-existing-solution"></a>A nova solução pré-configurada está disponível na mesma região geográfica que a solução existente?

Sim, nova monitorização remota está disponível as mesmas regiões geográficas.

### <a name="what-is-the-difference-between-the-basic-and-standard-deployment-options-how-do-i-decide-between-the-two-deployment-options"></a>O que é a diferença entre as opções de implementação básico e padrão? Como decidir entre as opções de implementação de dois?

Cada opção de implementação responde às necessidades diferentes. A implementação básica é concebida para começar a desenvolver PoC e pilotos pequenos. Fornece uma arquitetura simplificada com os recursos necessários mínimos e um custo mais baixo. A implementação padrão foi concebida para criar e personalizar uma solução pronta para produção e fornece uma implementação com tenha em atenção que os elementos necessários. Para a fiabilidade e o dimensionamento, micro-serviços de aplicação são criados como contentores de Docker e implementadas utilizando um orchestrator (Kubernetes por predefinição). O orchestrator é responsável pela implementação, dimensionamento e gestão da aplicação. Deverá escolher uma opção com base nas necessidades atuais. Poderá utilizar um, o outro ou uma combinação de ambos, dependendo da fase de projeto.

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-suite"></a>Pode continuar a tirar partido do meu investimentos existentes no Azure IoT Suite?

Sim. Qualquer solução de que existe atualmente continua a trabalhar na sua subscrição do Azure e o código de origem permanece disponível no GitHub.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Qual a diferença entre a eliminação de um grupo de recursos no Portal do Azure e clicar Eliminar numa solução pré-configurada em azureiotsuite.com?

* Se eliminar a solução pré-configurada em [azureiotsuite.com](https://www.azureiotsuite.com/), estará a eliminar todos os recursos que aprovisionou aquando da criação da solução pré-configurada. Se adicionou recursos adicionais ao grupo de recursos, estes recursos também serão eliminados.
* Se eliminar o grupo de recursos no [portal do Azure](https://portal.azure.com), apenas estará a eliminar os recursos nesse grupo de recursos. Também terá de eliminar a aplicação do Azure Active Directory associada à solução pré-configurada.

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias do Hub IoT é possível aprovisionar numa subscrição?

Por predefinição pode aprovisionar [10 hubs IoT por subscrição](../azure-subscription-service-limits.md#iot-hub-limits). Pode criar um [pedido de suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar este limite. Como resultado, uma vez que cada solução pré-configurada aprovisiona um IoT Hub novo, apenas pode aprovisionar até 10 soluções pré-configuradas numa determinada subscrição.

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias de base de dados do Azure Cosmos possível aprovisionar numa subscrição?

Cinquenta. Pode criar um [pedido de suporte do Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para aumentar este limite, mas por predefinição, apenas pode aprovisionar 50 instâncias do Cosmos DB por subscrição.

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Quantas APIs para o Free Bing Maps é possível aprovisionar numa subscrição?

Duas. Pode criar apenas dois Bing Maps de Transações Internas de Nível 1 para planos Enterprise numa subscrição do Azure. Por predefinição, a solução de monitorização remota é aprovisionada com o plano de Transações Internas de Nível 1. Como resultado, apenas pode aprovisionar até duas soluções de monitorização remota numa subscrição sem qualquer modificação.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Posso criar uma solução pré-configurada se tiver o Microsoft Azure para DreamSpark?

Atualmente, não é possível criar uma solução pré-configurada com uma [Microsoft Azure para DreamSpark](https://www.dreamspark.com/Product/Product.aspx?productid=99) conta. No entanto, pode criar um [conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos que lhe permite criar uma solução pré-configurada.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>Posso criar uma solução pré-configurada se tiver subscrição do fornecedor de solução em nuvem (CSP)?

Atualmente, não é possível criar uma solução pré-configurada com uma subscrição do fornecedor de solução em nuvem (CSP). No entanto, pode criar um [conta de avaliação gratuita do Azure](https://azure.microsoft.com/free/) em apenas alguns minutos que lhe permite criar uma solução pré-configurada.

### <a name="how-do-i-delete-an-aad-tenant"></a>Como posso eliminar um inquilino do AAD?

Consulte a mensagem de blogue de Eric Golpe [instruções de eliminação de um inquilino do Azure AD](http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx).

### <a name="next-steps"></a>Passos seguintes

Também pode explorar algumas das outras funcionalidades e capacidades das soluções pré-configuradas do IoT Suite:

* [Descrição geral de solução pré-configurada de manutenção preditiva](iot-suite-predictive-overview.md)
* [Descrição geral da solução de fábrica ligado pré-configurada](iot-suite-connected-factory-overview.md)
* [Segurança de IoT a partir do zero](securing-iot-ground-up.md)