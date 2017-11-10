---
title: "Resolver erros de cópia de segurança com a máquina virtual do Azure | Microsoft Docs"
description: "Resolver problemas de cópia de segurança e restauro de máquinas virtuais do Azure"
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: 73214212-57a4-4b57-a2e2-eaf9d7fde67f
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: trinadhk;markgal;jpallavi;
ms.openlocfilehash: 5c4ea3e3714f6a3989a260937c2c67815a6dd6f7
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Resolver problemas das cópias de segurança de máquina virtuais do Azure
> [!div class="op_single_selector"]
> * [Cofre de serviços de recuperação](backup-azure-vms-troubleshoot.md)
> * [Cofre de cópia de segurança](backup-azure-vms-troubleshoot-classic.md)
>
>

Pode resolver erros encontrados ao utilizar o Backup do Azure com as informações apresentadas na tabela abaixo.

## <a name="backup"></a>Cópia de segurança

### <a name="error-the-specified-disk-configuration-is-not-supported"></a>Erro: Não é suportada a configuração de disco especificado

> [!NOTE]
> Temos uma versão de pré-visualização privada para suportar cópias de segurança para VMs com > discos de 1TB não gerido. Para obter detalhes, consulte [pré-visualização privada para o suporte de cópia de segurança de VM de disco grande](https://gallery.technet.microsoft.com/Instant-recovery-point-and-25fe398a)
>
>

Cópia de segurança do Azure não suporta atualmente os tamanhos de disco [superior a 1023GB](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms-prepare#limitations-when-backing-up-and-restoring-a-vm). 
- Se tiver discos superiores a 1 TB, [anexar novos discos](https://docs.microsoft.com/azure/virtual-machines/windows/attach-managed-disk-portal) que são inferior a 1 TB <br>
- Em seguida, copie os dados a partir de disco superior a 1TB para recém-criado discos de tamanho inferior a 1 TB. <br>
- Certifique-se de que todos os dados foram copiados e remova os discos superiores a 1TB
- Inicie a cópia de segurança.

| Detalhes do erro | Solução |
| --- | --- |
| Não foi possível executar a operação visto que a VM já não existe. -Pare a proteção de máquina virtual sem eliminar dados de cópia de segurança. Obter mais detalhes em http://go.microsoft.com/fwlink/?LinkId=808124 |Isto acontece quando a VM principal é eliminado mas a política de cópia de segurança continua à procura de uma VM para criar cópias de segurança. Para corrigir este erro: <ol><li> Recriar a máquina virtual com o mesmo nome e o mesmo nome de grupo de recursos [nome do serviço de nuvem]<br>(OU)</li><li> Pare a proteção de máquinas virtuais com ou sem eliminar os dados de cópia de segurança. [Obter mais detalhes](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol> |
| Operação de instantâneos falhou devido a sem conectividade de rede na máquina virtual - Certifique-se de que a VM tem acesso à rede. Para instantâneo tenha êxito, o IP do datacenter do Azure da lista branca intervalos ou configurar um servidor proxy para acesso à rede. Para obter mais detalhes, consulte a http://go.microsoft.com/fwlink/?LinkId=800034. Se já estiver a utilizar o servidor proxy, certifique-se de que as definições do servidor proxy estão configuradas corretamente | Este erro é apresentado quando a negar o acesso à internet saída na máquina virtual. A conectividade à Internet é necessária para a extensão de instantâneos VM criar um instantâneo de subjacentes discos da máquina virtual. [Saiba mais](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#snapshot-operation-failed-due-to-no-network-connectivity-on-the-virtual-machine) sobre como corrigir falhas de instantâneos devido a acesso à rede bloqueadas. |
| Agente VM não é possível comunicar com o serviço de cópia de segurança do Azure. -Certifique-se a VM possui conectividade de rede e o agente VM é mais recente e em execução. Para obter mais informações, consulte http://go.microsoft.com/fwlink/?LinkId=800034 |Este erro é apresentado se ocorrer um problema com o agente da VM ou acesso de rede para a infraestrutura do Azure é bloqueado de alguma forma. [Saiba mais](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#vm-agent-unable-to-communicate-with-azure-backup) sobre a depuração de VM problemas de instantâneos.<br> Se o agente da VM não está a causar problemas de, em seguida, reinicie a VM. Por vezes um Estado VM incorreto pode causar problemas e reiniciar a VM repõe este "Estado incorreto". |
| VM se encontra em estado de aprovisionamento falhou - reiniciar a VM e certifique-se de que a VM está no estado de execução ou encerrar pendente para cópia de segurança | Isto ocorre quando uma das falhas extensão servem como o estado VM para estar no estado de aprovisionamento com falhas. Aceda à lista de extensões e ver se existe uma extensão de falha, removê-la e tente reiniciar a máquina virtual. Se todas as extensões estão em estado de execução, verifique se o serviço de agente VM está em execução. Caso contrário, reinicie o serviço de agente VM. | 
| A operação de extensão de VMSnapshot falhou para discos geridos -. Repita a operação de cópia de segurança. Se o problema repete, siga as instruções apresentadas em 'http://go.microsoft.com/fwlink/?LinkId=800034'. Se ainda mais falhar, contacte o suporte da Microsoft | Este erro quando ocorre uma falha do serviço de cópia de segurança do Azure acionar um instantâneo. [Saiba mais](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md#vmsnapshot-extension-operation-failed) sobre depuração VM problemas de instantâneos. |
| Foi possível copiar o instantâneo da máquina virtual, devido a insuficiente espaço livre na conta de armazenamento - Certifique-se de que a conta de armazenamento tiver espaço livre equivalente para os dados presentes nos discos de armazenamento premium ligados à máquina virtual | Em caso de VMs do premium, iremos copiar o instantâneo para a conta de armazenamento. Isto é certificar-se de que o tráfego de gestão de cópia de segurança, o que funciona instantâneo, não limita o número de IOPS disponíveis para a aplicação utilizar discos premium. A Microsoft recomenda que atribuir apenas 50% de espaço de conta de armazenamento total para que o serviço de cópia de segurança do Azure, pode copiar o instantâneo para a conta e a transferência de dados de armazenamento nesta localização copiado na conta de armazenamento para o cofre. | 
| Não é possível efetuar a operação dado que o agente da VM não está a responder |Este erro é apresentado se ocorrer um problema com o agente da VM ou acesso de rede para a infraestrutura do Azure é bloqueado de alguma forma. Para VMs do Windows, verifique o estado de serviço do agente VM nos serviços e se o agente é apresentado em programas no painel de controlo. Tente remover o programa do controlo painel e volte a instalar o agente, tal como mencionado [abaixo](#vm-agent). Depois de reinstalar o agente, acione uma cópia de segurança ad hoc para verificar. |
| Falha na operação de extensão de serviços de recuperação. -Certifique-se de que o agente mais recente da máquina virtual está presente na máquina virtual e serviço de agente está em execução. Repita a operação de cópia de segurança e se falhar, contacte o suporte da Microsoft. |Este erro é apresentado quando o agente VM está desatualizada. Consulte a secção "Atualizar o agente da VM" abaixo para atualizar o agente da VM. |
| Máquina virtual não existe. -Certifique-se de que a máquina virtual existe ou selecione outra máquina virtual. |Isto acontece quando a VM principal é eliminada, mas continua a política de cópia de segurança procurar uma VM efetuar a cópia de segurança. Para corrigir este erro: <ol><li> Recriar a máquina virtual com o mesmo nome e o mesmo nome de grupo de recursos [nome do serviço de nuvem]<br>(OU)<br></li><li>Pare a proteção da máquina virtual sem eliminar os dados de cópia de segurança. [Obter mais detalhes](http://go.microsoft.com/fwlink/?LinkId=808124)</li></ol> |
| Falha na execução de comando. -Outra operação está atualmente em curso neste item. Aguarde até que a anterior operação esteja concluída e tente novamente |Uma cópia de segurança existente na VM está em execução e não é possível iniciar uma nova tarefa enquanto a tarefa existente está em execução. |
| Copiar VHDs a partir do Cofre de cópia de segurança excedeu o tempo-. Repita a operação dentro de alguns minutos. Se o problema persistir, contacte o Suporte da Microsoft. | Isto acontece se existir um erro transitório no lado de armazenamento ou se o serviço de cópia de segurança não está a obter o IOPS suficiente da conta de armazenamento que aloja a VM para a transferência de dados dentro do período de tempo limite para o cofre. Certifique-se de que seguiu [melhores práticas](backup-azure-vms-introduction.md#best-practices) ao configurar a cópia de segurança. Tente mover VM para um armazenamento diferente da conta que não está carregada e repita cópia de segurança.|
| Falha de cópia de segurança com um erro interno -. Repita a operação dentro de alguns minutos. Se o problema persistir, contacte Support da Microsoft |Pode obter este erro por motivos de 2: <ol><li> Não há um problema transitório ao aceder ao armazenamento VM. Verifique [Azure estado](https://azure.microsoft.com/en-us/status/) para ver se existe qualquer problema relacionado com a computação em curso, de armazenamento ou redes na região. Em seguida, repita a tarefa de cópia de segurança depois do problema está resolvido. <li>Foi eliminada da VM original e, por conseguinte, não é possível efetuar o ponto de recuperação. Para manter os dados de cópia de segurança para uma VM eliminada, mas remova os erros de cópia de segurança: desproteger a VM e escolha a opção para manter os dados. Esta ação para a tarefa de cópia de segurança agendada e as mensagens de erro periódica. |
| Falha ao instalar a extensão do Azure Recovery Services do item selecionado - o agente VM a é um pré-requisito para a extensão de serviços de recuperação do Azure. Instale o agente da VM do Azure e reinicie a operação de registo |<ol> <li>Verificar se o agente da VM foi instalado corretamente. <li>Certifique-se de que o sinalizador da configuração VM está definido corretamente.</ol> [Leia mais](#validating-vm-agent-installation) sobre como instalar o agente da VM e, como validar a instalação do agente VM. |
| Instalação da extensão falhou com o erro "COM+ não conseguiu comunicar com o coordenador de transações Microsoft Distributed |Normalmente, isto significa que o serviço COM+ não está em execução. Contacte o suporte da Microsoft para obter ajuda na corrigir este problema. |
| A operação de instantâneos falhou com o erro de operação do VSS "Esta unidade está bloqueada pela encriptação de unidade BitLocker. Tem de desbloquear esta unidade no painel de controlo. |Desativar o BitLocker para todas as unidades na VM e verifique se o problema VSS for resolvido |
| VM não se encontra num Estado que permite que as cópias de segurança. |<ul><li>Verifique a VM estiver num estado transitório entre em execução e encerrar para baixo. Se for o caso, aguarde para o estado VM ser um dos atributos e acionar a cópia de segurança novamente. <li> Se a VM é uma VM com Linux e o módulo de kernel utiliza [segurança avançada Linux], tem de excluir o caminho de agente Linux (_/var/lib/waagent_) de segurança políticas para fazer a cópia de segurança se extensão é instalada.  |
| Máquina Virtual do Azure não encontrado. |Isto acontece quando a VM principal é eliminada, mas continua a política de cópia de segurança procurar uma VM efetuar uma cópia de segurança. Para corrigir este erro: <ol><li>Recriar a máquina virtual com o mesmo nome e o mesmo nome de grupo de recursos [nome do serviço de nuvem] <br>(OU) <li> Desative a proteção para esta VM para que as tarefas de cópia de segurança não serão criadas. </ol> |
| Agente da máquina virtual não está presente na máquina virtual - instale quaisquer pré-requisito e o agente da VM e, em seguida, reinicie a operação. |[Leia mais](#vm-agent) sobre a instalação do agente VM e, como validar a instalação do agente VM. |
| Operação de instantâneos falhou devido a escritores VSS se num Estado incorreto |É necessário reiniciar os escritores VSS (cópia sombra de serviço) que estão num Estado incorreto. Para tal, numa linha de comandos elevada, execute _escritores de lista vssadmin_. Saída contém todos os escritores VSS e o respetivo estado. Para cada escritor VSS cujo estado é não "[1] Stable", reinicie o escritor VSS, executando os seguintes comandos numa linha de comandos elevada:<br> _net stop serviceName_ <br> _net start serviceName_|
| Operação de instantâneos falhou devido a uma falha de análise da configuração |Isto acontece devido a permissões alteradas no diretório MachineKeys: _%systemdrive%\programdata\microsoft\crypto\rsa\machinekeys_ <br>Execute o comando abaixo e certifique-se de que as permissões no diretório MachineKeys aqueles predefinido:<br>_ICACLS %systemdrive%\programdata\microsoft\crypto\rsa\machinekeys_ <br><br> As permissões predefinidas são:<br>Everyone:(R,W) <br>BUILTIN\Administrators:(F)<br><br>Se vir permissões no diretório MachineKeys diferente do predefinido,. Siga os passos para as permissões corretas, elimine o certificado e acionar a cópia de segurança abaixo.<ol><li>Corrigir permissões no diretório MachineKeys.<br>Utilizar o Explorador de propriedades de segurança e definições de segurança avançadas no diretório, repor permissões para os valores predefinidos, remova qualquer objeto de utilizador adicionais (a predefinição) do diretório e certifique-se de que o 'todas as pessoas' tinham de permissões de acesso especiais para:<br>-Listar pasta / Ler dados <br>-Leia atributos <br>-Leia atributos expandidos <br>-Criar ficheiros / escrever dados <br>-Criar pastas / Acrescentar dados<br>-Escrever atributos<br>-Escrever atributos expandidos<br>-Permissões de leitura<br><br><li>Eliminar todos os certificados com o campo "Emitido para" = "Windows Azure gestão para extensões de serviço" ou "Gerador do certificado de CRP de Azure Windows".<ul><li>[Abra a consola de certificados (computador Local)](https://msdn.microsoft.com/library/ms788967(v=vs.110).aspx)<li>Eliminar todos os certificados (em pessoal -> certificados) com o campo "Emitido para" = "Windows Azure gestão para extensões de serviço" ou "Gerador do certificado de CRP de Azure Windows".</ul><li>Cópia de segurança VM acionador. </ol>|
| Falha na validação como máquina virtual é encriptada com BEK individualmente. As cópias de segurança podem ser ativadas apenas para máquinas virtuais encriptadas com BEK e KEK. |Máquina virtual deve ser encriptada utilizando a chave de encriptação do BitLocker e chave de encriptação de chave. Depois disso, a cópia de segurança deve ser ativada. |
| Serviço de cópia de segurança do Azure não tem permissões suficientes para o Cofre de chaves para cópia de segurança de encriptados máquinas virtuais. |Serviço de cópia de segurança deve ser fornecido estas permissões no PowerShell utilizando os passos mencionados **ativar Backup** secção [PowerShell documentação](backup-azure-vms-automation.md). |
|Instalação da extensão falhou com o erro de instantâneos - COM+ não conseguiu comunicar com o coordenador de transações distribuídas da Microsoft | Volte a tentar iniciar o serviço do windows "sistema aplicação COM+" (a partir de uma linha de comandos elevada - _net iniciar COMSysApp_). <br>Se falhar ao iniciar,. Siga os passos abaixo:<ol><li> Confirme que a conta de início de sessão do serviço "Coordenador de transações distribuídas" "Serviço de rede". Se não for, altere "Serviço de rede", reinicie este serviço e, em seguida, tente iniciar o serviço "sistema aplicação COM+". "<li>Se ainda conseguir iniciar, desinstalar/instalar o serviço "Coordenador de transações distribuídas" pelos seguintes passos abaixo:<br> -Parar o serviço MSDTC<br> -Abra uma linha de comandos (cmd) <br> -Execute o comando "msdtc-desinstalar" <br> -Execute o comando "msdtc-instalar" <br> -Inicie o serviço MSDTC<li>Iniciar o serviço do windows "sistema aplicação COM+" e depois de ser iniciado, acionar a cópia de segurança do portal.</ol> |
|  A operação de instantâneos falhou devido a erro de COM+ | A ação recomendada consiste em reiniciar o serviço de windows "sistema aplicação COM+" (a partir de uma linha de comandos elevada - _net iniciar COMSysApp_). Se o problema persistir, reinicie a VM. Se reiniciar a VM não ajudar, tente [remover a extensão de VMSnapshot](https://docs.microsoft.com/en-us/azure/backup/backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout#cause-3-the-backup-extension-fails-to-update-or-load) e acionar a cópia de segurança manualmente. |
| Falha ao parar um ou mais-pontos de montagem da VM para criar um instantâneo consistente de sistema de ficheiros | Utilize os passos seguintes: <ol><li>Verifique o estado do sistema de ficheiros de todos os dispositivos montados com _'tune2fs'_ comando.<br> Ex:, como tune2fs -l/dev/sdb1 \| GREP "Estado do sistema de ficheiros" <li>Desmonte os dispositivos para o sistema de ficheiros Estado não está a utilizar limpa _'umount'_ comando <li> Executar verificação de FileSystemConsistency nestes dispositivos utilizando _'ou a fsck'_ comando <li> Monte novamente os dispositivos e repita a cópia de segurança.</ol> |
| Operação de instantâneos falhou devido uma falha na criação de canal de comunicação de rede segura | <ol><Li> Abra o Editor de registo ao executar regedit.exe um elevados. <li> Identifique todas as versões do. NetFramework presente no sistema. Estão presentes na hierarquia da chave de registo "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft" <li> Para cada. NetFramework presente na chave de registo, adicione a seguinte chave: <br> "SchUseStrongCrypto" = dword:00000001 </ol>|
| A operação de instantâneos falhou devido uma falha na instalação do Visual C++ Redistributable for Visual Studio 2012 | Navegue para C:\Packages\Plugins\Microsoft.Azure.RecoveryServices.VMSnapshot\agentVersion e instalar vcredist2012_x64. Certifique-se de que o valor da chave de registo para permitir esta instalação do serviço está definido para o valor correto ou seja, o valor da chave de registo _HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Msiserver_ está definido para 3 e 4 não. Se ainda estão a enfrentar problemas com a instalação, reinicie o serviço de instalação, executando _MSIEXEC /UNREGISTER_ seguido _MSIEXEC /REGISTER_ da linha de comandos elevada.  |


## <a name="jobs"></a>Tarefas
| Detalhes do erro | Solução |
| --- | --- |
| Cancelamento não é suportado para este tipo de tarefa - por favor, aguardar até que a tarefa é concluída. |Nenhuma |
| A tarefa não está num Estado cancelable - por favor, aguardar até que a tarefa é concluída. <br>OU<br> A tarefa selecionada não está num Estado cancelable -. Aguarde a conclusão da tarefa. |Em todos os probabilidade, a tarefa quase estiver concluída. Aguarde até que a tarefa esteja concluída.|
| Não é possível cancelar a tarefa porque não está em curso - cancelamento só é suportado para tarefas que se encontrem em curso. . A tentativa de cancelar num em curso tarefa. |Isto acontece devido a um Estado temporárias. Aguarde um minuto e repita a operação de cancelamento. |
| Falha ao cancelar a tarefa -. Aguarde pela conclusão da tarefa. |Nenhuma |

## <a name="restore"></a>Restauro
| Detalhes do erro | Solução |
| --- | --- |
| Falha no restauro com um erro interno de nuvem |<ol><li>O serviço em nuvem para o qual está a tentar restaurar está configurado com as definições de DNS. Pode verificar <br>$deployment = get-AzureDeployment - ServiceName "ServiceName"-ranhura "Production" Get-AzureDns - DnsSettings $deployment. DnsSettings<br>Se houver um endereço configurado, isto significa que as definições de DNS estão configuradas.<br> <li>O serviço em nuvem para a qual está a tentar restaurar está configurado com ReservedIP e VMs existentes num serviço em nuvem estão no estado de paragem.<br>Pode verificar o que IP reservado tem um serviço em nuvem, utilizando os seguintes cmdlets do powershell:<br>$deployment = get-AzureDeployment - ServiceName "servicename"-DEP de ranhura "Production" $. ReservedIPName <br><li>Está a tentar restaurar uma máquina virtual com as seguintes configurações de rede especial no mesmo serviço em nuvem. <br>-As máquinas virtuais em configuração de Balanceador de carga (externas e internas)<br>-As máquinas virtuais com vários IPs reservados<br>-As máquinas virtuais com vários NICs<br>Selecione um novo serviço em nuvem na IU ou consulte [restaurar considerações](backup-azure-arm-restore-vms.md#restore-vms-with-special-network-configurations) para VMs com configurações de rede especiais.</ol> |
| O nome DNS selecionado já foi atribuído - Especifique um nome DNS diferente e tente novamente. |O nome DNS aqui refere-se ao nome do serviço em nuvem (normalmente, terminando. cloudapp.net). Isto deve ser exclusivo. Se ocorrer este erro, terá de escolher um nome VM diferente durante o restauro. <br><br> Este erro é apresentado apenas a utilizadores do portal do Azure. A operação de restauro através do PowerShell será bem sucedida porque apenas restaura os discos e não criar a VM. O erro irá ser deparam quando a VM é explicitamente criada por si após a operação de restauro de disco. |
| A configuração de rede virtual especificado não está correta –. Especifique uma configuração de rede virtual diferente e tente novamente. |Nenhuma |
| O serviço de nuvem especificada está a utilizar um IP reservado, que não coincide com a configuração da máquina virtual a ser restaurada - Especifique um serviço de nuvem diferente, que não está a utilizar o IP reservado, ou escolha outro ponto de recuperação para restaurar a partir de. |Nenhuma |
| Serviço em nuvem atingiu o limite do número de pontos finais de entrada - repetir a operação especificando um serviço em nuvem diferente ou através da utilização de um ponto final existente. |Nenhuma |
| Conta de armazenamento do cofre e o destino da cópia de segurança em duas regiões diferentes - Certifique-se de que a conta de armazenamento especificada na operação de restauro na mesma região que o Cofre de cópia de segurança do Azure. |Nenhuma |
| Conta de armazenamento especificada para a operação de restauro não é suportada - contas do storage apenas Basic/Standard com localmente redundante ou definições de replicação redundante georreplicação são suportadas. Selecione uma conta de armazenamento suportadas |Nenhuma |
| Tipo de conta de armazenamento especificado para a operação de restauro não está online - Certifique-se de que a conta de armazenamento especificada na operação de restauro online |Isto pode acontecer devido a um erro transitório no armazenamento do Azure ou devido a uma falha. Escolha outra conta de armazenamento. |
| Foi atingida a Quota do grupo de recursos - elimine alguns grupos de recursos a partir do portal do Azure ou contacte o suporte do Azure para aumentar os limites. |Nenhuma |
| Sub-rede selecionada não existe - Selecione uma sub-rede de que existe |Nenhuma |
| O Serviço de Cópia de Segurança não tem autorização para aceder a recursos na sua subscrição. |Para resolver isto, primeiro discos restauro utilizando os passos mencionados na secção **restaurar uma cópia de segurança discos** no [configuração de restauro de VM escolher](backup-azure-arm-restore-vms.md#choose-a-vm-restore-configuration). Depois disso, utilize os passos de PowerShell mencionados [criar uma VM a partir de discos restaurados](backup-azure-vms-automation.md#create-a-vm-from-restored-disks) para criar a VM completo de discos restaurados. |

## <a name="backup-or-restore-taking-time"></a>Cópia de segurança ou restauro demorar tempo
Se vir o backup(>12 hours) ou restauro demorar time(>6 hours):
* Compreender [fatores que contribuem para o tempo de cópia de segurança](backup-azure-vms-introduction.md#total-vm-backup-time) e [fatores que contribuem para restaurar tempo](backup-azure-vms-introduction.md#total-restore-time).
* Certifique-se de que segue [melhores práticas de cópia de segurança](backup-azure-vms-introduction.md#best-practices). 

## <a name="vm-agent"></a>Agente VM
### <a name="setting-up-the-vm-agent"></a>Configurar o agente da VM
Normalmente, o agente da VM já se encontra presente no VMs que são criadas a partir da galeria do Azure. No entanto, as máquinas virtuais que são migradas dos centros de dados no local não teriam o agente da VM instalado. Para essas VMs, o agente da VM tem de ser instalada explicitamente.

Para VMs do Windows:

* Transfira e instale o [MSI do agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Precisa de privilégios de Administrador para concluir a instalação.
* Para máquinas virtuais clássicas, [atualizar a propriedade da VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado. Este passo não é necessário para as máquinas virtuais do Gestor de recursos.

Para VMs com Linux:

* Instale os mais recentes do repositório de distribuição. Iremos **vivamente recomendável** instalar agente apenas através do repositório de distribuição. Para obter detalhes sobre o nome do pacote, consulte [repositório de agente do Linux](https://github.com/Azure/WALinuxAgent) 
* Para VMs clássicas, [atualizar a propriedade da VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado. Este passo não é necessário para as máquinas virtuais do Gestor de recursos.

### <a name="updating-the-vm-agent"></a>Atualizar o Agente da VM
Para VMs do Windows:

* A atualização do Agente da VM é tão simples como reinstalar os [binários do Agente da VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). No entanto, terá de se certificar de que nenhuma operação de cópia de segurança está em execução enquanto o agente VM está a ser atualizado.

Para VMs com Linux:

* Siga as instruções no [atualizar o agente de VM Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
Iremos **vivamente recomendável** agente a atualizar apenas através do repositório de distribuição. Não é recomendada a transferir o código de agente a partir do github diretamente e atualizá-la. Agente mais recente não esteja disponível para a distribuição, contacte o suporte de distribuição para obter instruções sobre como instalar o agente mais recente. Pode verificar os mais recentes [agente Linux do Windows Azure](https://github.com/Azure/WALinuxAgent/releases) informações no repositório do github.

### <a name="validating-vm-agent-installation"></a>A validar a instalação do agente de VM
Como verificar a versão do agente de VM em VMs do Windows:

1. Inicie sessão na máquina virtual do Azure e navegue para a pasta *C:\WindowsAzure\Packages*. Deve encontrar o ficheiro WaAppAgent.exe presente.
2. Clique com o botão direito do rato no ficheiro, aceda a **Propriedades** e, em seguida, selecione o separador **Detalhes**. O campo de versão do produto deve ser 2.6.1198.718 ou superior

## <a name="troubleshoot-vm-snapshot-issues"></a>Resolver problemas de instantâneos VM
Cópia de segurança VM depende emitir comandos de instantâneo para o armazenamento subjacente. Não ter acesso ao armazenamento, ou atrasos numa execução de tarefas de instantâneo pode fazer com que a tarefa de cópia de segurança falhem. Pode causar falhas de tarefas de instantâneo.

1. Acesso de rede para o armazenamento está bloqueado, utilizando o NSG<br>
    Saiba mais sobre como [ativar o acesso de rede](backup-azure-vms-prepare.md#network-connectivity) para o armazenamento utilizando o adicionar à lista branca de IPs ou através de servidor proxy.
2. As VMs com cópia de segurança do Sql Server configurada podem fazer com que o atraso de tarefa de instantâneo <br>
   Por predefinição VM cópia de segurança problemas VSS cópia de segurança completa em VMs do Windows. Em VMs que estejam a executar o Sql Servers e se a cópia de segurança do Sql Server está configurada, isto poderá provocar atraso na execução de instantâneo. Defina os seguintes chave de registo se ocorrerem falhas de cópia de segurança devido a problemas de instantâneo.

   ```
   [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
   "USEVSSCOPYBACKUP"="TRUE"
   ```
3. Estado da VM reportou incorretamente porque a VM seja encerrada RDP.  <br>
   Se tem de encerrar a máquina virtual no RDP, verifique novamente no portal do que o estado da VM é reflectido correctamente. Caso contrário, encerre a VM no portal através da opção de "Encerramento" no dashboard VM.
4. Se mais de quatro VMs partilharem o mesmo serviço de nuvem, configure várias políticas de cópia de segurança para pré-configurar os tempos cópia de segurança, por isso, não existem que mais de quatro cópias de segurança VM são iniciadas ao mesmo tempo. Tente propagar-se o início vezes uma hora, à excepção entre as políticas de cópia de segurança.
5. VM está em execução em elevada da CPU/memória.<br>
   Se a máquina virtual está em execução em elevada da CPU usage(>90%) ou tarefa de instantâneo de memória, é colocado em fila, atrasada e, eventualmente, serão obtém excedido. Repita a cópia de segurança a pedido estas situações.

<br>

## <a name="networking"></a>Redes
Como todas as extensões, extensão de cópia de segurança necessita de acesso à internet pública funcione. Não ter acesso à internet pública pode manifesto próprio de várias formas:

* A instalação da extensão pode falhar
* Podem efetuar as operações de cópia de segurança (por exemplo, o instantâneo de disco)
* Apresentar o estado da operação de cópia de segurança pode falhar

A necessidade de resolução de endereços internet públicos foi articulated [aqui](http://blogs.msdn.com/b/mast/archive/2014/06/18/azure-vm-provisioning-stuck-on-quot-installing-extensions-on-virtual-machine-quot.aspx). Terá de verificar as configurações de DNS para a VNET e certifique-se de que o URI do Azure podem ser resolvido.

Assim que a resolução do nome foi efetuada corretamente, acesso para IPs o Azure também tem de ser fornecido. Para desbloquear o acesso à infraestrutura do Azure, siga um destes passos:

1. Intervalos de IP de lista branca de datacenter do Azure.
   * Obter a lista de [datacenter do Azure IPs](https://www.microsoft.com/download/details.aspx?id=41653) na lista de permissões.
   * Desbloquear a IPs utilizando a [New-NetRoute](https://technet.microsoft.com/library/hh826148.aspx) cmdlet. Execute este cmdlet numa VM do Azure, numa janela elevada do PowerShell (executar como administrador).
   * Adicione regras para o NSG (se tiver um no local) para permitir o acesso para os IPs.
2. Criar um caminho para o tráfego HTTP para o fluxo
   * Se tiver alguma restrição de rede no local (um grupo de segurança rede, por exemplo) implementar um servidor de proxy HTTP para encaminhar o tráfego. Podem encontrar passos para implementar um servidor HTTP Proxy [aqui](backup-azure-vms-prepare.md#network-connectivity).
   * Adicione regras para o NSG (se tiver um no local) para permitir o acesso à INTERNET do HTTP Proxy.

> [!NOTE]
> DHCP deve ser ativado no computador convidado para cópia de segurança de VM de IaaS funcione.  Se precisar de um IP privado estático, deve configurá-lo através da plataforma. A opção de DHCP dentro da VM deve ser ativada à esquerda.
> Ver mais informações sobre [definir um IP estático de privada interna](../virtual-network/virtual-networks-reserved-private-ip.md).
>
>
