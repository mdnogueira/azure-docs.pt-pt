---
title: "Solução de monitorização da VMware no Log Analytics | Microsoft Docs"
description: "Saiba mais sobre como a solução de monitorização de VMware pode ajudar a gerir os registos e monitorizar anfitriões ESXi."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 16516639-cc1e-465c-a22f-022f3be297f1
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/11/2017
ms.author: banders
ms.openlocfilehash: 17072c4b6e4fdf6e4dc2b7a6a4ded7fa9f9f6fde
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="vmware-monitoring-preview-solution-in-log-analytics"></a>Solução de monitorização do VMware (pré-visualização) na análise de registos

![Símbolo de VMware](./media/log-analytics-vmware/vmware-symbol.png)

A solução de monitorização do VMware na análise de registos é uma solução que ajuda a criar um registo centralizado e a abordagem de monitorização para os registos de VMware grandes. Este artigo descreve como pode resolver problemas, capturar e gerir os anfitriões ESXi numa única localização utilizando a solução. Com a solução, pode ver os dados detalhados para todos os seus anfitriões ESXi numa única localização. Pode ver as contagens de principais de eventos, o estado e as tendências de anfitriões VM e ESXi fornecidos através de registos de anfitrião ESXi. Pode resolver ao visualizar e pesquisar registos de anfitrião ESXi centralizados. Além disso, pode criar alertas com base em consultas de pesquisa de registo.

A solução utiliza a funcionalidade de syslog nativo do anfitrião ESXi para enviar dados para um destino de VM, que tem o agente do OMS. No entanto, a solução não escrever ficheiros no syslog dentro da VM de destino. O agente do OMS abre porta 1514 e escuta para este. Depois de receber os dados, o agente do OMS pushes de dados no OMS.

## <a name="installing-and-configuring-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes informações para instalar e configurar a solução.

* Adicionar a solução de monitorização do VMware a sua área de trabalho do OMS utilizando o processo descrito no [soluções de análise de registos adicionar da galeria do soluções](log-analytics-add-solutions.md).

#### <a name="supported-vmware-esxi-hosts"></a>Anfitriões do VMware ESXi suportados
vSphere ESXi 5.5 de anfitrião e 6.0

#### <a name="prepare-a-linux-server"></a>Preparar um servidor Linux
Crie um VM receba todos os dados de syslog dos anfitriões ESXi do sistema de operativo de Linux. O [agente Linux do OMS](log-analytics-linux-agents.md) é o ponto de coleção para todos os dados de syslog do anfitrião ESXi. Pode utilizar vários anfitriões ESXi para reencaminhar os registos para um único servidor Linux, como no exemplo seguinte.  

   ![fluxo de syslog](./media/log-analytics-vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Configurar a recolha do syslog
1. Configure o reencaminhamento de syslog para VSphere. Para obter informações detalhadas para o ajudar a configurar o reencaminhamento de syslog, consulte [configurar syslog no ESXi 5. x e 6.0 (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Aceda a **configuração de anfitrião ESXi** > **Software** > **definições avançadas** > **Syslog**.
   ![vsphereconfig](./media/log-analytics-vmware/vsphere1.png)  
2. No *Syslog.global.logHost* campo, adicione o servidor Linux e o número da porta *1514*. Por exemplo, `tcp://hostname:1514` ou`tcp://123.456.789.101:1514`
3. Abra a firewall do anfitrião ESXi para syslog. **Configuração do anfitrião ESXi** > **Software** > **perfil de segurança** > **Firewall** e abra  **Propriedades**.  

    ![vspherefw](./media/log-analytics-vmware/vsphere2.png)  

    ![vspherefwproperties](./media/log-analytics-vmware/vsphere3.png)  
4. Verifique o vSphere consola para verificar que esse syslog está corretamente configurado. Confirme no anfitrião ESXI essa porta **1514** está configurado.
5. Transfira e instale o agente do OMS para Linux no servidor do Linux. Para obter mais informações, consulte o [documentação do agente do OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).
6. Após a instalação do agente do OMS para Linux, vá para o diretório de /etc/opt/microsoft/omsagent/sysconf/omsagent.d e copie o ficheiro de vmware_esxi.conf para o diretório de /etc/opt/microsoft/omsagent/conf/omsagent.d e a alteração de permissões de proprietário/grupo e o ficheiro. Por exemplo:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
7. Reinicie o agente do OMS para Linux executando `sudo /opt/microsoft/omsagent/bin/service_control restart`.
8. Testar a conectividade entre o servidor Linux e o anfitrião ESXi utilizando o `nc` comando no anfitrião ESXi. Por exemplo:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

9. No Portal do OMS, efetuar uma pesquisa de registo para `Type=VMware_CL`. Quando OMS recolhe os dados de syslog, mantém o formato do syslog. No portal, alguns campos específicos são capturados, tais como *Hostname* e *ProcessName*.  

    ![tipo](./media/log-analytics-vmware/type.png)  

    Se os resultados da pesquisa ver registo são semelhantes a imagem acima, está a definir para utilizar o dashboard de solução de monitorização do OMS VMware.  

## <a name="vmware-data-collection-details"></a>Detalhes de recolha de dados do VMware
A solução de monitorização de VMware recolhe diversos dados de métricas e registo de desempenho dos anfitriões ESXi utilizando os agentes do OMS para Linux que tiver ativado.

A tabela seguinte mostra os métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos.

| Plataforma | Agente do OMS para Linux | Agente do SCOM | Storage do Azure | SCOM necessário? | Dados de agente do SCOM enviados através do grupo de gestão | Frequência de recolha |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |a cada 3 minutos |

A tabela seguinte mostra exemplos de campos de dados recolhidos pela solução de monitorização do VMware:

| Nome do campo | descrição |
| --- | --- |
| Device_s |Dispositivos de armazenamento do VMware |
| ESXIFailure_s |tipos de falha |
| EventTime_t |tempo quando o evento ocorreu |
| HostName_s |Nome de anfitrião ESXi |
| Operation_s |criar a VM ou eliminar VM |
| ProcessName_s |Nome do evento |
| ResourceId_s |nome do anfitrião do VMware |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |Estado de SCSI de VMware |
| SyslogMessage_s |Dados do syslog |
| UserName_s |utilizador criada ou eliminada VM |
| VMName_s |o nome da VM |
| Computador |computador anfitrião |
| TimeGenerated |hora que de dados foi gerados |
| DataCenter_s |Centro de dados do VMware |
| StorageLatency_s |Latência de armazenamento (ms) |

## <a name="vmware-monitoring-solution-overview"></a>Descrição geral da solução monitorização de VMware
É apresentada no mosaico do VMware no portal do OMS. Fornece uma vista de alto nível de eventuais falhas. Quando clica no mosaico, visite numa vista de dashboard.

![Mosaico](./media/log-analytics-vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Navegue até a vista do dashboard
No **VMware** vista do dashboard, painéis estão organizados por:

* Número de falhas de estado
* Anfitrião superior ao número de eventos
* Contagens de principais de eventos
* Atividades de máquinas virtuais
* Eventos do anfitrião ESXi do disco

![solution1](./media/log-analytics-vmware/solutionview1-1.png)

![solution2](./media/log-analytics-vmware/solutionview1-2.png)

Clique em qualquer painel para abrir o painel de pesquisa de análise de registos que mostra informações detalhadas específico para o painel.

Aqui, pode editar a consulta de pesquisa para modificá-lo para algo mais específico. Para um tutorial sobre as noções básicas de pesquisa do OMS, veja o [tutorial de pesquisa de registo do OMS.](log-analytics-log-searches.md)

#### <a name="find-esxi-host-events"></a>Encontrar eventos de anfitrião ESXi
Um único anfitrião ESXi gera vários registos, com base nos respetivos processos. A solução de monitorização de VMware centraliza-los e resume o número de eventos. Esta vista centralizada ajuda-o a compreender que anfitrião ESXi tem um elevado volume de eventos e os eventos ocorrem com maior frequência no seu ambiente.

![evento](./media/log-analytics-vmware/events.png)

Pode desagregar adicionais, clicando num anfitrião ESXi ou um tipo de evento.

Ao clicar um nome de anfitrião ESXi, visualiza informações a partir desse anfitrião ESXi. Se pretender limitar os resultados com o tipo de evento, adicione `“ProcessName_s=EVENT TYPE”` na sua consulta de pesquisa. Pode selecionar **ProcessName** no filtro de procura. Que restringe as informações por si.

![desagregar](./media/log-analytics-vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Localizar atividades elevadas de VM
Uma máquina virtual pode ser criada e eliminada em qualquer anfitrião ESXi. É útil para um administrador identificar quantas VMs cria um anfitrião ESXi. Esse por sua vez, ajuda a compreender o desempenho e o planeamento de capacidade. Manter um registo dos eventos de atividade VM é crucial ao gerir o seu ambiente.

![desagregar](./media/log-analytics-vmware/vmactivities1.png)

Se pretender ver adicionais ESXi anfitrião VM criação dados, clique num nome de anfitrião ESXi.

![desagregar](./media/log-analytics-vmware/createvm.png)

#### <a name="common-search-queries"></a>Consultas de pesquisa comuns
A solução inclui outras consultas útil que podem ajudar a gerir os seus anfitriões ESXi, como o espaço de armazenamento de elevado, latência de armazenamento e falha de caminho.

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

![Consultas](./media/log-analytics-vmware/queries.png)


#### <a name="save-queries"></a>Guardar consultas
Guardar consultas de pesquisa é uma funcionalidade padrão no OMS e pode ajudar a manter todas as consultas que encontrou útil. Depois de criar uma consulta que úteis, guarde-o ao clicar no **Favoritos**. Uma consulta guardada permite-lhe facilmente reutilizá-lo mais tarde a partir de [My Dashboard](log-analytics-dashboards.md) página onde pode criar os seus próprios dashboards personalizados.

![DockerDashboardView](./media/log-analytics-vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Criar alertas a partir de consultas
Depois de criar as suas consultas, pode querer utilizar consultas para o alertar quando ocorrem eventos específicos. Consulte [alertas na análise de registos](log-analytics-alerts.md) para obter informações sobre como criar alertas. Para obter exemplos de alertas, consultas e outros exemplos de consultas, consulte o [VMware Monitor utilizando a análise de registos do OMS](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) blogue.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>O que é necessário no ESXi anfitrião definição? Que impacto terão-lo no meu ambiente atual?
A solução utiliza o Syslog de anfitrião ESXi nativo mecanismo de reencaminhamento. Não necessita de qualquer software da Microsoft adicional no anfitrião ESXi para capturar os registos. Deve ter um impacto baixo ao seu ambiente existente. No entanto, tem de definir o encaminhamento de syslog, que é a funcionalidade ESXI.

### <a name="do-i-need-to-restart-my-esxi-host"></a>É necessário reiniciar o meu anfitrião ESXi?
Não. Este processo não requer um reinício. Por vezes, o vSphere não atualiza corretamente o syslog. Nesse caso, inicie sessão para o anfitrião ESXi e recarregar o syslog. Novamente, não terá de reiniciar o anfitrião, pelo que este processo não acontece no seu ambiente.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-oms"></a>Pode aumentar ou diminuir o volume de dados de registo enviados para OMS?
Sim, pode. Pode utilizar as definições de nível de registo de anfitrião ESXi vSphere. Recolha de registos baseia-se no *informações* nível. Por isso, se pretender que a auditoria da VM é criada ou eliminada, terá de manter o *informações* nível no Hostd. Para obter mais informações, consulte o [Base de dados de conhecimento do VMware](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-oms-my-log-setting-is-set-to-info"></a>Por que motivo é Hostd não fornecer dados ao OMS? A minha definição de registo está definida como informações.
Ocorreu um erro de anfitrião ESXi para o carimbo de syslog. Para obter mais informações, consulte o [Base de dados de conhecimento do VMware](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). Depois de aplicar a solução, Hostd deverá funcionar normalmente.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Pode ter vários anfitriões ESXi a reencaminhar dados do syslog para uma única VM com omsagent?
Sim. Pode ter vários anfitriões ESXi a reencaminhar para uma única VM com omsagent.

### <a name="why-dont-i-see-data-flowing-into-oms"></a>Por que motivo não vejo dados que fluem para OMS?
Pode ser por vários motivos:

* O anfitrião ESXi é não corretamente enviar por push dados para a VM em execução omsagent. Para testar, execute os seguintes passos:

  1. Para confirmar, inicie sessão para o anfitrião ESXi utilizando ssh e execute o seguinte comando:`nc -z ipaddressofVM 1514`

      Se não for bem-sucedida, são provável que vSphere definições numa configuração avançada corrigir. Consulte [configurar a recolha de syslog](#configure-syslog-collection) para obter informações sobre como configurar o anfitrião ESXi para reencaminhamento syslog.
  2. Se a conectividade de porta de syslog é efetuada com êxito, mas ainda não vê os dados, recarregar o syslog no anfitrião ESXi utilizando ssh para executar o seguinte comando:` esxcli system syslog reload`
* A VM com o agente do OMS não está definida corretamente. Para testar isto, execute os seguintes passos:

  1. OMS escuta a porta 1514 e pushes de dados no OMS. Para verificar que está aberta, execute o seguinte comando:`netstat -a | grep 1514`
  2. Deverá ver porta `1514/tcp` abrir. Se não o fizer, certifique-se de que o omsagent está corretamente instalado. Se não visualizar as informações de porta, a porta de syslog não está aberta na VM.

     1. Certifique-se de que o agente do OMS está em execução utilizando `ps -ef | grep oms`. Se não está em execução, inicie o processo executando o comando` sudo /opt/microsoft/omsagent/bin/service_control start`
     2. Abra o ficheiro `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf`.

         Certifique-se de que o utilizador adequado e a definição de grupo é válido, semelhante a:`-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

         Se o ficheiro não existe ou o utilizador e a definição de grupo está errada, tomar uma ação corretiva por [preparar um servidor Linux](#prepare-a-linux-server).

## <a name="next-steps"></a>Passos seguintes
* Utilize [pesquisas de registo](log-analytics-log-searches.md) na análise de registos para ver o VMware detalhada alojam os dados.
* [Criar os seus próprios dashboards](log-analytics-dashboards.md) mostrar os dados de anfitrião do VMware.
* [Criar alertas](log-analytics-alerts.md) quando ocorrem eventos específicos de anfitriões de VMware.
