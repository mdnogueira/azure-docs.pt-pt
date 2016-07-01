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
  ms.date="05/09/2016"
  ms.author="araguila"/>
   
# Perguntas mais frequentes sobre o IoT Suite

### Qual a diferença entre a eliminação de um grupo de recursos no Portal do Azure e clicar Eliminar numa solução pré-configurada em azureiotsuite.com?

- Se eliminar a solução pré-configurada em [azureiotsuite.com][Ink-azureiotsuite], estará a eliminar todos os recursos que aprovisionou aquando da criação da solução pré-configurada; se adicionou recursos adicionais ao grupo de recursos, estes também serão eliminados. 

- Se eliminar o grupo de recursos no [Portal do Azure][Ink-azure-portal], apenas estará a eliminar os recursos desse grupo de recursos; também terá de eliminar a aplicação Azure Active Directory associada à solução pré-configurada no [Portal Clássico do Azure][Ink-classic-portal].

### Quantas instâncias do DocumentDB é possível aprovisionar numa subscrição?

Cinco. Pode criar um [pedido de suporte do Azure][link-azuresupportticket] para aumentar este limite. No entanto, por predefinição, apenas pode aprovisionar cinco instâncias do DocumentDB por subscrição. Como resultado, apenas pode aprovisionar até cinco soluções de monitorização remota de uma dada subscrição.

### Quantas APIs para o Free Bing Maps é possível aprovisionar numa subscrição?

Duas. Apenas pode criar duas APIs para o Free Bing Maps numa subscrição. Por predefinição, a solução de monitorização remota é aprovisionada com uma API para o Free Bing Maps. Como resultado, apenas pode aprovisionar até duas soluções de monitorização remota numa subscrição sem qualquer modificação.

### Tenho uma implementação de solução de monitorização remota com um mapa estático, como adicionar um mapa interativo ao Bing? 
1. Obtenha a API para Bing Maps para Enterprise QueryKey a partir do [Portal do Azure][Ink-azure-portal]: 
 1. Navegue para o Grupo de Recursos onde se encontra a sua API para Bing Maps Enterprise no [Portal do Azure][Ink-azure-portal].
 2. Clique em Todas as definições e, em seguida, em Gestão de chave. 
 3. Irá ver duas chaves: a MasterKey e a QueryKey. Copie o valor para QueryKey.

     > [AZURE.NOTE] Não tem uma API para o Bing Maps para conta Enterprise? Crie uma no [Portal do Azure][Ink-azure-portal] clicando em + Nova, procure a API para Bing Maps para Enterprise e siga as instruções para a criação.

2. Obtenha o código mais recente a partir de [Monitorização-Remota-IoT-Azure][Ink-remote-monitoring-github].

3. Executar uma implementação local ou na nuvem seguindo a orientação de implementação de uma linha de comandos na pasta /docs/ do repositório. 

4. Depois de ter executado a implementação local ou na nuvem, procure na sua pasta raiz o ficheiro *.user.config criado aquando da implementação. Abra este ficheiro num editor de texto. 

5. Altere a seguinte linha para incluir o valor que copiou para a sua QueryKey: 
   
  `<setting name="MapApiQueryKey" value="" />`

### Posso criar uma solução pré-configurada se tiver o Microsoft Azure para DreamSpark?
Neste momento, não é possível criar uma solução pré-configurada com uma conta [Microsoft Azure para DreamSpark][Ink-dreamspark]. Contudo, pode criar uma [conta de avaliação gratuita para o Azure][Ink-30daytrial] em apenas alguns minutos, o que lhe permitirá criar uma solução pré-configurada.

### Como posso eliminar um inquilino do AAD?

Consulte a mensagem de blogue de Eric Golpe [Instruções de Eliminação de um Inquilino do Azure AD][Ink-delete-aad-tennant].

[link-azuresupportticket]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade 
[lnk-azure-portal]: https://portal.azure.com
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-remote-monitoring-github]: https://github.com/Azure/azure-iot-remote-monitoring 
[lnk-dreamspark]: https://www.dreamspark.com/Product/Product.aspx?productid=99 
[lnk-30daytrial]: https://azure.microsoft.com/free/
[lnk-delete-aad-tennant]: http://blogs.msdn.com/b/ericgolpe/archive/2015/04/30/walkthrough-of-deleting-an-azure-ad-tenant.aspx



<!--HONumber=Jun16_HO2-->


