---
title: VMs do Linux do Azure e o armazenamento do Azure | Microsoft Docs
description: "Descreve do Azure Standard e Premium Storage e gerido e discos não geridos com máquinas virtuais do Linux."
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: vlivech
manager: timlt
editor: 
ms.assetid: d364c69e-0bd1-4f80-9838-bbc0a95af48c
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 2/7/2017
ms.author: rasquill
ms.openlocfilehash: 934580f6fcfdbff6e61626ed685459478559717d
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="azure-and-linux-vm-storage"></a>Armazenamento do Azure e a VM com Linux
O Storage do Azure é a solução de armazenamento na nuvem para aplicações modernas que dependem da durabilidade, da disponibilidade e da escalabilidade para satisfazer as necessidades dos seus clientes.  Para permitir que os programadores criem aplicações em grande escala para suportar novos cenários, além de armazenamento do Azure também fornece a base de armazenamento para máquinas virtuais do Azure.

## <a name="managed-disks"></a>Managed Disks

As VMs do Azure estão agora disponíveis utilizando [Azure geridos discos](../windows/managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), que permite-lhe criar as suas VMs sem criar ou gerir qualquer [contas de armazenamento do Azure](../../storage/common/storage-introduction.md) por si. Especifique se pretende ou armazenamento Standard e Premium grande como deve ser o disco e os discos da VM para o Azure cria. As VMs com discos geridos tem várias funcionalidades importantes, incluindo:

- Suporte de escalabilidade automática. O Azure cria os discos e gere o armazenamento subjacente para suportar até 10 000 discos por subscrição.
- Uma maior fiabilidade com conjuntos de disponibilidade. Azure garante que os discos VM estão isolados umas das outras dentro de conjuntos de disponibilidade automaticamente.
- Controlo de acesso de aumento. Discos geridos expõem uma variedade de operações controlado pelo [controlo de acesso em funções do Azure (RBAC)](../../active-directory/role-based-access-control-what-is.md).

Preços para discos geridos são diferente para que os discos não geridos. Para essas informações, consulte [preços e faturação para discos geridos](../windows/managed-disks-overview.md#pricing-and-billing).

Pode converter VMs existentes que utilizam discos não geridos para utilizar discos geridos com [converter az vm](/cli/azure/vm#convert). Para obter mais informações, consulte [como converter uma VM com Linux de discos não geridos para o Azure geridos discos](convert-unmanaged-to-managed-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Não é possível converter um disco não gerido num disco geridos se o disco não gerido numa conta do storage ou em qualquer altura foi, encriptada utilizando [encriptação de serviço de armazenamento do Azure (SSE)](../../storage/common/storage-service-encryption.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Como converter discos não geridos que são, ou tem sido, uma conta de armazenamento encriptados de detalhe os seguintes passos:

- Copie o disco rígido virtual (VHD) com [início de cópia de BLOBs de armazenamento az](/cli/azure/storage/blob/copy#start) para uma conta de armazenamento que nunca foi ativada para a encriptação do serviço de armazenamento do Azure.
- Criar uma VM que utiliza discos geridos e especificar se o ficheiro VHD durante a criação com [az vm criar](/cli/azure/vm#create), ou
- Anexe o VHD copiado com [anexar o disco da vm az](/cli/azure/vm/disk#attach) para uma VM em execução com discos geridos.


## <a name="azure-storage-standard-and-premium"></a>Storage do Azure: Standard e Premium
As VMs do Azure – se utilizar discos geridos ou não gerido – podem ser criados com base no discos de armazenamento standard ou discos de armazenamento premium. Quando utilizar o portal para escolher a VM deve acionar um menu pendente no **Noções básicas** ecrã para ver os discos standard e premium. Quando ativado para SSD, armazenamento de premium só serão apresentadas as VMs ativadas, todas as cópias de SSD unidades.  Quando tivada/desativada para HDD, com armazenamento standard ativado VMs por girar unidades de disco são apresentadas, juntamente com o premium storage VMs associada a SSD.

Ao criar uma VM a partir de `azure-cli` pode escolher entre padrão e premium ao escolher o tamanho da VM através o `-z` ou `--vm-size` sinalizador cli.

## <a name="creating-a-vm-with-a-managed-disk"></a>Criar uma VM com um disco gerido

O exemplo seguinte requer o Azure CLI 2.0, que pode [instalar aqui](/cli/azure/install-azure-cli).

Em primeiro lugar, crie um grupo de recursos para gerir os recursos com [criar grupo az](/cli/azure/group#create):

```azurecli
az group create --location westus --name myResourceGroup
```

Agora criar a VM com [az vm criar](/cli/azure/vm#create). Especifique um único `--public-ip-address-dns-name` argumento, como `mypublicdns` provavelmente é executada.

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --public-ip-address-dns-name mypublicdns
```

O exemplo anterior cria uma VM com um disco gerido numa conta do Standard storage. Para utilizar uma conta de armazenamento Premium, adicione o `--storage-sku Premium_LRS` argumento, semelhante ao seguinte exemplo:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --public-ip-address-dns-name mypublicdns \
    --storage-sku Premium_LRS
```

## <a name="standard-storage"></a>Armazenamento Standard
Armazenamento padrão do Azure é o tipo de predefinição de armazenamento.  Armazenamento Standard também é rentável enquanto performant.  

## <a name="premium-storage"></a>Armazenamento Premium
Armazenamento Premium do Azure fornece suporte de disco de elevado desempenho, baixa latência para máquinas virtuais em execução I/O intensivo cargas de trabalho. Discos de máquina virtual (VM) que utilizam o armazenamento Premium armazenam dados em unidades de estado sólido (SSDs). Pode migrar discos VM da sua aplicação para o armazenamento do Azure Premium para tirar partido da velocidade e o desempenho destes discos.

Funcionalidades de armazenamento Premium:

* Os discos de armazenamento Premium: O Premium Storage do Azure suporta discos VM que podem ser anexados a série DS, série DSv2 ou GS VMs do Azure.
* Blob de página Premium: Armazenamento Premium suporta Blobs de páginas do Azure, que são utilizados para armazenar discos persistentes para máquinas virtuais do Azure (VMs).
* Armazenamento localmente redundante Premium: Uma conta de armazenamento Premium apenas suporta localmente redundante armazenamento (LRS) como a opção de replicação e mantém três cópias dos dados numa única região.
* [Armazenamento Premium](../windows/premium-storage.md)

## <a name="premium-storage-supported-vms"></a>Armazenamento Premium suportado VMs
Armazenamento Premium suporta-série DS, série DSv2-série, série GS e série Fs Virtual Machines do Azure (VMs). Pode utilizar discos de armazenamento Standard e Premium com suportado de VMs do Premium Storage. Mas não é possível utilizar discos de armazenamento Premium com a série VM, que não é compatíveis do Premium Storage.

Seguem-se as distribuições de Linux que foi validadas com o armazenamento Premium.

| Distribuição | Versão | Kernel suportado |
| --- | --- | --- |
| Ubuntu |12.04 |3.2.0-75.110+ |
| Ubuntu |14.04 |3.13.0-44.73+ |
| Debian |7, 8. x |3.16.7-ckt4-1+ |
| SLES |SLES 12 |3.12.36-38.1+ |
| SLES |SLES 11 SP4 |3.0.101-0.63.1+ |
| CoreOS |584.0.0+ |3.18.4+ |
| Centos |6.5, 6.6, 6.7, 7.0, 7.1 |3.10.0-229.1.2.el7+ |
| RHEL |6.8+, 7.2+ | |

## <a name="azure-file-storage"></a>File storage do Azure
File storage do Azure oferece partilhas de ficheiros na nuvem utilizando o protocolo SMB padrão. Com os ficheiros do Azure, pode migrar as aplicações empresariais que se baseiam em servidores de ficheiros para o Azure. As aplicações em execução no Azure podem montar facilmente partilhas de ficheiros de máquinas virtuais do Azure com o Linux. E com a versão mais recente do armazenamento de ficheiros, também podem montar uma partilha de ficheiros de uma aplicação no local que suporta o SMB 3.0.  Como partilhas de ficheiros são partilhas SMB, pode aceder-lhes através de APIs do sistema de ficheiros padrão.

Armazenamento de ficheiros baseia-se a ser a mesma tecnologia como armazenamento de Blob, tabela e fila, para que o File storage oferece a disponibilidade, características de durabilidade, escalabilidade e redundância geográfica que baseia-se na plataforma de armazenamento do Azure. Para obter mais informações sobre metas de desempenho de armazenamento de ficheiros e limites, consulte metas de desempenho e escalabilidade do Storage do Azure.

* [How to use Azure File Storage with Linux (Como utilizar o Armazenamento de Ficheiros do Azure com o Linux)](../../storage/files/storage-how-to-use-files-linux.md)

## <a name="hot-storage"></a>Armazenamento frequente
A camada de armazenamento frequente do Azure está otimizada para armazenar dados que são acedidos com frequência.  Armazenamento de acesso frequente é o tipo de armazenamento predefinido para os arquivos de blob.

## <a name="cool-storage"></a>Armazenamento de acesso esporádico
Camada de armazenamento esporádico do Azure está otimizada para armazenar dados que são raramente acedidos e de longa duração. Casos de utilização de exemplo para armazenamento esporádico incluem as cópias de segurança, conteúdo do suporte de dados, dados científicos, conformidade e os dados de arquivo. Em geral, quaisquer dados que raramente são acedidos são uma candidata perfeita para armazenamento de acesso esporádico.

|  | Camada de armazenamento frequente | Camada de armazenamento esporádico |
|:--- |:---:|:---:|
| Disponibilidade |99,9% |99% |
| Disponibilidade (leituras de RA-GRS) |99,99% |99,9% |
| Custos de utilização |Custos de armazenamento superiores |Custos de armazenamento inferiores |
| Menor de acesso |Acesso superior | |
| e os custos de transação |e os custos de transação | |

## <a name="redundancy"></a>Redundância
Os dados na sua conta de armazenamento do Microsoft Azure estão sempre replicados para garantir durabilidade e elevada disponibilidade, a cumprir o SLA de armazenamento do Azure, mesmo face a falhas de hardware transitórias.

Quando cria uma conta do Storage, tem de selecionar uma das seguintes opções de replicação:

* Armazenamento localmente redundante (LRS)
* Armazenamento com redundância de zona (ZRS)
* Armazenamento georredundante (GRS)
* Armazenamento georredundante com acesso de leitura (RA-GRS)

### <a name="locally-redundant-storage"></a>Armazenamento localmente redundante
Armazenamento localmente redundante (LRS) replica os dados numa região na qual criou a conta de armazenamento. Para maximizar a durabilidade, todos os pedidos efetuados em relação a dados na sua conta de armazenamento é replicado três vezes. Estas três réplicas de cada residirem em domínios de falhas separada e domínios de atualização.  Um pedido com êxito devolve apenas depois de ter sido escrito para todos os três réplicas.

### <a name="zone-redundant-storage"></a>Armazenamento com redundância de zona
Armazenamento com redundância de zona (ZRS) replica os dados em dois ou três locais, numa única região ou em duas regiões, fornecendo uma durabilidade superior ao LRS. Se a sua conta do storage tiver ativado o ZRS, em seguida, os dados são duráveis mesmo em caso de falha das instalações de.

### <a name="geo-redundant-storage"></a>Armazenamento georredundante
Armazenamento georredundante (GRS) replica os dados para uma região secundária centenas de quilómetros região primária. Se a sua conta do storage tiver GRS ativada, em seguida, os dados são duráveis mesmo em caso de uma falha regional completa ou de um desastre no qual a região primária não é recuperável.

### <a name="read-access-geo-redundant-storage"></a>Armazenamento georredundante com acesso de leitura
Armazenamento georredundante com acesso de leitura (RA-GRS) maximiza a disponibilidade para a sua conta de armazenamento, fornecendo acesso só de leitura aos dados na localização secundária, além de replicação em duas regiões fornecida pelo GRS. No caso de dados ficam disponíveis na região primária, a aplicação pode ler dados a partir da região secundária.

Para obter uma descrição profunda em Consulte de redundância do armazenamento do Azure:

* [Replicação do Armazenamento do Azure](../../storage/common/storage-redundancy.md)

## <a name="scalability"></a>Escalabilidade
O Storage do Azure é extremamente dimensionável, pelo que pode armazenar e processar centenas de terabytes de dados para suportar os cenários de macrodados exigidos pela análise científica e financeira e pelas aplicações de suporte de dados. Em alternativa, pode armazenar as pequenas quantidades de dados necessárias para um Web site de pequenas empresas. Sejam quais forem as suas necessidades, paga apenas os dados que está a armazenar. Atualmente, o Storage do Azure armazena dezenas de biliões de objetos de clientes únicos e processa, em média, milhões de pedidos por segundo.

Para contas de armazenamento standard: uma conta de armazenamento standard tem uma taxa de pedidos total máximo de 20.000 IOPS. O IOPS total em todos os discos da máquina virtual numa conta de armazenamento standard não deve exceder este limite.

Para contas de armazenamento premium: uma conta de armazenamento premium tem uma taxa de débito total máximo de 50 Gbps. O débito total em todos os discos da VM não deve exceder este limite.

## <a name="availability"></a>Disponibilidade
Garantimos que durante 99,99% (99,9% para o Escalão de Acesso Esporádico) do tempo, pelo menos, processaremos com êxito os pedidos para ler dados de Contas de Armazenamento Georredundante com Acesso de Leitura (RA-GRS), na condição de que as tentativas falhadas de ler dados da região primária sejam repetidas na região secundária.

Garantimos que durante 99,9% (99% para o Escalão de Acesso Esporádico) do tempo, pelo menos, processaremos com êxito os pedidos para ler dados a partir de Contas de Armazenamento Localmente Redundante (LRS), de Armazenamento com Redundância de Zona (ZRS) e de Armazenamento Georredundante (GRS).

Garantimos que durante 99,9% (99% para o Escalão de Acesso Esporádico) do tempo, pelo menos, processaremos com êxito os pedidos para escrever dados para Contas de Armazenamento Localmente Redundante (LRS), de Armazenamento com Redundância de Zona (ZRS) e de Armazenamento Georredundante (GRS) e de Armazenamento Georredundante com Acesso de Leitura (RA-GRS).

* [SLA do Azure para o armazenamento](https://azure.microsoft.com/support/legal/sla/storage/v1_1/)

## <a name="regions"></a>Regiões
Azure está normalmente disponível em 30 regiões em todo o mundo e tem anunciada planos para 4 regiões adicionais. A expansão geográfica é uma prioridade para o Azure, pois permite que os nossos clientes atinjam níveis de desempenho mais elevados e suporta os requisitos e as preferências dos clientes relativamente à localização dos dados.  Região mais recente do azures Iniciar é na Alemanha.

Os Datacenters da Microsoft Cloud oferece uma opção diferenciada para os serviços do Microsoft Cloud já disponíveis na Europa, criar maiores oportunidades de inovação e económico crescimento para elevada disponibilidade regulados parceiros e os clientes na Alemanha, o União Europeia (EU) e a associação de alfabetos livres compromissos (EFTA).

Dados de cliente nestes centros de dados nova, Magdeburg e Frankfurt, são geridos sob o controlo de uma trustee de dados, internacionais de sistemas de T, uma empresa em alemão independente e subsidiária de Deutsche Telekom. Os serviços em nuvem comerciais da Microsoft nestes centros de dados cumprem em alemão de regulamentos de manuseamento de dados e dar aos clientes opções adicionais de como e onde os dados são processados.

* [Mapa de regiões do Azure](https://azure.microsoft.com/regions/)

## <a name="security"></a>Segurança
Storage do Azure fornece um conjunto completo de capacidades de segurança que em conjunto permitem aos programadores criarem aplicações seguras. A conta de armazenamento em si pode ser protegida utilizando o controlo de acesso baseado em funções e o Azure Active Directory. Dados podem ser protegidos em trânsito entre uma aplicação e o Azure utilizando a encriptação do lado do cliente, HTTPS ou SMB 3.0. Dados podem ser definidos para ser encriptados automaticamente quando escrita ao Storage do Azure utilizando a encriptação de serviço de armazenamento (SSE). SO e discos de dados utilizados por máquinas virtuais podem ser definidos para ser encriptado utilizando o Azure Disk Encryption. Pode ser concedido acesso delegado aos objetos de dados no armazenamento do Azure através de assinaturas de acesso partilhado.

### <a name="management-plane-security"></a>Gestão Plane segurança
O plane de gestão é constituído pelos recursos utilizados para gerir a sua conta de armazenamento. Nesta secção, iremos falar sobre o modelo de implementação Azure Resource Manager e como utilizar o controlo de acesso baseado em funções (RBAC) para controlar o acesso às suas contas de armazenamento. Também irá abordadas gerir as chaves de conta de armazenamento e como voltar a gerá-los.

### <a name="data-plane-security"></a>Segurança dos dados Plane
Nesta secção, vamos abordar permitir o acesso os objetos de dados real na sua conta de armazenamento, tais como blobs, ficheiros, filas e tabelas, utilizando as assinaturas de acesso partilhado e políticas de acesso armazenada. Abordaremos SAS de nível de serviço e o nível de conta SAS. Podemos também verá como limitar o acesso a um endereço IP específico (ou intervalo de endereços IP), como limitar o protocolo utilizado para HTTPS e como revogar uma assinatura de acesso partilhado sem aguardar que expire.

## <a name="encryption-in-transit"></a>Encriptação em trânsito
Esta secção descreve como proteger dados quando a transferência ou a sair do armazenamento do Azure. Iremos falar sobre a utilização recomendada de HTTPS e a encriptação utilizado pelo SMB 3.0 para partilhas de ficheiros do Azure. Iremos irá também observe encriptação do lado do cliente, que permite-lhe para encriptar os dados antes de é transferido para o armazenamento numa aplicação de cliente e para desencriptar os dados depois de serem transferidos fora do armazenamento.

## <a name="encryption-at-rest"></a>Encriptação de Inativos
Serão abordadas encriptação de serviço de armazenamento (SSE), e como pode ativá-la para uma conta de armazenamento, resultando nos blobs de blocos, blobs de páginas a ser encriptados automaticamente quando escrita ao Storage do Azure de blobs de acréscimo. Também veremos como pode utilizar o Azure Disk Encryption e explorar as diferenças básicas e casos de encriptação de disco versus SSE versus encriptação do lado do cliente. Iremos brevemente abordar a conformidade FIPS para E.U.A. Computadores Government.

* [Guia de segurança de armazenamento do Azure](../../storage/common/storage-security-guide.md)

## <a name="temporary-disk"></a>Disco temporário
Cada VM contém um disco temporário. O disco temporário fornece armazenamento de curta duração para aplicações e processos e destina-se para armazenar apenas os dados, tais como ficheiros de página ou a troca. Dados no disco temporário podem ser perdidos durante uma [evento de manutenção](manage-availability.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#understand-vm-reboots---maintenance-vs-downtime) ou quando a [voltar a implementar uma VM](redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Durante um reinício padrão da VM, devem manter os dados na unidade temporária.

Em máquinas virtuais do Linux, o disco é normalmente **/dev/sdb** é formatado e montado para **/mnt** pelo agente Linux do Azure. O tamanho do disco temporário varia, com base no tamanho da máquina virtual. Para obter mais informações, consulte [tamanhos de máquinas virtuais do Linux](sizes.md).

Para obter mais informações sobre como o Azure utiliza o disco temporário, consulte [compreender unidade temporária no Microsoft Virtual Machines do Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="cost-savings"></a>Reduções de custos
* [Custo de armazenamento](https://azure.microsoft.com/pricing/details/storage/)
* [Calculadora de armazenamento de custos](https://azure.microsoft.com/pricing/calculator/?service=storage)

## <a name="storage-limits"></a>Limites de armazenamento
* [Limites de serviço de armazenamento](../../azure-subscription-service-limits.md#storage-limits)
