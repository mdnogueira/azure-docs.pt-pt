---
title: "Configurar definições de proxy e de firewall do Azure Log Analytics | Microsoft Docs"
description: "Configure as definições de proxy e de firewall quando os agentes ou os serviços OMS necessitam utilizar portas específicas."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: b55ebd80-efd4-4220-971b-c18aea1b1ab2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 04/12/2017
ms.author: banders;magoedte
translationtype: Human Translation
ms.sourcegitcommit: 7f469fb309f92b86dbf289d3a0462ba9042af48a
ms.openlocfilehash: cc3ff1ca5e638896df155b55145b300f70f7540a
ms.lasthandoff: 04/13/2017


---
# <a name="configure-proxy-and-firewall-settings-in-log-analytics"></a>Configurar definições de proxy e de firewall do Log Analytics
Ações necessárias para configurar o proxy e as definições da firewall para o Log Analytics diferem consoante o tipo de agentes que está a utilizar. Reveja as secções seguintes para o tipo de agente que utiliza.

## <a name="settings-for-the-oms-gateway"></a>Definições do Gateway de OMS

Se os agentes não tiverem acesso à Internet, podem enviar os dados com os recursos de rede para o Gateway de OMS. O Gateway recolhe os dados e envia-os para o serviço de OMS em seu nome.

Configure os agentes que comunicam com o Gateway de OMS com o respetivo nome de domínio completamente qualificado e o número de porta personalizado.

O Gateway de OMS necessita de acesso à Internet. Utilize o mesmo servidor proxy ou as definições da firewall para o Gateway de OMS que escolheria para o tipo de agentes que tem. Para obter mais informações sobre o Gateway de OMS, consulte [Connect computers and devices to OMS using the OMS Gateway (Ligar computadores e dispositivos ao OMS com o Gateway de OMS)](log-analytics-oms-gateway.md).

## <a name="configure-settings-with-the-microsoft-monitoring-agent"></a>Configurar definições com o Agente de Monitorização da Microsoft
Para que o Agente de Monitorização da Microsoft se ligue e para se registar no serviço OMS, este deve ter acesso ao número de porta dos domínios e aos URLs. Se utilizar um servidor proxy para comunicação entre o agente e o serviço OMS, terá de assegurar que os recursos adequados estão acessíveis. Se utilizar uma firewall para restringir o acesso à Internet, terá de configurar a firewall para permitir acesso ao OMS. As tabelas seguintes listam as portas de OMS que necessita.

| **Recursos do Agente** | **Portas** | **Inspeção de HTTPS direto** |
| --- | --- | --- |
| \*.ods.opinsights.azure.com |443 |Sim |
| \*.oms.opinsights.azure.com |443 |Sim |
| \*.blob.core.windows.net |443 |Sim |
| \*.azure-automation.net |443 |Sim |

Pode utilizar o procedimento seguinte para configurar as definições de proxy para o Agente de Monitorização da Microsoft utilizando o Painel de Controlo. Terá de utilizar o procedimento para cada servidor. Se tiver vários servidores que necessita configurar, poderá considerar mais fácil utilizar um script para automatizar este processo. Se este é o caso, consulte o seguinte procedimento [Configurar as definições de proxy para o Agente de Monitorização da Microsoft utilizando um script](#to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script).

### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-control-panel"></a>Configurar as definições de proxy para o Agente de Monitorização da Microsoft utilizando o Painel de Controlo
1. Abra o **Painel de Controlo**.
2. Abra o **Agente de Monitorização da Microsoft**.
3. Clique no separador **Definições de Proxy**.<br>  
   ![separador de definições de proxy](./media/log-analytics-proxy-firewall/proxy-direct-agent-proxy.png)
4. Selecione **Utilizar um servidor proxy** e escreva o URL e o número de porta, se necessário, semelhante ao exemplo apresentado. Se o servidor proxy requer autenticação, escreva o nome de utilizador e a palavra-passe para aceder ao servidor proxy.

Utilize o procedimento seguinte para criar um script do PowerShell que possa executar para configurar as definições de proxy para cada agente que estabelece ligação ao servidores diretamente.

### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script"></a>Configurar as definições de proxy para o Agente de Monitorização da Microsoft utilizando um script
Copie o exemplo seguinte, atualize-o com informações específicas do ambiente, guarde-o com uma extensão de nome de ficheiro PS1 e, em seguida, execute o script em cada computador que estabelece ligação ao serviço OMS diretamente.

    param($ProxyDomainName="http://proxy.contoso.com:80", $cred=(Get-Credential))

    # First we get the Health Service configuration object.  We need to determine if we
    #have the right update rollup with the API we need.  If not, no need to run the rest of the script.
    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

    $proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

    if (!$proxyMethod)
    {
         Write-Output 'Health Service proxy API not present, will not update settings.'
         return
    }

    Write-Output "Clearing proxy settings."
    $healthServiceSettings.SetProxyInfo('', '', '')

    $ProxyUserName = $cred.username

    Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
    $healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)


## <a name="configure-settings-with-operations-manager"></a>Configure as definições com o Operations Manager
Para que um grupo de gestão do Operations Manager se ligue e para se registar no serviço OMS, este deve ter acesso aos números de porta dos domínios e aos URLs. Se utilizar um servidor proxy para comunicação entre o servidor de gestão do Operations Manager e o serviço OMS, terá de assegurar que os recursos adequados estão acessíveis. Se utilizar uma firewall para restringir o acesso à Internet, terá de configurar a firewall para permitir acesso ao OMS. Mesmo se um servidor de gestão do Operations Manager não estiver protegido por um servidor proxy, é possível que os agentes do mesmo estejam. Neste caso, o servidor proxy deve ser configurado da mesma forma que os agentes de modo a ativar e a permitir que os dados da solução de Gestão de Segurança e de Registo sejam enviados para o serviço Web do OMS.

Para os agentes do Operations Manager comunicarem com o serviço OMS, a infraestrutura do Operations Manager (incluindo os agentes) deve ter as definições e a versão de proxy corretas. A definição de proxy para agentes é especificada na consola do Operations Manager. A sua versão deve ser uma das seguintes:

* Operations Manager 2012 SP1 Update Rollup 7 ou posterior
* Operations Manager 2012 R2 Update Rollup 3 ou posterior

As tabelas seguintes listam as portas relacionados com estas tarefas.

> [!NOTE]
> Alguns dos recursos seguintes mencionam Informações Operacionais e de Assistência, ambas como versões anteriores do OMS. No entanto, os recursos listados irão mudar no futuro.
>
>

Eis uma lista de recursos de agente e de portas:<br>

| **Recursos do agente** | **Portas** |
| --- | --- |
| \*.ods.opinsights.azure.com |443 |
| \*.oms.opinsights.azure.com |443 |
| \*.blob.core.windows.net/\* |443 |

<br>
Eis uma lista de recursos de servidor de gestão e de portas:<br>

| **Recursos do servidor de gestão** | **Portas** | **Inspeção de HTTPS direto** |
| --- | --- | --- |
| service.systemcenteradvisor.com |443 | |
| \*.service.opinsights.azure.com |443 | |
| \*.blob.core.windows.net |443 |Sim |
| \*.ods.opinsights.azure.com |443 |Sim |
| \*.azure-automation.net |443 |Sim |

<br>
Eis uma lista de recursos de consola do OMS e do Operations Manager e de portas.<br>

| **Recurso de consola do OMS e do Operations Manager** | **Portas** |
| --- | --- |
| service.systemcenteradvisor.com |443 |
| \*.service.opinsights.azure.com |443 |
| \*.live.com |Porta 80 e 443 |
| \*.microsoft.com |Porta 80 e 443 |
| \*.microsoftonline.com |Porta 80 e 443 |
| \*.mms.microsoft.com |Porta 80 e 443 |
| login.windows.net |Porta 80 e 443 |

<br>

Utilize os procedimentos seguintes para registar o grupo de gestão do Operations Manager com o serviço OMS. Se estiver a ter problemas de comunicação entre o grupo de gestão e o serviço OMS, utilize os procedimentos de validação para resolver problemas de transmissão de dados para o serviço OMS.

### <a name="to-request-exceptions-for-the-oms-service-endpoints"></a>Pedir exceções para os pontos finais de serviço OMS
1. Utilize as informações da primeira tabela apresentada anteriormente para certificar-se de que os recursos necessários para o servidor de gestão do Operations Manager estão acessíveis através de quaisquer firewalls que possa ter.
2. Utilize as informações da segunda tabela apresentada anteriormente para certificar-se de que os recursos necessários para a consola de Operações do Operations Manager e do OMS estão acessíveis através de quaisquer firewalls que possa ter.
3. Se utilizar um servidor proxy com o Internet Explorer, certifique-se de que este está configurado e que funciona corretamente. Para verificar, pode abrir uma ligação Web segura (HTTPS), por exemplo [https://bing.com](https://bing.com). Se a ligação Web segura não funciona num browser, é provável que não funcione na consola de gestão do Operations Manager com os serviços Web na nuvem.

### <a name="to-configure-the-proxy-server-in-the-operations-manager-console"></a>Configurar o servidor proxy na consola do Operations Manager
1. Abra a consola do Operations Manager e selecione a área de trabalho de **Administração**.
2. Expanda as **Informações Operacionais** e, em seguida, selecione a **Ligação Informações Operacionais**.<br>  
   ![Ligação do OMS do Operations Manager](./media/log-analytics-proxy-firewall/proxy-om01.png)
3. Na vista Ligação OMS, clique em **Configurar Servidor Proxy**.<br>  
   ![Ligação do OMS do Operations Manager Configurar Servidor Proxy](./media/log-analytics-proxy-firewall/proxy-om02.png)
4. No Assistente de Definição de Informações Operacionais: Servidor Proxy, selecione **Utilizar um servidor proxy para aceder ao Serviço Web de Informações Operacionais** e, em seguida, escreva o URL com o número de porta, por exemplo, **http://myproxy:80**.<br>  
   ![Endereço proxy do OMS do Operations Manager](./media/log-analytics-proxy-firewall/proxy-om03.png)

### <a name="to-specify-credentials-if-the-proxy-server-requires-authentication"></a>Especificar credenciais se o servidor proxy requer autenticação
 É necessário que definições e as credenciais do servidor proxy propaguem para computadores geridos que irão reportar ao OMS. Os servidores devem estar no *Microsoft System Center Advisor Monitoring Server Group*. As credenciais são encriptadas no registo de cada servidor do grupo.

1. Abra a consola do Operations Manager e selecione a área de trabalho de **Administração**.
2. Em **Configuração RunAs**, selecione **Perfis**.
3. Abra o perfil **System Center Advisor Run As Profile Proxy**.<br>  
   ![imagem do perfil do System Center Advisor Run As Proxy](./media/log-analytics-proxy-firewall/proxy-proxyacct1.png)
4. No Assistente do Perfil Run As, clique em **Adicionar** para utilizar uma conta Run As. Pode criar uma nova conta Run As ou utilizar uma conta existente. Esta conta tem de ter permissões suficientes para passar através do servidor proxy.<br>   
   ![imagem do Assistente de Perfil Run As](./media/log-analytics-proxy-firewall/proxy-proxyacct2.png)
5. Para configurar a conta a gerir, escolha **Uma classe, grupo ou objeto selecionado** para abrir a caixa de Pesquisa de Objeto.<br>  
   ![imagem do Assistente de Perfil Run As](./media/log-analytics-proxy-firewall/proxy-proxyacct2-1.png)
6. Pesquise e, em seguida, selecione **Microsoft System Center Advisor Monitoring Server Group**.<br>  
   ![imagem da caixa de Pesquisa de Objeto](./media/log-analytics-proxy-firewall/proxy-proxyacct3.png)
7. Clique em **OK** para fechar a caixa Adicionar uma conta Run As.<br>  
   ![imagem do Assistente de Perfil Run As](./media/log-analytics-proxy-firewall/proxy-proxyacct4.png)
8. Conclua o assistente e guarde as alterações.<br>  
   ![imagem do Assistente de Perfil Run As](./media/log-analytics-proxy-firewall/proxy-proxyacct5.png)

### <a name="to-validate-that-oms-management-packs-are-downloaded"></a>Validar a transferência dos pacotes de gestão do OMS
Se adicionou soluções para OMS, pode visualizá-las na consola do Operations Manager como pacotes de gestão em **Administração**. Pesquise *System Center Advisor* para encontrá-los rapidamente.<br>  
   ![pacotes de gestão transferidos](./media/log-analytics-proxy-firewall/proxy-mpdownloaded.png)  <br>  
Ou, pode também verificar os pacotes de gestão do OMS através do seguinte comando do Windows PowerShell no servidor de gestão do Operations Manager:

   ```  
    Get-ScomManagementPack | where {$_.DisplayName -match 'Advisor'} | select Name,DisplayName,Version,KeyToken
   ```  

### <a name="to-validate-that-operations-manager-is-sending-data-to-the-oms-service"></a>Para validar que o Operations Manager está a enviar dados para o serviço OMS
1. No servidor de gestão do Operations Manager, abra o Monitor de Desempenho (perfmon.exe) e, em seguida, selecione **Monitor de Desempenho**.
2. Clique em **Adicionar** e, em seguida, selecione **Grupos de Gestão do Serviço de Estado de Funcionamento**.
3. Adicionar todos os contadores que começam com **HTTP**.<br>  
   ![adicionar contadores](./media/log-analytics-proxy-firewall/proxy-sendingdata1.png)
4. Se a configuração do Operations Manager for boa, irá ver a atividade para os contadores de Gestão do Serviço de Estado de Funcionamento de eventos e outros itens de dados, com base nos pacotes de gestão que adicionou ao OMS e na política de coleção de registo configurada.<br>  
   ![Atividade da apresentação de Monitor de Desempenho](./media/log-analytics-proxy-firewall/proxy-sendingdata2.png)

## <a name="next-steps"></a>Passos seguintes
* [Adicionar soluções do Log Analytics a partir da Galeria de Soluções](log-analytics-add-solutions.md) para adicionar funcionalidade e recolher dados.
* Familiarize-se com as [pesquisas de registos](log-analytics-log-searches.md) para ver informações detalhadas recolhidas pelas soluções.

