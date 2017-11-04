# <a name="cost-effective-standard-storage-and-unmanaged-and-managed-azure-vm-disks"></a>Padrão de armazenamento económico e discos de VM do Azure não geridos e geridos

Armazenamento padrão do Azure fornece suporte de disco fiável, de baixo custo para VMs executar cargas de trabalho insensível à latência. Também suporta blobs, tabelas, filas e os ficheiros. Com o armazenamento Standard, os dados são armazenados em unidades de disco rígido (HDDs). Ao trabalhar com as VMs, pode utilizar discos de armazenamento standard para cenários de desenvolvimento/teste e cargas de trabalho críticas inferior e discos de armazenamento premium para aplicações de produção fundamentais. Armazenamento Standard está disponível em todas as regiões do Azure. 

Este artigo foca-se na utilização de armazenamento standard para os discos de VM. Para obter mais informações sobre a utilização de armazenamento com blobs, tabelas, filas e ficheiros, consulte o [introdução ao Storage](../articles/storage/common/storage-introduction.md).

## <a name="disk-types"></a>Tipos de disco

Existem duas formas de criar os discos padrão para VMs do Azure:

**Não gerido discos**: Este é o método original onde irá gerir as contas de armazenamento utilizadas para armazenar os ficheiros VHD que correspondem aos discos VM. Ficheiros VHD são armazenados como blobs de páginas em contas de armazenamento. Discos não geridos podem ser ligados a nenhum tamanho de VM do Azure, incluindo as VMs que utilizam o armazenamento Premium, principalmente, tais como a série de série DSv2 e GS. As VMs do Azure suportam anexar vários discos padrão, permitindo até 256 TB de armazenamento por VM.

[**Discos do Azure gerida**](../articles/virtual-machines/windows/managed-disks-overview.md): esta funcionalidade gere as contas de armazenamento utilizadas para os discos da VM para si. Especifique o tipo (Premium ou Standard) e o tamanho do disco tem e o Azure cria e gere o disco para si. Não precisa de preocupar com a colocar os discos em várias contas de armazenamento para garantir que permanecem dentro dos limites de escalabilidade para as contas de armazenamento – Azure processa que por si.

Apesar de ambos os tipos de discos estão disponíveis, recomendamos que utilize discos geridos para tirar partido das respetivas funcionalidades várias.

Para começar a utilizar com o armazenamento padrão do Azure, visite [começar gratuitamente](https://azure.microsoft.com/pricing/free-trial/). 

Para obter informações sobre como criar uma VM com discos geridos, consulte um dos seguintes artigos.

* [Criar uma VM com o Resource Manager e o PowerShell](../articles/virtual-machines/windows/quick-create-powershell.md)
* [Criar uma VM com Linux através da CLI do Azure 2.0](../articles/virtual-machines/linux/quick-create-cli.md)

## <a name="standard-storage-features"></a>Funcionalidades de armazenamento Standard 

Vamos ver algumas das funcionalidades de armazenamento Standard. Para obter mais detalhes, consulte [introdução ao Storage do Azure](../articles/storage/common/storage-introduction.md).

**Armazenamento Standard**: armazenamento padrão do Azure suporta discos do Azure, Blobs do Azure, ficheiros do Azure, Azure tabelas e filas do Azure. Para utilizar os serviços de armazenamento Standard, comece com [criar uma conta de armazenamento do Azure](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account).

**Discos de armazenamento Standard:** discos de armazenamento Standard podem ser anexados a todas as VMs do Azure, incluindo VMs de tamanho da série utilizadas com o Premium Storage, tais como a série de série DSv2 e GS. Só é possível ligar um disco de armazenamento standard para uma VM. No entanto, poderá anexar um ou mais destes discos para uma VM, até a contagem de disco máximo definida para esse tamanho VM. Na secção seguinte no metas de desempenho e escalabilidade do Storage Standard, vamos descrever as especificações mais detalhadamente. 

**Blob de páginas padrão**: blobs de páginas padrão são utilizados para armazenar discos persistentes para VMs e também pode ser acedidos diretamente através de REST como outros tipos de Blobs do Azure. [Blobs de páginas](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs) são uma coleção de páginas de 512 bytes otimizado para operações de escrita e leitura aleatória. 

**Replicação de armazenamento:** na maior parte das regiões, dados numa conta do standard storage podem ser replicadas localmente ou georreplicação em múltiplos centros de dados. Os quatro tipos de replicação disponível são armazenamento localmente redundante (LRS), o armazenamento com redundância de zona (ZRS), o armazenamento Georredundante (GRS) e o armazenamento Georredundante com acesso de leitura (RA-GRS). Gerido discos no armazenamento Standard suportam atualmente armazenamento localmente redundante (LRS) apenas. Para obter mais informações, consulte [replicação do Storage](../articles/storage/common/storage-redundancy.md).

## <a name="scalability-and-performance-targets"></a>Metas de Escalabilidade e Desempenho

Nesta secção, iremos descrever os destinos de escalabilidade e desempenho que tem de ter em consideração ao utilizar o armazenamento standard.

### <a name="account-limits--does-not-apply-to-managed-disks"></a>Limites de contas – não é aplicável a discos geridos

| **Recurso** | **Limite Predefinido** |
|--------------|-------------------|
| TB por conta de armazenamento  | 500 TB |
| Entrada máximo<sup>1</sup> por conta de armazenamento (regiões EUA) | 10 Gbps, se estiver ativada GRS/ZRS, 20 Gbps para LRS |
| Saída máximo<sup>1</sup> por conta de armazenamento (regiões EUA) | 20 Gbps se estiver ativada RA-GRS/GRS/ZRS, 30 Gbps para LRS |
| Entrada máximo<sup>1</sup> por conta de armazenamento (Europeia e regiões Asian) | 5 Gbps, se estiver ativada GRS/ZRS, 10 Gbps para LRS |
| Saída máximo<sup>1</sup> por conta de armazenamento (Europeia e regiões Asian) | 10 Gbps, se estiver ativada RA-GRS/GRS/ZRS, 15 Gbps para LRS |
| Total de pedidos taxa (assumindo que o tamanho do objeto de 1 KB) por conta de armazenamento | Até 20.000 IOPS, as entidades por segundo ou mensagens por segundo |

<sup>1</sup> entrada refere-se a todos os dados (pedidos) que está a ser enviados para uma conta de armazenamento. Saída refere-se a todos os dados (respostas) a ser recebidos a partir de uma conta de armazenamento.

Para obter mais informações, consulte [metas de desempenho e escalabilidade do Storage do Azure](../articles/storage/common/storage-scalability-targets.md).

Se as necessidades da sua aplicação excedem os destinos de escalabilidade de uma única conta de armazenamento, crie a sua aplicação para utilizar várias contas de armazenamento e os dados de partição entre essas contas de armazenamento. Em alternativa, pode discos gerida do Azure e Azure gere a criação de partições e o posicionamento dos seus dados para si.

### <a name="standard-disks-limits"></a>Limites de discos padrão

Ao contrário dos discos Premium, as operações de entrada/saída por segundo (IOPS) e débito (largura de banda) de discos padrão não são aprovisionadas. O desempenho de discos padrão varia de acordo com o tamanho da VM para que o disco está ligado, não para o tamanho do disco. Foi esperada alcançar até ao limite de desempenho listado na tabela abaixo.

**Limites de discos padrão (geridos e não gerido)**

| **Camada de VM**            | **Escalão básico VM** | **Escalão Standard VM** |
|------------------------|-------------------|----------------------|
| Tamanho máximo do disco          | 4095 GB           | 4095 GB              |
| Máx. 8 KB IOPS por disco | 300 até         | Até 500            |
| Largura de banda máximo por disco | Até 60 MB/s     | Até 60 MB/s        |

Se a carga de trabalho necessita de suporte de disco de elevado desempenho, baixa latência, deve considerar a utilizar o Premium Storage. Para saber mais benefícios do Premium Storage, visite [elevado desempenho Premium Storage e discos da VM do Azure](../articles/virtual-machines/windows/premium-storage.md). 

## <a name="snapshots-and-copy-blob"></a>Instantâneos e BLOBs de cópia

Para o serviço de armazenamento, o ficheiro VHD é um blob de página. Pode criar instantâneos de blobs de páginas e copie-os para outra localização, como uma conta de armazenamento diferente.

### <a name="unmanaged-disks"></a>Discos não geridos

Pode criar [instantâneos incrementais](../articles/virtual-machines/windows/incremental-snapshots.md) para não geridos discos padrão da mesma forma que utilizar instantâneos com armazenamento Standard. Recomendamos que cria instantâneos e, em seguida, copie os instantâneos para uma conta de armazenamento standard georredundante se o disco de origem está a ser uma conta de armazenamento localmente redundante. Para obter mais informações, consulte [as opções de redundância do armazenamento do Azure](../articles/storage/common/storage-redundancy.md).

Se um disco está ligado a uma VM, determinadas operações de API não são permitidas nos discos. Por exemplo, não é possível efetuar um [copiar Blob](/rest/api/storageservices/Copy-Blob) operação nesse blob desde que o disco está ligado a uma VM. Em vez disso, criar um instantâneo desse blob pela primeira vez utilizando o [instantâneo Blob](/rest/api/storageservices/Snapshot-Blob) método API de REST e, em seguida, execute o [copiar Blob](/rest/api/storageservices/Copy-Blob) do instantâneo ao copiar o disco ligado. Em alternativa, pode desligar o disco e, em seguida, executar quaisquer operações necessárias.

Para manter georredundante cópias da sua instantâneos, pode copiar de uma conta de armazenamento localmente redundante instantâneos para uma conta de armazenamento standard georredundante utilizando o AzCopy ou BLOBs de cópia. Para obter mais informações, consulte [transferir dados com o utilitário de linha de comandos do AzCopy](../articles/storage/common/storage-use-azcopy.md) e [copiar Blob](/rest/api/storageservices/Copy-Blob).

Para obter informações detalhadas sobre efetuar as operações REST contra os blobs de páginas em contas do storage padrão, consulte [API de REST dos serviços de armazenamento do Azure](/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference).

### <a name="managed-disks"></a>Managed disks

Um instantâneo de um disco gerido é uma cópia só de leitura do disco gerida que é armazenado como um disco gerido standard. Instantâneos incrementais não são atualmente suportados para discos geridos, mas serão suportados no futuro.

Se um disco gerido está ligado a uma VM, determinadas operações de API não são permitidas nos discos. Por exemplo, não é possível gerar uma assinatura de acesso partilhado (SAS) para efetuar uma operação de cópia, enquanto o disco está ligado a uma VM. Em vez disso, crie primeiro um instantâneo do disco e, em seguida, efetuar a cópia do instantâneo. Em alternativa, pode desligar o disco e, em seguida, gerar uma assinatura de acesso partilhado (SAS) para efetuar a operação de cópia.

## <a name="pricing-and-billing"></a>Preços e Faturação

Ao utilizar o armazenamento Standard, aplicam as seguintes considerações de faturação:

* Tamanho de discos/dados de armazenamento Standard não gerido 
* Discos geridos padrão
* Instantâneos de armazenamento Standard
* Transferências de dados de saída
* Transações

**Não gerido tamanho de dados e o disco de armazenamento:** para discos não geridos e outros dados (blobs, tabelas, filas e ficheiros), são-lhe cobrados apenas para a quantidade de espaço está a utilizar. Por exemplo, se tiver uma VM cuja BLOBs de páginas é aprovisionado como 127 GB, mas a VM é realmente o utilizador só de utilização de 10 GB de espaço, é-lhe faturado de 10 GB de espaço. Suportamos até o armazenamento Standard 8191 GB e padrão não gerido discos 4095 GB. 

**Discos geridos pelo:** discos geridos são faturados no tamanho aprovisionado. Se o disco é aprovisionado como um disco de 10 GB e estiver a utilizar apenas 5 GB, são-lhe cobrados para o tamanho de aprovisionamento de 10 GB.

**Instantâneos**: os instantâneos de discos padrão são cobrados para a capacidade adicional utilizada pelos instantâneos. Para obter informações sobre instantâneos, consulte [criar um instantâneo de um Blob](/rest/api/storageservices/Creating-a-Snapshot-of-a-Blob).

**Transferências de dados de saída**: [transferências de dados de saída](https://azure.microsoft.com/pricing/details/data-transfers/) (dados ficar fora de centros de dados do Azure) cobrança para utilização de largura de banda.

**Transação**: Azure cobra 0.0036 $ 100 000 transações de armazenamento standard. As transações incluem as opções de leitura e escrita para armazenamento.

Para obter informações detalhadas sobre os preços de armazenamento Standard, máquinas virtuais e discos geridos, consulte:

* [Preços do Storage do Azure](https://azure.microsoft.com/pricing/details/storage/)
* [Máquinas virtuais de preços](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Discos geridos de preços](https://azure.microsoft.com/pricing/details/managed-disks)

## <a name="azure-backup-service-support"></a>Suporte do serviço de cópia de segurança do Azure 

Máquinas virtuais com discos não geridos pode ser feitas através de cópia de segurança do Azure. [Obter mais detalhes](../articles/backup/backup-azure-vms-first-look-arm.md).

Também pode utilizar o serviço de cópia de segurança do Azure com discos de gerido para criar uma tarefa de cópia de segurança com políticas de retenção de cópias de segurança, fácil restauro de VM e cópias de segurança baseados no tempo. Pode ler mais sobre esta em [serviço utilizando a cópia de segurança do Azure para as VMs com discos geridos](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

## <a name="next-steps"></a>Passos seguintes

* [Introdução ao Storage do Azure](../articles/storage/common/storage-introduction.md)

* [Criar uma conta de armazenamento](../articles/storage/common/storage-create-storage-account.md)

* [Descrição Geral do Managed Disks](../articles/virtual-machines/linux/managed-disks-overview.md)

* [Criar uma VM com o Resource Manager e o PowerShell](../articles/virtual-machines/windows/quick-create-powershell.md)

* [Criar uma VM com Linux através da CLI do Azure 2.0](../articles/virtual-machines/linux/quick-create-cli.md)
