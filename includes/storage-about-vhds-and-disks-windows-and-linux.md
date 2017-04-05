
## <a name="about-vhds"></a>Sobre VHDs

Os VHDs utilizados no Azure são ficheiros .vhd armazenados como blobs de páginas numa conta de armazenamento standard ou premium no Azure. Para obter detalhes sobre blobs de páginas, consulte [Understanding block blobs and page blobs (Noções sobre blobs de blocos e blobs de páginas)](/rest/api/storageservices/fileservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs/). Para obter detalhes sobre o armazenamento premium, consulte [High-performance premium storage and Azure VMs (VMs do Azure e armazenamento premium de elevado desempenho)](../articles/storage/storage-premium-storage.md).

O Azure suporta o formato VHD de disco fixo. O formato fixo dispõe do disco lógico de forma linear no ficheiro, para que o deslocamento do disco X seja armazenado no deslocamento do blob X. Um rodapé pequeno no final do blob descreve as propriedades do VHD. Muitas vezes, o formato fixo ocupa espaço porque a maioria dos discos tem grandes intervalos não utilizados nos mesmos. No entanto, o Azure armazena ficheiros .vhd num formato disperso, para receber os benefícios dos discos fixos e dinâmicos ao mesmo tempo. Para obter mais detalhes, consulte [Getting started with virtual hard disks (Introdução aos discos rígidos virtuais)](https://technet.microsoft.com/library/dd979539.aspx).

Todos os ficheiros .vhd no Azure que pretende utilizar como uma origem para criar discos ou imagens são só de leitura. Quando cria um disco ou uma imagem, o Azure faz cópias dos ficheiros .vhd. Estas cópias podem ser só de leitura ou de leitura e escrita, dependendo de como utiliza o VHD.

Quando cria uma máquina virtual a partir de uma imagem, o Azure cria um disco para a máquina virtual que é uma cópia do ficheiro .vhd de origem. Para proteger contra a eliminação acidental, o Azure coloca uma concessão em qualquer ficheiro .vhd de origem que é utilizado para criar uma imagem, um disco do sistema operativo ou um disco de dados.

Antes de poder eliminar um ficheiro .vhd de origem, terá de remover a concessão, ao eliminar o disco ou a imagem. Para eliminar um ficheiro .vhd que esteja a ser utilizado por uma máquina virtual como um disco do sistema operativo, pode eliminar a máquina virtual, o disco do sistema operativo e o ficheiro .vhd de origem em simultâneo, ao eliminar a máquina virtual e todos os discos associados. No entanto, a eliminação de um ficheiro .vhd que é uma origem para um disco exige vários passos numa determinada ordem. Primeiro desanexe o disco da máquina virtual e, em seguida, elimine o disco e depois o ficheiro .vhd.

> [!WARNING]
> Se eliminar um ficheiro .vhd de origem do armazenamento ou eliminar a sua conta de armazenamento, a Microsoft não pode recuperar os dados por si.
> 

## <a name="types-of-disks"></a>Tipos de discos 

Existem dois escalões de desempenho para o armazenamento que pode escolher ao criar os discos - Armazenamento Standard e Premium. Além disso, existem dois tipos de discos - geridos e não geridos - que podem residir em qualquer escalão de desempenho.  

### <a name="standard-storage"></a>Armazenamento Standard 

O Armazenamento Standard está protegido por HDDs e fornece armazenamento económico, mantendo o desempenho. O Armazenamento Standard pode ser replicado localmente num datacenter, ou ser georredundante com datacenters primários e secundários. Para mais informações sobre a replicação de armazenamento, consulte [Azure Storage replication (Replicação de Armazenamento do Azure)](../articles/storage/storage-redundancy.md). 

Para mais informações sobre a utilização do Armazenamento Standard com discos da VM, consulte [Standard Storage and Disks (Armazenamento Standard e Discos)](../articles/storage/storage-standard-storage.md).

### <a name="premium-storage"></a>Armazenamento Premium 

O Armazenamento Premium é apoiado por SSDs e fornece suporte para discos de elevado desempenho e de baixa latência para VMs com cargas de trabalho E/S intensivas. Pode utilizar o Armazenamento Premium com VMs do Azure de série DSv2, GS ou FS. Para mais informações, consulte [Premium Storage (Armazenamento Premium)](../articles/storage/storage-premium-storage.md).

### <a name="unmanaged-disks"></a>Discos não geridos

Os discos não geridos são o tipo de discos tradicionais que foram utilizados por VMs. Com eles, pode criar a sua própria conta de armazenamento e especificar essa conta de armazenamento ao criar o disco. Tem de garantir que não inclui demasiados discos na mesma conta de armazenamento, porque poderá exceder os [destinos de escalabilidade](../articles/storage/storage-scalability-targets.md) da conta de armazenamento (20.000 IOPS, por exemplo), resultando na limitação das VMs. Com discos não geridos, deve maximizar a utilização de uma ou mais contas de armazenamento para obter o melhor desempenho das suas VMs.

### <a name="managed-disks"></a>Managed disks 

Os Managed Disks suportam a criação/gestão da conta de armazenamento em segundo plano para si, e asseguram que não tem de se preocupar com os limites de escalabilidade da conta de armazenamento. Basta especificar o tamanho do disco e o escalão de desempenho (Standard/Premium), e o Azure cria e gere o disco por si. Mesmo que adicione discos ou aumente e reduza verticalmente a VM, não terá de se preocupar se o armazenamento está a ser utilizado. 

Também pode gerir as imagens personalizadas numa conta de armazenamento por região do Azure e utilizá-las para criar centenas de VMs na mesma subscrição. Para mais informações sobre os Managed Disks, consulte [Managed Disks Overview (Descrição geral dos Managed Disks)](../articles/storage/storage-managed-disks-overview.md).

Recomendamos que utilize os Managed Disks do Azure para novas VMs e que converta os discos não geridos anteriores para discos geridos, para tirar partido das diversas funcionalidades disponíveis nos Managed Disks.

### <a name="disk-comparison"></a>Comparação de discos

A tabela seguinte apresenta uma comparação dos Premium vs Standard para os discos geridos e não geridos, para o ajudar a decidir o que utilizar.

|    | Disco Premium do Azure | Disco Standard do Azure |
|--- | ------------------ | ------------------- |
| Tipo de Disco | Unidades de Estado Sólido (SSD) | Unidades de Disco Rígido (HDD)  |
| Descrição geral  | Suporte de disco de baixa latência, alto desempenho baseado em SSD para VMs com cargas de trabalho intensivas em E/S ou ambiente de produção crítico de missão de alojamento | Suporte de disco económico baseado em HDD para cenários de VM de Programação/Teste |
| Cenário  | Cargas de trabalho confidenciais de produção e de desempenho | Programação/Teste, não crítico, <br>Acesso pouco frequente |
| Tamanho do Disco | P10: 128 GB<br>P20: 512 GB<br>P30: 1024 GB | Discos Não Geridos: 1 GB – 1 TB <br><br>Managed Disks:<br> S4: 32 GB <br>S6: 64 GB <br>S10: 128 GB <br>S20: 512 GB <br>S30: 1024 GB |
| Débito Máx por Disco | 200 MB/s | 60 MB/s |
| IOPs Máx por disco | 5000 IOPS | 500 IOPS |
