
# <a name="backup-and-disaster-recovery-for-azure-iaas-disks"></a>Cópia de segurança e recuperação após desastre para discos IaaS do Azure

Este artigo explica como planear a cópia de segurança e recuperação após desastre (DR) de máquinas virtuais (VMs) IaaS e os discos no Azure. Este documento abrange geridos e discos.

Em primeiro lugar, iremos abrange as capacidades de tolerância a falhas incorporadas na plataforma do Azure que ajuda a proteger contra falhas locais. Em seguida, vamos discutir os cenários de desastre totalmente não abrangidos pelas capacidades incorporadas. Este é o tópico principal abordado neste documento. Vamos mostrar também vários exemplos de cenários de carga de trabalho onde podem aplicar a cópia de segurança diferentes e considerações de DR. Vamos, em seguida, reveja possíveis soluções para os discos de DR de IaaS. 

## <a name="introduction"></a>Introdução

A plataforma do Azure utiliza vários métodos para redundância e tolerância a falhas para ajudar a proteger os clientes de falhas de hardware localizada. Falhas locais podem incluir problemas com um computador de servidor de armazenamento do Azure que armazena a parte dos dados para um disco virtual ou falhas de um SSD ou HDD nesse servidor. Estas falhas de componente de hardware isolado podem acontecer durante as operações normais. 

A plataforma do Azure foi concebida para ser resiliente para estas falhas. Principais perante desastres podem resultar em falhas ou inaccessibility de vários servidores de armazenamento ou mesmo todo um centro de dados. Embora as VMs e os discos estão normalmente protegidos de falhas localizadas, são necessários para proteger a sua carga de trabalho de falhas de catastrófica de toda a região, tal como um desastre grave, que podem afetar o VM e os discos passos adicionais.

Para além de conhecimento do possibilidade de falhas da plataforma, podem ocorrer problemas com uma aplicação de cliente ou dados. Por exemplo, uma nova versão da aplicação poderá tornar inadvertidamente uma alteração aos dados que faz com que a interrupção. Nesse caso, pode querer reverter a aplicação e os dados para uma versão anterior que contém o último bom estado conhecido. É necessário manter cópias de segurança regulares.

Regional recuperação de desastres, tem de copiar os discos da VM do IaaS noutra região. 

Antes de Vamos observar cópia de segurança e as opções de DR, vamos recap alguns métodos disponíveis para processamento de falhas localizadas.

### <a name="azure-iaas-resiliency"></a>Resiliência de IaaS do Azure

*Resiliência* refere-se para a tolerância de falhas normais que ocorrem nos componentes de hardware. Resiliência é a capacidade de recuperar de falhas e continuar a funcionar. Não é sobre evitando falhas, mas a responder às falhas de uma forma que evita o período de indisponibilidade ou perda de dados. É o objetivo de resiliência para devolver a aplicação para um Estado ao funcionamento integral de seguir uma falha. Máquinas virtuais do Azure e os discos foram concebidos para serem resilientes a falhas de hardware comuns. Vamos ver como a plataforma do IaaS do Azure fornece este resiliência.

Uma máquina virtual consiste principalmente de duas partes: um servidor de computação e os discos persistentes. Ambos afetam a tolerância a falhas de uma máquina virtual.

Se o servidor de anfitrião de computação do Azure que aloja a VM sofre uma falha de hardware, que é raro, Azure foi concebido para restauro automático da VM noutro servidor. Se isto acontecer, os reinícios de computador, e a VM volta a funcionar após algum tempo. Azure Deteta essas falhas de hardware e executa recuperações para ajudar a garantir que o cliente VM encontra-se disponíveis assim que possível automaticamente.

Relativas aos discos IaaS, características de durabilidade dos dados é essencial para uma plataforma de armazenamento persistente. Clientes do Azure tem aplicações de negócio importantes em execução no IaaS e dependem a persistência dos dados. Proteção de estruturas do Azure para estes discos IaaS, com três cópias redundantes dos dados que esteja armazenadas localmente. Estas cópias de fornecem para uma durabilidade elevada contra falhas locais. Se um dos componentes de hardware que contém o disco falhar, a VM não é afetada, porque existem duas cópias adicionais para suportar pedidos de disco. -Funciona bem, mesmo se falharem dois componentes de hardware diferentes que suportam um disco em simultâneo (que é muito raros). 

Para garantir que mantém três réplicas sempre, o Storage do Azure cria automaticamente uma nova cópia dos dados em segundo plano, se uma das três copia fica indisponível. Por conseguinte, não deve ser necessário utilizar RAID com discos do Azure para tolerância a falhas. Um simple RAID 0 configuração deve ser suficiente para striping discos, se necessário, para criar volumes maiores.

Devido a esta arquitetura, Azure consistentemente tem ser entregue a nível empresarial durabilidade para IaaS discos, com um líder da indústria zero percentagem [taxa de falhas annualized](https://en.wikipedia.org/wiki/Annualized_failure_rate).

Falhas de hardware localizada na computação anfitrião ou na plataforma de armazenamento, por vezes, pode resultar de indisponibilidade temporária da VM que é abrangida pelo [SLA do Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) para disponibilidade VM. Azure também fornece um SLA de líder da indústria para único instâncias VM que utilizam discos Premium Storage do Azure.

Para salvaguardar as cargas de trabalho de aplicação do período de indisponibilidade devido a indisponibilidade temporária de uma VM ou disco, os clientes podem utilizar [conjuntos de disponibilidade](../articles/virtual-machines/windows/manage-availability.md). Dois ou mais máquinas virtuais num conjunto de disponibilidade fornecer redundância para a aplicação. O Azure, em seguida, cria nessas VMs e discos em domínios de falhas separados com vários componentes de alimentação, rede e servidor. 

Devido a estes domínios de falhas separado, falhas de hardware localizada normalmente não afetam várias VMs no conjunto ao mesmo tempo. Ter domínios de falhas separado fornece elevada disponibilidade para a sua aplicação. Tem considera-uma boa prática para utilizar conjuntos de disponibilidade quando for necessária uma elevada disponibilidade. A secção seguinte abrange o aspeto de recuperação após desastre.

### <a name="backup-and-disaster-recovery"></a>Cópia de segurança e recuperação após desastre

Recuperação após desastre é a capacidade de recuperar a partir de raros, mas principais, incidentes. Isto inclui a falhas não transitório, dimensionamento wide, tais como a interrupção do serviço que afeta uma região de toda. Recuperação após desastre inclui a cópia de segurança de dados e o arquivamento e pode incluir uma intervenção manual, como restaurar uma base de dados a partir de uma cópia de segurança.

Proteção incorporada da plataforma do Azure contra falhas localizadas poderá não totalmente proteger VMs/discos caso de desastre grave provoca falhas em grande escala. Isto inclui catastrófica eventos, tal como se um datacenter é acessos por um furacão, terramoto, fire, ou se houver um falhas de unidade de hardware em grande escala. Além disso, podem ocorrer falhas devido a problemas de dados ou da aplicação.

Para ajudar a proteger as cargas de trabalho IaaS contra falhas, deve planear a redundância e ter cópias de segurança para ativar a recuperação. Recuperação de desastres, deve copiar em segurança numa localização geográfica diferente na direção oposta ao site primário. Isto ajuda a garantir a que sua cópia de segurança não é afetada pelo mesmo evento que originalmente afetados VM ou discos. Para obter mais informações, consulte [recuperação após desastre para aplicações do Azure](/azure/architecture/resiliency/disaster-recovery-azure-applications).

As considerações de DR podem incluir os seguintes aspetos:

- Elevada disponibilidade: A capacidade da aplicação continuar em execução em bom estado de funcionamento, sem períodos de indisponibilidade significativo. Por *bom estado de funcionamento*, significa que a aplicação está a responder e que os utilizadores podem ligar à aplicação e interagir com ele. Determinadas aplicações fundamentais e bases de dados poderão ser necessários para estar sempre disponíveis, mesmo quando ocorrem falhas na plataforma. Para estas cargas de trabalho, poderá ter de planear a redundância para a aplicação, bem como os dados.

- Durabilidade de dados: em alguns casos, a principal consideração é garantir que os dados são preservados se acontecer de um desastre. Por conseguinte, poderá ter uma cópia de segurança dos seus dados num site diferente. Para essas cargas de trabalho, não poderá ser necessário redundância completa para a aplicação, mas apenas uma cópia de segurança normal dos discos.

## <a name="backup-and-dr-scenarios"></a>Cenários de cópia de segurança e de DR

Vamos ver alguns exemplos típicos de cenários de carga de trabalho de aplicações e as considerações de planeamento para recuperação após desastre.

### <a name="scenario-1-major-database-solutions"></a>Cenário 1: Soluções de base de dados principais

Considere um servidor de base de dados de produção, como o SQL Server ou Oracle, que pode suportar a elevada disponibilidade. Os utilizadores e aplicações de produção crítico dependem desta base de dados. O plano de recuperação de desastres para este sistema poderá ter de suportar os requisitos seguintes:

- Os dados tem de ser protegido e recuperáveis.
- O servidor tem de estar disponível para utilização.

O plano de recuperação de desastre pode necessitar de manter uma réplica da base de dados numa região diferente como uma cópia de segurança. Consoante os requisitos para disponibilidade de servidor e recuperação de dados, a solução poderá variar entre um site de réplica ativa-ativa ou ativo-passivo para cópias de segurança offline periódicas dos dados. Bases de dados relacionais, como SQL Server e Oracle, fornecem várias opções para replicação. Para o SQL Server, utilize [grupos de Disponibilidade AlwaysOn do SQL Server](https://msdn.microsoft.com/library/hh510230.aspx) para elevada disponibilidade.

Bases de dados NoSQL, como o MongoDB, também suportam [réplicas](https://docs.mongodb.com/manual/replication/) para redundância. São utilizadas as réplicas de elevada disponibilidade.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Cenário 2: Um cluster de VMs redundantes

Considere uma carga de trabalho processada por um cluster de VMs que fornecer redundância e balanceamento de carga. Um exemplo é um cluster de Cassandra implementado numa região. Este tipo de arquitetura já fornece um elevado nível de redundância nessa região. No entanto, para proteger a carga de trabalho de uma falha de nível de regionais, deve considerar a propagando-se o cluster em duas regiões ou efetuar cópias de segurança periódicas noutra região.

### <a name="scenario-3-iaas-application-workload"></a>Cenário 3: Carga de trabalho de aplicação de IaaS

Vamos ver a carga de trabalho de aplicação de IaaS. Por exemplo, pode ser uma carga de trabalho de produção normal em execução numa VM do Azure. Poderá ser um servidor web ou um servidor de ficheiro que contém o conteúdo e a outros recursos de um site. Também pode ser uma aplicação de negócio personalizada em execução numa VM que armazenados os respetivos dados, a recursos e o estado da aplicação nos discos de VM. Neste caso, é importante certificar-se a que fazer cópias de segurança regulares. Frequência de cópia de segurança deve ser baseada na natureza da carga de trabalho VM. Por exemplo, se a aplicação é executada diariamente e modifica dados, em seguida, a cópia de segurança deverão ser executada a cada hora.

Outro exemplo é um servidor de relatórios que obtém dados a partir de outras origens e gera relatórios agregados. A perda desta VM ou discos pode levar a perda dos relatórios. No entanto, poderá ser possível voltar a executar o processo de criação de relatórios e voltar a gerar a saída. Nesse caso, realmente não tem uma perda de dados, mesmo que o servidor de relatórios é acessos com um desastre. Como resultado, pode ter um nível mais elevado de tolerância de perda de parte dos dados no servidor de relatórios. Nesse caso, as cópias de segurança menos frequentes são uma opção para reduzir os custos.

### <a name="scenario-4-iaas-application-data-issues"></a>Cenário 4: Problemas de dados de aplicação de IaaS

Problemas de dados de aplicação de IaaS são possibilidade de outra. Considere uma aplicação que calcula, mantém e serve de dados comerciais críticos, como obter informações sobre preços. Uma nova versão da sua aplicação tinha um erro de software que incorretamente calculados os preços e os dados existentes de comércio servidos pela plataforma de danificado. Aqui, o melhor método de ação é reverter para a versão anterior da aplicação e os dados. Para ativar esta opção, efetuar cópias de segurança periódicas do seu sistema.

## <a name="disaster-recovery-solution-azure-backup"></a>Solução de recuperação após desastre: cópia de segurança do Azure 

[Cópia de segurança do Azure](https://azure.microsoft.com/services/backup/) é utilizado para cópias de segurança e de DR, e funciona com [discos geridos pelo](../articles/virtual-machines/windows/managed-disks-overview.md) , bem como [não gerido discos](../articles/virtual-machines/windows/about-disks-and-vhds.md#unmanaged-disks). Pode criar uma tarefa de cópia de segurança com cópias de segurança baseados no tempo, fácil restauro de VM e as políticas de retenção de cópias de segurança. 

Se utilizar [discos de armazenamento Premium](../articles/virtual-machines/windows/premium-storage.md), [discos geridos pelo](../articles/virtual-machines/windows/managed-disks-overview.md), ou outros tipos de discos com o [armazenamento localmente redundante](../articles/storage/common/storage-redundancy.md#locally-redundant-storage) opção, é especialmente importante tornar DR periódica cópias de segurança. Cópia de segurança do Azure armazena os dados no seu Cofre de serviços de recuperação para a retenção de longo prazo. Escolha o [armazenamento georredundante](../articles/storage/common/storage-redundancy.md#geo-redundant-storage) cofre dos serviços de opção para a recuperação de cópia de segurança. Essa opção assegura que as cópias de segurança são replicadas noutra região do Azure para salvaguardar de perante desastres regionais.

Para [não gerido discos](../articles/virtual-machines/windows/about-disks-and-vhds.md#unmanaged-disks), pode utilizar o tipo de armazenamento localmente redundante de discos IaaS, mas certifique-se de que a cópia de segurança do Azure está ativada com a opção de armazenamento com redundância geográfica do Cofre de serviços de recuperação.

> [!NOTE]
> Se utilizar o [armazenamento georredundante](../articles/storage/common/storage-redundancy.md#geo-redundant-storage) ou [armazenamento georredundante com acesso de leitura](../articles/storage/common/storage-redundancy.md#read-access-geo-redundant-storage) opção para os discos não geridos, que ainda precisa de instantâneos consistentes para cópia de segurança e recuperação. Utilizar um [cópia de segurança do Azure](https://azure.microsoft.com/services/backup/) ou [instantâneos consistentes](#alternative-solution-consistent-snapshots).

 A tabela seguinte é um resumo das soluções disponíveis para DR.

| Cenário | Replicação automática | Solução de DR |
| --- | --- | --- |
| Discos de armazenamento Premium | Local ([armazenamento localmente redundante](../articles/storage/common/storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Managed disks | Local ([armazenamento localmente redundante](../articles/storage/common/storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Discos de armazenamento localmente redundante não gerido | Local ([armazenamento localmente redundante](../articles/storage/common/storage-redundancy.md#locally-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/) |
| Discos de armazenamento georredundante não gerido | Cruzada região ([armazenamento georredundante](../articles/storage/common/storage-redundancy.md#geo-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Instantâneos consistentes](#alternative-solution-consistent-snapshots) |
| Discos de armazenamento com redundância geográfica do acesso de leitura não gerido | Cruzada região ([armazenamento georredundante com acesso de leitura](../articles/storage/common/storage-redundancy.md#read-access-geo-redundant-storage)) | [Azure Backup](https://azure.microsoft.com/services/backup/)<br/>[Instantâneos consistentes](#alternative-solution-consistent-snapshots) |

Elevada disponibilidade melhor é cumprida ao utilizar discos geridos num conjunto, juntamente com a cópia de segurança do Azure de disponibilidade. Se utilizar discos não geridos, pode continuar a utilizar a cópia de segurança do Azure para DR. Se não for possível utilizar a cópia de segurança do Azure, em seguida, colocar [instantâneos consistentes](#alternative-solution-consistent-snapshots), conforme descrito na secção posterior, é uma solução alternativa para cópia de segurança e recuperação.

As opções de elevada disponibilidade, cópia de segurança e DR em níveis de infraestrutura ou de aplicação podem ser representadas da seguinte forma:

| Nível |   Elevada disponibilidade   | Cópia de segurança ou DR |
| --- | --- | --- |
| Aplicação | SQL Server AlwaysOn | Azure Backup |
| Infraestrutura    | Conjunto de disponibilidade  | Armazenamento georredundante com instantâneos consistentes |

### <a name="using-azure-backup"></a>Utilizar o Backup do Azure 

[Cópia de segurança do Azure](../articles/backup/backup-azure-vms-introduction.md) pode fazer cópias de segurança as VMs com Windows ou Linux para o Azure recovery services cofre. Cópia de segurança e restaurar dados críticos da empresa é complicou pelo facto da que dados críticos da empresa tem uma cópia de segurança, quando as aplicações que produzem os dados estão em execução. 

Para resolver este problema, o Backup do Azure fornece cópias de segurança consistentes com aplicações para cargas de trabalho da Microsoft. Utiliza o serviço sombra de volumes para se certificar de que a data for escrito corretamente para o armazenamento. Para VMs com Linux, apenas as cópias de segurança consistente com ficheiros são possíveis, porque Linux não tem uma funcionalidade equivalente para o serviço sombra de volumes.

![Fluxo de cópia de segurança do Azure][1]

Quando a cópia de segurança do Azure inicia uma tarefa de cópia de segurança na hora agendada, aciona a cópia de segurança de extensão instaladas na VM para criar um instantâneo de ponto no tempo. É obtido um instantâneo em coordenação com o serviço sombra de volumes para obter um instantâneo consistente dos discos na máquina virtual sem ter de encerrá-la. A extensão de cópia de segurança na VM esvaziamentos da todas as escritas antes de um instantâneo consistente de todos os discos. Depois de criar o instantâneo, os dados são transferidos através da cópia de segurança do Azure para o Cofre de cópia de segurança. Para facilitar o processo de cópia de segurança mais eficiente, o serviço identifica, transfere apenas os blocos de dados que tenham sido alterados desde a última cópia de segurança.

Para restaurar, pode ver as cópias de segurança disponíveis através de cópia de segurança do Azure e, em seguida, iniciar um restauro. Pode criar e restaurar cópias de segurança do Azure através de [portal do Azure](https://portal.azure.com/), por [através do PowerShell](../articles/backup/backup-azure-vms-automation.md), ou utilizando o [CLI do Azure](/cli/azure/). 

### <a name="steps-to-enable-a-backup"></a>Passos para ativar uma cópia de segurança

Utilize os seguintes passos para ativar as cópias de segurança das suas VMs utilizando o [portal do Azure](https://portal.azure.com/). Há alguns variação dependendo do seu cenário exato. Consulte o [cópia de segurança do Azure](../articles/backup/backup-azure-vms-introduction.md) documentação para obter detalhes completos. Azure cópia de segurança também [suporta VMs com discos geridos](https://azure.microsoft.com/blog/azure-managed-disk-backup/).

1.  Crie um cofre dos serviços de recuperação para uma VM:

    a. No [portal do Azure](https://portal.azure.com/), procurar **todos os recursos** e localizar **cofres dos serviços de recuperação**.

    b. No **cofres dos serviços de recuperação** menu, clique em **adicionar** e siga os passos para criar um novo cofre na mesma região da VM. Por exemplo, se a VM está a ser a região EUA oeste, escolha EUA oeste para o cofre.

2.  Certifique-se a replicação de armazenamento para o Cofre recentemente criado. Aceder ao cofre em **cofres dos serviços de recuperação** e aceda a **definições** > **configuração de cópia de segurança**. Certifique-se a **armazenamento georredundante** opção está selecionada por predefinição. Isto garante que o seu Cofre automaticamente é replicado para um datacenter secundário. Por exemplo, o seu Cofre nos EUA oeste automaticamente é replicado para EUA Leste.

3.  Configurar a política de cópia de segurança e selecione a VM a partir da mesma IU.

4.  Certifique-se de que o agente de cópia de segurança está instalado na VM. Se a VM é criada utilizando uma imagem de galeria do Azure, o agente de cópia de segurança já está instalado. Caso contrário (ou seja, se utilizar uma imagem personalizada), utilize as instruções para [instale o agente VM numa máquina virtual](../articles/backup/backup-azure-arm-vms-prepare.md#install-the-vm-agent-on-the-virtual-machine).

5.  Certifique-se de que a VM possibilita a conectividade de rede para o serviço de cópia de segurança para a função. Siga as instruções para [conectividade de rede](../articles/backup/backup-azure-arm-vms-prepare.md#network-connectivity).

6.  Depois de concluídos os passos anteriores, a cópia de segurança é executada em intervalos regulares, conforme especificado na política de cópia de segurança. Se necessário, pode acionar a cópia de segurança primeiro manualmente a partir do dashboard do cofre no portal do Azure.

Para automatização da cópia de segurança do Azure utilizando scripts, consulte [cmdlets do PowerShell para cópia de segurança VM](../articles/backup/backup-azure-vms-automation.md).

### <a name="steps-for-recovery"></a>Passos de recuperação

Se precisar de reparação ou rebuild uma VM, pode restaurar a VM a partir de qualquer um dos pontos de recuperação de cópia de segurança no cofre. Existem algumas das diferentes opções para efetuar a recuperação:

-   Pode criar uma nova VM como uma representação de ponto no tempo da sua VM de cópia de segurança.

-   Pode restaurar os discos e, em seguida, utilize o modelo para a VM para personalizar e reconstrua a VM restaurada. 

Para obter mais informações, consulte as instruções para [utilizar o portal do Azure para monitorizar máquinas virtuais](../articles/backup/backup-azure-arm-restore-vms.md). Este documento também explica os passos específicos para restaurar VMs de cópia de segurança para um centro de dados emparelhado com o seu Cofre de cópia de segurança georredundante se ocorrer um desastre no Centro de dados primário. Nesse caso, cópia de segurança do Azure utiliza o serviço de computação da região secundária para criar a máquina virtual restaurada.

Também pode utilizar o PowerShell para [restaurar uma VM](../articles/backup/backup-azure-arm-restore-vms.md#restore-a-vm-during-an-azure-datacenter-disaster) ou para [criar uma nova VM do restaurada discos](../articles/backup/backup-azure-vms-automation.md#create-a-vm-from-restored-disks).

## <a name="alternative-solution-consistent-snapshots"></a>Solução alternativa: instantâneos consistentes

Se não for possível utilizar a cópia de segurança do Azure, pode implementar o seus próprios mecanismo de cópia de segurança através de instantâneos. Criação de instantâneos consistentes para todos os discos utilizados por uma VM e, em seguida, replicar os instantâneos noutra região, é complicado. Por este motivo, o Azure considera utilizando o serviço de cópia de segurança como uma opção melhor a criação de uma solução personalizada. 

Se utilizar o armazenamento georredundante armazenamento/georredundante de acesso de leitura de discos, os instantâneos automaticamente são replicados para um datacenter secundário. Se utilizar o armazenamento localmente redundante de discos, terá de replicar os dados por si. Para obter mais informações, consulte [cópia de segurança discos VM não gerido do Azure com instantâneos incrementais](../articles/virtual-machines/windows/incremental-snapshots.md).

Um instantâneo é uma representação de um objeto de um ponto específico no tempo. Um instantâneo incorreu faturação para o tamanho incremental dos dados contém. Para obter mais informações, consulte [criar um instantâneo de blob](../articles/storage/blobs/storage-blob-snapshots.md).

### <a name="create-snapshots-while-the-vm-is-running"></a>Criar instantâneos enquanto a VM está em execução

Embora pode tirar um instantâneo em qualquer altura, se a VM está em execução, ainda está a ser transmitidos para os discos de dados e os instantâneos podem conter operações parciais que estavam em trânsito. Além disso, se existirem vários discos envolvidas, poderão ter havido os instantâneos de discos diferentes em momentos diferentes. Isto significa que estes instantâneos podem não estar coordenados. Isto é particularmente problemático para os volumes repartidos cujos ficheiros podem estar danificados se a ser efetuadas alterações durante a cópia de segurança.

Para evitar esta situação, o processo de cópia de segurança tem de implementar os seguintes passos:

1.  Parar todos os discos.

2.  Esvaziar todas as operações de escrita pendentes.

3.  [Criar um instantâneo de blob](../articles/storage/blobs/storage-blob-snapshots.md) para todos os discos.

Algumas aplicações do Windows, como o SQL Server, fornecem um mecanismo de cópia de segurança coordenado através de um serviço sombra de volumes para criar cópias de segurança consistentes com aplicações. No Linux, pode utilizar uma ferramenta como fsfreeze para coordenar os discos. Esta ferramenta fornece cópias de segurança consistente com ficheiros, mas não consistentes com aplicações instantâneos. Este processo é complexo, pelo que deve considerar a utilização [cópia de segurança do Azure](../articles/backup/backup-azure-vms-introduction.md) ou uma solução de cópia de segurança de terceiros que já o implementa este procedimento.

Os resultados do processo anterior de uma coleção de instantâneos coordenadas para todos os discos VM, que representa uma vista de ponto no tempo específica da VM. Este é um ponto de restauro de cópia de segurança para a VM. Pode repetir o processo em intervalos agendados para criar cópias de segurança periódicas. Consulte [copiar as cópias de segurança noutra região](#copy-the-snapshots-to-another-region) para obter os passos copiar os instantâneos noutra região para DR.

### <a name="create-snapshots-while-the-vm-is-offline"></a>Criar instantâneos enquanto a VM está offline

Outra opção para criar cópias de segurança é encerre a VM e criar instantâneos do blob de cada disco. Tirar instantâneos do blob é mais fácil de coordenação de instantâneos de uma VM em execução, mas requer alguns minutos de indisponibilidade.

1. Encerre a VM.

2. Crie um instantâneo de cada blob do disco rígido virtual, que demora apenas alguns segundos.

    Para criar um instantâneo, pode utilizar [PowerShell](../articles/storage/common/storage-powershell-guide-full.md), a [API de REST do Storage do Azure](https://msdn.microsoft.com/library/azure/ee691971.aspx), [CLI do Azure](/cli/azure/), ou uma das bibliotecas de cliente do armazenamento do Azure, tal como [o Biblioteca de clientes de armazenamento para .NET](https://msdn.microsoft.com/library/azure/hh488361.aspx).

3. Inicie a VM, que termina o período de indisponibilidade. Normalmente, todo o processo é concluído dentro de alguns minutos.

Este processo gera uma coleção de instantâneos consistentes para todos os discos, fornecer um ponto de restauro de cópia de segurança para a VM.

### <a name="copy-the-snapshots-to-another-region"></a>Copie os instantâneos noutra região

Criação de instantâneos por si só não pode ser suficiente para DR. Também têm de replicar as cópias de segurança do instantâneo noutra região.

Se utilizar o armazenamento georredundante ou armazenamento georredundante com acesso de leitura para os discos, em seguida, os instantâneos são replicados para a região secundária automaticamente. Pode haver alguns minutos do desfasamento antes da replicação. Se o datacenter primário ficar inativo antes dos instantâneos de concluir a replicação, não é possível aceder aos instantâneos do datacenter secundário. A probabilidade de este é pequena.

> [!NOTE] 
> Só ter discos em armazenamento georredundante ou acesso de leitura georredundante conta de armazenamento protege a VM contra desastres inesperados. Também tem de criar instantâneos coordenados ou utilizar o Backup do Azure. Isto é necessário para recuperar uma VM para um estado consistente.

Se utilizar o armazenamento localmente redundante, tem de copiar os instantâneos para uma conta de armazenamento diferente imediatamente depois de criar o instantâneo. O destino de cópia pode ser uma conta de armazenamento localmente redundante numa região diferente, resultando em de cópia está a ser uma região remota. Também pode copiar o instantâneo para uma conta de armazenamento georredundante com acesso de leitura na mesma região. Neste caso, o instantâneo lenta é replicado para a região secundária remota. A cópia de segurança se encontra protegida contra desastres inesperados no site primário depois do copiar e a replicação esteja concluída.

Para copiar os instantâneos incrementais para DR forma eficiente, reveja as instruções em [cópia de segurança do Azure discos VM não geridos com instantâneos incrementais](../articles/virtual-machines/windows/incremental-snapshots.md).

![Criar cópias de segurança do Azure discos VM não geridos com instantâneos incrementais][2]

### <a name="recovery-from-snapshots"></a>Recuperação a partir de instantâneos

Para obter um instantâneo, copie-o para tornar um novo blob. Se estiver a copiar o instantâneo da conta do principal, pode copiar o instantâneo ao longo para o blob de base do instantâneo. Este processo reverte o disco para o instantâneo. Este processo é conhecido como promover o instantâneo. Se estiver a copiar a cópia de segurança do instantâneo de uma conta do secundária, no caso de uma conta de armazenamento georredundante com acesso de leitura, tem de o copiar para uma conta do principal. Pode copiar um instantâneo por [através do PowerShell](../articles/storage/common/storage-powershell-guide-full.md) ou utilizando o utilitário do AzCopy. Para obter mais informações, consulte [transferir dados com o utilitário de linha de comandos do AzCopy](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy).

Para VMs com vários discos, tem de copiar todos os instantâneos que fazem parte do mesmo ponto de restauro coordenada. Depois de copiar os instantâneos para blobs VHD graváveis, pode utilizar os blobs para recriar a VM utilizando o modelo para a VM.

## <a name="other-options"></a>Outras opções

### <a name="sql-server"></a>SQL Server

SQL Server em execução numa VM tem as suas próprias funções incorporadas, a partilha de cópia de segurança da base de dados do SQL Server para o Blob storage do Azure ou um ficheiro. Se a conta de armazenamento é armazenamento georredundante ou armazenamento georredundante com acesso de leitura, pode aceder a essas cópias de segurança no Centro de dados secundária a conta de armazenamento em caso de desastre, com as mesmas restrições conforme discutidas anteriormente. Para obter mais informações, consulte [cópia de segurança e restaurar para o SQL Server em virtual machines do Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-backup-recovery.md). Além disso, para fazer cópias de segurança e restaurar, [grupos de Disponibilidade AlwaysOn do SQL Server](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-high-availability-dr.md) pode manter as réplicas secundárias de bases de dados. Isto reduz significativamente o tempo de recuperação após desastre.

## <a name="other-considerations"></a>Outras considerações

Este artigo foi abordado como fazer cópias de segurança ou criar instantâneos das suas VMs e os respetivos discos para suportar a recuperação após desastre e como utilizá-los para recuperar os dados. Com o modelo Azure Resource Manager, muitas pessoas utilizam modelos para criar as respetivas VMs e outras infraestruturas no Azure. Pode utilizar um modelo para criar uma VM que tenha a mesma configuração de cada vez. Se utilizar imagens personalizadas para criar as suas VMs, tem também de se certificar de que as imagens estão protegidas com uma conta de armazenamento georredundante com acesso de leitura para armazená-las.

Por conseguinte, o processo de cópia de segurança pode ser uma combinação de duas coisas:

- Cópia de segurança de dados (discos).
- Cópia de segurança de configuração (modelos e imagens personalizadas).

Dependendo da opção de cópia de segurança que escolher, poderá ter de lidar com a cópia de segurança dos dados e a configuração ou o serviço de cópia de segurança pode lidar com todo que para si.

## <a name="appendix-understanding-the-impact-of-data-redundancy"></a>Apêndice: Compreender o impacto de redundância de dados

Para contas de armazenamento do Azure, existem três tipos de redundância de dados que deve considerar relativamente a recuperação após desastre: localmente redundante, georredundante ou georredundante com acesso de leitura. 

Armazenamento localmente redundante mantém três cópias dos dados no mesmo centro de dados. Quando a VM escreve os dados, todos os três cópias são atualizadas antes de sucesso é devolvido ao autor da chamada, para saber que se são idênticos. O disco se encontra protegido contra falhas locais, porque não é muito provável que todos os três cópias são afetadas ao mesmo tempo. No caso de armazenamento localmente redundante, não há nenhum georredundância, para que o disco não se encontra protegido contra falhas catastrófica, que podem afetar uma unidade de armazenamento ou centro de dados completa.

Com o armazenamento georredundante e o armazenamento georredundante com acesso de leitura, três cópias dos seus dados são mantidas na região primária que está selecionada por si. Mais três cópias dos seus dados são mantidas numa região secundária correspondente definido pelo Azure. Por exemplo, se armazenar dados nos EUA oeste, os dados são replicados para EUA Leste. Retenção de cópia é feita de forma assíncrona e, existe um pequeno atraso entre as atualizações para os sites primários e secundários. As réplicas dos discos no site secundário são consistentes numa base por disco (com atraso), mas as réplicas de vários discos de Active Directory poderão não estar sincronizadas com entre si. Para que as réplicas consistentes entre múltiplos discos, são necessárias instantâneos consistentes.

A principal diferença entre o armazenamento georredundante e o armazenamento georredundante com acesso de leitura é que com o armazenamento georredundante com acesso de leitura, pode ler a cópia secundária em qualquer altura. Se existir um problema que apresenta os dados na região primária inacessível, a equipa do Azure faz com que cada esforço para restaurar o acesso. Enquanto o principal está inativo, se tiver o armazenamento georredundante com acesso de leitura ativado, pode aceder aos dados no Centro de dados secundário. Por conseguinte, se pretender ler a partir da réplica enquanto o principal está inacessível, em seguida, armazenamento georredundante com acesso de leitura deve ser considerado.

Se transforma seja uma indisponibilidade significativa, a equipa do Azure pode acionar uma ativação pós-falha georreplicação e alterar as entradas DNS principais para apontar para o armazenamento secundário. Neste momento, se tiver o armazenamento georredundante ou armazenamento georredundante com acesso de leitura ativado, pode aceder aos dados a região utilizada para ser o elemento secundário. Por outras palavras, se a sua conta de armazenamento é armazenamento georredundante e existir um problema, pode aceder ao armazenamento secundário apenas se não houver uma ativação pós-falha georreplicação.

Para obter mais informações, consulte [o que fazer se ocorrer uma falha de armazenamento do Azure](../articles/storage/common/storage-disaster-recovery-guidance.md). 

>[!NOTE] 
>A Microsoft controla se ocorrer uma ativação pós-falha. Ativação pós-falha não é controlada por conta de armazenamento, pelo que não é decidido por clientes individuais. Para implementar a recuperação após desastre para as contas de armazenamento específicas ou discos da máquina virtual, tem de utilizar as técnicas descritas anteriormente neste artigo.



[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png
