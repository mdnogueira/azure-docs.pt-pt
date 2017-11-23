---
title: "Resolver problemas de proteção de falhas VMware/físico para o Azure | Microsoft Docs"
description: "Este artigo descreve as falhas de replicação de máquina VMware comuns e como resolvê-los"
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: asgang
ms.openlocfilehash: 7a8cd09731ccdf7ad7385f7e707125c3902ebdf2
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="troubleshoot-on-premises-vmwarephysical-server-replication-issues"></a>Resolver problemas de replicação do servidor de VMware/físico no local
Poderá receber uma mensagem de erro específico ao proteger as máquinas virtuais VMware ou servidores físicos, utilizando o Azure Site Recovery. Este artigo fornece detalhes sobre algumas das mensagens de erro mais comuns encontradas, juntamente com os passos para resolvê-los de resolução de problemas.


## <a name="initial-replication-is-stuck-at-0"></a>A replicação inicial está encravada % 0
A maioria das falhas de replicação inicial que estamos a encontrar no suporte são devido a problemas de conectividade entre o servidor de processo do servidor de origem ou processo servidor para o Azure.
Para a maioria dos casos, pode resolver estes problemas, seguindo os passos listados abaixo.

###<a name="check-the-following-on-source-machine"></a>Verifique o seguinte na máquina de origem
* A partir de linha de comandos de máquina do servidor de origem, utilize Telnet ping do servidor de processos com a porta https (predefinição 9443), conforme mostrado abaixo para ver se existem quaisquer problemas de conectividade de rede ou problemas de bloqueios de porta de firewall.

    `telnet <PS IP address> <port>`
> [!NOTE]
    > Utilizar o Telnet, não utilize PING para testar a conectividade.  Se o Telnet não estiver instalado, siga a lista de passos [aqui](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)

Se não for possível estabelecer ligação, permitir que a porta de entrada 9443 no servidor de processos e verifique se o problema ainda sai. Foi alguns casos em que o servidor de processos foi atrás de rede de Perímetro, o qual foi fazendo com que este problema.

* Verifique o estado do serviço `InMage Scout VX Agent – Sentinel/OutpostStart` se não estiver em execução e verifique se o problema ainda existe.   

###<a name="check-the-following-on-process-server"></a>Verifique o seguinte no servidor de processos

* **Verifique se o servidor de processo é ativamente enviar por push dados para o Azure**

A partir da máquina do servidor de processos, abra o Gestor de tarefas (prima Ctrl-Shift-Esc). Aceda ao separador de desempenho e clique em ligação 'Abra Monitor de recursos'. No Gestor de recursos, aceda ao separador de rede. Verifique se cbengine.exe em 'Processos com a atividade de rede' está a enviar ativamente grande volume (em MB) de dados.

![Ativar a replicação](./media/site-recovery-protection-common-errors/cbengine.png)

Caso contrário, siga os passos listados abaixo:

* **Verifique se o servidor de processos é capaz de ligar o Blob do Azure**: selecione e verifique cbengine.exe para ver as 'ligações TCP' para verificar se existe conectividade do servidor de processos ao URL do blob Storage do Azure.

![Ativar a replicação](./media/site-recovery-protection-common-errors/rmonitor.png)

Se não, em seguida, aceda ao painel de controlo > serviços, verifique se os seguintes serviços estão em execução:

     * cxprocessserver
     * InMage Scout VX Agent – Sentinel/Outpost
     * Microsoft Azure Recovery Services Agent
     * Microsoft Azure Site Recovery Service
     * tmansvc
     *
(Re) Inicie qualquer serviço, que não está em execução e verifique se o problema ainda existe.

* **Verifique se o servidor de processos é capaz de ligar ao endereço IP público do Azure através da porta 443**

Abra o CBEngineCurr.errlog mais recente do `%programfiles%\Microsoft Azure Recovery Services Agent\Temp` e procure: 443 e ligação tentativa falhada.

![Ativar a replicação](./media/site-recovery-protection-common-errors/logdetails1.png)

Se existirem problemas, em seguida, a partir da linha de comandos do servidor de processos, utilizam telnet ping o seu endereço de IP público do Azure (mascarado nos acima imagem) foi encontrado no CBEngineCurr.currLog através da porta 443.

      telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443
Se não for possível estabelecer ligação, em seguida, verifique se o problema de acesso devido a firewall ou Proxy, tal como descrito no passo seguinte.


* **Verifique se firewall de baseadas no endereço IP no servidor de processos não está a bloquear o acesso**: Se estiver a utilizar um regras de firewall baseadas no endereço IP no servidor, em seguida, transferir a lista completa de Microsoft Azure Datacenter intervalos de IP do [aqui](https://www.microsoft.com/download/details.aspx?id=41653) e adicioná-los à sua configuração de firewall para se certificar de que permitem a comunicação com o Azure (e a porta HTTPS (443)).  Permita os intervalos de endereços IP da região do Azure da sua subscrição e para a região E.U.A. Oeste (utilizada para Controlo de Acesso e Gestão de Identidades).

* **Verifique se firewall baseada no URL no servidor de processos não está a bloquear o acesso**: Se estiver a utilizar regras de firewall baseada no URL no servidor, certifique-se os seguintes URLs são adicionados à configuração da firewall.

  `*.accesscontrol.windows.net:` Utilizado para controlo de acesso e gestão de identidades

  `*.backup.windowsazure.com:` Utilizado para transferência de dados de replicação e orquestração

  `*.blob.core.windows.net:`Utilizado para acesso à conta de armazenamento que armazena os dados replicados

  `*.hypervrecoverymanager.windowsazure.com:` Utilizado para operações e gestão de replicação e orquestração

  `time.nist.gov`e `time.windows.com`: utilizado para verificar a sincronização de hora entre o sistema e a hora global.

URLs para **Cloud do Azure Government**:

`* .ugv.hypervrecoverymanager.windowsazure.us`

`* .ugv.backup.windowsazure.us`

`* .ugi.hypervrecoverymanager.windowsazure.us`

`* .ugi.backup.windowsazure.us`

* **Verifique se as definições de Proxy no servidor de processos não estão a bloquear o acesso**.  Se estiver a utilizar um servidor Proxy, certifique-se de que o nome do servidor proxy está a resolver pelo servidor DNS.
Para verificar que forneceu no momento da configuração do servidor de configuração. Aceda a chave de registo

    `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings`

Agora Certifique-se de que as mesmas definições estão a ser utilizadas pelo agente do Azure Site Recovery para enviar dados.
Cópia de segurança do Microsoft Azure Search

![Ativar a replicação](./media/site-recovery-protection-common-errors/mab.png)

Abra-o e clique na ação > alterar propriedades. No separador de configuração de Proxy, deverá ver o endereço de proxy, que deve ser a mesmo, conforme exibido as definições de registo. Caso contrário, mude para o mesmo endereço.

![Ativar a replicação](./media/site-recovery-protection-common-errors/mabproxy.png)

* **Verifique se a largura de banda de limitação não é restrita no servidor de processos**: aumentar a largura de banda e verifique se o problema ainda existe.

##<a name="next-steps"></a>Passos seguintes
Se precisar de mais ajuda, em seguida, publicar a sua consulta [fórum do Azure Site Recovery](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Temos uma Comunidade de Active Directory e um dos nossos engenheiros conseguirá ajudá-lo.
