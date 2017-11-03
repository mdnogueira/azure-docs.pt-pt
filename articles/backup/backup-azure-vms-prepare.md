---
title: "Preparar o ambiente para fazer cópias de segurança de máquinas virtuais do Azure | Microsoft Docs"
description: "Certifique-se de que o seu ambiente está preparado para fazer uma cópia de segurança de máquinas virtuais no Azure"
services: backup
documentationcenter: 
author: markgalioto
manager: carmonn
editor: 
keywords: "cópias de segurança; cópia de segurança;"
ms.assetid: 238ab93b-8acc-4262-81b7-ce930f76a662
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/10/2017
ms.author: markgal;trinadhk;
ms.openlocfilehash: 35b40f80c5a9ccc67830429a5a75d2974d0d138c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="prepare-your-environment-to-back-up-azure-virtual-machines"></a>Preparar o seu ambiente para fazer cópias de segurança de máquinas virtuais do Azure
> [!div class="op_single_selector"]
> * [Modelo do Resource Manager](backup-azure-arm-vms-prepare.md)
> * [Modelo clássico](backup-azure-vms-prepare.md)
>
>

Antes de pode efetuar cópias de segurança de uma máquina virtual do Azure (VM), existem três condições que têm de existir.

* Terá de criar um cofre de cópia de segurança ou identifique um cofre de cópias de segurança existente *na mesma região que a VM*.
* Estabelece conectividade de rede entre os endereços de Internet públicos do Azure e os pontos finais de armazenamento do Azure.
* Instale o agente da VM na VM.

Se conhecer estas condições já existem no seu ambiente, em seguida, avance para o [cópia de segurança do artigo VMs](backup-azure-vms.md). Caso contrário, continue a ler, este artigo irá orientá-lo os passos para preparar o ambiente para fazer cópias de segurança de uma VM do Azure.

##<a name="supported-operating-system-for-backup"></a>Sistema operativo suportado para cópia de segurança
 * **Linux**: o Azure Backup suporta [uma lista de distribuições apoiadas pelo Azure](../virtual-machines/linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), exceto Core OS Linux. _Outras Bring-Your-proprietário-as distribuições do Linux também podem funcionar desde que o agente VM está disponível na máquina virtual e suporte para o Python existe. No entanto, não podemos apoia esses distribuições para cópia de segurança._
 * **Windows Server**: não são suportadas as versões mais antigas do que o Windows Server 2008 R2.


## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>Limitações ao fazer cópias de segurança e restauro de uma VM
> [!NOTE]
> O Azure tem dois modelos de implementação para criar e trabalhar com recursos: [Resource Manager e clássico](../azure-resource-manager/resource-manager-deployment-model.md). A lista seguinte fornece as limitações ao implementar o modelo clássico.
>
>

* Não é suportada a cópia de segurança de máquinas virtuais com mais de 16 discos de dados.
* Não é suportada a cópia de segurança de máquinas virtuais com um endereço IP reservado e nenhum ponto final de definidos.
* Dados de cópia de segurança não incluem unidades de rede montado ligadas à VM.
* A substituição de uma máquina virtual existente durante o restauro não é suportada. Primeiro eliminar a máquina virtual existente e quaisquer discos associados e, em seguida, restaure os dados da cópia de segurança.
* Não é suportado por várias regiões cópia de segurança e restauro.
* Fazer uma cópia de segurança de máquinas virtuais utilizando o serviço de cópia de segurança do Azure é suportada em todas as regiões públicas do Azure (consulte o [lista de verificação](https://azure.microsoft.com/regions/#services) de regiões suportadas). Se a região que procura não é suportado atualmente, não serão apresentados na lista pendente durante a criação do cofre.
* Fazer uma cópia de segurança de máquinas virtuais utilizando o serviço de cópia de segurança do Azure é suportada apenas para versões do sistema de operativo selecione:
* Restaurar um controlador de domínio (DC) VM que faz parte de uma configuração de várias DC é suportada apenas através do PowerShell. Leia mais sobre [restaurar um controlador de domínio do DC várias](backup-azure-restore-vms.md#restoring-domain-controller-vms).
* Máquinas virtuais que têm as seguintes configurações de rede especiais o restauro é suportado apenas através do PowerShell. As VMs que criar utilizando o fluxo de trabalho de restauro na IU não terá estas configurações de rede após a conclusão da operação de restauro. Para obter mais informações, consulte [restaurar VMs com configurações de rede especiais](backup-azure-restore-vms.md#restoring-vms-with-special-network-configurations).
  * Máquinas virtuais em configuração de Balanceador de carga (interna e externa)
  * Máquinas virtuais com vários endereços IP reservados
  * Máquinas virtuais com vários adaptadores de rede

## <a name="create-a-backup-vault-for-a-vm"></a>Criar um cofre de cópia de segurança para uma VM
Um cofre de cópia de segurança é uma entidade que armazena todas as cópias de segurança e os pontos de recuperação que foram criados ao longo do tempo. O Cofre de cópia de segurança também contém as políticas de cópia de segurança que serão aplicadas às máquinas virtuais a cópia de segurança.

> [!IMPORTANT]
> A partir de março de 2017, já não pode utilizar o portal clássico para criar cofres de Cópia de Segurança. Os cofres de Cópia de Segurança existentes continuam a ser suportados e é possível [utilizar o Azure PowerShell para criar cofres de Cópia de Segurança](./backup-client-automation-classic.md#create-a-backup-vault). No entanto, a Microsoft recomenda a criação de cofres de Serviços de Recuperação para todas as implementações, uma vez que os melhoramentos futuros se aplicam apenas a cofres de Serviços de Recuperação.


Esta imagem mostra as relações entre as várias entidades de cópia de segurança do Azure: ![entidades de cópia de segurança do Azure e relações](./media/backup-azure-vms-prepare/vault-policy-vm.png)



## <a name="network-connectivity"></a>Conectividade de rede
Para gerir os instantâneos VM, a extensão de cópia de segurança necessita de conectividade para os endereços IP públicos do Azure. Sem o acesso correta à Internet, o tempo limite de pedidos de HTTP da máquina virtual e a operação de cópia de segurança falhará. Se a sua implementação tem restrições de acesso no local (através de um grupo de segurança de rede (NSG), por exemplo), em seguida, escolha uma destas opções para fornecer um caminho de simples para o tráfego de cópia de segurança:

* [Lista branca de IP do datacenter do Azure intervalos](http://www.microsoft.com/en-us/download/details.aspx?id=41653) -consulte o artigo para obter instruções sobre como a lista branca os endereços IP.
* Implemente um servidor de proxy HTTP para o encaminhamento de tráfego.

Ao decidir qual a opção a utilizar, são os compromissos entre capacidade de gestão, o controlo granular e o custo.

| Opção | Vantagens | Desvantagens |
| --- | --- | --- |
| Intervalos de IP da lista branca |Sem custos adicionais.<br><br>Para abrir o acesso num NSG, utilize o <i>conjunto AzureNetworkSecurityRule</i> cmdlet. |Complexo de gerir como a estando afetadas alteram os intervalos de IP ao longo do tempo.<br><br>Fornece acesso a totalidade do Azure e não apenas o armazenamento. |
| Proxy HTTP |Controlo granular no proxy através de URLs de armazenamento permitido. Para o controlo granular sobre o programa de configuração no proxy, https://\*.blob.core.windows.net/\* padrão de URL tem de estar na lista de permissões. A lista branca apenas a conta de armazenamento utilizada pela VM, https://\<storageAccount\>.blob.core.windows.net/\* padrão de URL tem de estar na lista de permissões. <br>Único ponto de acesso à Internet às VMs.<br>Não sujeitos a alterações do endereço IP do Azure. |Custos adicionais para executar uma VM com o software de proxy. |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>Intervalos de IP da lista branca de datacenter do Azure
A lista de permissões os intervalos IP do datacenter do Azure, consulte o [Web site Azure](http://www.microsoft.com/en-us/download/details.aspx?id=41653) para obter detalhes sobre os intervalos de IP e instruções.

### <a name="using-an-http-proxy-for-vm-backups"></a>Utilizar um proxy HTTP para cópias de segurança VM
Ao fazer cópias de segurança de uma VM, a extensão de cópia de segurança na VM envia os comandos de gestão do instantâneo ao Storage do Azure para utilizar uma API de HTTPS. Encaminhar o tráfego de extensão de cópia de segurança através do proxy HTTP, uma vez que é o único componente configurado para acesso à Internet pública.

> [!NOTE]
> Não há nenhuma recomendação para o software de proxy que deve ser utilizada. Certifique-se que escolha um proxy que tenha saída retenções e que é compatível com os passos de configuração abaixo. Certifique-se de softwares de terceiros não modificar as definições de proxy
>
>

A imagem de exemplo abaixo mostra os passos de três configuração necessárias para utilizar um proxy HTTP:

* VM de aplicação encaminha todo o tráfego HTTP vinculado a Internet pública através de VM de Proxy.
* Proxy VM permite o tráfego de entrada de VMs na rede virtual.
* O grupo de segurança de rede (NSG) com o nome de bloqueio NSF tem um segurança regra que permite saído tráfego de Internet de VM de Proxy.

![NSG com diagrama de implementação do proxy HTTP](./media/backup-azure-vms-prepare/nsg-with-http-proxy.png)

Para utilizar um proxy HTTP para comunicar com a Internet pública, siga estes passos:

#### <a name="step-1-configure-outgoing-network-connections"></a>Passo 1. Configurar ligações de rede de saída
###### <a name="for-windows-machines"></a>Para máquinas do Windows
Isto irá configurar a configuração do servidor proxy para a conta de sistema Local.

1. Transferir [PsExec](https://technet.microsoft.com/sysinternals/bb897553)
2. Execute o seguinte comando na linha de comandos elevada,

     ```
     psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
     ```
     Irá abrir a janela do internet explorer.
3. Aceda a ferramentas -> Opções da Internet -> ligações -> definições de LAN.
4. Verifique as definições de proxy para a conta de sistema. Definir o IP de Proxy e a porta.
5. Feche o Internet Explorer.

Isto irá configurar uma configuração de proxy a nível de máquina e será utilizado para enviar tráfego HTTP/HTTPS.

Se tiver a configuração de um servidor proxy numa conta de utilizador atual (não uma conta de sistema Local), utilize o seguinte script para aplicá-las para SYSTEMACCOUNT:

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> Se observar "(407) a autenticação de Proxy necessário" no registo de servidor proxy, verifique a que sua autenticação está configurada corretamente.
>
>

###### <a name="for-linux-machines"></a>Para computadores Linux
Adicione a seguinte linha para o ```/etc/environment``` ficheiro:

```
http_proxy=http://<proxy IP>:<proxy port>
```

Adicione as seguintes linhas à ```/etc/waagent.conf``` ficheiro:

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>Passo 2. Permitir ligações de entrada no servidor de proxy:
1. No servidor de proxy, abra a Firewall do Windows. É a forma mais fácil de aceder a firewall para procurar a Firewall do Windows com segurança avançada.

    ![Abrir a Firewall](./media/backup-azure-vms-prepare/firewall-01.png)
2. Na caixa de diálogo a Firewall do Windows, clique com botão direito **regras de entrada** e clique em **nova regra...** .

    ![Criar uma nova regra](./media/backup-azure-vms-prepare/firewall-02.png)
3. No **novo Assistente de regras de entrada**, escolha o **personalizada** opção para o **tipo de regra** e clique em **seguinte**.
4. Na página para selecionar o **programa**, escolha **todos os programas** e clique em **seguinte**.
5. No **protocolo e portas** página, introduza as seguintes informações e clique em **seguinte**:

    ![Criar uma nova regra](./media/backup-azure-vms-prepare/firewall-03.png)

   * para *tipo de protocolo* escolha *TCP*
   * para *Porta Local* escolha *portas específicas*, no campo abaixo, especifique o ```<Proxy Port>``` que foi configurado.
   * para *portas remotas* selecione *todas as portas*

     Para o resto do assistente, clique em até ao fim e atribua um nome esta regra.

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>Passo 3. Adicione uma regra de exceção para o NSG:
Numa linha de comandos do Azure PowerShell, introduza o seguinte comando:

O comando seguinte adiciona uma exceção para o NSG. Esta exceção permite tráfego TCP a partir de qualquer porta no 10.0.0.5 para qualquer endereço Internet na porta 80 (HTTP) ou 443 (HTTPS). Se necessitar de uma porta específica na Internet pública, lembre-se de que adicione essa porta para o ```-DestinationPortRange``` bem.

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

*Certifique-se de que substitua os nomes de exemplo com os detalhes adequados à sua implementação.*

## <a name="vm-agent"></a>Agente VM
Antes de pode efetuar cópias de segurança da máquina virtual do Azure, deve certificar-se de que o agente da VM do Azure está corretamente instalado na máquina virtual. Uma vez que o agente VM a um componente opcional momento em que a máquina virtual é criada, certifique-se de que a caixa de verificação para o agente VM está selecionada antes da máquina virtual estar aprovisionada.

### <a name="manual-installation-and-update"></a>Instalação manual e atualização
O agente da VM já se encontra presente no VMs que são criadas a partir da galeria do Azure. No entanto, as máquinas virtuais que são migradas dos centros de dados no local não teriam o agente da VM instalado. Para essas VMs, o agente da VM tem de ser instalada explicitamente. 

| **Operação** | **Windows** | **Linux** |
| --- | --- | --- |
| Instalar o Agente da VM |Transfira e instale o [MSI do agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Necessita de privilégios de Administrador para concluir o processo de instalação.<li>[Atualize a propriedade da VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado. |<li> Instalar a versão mais recente [agente Linux](../virtual-machines/linux/agent-user-guide.md). Necessita de privilégios de Administrador para concluir o processo de instalação. Recomendamos que instale o agente do seu repositório de distribuição. Iremos **não é recomendada a** instalar agente de VM com Linux diretamente a partir do github.  |
| Atualizar o Agente da VM |A atualização do Agente da VM é tão simples como reinstalar os [binários do Agente da VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). <br>Certifique-se de que nenhuma operação de cópia de segurança está em execução enquanto o agente da VM está a ser atualizado. |Siga as instruções para [atualizar o Agente da VM do Linux ](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Recomendamos a atualizar o agente do seu repositório de distribuição. Iremos **não é recomendada a** atualizar agente de VM com Linux diretamente a partir do github.<br>Certifique-se de que nenhuma operação de cópia de segurança está em execução enquanto o Agente da VM está a ser atualizado. |
| A validar a instalação do Agente da VM |<li>Navegue até à pasta *C:\WindowsAzure\Packages* na VM do Azure. <li>Deve encontrar o ficheiro WaAppAgent.exe presente.<li> Clique com o botão direito do rato no ficheiro, aceda a **Propriedades** e, em seguida, selecione o separador **Detalhes**. O campo da Versão do Produto deve ser 2.6.1198.718 ou superior. |N/D |

Saiba mais sobre o [agente da VM](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409) e [como instalá-lo](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/).

### <a name="backup-extension"></a>Extensão da cópia de segurança
Para fazer uma cópia de segurança da máquina virtual, o serviço de cópia de segurança do Azure instala uma extensão para o agente da VM. O serviço de Backup do Azure atualiza de forma totalmente integrada e aplica os patches à extensão da cópia de segurança sem intervenção adicional do utilizador.

A extensão de cópia de segurança está instalada se a VM está em execução. Uma VM em execução também fornece a maior possibilidade de obter um ponto de recuperação consistentes com aplicações. No entanto, a cópia de segurança do Azure serviço continuará a fazer cópias de segurança a VM –, mesmo se está a ser desativada e a extensão não pode ser instalado (também conhecido como VM Offline). Neste caso, o ponto de recuperação será *consistente com a falha* conforme mencionado acima.

## <a name="questions"></a>Tem dúvidas?
Se tiver dúvidas ou se houver alguma funcionalidade que gostaria de ver incluída, [envie-nos comentários](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Passos seguintes
Agora que tem de preparar o ambiente para fazer cópias de segurança a VM, o próximo passo lógico é criar uma cópia de segurança. O planeamento artigo fornece informações mais detalhadas sobre a cópia de segurança de VMs.

* [Fazer uma cópia de segurança de máquinas virtuais](backup-azure-vms.md)
* [Planear a infraestrutura de cópia de segurança de VM](backup-azure-vms-introduction.md)
* [Gerir cópias de segurança da máquina virtual](backup-azure-manage-vms.md)
