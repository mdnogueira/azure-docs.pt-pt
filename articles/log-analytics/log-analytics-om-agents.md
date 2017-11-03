---
title: "Estabelecer a ligação do Operations Manager ao Log Analytics | Microsoft Docs"
description: "Para manter o investimento existente no System Center Operations Manager e utilizar as capacidades de expandida com a análise de registos, pode integrar o Operations Manager com a sua área de trabalho do OMS."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 245ef71e-15a2-4be8-81a1-60101ee2f6e6
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/21/2017
ms.author: magoedte
ms.openlocfilehash: 387ec757ec17799408ef45bfeb523eb98a5b1013
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="connect-operations-manager-to-log-analytics"></a>Estabelecer a ligação do Operations Manager ao Log Analytics
Para manter o investimento existente no System Center Operations Manager e utilizar as capacidades de expandida com a análise de registos, pode integrar o Operations Manager com a sua área de trabalho do OMS.  Isto permite que tirar partido as oportunidades de OMS ao continuar a utilizar o Operations Manager para:

* Continuar a monitorizar o estado de funcionamento dos seus serviços de TI com o Operations Manager
* Manter a integração com as suas soluções ITSM suportar gestão de incidente e problema
* Gerir o ciclo de vida dos agentes implementados no local e nuvem pública IaaS as máquinas virtuais que monitorizar com o Operations Manager

Integração com o System Center Operations Manager acrescenta valor à sua estratégia de operações do serviço utilizando a velocidade e a eficiência do OMS no recolher, armazenar e analisar dados do Operations Manager.  OMS ajuda correlacionar e de trabalho para identificar as falhas de problemas e analisar recurrences para suportar o processo de gestão de problema existente.   A flexibilidade do motor de busca para examinar os dados de desempenho, eventos e alertas, com dashboards avançados e capacidades de relatórios para expor estes dados de formas significativas, demonstra a força que OMS proporciona na complimenting do Operations Manager.

Os agentes de relatórios para o grupo de gestão do Operations Manager recolhe dados de servidores com base no origens de dados de análise de registos e soluções que tiver ativado na sua subscrição do OMS.  Consoante a solução que tiver ativado, dados a partir destas soluções são um enviada diretamente a partir de um servidor de gestão do Operations Manager para o serviço web do OMS, ou devido ao volume de dados recolhidos no sistema geridos por agente, são enviados diretamente a partir do agente ao serviço web do OMS. O servidor de gestão reencaminha os dados OMS diretamente para o serviço web do OMS; Se nunca é escrito na base de dados OperationsManager ou OperationsManagerDW.  Quando um servidor de gestão perde a conectividade com o serviço web do OMS,-coloca em cache os dados localmente até que a comunicação é restabelecida com o OMS.  Se o servidor de gestão estiver offline devido a manutenção planeada ou falha não planeada, o outro servidor de gestão no grupo de gestão retoma conectividade com o OMS.  

O diagrama seguinte ilustra a ligação entre servidores de gestão e os agentes num grupo de gestão do System Center Operations Manager e o OMS, incluindo as portas e direção.   

![OMS-operations-manager-integração-diagrama](./media/log-analytics-om-agents/oms-operations-manager-connection.png)

Se as políticas de segurança de TI não permitir que os computadores na sua rede para ligar à Internet, os servidores de gestão podem ser configurados para ligar ao Gateway para receber as informações de configuração e enviar os dados recolhidos consoante a solução que tiver ativado o OMS.  Para obter mais informações e os passos sobre como configurar o grupo de gestão do Operations Manager para comunicar através de um Gateway do OMS para o serviço do OMS, consulte [ligar computadores ao OMS utilizando o Gateway do OMS](log-analytics-oms-gateway.md).  

## <a name="system-requirements"></a>Requisitos de sistema
Antes de começar, reveja os detalhes seguintes para verificar que cumpre os pré-requisitos.

* OMS só suporta o Operations Manager 2016, UR10 do Operations Manager 2012 SP1 e posterior e UR11 do Operations Manager 2012 R2 e superior.
* Todos os agentes do Operations Manager têm de cumprir os requisitos de suporte mínimo. Certifique-se de que os agentes estão em atualização mínima, caso contrário, o tráfego de agente do Windows poderão falhar e muitos erros podem preencher o registo de eventos do Operations Manager.
* Uma subscrição do Log Analytics do Azure.  Para obter mais informações, consulte [introdução à análise de registos](log-analytics-get-started.md).

### <a name="network"></a>Rede
As informações abaixo lista as informações de configuração de proxy e de firewall necessárias para o agente do Operations Manager, servidores de gestão e consola de operações comunicar com o OMS.  Tráfego de cada componente é de saída da sua rede para o serviço do OMS.     

|Recurso | Número de porta| Ignorar a inspeção de HTTP|  
|---------|------|-----------------------|  
|**Agente**|||  
|\*.ods.opinsights.azure.com| 443 |Sim|  
|\*.oms.opinsights.azure.com| 443|Sim|  
|\*.blob.core.windows.net| 443|Sim|  
|\*.azure-automation.net| 443|Sim|  
|**Servidor de gestão**|||  
|\*.service.opinsights.azure.com| 443||  
|\*.blob.core.windows.net| 443| Sim|  
|\*.ods.opinsights.azure.com| 443| Sim|  
|*.azure-automation.net | 443| Sim|  
|**Consola do Operations Manager para OMS**|||  
|service.systemcenteradvisor.com| 443||  
|\*.service.opinsights.azure.com| 443||  
|\*.live.com| 80 e 443||  
|\*.microsoft.com| 80 e 443||  
|\*.microsoftonline.com| 80 e 443||  
|\*.mms.microsoft.com| 80 e 443||  
|login.windows.net| 80 e 443||  


## <a name="connecting-operations-manager-to-oms"></a>Ligar o Operations Manager para OMS
Execute a seguinte série de passos para configurar o grupo de gestão do Operations Manager para ligar a uma das áreas de trabalho das OMS.

1. Na consola do Operations Manager, selecione o **administração** área de trabalho.
2. Expanda o nó de Operations Management Suite e clique em **ligação**.
3. Clique em de **registar no Operations Management Suite** ligação.
4. No **Assistente de integração do Operations Management Suite: autenticação** página, introduza o endereço de e-mail ou número de telefone e a palavra-passe da conta de administrador que está associada a sua subscrição do OMS e clique em  **Inicie sessão no**.
5. Depois que serem autenticadas com êxito, no **Assistente de integração do Operations Management Suite: selecionar área de trabalho** página lhe for pedido para selecionar a sua área de trabalho do OMS.  Se tiver mais de uma área de trabalho, selecione a área de trabalho que pretende registar com o grupo de gestão do Operations Manager da lista pendente e, em seguida, clique em **seguinte**.
   
   > [!NOTE]
   > O Operations Manager só suporta uma área de trabalho do OMS cada vez. A ligação e os computadores que foram registados para OMS com a área de trabalho anterior são removidos do OMS.
   > 
   > 
6. No **Assistente de integração do Operations Management Suite: resumo** página, confirme as suas definições e se estiverem corretas, clique em **criar**.
7. No **Assistente de integração do Operations Management Suite: concluir** página, clique em **fechar**.

### <a name="add-agent-managed-computers"></a>Adicionar computadores geridos por agente
Após configurar a integração com a sua área de trabalho do OMS, isto só estabelece uma ligação com o OMS, sem dados são recolhidos a partir dos agentes que reportam ao seu grupo de gestão. Tal não acontecer até depois de configurar os computadores geridos por agente específicos recolhe dados para análise de registos. Pode selecionar os objetos de computador individualmente ou pode selecionar um grupo que contenha objetos de computador do Windows. Não é possível selecionar um grupo que contenha as instâncias de outra classe, tais como discos lógicos ou bases de dados SQL.

1. Abra a consola do Operations Manager e selecione a área de trabalho de **Administração**.
2. Expanda o nó de Operations Management Suite e clique em **ligação**.
3. Clique no **adicionar um computador/grupo** ligação sob as ações de cabeçalho no lado direito do painel.
4. No **computador pesquisa** caixa de diálogo, pode procurar computadores ou grupos monitorizados pelo Operations Manager. Selecionar computadores ou grupos para ser integrado no OMS, clique em **adicionar**e, em seguida, clique em **OK**.

Pode ver computadores e grupos configurados para recolher dados a partir do nó computadores geridos no Operations Management Suite no **administração** área de trabalho da consola de operações.  Aqui, pode adicionar ou remover computadores e grupos conforme necessário.

### <a name="configure-oms-proxy-settings-in-the-operations-console"></a>Configurar definições de proxy do OMS na consola de operações
Se for um servidor proxy interno entre o grupo de gestão e o serviço web do OMS, execute os seguintes passos.  Estas definições são geridas do grupo de gestão e distribuídas a sistemas geridos por agente que estão incluídos no âmbito para recolher dados para OMS centralmente.  Este é vantajoso para quando determinadas soluções ignorar o servidor de gestão e enviar dados diretamente para o serviço web do OMS.

1. Abra a consola do Operations Manager e selecione a área de trabalho de **Administração**.
2. Expanda o Operations Management Suite e, em seguida, clique em **ligações**.
3. Na vista Ligação OMS, clique em **Configurar Servidor Proxy**.
4. No **assistente Operations Management Suite: servidor Proxy** página, selecione **utilizar um servidor proxy para aceder ao Operations Management Suite**, e, em seguida, escreva o URL com o número de porta, por exemplo, http:// corpproxy:80 e, em seguida, clique em **concluir**.

Se o servidor proxy requer autenticação, execute os seguintes passos para configurar as definições que têm de ser propagados para computadores geridos que comunica OMS no grupo de gestão e as credenciais.

1. Abra a consola do Operations Manager e selecione a área de trabalho de **Administração**.
2. Em **Configuração RunAs**, selecione **Perfis**.
3. Abra o perfil **System Center Advisor Run As Profile Proxy**.
4. No assistente Run As perfil, clique em Adicionar para utilizar uma conta Run As. Pode criar um [conta Run As](https://technet.microsoft.com/library/hh321655.aspx) ou utilizar uma conta existente. Esta conta tem de ter permissões suficientes para passar através do servidor proxy.
5. Para configurar a conta para gerir, escolha **uma classe selecionada, grupo ou objeto**, clique em **selecionar...** e, em seguida, clique em **grupo...** Para abrir o **grupo pesquisa** caixa.
6. Procure e, em seguida, selecione **grupo do servidor de monitorização do Microsoft System Center Advisor**.  Clique em **OK** depois de selecionar o grupo para fechar o **grupo pesquisa** caixa.
7. Clique em **OK** para fechar o **adicionar uma conta Run As** caixa.
8. Clique em **guardar** para concluir o assistente e guarde as alterações.

Depois de criar a ligação e configurar os agentes irão recolher e comunicar dados OMS, é aplicada a seguinte configuração no grupo de gestão, não necessariamente por ordem:

* A conta Run As **Microsoft.SystemCenter.Advisor.RunAsAccount.Certificate** é criado.  É associada ao perfil Run As **Microsoft System Center Advisor executar como perfil Blob** e está direcionado para duas classes - **servidor recolha** e **grupo de gestão do Operations Manager** .
* São criados dois conectores.  O primeiro nome **Microsoft.SystemCenter.Advisor.DataConnector** e é automaticamente configurado com uma subscrição que reencaminha todos os alertas gerados a partir de instâncias de todas as classes no grupo de gestão para análise de registos do OMS. O conector do segundo **Advisor Connector**, que é responsável por comunicar com o serviço web do OMS e partilha de dados.
* Os agentes e grupos que selecionou para recolher dados no grupo de gestão é adicionado ao **grupo do servidor de monitorização do Microsoft System Center Advisor**.

## <a name="management-pack-updates"></a>Atualizações de pacotes de gestão
Depois de concluir a configuração, o grupo de gestão do Operations Manager estabelece uma ligação com o serviço do OMS.  O servidor de gestão sincroniza com o serviço web e receber informações de configuração atualizada sob a forma de pacotes de gestão para as soluções tiver ativado o que se integram com o Operations Manager.   O Operations Manager verifica a existência de atualizações destes pacotes de gestão e automaticamente transferir e importa-las quando estiverem disponíveis.  Existem duas regras em particular controlar qual este comportamento:

* **Microsoft.SystemCenter.Advisor.MPUpdate** -atualizações de pacotes de gestão base do OMS. Por predefinição, é executada a cada 12 horas.
* **Microsoft.SystemCenter.Advisor.Core.GetIntelligencePacksRule** -atualizações de pacotes de gestão de solução ativados na sua área de trabalho. Por predefinição, é executada a cada cinco (5) minutos.

Pode substituir estas duas regras para impedir a transferência automática, desativando-los, ou modificar a frequência com que frequência o servidor de gestão sincroniza com o OMS para determinar se um novo pacote de gestão está disponível e deve ser transferido.  Siga os passos [como substituir um Monitor ou regra](https://technet.microsoft.com/library/hh212869.aspx) para modificar o **frequência** parâmetro com um valor em segundos, para alterar a agenda de sincronização ou modificar o **ativado**parâmetro para desativar as regras.  As substituições para todos os objetos da classe de grupo de gestão do Operations Manager de destino.

Se pretender continue a seguir o processo de controlo de alterações existente para controlar as versões do pacote de gestão no grupo de gestão de produção, pode desativar as regras e ativá-los durante a horas específicas quando as atualizações são permitidas. Se tiver um desenvolvimento ou grupo de gestão de pergunta e resposta no seu ambiente e tem conetividade à Internet, pode configurar esse grupo de gestão com uma área de trabalho do OMS para suportar este cenário.  Isto permite-lhe rever e avaliar as versões dos pacotes de gestão de OMS iterativas antes libertá-los para o grupo de gestão de produção.

## <a name="switch-an-operations-manager-group-to-a-new-oms-workspace"></a>Mudar de um grupo do Operations Manager para uma nova área de trabalho do OMS
1. Inicie sessão na sua subscrição do OMS e criar uma área de trabalho [Microsoft Operations Management Suite](http://oms.microsoft.com/).
2. Abra a consola do Operations Manager com uma conta que seja um membro da função administradores do Operations Manager e selecione o **administração** área de trabalho.
3. Expanda o Operations Management Suite e selecione **ligações**.
4. Selecione o **reconfigure operação Management Suite** ligação no lado do meio do painel.
5. Siga o **Assistente de integração do Operations Management Suite** e introduza o e-mail endereço ou número de telefone e a palavra-passe da conta de administrador que está associada a sua nova área de trabalho do OMS.
   
   > [!NOTE]
   > O **Assistente de integração do Operations Management Suite: selecionar área de trabalho** página apresenta a área de trabalho existente que está a ser utilizado.
   > 
   > 

## <a name="validate-operations-manager-integration-with-oms"></a>Validar a integração do Operations Manager com o OMS
Existem algumas formas diferentes pode verificar que o OMS para integração do Operations Manager foi concluída com êxito.

### <a name="to-confirm-integration-from-the-oms-portal"></a>Para confirmar a integração do portal do OMS
1. No portal do OMS, clique em de **definições** mosaico
2. Selecione **ligado origens**.
3. Na tabela na secção System Center Operations Manager, deverá ver o nome do grupo de gestão listado com o número de agentes e o estado quando os dados foram recebidos pela última vez.
   
   ![definições de OMS-connectedsources](./media/log-analytics-om-agents/oms-settings-connectedsources.png)
4. Tenha em atenção o **ID da área de trabalho** valor sob o lado esquerdo da página de definições.  Validá-lo contra o grupo de gestão do Operations Manager, abaixo.  

### <a name="to-confirm-integration-from-the-operations-console"></a>Para confirmar a integração da consola de operações
1. Abra a consola do Operations Manager e selecione a área de trabalho de **Administração**.
2. Selecione **pacotes de gestão** e no **procure:** tipo caixa de texto **Advisor** ou **Intelligence**.
3. Consoante as soluções que tiver ativado, pode ver um pacote de gestão correspondentes, listado nos resultados da pesquisa.  Por exemplo, se tiver ativado a solução de gestão de alertas, o pacote de gestão Gestão alertas do Microsoft System Center Advisor está na lista.
4. Do **monitorização** ver, navegue para o **operações de gestão Suite\Health estado** vista.  Selecione um servidor de gestão no **estado do servidor de gestão** painel e, no **vista de detalhes** painel confirmar o valor da propriedade **URI do serviço de autenticação** corresponde o ID da área de trabalho OMS.
   
   ![OMS-opsmgr-mg-authsvcuri-Property-MS](./media/log-analytics-om-agents/oms-opsmgr-mg-authsvcuri-property-ms.png)

## <a name="remove-integration-with-oms"></a>Remova a integração com o OMS
Quando já não necessitar de integração entre o grupo de gestão do Operations Manager e a área de trabalho do OMS, existem vários passos necessários para remover corretamente a ligação e a configuração do grupo de gestão. O procedimento seguinte tiver que atualizar a sua área de trabalho do OMS, eliminando a referência do seu grupo de gestão, elimine os conectores do OMS e, em seguida, elimine os pacotes de gestão que suportam OMS.   

Pacotes de gestão para as soluções tiver ativado a que se integram com o Operations Manager e os pacotes de gestão necessários para suportar a integração com o serviço do OMS não podem ser facilmente eliminados do grupo de gestão.  Isto acontece porque alguns dos pacotes de gestão de OMS terem dependências de outros pacotes de gestão relacionados.  Para eliminar pacotes de gestão, tendo uma dependência de outros pacotes de gestão, transferir o script [remover um pacote de gestão com dependências](https://gallery.technet.microsoft.com/scriptcenter/Script-to-remove-a-84f6873e) do Centro de scripts do TechNet.  

1. Abra a Shell de comandos do Operations Manager com uma conta que seja membro da função administradores do Operations Manager.
   
    > [!WARNING]
    > Certifique-se de não tem quaisquer pacotes de gestão personalizados com o word Advisor ou IntelligencePack no nome do antes de continuar, caso contrário, os seguintes passos eliminá-los a partir do grupo de gestão.
    > 

2. A partir do shell de comandos, escreva`Get-SCOMManagementPack -name "*Advisor*" | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
3. Tipo de próximo`Get-SCOMManagementPack -name “*IntelligencePack*” | Remove-SCOMManagementPack -ErrorAction SilentlyContinue`
4. Para remover quaisquer pacotes de gestão restantes que tenham uma dependência de outros pacotes de gestão do System Center Advisor, utilize o script *RecursiveRemove.ps1* transferiu a partir do Centro de scripts da TechNet anteriormente.  
 
    > [!NOTE]
    > Não elimine os pacotes de gestão do Microsoft System Center Advisor ou o Microsoft System Center Advisor interno.  
    >  

5. Abra a consola de operações do Operations Manager com uma conta que seja membro da função administradores do Operations Manager.
6. Em **administração**, selecione o **pacotes de gestão** nó e, no **procure:** caixa, escreva **Advisor** e verifique o seguinte ainda serem importados pacotes de gestão no grupo de gestão:
   
   * Microsoft System Center Advisor
   * Microsoft System Center Advisor interno
7. No portal do OMS, clique no mosaico **Definições**.
8. Selecione **ligado origens**.
9. Na tabela na secção System Center Operations Manager, deverá ver o nome do grupo de gestão que pretende remover a área de trabalho.  Sob a coluna **últimos dados**, clique em **remover**.  
   
    > [!NOTE]
    > O **remover** ligação não estarão disponível até depois de 14 dias se não existir nenhuma atividade detetada do grupo de gestão ligado.  
    > 

10. Será apresentada uma janela de pedir-lhe para confirmar que pretende prosseguir com a remoção.  Clique em **Sim** para continuar. 

Para eliminar os dois conectores - Microsoft.SystemCenter.Advisor.DataConnector Advisor Connector, guarde o script do PowerShell abaixo para o seu computador e executar utilizando os exemplos seguintes:

```
    .\OM2012_DeleteConnector.ps1 “Advisor Connector” <ManagementServerName>
    .\OM2012_DeleteConnectors.ps1 “Microsoft.SytemCenter.Advisor.DataConnector” <ManagementServerName>
```

> [!NOTE]
> O computador que executa este script de, se não for um servidor de gestão, deve ter a shell de comandos do Operations Manager instalada, dependendo da versão do seu grupo de gestão.
> 
> 

```
    `param(
    [String] $connectorName,
    [String] $msName="localhost"
    )
    $mg = new-object Microsoft.EnterpriseManagement.ManagementGroup $msName
    $admin = $mg.GetConnectorFrameworkAdministration()
    ##########################################################################################
    # Configures a connector with the specified name.
    ##########################################################################################
    function New-Connector([String] $name)
    {
         $connectorForTest = $null;
         foreach($connector in $admin.GetMonitoringConnectors())
    {
    if($connectorName.Name -eq ${name})
    {
         $connectorForTest = Get-SCOMConnector -id $connector.id
    }
    }
    if ($connectorForTest -eq $null)
    {
         $testConnector = New-Object Microsoft.EnterpriseManagement.ConnectorFramework.ConnectorInfo
         $testConnector.Name = $name
         $testConnector.Description = "${name} Description"
         $testConnector.DiscoveryDataIsManaged = $false
         $connectorForTest = $admin.Setup($testConnector)
         $connectorForTest.Initialize();
    }
    return $connectorForTest
    }
    ##########################################################################################
    # Removes a connector with the specified name.
    ##########################################################################################
    function Remove-Connector([String] $name)
    {
        $testConnector = $null
        foreach($connector in $admin.GetMonitoringConnectors())
       {
        if($connector.Name -eq ${name})
       {
         $testConnector = Get-SCOMConnector -id $connector.id
       }
      }
     if ($testConnector -ne $null)
     {
        if($testConnector.Initialized)
     {
     foreach($alert in $testConnector.GetMonitoringAlerts())
     {
       $alert.ConnectorId = $null;
       $alert.Update("Delete Connector");
     }
     $testConnector.Uninitialize()
     }
     $connectorIdForTest = $admin.Cleanup($testConnector)
     }
    }
    ##########################################################################################
    # Delete a connector's Subscription
    ##########################################################################################
    function Delete-Subscription([String] $name)
    {
      foreach($testconnector in $admin.GetMonitoringConnectors())
      {
      if($testconnector.Name -eq $name)
      {
        $connector = Get-SCOMConnector -id $testconnector.id
      }
    }
    $subs = $admin.GetConnectorSubscriptions()
    foreach($sub in $subs)
    {
      if($sub.MonitoringConnectorId -eq $connector.id)
      {
        $admin.DeleteConnectorSubscription($admin.GetConnectorSubscription($sub.Id))
      }
     }
    }
    #New-Connector $connectorName
    write-host "Delete-Subscription"
    Delete-Subscription $connectorName
    write-host "Remove-Connector"
    Remove-Connector $connectorName
```

No futuro se planear no seu grupo de gestão para uma área de trabalho do OMS restabelecer a ligação, terá de voltar a importar o `Microsoft.SystemCenter.Advisor.Resources.\<Language>\.mpb` ficheiro de pacote de gestão do update rollup mais recente aplicado ao seu grupo de gestão.  Pode encontrar este ficheiro no `%ProgramFiles%\Microsoft System Center 2012` ou `System Center 2012 R2\Operations Manager\Server\Management Packs for Update Rollups` pasta.

## <a name="next-steps"></a>Passos seguintes
Para adicionar a funcionalidade e recolher dados, consulte [soluções de análise de registos adicionar da galeria do soluções](log-analytics-add-solutions.md).


