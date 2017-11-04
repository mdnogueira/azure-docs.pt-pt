---
title: Conceber e implementar uma base de dados Oracle no Azure | Microsoft Docs
description: Conceber e implementar uma base de dados Oracle no seu ambiente do Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/22/2017
ms.author: rclaus
ms.openlocfilehash: c8f858bf249c4b56ad4fe60654ab489676eceb1f
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/17/2017
---
# <a name="design-and-implement-an-oracle-database-in-azure"></a>Conceber e implementar uma base de dados Oracle no Azure

## <a name="assumptions"></a>Pressupostos

- Está a planear migrar uma base de dados Oracle no local para o Azure.
- Ter uma compreensão das várias métricas nos relatórios de Oracle AWR.
- Ter uma compreensão de linha de base de desempenho da aplicação e a utilização de plataforma.

## <a name="goals"></a>Objetivos

- Compreenda como otimizar a implementação da Oracle no Azure.
- Explore as opções para uma base de dados Oracle num ambiente do Azure de otimização do desempenho.

## <a name="the-differences-between-an-on-premises-and-azure-implementation"></a>As diferenças entre no local e a implementação do Azure 

Seguem-se alguns pontos importantes a lembrar quando estiver a migrar aplicações no local para o Azure. 

Uma diferença importante é que uma implementação do Azure, recursos, tais como VMs, discos e redes virtuais são partilhados entre os outros clientes. Além disso, os recursos podem ser limitados com base nos requisitos. Em vez de concentrar-se no evitando (MTBF) a falhar, Azure mais concentra-se no reiniciadas a falha (MTTR).

A tabela seguinte lista algumas das diferenças entre uma implementação no local e uma implementação do Azure de uma base de dados Oracle.

> 
> |  | **Implementação no local** | **Implementação do Azure** |
> | --- | --- | --- |
> | **Redes** |LAN/WAN  |SDN (redes definidas por software)|
> | **Grupo de segurança** |Ferramentas de restrição de IP/porta |[Grupo de segurança de rede (NSG)](https://azure.microsoft.com/blog/network-security-groups) |
> | **Resiliência** |MTBF (tempo médio entre falhas) |MTTR (hora para recuperação)|
> | **Manutenção planeada** |Aplicação de patches/atualizações|[Conjuntos de disponibilidade](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines) (aplicação de patches/atualizações geridas pelo Azure) |
> | **Recurso** |Dedicado  |Partilhada com outros clientes|
> | **Regiões** |Datacenters |[Pares de região](https://docs.microsoft.com/azure/virtual-machines/windows/regions-and-availability)|
> | **Armazenamento** |Discos físicos/SAN |[Armazenamento gerido pelo Azure](https://azure.microsoft.com/pricing/details/managed-disks/?v=17.23h)|
> | **Dimensionamento** |Escala vertical |Dimensionamento horizontal|


### <a name="requirements"></a>Requisitos

- Determine a velocidade de tamanho e crescimento da base de dados.
- Determine os requisitos de IOPS, que pode fazer a estimativa baseada em relatórios Oracle AWR ou outra ferramentas de monitorização de rede.

## <a name="configuration-options"></a>Opções de configuração

Existem quatro áreas potenciais que podem otimizar para melhorar o desempenho num ambiente do Azure:

- Tamanho da máquina virtual
- Débito de rede
- Configurações e os tipos de disco
- Definições de cache do disco

### <a name="generate-an-awr-report"></a>Gerar um relatório AWR

Se tiver uma base de dados Oracle existente e estiver a planear migrar para o Azure, tem várias opções. Pode executar o relatório de Oracle AWR para obter as métricas (IOPS, Mbps, GiBs e assim sucessivamente). Em seguida, escolha a VM com base nas métricas que recolheu. Ou pode contactar a equipa de infraestrutura para obter informações semelhantes.

Poderá considerar executar relatório AWR durante regulares e pico cargas de trabalho, pelo que pode comparar. Com base nestes relatórios, pode dimensiona as VMs com base na carga de trabalho média ou a carga de trabalho máxima.

Segue-se um exemplo de como gerar um relatório AWR:

```bash
$ sqlplus / as sysdba
SQL> EXEC DBMS_WORKLOAD_REPOSITORY.CREATE_SNAPSHOT;
SQL> @?/rdbms/admin/awrrpt.sql
```

### <a name="key-metrics"></a>Métricas-chave

Seguem-se as métricas que pode obter a partir do relatório AWR:

- Número total de núcleos
- Velocidade de relógio de CPU
- Total de memória em GB
- Utilização da CPU
- Velocidade de transferência de dados das horas de ponta
- Taxa de alterações de e/s (leitura/escrita)
- Refazer a taxa de registo (MBPs)
- Débito de rede
- Taxa de latência de rede (baixa/alta)
- Tamanho de base de dados em GB
- Bytes recebidos através de SQL * Net de/para cliente

### <a name="virtual-machine-size"></a>Tamanho da máquina virtual

#### <a name="1-estimate-vm-size-based-on-cpu-memory-and-io-usage-from-the-awr-report"></a>1. Tamanho da VM estimativa com base na utilização da CPU, memória e e/s do relatório AWR

Um aspeto que poderá ver é os eventos de primeiro plano excedeu o tempo limite de cinco superior que indicam onde estão os congestionamentos de sistema.

Por exemplo, o diagrama a seguir, a sincronização de ficheiros de registo é na parte superior. Indica o número de aguarda que sejam necessários antes do LGWR escreve a memória intermédia de registo para o ficheiro de registo de Refazer. Estes resultados indicam que o melhor desempenho de armazenamento ou discos são necessários. Além disso, o diagrama mostra o número de CPU (núcleos) e a quantidade de memória.

![Captura de ecrã da página de relatório AWR](./media/oracle-design/cpu_memory_info.png)

O diagrama seguinte mostra a e/s total de leitura e escrita. Ocorreram GB 59 ler e GB 247.3 escritos durante o período do relatório.

![Captura de ecrã da página de relatório AWR](./media/oracle-design/io_info.png)

#### <a name="2-choose-a-vm"></a>2. Escolha uma VM

Com base nas informações que recolheu do relatório AWR, o próximo passo é escolher uma VM de um tamanho semelhante aos seus requisitos. Pode encontrar uma lista de VMs disponíveis no artigo [com otimização de memória](../../linux/sizes-memory.md).

#### <a name="3-fine-tune-the-vm-sizing-with-a-similar-vm-series-based-on-the-acu"></a>3. Otimizar o dimensionamento de VM com uma série VM semelhante com base no ACU

Depois que escolheu a VM, preste atenção para o ACU para a VM. Pode optar por utilizar uma VM diferente com base no valor ACU que melhor se adequa aos seus requisitos. Para obter mais informações, consulte [unidade de computação do Azure](https://docs.microsoft.com/azure/virtual-machines/windows/acu).

![Captura de ecrã da página de unidades ACU](./media/oracle-design/acu_units.png)

### <a name="network-throughput"></a>Débito de rede

O diagrama seguinte mostra a relação entre o débito e IOPS:

![Captura de ecrã de débito](./media/oracle-design/throughput.png)

O débito de rede total é estimado com base nas seguintes informações:
- SQL * tráfego de rede
- MBps x o número de servidores (sequência de saída, tais como o Oracle Data Guard)
- Outros fatores, tais como replicação de aplicação

![Captura de ecrã do SQL * débito Net](./media/oracle-design/sqlnet_info.png)

Com base nos seus requisitos de largura de banda de rede, existem vários tipos de gateway para escolher. Estes incluem basic VpnGw e Azure ExpressRoute. Para obter mais informações, consulte o [página de preços de gateway VPN](https://azure.microsoft.com/en-us/pricing/details/vpn-gateway/?v=17.23h).

**Recommendations (Recomendações)**

- Latência de rede superior é comparado com uma implementação no local. Reduzir rede arredondar viagens pode significativamente melhorar o desempenho.
- Para reduzir a ida e volta, consolide as aplicações que têm aplicações "chatty" ou de transações elevadas na mesma máquina virtual.

### <a name="disk-types-and-configurations"></a>Configurações e os tipos de disco

- *Predefinição discos de SO*: estes tipos de disco oferecem dados persistentes e colocação em cache. Estes estão otimizados para acesso de SO no arranque e não são concebidos para um transacional ou cargas de trabalho (analíticas) do armazém de dados.

- *Não gerido discos*: com estes tipos de disco, gerir as contas de armazenamento que armazenam os ficheiros de disco rígido virtual (VHD) que correspondem aos seus discos VM. Ficheiros VHD são armazenados como blobs de páginas em contas do storage do Azure.

- *Discos geridos pelo*: Azure gere as contas de armazenamento que utilizar para os discos VM. Especifique o tipo de disco (standard ou premium) e o tamanho do disco que precisa. Azure cria e gere o disco para si.

- *Discos de armazenamento Premium*: estes tipos de disco são mais adequados para cargas de trabalho de produção. Armazenamento Premium suporta discos VM que podem ser anexados a VMs de série de tamanho específicas, como série DS, série DSv2, GS e F VMs. O disco premium vem com tamanhos diferentes e pode escolher entre discos entre 32 GB e 4.096 GB. Cada tamanho do disco tem as suas próprias especificações de desempenho. Dependendo dos requisitos de aplicação, poderá anexar um ou mais discos para a VM.

Quando criar um novo disco gerido a partir do portal, pode escolher o **tipo de conta** para o tipo de disco que pretende utilizar. Tenha em atenção que nem todos os discos disponíveis são apresentados no menu pendente. Depois de escolher um tamanho VM específico, o menu mostra apenas o armazenamento premium disponíveis SKUs que se baseiam nesse tamanho VM.

![Captura de ecrã da página de disco gerido](./media/oracle-design/premium_disk01.png)

Para obter mais informações, consulte [elevado desempenho Premium Storage e discos geridos para VMs](https://docs.microsoft.com/azure/storage/storage-premium-storage).

Depois de configurar o armazenamento numa VM, pode querer carregar os discos de teste antes de criar uma base de dados. Saber a taxa de e/s em termos de latência e débito pode ajudar a determinar se as VMs suportam o débito esperado com destinos de latência.

Existem várias ferramentas para a aplicação teste de carga, como Oracle Orion, Sysbench e Fio.

Execute o teste de carga novamente depois de implementar uma base de dados Oracle. Iniciar as cargas de trabalho normais e utilização máxima e os resultados mostram a linha de base do seu ambiente.

Poderá ser mais importante para o tamanho do armazenamento baseado na taxa IOPS em vez do tamanho de armazenamento. Por exemplo, se o IOPS necessário é a 5000, mas só precisa de 200 GB, poderá obter o disco do P30 classe premium, apesar de este inclui mais de 200 GB de armazenamento.

A taxa IOPS pode ser obtida a partir do relatório AWR. Este é determinado pelo registo de Refazer, leituras físicas e escritas taxa.

![Captura de ecrã da página de relatório AWR](./media/oracle-design/awr_report.png)

Por exemplo, o tamanho de Refazer é 12,200,000 bytes por segundo, que é igual a 11.63 MBPs.
O IOPS é 12,200,000 / 2,358 = 5,174.

Depois de ter uma visão clara dos requisitos de e/s, pode escolher uma combinação de unidades que melhor se adequam a satisfazer essas necessidades.

**Recommendations (Recomendações)**

- Para o espaço de tabelas de dados, distribuídos a carga de trabalho de e/s por um número de discos utilizando o armazenamento gerido ou Oracle ASM.
- À medida que aumenta o tamanho do bloco e/s para operações que exigem muita leitura e escrita intensivas, adicione mais discos de dados.
- Aumente o tamanho do bloco para processos sequenciais grandes.
- Utiliza a compressão de dados para reduzir e/s (para dados e índices).
- Separe os registos de Refazer, sistema e temps e anular TS em discos de dados separada.
- Não coloque os ficheiros de aplicação em discos de SO predefinido (dev/sda). Estes discos não estão otimizados para a VM rápida tempos de arranque e poderá não fornecer um bom desempenho para a sua aplicação.

### <a name="disk-cache-settings"></a>Definições de cache do disco

Existem três opções para a colocação em cache do anfitrião:

- *Só de leitura*: todos os pedidos são colocados em cache para leituras futuras. Todas as escritas são mantidas diretamente ao armazenamento de Blobs do Azure.

- *Leitura e escrita*: Este é um algoritmo "antecipadas". As leituras e escritas são colocadas em cache para leituras futuras. Não-escrita-através de escritas são mantidas primeiro para a cache local. Para o SQL Server, escritas são mantidas ao Storage do Azure porque utiliza através de escrita. Também fornece a menor latência de disco para cargas de trabalho leves.

- *Nenhum* (desativada): ao utilizar esta opção, pode ignorar a cache. Todos os dados são transferidos para o disco e persistente ao Storage do Azure. Este método dá-lhe a taxa de e/s mais elevada para cargas de trabalho exigente em termos de e/s. Também precisa de tomar "custos de transação" em consideração.

**Recommendations (Recomendações)**

Para maximizar o débito, recomendamos que comece com **nenhum** para a colocação em cache do anfitrião. Para o Premium Storage, tenha em atenção que tem de desativar as "barreiras as eficazes" ao montar o sistema de ficheiros com o **ReadOnly** ou **nenhum** opções. Atualize o ficheiro de /etc/fstab com o UUID aos discos.

Para obter mais informações, consulte [Premium Storage para VMs com Linux](https://docs.microsoft.com/azure/storage/storage-premium-storage#premium-storage-for-linux-vms).

![Captura de ecrã da página de disco gerido](./media/oracle-design/premium_disk02.png)

- Para discos de SO, utilizar a predefinição **leitura/escrita** colocação em cache.
- Para o sistema, TEMP e anulação utilizar **nenhum** para colocar em cache.
- Para os dados, utilize **nenhum** para colocar em cache. Mas se a base de dados é só de leitura ou de leitura intensiva, utilize **só de leitura** colocação em cache.

Depois da definição de disco de dados é guardada, não é possível alterar a definição de cache do anfitrião, a menos que desmonte a unidade ao nível do SO e, em seguida, voltar a montá-lo Depois de efetuadas a alteração.


## <a name="security"></a>Segurança

Depois de configurar e configurar o seu ambiente do Azure, o passo seguinte consiste em proteger a sua rede. Seguem-se algumas recomendações:

- *Política NSG*: NSG pode ser definido por uma sub-rede ou NIC. É mais simples para controlar o acesso ao nível da sub-rede, tanto para segurança e a imposição de encaminhamento para coisas como firewalls de aplicação.

- *Jumpbox*: para um acesso mais seguro, os administradores devem não ligar diretamente para o serviço de aplicações ou a base de dados. Um jumpbox é utilizado como um suporte de dados entre a máquina de administrador e os recursos do Azure.
![Captura de ecrã da página de topologia Jumpbox](./media/oracle-design/jumpbox.png)

    A máquina de administrador deve oferecer acesso restrito de IP para o jumpbox apenas. O jumpbox deve ter acesso à aplicação e da base de dados.

- *Rede privada* (sub-redes): Recomendamos que tenha o serviço de aplicações e a base de dados em sub-redes diferentes, para melhorar o controlo pode ser definido pela política NSG.


## <a name="additional-reading"></a>Leitura adicional

- [Configurar Oracle ASM](configure-oracle-asm.md)
- [Configurar a proteção de dados Oracle](configure-oracle-dataguard.md)
- [Configurar a porta de Golden Oracle](configure-oracle-golden-gate.md)
- [Cópia de segurança do Oracle e recuperação](oracle-backup-recovery.md)

## <a name="next-steps"></a>Passos seguintes

- [Tutorial: Criar as VMs de elevada disponibilidade](../../linux/create-cli-complete.md)
- [Explorar amostras de CLI do Azure de implementação de VM](../../linux/cli-samples.md)
