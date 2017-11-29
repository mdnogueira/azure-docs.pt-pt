---
title: "Testar a ativação pós-falha (VMM para o VMM) no Azure Site Recovery | Microsoft Docs"
description: "O Azure Site Recovery coordena a replicação, ativação pós-falha e recuperação de máquinas virtuais e servidores físicos. Saiba mais sobre a ativação pós-falha para o Azure ou num datacenter secundário."
services: site-recovery
documentationcenter: 
author: ponatara
manager: abhemraj
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/22/2017
ms.author: ponatara
ms.openlocfilehash: 2730cebc1cdc47db283ae851560d93fdbf50ee48
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/28/2017
---
# <a name="test-failover-vmm-to-vmm-in-site-recovery"></a>Testar a ativação pós-falha (VMM para o VMM) no Site Recovery


Este artigo fornece informações e instruções para efetuar uma ativação pós-falha de teste ou um exercício de recuperação (DR) de desastre de máquinas virtuais (VMs) e servidores físicos que estão protegidas com o Azure Site Recovery. Irá utilizar um site gerido pelo VMM do System Center Virtual Machine Manager no local como o site de recuperação.

Execute uma ativação pós-falha de teste para validar a sua estratégia de replicação ou efetuar uma pormenorização DR sem qualquer perda de dados ou o período de indisponibilidade. Uma ativação pós-falha de teste não tem qualquer impacto sobre a replicação em curso ou no seu ambiente de produção. Pode executá-la numa máquina virtual ou um [plano de recuperação](site-recovery-create-recovery-plans.md). Quando está a acionar uma ativação pós-falha de teste, terá de especificar que as máquinas virtuais de teste se irão ligar à rede. Pode controlar o progresso da ativação pós-falha de teste no **tarefas** página.  

Se tiver comentários ou perguntas, publicá-las na parte inferior deste artigo ou no [fórum de serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="prepare-the-infrastructure-for-test-failover"></a>Preparar a infraestrutura de ativação pós-falha de teste
Se pretender executar uma ativação pós-falha de teste ao utilizar uma rede existente, prepare o DNS, DHCP e do Active Directory nessa rede.

Se pretender executar uma ativação pós-falha de teste utilizando a opção para criar automaticamente a redes VM, adicione um passo manual antes de 1 de grupo no plano de recuperação que vai utilizar a ativação pós-falha de teste. Em seguida, adicione os recursos de infraestrutura para a rede automaticamente criada antes de executar a ativação pós-falha de teste.

### <a name="things-to-note"></a>Coisas a salientar
Quando está a replicar para um site secundário, o tipo de rede que utiliza a máquina de réplica não necessita de corresponder ao tipo de rede lógica utilizada para ativação pós-falha de teste, mas algumas combinações poderão não funcionar. Se a réplica utiliza DHCP e do isolamento baseado em VLAN, a rede VM para a réplica não necessita de um conjunto de endereços IP estáticos. Ao utilizar a virtualização de rede do Windows para a ativação pós-falha de teste poderá não funciona porque não existem conjuntos de endereços estão disponíveis. 

Além disso, a ativação pós-falha de teste não funcionará se a rede de réplica utiliza sem isolamento e a rede de teste utiliza Virtualização de rede do Windows. Isto acontece porque a rede sem isolamento não tem sub-redes necessárias para criar uma rede de Virtualização de rede do Windows.

Como máquinas virtuais de réplica estão ligadas a redes VM mapeadas após a ativação pós-falha depende de como a rede VM está configurada na consola do VMM.

#### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Rede VM configurada sem isolamento nem isolação de VLAN
Se o DHCP é definido para a rede VM, a máquina virtual de réplica está ligada para o ID de VLAN através de definições que são especificadas para o site de rede na rede lógica associada. A máquina virtual recebe o respetivo endereço IP do servidor DHCP disponível. 

Não precisa de definir um conjunto de endereços IP estáticos para a rede VM de destino. Se for utilizado um conjunto de endereços IP estáticos para a rede VM, a máquina virtual de réplica está ligada para o ID de VLAN através de definições que são especificadas para o site de rede na rede lógica associada.

A máquina virtual recebe o respetivo endereço IP do conjunto que está definido para a rede VM. Se não está definido um conjunto de endereços IP estático na rede VM de destino, a atribuição de endereços IP irá falhar. Crie conjunto de endereços IP em servidores do VMM de origem e de destino que irá utilizar para proteção e recuperação.

#### <a name="vm-network-with-windows-network-virtualization"></a>Rede VM com Virtualização de rede do Windows
Se estiver configurada uma rede VM com Virtualização de rede do Windows, deve definir um conjunto estático para a rede VM de destino, independentemente se a rede VM de origem está configurada para utilizar DHCP ou um conjunto de endereços IP estáticos. 

Se definir o DHCP, o servidor do VMM de destino age como um servidor DHCP e fornece um endereço IP do conjunto que está definido para a rede VM de destino. Se a utilização de um conjunto de endereços IP estáticos é definida para o servidor de origem, o VMM de destino atribui um endereço IP do conjunto. Em ambos os casos, a atribuição de endereços IP irá falhar se não for definido um conjunto de endereços IP estáticos.


### <a name="prepare-dhcp"></a>Preparar o DHCP
Se as máquinas virtuais envolvido na ativação pós-falha de teste utilizar DHCP, crie um servidor DHCP de teste dentro da rede para efeitos de ativação pós-falha de teste isolado.

### <a name="prepare-active-directory"></a>Preparar do Active Directory
Para executar uma ativação pós-falha de teste para a aplicação de teste, terá de uma cópia do ambiente de produção do Active Directory no seu ambiente de teste. Para obter mais informações, consulte o [considerações de ativação pós-falha do Active Directory](site-recovery-active-directory.md#test-failover-considerations).

### <a name="prepare-dns"></a>Preparar o DNS
Prepare um servidor DNS para a ativação pós-falha de teste da seguinte forma:

* **DHCP**: se as máquinas virtuais utilizar DHCP, o endereço IP do teste DNS deve ser atualizado no servidor DHCP de teste. Se estiver a utilizar um tipo de rede de Virtualização de rede do Windows, o servidor VMM atua como servidor DHCP. Por conseguinte, o endereço IP do DNS deve ser atualizado na rede de ativação pós-falha de teste. Neste caso, as máquinas virtuais registar-se ao servidor DNS relevante.
* **Endereço estático**: se as máquinas virtuais utilizar um endereço IP estático, o endereço IP do servidor DNS de teste deve ser atualizado na rede de ativação pós-falha de teste. Poderá ter de atualizar o DNS com o endereço IP de máquinas de virtuais de teste. Pode utilizar o seguinte script de exemplo para esta finalidade:

        Param(
        [string]$Zone,
        [string]$name,
        [string]$IP
        )
        $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
        $newrecord = $record.clone()
        $newrecord.RecordData[0].IPv4Address  =  $IP
        Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord



## <a name="run-a-test-failover"></a>Executar uma ativação pós-falha de teste
Este procedimento descreve como executar uma ativação pós-falha de teste para um plano de recuperação. Em alternativa, pode executar a ativação pós-falha para uma única máquina virtual no **máquinas virtuais** separador.

![Painel de ativação pós-falha de teste](./media/site-recovery-test-failover-vmm-to-vmm/TestFailover.png)

1. Selecione **planos de recuperação** > *recoveryplan_name*. Clique em **ativação pós-falha** > **ativação pós-falha de teste**.
1. No **ativação pós-falha de teste** painel, especifique a forma como as máquinas virtuais devem estar ligadas a redes após a ativação pós-falha de teste. Para obter mais informações, consulte o [opções de rede](#network-options-in-site-recovery).
1. Controlar o progresso de ativação pós-falha no **tarefas** separador.
1. Após a conclusão da ativação pós-falha, certifique-se de que as máquinas virtuais iniciada com êxito.
1. Quando tiver terminado, clique em **ativação pós-falha de teste de limpeza** no plano de recuperação. No **notas**, registar e guardar todas as observações associadas à ativação pós-falha de teste. Este passo elimina as máquinas virtuais e redes que foram criados durante a ativação pós-falha de teste.


## <a name="network-options-in-site-recovery"></a>Opções de rede no Site Recovery

Quando executar uma ativação pós-falha de teste, está a pedido para selecionar as definições de rede para máquinas de réplica de teste. Tem várias opções.  

| **Opção de ativação pós-falha de teste** | **Descrição** | **Verificação de ativação pós-falha** | **Detalhes** |
| --- | --- | --- | --- |
| **Efetuar a ativação pós-falha para um site secundário do VMM - sem rede** |Não selecione uma rede VM. |Ativação pós-falha verifica que são criadas máquinas de teste.<br/><br/>A máquina virtual de teste é criada no anfitrião onde existe a máquina virtual de réplica. Não é adicionada a para a nuvem onde está localizada a máquina virtual de réplica. |<p>A máquina de efetuada a ativação pós-falha não está ligada a nenhuma rede.<br/><br/>A máquina pode ser ligada a uma rede VM depois de terem sido criadas. |
| **Efetuar a ativação pós-falha para um site secundário do VMM – com a rede** |Selecione uma rede VM. |Ativação pós-falha verifica se as máquinas virtuais são criadas. |A máquina virtual de teste é criada no anfitrião onde existe a máquina virtual de réplica. Não é adicionada a para a nuvem onde está localizada a máquina virtual de réplica.<br/><br/>Crie uma rede VM que está isolada da sua rede de produção.<br/><br/>Se estiver a utilizar uma rede baseada em VLAN, recomendamos que crie uma rede lógica separada (não utilizada em produção) no VMM para esta finalidade. Esta rede lógica é utilizada para criar redes VM para as ativações pós-falha de teste.<br/><br/>A rede lógica deve estar associada a, pelo menos, um dos adaptadores de rede de todos os servidores de Hyper-V que alojam máquinas virtuais.<br/><br/>Para redes lógicas VLAN, os sites de rede que adicionar à rede lógica devem ser isolados.<br/><br/>Se estiver a utilizar uma rede lógica baseada em Virtualização de rede do Windows, o Azure Site Recovery cria automaticamente redes isoladas de VM. |
| **Efetuar a ativação pós-falha para um site VMM secundário – criar uma rede** |Uma rede de teste temporário é criada automaticamente com base nas definições que especificou nas **rede lógica** e os respetivos sites de rede relacionadas. |Ativação pós-falha verifica se as máquinas virtuais são criadas. |Utilize esta opção se o plano de recuperação utiliza mais do que uma rede VM. Se estiver a utilizar redes de Virtualização de rede do Windows, esta opção pode criar automaticamente redes VM com as mesmas definições (sub-redes e conjuntos de endereços IP) na rede da máquina virtual de réplica. Estas redes VM são automaticamente limpa após a conclusão da ativação pós-falha de teste.</p><p>A máquina virtual de teste é criada no anfitrião onde existe a máquina virtual de réplica. Não é adicionada a para a nuvem onde está localizada a máquina virtual de réplica. |

> [!TIP]
> O endereço IP especificado para uma máquina virtual durante a ativação pós-falha de teste é o mesmo endereço IP que receberia a máquina virtual para uma ativação de pós-falha planeada ou imprevista (presuming que o endereço IP está disponível na rede de ativação pós-falha de teste). Se o mesmo endereço IP não está disponível na rede de ativação pós-falha de teste, a máquina virtual recebe outro endereço IP que está disponível na rede de ativação pós-falha de teste.
>
>


## <a name="test-failover-to-a-production-network-on-a-recovery-site"></a>Ativação pós-falha de teste para uma rede de produção de um site de recuperação
Recomendamos que, quando estiver a efetuar uma ativação pós-falha de teste, pode escolher uma rede que é diferente da sua rede de site de recuperação de produção que forneceu no [mapeamento da rede](https://docs.microsoft.com/azure/site-recovery/site-recovery-network-mapping). Mas se a certeza de que pretende validar a conectividade de rede ponto a ponto numa máquina virtual efetuada a ativação pós-falha, tenha em atenção os seguintes pontos:

* Certifique-se de que a máquina virtual primária está encerrada quando estiver a fazer a ativação pós-falha de teste. Se não o fizer, duas máquinas virtuais com a mesma identidade será executado na mesma rede ao mesmo tempo. Nessa situação pode levar a consequências indesejadas.
* As alterações que efetuar para as máquinas de virtuais de ativação pós-falha de teste serão perdidas quando limpar as máquinas de virtuais de ativação pós-falha de teste. Estas alterações não são replicadas para a máquina virtual primária.
* Desta forma de fazer testes servem como o período de indisponibilidade para a sua aplicação de produção. Peça aos utilizadores da aplicação para não utilizar a aplicação quando a simulação de recuperação está em curso.  


## <a name="next-steps"></a>Passos seguintes
Depois de executar uma ativação pós-falha de teste, pode tentar uma [ativação pós-falha](site-recovery-failover.md).
