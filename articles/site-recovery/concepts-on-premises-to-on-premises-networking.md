---
title: "Configurar a ligação após a ativação pós-falha para um site secundário com o Azure Site Recovery de endereçamento IP | Microsoft Docs"
description: "Descreve como configurar o endereçamento IP para estabelecer a ligação para as VMs após a ativação pós-falha para um site secundário com o Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: prateek9us
editor: 
ms.assetid: 67d73590-185c-49b2-a097-597bf54747a9
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: pratshar
ms.openlocfilehash: 6baeda08b1c41cc024a02f51ca27be2829c46962
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/15/2017
---
# <a name="set-up-ip-addressing-to-connect-after-failover-to-a-secondary-site"></a>Configurar a ligação após a ativação pós-falha para um site secundário de endereçamento IP

Depois de rever os pré-requisitos de implementação, leia este artigo para planear o funcionamento em rede quando replicar máquinas virtuais de Hyper-V (VMs) geridas em nuvens do System Center Virtual Machine Manager (VMM), para um site secundário utilizando [do Azure Site Recovery](site-recovery-overview.md) no portal do Azure. 

Depois de ler este artigo, publique comentários na parte inferior ou no [Fórum dos Serviços de Recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="overview"></a>Descrição geral

Ao planear a replicação e a estratégia de ativação pós-falha, uma das perguntas chaves é como ligar à réplica após a ativação pós-falha. Existem duas opções: 

- **Utilizar um endereço IP diferente**:, pode optar por utilizar um endereço IP diferente para a VM replicada. Neste cenário a VM obtém um novo endereço IP após a ativação pós-falha e é necessária uma atualização DNS.
- **Manter o mesmo endereço IP**: pode pretender utilizar o mesmo endereço IP para a VM de réplica. Manter o mesmo IP endereços simplifica a recuperação ao reduzir relacionadas com a rede problemas após a ativação pós-falha. 

## <a name="retaining-ip-addresses"></a>Endereços IP de retenção

Se pretender manter os endereços IP do site primário depois da ativação pós-falha para o site secundário, pode Efetue uma ativação pós-falha de sub-rede completa e atualizar as rotas para indicar a nova localização dos endereços IP ou alternativa implementar uma sub-rede Stretch entre a primária e os sites de recuperação.

### <a name="stretched-subnet"></a>Sub-rede Stretch

Numa sub-rede Stretch, a sub-rede está disponível em simultâneo no site primário e secundário. Se mover um servidor e a respetiva configuração de IP (Layer 3) para o site secundário, a rede irá encaminhar o tráfego para a nova localização automaticamente. 

De uma perspetiva de (camada de ligação de dados) de camada 2, terá de equipamento de rede que pode gerir uma VLAN Stretch. Além disso, por alongar a VLAN, o domínio de falhas potenciais expande a ambos os sites, essencialmente se tornar um ponto único de falha. Apesar de ser um pouco provável, pode ocorrer se um storm difusão iniciada e não pode ser isolado. 


### <a name="subnet-failover"></a>Ativação pós-falha de sub-rede

Pode executar uma ativação pós-falha de sub-rede desfrutar das vantagens da Stretch sub-rede, sem realmente alongá-lo. Nesta solução, uma sub-rede estará disponível no site de origem ou de destino, mas não em ambas em simultâneo. Para manter o espaço de endereços IP na eventualidade de ocorrer uma ativação pós-falha, pode dispor programaticamente para a infraestrutura de router mover as sub-redes de um site para outro. Depois de quando ocorre a ativação pós-falha, sub-redes seriam movidos com as VMs associadas. A principal desvantagem é que o se ocorrer uma falha, terá de mover a sub-rede de toda.

### <a name="example"></a>Exemplo

Eis um exemplo de ativação pós-falha de sub-rede concluída. O site primário tem aplicações sejam executadas na sub-rede 192.168.1.0/24. Na ativação pós-falha, todas as VMs nesta sub-rede executar a ativação pós-falha para o site secundário e manter os seus endereços IP. Rotas têm de ser modificados para refletir o facto de que todas as VM máquinas virtuais que pertencem à sub-rede 192.168.1.0/24 agora movido para o site secundário.

Os gráficos seguintes mostram as sub-redes antes e após a ativação pós-falha:

- Antes da ativação pós-falha, 192.168.0.1/24 sub-rede está ativo no site de origem, ficar ativo no site secundário após a ativação pós-falha.
- As rotas entre o site primário e site de recuperação, terceiro site e site primário, terceiro site e site de recuperação terá de ser modificados adequadamente.

**Antes da ativação pós-falha**

![Antes da ativação pós-falha](./media/vmm-to-vmm-walkthrough-network/network-design2.png)

**Após a ativação pós-falha**

![Após a ativação pós-falha](./media/vmm-to-vmm-walkthrough-network/network-design3.png)

Após a ativação pós-falha, eis o que acontece:

- Recuperação de site atribui um endereço IP para cada interface de rede da VM, do conjunto de endereços IP estático na rede de relevante, para cada instância VMM.
- Se o conjunto de endereços IP no site secundário é o mesmo que o site de origem, a recuperação de sites aloca o mesmo endereço IP (da VM de origem) para a VM de réplica. O endereço IP é reservado no VMM, mas não está definida como o endereço IP de ativação pós-falha no anfitrião Hyper-V. O endereço IP de ativação pós-falha num anfitrião Hyper-v estiver definido para apenas antes da ativação pós-falha.
- Se o mesmo endereço IP não estiver disponível, a recuperação de sites aloca outro endereço IP disponível do conjunto.
- Se as VMs utilizam DHCP, a recuperação de Site não gere os endereços IP. Tem de verificar que o servidor DHCP no site secundário pode alocar endereço do mesmo intervalo de que o site de origem.

### <a name="validate-the-ip-address"></a>Validar o endereço IP

Depois de ativar a proteção para uma VM, pode utilizar seguintes script de exemplo para verificar o endereço atribuído à VM. O mesmo endereço IP será definido como o endereço IP de ativação pós-falha e atribuído à VM no momento da ativação pós-falha:

    ```
    $vm = Get-SCVirtualMachine -Name <VM_NAME>
    $na = $vm[0].VirtualNetworkAdapters>
    $ip = Get-SCIPAddress -GrantToObjectID $na[0].id
    $ip.address 
    ```

## <a name="changing-ip-addresses"></a>A alteração dos endereços IP

Neste cenário, os endereços IP de VMs com ativação pós-falha são alterados. A desvantagem desta solução é a manutenção necessária. Normalmente, DNS será atualizado depois de iniciar as VMs da réplica. As entradas de DNS poderão ter de ser alterado ou fluster em thenetwork e em entradas em cache atualizadas. Isto pode resultar num período de indisponibilidade. Período de indisponibilidade pode ser mitigado da seguinte forma:

- Utilize os valores TTL baixos para aplicações de intranet.
- Utilize o seguinte script no plano de recuperação a recuperação de Site, para atualizar o servidor DNS para garantir uma atualização atempadamente. Não precisa do script se utilizar o registo de DNS dinâmico.

    ```
    param(
    string]$Zone,
    [string]$name,
    [string]$IP
    )
    $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
    $newrecord = $record.clone()
    $newrecord.RecordData[0].IPv4Address  =  $IP
    Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
    ```
    
### <a name="example"></a>Exemplo 

Vamos ver um cenário no qual está a planear a utilização de endereços IP diferentes em principal e os sites de recuperação. Neste exemplo temos de endereços IP diferentes em sites primários e secundários e existe; o site de s uma terceira a partir dos quais as aplicações alojadas no site primário ou de recuperação pode ser acedido.

- Antes da ativação pós-falha, as aplicações são 192.168.1.0/24 sub-rede alojada no site primário e estão configuradas para serem na sub-rede 172.16.1.0/24 no site secundário após uma ativação pós-falha.
- Rotas de rede/ligações VPN foram configuradas corretamente para que todos os três sites podem aceder umas às outras.
- Após a ativação pós-falha, serão restauradas as aplicações na sub-rede de recuperação. Este cenário não é necessário para efetuar a ativação pós-falha de toda a sub-rede e sem alterações são necessárias para reconfigurar as rotas de rede ou VPN. A ativação pós-falha e algumas atualizações DNS, certifique-se de que as aplicações permanecem acessíveis.
- Se o DNS está configurado para permitir atualizações dinâmicas, em seguida, as VMs irão registar-se utilizando o novo endereço IP, quando for iniciado após a ativação pós-falha.

**Antes da ativação pós-falha**

![IP diferentes - antes da ativação pós-falha](./media/vmm-to-vmm-walkthrough-network/network-design10.png)

**Após a ativação pós-falha**

![IP diferentes - após a ativação pós-falha](./media/vmm-to-vmm-walkthrough-network/network-design11.png)




