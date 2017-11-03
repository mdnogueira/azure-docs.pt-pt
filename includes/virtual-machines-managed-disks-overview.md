# <a name="azure-managed-disks-overview"></a>Descrição geral de discos gerida do Azure

Discos gerida do Azure simplifica a gestão de discos para VMs IaaS do Azure ao gerir o [contas do storage](../articles/storage/common/storage-introduction.md) associadas aos discos VM. Só tem de especificar o tipo ([Premium](../articles/virtual-machines/windows/premium-storage.md) ou [padrão](../articles/virtual-machines/windows/standard-storage.md)) e o tamanho do disco é necessário e Azure cria e gere o disco para si.

## <a name="benefits-of-managed-disks"></a>Benefícios dos discos geridos

Vamos ver em algumas das vantagens que obtém ao utilizar discos geridos, começando neste vídeo no Channel 9, [melhor resiliência de VM do Azure com discos geridos](https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency).
<br/>
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Managed-Disks-for-Azure-Resiliency/player]

### <a name="simple-and-scalable-vm-deployment"></a>Implementação da VM Simple e dimensionável

Gerida armazenamento de identificadores de discos nos bastidores. Anteriormente, era necessário criar as contas do storage para armazenar os discos (ficheiros. VHD) para as suas VMs do Azure. Quando como aumentar verticalmente, era necessário Certifique-se de que criou mais contas do storage, pelo que não foi excedido o limite IOPS de armazenamento com qualquer um dos seus discos. Com os discos geridos processamento de armazenamento, já não são limitados por limites de conta de armazenamento (por exemplo, o 20.000 IOPS / conta). Também já não tem de copiar as imagens personalizadas (ficheiros. VHD) para várias contas de armazenamento. Pode geri-los numa localização central – uma conta do storage por região do Azure – e utilizá-los para criar centenas de VMs numa subscrição.

Discos geridos permite-lhe criar até 10 000 VM **discos** numa subscrição, que permitirá criar milhares de **VMs** numa única subscrição. Esta funcionalidade também aumenta a escalabilidade do [conjuntos de dimensionamento da Máquina Virtual (VMSS)](../articles/virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) , permitindo-lhe criar até um thousand VMs num VMSS utilizando uma imagem do Marketplace.

### <a name="better-reliability-for-availability-sets"></a>Melhor fiabilidade para conjuntos de disponibilidade

Discos geridos fornece melhor fiabilidade para conjuntos de disponibilidade, garantindo que os discos de [VMs num conjunto de disponibilidade](../articles/virtual-machines/windows/manage-availability.md#use-managed-disks-for-vms-in-an-availability-set) são suficientemente isoladas umas das outras para evitar pontos únicos de falha. Este é automaticamente colocando os discos em unidades de escala de armazenamento diferente (carimbos de data /). Se um carimbo falhar devido uma falha de hardware ou software, apenas as instâncias de VM com discos os carimbos de data / falharem. Por exemplo, vamos supor que tem uma aplicação em execução em cinco VMs e as VMs estão num conjunto de disponibilidade. Os discos para essas VMs não todas ser armazenadas no mesmo carimbo, por isso, se um carimbo ficar inativo, as outras instâncias da aplicação continuam a ser executado.

### <a name="highly-durable-and-available"></a>Elevada disponibilidade e durabilidade

Os Discos do Azure foram concebidos para garantir uma disponibilidade de 99,999%. REST mais fácil sabendo que tem três réplicas dos dados que permite elevada durabilidade. Se uma ou até mesmo duas réplicas apresentarem problemas, as restantes ajudam a garantir a persistência dos dados e alta tolerância contra falhas. Esta arquitetura tem ajudado o Azure a garantir de forma consistente uma durabilidade de nível empresarial para discos IaaS, com uma Taxa de Falhas Anual de ZERO% líder da indústria. 

### <a name="granular-access-control"></a>Controlo de acesso granulares

Pode utilizar [controlo de acesso em funções do Azure (RBAC)](../articles/active-directory/role-based-access-control-what-is.md) para atribuir permissões específicas para um disco gerido a um ou mais utilizadores. Gerido discos expõe uma variedade de operações, incluindo de leitura, escrita (criar/atualizar), eliminar e obter um [assinatura de acesso partilhado (SAS) URI](../articles/storage/common/storage-dotnet-shared-access-signature-part-1.md) para o disco. Pode conceder acesso apenas as operações de uma pessoa que precisa de realizar a tarefa. Por exemplo, se não pretender que uma pessoa para copiar um disco gerido para uma conta de armazenamento, pode optar por não conceda acesso para a ação de exportação para esse disco gerido. Da mesma forma, se não pretender que uma pessoa a utilizar um URI de SAS para copiar um disco gerido, pode optar por não conceder essa permissão para o disco gerido.

### <a name="azure-backup-service-support"></a>Suporte do serviço de cópia de segurança do Azure
Utilize o serviço de cópia de segurança do Azure com discos geridos para criar uma tarefa de cópia de segurança com políticas de retenção de cópias de segurança, fácil restauro de VM e cópias de segurança baseados no tempo. Discos geridos só suportam localmente redundante armazenamento (LRS) como a opção de replicação; Isto significa que mantém três cópias dos dados numa única região. Recuperação de desastres regionais, deve efetuar cópias de segurança aos discos VM numa região diferente através de [serviço cópia de segurança do Azure](../articles/backup/backup-introduction-to-azure-backup.md) e uma conta de armazenamento GRS como o Cofre de cópia de segurança. Atualmente, disco de dados do Backup do Azure suporta tamanhos até 1TB para cópia de segurança. Saiba mais sobre no [serviço utilizando a cópia de segurança do Azure para as VMs com discos geridos](../articles/backup/backup-introduction-to-azure-backup.md#using-managed-disk-vms-with-azure-backup).

## <a name="pricing-and-billing"></a>Preços e Faturação

Ao utilizar discos geridos, aplicam as seguintes considerações de faturação:
* Tipo de armazenamento

* Tamanho do Disco

* Número de transações

* Transferências de dados de saída

* Gerido instantâneos de discos (cópia de disco completo)

Vamos um olhar estes.

**Tipo de armazenamento:** discos geridos oferece 2 escalões de desempenho: [Premium](../articles/virtual-machines/windows/premium-storage.md) (baseadas em SSD) e [padrão](../articles/virtual-machines/windows/standard-storage.md) (baseado em HDD). A faturação de um disco gerido depende de qual o tipo de armazenamento que selecionou para o disco.


**Tamanho do disco**: faturação para discos geridos depende do tamanho do disco aprovisionado. Azure mapeia o tamanho de aprovisionamento (arredondar por excesso) para a opção de discos geridos mais próximo conforme especificado nas tabelas abaixo. Cada disco gerido mapeia para um dos tamanhos suportados aprovisionados e é faturado em conformidade. Por exemplo, se criar um disco gerido standard e especificar um tamanho de aprovisionamento de 200 GB, é-lhe cobrada de acordo com os preços do tipo disco S20.

Aqui estão os tamanhos de disco disponíveis para um disco gerido premium:

| **Premium gerido <br>tipo de disco** | **P4** | **P6** |**P10** | **P20** | **P30** | **P40** | **P50** | 
|------------------|---------|---------|---------|---------|----------------|----------------|----------------|  
| Tamanho do Disco        | 32 GB   | 64 GB   | 128 GB  | 512 GB  | 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 


Seguem-se disponível para um disco gerido standard os tamanhos de disco:

| **Standard gerido <br>tipo de disco** | **S4** | **S6** | **S10** | **S20** | **S30** | **S40** | **S50** |
|------------------|---------|---------|--------|--------|----------------|----------------|----------------| 
| Tamanho do Disco        | 32 GB   | 64 GB   | 128 GB | 512 GB | 1024 GB (1 TB) | 2048 GB (2 TB) | 4095 GB (4 TB) | 


**Número de transações**: É Faturado por número de transações que efetuar um disco gerido standard. Não há sem qualquer custo para transações para um disco gerido premium.

**Transferências de dados de saída**: [transferências de dados de saída](https://azure.microsoft.com/pricing/details/data-transfers/) (dados ficar fora de centros de dados do Azure) cobrança para utilização de largura de banda.

Para obter informações detalhadas sobre os preços para discos geridos, consulte [geridos discos preços](https://azure.microsoft.com/pricing/details/managed-disks).


## <a name="managed-disk-snapshots"></a>Disco gerido instantâneos

Um instantâneo gerido é uma cópia completa de só de leitura de um disco gerida que é armazenado como um disco gerido standard por predefinição. Com instantâneos, pode criar cópias de segurança aos discos geridos em qualquer ponto no tempo. Estes instantâneos existem independentes de disco de origem e podem ser utilizados para criar novos discos geridos. Estes são cobrados com base no tamanho utilizado. Por exemplo, se criar um instantâneo de um disco gerido com capacidade de aprovisionamento de 64 GB e o tamanho de dados utilizados real de 10 GB, instantâneo será faturado apenas para o tamanho de dados utilizados de 10 GB.  

[Instantâneos incrementais](../articles/virtual-machines/windows/incremental-snapshots.md) não são atualmente suportadas para discos geridos, mas será suportada no futuro.

Para obter mais informações sobre como criar instantâneos com discos geridos, consulte estes recursos:

* [Criar uma cópia de um VHD armazenado como Disco Gerido com os Instantâneos no Windows](../articles/virtual-machines/windows/snapshot-copy-managed-disk.md)
* [Criar uma cópia de um VHD armazenado como Disco Gerido com os Instantâneos no Linux](../articles/virtual-machines/linux/snapshot-copy-managed-disk.md)


## <a name="images"></a>Imagens

Discos geridos também suportam a criação de uma imagem personalizada gerida. Pode criar uma imagem do seu VHD personalizado em contas de armazenamento ou diretamente a partir de uma VM (sys prepped) generalizada. Isto captura numa única imagem gerido todos os discos associados uma VM, incluindo tanto o SO e discos de dados. Isto permite criar centenas de VMs com a sua imagem personalizada sem a necessidade de copiar ou gerir as contas de armazenamento.

Para obter informações sobre a criação de imagens, consulte os artigos seguintes:
* [Como capturar uma imagem gerida de uma VM generalizada no Azure](../articles/virtual-machines/windows/capture-image-resource.md)
* [Como generalize e capturar uma máquina virtual de Linux utilizando o 2.0 CLI do Azure](../articles/virtual-machines/linux/capture-image.md)

## <a name="images-versus-snapshots"></a>Imagens versus instantâneos

Muitas vezes, vir a palavra "imagem" utilizada com as VMs e agora ver "instantâneos" bem. É importante compreender a diferença entre estes. Com os discos geridos, pode demorar uma imagem de uma VM generalizada que tenha sido anulada. Esta imagem incluirá todos os discos ligados à VM. Pode utilizar esta imagem para criar uma nova VM e incluirá todos os discos.

Um instantâneo é uma cópia de um disco no ponto no tempo que é executada. Aplica-se apenas a um disco. Se tiver uma VM que tem apenas um disco (SO), pode tirar um instantâneo ou uma imagem do mesmo e criar uma VM a partir do instantâneo ou a imagem.

E se uma VM tem cinco discos e estão repartidas? Poderá tirar um instantâneo de cada um dos discos, mas não existe nenhum deteção dentro da VM do estado dos discos – os instantâneos de conhecer apenas se um disco. Neste caso, os instantâneos seriam têm de ser coordenada entre si e que não é atualmente suportado.

## <a name="managed-disks-and-encryption"></a>Discos geridos e encriptação

Existem dois tipos de encriptação para discutir no contexto de discos geridos. A primeira é armazenamento serviço encriptação (SSE), que é efetuada pelo serviço de armazenamento. Segunda é Azure Disk Encryption, que pode ativar nos SO e discos de dados para as suas VMs.

### <a name="storage-service-encryption-sse"></a>Encriptação do serviço de armazenamento (SSE)

[Encriptação do serviço de armazenamento do Azure](../articles/storage/common/storage-service-encryption.md) fornece encriptação em rest e salvaguardar os seus dados para satisfazer os seus compromissos de segurança e conformidade organizacionais. SSE está ativada por predefinição para todos os discos geridos, instantâneos e imagens em todas as regiões onde discos geridos está disponível. A partir de 10 de Junho de 2017, todos os novos geridos instantâneos/discos/imagens e novos dados escritos discos geridos existentes são automaticamente encriptados-em-rest com chaves geridas pela Microsoft.  Visite o [página de FAQ de discos geridos](../articles/virtual-machines/windows/faq-for-disks.md#managed-disks-and-storage-service-encryption) para obter mais detalhes.


### <a name="azure-disk-encryption-ade"></a>Encriptação de disco do Azure (ADE)

Encriptação de disco do Azure permite-lhe encriptar os SO e discos de dados utilizados pela máquina Virtual IaaS. A lista inclui discos geridos. Para o Windows, as unidades estão encriptadas com tecnologia de encriptação do BitLocker de norma da indústria. Para Linux, os discos estão encriptados com a tecnologia de DM-Crypt. Isto é integrado com o Cofre de chaves do Azure para permitem controlar e gerir as chaves de encriptação de disco. Para obter mais informações, consulte [encriptação de disco do Azure para Windows e as VMs de IaaS Linux](../articles/security/azure-security-disk-encryption.md).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre discos geridos, consulte os artigos seguintes.

### <a name="get-started-with-managed-disks"></a>Introdução aos Discos Geridos

* [Criar uma VM com o Resource Manager e o PowerShell](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm.md)

* [Criar uma VM com Linux através da CLI do Azure 2.0](../articles/virtual-machines/linux/quick-create-cli.md)

* [Anexar um disco de dados geridos para uma VM do Windows com o PowerShell](../articles/virtual-machines/windows/attach-disk-ps.md)

* [Adicionar um disco gerido a uma VM do Linux](../articles/virtual-machines/linux/add-disk.md)

* [Gerido Scripts de exemplo do PowerShell de discos](https://github.com/Azure-Samples/managed-disks-powershell-getting-started)

* [Utilizar discos geridos na modelos Azure Resource Manager](../articles/virtual-machines/windows/using-managed-disks-template-deployments.md)

### <a name="compare-managed-disks-storage-options"></a>Comparar as opções de armazenamento de discos geridos

* [Armazenamento Premium e discos](../articles/virtual-machines/windows/premium-storage.md)

* [Armazenamento padrão e discos](../articles/virtual-machines/windows/standard-storage.md)

### <a name="operational-guidance"></a>Orientação operacional

* [Migrar do AWS e outras plataformas para discos geridos no Azure](../articles/virtual-machines/windows/on-prem-to-azure.md)

* [Converter as VMs do Azure para discos geridos no Azure](../articles/virtual-machines/windows/migrate-to-managed-disks.md)
