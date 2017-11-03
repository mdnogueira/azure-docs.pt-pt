---
title: "Criar e gerir ligações híbridas | Microsoft Docs"
description: "Saiba como criar uma ligação híbrida, a ligação de gerir e instalar o Gestor de ligações híbridas. MABS, WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: erikre
editor: 
ms.assetid: aac0546b-3bae-41e0-b874-583491a395ea
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: ccompy
ms.openlocfilehash: 7b8b9072d0e2fd054ca07873c0a9ce772dc2941e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-manage-hybrid-connections"></a>Criar e Gerir Ligações Híbridas

> [!IMPORTANT]
> As Ligações Híbridas do BizTalk são desativadas e substituídas por Ligações Híbridas do Serviço de Aplicações. Para obter mais informações, incluindo como gerir as Ligações Híbridas do BizTalk, veja [Azure App Service Hybrid Connections](../app-service/app-service-hybrid-connections.md) (Ligações Híbridas do Serviço de Aplicações do Azure).


## <a name="overview-of-the-steps"></a>Descrição geral dos passos
1. Criar uma ligação híbrida introduzindo o **nome de anfitrião** ou **FQDN** do recurso no local na sua rede privada.
2. Ligar a aplicações web do Azure ou mobile apps do Azure para a ligação híbrida.
3. Instalar o Gestor de ligações híbridas no seu recurso no local e ligue-se para a ligação híbrida específico. O portal do Azure fornece uma experiência de clique único para instalar e ligar.
4. Gerir ligações híbridas e as respetivas chaves de ligação.

Este tópico lista estes passos. 

> [!IMPORTANT]
> É possível definir um ponto final da ligação híbrida para um endereço IP. Se utilizar um endereço IP, poderá ou poderá não atingir o recurso no local, dependendo do seu cliente. A ligação híbrida depende do cliente efetuar uma pesquisa de DNS. Na maioria dos casos, o **cliente** é o código da aplicação. Se o cliente não efetua uma pesquisa de DNS (está a tentar resolver o endereço IP, como se fosse um nome de domínio (x.x.x. x)), em seguida, o tráfego não é enviado através da ligação híbrida.
> 
> Por exemplo (pseudocode), é possível definir **10.4.5.6** como o anfitrião no local:
> 
> **Funciona da seguinte cenário:**  
> `Application code -> GetHostByName("10.4.5.6") -> Resolves to 127.0.0.3 -> Connect("127.0.0.3") -> Hybrid Connection -> on-prem host`
> 
> **Não utilizar o seguinte cenário:**  
> `Application code -> Connect("10.4.5.6") -> ?? -> No route to host`
> 
> 

## <a name="CreateHybridConnection"></a>Criar uma ligação híbrida
Uma ligação híbrida podem ser criada no portal do Azure com as Web Apps **ou** utilizando os BizTalk Services. 

<!-- **To create Hybrid Connections using Web Apps**, see [Connect Azure Web Apps to an On-Premises Resource](../app-service-web/web-sites-hybrid-connection-get-started.md). You can also install the Hybrid Connection Manager (HCM) from your web app, which is the preferred method.  -->

**Para criar ligações híbridas no BizTalk Services**:

1. Inicie sessão no [Portal Clássico do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. No painel de navegação esquerdo, selecione **BizTalk Services** e, em seguida, selecione o seu BizTalk Service. 
   
    Se não tiver um serviço BizTalk existente, pode [cria um BizTalk Service](biztalk-provision-services.md).
3. Selecione o **ligações híbridas** separador:  
   ![Separador ligações híbridas][HybridConnectionTab]
4. Selecione **criar uma ligação híbrida** ou selecione o **adicionar** botão na barra de tarefas. Introduza o seguinte:
   
   | Propriedade | Descrição |
   | --- | --- |
   | Nome |O nome da ligação híbrida têm de ser exclusivo e não pode ser o mesmo nome que o BizTalk Service. Pode introduzir qualquer nome, mas ser específico com o objetivo. Os exemplos incluem:<br/><br/>Folha de pagamentos*SQLServer*<br/>SupplyList*SharepointServer*<br/>Os clientes*OracleServer* |
   | Nome de anfitrião |Introduza o nome de anfitrião totalmente qualificado, apenas o nome de anfitrião ou o endereço IPv4 do recurso no local. Os exemplos incluem:<br/><br/>mySQLServer<br/>*mySQLServer*. *Domínio*. corp.*yourCompany*empresa>.com<br/>*myHTTPSharePointServer*<br/>*myHTTPSharePointServer*. *yourCompany*empresa>.com<br/>10.100.10.10<br/><br/>Se utilizar o endereço IPv4, tenha em atenção que o código de cliente ou a aplicação não poderá resolver o endereço IP. Consulte o importante que tenha em atenção na parte superior deste tópico. |
   | Porta |Introduza o número de porta do recurso no local. Por exemplo, se estiver a utilizar as Web Apps, introduza a porta 443 ou a porta 80. Se estiver a utilizar o SQL Server, introduza a porta 1433. |
5. Selecione a marca de verificação para concluir a configuração. 

#### <a name="additional"></a>Adicionais
* Podem ser criadas várias ligações híbridas. Consulte o [BizTalk Services: gráfico de edições](biztalk-editions-feature-chart.md) para o número de ligações permitido. 
* Cada ligação híbrida é criada com um par de cadeias de ligação: esse chaves de envio e no local que ESCUTAM as chaves de aplicação. Cada par tem um site primário e uma chave secundária. 

## <a name="LinkWebSite"></a>Ligar a aplicação móvel ou aplicação de Web do App Service do Azure
Para ligar uma aplicação Web ou aplicação móvel no App Service do Azure para uma ligação híbrida existente, selecione **utilizar uma ligação híbrida existente** no painel ligações híbridas. 
<!-- See [Access on-premises resources using hybrid connections in Azure App Service](../app-service-web/web-sites-hybrid-connection-get-started.md). -->

## <a name="InstallHCM"></a>Instalar o Gestor de ligações híbridas no local
Depois de criar uma ligação híbrida, instale o Gestor de ligações híbridas no recurso no local. Pode ser transferido das suas aplicações web do Azure ou a partir do seu BizTalk Service. Passos de BizTalk Services: 

1. Inicie sessão no [Portal Clássico do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. No painel de navegação esquerdo, selecione **BizTalk Services** e, em seguida, selecione o seu BizTalk Service. 
3. Selecione o **ligações híbridas** separador:  
   ![Separador ligações híbridas][HybridConnectionTab]
4. Na barra de tarefas, selecione **On-Premises configuração**:  
   ![Configuração no local][HCOnPremSetup]
5. Selecione **instalar e configurar** para executar ou transferir o Gestor de ligações híbridas no sistema local. 
6. Selecione a marca de verificação para iniciar a instalação. 

<!--
You can also download the Hybrid Connection Manager MSI file and copy the file to your on-premises resource. Specific steps:

1. Copy the on-premises primary Connection String. See [Manage Hybrid Connections](#ManageHybridConnection) in this topic for the specific steps.
2. Download the Hybrid Connection Manager MSI file. 
3. On the on-premises resource, install the Hybrid Connection Manager from the MSI file. 
4. Using Windows PowerShell, type: 
> Add-HybridConnection -ConnectionString “*Your On-Premises Connection String that you copied*” 
--> 

#### <a name="additional"></a>Adicionais
* Gestor de ligações híbridas pode ser instalado nos seguintes sistemas operativos:
  
  * Windows Server 2008 R2 (.NET Framework 4.5 + e Windows Management Framework 4.0 + necessário)
  * Windows Server 2012 (Windows Management Framework 4.0 + necessário)
  * Windows Server 2012 R2
* Depois de instalar o Gestor de ligações híbridas, ocorre o seguinte: 
  
  * A ligação híbrida alojada no Azure é automaticamente configurada para utilizar a cadeia de ligação de aplicação principal. 
  * O recurso no local é automaticamente configurado para utilizar a cadeia de ligação no local primário.
* O Gestor de ligações híbridas tem de utilizar uma cadeia de ligação válido no local para autorização. As Web Apps do Azure ou Mobile Apps, tem de utilizar uma cadeia de ligação de aplicação válida para autorização.
* Pode dimensionar as ligações híbridas instalando outra instância do Gestor de ligações híbridas noutro servidor. Configure o serviço de escuta no local para utilizar o mesmo endereço como o primeiro serviço de escuta no local. Nesta situação, o tráfego é distribuído aleatoriamente (o round robin) entre os serviços de escuta do Active Directory no local. 

## <a name="ManageHybridConnection"></a>Gerir ligações híbridas
Para gerir as ligações híbridas, pode:

* Utilize o portal do Azure e aceda ao seu BizTalk Service. 
* Utilize [REST APIs](http://msdn.microsoft.com/library/azure/dn232347.aspx).

#### <a name="copyregenerate-the-hybrid-connection-strings"></a>Cópia/voltar a gerar as cadeias de ligação híbrida
1. Inicie sessão no [Portal Clássico do Azure](http://go.microsoft.com/fwlink/p/?LinkID=213885).
2. No painel de navegação esquerdo, selecione **BizTalk Services** e, em seguida, selecione o seu BizTalk Service. 
3. Selecione o **ligações híbridas** separador:  
   ![Separador ligações híbridas][HybridConnectionTab]
4. Selecione a ligação híbrida. Na barra de tarefas, selecione **gerir ligação**:  
   ![Gerir as opções][HCManageConnection]
   
    **Gerir ligação** apresenta as cadeias de ligação da aplicação e no local. Pode copiar as cadeias de ligação ou voltar a gerar a chave de acesso utilizada na cadeia de ligação. 
   
    **Se selecionar voltar a gerar**, a chave de acesso partilhado utilizados numa cadeia de ligação é alterada. Faça o seguinte:
   
   * No portal clássico do Azure, selecione **sincronizar chaves** na aplicação do Azure.
   * Volte a executar o **no local configuração**. Quando executar novamente o programa de configuração no local, o recurso no local é automaticamente configurado para utilizar a cadeia de ligação principal atualizadas.

#### <a name="use-group-policy-to-control-the-on-premises-resources-used-by-a-hybrid-connection"></a>Política de grupo de utilização para controlar os recursos no local utilizados por uma ligação híbrida
1. Transferir o [modelos administrativos do Gestor de ligações híbridas](http://www.microsoft.com/download/details.aspx?id=42963).
2. Extraia os ficheiros.
3. No computador que modifica a política de grupo, faça o seguinte:  
   
   * Copiar o. Ficheiros ADMX para o *%WINROOT%\PolicyDefinitions* pasta.
   * Copiar o. ADML ficheiros para o *%WINROOT%\PolicyDefinitions\en-us* pasta.

Depois de copiar, pode utilizar o Editor de políticas de grupo para alterar a política.

## <a name="next"></a>Seguinte
[Descrição geral de ligações híbridas](integration-hybrid-connection-overview.md)

## <a name="see-also"></a>Veja Também
[API REST para gerir os BizTalk Services no Microsoft Azure](http://msdn.microsoft.com/library/azure/dn232347.aspx)  
[Serviços BizTalk: Gráfico de Edições](biztalk-editions-feature-chart.md)  
[Criar um BizTalk Service com o portal clássico do Azure](biztalk-provision-services.md)  
[Serviços BizTalk: Separadores Dashboard, Monitorizar e Dimensionar](biztalk-dashboard-monitor-scale-tabs.md)

[HybridConnectionTab]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionTab.png
[HCOnPremSetup]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionOnPremSetup.png
[HCManageConnection]: ./media/integration-hybrid-connection-create-manage/WABS_HybridConnectionManageConn.png 
