<properties
  pageTitle="Perguntas mais frequentes sobre o Azure IoT Suite | Microsoft Azure"
  description="Perguntas mais frequentes sobre o IoT Suite"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="get-started-article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="06/27/2016"
  ms.author="araguila"/>
   
# Perguntas mais frequentes sobre o IoT Suite

### Qual a diferença entre a eliminação de um grupo de recursos no Portal do Azure e clicar Eliminar numa solução pré-configurada em azureiotsuite.com?

- Se eliminar a solução pré-configurada em [azureiotsuite.com][lnk-azureiotsuite], estará a eliminar todos os recursos que aprovisionou aquando da criação da solução pré-configurada; se adicionou recursos adicionais ao grupo de recursos, estes também serão eliminados. 

- Se eliminar o grupo de recursos no [Portal do Azure][lnk-azure-portal], apenas estará a eliminar os recursos desse grupo de recursos; também terá de eliminar a aplicação Azure Active Directory associada à solução pré-configurada no [Portal Clássico do Azure][lnk-classic-portal].

### Quantas instâncias do Hub IoT é possível aprovisionar numa subscrição? 

Dez. Pode criar um [pedido de suporte do Azure][link-azuresupportticket] para aumentar esse limite. No entanto, por predefinição, apenas pode aprovisionar 10 Hubs IoT por subscrição, conforme descrito nos [limites de subscrição do Azure][link-azuresublimits]. Como resultado, uma vez que cada solução pré-configurada aprovisiona um Hub IoT novo, apenas pode aprovisionar até dez soluções pré-configuradas numa determinada subscrição. 

### Quantas instâncias do DocumentDB é possível aprovisionar numa subscrição?

Cinquenta. Pode criar um [pedido de suporte do Azure][link-azuresupportticket] para aumentar este limite. No entanto, por predefinição, apenas pode aprovisionar cinquenta instâncias do DocumentDB por subscrição. 

### Quantas APIs para o Free Bing Maps é possível aprovisionar numa subscrição?

Duas. Pode criar apenas dois Bing Maps de Transações Internas de Nível 1 para planos Enterprise numa subscrição do Azure. Por predefinição, a solução de monitorização remota é aprovisionada com o plano de Transações Internas de Nível 1. Como resultado, apenas pode aprovisionar até duas soluções de monitorização remota numa subscrição sem qualquer modificação.

### Tenho uma implementação de solução de monitorização remota com um mapa estático, como adicionar um mapa interativo ao Bing? 
1. Obtenha a API para Bing Maps para Enterprise QueryKey a partir do [Portal do Azure][lnk-azure-portal]: 
 1. Navegue para o Grupo de Recursos onde se encontra a sua API para Bing Maps Enterprise no [Portal do Azure][lnk-azure-portal].
 2. Clique em Todas as definições e, em seguida, em Gestão de chave. 
 3. Irá ver duas chaves: a MasterKey e a QueryKey. Copie o valor para QueryKey.

     > [AZURE.NOTE] Não tem uma API para o Bing Maps para conta Enterprise? Crie uma no [Portal do Azure][lnk-azure-portal] clicando em + Nova, procure a API para Bing Maps para Enterprise e siga as instruções para a criação.

2. Obtenha o código mais recente a partir de [Monitorização-Remota-IoT-Azure][lnk-remote-monitoring-github].

3. Executar uma implementação local ou na nuvem seguindo a orientação de implementação de uma linha de comandos na pasta /docs/ do repositório. 

4. Depois de ter executado a implementação local ou na nuvem, procure na sua pasta raiz o ficheiro *.user.config criado aquando da implementação. Abra este ficheiro num editor de texto. 

5. Altere a seguinte linha para incluir o valor que copiou para a sua QueryKey: 
   
  `<setting name="MapApiQueryKey" value="" />`

### Posso criar uma solução pré-configurada se tiver o Microsoft Azure para DreamSpark?
Neste momento, não é possível criar uma solução pré-configurada com uma conta [Microsoft Azure para DreamSpark][lnk-dreamspark]. Contudo, pode criar uma [conta de avaliação gratuita para o Azure][lnk-30daytrial] em apenas alguns minutos, o que lhe permitirá criar uma solução pré-configurada.

### Como posso eliminar um inquilino do AAD?

Consulte a mensagem de blogue de Eric Golpe [Instruções de Eliminação de um Inquilino do Azure AD][lnk-delete-aad-tennant].

## Passos seguintes

Também pode explorar algumas das outras funcionalidades e capacidades das soluções pré-configuradas do IoT Suite:

- [Descrição geral da manutenção preditiva da solução pré-configurada][Ink-predictive-overview]
- [Segurança de IoT a partir do zero][lnk-security-groundup]

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-security-groundup]: securing-iot-ground-up.md

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[link-azuresublimits]: https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/#iot-hub-limits
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx



<!--HONumber=Aug16_HO1-->


