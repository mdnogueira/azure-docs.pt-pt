---
title: "Resolver erros de cópia de segurança do Azure no portal clássico | Microsoft Docs"
description: "Resolver problemas de cópia de segurança do Azure e o restauro de máquinas virtuais do Azure no portal clássico."
services: backup
documentationcenter: 
author: trinadhk
manager: shreeshd
editor: 
ms.assetid: 117201fb-c0cd-4be4-b47f-abd88fe914cf
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/23/2017
ms.author: trinadhk;markgal;
ms.openlocfilehash: 284a1b64fbb15d0aa800182c6671d447e191b76a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-virtual-machine-backup"></a>Resolver problemas das cópias de segurança de máquina virtuais do Azure
> [!div class="op_single_selector"]
> * [Cofre de serviços de recuperação](backup-azure-vms-troubleshoot.md)
> * [Cofre de cópia de segurança](backup-azure-vms-troubleshoot-classic.md)
>
>

Pode resolver erros encontrados ao utilizar o Backup do Azure com as informações apresentadas na tabela abaixo.

## <a name="discovery"></a>Deteção
| Operação de cópia de segurança | Detalhes do erro | Solução |
| --- | --- | --- |
| Deteção |Falha ao detetar novos itens - Microsoft Azure Backup encontrou e erro interno. Aguarde alguns minutos e, em seguida, repita a operação. |Repita o processo de deteção após 15 minutos. |
| Deteção |Falha ao detetar novos itens – operação de outro método de deteção já está em curso. Aguarde enquanto a operação de deteção atual tiver sido concluída. |Nenhuma |

## <a name="register"></a>Registar
| Operação de cópia de segurança | Detalhes do erro | Solução |
| --- | --- | --- |
| Registar |Número de discos de dados anexados à máquina virtual excedeu o limite suportado -. Desligue alguns discos de dados nesta máquina virtual e repita a operação. Cópia de segurança do Azure suporta até 16 discos de dados ligados a uma máquina virtual do Azure para cópia de segurança |Nenhuma |
| Registar |Microsoft Azure Backup encontrou um erro interno - Aguarde alguns minutos e repita a operação de novo. Se o problema persistir, contacte o Suporte da Microsoft. |Pode obter este erro devido a um da seguinte configuração não suportada da VM na Premium LRS. <br> VMs do Premium storage podem ser feita com o Cofre de serviços de recuperação. [Saiba mais](backup-introduction-to-azure-backup.md#using-premium-storage-vms-with-azure-backup) |
| Registar |Registo falhou com o tempo limite da operação de instalação de agentes |Verifique se a versão do SO da máquina virtual é suportada. |
| Registar |Execução do comando falhou - outra operação está em curso neste item. Aguarde até que a anterior operação esteja concluída |Nenhuma |
| Registar |Máquinas virtuais com discos rígidos virtuais armazenados no armazenamento Premium não são suportadas para cópia de segurança |Nenhuma |
| Registar |Agente da máquina virtual não está presente na máquina virtual - instale o necessário pré-requisitos, agente da VM e repita a operação. |[Leia mais](#vm-agent) sobre a instalação do agente VM e, como validar a instalação do agente VM. |

## <a name="backup"></a>Cópia de segurança
| Operação de cópia de segurança | Detalhes do erro | Solução |
| --- | --- | --- |
| Cópia de segurança |Não foi possível comunicar com o agente da VM para obter o estado do instantâneo. Tarefa do instantâneo VM sub excedeu o tempo limite. -Consulte o guia de resolução de problemas sobre como resolver este problema. |Este erro é apresentado se ocorrer um problema com o agente da VM ou acesso de rede para a infraestrutura do Azure é bloqueado de alguma forma. Saiba mais sobre [problemas de instantâneos de VM de depuração](backup-azure-troubleshoot-vm-backup-fails-snapshot-timeout.md). <br> Se o agente da VM não está a causar problemas de, em seguida, reinicie a VM. Por vezes um Estado VM incorreto pode causar problemas e reiniciar a VM repõe neste estado"incorreto" |
| Cópia de segurança |Falha de cópia de segurança com um erro interno -. Repita a operação dentro de alguns minutos. Se o problema persistir, contacte Support da Microsoft |Verifique se há um problema transitório ao aceder ao armazenamento VM. Verifique [Azure estado](https://azure.microsoft.com/en-us/status/) ver se existe qualquer problema em curso relacionados com o armazenamento/computação/rede na região. Repita que o problema de cópia de segurança post é atenuado. |
| Cópia de segurança |Não foi possível executar a operação visto que a VM já não existe. |Não é possível efetuar a cópia de segurança como a VM configurada para cópia de segurança foi eliminada. Parar mais cópias de segurança por que a vista de itens protegidos, selecione o item protegido e clique em Parar proteção. Pode manter os dados, selecionando a opção de manter a cópia de segurança de dados. Pode retomar mais tarde a proteção para esta máquina virtual clicando na configuração da proteção da vista de itens registados |
| Cópia de segurança |Falha ao instalar os serviços de recuperação do Azure a extensão do item selecionado - agente da VM é um pré-requisito para a extensão de serviços de recuperação do Azure. Instale o agente da VM do Azure e reinicie a operação de registo |<ol> <li>Verificar se o agente da VM foi instalado corretamente. <li>Certifique-se de que o sinalizador da configuração VM está definido corretamente.</ol> [Leia mais](#validating-vm-agent-installation) sobre a instalação do agente VM e, como validar a instalação do agente VM. |
| Cópia de segurança |Execução do comando falhou - está outra operação atualmente em curso neste item. Aguarde até que a anterior operação esteja concluída e tente novamente |Uma cópia de segurança existente ou a tarefa de restauro para a VM está em execução e não é possível iniciar uma nova tarefa enquanto a tarefa existente está em execução. |
| Cópia de segurança |Instalação da extensão falhou com o erro "COM+ não conseguiu comunicar com o coordenador de transações Microsoft Distributed |Normalmente, isto significa que o serviço COM+ não está em execução. Contacte o suporte da Microsoft para obter ajuda na corrigir este problema. |
| Cópia de segurança |A operação de instantâneos falhou com o erro de operação do VSS "Esta unidade está bloqueada pela encriptação de unidade BitLocker. Tem de desbloquear esta unidade no painel de controlo. |Desativar o BitLocker para todas as unidades na VM e verifique se o problema VSS for resolvido |
| Cópia de segurança |Máquinas virtuais com discos rígidos virtuais armazenados no armazenamento Premium não são suportadas para cópia de segurança |Nenhuma |
| Cópia de segurança |Máquina Virtual do Azure não encontrado. |Isto acontece quando a VM principal é eliminada, mas continua a política de cópia de segurança procurar uma VM efetuar a cópia de segurança. Para corrigir este erro: <ol><li>Recriar a máquina virtual com o mesmo nome e o mesmo nome de grupo de recursos [nome do serviço de nuvem] <br>(OU) <li> Desative a proteção para esta VM, para que não irão obter acionadas cópias de segurança subsequentes. </ol> |
| Cópia de segurança |Agente da máquina virtual não está presente na máquina virtual - instale o necessário pré-requisitos, agente da VM e repita a operação. |[Leia mais](#vm-agent) sobre a instalação do agente VM e, como validar a instalação do agente VM. |

## <a name="jobs"></a>Tarefas
| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Cancelar a tarefa |Cancelamento não é suportado para este tipo de tarefa - por favor, aguardar até que a tarefa é concluída. |Nenhuma |
| Cancelar a tarefa |A tarefa não está num Estado cancelable - por favor, aguardar até que a tarefa é concluída. <br>OU<br> A tarefa selecionada não está num Estado cancelable -. Aguarde a conclusão da tarefa. |Em todos os probabilidade, a tarefa é quase concluída; Aguarde até que a tarefa é concluída |
| Cancelar a tarefa |Não é possível cancelar a tarefa porque não está em curso - cancelamento só é suportado para tarefas que se encontrem em curso. . A tentativa de cancelar num em curso tarefa. |Isto acontece devido a um Estado temporárias. Aguarde um minuto e repita a operação de cancelamento |
| Cancelar a tarefa |Falha ao cancelar a tarefa - aguarde pela conclusão da tarefa. |Nenhuma |

## <a name="restore"></a>Restauro
| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Restauro |Falha no restauro com um erro interno de nuvem |<ol><li>O serviço em nuvem para o qual está a tentar restaurar está configurado com as definições de DNS. Pode verificar <br>$deployment = get-AzureDeployment - ServiceName "ServiceName"-ranhura "Production" Get-AzureDns - DnsSettings $deployment. DnsSettings<br>Se houver um endereço configurado, isto significa que as definições de DNS estão configuradas.<br> <li>O serviço em nuvem para a qual está a tentar restaurar está configurado com ReservedIP e VMs existentes num serviço em nuvem estão no estado de paragem.<br>Pode verificar o que IP reservado tem um serviço em nuvem, utilizando os seguintes cmdlets do powershell:<br>$deployment = get-AzureDeployment - ServiceName "servicename"-DEP de ranhura "Production" $. ReservedIPName <br><li>Está a tentar restaurar uma máquina virtual com as seguintes configurações de rede especial no mesmo serviço em nuvem. <br>-As máquinas virtuais em configuração de Balanceador de carga (externas e internas)<br>-As máquinas virtuais com vários IPs reservados<br>-As máquinas virtuais com vários NICs<br>Selecione um novo serviço em nuvem na IU ou consulte [restaurar considerações](backup-azure-restore-vms.md#restoring-vms-with-special-network-configurations) para VMs com configurações de rede especiais</ol> |
| Restauro |O nome DNS selecionado já foi atribuído - Especifique um nome DNS diferente e tente novamente. |O nome DNS aqui refere-se ao nome do serviço em nuvem (normalmente, terminando. cloudapp.net). Isto deve ser exclusivo. Se ocorrer este erro, terá de escolher um nome VM diferente durante o restauro. <br><br> Este erro é apresentado apenas a utilizadores do portal do Azure. A operação de restauro através do PowerShell é bem sucedida porque apenas restaura os discos e não criar a VM. O erro irá ser deparam quando a VM é explicitamente criada por si após a operação de restauro de disco. |
| Restauro |A configuração de rede virtual especificado não está correta –. Especifique uma configuração de rede virtual diferente e tente novamente. |Nenhuma |
| Restauro |O serviço de nuvem especificada está a utilizar um IP reservado, que não coincide com a configuração da máquina virtual a ser restaurada - Especifique um serviço de nuvem diferente que não está a utilizar o IP reservado ou escolha outro ponto de recuperação para restaurar a partir de. |Nenhuma |
| Restauro |Serviço em nuvem atingiu o limite do número de pontos finais de entrada - repetir a operação especificando um serviço em nuvem diferente ou através da utilização de um ponto final existente. |Nenhuma |
| Restauro |Conta de armazenamento do cofre e o destino da cópia de segurança em duas regiões diferentes - Certifique-se de que a conta de armazenamento especificada na operação de restauro na mesma região que o Cofre de cópia de segurança do Azure. |Nenhuma |
| Restauro |Conta de armazenamento especificada para a operação de restauro não é suportada - contas do storage apenas Basic/Standard com localmente redundante ou definições de replicação redundante georreplicação são suportadas. Selecione uma conta de armazenamento suportadas |Nenhuma |
| Restauro |Tipo de conta de armazenamento especificado para a operação de restauro não está online - Certifique-se de que a conta de armazenamento especificada na operação de restauro online |Isto pode acontecer devido a um erro transitório no armazenamento do Azure ou devido a uma falha. Escolha outra conta de armazenamento. |
| Restauro |Foi atingida a Quota do grupo de recursos - elimine alguns grupos de recursos a partir do portal do Azure ou contacte o suporte do Azure para aumentar os limites. |Nenhuma |
| Restauro |Sub-rede selecionada não existe - Selecione uma sub-rede de que existe |Nenhuma |

## <a name="policy"></a>Política
| Operação | Detalhes do erro | Solução |
| --- | --- | --- |
| Criar política |Falha ao criar a política - Reduza as opções de retenção para continuar com a configuração da política. |Nenhuma |

## <a name="vm-agent"></a>Agente VM
### <a name="setting-up-the-vm-agent"></a>Configurar o agente da VM
Normalmente, o agente da VM já se encontra presente no VMs que são criadas a partir da galeria do Azure. No entanto, as máquinas virtuais que são migradas dos centros de dados no local não teriam o agente da VM instalado. Para essas VMs, o agente da VM tem de ser instalada explicitamente. Leia mais sobre [instalar o agente da VM numa VM existente](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx).

Para VMs do Windows:

* Transfira e instale o [MSI do agente](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Necessita de privilégios de Administrador para concluir o processo de instalação.
* [Atualize a propriedade da VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado.

Para VMs com Linux:

* Instalar a versão mais recente [agente Linux](https://github.com/Azure/WALinuxAgent) a partir do github.
* [Atualize a propriedade da VM](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) para indicar que o agente está instalado.

### <a name="updating-the-vm-agent"></a>Atualizar o Agente da VM
Para VMs do Windows:

* A atualização do Agente da VM é tão simples como reinstalar os [binários do Agente da VM](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). No entanto, terá de se certificar de que nenhuma operação de cópia de segurança está em execução enquanto o agente VM está a ser atualizado.

Para VMs com Linux:

* Siga as instruções no [atualizar o agente de VM Linux](../virtual-machines/linux/update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="validating-vm-agent-installation"></a>A validar a instalação do agente de VM
Como verificar a versão do agente de VM em VMs do Windows:

1. Inicie sessão na máquina virtual do Azure e navegue para a pasta *C:\WindowsAzure\Packages*. Deve encontrar o ficheiro WaAppAgent.exe presente.
2. Clique com o botão direito do rato no ficheiro, aceda a **Propriedades** e, em seguida, selecione o separador **Detalhes**. O campo de versão do produto deve ser 2.6.1198.718 ou superior
