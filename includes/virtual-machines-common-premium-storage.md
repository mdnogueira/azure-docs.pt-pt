# <a name="high-performance-premium-storage-and-managed-disks-for-vms"></a>Armazenamento Premium de elevado desempenho e discos geridos para VMs
Armazenamento Premium do Azure fornece suporte de disco de elevado desempenho, baixa latência para máquinas virtuais (VMs) com a entrada/saída (e/s)-as cargas de trabalho que consomem muita. Discos VM que utilizam o armazenamento Premium armazenam dados em unidades de estado sólido (SSDs). Para tirar partido da velocidade e o desempenho de discos de armazenamento premium, pode migrar discos VM existentes para o Premium Storage.

No Azure, pode anexar vários discos de armazenamento premium para uma VM. Utilizar vários discos fornece as suas aplicações até 256 TB de armazenamento por VM. Armazenamento Premium, as suas aplicações podem alcançar 80.000 operações de e/s por segundo (IOPS) por VM e um débito de disco de até 2000 megabytes por segundo (MB/s) por VM. As operações de leitura dão-lhe latências muito baixos.

Armazenamento Premium, o Azure oferece a capacidade de comparação de precisão e shift verdadeiramente pedir o seu trabalho as aplicações empresariais, como farms de Dynamics AX, o Dynamics CRM, o Exchange Server, o SAP Business Suite e o SharePoint para a nuvem. Pode executar cargas de trabalho de base de dados de desempenho intensivas em aplicações como o SQL Server, Oracle, MongoDB, MySQL e Redis, que necessitam de elevado desempenho consistente e a latência baixa.

> [!NOTE]
> Para obter o melhor desempenho para a sua aplicação, recomendamos que migrar qualquer disco da VM que requer o IOPS elevado para o Premium Storage. Se o disco não necessita de IOPS elevado, pode ajudar os custos de limite ao mantê-la no armazenamento do Azure standard. No armazenamento standard, os dados de disco da VM são armazenados em unidades de disco rígido (HDDs), em vez de em SSDs.
> 

O Azure disponibiliza duas formas de criar os discos de armazenamento premium para as VMs:

* **Discos não geridos**

    O método original é utilizar discos não geridos. Um disco não geridos, gerir as contas de armazenamento que utiliza para armazenar os ficheiros de disco rígido virtual (VHD) que correspondem aos seus discos VM. Ficheiros VHD são armazenados como blobs de páginas em contas do storage do Azure. 

* **Discos geridos**

    Quando escolhe [Azure geridos discos](../articles/virtual-machines/windows/managed-disks-overview.md), Azure gere as contas de armazenamento que utilizar para os discos VM. Especifique o tipo de disco (Premium ou Standard) e o tamanho do disco que precisa. Azure cria e gere o disco para si. Não tem preocupar colocar os discos em várias contas de armazenamento para se certificar de que permanecem dentro dos limites de escalabilidade para as contas do storage. Azure processa que por si.

Recomendamos que escolha discos geridos, para tirar partido das respetivas funcionalidades várias.

Para começar a utilizar com o Premium Storage, [criar a sua conta do Azure gratuita](https://azure.microsoft.com/pricing/free-trial/). 

Para obter informações sobre como migrar as suas VMs existentes para o Premium Storage, consulte [converter uma VM do Windows de discos não geridos para gerido discos](../articles/virtual-machines/windows/convert-unmanaged-to-managed-disks.md) ou [converter uma VM com Linux de discos não geridos para gerido discos](../articles/virtual-machines/linux/convert-unmanaged-to-managed-disks.md).

> [!NOTE]
> Armazenamento Premium está disponível na maior parte das regiões. Para obter a lista de regiões disponíveis, no [serviços do Azure por região](https://azure.microsoft.com/regions/#services), veja as regiões na qual suportado VMs de série de tamanho de suporte Premium (-série DS, série DSV2-série, série GS e VMs Fs-série) são suportados.
> 

## <a name="features"></a>Funcionalidades

Seguem-se algumas das funcionalidades do Premium Storage:

* **Discos de armazenamento Premium**

    Armazenamento Premium suporta discos VM que podem ser anexados a VMs de série de tamanho específicas. Armazenamento Premium suporta-série DS, série DSv2-série GS série, série Ls e série Fs VMs. Tem uma opção de sete tamanhos de discos: P4 (32GB), P6 (64GB), P10 (128GB), P20 (GB de 512), P30 (1024GB), P40 (GB de 2048), P50 (4095GB). P4 e tamanhos de disco P6 ainda só são suportados para discos geridos. Cada tamanho do disco tem as suas próprias especificações de desempenho. Dependendo dos requisitos de aplicação, poderá anexar um ou mais discos para a VM. Vamos descrever as especificações com maior detalhe em [metas de desempenho e escalabilidade do armazenamento Premium](#scalability-and-performance-targets).

* **Blobs de páginas Premium**

    Armazenamento Premium suporta blobs de páginas. Utilize blobs de páginas para armazenar discos persistentes, não geridos para as VMs do Premium Storage. Ao contrário do armazenamento do Azure standard, armazenamento Premium não suportam blobs de blocos, blobs, ficheiros, tabelas ou filas de acréscimo. Os blobs de páginas Premium suporta tamanhos de seis de P10 P50 e P60 (8191GiB). Blob de páginas P60 Premium não é suportado para ser anexados como discos VM. 

    Qualquer objeto colocado numa conta de armazenamento premium será um blob de página. Ajusta o blob de página para um dos tamanhos de aprovisionamento suportados. É por isso uma conta de armazenamento premium não se destina a ser utilizado para armazenar blobs muito pequena.

* **Conta de armazenamento Premium**

    Para começar a utilizar o Premium Storage, crie uma conta de armazenamento premium para os discos não geridos. No [portal do Azure](https://portal.azure.com)para criar uma conta de armazenamento premium, escolha o **Premium** camada de desempenho. Selecione o **armazenamento localmente redundante (LRS)** opção de replicação. Também pode criar uma conta de armazenamento premium, o tipo de definição **Premium_LRS** das seguintes localizações:
    * [API REST do Storage](https://docs.microsoft.com/rest/api/storageservices/Azure-Storage-Services-REST-API-Reference) (versão 2014-02-14 ou uma versão posterior)
    * [API de REST de gestão do serviço](http://msdn.microsoft.com/library/azure/ee460799.aspx) (versão 2014-10-01 ou uma versão posterior; para implementações clássicas do Azure)
    * [Azure API de REST do fornecedor de recursos de armazenamento](https://docs.microsoft.com/rest/api/storagerp) (para implementações do Azure Resource Manager)
    * [O Azure PowerShell](/powershell/azureps-cmdlets-docs.md) (versão 0.8.10 ou uma versão posterior)

    Para mais informações sobre limites de conta de armazenamento premium, consulte o artigo [metas de desempenho e escalabilidade do armazenamento Premium](#premium-storage-scalability-and-performance-targets).

* **Armazenamento localmente redundante Premium**

    Uma conta do premium storage suporta armazenamento apenas localmente redundante como a opção de replicação. Armazenamento localmente redundante mantém três cópias dos dados numa única região. Regional recuperação de desastres, tem de copiar os discos VM numa região diferente utilizando [cópia de segurança do Azure](../articles/backup/backup-introduction-to-azure-backup.md). Também tem de utilizar uma conta de armazenamento georredundante (GRS) que o Cofre de cópia de segurança. 

    Azure utiliza a conta de armazenamento como um contentor para os discos não geridos. Quando criar uma Azure-série DS, série DSv2-série GS série, ou Fs-série VM com discos não geridos e selecione uma conta de armazenamento premium, o sistema operativo e os discos de dados são armazenados na conta do storage.

## <a name="supported-vms"></a>VMs suportadas
Armazenamento Premium suporta-série DS, série DSv2-série GS série, série Ls e série Fs VMs. Pode utilizar discos de armazenamento standard e premium com estes tipos VM. Não é possível utilizar discos de armazenamento premium com a série VM que não é Premium compatível com o armazenamento.

Para obter informações sobre tipos VM e tamanhos do Azure para Windows, consulte [tamanhos de Windows VM](../articles/virtual-machines/windows/sizes.md). Para obter informações sobre os tamanhos no Azure e tipos VM Linux, consulte [tamanhos de VM com Linux](../articles/virtual-machines/linux/sizes.md).

Estas são algumas das funcionalidades da-série DS, série DSv2-série GS-série, série Ls e série Fs VMs:

* **Serviço em nuvem**

    Pode adicionar-série DS VMs num serviço em nuvem que tenha apenas as VMs de série DS. Não adicione-série DS VMs num serviço em nuvem existente com qualquer tipo que não sejam-série DS VMs. Pode migrar os seus VHDs existentes para um novo serviço em nuvem que é executado apenas as VMs de série DS. Se pretender utilizar o mesmo endereço IP virtual para o novo serviço em nuvem que aloja as VMs de série DS, utilize [endereços IP reservados](../articles/virtual-network/virtual-networks-instance-level-public-ip.md). Série GS VMs podem ser adicionadas a um serviço em nuvem existente que tenha apenas as VMs de série GS.

* **Disco do sistema operativo**

    Pode configurar a sua VM do Premium Storage para utilizar um premium ou um disco de sistema operativo padrão. Para uma experiência otimizada, recomendamos que utilize um disco de sistema operativo baseado no armazenamento Premium.

* **Discos de dados**

    Pode utilizar discos standard e premium da mesma VM do Premium Storage. Armazenamento Premium, pode aprovisionar uma VM e anexar vários discos de dados persistentes para a VM. Se for necessário, para aumentar a capacidade e o desempenho do volume, pode stripe em todos os discos.

    > [!NOTE]
    > Se lhe stripe discos de dados de armazenamento premium utilizando [espaços de armazenamento](http://technet.microsoft.com/library/hh831739.aspx), configurar os espaços de armazenamento com 1 coluna para cada disco que utiliza. Caso contrário, o desempenho global do volume repartido pode ser menor do que o esperado devido a distribuição desigual de tráfego nos discos. Por predefinição, no Gestor de servidores, pode configurar colunas para até 8 discos. Se tiver mais de 8 discos, utilize o PowerShell para criar o volume. Especifique o número de colunas manualmente. Caso contrário, a IU do Gestor de servidor continua a utilizar 8 colunas, mesmo se tiver mais discos. Por exemplo, se tiver 32 discos num conjunto único stripe, especifique 32 colunas. Para especificar o número de colunas no disco virtual utiliza o [New-VirtualDisk](http://technet.microsoft.com/library/hh848643.aspx) cmdlet do PowerShell, utilize o *NumberOfColumns* parâmetro. Para obter mais informações, consulte [descrição geral dos espaços de armazenamento](http://technet.microsoft.com/library/hh831739.aspx) e [perguntas mais frequentes sobre espaços de armazenamento](http://social.technet.microsoft.com/wiki/contents/articles/11382.storage-spaces-frequently-asked-questions-faq.aspx).
    >
    > 

* **Cache**

    As VMs da série de tamanho que suportam o Premium Storage tem uma capacidade de colocação em cache exclusiva para níveis elevados de débito e latência. A capacidade de colocação em cache excede o desempenho do disco de armazenamento premium subjacente. Pode definir o disco política nos discos de armazenamento premium para a colocação em cache **ReadOnly**, **ReadWrite**, ou **nenhum**. O disco de predefinição política de colocação em cache é **ReadOnly** para todos os discos de dados premium, e **ReadWrite** para discos de sistema operativo. Para um desempenho ideal para a sua aplicação, utilize a definição de cache correto. Por exemplo, para discos de dados de leitura pesado ou só de leitura, tais como ficheiros de dados do SQL Server, defina o disco de política para a colocação em cache **ReadOnly**. Pesado de escrita ou só de escrita para discos de dados, tais como ficheiros de registo do SQL Server, defina o disco de política para a colocação em cache **nenhum**. Para obter mais informações sobre como otimizar o design de Premium Storage, consulte [conceção para desempenho com o Premium Storage](../articles/virtual-machines/windows/premium-storage-performance.md).

* **Análise**

    Para analisar o desempenho da VM utilizando discos no armazenamento Premium, ative os diagnósticos VM no [portal do Azure](https://portal.azure.com). Para obter mais informações, consulte [monitorização VM do Azure com a extensão de diagnóstico do Azure](https://azure.microsoft.com/blog/2014/09/02/windows-azure-virtual-machine-monitoring-with-wad-extension/). 

    Para ver o desempenho de disco, como utilizar ferramentas com base no sistema operativo [Monitor de desempenho do Windows](https://technet.microsoft.com/library/cc749249.aspx) para VMs do Windows e o [iostat](http://linux.die.net/man/1/iostat) comando para VMs com Linux.

* **Desempenho e limites de dimensionamento VM**

    Cada tamanho da VM de armazenamento Premium-suportado tem limites de escala e as especificações de desempenho para o IOPS, largura de banda e o número de discos que podem ser anexados por VM. Quando utilizar discos de armazenamento premium com VMs, certifique-se de que existe espaço suficiente IOPS e largura de banda na VM para tráfego de disco da unidade.

    Por exemplo, uma VM STANDARD_DS1 tem uma largura de banda dedicada de 32 MB/s para o tráfego de disco de armazenamento premium. Um disco de armazenamento premium P10 pode fornecer uma largura de banda de 100 MB/s. Se um disco de armazenamento premium P10 está ligado a esta VM, se pode aceder apenas até 32 MB/s. Não poderá utilizar o máximo 100 MB/s que o disco P10 pode fornecer.

    Atualmente, a VM maior na série de DS é o Standard_DS15_v2. O Standard_DS15_v2 pode fornecer até 960 MB/s através de todos os discos. A VM maior na série de GS é o Standard_GS5. O Standard_GS5 pode fornecer até a 2.000 MB/s através de todos os discos.

    Tenha em atenção que estes limites para o tráfego de disco apenas. Estes limites não incluem acertos na cache e o tráfego de rede. Uma largura de banda separada está disponível para o tráfego de rede VM. Largura de banda para tráfego de rede é diferente da largura de banda dedicada utilizada pelos discos de armazenamento premium.

    Para obter as informações mais atualizadas sobre IOPS máximo e de débito (largura de banda) para as VMs de armazenamento Premium-suportados, consulte [tamanhos de Windows VM](../articles/virtual-machines/windows/sizes.md) ou [tamanhos de VM com Linux](../articles/virtual-machines/linux/sizes.md).

    Para obter mais informações sobre os discos de armazenamento premium e os respetivos limites IOPS e de débito, consulte a tabela na secção seguinte.

## <a name="scalability-and-performance-targets"></a>Metas de escalabilidade e desempenho
Nesta secção, iremos descrever os destinos de escalabilidade e desempenho a ter em consideração quando utilizar o armazenamento Premium.

Contas de armazenamento Premium têm os seguintes destinos de escalabilidade:

| Capacidade total de conta | Largura de banda total de uma conta de armazenamento localmente redundante |
| --- | --- | 
| Capacidade do disco: 35 TB <br>Capacidade de instantâneos: 10 TB | Cópia de segurança 50 gigabits por segundo para entrada<sup>1</sup> + saída<sup>2</sup> |

<sup>1</sup> todos os dados (pedidos) que são enviados para uma conta de armazenamento

<sup>2</sup> todos os dados (respostas) que são recebidos a partir de uma conta de armazenamento

Para obter mais informações, consulte [metas de desempenho e escalabilidade do Storage do Azure](../articles/storage/common/storage-scalability-targets.md).

Se estiver a utilizar contas de armazenamento premium para os discos não geridos e a aplicação excede os destinos de escalabilidade de uma única conta de armazenamento, pode querer migrar discos geridos. Se não pretender migrar discos geridos, crie a sua aplicação para utilizar várias contas de armazenamento. Em seguida, particionar os dados entre essas contas de armazenamento. Por exemplo, se pretender ligar 51 TB discos através de várias VMs, distribuídos-los por duas contas de armazenamento. 35 TB é o limite para uma conta de armazenamento premium único. Certifique-se de que uma conta de armazenamento premium único nunca tem mais de 35 TB de discos aprovisionados.

### <a name="premium-storage-disk-limits"></a>Limites de disco de armazenamento Premium
Quando aprovisionar um disco de armazenamento premium, o tamanho do disco determina o IOPS máximo e de débito (largura de banda). O Azure disponibiliza sete tipos de discos de armazenamento premium: P4 (geridos discos apenas), P6 (geridos discos apenas), P10, P20, P30, P40 e P50. Cada tipo de disco de armazenamento premium tem limites específicos para o IOPS e débito. Os limites para os tipos de disco são descritos na tabela seguinte:

| Tipo de discos Premium  | P4    | P6    | P10   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| Tamanho do disco           | 32 GB| 64 GB| 128 GB| 512 GB            | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOPs por disco       | 120   | 240   | 500   | 2300              | 5000              | 7500              | 7500              | 
| Débito por disco | 25 MB por segundo  | 50 MB por segundo  | 100 MB por segundo | 150 MB por segundo | 200 MB por segundo | 250 MB por segundo | 250 MB por segundo | 

> [!NOTE]
> Certifique-se a largura de banda suficiente não está disponível na VM para tráfego de disco da unidade, conforme descrito em [VMs de armazenamento Premium-suportado](#premium-storage-supported-vms). Caso contrário, o débito de disco e IOPS está restringida para reduzir os valores. Débito máximo e IOPS baseiam-se nos limites à VM, não os limites de disco descritos na tabela anterior.  
> 
> 

Eis alguns pontos importantes saber sobre metas de desempenho e escalabilidade do armazenamento Premium:

* **Capacidade de aprovisionamento e o desempenho**

    Quando aprovisiona um disco de armazenamento premium, ao contrário do armazenamento padrão, são garantida a capacidade, o IOPS e o débito desse disco. Por exemplo, se criar um disco de P50, Azure Aprovisiona capacidade de armazenamento 4,095 GB, o 7.500 IOPS e 250 MB/s débito para esse disco. A aplicação pode utilizar a totalidade ou parte da capacidade e desempenho.

* **Tamanho do disco**

    Azure mapeia o tamanho do disco (arredondar por excesso) para a mais próximo premium disco opção de armazenamento, conforme especificado na tabela na secção anterior. Por exemplo, um tamanho de disco de 100 GB é classificado como uma opção de P10. Pode efetuar até 500 IOPS, com até 100 débito de MB/s. Da mesma forma, um disco de tamanho que 400 GB é classificado como um P20. Pode efetuar até 2,300 IOPS, com débito do 150 MB/s.
    
    > [!NOTE]
    > Pode facilmente aumentar o tamanho dos discos existentes. Por exemplo, pode querer aumentar o tamanho de um disco de 30 GB, 128 GB ou até 1 TB. Em alternativa, pode querer sua P20 Converter disco num disco de P30 porque necessita de mais capacidade ou mais de IOPS e de débito. 
    > 
 
* **Tamanho de e/s**

    O tamanho de e/s é 256 KB. Se os dados que está a ser transferidos são inferior a 256 KB, considera-se 1 unidade de e/s. Tamanhos maiores de e/s são contados como várias e/s do tamanho de 256 KB. Por exemplo, e/s de 1,100 KB é contabilizado como 5 unidades de e/s.

* **Débito**

    O limite de débito inclui escritas para o disco e inclui as operações de leitura do disco que não são servidas da cache. Por exemplo, um disco de P10 tem 100 MB/s débito por disco. Alguns exemplos de débito válido para um disco de P10 são mostrados na tabela seguinte:

    | Débito máximo por disco P10 | Leituras de não-cache do disco | Escritas de não-cache em disco |
    | --- | --- | --- |
    | 100 MB/s | 100 MB/s | 0 |
    | 100 MB/s | 0 | 100 MB/s |
    | 100 MB/s | 60 MB/s | 40 MB/s |

* **Acertos na cache**

    Acertos na cache não estão limitados pelo IOPS ou débito do disco alocado. Por exemplo, quando utilizar um disco de dados com um **ReadOnly** definição de cache numa VM que é suportada pelo armazenamento Premium, leituras são servidas da cache não está sujeitas ao IOPS e débito caps do disco. Se a carga de trabalho de um disco for predominantemente leituras, poderá obter débito muito elevado. A cache está sujeita a IOPS separada e limites de débito no VM nível, com base no tamanho VM. VMs de série DS tem aproximadamente 4000 IOPS e 33 MB/s débito por núcleo para a cache de e/s de SSD local. VMs de série GS têm um limite de 5000 IOPS e 50 MB/s débito por núcleo para a cache de e/s de SSD local. 

## <a name="throttling"></a>Limitação
Limitação poderão ocorrer, se a aplicação IOPS ou débito excede os limites atribuídos para um disco de armazenamento premium. Também limitação poderá ocorrer se o tráfego total do disco em todos os discos da VM excede o limite de largura de banda disco disponível para a VM. Para evitar a limitação, recomendamos que limite o número de pedidos de e/s de disco pendentes. Utilize um limite baseado em destinos de escalabilidade e desempenho para o disco que ter aprovisionado e a largura de banda de disco disponível para a VM.  

A aplicação pode alcançar a menor latência quando foi concebido para evitar a limitação. No entanto, se o número de pedidos de e/s de disco pendentes é demasiado pequeno, a aplicação não é possível beneficiam de IOPS máximo e níveis de débito que estão disponíveis para o disco.

Os exemplos seguintes demonstram como calcular níveis de limitação. Todos os cálculos baseiam-se um tamanho de unidade de e/s de 256 KB.

### <a name="example-1"></a>Exemplo 1
A aplicação processou 495 e/s unidades de tamanho de 16 KB num segundo num disco P10. As unidades de e/s são contadas como 495 IOPS. Se tentar uma e/s de 2 MB no mesmo segundo, o total de unidades de e/s de é igual ao IOPS 495 + 8. Isto acontece porque e/s de 2 MB = unidades de 256 KB/KB 2.048 = 8 e/s, quando o tamanho da unidade de e/s é 256 KB. Dado que a soma de 495 + 8 excede o limite IOPS 500 para o disco, limitação ocorre.

### <a name="example-2"></a>Exemplo 2
A aplicação tem de processar 400 e/s unidades de tamanho de 256 KB num disco P10. A largura de banda total consumido (400 &#215; 256) / 1.024 KB = 100 MB/s. Um disco de P10 tem um limite de débito de 100 MB/s. Se a aplicação tenta efetuar mais operações de e/s desse segundo, está limitada porque excede o limite atribuído.

### <a name="example-3"></a>Exemplo 3
Tiver uma VM DS4 com dois P30 discos anexados. Cada disco P30 é capaz de débito do 200 MB/s. No entanto, uma VM DS4 tem uma capacidade de largura de banda total do disco de 256 MB/s. Não é possível unidade ambos os discos ligados para o débito máximo nesta DS4 VM ao mesmo tempo. Para resolver este problema, pode suportar o tráfego de 200 MB/s num disco e 56 MB/s no outro disco. Se a soma do tráfego disco ficar através de 256 MB/s, o tráfego do disco é limitado.

> [!NOTE]
> Se o tráfego de disco consiste principalmente tamanhos de e/s pequenos, a aplicação provável será atingiu o limite IOPS antes do limite de débito. No entanto, se o tráfego de disco consiste principalmente tamanhos de e/s grande, a aplicação provável será atingiu o limite de débito em primeiro lugar, em vez do limite IOPS. Pode maximizar IOPS da sua aplicação e a capacidade de débito utilizando tamanhos ideal de e/s. Além disso, pode limitar o número de pedidos de e/s para um disco pendentes.
> 

Para saber mais sobre a conceção de elevado desempenho ao utilizar o Premium Storage, consulte [conceção para desempenho com o Premium Storage](../articles/virtual-machines/windows/premium-storage-performance.md).

## <a name="snapshots-and-copy-blob"></a>Instantâneos e BLOBs de cópia

Para o serviço de armazenamento, o ficheiro VHD é um blob de página. Pode criar instantâneos de blobs de páginas e copie-os para outra localização, tal como para uma conta de armazenamento diferente.

### <a name="unmanaged-disks"></a>Discos não geridos

Criar [instantâneos incrementais](../articles/virtual-machines/linux/incremental-snapshots.md) para não gerido premium discos da mesma forma que utilizar instantâneos com armazenamento standard. Armazenamento Premium suporta armazenamento apenas localmente redundante como a opção de replicação. Recomendamos que crie instantâneos e, em seguida, copie os instantâneos para uma conta de armazenamento georredundante de padrão. Para obter mais informações, consulte [opções de redundância do armazenamento do Azure](../articles/storage/common/storage-redundancy.md).

Se um disco está ligado a uma VM, algumas operações de API no disco não são permitidas. Por exemplo, não é possível efetuar um [copiar Blob](/rest/api/storageservices/Copy-Blob) operação nesse blob se o disco está ligado a uma VM. Em vez disso, criar um instantâneo desse blob pela primeira vez utilizando o [instantâneo Blob](/rest/api/storageservices/Snapshot-Blob) REST API. Em seguida, execute o [copiar Blob](/rest/api/storageservices/Copy-Blob) do instantâneo ao copiar o disco ligado. Em alternativa, pode desligar o disco e, em seguida, executar quaisquer operações necessárias.

Os seguintes limites são aplicáveis a instantâneos de blob de armazenamento premium:

| Limite de armazenamento Premium | Valor |
| --- | --- |
| Número máximo de instantâneos por blob | 100 |
| Capacidade da conta de armazenamento de instantâneos<br>(Inclui dados apenas com instantâneos. Não incluem dados no base blob). | 10 TB |
| Tempo mínimo entre instantâneos consecutivos | 10 minutos |

Para manter georredundante cópias da sua instantâneos, pode copiar de uma conta de armazenamento premium instantâneos para uma conta de armazenamento standard georredundante utilizando o AzCopy ou BLOBs de cópia. Para obter mais informações, consulte [transferir dados com o utilitário de linha de comandos do AzCopy](../articles/storage/common/storage-use-azcopy.md) e [copiar Blob](/rest/api/storageservices/Copy-Blob).

Para obter informações detalhadas sobre como efetuar as operações REST contra os blobs de páginas numa conta de armazenamento premium, consulte [Blob operações de serviço com o Premium Storage do Azure](http://go.microsoft.com/fwlink/?LinkId=521969).

### <a name="managed-disks"></a>Managed disks

Um instantâneo de um disco gerido é uma cópia só de leitura do disco gerido. O instantâneo é armazenado como um disco gerido standard. Atualmente, [instantâneos incrementais](../articles/virtual-machines/windows/incremental-snapshots.md) não são suportadas para discos geridos. Para saber como criar um instantâneo de um disco gerido, consulte o artigo [criar uma cópia de um VHD armazenado como um Azure gerido disco utilizando gerido instantâneos no Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md) ou [criar uma cópia de um VHD armazenado como um Azure gerido disco utilizando geridos instantâneos no Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md).

Se um disco gerido está ligado a uma VM, algumas operações de API no disco não são permitidas. Por exemplo, não é possível gerar uma assinatura de acesso partilhado (SAS) para efetuar uma operação de cópia, enquanto o disco está ligado a uma VM. Em vez disso, crie primeiro um instantâneo do disco e, em seguida, efetuar a cópia do instantâneo. Em alternativa, pode desligar o disco e, em seguida, gerar uma SAS para efetuar a operação de cópia.


## <a name="premium-storage-for-linux-vms"></a>Armazenamento Premium para VMs com Linux
Pode utilizar as seguintes informações para o ajudar a configurar as suas VMs com Linux no armazenamento Premium:

Para atingir os objetivos de escalabilidade no armazenamento Premium, para todos os discos de armazenamento premium com a cache definido como **ReadOnly** ou **nenhum**, tem de desativar "barreiras as eficazes" ao montar o sistema de ficheiros. Não precisa barreiras as eficazes neste cenário, uma vez que as operações de escrita para discos de armazenamento premium são duráveis para estas definições de cache. Quando o pedido de escrita for concluída com êxito, dados foi escritos para o arquivo persistente. Para desativar "barreiras as eficazes", utilize um dos seguintes métodos. Escolha a para o seu sistema de ficheiros:
  
* Para **reiserFS**para desativar barreiras as eficazes, utilize o `barrier=none` montar opção. (Para ativar barreiras as eficazes, utilize `barrier=flush`.)
* Para **ext3/ext4**para desativar barreiras as eficazes, utilize o `barrier=0` montar opção. (Para ativar barreiras as eficazes, utilize `barrier=1`.)
* Para **XFS**para desativar barreiras as eficazes, utilize o `nobarrier` montar opção. (Para ativar barreiras as eficazes, utilize `barrier`.)
* Para o premium storage discos com a cache definido como **ReadWrite**, ativar barreiras as eficazes para uma durabilidade de escrita.
* Para as etiquetas de volume manter depois de reiniciar a VM, tem de atualizar /etc/fstab com as referências de identificador exclusivo universalmente (UUID) para os discos. Para obter mais informações, consulte [adicionar um disco gerido a uma VM com Linux](../articles/virtual-machines/linux/add-disk.md).

As seguintes distribuições de Linux tem foi validadas para Premium Storage do Azure. Para um melhor desempenho e a estabilidade com o Premium Storage, recomendamos que Atualize as suas VMs destas versões, no mínimo (ou para uma versão posterior). Algumas das versões requerem o mais recente Linux Integration Services (LIS), v 4.0, para o Azure. Para transferir e instalar uma distribuição, siga a ligação listada na seguinte tabela. Iremos adicionar imagens à lista, à medida que concluir a validação. Tenha em atenção que o nosso validações mostram que o desempenho varia de cada imagem. Desempenho depende características de carga de trabalho e as definições de imagem. Imagens de diferentes são otimizadas para diferentes tipos de cargas de trabalho.

| Distribuição | Versão | Kernel suportado | Detalhes |
| --- | --- | --- | --- |
| Ubuntu | 12.04 | 3.2.0-75.110+ | Ubuntu-12_04_5-LTS-amd64-Server-20150119-en-us-30GB |
| Ubuntu | 14.04 | 3.13.0-44.73+ | Ubuntu-14_04_1-LTS-amd64-Server-20150123-en-us-30GB |
| Debian | 7, 8. x | 3.16.7-ckt4-1+ | &nbsp; |
| SUSE | SLES 12| 3.12.36-38.1+| SUSE-sles-12-prioridade-v20150213 <br> SUSE-sles-12-v20150213 |
| SUSE | SLES 11 SP4 | 3.0.101-0.63.1+ | &nbsp; |
| CoreOS | 584.0.0+| 3.18.4+ | CoreOS 584.0.0 |
| CentOS | 6.5, 6.6, 6.7, 7.0 | &nbsp; | [LIS4 necessário](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Consulte a nota na secção seguinte* |
| CentOS | 7.1+ | 3.10.0-229.1.2.el7+ | [LIS4 recomendado](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) <br> *Consulte a nota na secção seguinte* |
| Red Hat Enterprise Linux (RHEL) | 6.8+, 7.2+ | &nbsp; | &nbsp; |
| Oracle | 6.0+, 7.2+ | &nbsp; | UEK4 ou RHCK |
| Oracle | 7.0-7.1 | &nbsp; | UEK4 ou RHCK com[LIS 4.1 +](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |
| Oracle | 6.4-6.7 | &nbsp; | UEK4 ou RHCK com[LIS 4.1 +](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409) |


### <a name="lis-drivers-for-openlogic-centos"></a>Controladores LIS para OpenLogic CentOS

Se estiver a executar OpenLogic CentOS VMs, execute o seguinte comando para instalar os controladores mais recentes:

```
sudo rpm -e hypervkvpd  ## (Might return an error if not installed. That's OK.)
sudo yum install microsoft-hyper-v
```

Para ativar os novos controladores, reinicie o computador.

## <a name="pricing-and-billing"></a>Preços e faturação

Ao utilizar o armazenamento Premium, aplicam as seguintes considerações de faturação:

* **Tamanho do disco e BLOBs de armazenamento Premium**

    Faturação de um disco de armazenamento premium ou o blob depende o tamanho do disco ou blob aprovisionado. Azure mapeia o tamanho de aprovisionamento (arredondar por excesso) para a opção de disco de armazenamento premium mais próximo. Para obter detalhes, consulte a tabela em [metas de desempenho e escalabilidade do armazenamento Premium](#premium-storage-scalability-and-performance-targets). Cada disco mapeia para um tamanho de disco de aprovisionamento suportados e é faturado em conformidade. A faturação para qualquer disco aprovisionado é proporcional numa base horária, utilizando o preço mensal para a oferta de armazenamento Premium. Por exemplo, se aprovisionado um disco de P10 e eliminado depois de 20 horas, é-lhe faturado para a oferta P10 proporcional 20 horas. Isto é, independentemente da quantidade de dados reais escritos para o disco ou de IOPS e débito utilizado.

* **Instantâneos de discos não gerido Premium**

    Instantâneos em discos premium não gerido são cobrados para a capacidade adicional utilizada pelos instantâneos. Para obter mais informações sobre instantâneos, consulte [criar um instantâneo de um blob](/rest/api/storageservices/Snapshot-Blob).

* **Instantâneos de discos Premium gerido**

    Um instantâneo de um disco gerido é uma cópia só de leitura do disco. O disco é armazenado como um disco gerido standard. Um instantâneo custos da mesma, como um padrão gerido disco. Por exemplo, se tirar um instantâneo de um disco gerido de 128 GB premium, o custo do instantâneo é equivalente a um disco gerido standard da 128 GB.  

* **Transferências de dados de saída**

    [Transferências de dados de saída](https://azure.microsoft.com/pricing/details/data-transfers/) (dados ficar fora de centros de dados do Azure) cobrança para utilização de largura de banda.

Para obter informações detalhadas sobre os preços para o Premium Storage, suportada de armazenamento Premium VMs e discos geridos, consulte estes artigos:

* [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/)
* [Máquinas virtuais de preços](https://azure.microsoft.com/pricing/details/virtual-machines/)
* [Discos geridos de preços](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="azure-backup-support"></a>Suporte de cópia de segurança do Azure 

Regional recuperação de desastres, tem de copiar os discos VM numa região diferente utilizando [cópia de segurança do Azure](../articles/backup/backup-introduction-to-azure-backup.md) e uma conta de armazenamento GRS como um cofre de cópia de segurança.

Para criar uma tarefa de cópia de segurança com cópias de segurança baseados no tempo, fácil restauro de VM e políticas de retenção de cópias de segurança, utilizam cópias de segurança do Azure. Pode utilizar a cópia de segurança ambos os com discos não geridos e geridos. Para obter mais informações, consulte [cópia de segurança do Azure para as VMs com discos não geridos](../articles/backup/backup-azure-vms-first-look-arm.md) e [cópia de segurança do Azure para as VMs com discos geridos](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup). 

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações sobre o Premium Storage, consulte os artigos seguintes.

### <a name="design-and-implement-with-premium-storage"></a>Conceber e implementar com o Premium Storage
* [Estruturar para desempenho com o Premium Storage](../articles/virtual-machines/windows/premium-storage-performance.md)
* [Operações de armazenamento de Blobs com o Premium Storage](http://go.microsoft.com/fwlink/?LinkId=521969)

### <a name="operational-guidance"></a>Orientação operacional
* [Migrar para o armazenamento do Azure Premium](../articles/storage/common/storage-migration-to-premium-storage.md)

### <a name="blog-posts"></a>Publicações no blogue
* [Armazenamento Premium do Azure geralmente disponível](https://azure.microsoft.com/blog/azure-premium-storage-now-generally-available-2/)
* [Anunciar a série GS: suporte de adicionar o Premium Storage para as VMs maiores na nuvem pública](https://azure.microsoft.com/blog/azure-has-the-most-powerful-vms-in-the-public-cloud/)
