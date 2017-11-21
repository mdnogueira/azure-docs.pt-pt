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
ms.date: 11/02/2017
ms.author: corywink
ms.openlocfilehash: db928795cc38dea4ca9fc441e6053e3bb44da23e
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="frequently-asked-questions-for-iot-suite"></a>Perguntas mais frequentes sobre o IoT Suite

Além disso, consulte o específicos de fábrica ligado [FAQ](iot-suite-faq-cf.md).

### <a name="where-can-i-find-the-source-code-for-the-preconfigured-solutions"></a>Onde posso encontrar o código de origem para as soluções pré-configuradas?

O código de origem é armazenado no repositórios do GitHub seguintes:
* [Solução pré-configurada de monitorização remota][lnk-remote-monitoring-github]
* [Solução pré-configurada de manutenção preditiva][lnk-predictive-maintenance-github]
* [Solução de fábrica ligado pré-configurada](https://github.com/Azure/azure-iot-connected-factory)

### <a name="how-do-i-update-to-the-latest-version-of-the-remote-monitoring-preconfigured-solution-that-uses-the-iot-hub-device-management-features"></a>Como atualizar para a versão mais recente da solução pré-configurada de monitorização remota que utiliza as funcionalidades de gestão de dispositivos do IoT Hub?

* Se implementar uma solução pré-configurada a partir do https://www.azureiotsuite.com/ site, implementa sempre uma nova instância da versão mais recente da solução.
* Se implementar uma solução pré-configurada através da linha de comandos, pode atualizar uma implementação existente com o código de novo. Consulte [implementação na nuvem] [ lnk-cloud-deployment] no GitHub [repositório][lnk-remote-monitoring-github].

### <a name="how-can-i-add-support-for-a-new-device-method-to-the-remote-monitoring-preconfigured-solution"></a>Como adicionar suporte para um novo método de dispositivo para a solução pré-configurada de monitorização remota?

Consulte a secção [adicionar suporte para um novo método para o simulador] [ lnk-add-method] no [personalizar uma solução pré-configurada] [ lnk-customize] artigo.

### <a name="the-simulated-device-is-ignoring-my-desired-property-changes-why"></a>O dispositivo simulado está a ignorar a minha alterações de propriedade pretendido, por que motivo?
A solução pré-configurada monitorização remota, o dispositivo simulado código utiliza apenas a **Desired.Config.TemperatureMeanValue** e **Desired.Config.TelemetryInterval** pretendido propriedades Atualize as propriedades que relatados. Todos os outros pedidos de alteração de propriedade pretendido são ignorados.

### <a name="my-device-does-not-appear-in-the-list-of-devices-in-the-solution-dashboard-why"></a>Dispositivo não for apresentada a lista de dispositivos no dashboard de solução, por que motivo?

A lista de dispositivos no dashboard de solução utiliza uma consulta para devolver a lista de dispositivos. Atualmente, não pode devolver mais do que os dispositivos de 10 mil uma consulta. Tente efetuar os critérios de pesquisa na sua consulta mais restrita.

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-preconfigured-solution-in-azureiotsuitecom"></a>Qual a diferença entre a eliminação de um grupo de recursos no Portal do Azure e clicar Eliminar numa solução pré-configurada em azureiotsuite.com?

* Se eliminar a solução pré-configurada em [azureiotsuite.com][lnk-azureiotsuite], estará a eliminar todos os recursos que aprovisionou aquando da criação da solução pré-configurada. Se adicionou recursos adicionais ao grupo de recursos, estes recursos também serão eliminados. 
* Se eliminar o grupo de recursos no [portal do Azure][lnk-azure-portal], apenas estará a eliminar os recursos nesse grupo de recursos. Também terá de eliminar a aplicação do Azure Active Directory associada à solução pré-configurada no [portal do Azure][lnk-azure-portal].

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias do Hub IoT é possível aprovisionar numa subscrição?

Por predefinição pode aprovisionar [10 hubs IoT por subscrição][link-azuresublimits]. Pode criar um [pedido de suporte do Azure] [ link-azuresupportticket] para aumentar este limite. Como resultado, uma vez que cada solução pré-configurada aprovisiona um IoT Hub novo, apenas pode aprovisionar até 10 soluções pré-configuradas numa determinada subscrição. 

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>Quantas instâncias de base de dados do Azure Cosmos possível aprovisionar numa subscrição?

Cinquenta. Pode criar um [pedido de suporte do Azure] [ link-azuresupportticket] para aumentar este limite, mas por predefinição, apenas pode aprovisionar 50 instâncias do Cosmos DB por subscrição. 

### <a name="how-many-free-bing-maps-apis-can-i-provision-in-a-subscription"></a>Quantas APIs para o Free Bing Maps é possível aprovisionar numa subscrição?

Duas. Pode criar apenas dois Bing Maps de Transações Internas de Nível 1 para planos Enterprise numa subscrição do Azure. Por predefinição, a solução de monitorização remota é aprovisionada com o plano de Transações Internas de Nível 1. Como resultado, apenas pode aprovisionar até duas soluções de monitorização remota numa subscrição sem qualquer modificação.

### <a name="i-have-a-remote-monitoring-solution-deployment-with-a-static-map-how-do-i-add-an-interactive-bing-map"></a>Tenho uma implementação de solução de monitorização remota com um mapa estático, como adicionar um mapa interativo ao Bing?

1. Obter a API do Bing Maps para Enterprise QueryKey a partir de [portal do Azure][lnk-azure-portal]: 
   
   1. Navegue para o grupo de recursos onde a sua API para Bing Maps para Enterprise está no [portal do Azure][lnk-azure-portal].
   2. Clique em **todas as definições**, em seguida, **gestão de chaves**. 
   3. Pode ver duas chaves: **MasterKey** e **QueryKey**. Copie o valor para **QueryKey**.
      
      > [!NOTE]
      > Não tem uma API para o Bing Maps para conta Enterprise? Crie uma no [portal do Azure] [ lnk-azure-portal] pelas clicando em + nova, procura API para Bing Maps para Enterprise e siga as instruções para criar.
      > 
      > 
2. Obtenha o código mais recente do [Azure-IoT-Remote-Monitoring][lnk-remote-monitoring-github].
3. Executar uma local ou seguir as orientações de implementação da linha de comandos na pasta /docs/ do repositório de implementação na nuvem. 
4. Depois de ter executado a implementação local ou na nuvem, procure na sua pasta raiz o ficheiro *.user.config criado aquando da implementação. Abra este ficheiro num editor de texto. 
5. Altere a seguinte linha para incluir o valor que copiou do seu **QueryKey**: 
   
   `<setting name="MapApiQueryKey" value="" />`

### <a name="can-i-create-a-preconfigured-solution-if-i-have-microsoft-azure-for-dreamspark"></a>Posso criar uma solução pré-configurada se tiver o Microsoft Azure para DreamSpark?

Atualmente, não é possível criar uma solução pré-configurada com uma [Microsoft Azure para DreamSpark] [ lnk-dreamspark] conta. No entanto, pode criar um [conta de avaliação gratuita do Azure] [ lnk-30daytrial] em apenas alguns minutos que lhe permite criar uma solução pré-configurada.

### <a name="can-i-create-a-preconfigured-solution-if-i-have-cloud-solution-provider-csp-subscription"></a>Posso criar uma solução pré-configurada se tiver subscrição do fornecedor de solução em nuvem (CSP)?

Atualmente, não é possível criar uma solução pré-configurada com uma subscrição do fornecedor de solução em nuvem (CSP). No entanto, pode criar um [conta de avaliação gratuita do Azure] [ lnk-30daytrial] em apenas alguns minutos que lhe permite criar uma solução pré-configurada.

### <a name="how-do-i-delete-an-aad-tenant"></a>Como posso eliminar um inquilino do AAD?

Consulte a mensagem de blogue de Eric Golpe [instruções de eliminação de um inquilino do Azure AD][lnk-delete-aad-tennant].

### <a name="next-steps"></a>Passos seguintes

Também pode explorar algumas das outras funcionalidades e capacidades das soluções pré-configuradas do IoT Suite:

* [Descrição geral de solução pré-configurada de manutenção preditiva][lnk-predictive-overview]
* [Descrição geral da solução de fábrica ligado pré-configurada](iot-suite-connected-factory-overview.md)
* [Segurança de IoT desde o início][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx
[lnk-cloud-deployment]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md
[lnk-add-method]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md#add-support-for-a-new-method-to-the-simulator
[lnk-customize]: iot-suite-v1-guidance-on-customizing-preconfigured-solutions.md
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-predictive-maintenance-github]: https://github.com/Azure/azure-iot-predictive-maintenance
