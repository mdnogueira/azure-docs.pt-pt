---
title: "Perguntas mais frequentes de configuração para aplicações web do Azure | Microsoft Docs"
description: "Obtenha respostas às perguntas mais frequentes sobre problemas de configuração e gestão para a funcionalidade Web Apps do App Service do Azure."
services: app-service\web
documentationcenter: 
author: genlin
manager: cshepard
editor: 
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 92cbc36ac2a566cf5dfbb2f7b3347973bab5ee8c
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Gestão de configuração e perguntas mais frequentes para aplicações Web no Azure

Este artigo tem respostas a perguntas mais frequentes (FAQ) sobre problemas de gestão e configuração para o [funcionalidade Web Apps do App Service do Azure](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Existem limitações que posso deve ter conhecimento se pretender mover os recursos do serviço de aplicações?

Se planear mover recursos do serviço de aplicações para um novo grupo de recursos ou subscrição, existem algumas limitações a ter em consideração. Para obter mais informações, consulte [limitações do serviço de aplicações](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations).

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Como utilizar um nome de domínio personalizado para a minha aplicação web?

Para respostas a questões recorrentes sobre como utilizar um nome de domínio personalizado com a sua aplicação web do Azure, consulte os nosso vídeo de sete minuto [adicionar um nome de domínio personalizado](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name). O vídeo oferece uma explicação passo a passo sobre como adicionar um nome de domínio personalizado. Descreve como utilizar o seu próprio URL, em vez do *. azurewebsites.net URL com a sua aplicação web do serviço de aplicações. Também pode ver instruções detalhadas da [como mapear um nome de domínio personalizado](app-service-web-tutorial-custom-domain.md).


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Como se comprar um novo domínio personalizado para a minha aplicação web?

Para saber como comprar e configurar um domínio personalizado para a sua aplicação web do app Service, consulte [comprar e configurar um nome de domínio personalizado no App Service](custom-dns-web-site-buydomains-web-app.md).


## <a name="how-do-i-upload-and-configure-an-existing-ssl-certificate-for-my-web-app"></a>Como carregar e configurar um certificado SSL para a minha aplicação web?

Para saber como carregar e configurar um certificado SSL personalizado existente, consulte [vincular um certificado SSL personalizado existente para uma aplicação web do Azure](app-service-web-tutorial-custom-ssl.md#upload).


## <a name="how-do-i-purchase-and-configure-a-new-ssl-certificate-in-azure-for-my-web-app"></a>Como comprar e configurar um novo certificado SSL no Azure para a minha aplicação web?

Para saber como comprar e configurar um certificado SSL para a sua aplicação web do app Service, consulte [adicionar um certificado SSL para a sua aplicação de serviço de aplicações](web-sites-purchase-ssl-web-site.md).


## <a name="how-do-i-move-application-insights-resources"></a>Como posso mover recursos do Application Insights?

Atualmente, o Azure Application Insights não suporta a operação de movimentação. Se o grupo de recursos original inclui um recurso do Application Insights, não é possível mover esse recurso. Se incluir o recurso do Application Insights ao tentar mover uma aplicação do app Service, todo o mover falha da operação. No entanto, Application Insights e o plano de serviço aplicacional não tem de estar no mesmo grupo de recursos que a aplicação para a aplicação funcione corretamente.

Para obter mais informações, consulte [limitações do serviço de aplicações](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations).

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Onde posso encontrar uma lista de verificação orientações e saber mais sobre recursos movem operações?

[Limitações do serviço de aplicações](../azure-resource-manager/resource-group-move-resources.md#app-service-limitations) mostra-lhe como mover recursos para uma nova subscrição ou para um novo grupo de recursos na mesma subscrição. Pode obter informações sobre a lista de verificação de movimentação de recursos, saber quais os serviços suportam a operação de movimentação e mais informações sobre limitações do serviço de aplicações e outros tópicos.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Como definir o fuso horário do servidor a para a minha aplicação web?

Para definir o fuso horário do servidor para a sua aplicação web:

1. No portal do Azure, na sua subscrição do serviço de aplicações, vá para o **definições da aplicação** menu.
2. Em **as definições de aplicação**, adicione esta definição:
    * Chave = WEBSITE_TIME_ZONE
    * Valor = *o fuso horário pretende*
3. Selecione **Guardar**.

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Por que motivo os meus WebJobs contínuos, por vezes, falhar?

Por predefinição, as aplicações web são descarregadas que estejam Inativos durante um período de tempo definido. Isto permite que o sistema poupar recursos. Nos planos básico e padrão, pode ativar o **Always On** definição para manter a aplicação web carregada sempre. Se a sua aplicação web é executada WebJobs contínuos, deve ativar **Always On**, ou os trabalhos Web podem não ser fiável. Para obter mais informações, consulte [criar um trabalho Web em execução contínua](web-sites-create-web-jobs.md#CreateContinuous).

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Como posso obter o endereço IP de saída da minha aplicação web?

Para obter a lista de endereços IP saídas da sua aplicação web:

1. No portal do Azure, no painel da aplicação web, vá para o **propriedades** menu.
2. Procurar **endereços ip saída**.

É apresentada a lista de endereços IP saídas.

Se o seu Web site estiver alojada num ambiente de serviço de aplicações para PowerApps, para saber como obter o seu endereço IP de saída, consulte o artigo [endereços de rede de saída](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses).

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Como obter um endereço IP entrado reservado ou dedicado a minha aplicação web?

Para configurar um endereço IP reservado ou dedicado para entrada chamadas efetuadas ao seu Web site de aplicações do Azure, instalar e configurar um certificado SSL baseado em IP.

Tenha em atenção que ao utilizar um endereço IP reservado ou dedicado para chamadas de entrada, o plano de serviço aplicacional tem de ser um plano de serviço básico ou superior.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Pode exportar meu certificado de serviço de aplicações a utilizar fora do Azure, tal como para um Web site alojado noutro local? 

Certificados de serviço de aplicações são considerados os recursos do Azure. Estas não recomendações pretendem utilizar fora os serviços do Azure. Não é possível exportar deles para utilizar fora do Azure. Para obter mais informações, consulte [perguntas mais frequentes para certificados de serviço de aplicações e domínios personalizados](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Pode exportar o meu certificado de serviço de aplicações a utilizar com outros serviços em nuvem do Azure?

O portal fornece uma experiência de primeira classe para implementar um certificado de serviço de aplicações através do Cofre de chaves do Azure em aplicações do App Service. No entanto, podemos ter recebe pedidos de clientes para utilizar estes certificados fora da plataforma de serviço de aplicações, por exemplo, com máquinas virtuais do Azure. Para saber como criar uma cópia local do PFX do certificado do serviço de aplicações, pelo que pode utilizar o certificado com outros recursos do Azure, consulte [criar uma cópia local do PFX de um certificado de serviço de aplicações](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).

Para obter mais informações, consulte [perguntas mais frequentes para certificados de serviço de aplicações e domínios personalizados](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Por que motivo ver a mensagem "Parcialmente concluída com êxito" ao tentar fazer cópias de segurança a minha aplicação web?

Uma causa comum de falha de cópia de segurança é que alguns ficheiros estão em utilização pela aplicação. Ficheiros que estão em utilização bloqueados enquanto efetua a cópia de segurança. Isto impede que esses ficheiros a cópia de segurança e poderá resultar num estado "Parcialmente concluída com êxito". Pode potencialmente impedir que isto aconteça excluindo ficheiros do processo de cópia de segurança. Pode optar por criar cópias de segurança apenas o que é necessário. Para obter mais informações, consulte [apenas as partes do seu site com aplicações web do Azure importantes de cópia de segurança](http://www.zainrizvi.io/2015/06/05/creating-partial-backups-of-your-site-with-azure-web-apps/).

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Como remover um cabeçalho de resposta HTTP

Para remover os cabeçalhos de resposta de HTTP, atualize o ficheiro do seu site Web. config. Para obter mais informações, consulte [remover cabeçalhos de servidor padrão nos seus Web sites do Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>Serviço de aplicações está em conformidade com 3.0 padrão PCI e 3.1?

Atualmente, a funcionalidade Web Apps do App Service do Azure está em conformidade com a versão de PCI dados segurança Standard (DSS) 3.0 nível 1. PCI DSS versão 3.1 não está na nossa plano. Planear já está em curso para a forma como irá continuar a adoção de padrão mais recente.

Certificação de versão 3.1 PCI DSS requer desativar Transport Layer Security (TLS) 1.0. Atualmente, a desativação de TLS 1.0 não é uma opção para a maioria dos planos do App Service. No entanto, se utilizar o ambiente de serviço de aplicações ou está disposto a migrar a sua carga de trabalho para o ambiente de serviço de aplicações, pode obter maior controlo do seu ambiente. Isto envolve desativar TLS 1.0 contactando o suporte do Azure. Num futuro próximo, iremos planeie efetuar estas definições acessíveis aos utilizadores.

Para obter mais informações, consulte [compatibilidade de aplicações web do App Service do Microsoft Azure com 3.0 padrão PCI e 3.1](https://support.microsoft.com/help/3124528).

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Como utilizar o ranhuras de implementação e de ambiente de testes?

Nos planos Standard e Premium do serviço de aplicações, quando implementar a aplicação web no App Service, pode implementar para uma ranhura de implementação separadas em vez de para a ranhura de produção de predefinição. As ranhuras de implementação são aplicações web em direto com os seus próprios nomes de anfitrião. Elementos de conteúdo e a configuração da aplicação Web podem ser trocados entre duas ranhuras de implementação, incluindo a ranhura de produção.

Para obter mais informações sobre como utilizar as ranhuras de implementação, consulte [configurar um ambiente de teste no App Service](web-sites-staged-publishing.md).

## <a name="how-do-i-access-and-review-webjob-logs"></a>Como aceder e consulte os registos de WebJob?

Para rever os registos do trabalho Web:

1. Inicie sessão no seu [Web site do Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. Selecione o trabalho Web.
3. Selecione o **alternar saída** botão.
4. Para transferir o ficheiro de saída, selecione o **transferir** ligação.
5. Para execuções individuais, selecione **invocar individuais**.
6. Selecione o **alternar saída** botão.
7. Selecione a hiperligação de transferência.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Estou a tentar utilizar ligações híbridas com o SQL Server. Por que razão apresentada a mensagem "System.OverflowException: operação aritmética resultou numa capacidade excedida"?

Utilizar ligações híbridas para aceder ao SQL Server, uma atualização do Microsoft .NET em 10 de Maio de 2016, poderá causar à falha das ligações. Poderá ver esta mensagem:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Resolução

A exceção foi causada por um problema com o Gestor de ligações híbridas que uma vez que foi corrigido. Certifique-se para [atualizar o seu Gestor de ligações híbridas](https://go.microsoft.com/fwlink/?LinkID=841308) para resolver este problema.

## <a name="how-do-i-add-or-edit-a-url-rewrite-rule"></a>Como adicionar ou Editar regra de reescrever de URL

Para adicionar ou editar um URL rewrite regra:

1. Configure o Gestor de serviços de informação Internet (IIS) para que este se liga a sua aplicação web do app Service. Para saber como ligar o Gestor de IIS para o App Service, consulte [remota administração de Web sites do Azure utilizando o Gestor de IIS](https://azure.microsoft.com/blog/remote-administration-of-windows-azure-websites-using-iis-manager/).
2. No Gestor de IIS, adicionar ou Editar regra de reescrever de URL. Para saber como adicionar ou Editar regra de reescrever de URL, consulte [reescrever de criar regras para o URL rewrite módulo](https://www.iis.net/learn/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module).

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Como controlar o tráfego de entrada para o App Service?

Ao nível do site, tem duas opções para controlar o tráfego de entrada para o serviço de aplicações:

* Ative as restrições de IP dinâmicas. Para saber como ativar restrições de IP dinâmicas, consulte [restrições de IP e domínio para Web sites do Azure](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/).
* Ative o módulo de segurança. Para saber como ativar o módulo de segurança, consulte o artigo [ModSecurity firewall de aplicação web em Web sites do Azure](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/).

Se utilizar o ambiente de serviço de aplicações, pode utilizar [Barracuda firewall](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/).

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Como posso bloquear portas numa aplicação web do serviço de aplicações?

No ambiente de inquilino partilhado do App Service, não é possível bloquear portas específicas devido à natureza da infraestrutura. As portas TCP 4016, 4018 e 4020 também podem ser abertas para depuração remota do Visual Studio.

No ambiente de serviço de aplicações, tem controlo total sobre o tráfego de entrada e saído. Pode utilizar grupos de segurança de rede para restringir ou bloquear portas específicas. Para obter mais informações sobre o ambiente de serviço de aplicações, consulte [introdução ao ambiente de serviço de aplicações](https://azure.microsoft.com/blog/introducing-app-service-environment/).

## <a name="how-do-i-capture-an-f12-trace"></a>Como posso capturar um rastreio de F12?

Tem duas opções para capturar um rastreio de F12:

* Rastreio F12 HTTP
* Resultado da consola F12

### <a name="f12-http-trace"></a>Rastreio F12 HTTP

1. No Internet Explorer, aceda ao seu Web site. É importante iniciar sessão antes de efetuar os passos seguintes. Caso contrário, o rastreio F12 captura dados de início de sessão confidenciais.
2. Premir a tecla F12.
3. Certifique-se de que o **rede** separador está selecionada e, em seguida, selecione a verde **reproduzir** botão.
4. Efetue os passos que reproduza o problema.
5. Selecione a vermelho **parar** botão.
6. Selecione o **guardar** botão (ícone de disco) e guarde o ficheiro HAR (no Internet Explorer e Edge) *ou* o ficheiro HAR com o botão direito e, em seguida, selecione **guardar como HAR com conteúdo** (em Chrome).

### <a name="f12-console-output"></a>Resultado da consola F12

1. Selecione o **consola** separador.
2. Para cada separador contém mais do que zero itens, selecione o separador (**erro**, **aviso**, ou **informações**). Se o separador não está selecionado, o ícone de separador está a cinzento ou negra ao mover o cursor na direção oposta ao mesmo.
3. Faça duplo clique na área de mensagens do painel e, em seguida, selecione **copie todos os**.
4. Colar o texto copiado num ficheiro e, em seguida, guarde o ficheiro.

Para ver um ficheiro HAR, pode utilizar o [Visualizador HAR](http://www.softwareishard.com/har/viewer/).

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Por que motivo recebo um erro ao tentar ligar um serviço de aplicações web de aplicação a uma rede virtual que está ligada ao ExpressRoute?

Se tentar ligar uma aplicação web do Azure a uma rede virtual que tenha ligado ao ExpressRoute do Azure, falhará. É apresentada a seguinte mensagem: "Gateway não é um gateway de VPN."

Atualmente, não pode ter ligações de VPN ponto a site a uma rede virtual que está ligada ao ExpressRoute. Um ponto a site VPN e ExpressRoute não podem coexistir para a mesma rede virtual. Para obter mais informações, consulte [ExpressRoute e de VPN de site para site ligações limites e limitações](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations).

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Como ligar um aplicação web do app Service a uma rede virtual que tenha um gateway (baseado em políticas) de encaminhamento estático?

Atualmente, um aplicação web do app Service a ligar a uma rede virtual que tenha um gateway (baseado em políticas) de encaminhamento estático não é suportada. Se a rede virtual de destino já existir, tem de ter VPN ponto a site ativada, com um gateway de encaminhamento dinâmico, antes de pode ser ligado a uma aplicação. Se o gateway está definido como encaminhamento estático, não é possível ativar uma VPN ponto a site. 

Para obter mais informações, consulte [integrar uma aplicação com uma Azure virtual network](web-sites-integrate-with-vnet.md#getting-started).

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>Num ambiente de serviço a minha aplicação, por que motivo posso criar apenas um plano de serviço de aplicações, embora tiver duas trabalhadores disponíveis?

Para fornecer tolerância a falhas, ambiente de serviço de aplicações requer que cada conjunto de trabalho necessita de pelo menos um recurso de computação adicional. O recurso de computação adicionais não pode ser atribuído uma carga de trabalho.

Para obter mais informações, consulte [como criar um ambiente de serviço de aplicações](environment/app-service-web-how-to-create-an-app-service-environment.md).

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Por que razão vejo tempos limite ao tentar criar um ambiente de serviço de aplicações?

Por vezes, criar um ambiente de serviço de aplicações irá falhar. Nesse caso, consulte o seguinte erro nos registos de atividade:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period.”}}
```

Para resolver este problema, certifique-se de que nenhuma das seguintes condições são verdadeiras:
* A sub-rede é demasiado pequena.
* A sub-rede não está vazia.
* ExpressRoute impede que os requisitos de conectividade de rede de um ambiente de serviço de aplicações.
* Um grupo de segurança de rede incorreto impede que os requisitos de conectividade de rede de um ambiente de serviço de aplicações.
* Ativou a imposição do túnel.

Para obter mais informações, consulte [induzirem frequentes problemas quando implementar (criar) um novo ambiente do serviço de aplicações do Azure](https://blogs.msdn.microsoft.com/waws/2016/05/13/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase/).

## <a name="why-cant-i-delete-my-app-service-plan"></a>Por que motivo não é possível eliminar o meu plano do serviço de aplicações?

Não é possível eliminar um plano de serviço aplicacional se quaisquer aplicações de serviço de aplicações estão associadas com o plano de serviço de aplicações. Antes de eliminar um plano de serviço de aplicações, remova todas as aplicações de serviço de aplicações associadas do plano de serviço de aplicações.

## <a name="how-do-i-schedule-a-webjob"></a>Como posso agendar um trabalho Web?

Pode criar um WebJob agendado através de expressões de Cron:

1. Crie um ficheiro de settings.job.
2. Neste ficheiro JSON, inclua uma propriedade de agenda através da utilização de uma expressão de Cron: 
    ```
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Para obter mais informações sobre WebJobs agendadas, consulte [criar um WebJob agendado utilizando uma expressão de Cron](web-sites-create-web-jobs.md#CreateScheduledCRON).

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Como efetuar a penetração teste para a minha aplicação do app Service?

Para efetuar testes a penetração, [submeter um pedido](https://security-forms.azure.com/penetration-testing/terms).

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Como configurar a um nome de domínio personalizado para um aplicação web do app Service que utilize o Gestor de tráfego?

Para saber como utilizar um nome de domínio personalizado com uma aplicação de serviço de aplicações que utiliza o Gestor de tráfego do Azure para balanceamento de carga, consulte [configurar um nome de domínio personalizado para uma aplicação web do Azure com o Gestor de tráfego](web-sites-traffic-manager-custom-domain-name.md).

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>O meu certificado de serviço de aplicações é sinalizado para fraude. Como resolver isto?

Durante a verificação de domínio de uma compra do certificado de serviço de aplicações, poderá ver a mensagem seguinte:

"O certificado foi sinalizado de possível fraude. O pedido está atualmente a ser revisão. Se o certificado não ficar utilizável dentro de 24 horas, contacte o suporte do Azure."

Como a mensagem indica, o processo de verificação de fraude poderá demorar até 24 horas a concluir. Durante este tempo, irá continuar a ver a mensagem.

Se o certificado de serviço de aplicações continua a mostrar esta mensagem após 24 horas, execute o seguinte script do PowerShell. Os contactos de script a [fornecedor certificado](https://www.godaddy.com/) diretamente para resolver o problema.

```
Login-AzureRmAccount
Set-AzureRmContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzureRmResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Como a autenticação e autorização funcionam no App Service?

Para obter documentação detalhada para autenticação e autorização no serviço de aplicações, consulte docs para várias identificar fornecedor inícios de sessão:
* [Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication.md)
* [Facebook](app-service-mobile-how-to-configure-facebook-authentication.md)
* [Google](app-service-mobile-how-to-configure-google-authentication.md)
* [Conta Microsoft](app-service-mobile-how-to-configure-microsoft-authentication.md)
* [Twitter](app-service-mobile-how-to-configure-twitter-authentication.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Como redirecionar predefinido *. domínio azurewebsites.net para domínio personalizado da minha aplicação web do Azure?

Quando cria um novo Web site através da utilização de aplicações Web no Azure, uma predefinição *sitename*. domínio azurewebsites.net é atribuído ao seu site. Se adicionar um nome de anfitrião personalizado para o seu site e não pretende que os utilizadores possam aceder à sua predefinido *. domínio azurewebsites.net, pode redirecionar o URL predefinido. Para saber como redirecionar todo o tráfego de domínio do seu Web site predefinido para o domínio personalizado, consulte o artigo [redirecionar o domínio predefinido para o domínio personalizado de aplicações web do Azure](http://www.zainrizvi.io/2016/04/07/block-default-azure-websites-domain/).

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Como determinar qual é a versão do .NET está instalada a versão no App Service?

É a forma mais rápida para localizar a versão do Microsoft .NET que está instalado no App Service utilizando a consola do Kudu. Pode aceder a consola do Kudu a partir do portal ou utilizando o URL da sua aplicação de serviço de aplicações. Para obter instruções detalhadas, consulte [determinar a versão instalada do .NET no App Service](https://blogs.msdn.microsoft.com/waws/2016/11/02/how-to-determine-the-installed-net-version-in-azure-app-services/).

## <a name="why-isnt-autoscale-working-as-expected"></a>Por que motivo não está dimensionamento automático estão a funcionar como esperado?

Se ainda não ampliada dimensionamento automático do Azure no ou ampliar a instância da aplicação web a esperada, poderá estar em execução para um cenário no qual vamos intencionalmente optar por não dimensionar para evitar um ciclo infinito devido a "em oscilação." Normalmente, isto acontece quando não é uma margem suficiente entre os limiares de escalamento horizontal e escala. Para saber como para evitar "em oscilação" e para ler mais sobre outros procedimentos recomendados de dimensionamento automático, consulte [melhores práticas de dimensionamento automático](../monitoring-and-diagnostics/insights-autoscale-best-practices.md#autoscale-best-practices).

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Por que motivo dimensionamento automático, por vezes, dimensionar apenas parcialmente?

Dimensionamento automático é acionado quando métricas excederem limites pré-configurada. Por vezes, é possível que repare que a capacidade é apenas parcialmente preenchida compared que esperado. Esta situação pode ocorrer quando o número de instâncias que pretende que não está disponível. Nesse cenário, o dimensionamento automático preenche parcialmente a sessão com o número de instâncias disponível. Dimensionamento automático, em seguida, executa a lógica de reequilibre para obter mais capacidade. Atribui as instâncias restantes. Tenha em atenção que esta operação poderá demorar alguns minutos.

Se não vir o número esperado de instâncias após alguns minutos, poderá ser porque a refill parcial foi suficiente para colocar as métricas dentro dos limites. Em alternativa, poderá ter ampliada dimensionamento automático para baixo porque atingiu o limite inferior de métricas.

Se nenhum dos seguintes condições aplicam-se e o problema persistir, submeta um pedido de suporte.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Como posso ativar de compressão de HTTP para o meu conteúdo?

Para ativar a compressão estáticos e dinâmicos em tipos de conteúdo, adicione o seguinte código ao ficheiro Web. config do nível de aplicação:

```
<system.webServer>
<urlCompression doStaticCompression="true" doDynamicCompression="true" />
< /system.webServer>
```

Também pode especificar os tipos de MIME estáticos e dinâmicos específicos que pretende comprimir. Para obter mais informações, consulte o nosso resposta a uma pergunta de fórum nos [httpCompression definições num Web site do Azure simple](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview).

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Como posso migrar de um ambiente no local para o App Service?

Para migrar sites de servidores de web do Windows e Linux para o App Service, pode utilizar o Azure App Service migração através do assistente. A ferramenta de migração cria as web apps e bases de dados no Azure, conforme necessário e, em seguida, publica o conteúdo. Para obter mais informações, consulte [Azure App Service migração assistente](https://www.migratetoazure.net/).
