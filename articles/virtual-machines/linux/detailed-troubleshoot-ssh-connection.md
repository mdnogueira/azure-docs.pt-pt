---
title: "Resolução de SSH detalhadas para uma VM do Azure | Microsoft Docs"
description: "Mais passos para ligar a uma máquina virtual do Azure de problemas de resolução de problemas de SSH"
keywords: "SSH ligação recusada, ssh erro, azure ssh, ligação SSH falhou"
services: virtual-machines-linux
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.assetid: b8e8be5f-e8a6-489d-9922-9df8de32e839
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: support-article
ms.date: 07/06/2017
ms.author: iainfou
ms.openlocfilehash: 9ccdb3fbca21264065eeb1c4e46314c62af4c2e8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="detailed-ssh-troubleshooting-steps-for-issues-connecting-to-a-linux-vm-in-azure"></a>SSH detalhada passos para ligar a uma VM com Linux no Azure de problemas de resolução de problemas
Existem várias razões possíveis para que o cliente SSH poderá não ser capaz de alcançar o serviço SSH na VM. Se seguiu através de mais [SSH geral passos de resolução de problemas](troubleshoot-ssh-connection.md), terá de resolver o problema de ligação. Este artigo orienta-o pelos passos de resolução de problemas detalhados para determinar onde está a falhar a ligação SSH e como resolvê-lo.

## <a name="take-preliminary-steps"></a>Siga os passos preliminares
O diagrama seguinte mostra os componentes que estão relacionados.

![Diagrama que mostra os componentes do serviço SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot1.png)

Os seguintes passos ajudam a isolar a origem da falha e descobrir soluções ou soluções.

1. Verifique o estado da VM no portal.
   No [portal do Azure](https://portal.azure.com), selecione **máquinas virtuais** > *nome da VM*.

   O painel de estado para a VM deve mostrar **executar**. Desloque para baixo para mostrar a atividade recente para computação, armazenamento e recursos de rede.

2. Selecione **definições** para examinar os pontos finais, endereços IP, grupos de segurança de rede e outras definições.

   A VM deve ter um ponto final definido para o tráfego SSH que pode ver na **pontos finais** ou  **[grupo de segurança de rede](../../virtual-network/virtual-networks-nsg.md)**. Pontos finais em VMs que foram criadas utilizando o Gestor de recursos são armazenados num grupo de segurança de rede. Além disso, certifique-se de que as regras foram aplicadas ao grupo de segurança de rede e que está a referenciados na sub-rede.

Para verificar a conectividade de rede, consulte os pontos finais configurados e verifique se pode aceder a VM através de outro protocolo, tal como HTTP ou outro serviço.

Após estes passos, tente novamente a ligação de SSH.

## <a name="find-the-source-of-the-issue"></a>Localizar a origem do problema
O cliente SSH no seu computador poderão falhar alcançar o serviço SSH na VM do Azure devido a problemas ou configurações incorretas nas seguintes áreas:

* [Computador de cliente SSH](#source-1-ssh-client-computer)
* [Dispositivo de limite de organização](#source-2-organization-edge-device)
* [Ponto final do serviço em nuvem e de acesso (ACL) de lista de controlo](#source-3-cloud-service-endpoint-and-acl)
* [Grupos de segurança de rede](#source-4-network-security-groups)
* [VM do Azure baseados em Linux](#source-5-linux-based-azure-virtual-machine)

## <a name="source-1-ssh-client-computer"></a>Origem de 1: SSH computador cliente
Para eliminar o seu computador como origem da falha, certifique-se de que pode tornar a ligações SSH outra no local, computador baseado em Linux.

![Diagrama realça os componentes de computador do cliente SSH](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot2.png)

Se a ligação falhar, procure os seguintes problemas no seu computador:

* Uma definição de local firewall que está a bloquear o tráfego SSH de entrada ou de saída (TCP 22)
* Instalado localmente o software de proxy do cliente que está a impedir ligações SSH
* Localmente instalado software que está a impedir ligações SSH da monitorização de rede
* Outros tipos de software de segurança que monitorizar o tráfego ou permitirem/não permitir tipos específicos de tráfego

Se uma das seguintes condições aplicam-se, temporariamente desative o software e volte a tentar uma ligação SSH para um computador no local para determinar o motivo pelo qual que a ligação está a ser bloqueada no seu computador. Em seguida, trabalhe com o seu administrador de rede para corrigir as definições de software para permitir ligações de SSH.

Se estiver a utilizar a autenticação de certificado, certifique-se de que tem as permissões para a pasta. SSH no seu diretório raiz:

* Chmod 700 ~/.ssh
* Chmod 644 ~/.ssh/\*pub
* Chmod 600 ~/.ssh/id_rsa (ou outros ficheiros que tenham as chaves privadas armazenadas nelas)
* ~/.Ssh/known_hosts chmod 644 (contém anfitriões à através de SSH)

## <a name="source-2-organization-edge-device"></a>Origem 2: Dispositivo de limite de organização
Para eliminar o seu dispositivo de limite de organização como origem da falha, certifique-se de que um computador que está diretamente ligado à Internet pode efetuar ligações SSH para a VM do Azure. Se estiver a aceder a VM através de uma VPN de site para site ou uma ligação ExpressRoute do Azure, avance para o [origem 4: grupos de segurança de rede](#nsg).

![Diagrama realça o dispositivo de limite de organização](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot3.png)

Se não tiver um computador que está diretamente ligado à Internet, crie uma nova VM do Azure no seu próprio grupo de recursos ou serviço em nuvem e utilizá-lo. Para obter mais informações, consulte [criar uma máquina virtual com Linux no Azure](quick-create-cli.md). Elimine o grupo de recursos ou o serviço VM e na nuvem quando tiver terminado com os seus testes.

Se é possível criar uma ligação SSH com um computador que está diretamente ligado à Internet, verifique o seu dispositivo de limite de organização para:

* Uma firewall interno que está a bloquear o tráfego SSH com a Internet
* Um servidor proxy que está a impedir ligações SSH
* Deteção de intrusões ou software em execução em dispositivos na sua rede de limite que está a impedir ligações SSH de monitorização de rede

Trabalhar com o administrador da rede para corrigir as definições dos dispositivos de limite de organização para permitir tráfego SSH com a Internet.

## <a name="source-3-cloud-service-endpoint-and-acl"></a>Origem 3: Ponto final do serviço de nuvem e a ACL
> [!NOTE]
> Esta origem de apenas se aplica a VMs que foram criadas utilizando o modelo de implementação clássica. Para VMs que foram criadas utilizando o Gestor de recursos, avance para o [origem 4: grupos de segurança de rede](#nsg).

Para eliminar o ponto final do serviço de nuvem e a ACL como origem da falha, certifique-se de que outra VM do Azure na mesma rede virtual pode efetuar ligações SSH para a VM.

![Diagrama realça o ponto final do serviço de nuvem e ACL](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot4.png)

Se não tiver outra VM na mesma rede virtual, pode facilmente criar um. Para obter mais informações, consulte [criar uma VM com Linux no Azure utilizando a CLI](quick-create-cli.md). Elimine a VM adicional quando tiver terminado com os seus testes.

Se é possível criar uma ligação SSH com uma VM na mesma rede virtual, verifique as seguintes áreas:

* **A configuração de ponto final para o tráfego SSH no VM de destino.** A porta TCP privada do ponto final deve corresponder à porta TCP no qual o serviço SSH na VM está a escutar. (A porta predefinida é 22). Verifique o número da porta SSH TCP no portal do Azure ao selecionar **máquinas virtuais** > *nome da VM* > **definições** > **pontos finais**.
* **A ACL de ponto final de tráfego SSH na máquina virtual de destino.** Uma ACL permite-lhe especificar permitido ou negado o tráfego de entrada a partir da Internet, com base no respetivo endereço IP de origem. ACLs mal configuradas podem impedir o tráfego de entrada SSH para o ponto final. Verifique as ACLs para garantir que esse tráfego de entrada de endereços IP públicos do seu proxy ou outro servidor edge é permitido. Para obter mais informações, consulte [sobre o acesso de rede (ACLs) de listas de controlo](../../virtual-network/virtual-networks-acl.md).

Para eliminar o ponto final como uma origem do problema, remova o ponto final atual, crie outro ponto final e especifique o nome SSH (a porta TCP 22 para o número de porta pública e privada). Para obter mais informações, consulte [configurar pontos finais numa máquina virtual no Azure](../windows/classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

<a id="nsg"></a>

## <a name="source-4-network-security-groups"></a>Origem 4: Grupos de segurança de rede
Grupos de segurança de rede permitem-lhe ter controlo mais granular sobre o tráfego de entrada e saída permitido. Pode criar regras que abrangem sub-redes e serviços em nuvem na uma rede virtual do Azure. Verifique as regras de grupo de segurança de rede para se certificar de que o tráfego SSH e para a Internet é permitido.
Para obter mais informações, consulte [sobre grupos de segurança de rede](../../virtual-network/virtual-networks-nsg.md).

Também pode utilizar o IP verificar para validar a configuração de NSG. Para obter mais informações, consulte [descrição geral da monitorização de rede do Azure](https://docs.microsoft.com/en-us/azure/network-watcher/network-watcher-monitoring-overview). 

## <a name="source-5-linux-based-azure-virtual-machine"></a>Origem 5: Baseado em Linux máquina virtual do Azure
A origem último informações sobre problemas possíveis é a máquina virtual do Azure em si.

![Diagrama realça baseado em Linux máquina virtual do Azure](./media/detailed-troubleshoot-ssh-connection/ssh-tshoot5.png)

Se não tiver o feito, siga as instruções [repor uma palavra-passe ou SSH para máquinas virtuais baseadas em Linux](classic/reset-access.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

Tente ligar novamente a partir do seu computador. Se continuar a falhar, seguem-se alguns dos problemas possíveis:

* O serviço SSH não está em execução na máquina virtual de destino.
* O serviço SSH não está a escutar na porta TCP 22. Para testar, instalar um cliente telnet no seu computador local e execute "telnet *cloudServiceName*. cloudapp.net 22". Este passo determina se a máquina virtual permite a comunicação de entrada e saída para o ponto final SSH.
* A firewall local na máquina virtual de destino tem regras que estão a impedir o tráfego SSH de entrada ou de saída.
* Deteção de intrusões ou software que está em execução na máquina virtual do Azure de monitorização de rede está a impedir ligações SSH.

## <a name="additional-resources"></a>Recursos adicionais
Para obter mais informações sobre resolução de problemas de acesso de aplicação, consulte [resolver problemas de acesso a uma aplicação em execução numa máquina virtual do Azure](troubleshoot-app-connection.md)
