---
title: "Melhores práticas do desempenho para o SQL Server no Azure | Microsoft Docs"
description: "Apresenta as melhores práticas para otimizar o desempenho de SQL Server em VMs do Microsoft Azure."
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2017
ms.author: jroth
ms.openlocfilehash: e502be189a29590ebe0d848b3ec43611db8d035d
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/02/2017
---
# <a name="performance-best-practices-for-sql-server-in-azure-virtual-machines"></a>Melhores práticas de desempenho do SQL Server nas Máquinas Virtuais do Azure

## <a name="overview"></a>Descrição geral

Este tópico fornece as melhores práticas para otimizar o desempenho de SQL Server na máquina de Virtual do Microsoft Azure. Ao executar o SQL Server em Azure Virtual Machines, recomendamos que continue a utilizar o mesmo desempenho de base de dados de otimização de opções que são aplicáveis ao SQL Server no ambiente de servidor no local. No entanto, o desempenho da base de dados relacional numa nuvem pública depende de vários fatores, tais como o tamanho de uma máquina virtual e a configuração dos discos de dados.

Quando criar imagens do SQL Server, [considerar as suas VMs no portal do Azure de aprovisionamento](virtual-machines-windows-portal-sql-server-provision.md). VMs de SQL Server aprovisionado no Portal com o Resource Manager implementar todas estas melhores práticas, incluindo a configuração de armazenamento.

Este artigo concentra-se em obter o *melhor* desempenho para o SQL Server em VMs do Azure. Se a carga de trabalho for inferior pedir o seu trabalho, não poderá necessitar de cada otimização listada abaixo. Considere as necessidades de desempenho e padrões de carga de trabalho como avaliar estas recomendações.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="quick-check-list"></a>Lista de verificação rápida

Segue-se uma lista de verificação rápida para um desempenho ideal do SQL Server em Azure Virtual Machines:

| Área | Otimizações de |
| --- | --- |
| [Tamanho da VM](#vm-size-guidance) |[DS3](../../virtual-machines-windows-sizes-memory.md) ou superior para o SQL Server Enterprise edition.<br/><br/>[DS2](../../virtual-machines-windows-sizes-memory.md) ou superior para edições Standard do SQL Server e Web. |
| [Armazenamento](#storage-guidance) |Utilize [armazenamento Premium](../premium-storage.md). Armazenamento Standard só é recomendado para dev/teste.<br/><br/>Manter o [conta de armazenamento](../../../storage/common/storage-create-storage-account.md) e VM do SQL Server na mesma região.<br/><br/>Desativar o Azure [armazenamento georredundante](../../../storage/common/storage-redundancy.md) (georreplicação) na conta de armazenamento. |
| [Discos](#disks-guidance) |Utilize um mínimo de 2 [P30 discos](../premium-storage.md#scalability-and-performance-targets) (1 para ficheiros de registo; 1 para ficheiros de dados e o TempDB).<br/><br/>Evite utilizar o sistema operativo ou discos temporários para o armazenamento de base de dados ou registo.<br/><br/>Permitir leitura de colocação em cache em discos que alojam os ficheiros de dados e o TempDB.<br/><br/>Não ative a colocação em cache em discos que alojam o ficheiro de registo.<br/><br/>Importante: Pare o serviço do SQL Server ao alterar as definições de cache para um disco da VM do Azure.<br/><br/>Stripe vários discos de dados do Azure para obter o aumento do débito de e/s.<br/><br/>O formato com o tamanho de alocação documentado. |
| [E/S](#io-guidance) |Ative a compressão de página de base de dados.<br/><br/>Ative a inicialização instantânea de ficheiros para ficheiros de dados.<br/><br/>Limitar ou desativar a opção autogrow na base de dados.<br/><br/>Desative autoshrink na base de dados.<br/><br/>Mova bases de dados de todos os discos de dados, incluindo bases de dados do sistema.<br/><br/>Mova SQL Server erro registo e rastreio ficheiro diretórios para discos de dados.<br/><br/>Configure localizações de ficheiros de cópia de segurança e a base de dados predefinidos.<br/><br/>Ative páginas bloqueadas.<br/><br/>Aplica correções de desempenho de SQL Server. |
| [Funcionalidades específicas](#feature-specific-guidance) |Cópia de segurança diretamente ao armazenamento de Blobs. |

Para mais informações sobre *como* e *por que motivo* para efetuar estas otimizações, reveja os detalhes e as orientações fornecidas nas secções seguintes.

## <a name="vm-size-guidance"></a>Orientações de tamanho VM

Para aplicações confidenciais de desempenho, é recomendado que utilize o seguinte [tamanhos de máquinas virtuais](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json):

* **SQL Server Enterprise Edition**: DS3 ou superior
* **As edições Web e SQL Server Standard**: DS2 ou superior

## <a name="storage-guidance"></a>Orientações de armazenamento

Suporte de VMs de série DS (juntamente com a série dsv2 e GS série) [armazenamento Premium](../premium-storage.md). Armazenamento Premium é recomendado para todas as cargas de trabalho de produção.

> [!WARNING]
> Armazenamento standard tem largura de banda e latências variando e só é recomendado para cargas de trabalho de programador/teste. Cargas de trabalho de produção devem utilizar o armazenamento Premium.

Além disso, recomendamos que crie a sua conta do storage do Azure no mesmo centro de dados de máquinas virtuais do SQL Server para reduzir a atrasos de transferência. Ao criar uma conta de armazenamento, desative a georreplicação conforme a ordem de escrita consistente entre múltiplos discos não é garantida. Em vez disso, considere configurar uma tecnologia de recuperação de desastres do SQL Server entre centros de dados do Azure dois. Para obter mais informações, consulte [elevada disponibilidade e recuperação após desastre do SQL Server em Azure Virtual Machines](virtual-machines-windows-sql-high-availability-dr.md).

## <a name="disks-guidance"></a>Documentação de orientação dos discos

Existem três tipos de disco principal numa VM do Azure:

* **Disco do SO**: ao criar uma Máquina Virtual do Azure, a plataforma ligará pelo menos um disco (identificados como o **C** unidade) para a VM para o disco de sistema operativo. Este disco é um VHD armazenado como um blob de página no armazenamento.
* **Disco temporário**: máquinas virtuais do Azure contêm outro disco chamado o disco temporário (identificados como o **D**: unidade). Este é um disco no nó que pode ser utilizado para o espaço scratch.
* **Os discos de dados**: também pode anexar mais discos para a máquina virtual como discos de dados, e estas serão armazenadas no armazenamento como blobs de páginas.

As secções seguintes descrevem recomendações para utilizar estes discos diferentes.

### <a name="operating-system-disk"></a>Disco do sistema operativo

Um disco de sistema operativo é um VHD que pode efetuar o arranque e montar como uma versão de um sistema operativo em execução e está assinalada como como **C** unidade.

Predefinição a colocação em cache da política no disco do sistema operativo é **leitura/escrita**. Para aplicações confidenciais de desempenho, recomendamos que utilize discos de dados em vez do disco do sistema operativo. Consulte a secção em discos de dados abaixo.

### <a name="temporary-disk"></a>Disco temporário

A unidade de armazenamento temporário, identificada como o **D**: disco, não é transferido para o blob storage do Azure. Não armazene os ficheiros de base de dados de utilizador ou ficheiros de registo de transação de utilizador no **D**: unidade.

Para a série D, série Dv2 e VMs de série de G, unidade temporária nestas VMS é baseadas em SSD. Se a carga de trabalho fizer utilização intensa de TempDB (por exemplo, para objetos temporários ou associações complexas), TempDB a armazenar o **D** unidade pode resultar numa maior débito de TempDB e reduzir a latência de TempDB.

Para VMs que suportam o Premium Storage (série DS, série dsv2 e GS série), recomendamos o armazenamento de TempDB num disco que suporte o Premium Storage com cache de leitura ativado. Há uma exceção para esta recomendação; Se a utilização de TempDB escrita intensivas, pode alcançar um desempenho superior armazenando TempDB local **D** unidade, o que também seja baseadas em SSD nestes tamanhos de máquina.

### <a name="data-disks"></a>discos de dados

* **Utilizar discos de dados de ficheiros de registo e dados**: no mínimo, utilize 2 Premium Storage [P30 discos](../premium-storage.md#scalability-and-performance-targets) em que um disco contém os ficheiros de registo e o outro contém os dados e ficheiros de TempDB. Cada disco de armazenamento Premium fornece um número de IOPs e largura de banda (MB/s), dependendo do tamanho, conforme descrito no seguinte artigo: [utilizando o Premium Storage para discos](../premium-storage.md).

* **Disco Striping**: para obter mais débito, pode adicionar discos de dados adicionais e utilizar Striping de disco. Para determinar o número de discos de dados, tem de analisar o número de IOPS e largura de banda necessária para os ficheiros de registo de e para os seus dados e ficheiros de TempDB. Tenha em atenção que os tamanhos VM diferentes têm limites diferentes no número de IOPs e largura de banda suportadas, consulte as tabelas no IOPS por [tamanho da VM](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Utilize as seguintes diretrizes:

  * Para Windows 8/Windows Server 2012 ou posterior, utilize [espaços de armazenamento](https://technet.microsoft.com/library/hh831739.aspx) com as seguintes diretrizes:

      1. Defina a intercalação (tamanho do stripe) a 64 KB (65536 bytes) para cargas de trabalho OLTP e 256 KB (262144 bytes) para cargas de trabalho de armazenamento de dados para evitar o impacto no desempenho devido a desalinhamento de partição. Isto deve ser definido com o PowerShell.
      1. Definir o número de colunas = número de discos físicos. Utilize o PowerShell quando configurar mais de 8 discos (não IU do Gestor de servidor). 

    Por exemplo, o PowerShell seguinte cria um novo agrupamento de armazenamento com o tamanho de intercalação de 64 KB e o número de colunas para 2:

    ```powershell
    $PoolCount = Get-PhysicalDisk -CanPool $True
    $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

    New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple –UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false 
    ```

  * Para o Windows 2008 R2 ou anterior, pode utilizar discos dinâmicos (volumes repartido do SO) e o tamanho do stripe é sempre 64 KB. Tenha em atenção que esta opção é preterida a partir do Windows 8/Windows Server 2012. Para obter informações, consulte a declaração de suporte em [serviço de discos virtuais está a mudar para a API de gestão de armazenamento do Windows](https://msdn.microsoft.com/library/windows/desktop/hh848071.aspx).

  * Se a carga de trabalho não é exigente em termos de registo e não precisa de IOPs dedicados, pode configurar um agrupamento de armazenamento. Caso contrário, crie duas agrupamentos de armazenamento, uma para os ficheiros de registo e a outro agrupamento de armazenamento para os ficheiros de dados e o TempDB. Determine o número de discos associados a cada agrupamento de armazenamento com base nas suas expectativas de carga. Tenha em atenção que os tamanhos de VM diferentes permitem números diferentes de discos de dados anexados. Para obter mais informações, consulte [tamanhos das Virtual Machines](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

  * Se não estiver a utilizar o armazenamento Premium (cenários de desenvolvimento/teste), recomenda-se adicionar o número máximo de discos de dados suportados pela sua [tamanho da VM](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e utilizar Striping de disco.

* **Política de colocação em cache**: discos de dados para o Premium Storage, ativar a colocação em cache de leitura de discos de dados que aloja os ficheiros de dados e o TempDB apenas. Se não estiver a utilizar o armazenamento Premium, não ative a qualquer colocação em cache em qualquer discos de dados. Para obter instruções sobre como configurar a colocação em cache em disco, consulte os tópicos seguintes: [conjunto AzureOSDisk](https://msdn.microsoft.com/library/azure/jj152847) e [conjunto AzureDataDisk](https://msdn.microsoft.com/library/azure/jj152851.aspx).

  > [!WARNING]
  > Pare o serviço SQL Server quando alterar a definição de cache de discos de VM do Azure para evitar a possibilidade de quaisquer danos na base de dados.

* **Tamanho de unidade de alocação de NTFS**: quando formatar o disco de dados, é recomendado que utilize um tamanho de unidade de alocação de 64 KB para ficheiros de registo e dados, bem como TempDB.

* **Melhores práticas de gestão de disco**: Quando remover um disco de dados ou alterar o seu tipo de cache, pare o serviço do SQL Server durante a alteração. Quando são alteradas as definições de colocação em cache no disco do SO, o Azure para a VM, altera o tipo de cache e reinicia a VM. Quando as definições da cache de um disco de dados são alteradas, a VM não está parada, mas o disco de dados é desligado da VM durante a alteração e, em seguida, novamente ligado.

  > [!WARNING]
  > Falha ao parar o serviço SQL Server durante estas operações pode danificá-la.

## <a name="io-guidance"></a>Orientações de e/s

* Obter os melhores resultados com o Premium Storage são obtidos quando parallelize a sua aplicação e os pedidos. Armazenamento Premium foi concebido para cenários em que a profundidade de fila de e/s é superior a 1, pelo que verá pouco ou nenhum ganhos de desempenho para pedidos de série single-threaded (mesmo que se encontrem exigente em termos de armazenamento). Por exemplo, isto poderia afetar os resultados do teste single-threaded das ferramentas de análise de desempenho, tais como SQLIO.

* Considere a utilização de [compressão da página de base de dados](https://msdn.microsoft.com/library/cc280449.aspx) como pode ajudar a melhorar o desempenho das cargas de trabalho exigente em termos de e/s. No entanto, a compressão de dados pode aumentar o consumo de CPU no servidor de base de dados.

* Considere ativar a inicialização instantânea de ficheiros reduzir o tempo necessário para alocação de ficheiro inicial. Para tirar partido de inicialização instantânea de ficheiros, conceda à conta de serviço do SQL Server (MSSQLSERVER) com SE_MANAGE_VOLUME_NAME e adicioná-lo para o **efetuar tarefas de manutenção de Volume** política de segurança. Se estiver a utilizar uma imagem de plataforma do SQL Server para o Azure, a conta de serviço predefinido (NT Service\MSSQLSERVER) não está adicionada para o **efetuar tarefas de manutenção de Volume** política de segurança. Por outras palavras, inicialização instantânea de ficheiros não está ativada numa imagem de plataforma do Azure do SQL Server. Após a adição da conta de serviço do SQL Server para o **efetuar tarefas de manutenção de Volume** política de segurança, reinicie o serviço do SQL Server. Podem existir considerações de segurança para utilizar esta funcionalidade. Para obter mais informações, consulte [inicialização de ficheiro de base de dados](https://msdn.microsoft.com/library/ms175935.aspx).

* **o aumento automático** é considerada simplesmente plano de contingência para crescimento inesperado. Não é gerir o seu crescimento de dados e de registo numa base diária com o aumento automático. Se for utilizada a opção autogrow, aumente previamente o ficheiro com o comutador de tamanho.

* Certifique-se **autoshrink** está desativada para evitar custos desnecessários que podem afetar negativamente o desempenho.

* Mova bases de dados de todos os discos de dados, incluindo bases de dados do sistema. Para obter mais informações, consulte [mover bases de dados de sistema](https://msdn.microsoft.com/library/ms345408.aspx).

* Mova SQL Server erro registo e rastreio ficheiro diretórios para discos de dados. Isto pode ser feito no Gestor de configuração de servidor do SQL Server ao clicar a instância do SQL Server e ao selecionar propriedades. As definições de ficheiros de registo e rastreio erro podem ser alteradas no **parâmetros de arranque** separador. O diretório de informação do estado é especificado no **avançadas** separador. A seguinte captura de ecrã mostra onde serve para o parâmetro de arranque do registo de erro.

    ![Captura de ecrã de registo de erros SQL](./media/virtual-machines-windows-sql-performance/sql_server_error_log_location.png)

* Configure localizações de ficheiros de cópia de segurança e a base de dados predefinidos. Utilize as recomendações neste tópico e efetue as alterações na janela de propriedades do servidor. Para obter instruções, consulte [ver ou alterar as localizações predefinidas para dados e ficheiros de registo (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Captura de ecrã seguinte demonstra onde pretende efetuar estas alterações.

    ![Ficheiros de registo de dados do SQL Server e de cópia de segurança](./media/virtual-machines-windows-sql-performance/sql_server_default_data_log_backup_locations.png)
* Ative páginas bloqueadas reduzir e/s e quaisquer atividades de paginação. Para obter mais informações, consulte [ativar as páginas de bloqueio na opção de memória (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

* Se estiver a executar o SQL Server 2012, instale o Service Pack 1 a atualização cumulativa 10. Esta atualização contém a correção para fraco desempenho em e/s ao executar selecione numa declaração de tabela temporária no SQL Server 2012. Para obter informações, consulte este [artigo da base de dados de conhecimento](http://support.microsoft.com/kb/2958012).

* Considere a compressão de quaisquer ficheiros de dados ao transferir/fora do Azure.

## <a name="feature-specific-guidance"></a>Orientações específicas de funcionalidade

Algumas implementações poderão alcançar os benefícios de desempenho adicionais utilizando técnicas de configuração mais avançadas. A lista seguinte destaca algumas funcionalidades do SQL Server que podem ajudar a melhorar o desempenho:

* **Cópia de segurança para armazenamento do Azure**: quando efetuar cópias de segurança para o SQL Server em execução em máquinas virtuais do Azure, pode utilizar [cópia de segurança do SQL Server para URL](https://msdn.microsoft.com/library/dn435916.aspx). Esta funcionalidade está disponível a partir de com o SQL Server 2012 SP1 CU2 e recomendada para cópia de segurança nos discos de dados anexados. Quando a cópia de segurança/restauro para/de armazenamento do Azure, siga as recomendações fornecidas ao [SQL Server cópia de segurança para URL melhores práticas e resolução de problemas e restaurar a partir de cópias de segurança armazenadas no armazenamento do Azure](https://msdn.microsoft.com/library/jj919149.aspx). Também pode automatizar estas cópias de segurança utilizando [cópia de segurança automatizada do SQL Server em Azure Virtual Machines](virtual-machines-windows-sql-automated-backup.md).

    Antes do SQL Server 2012, pode utilizar [cópia de segurança do SQL Server para a ferramenta do Azure](https://www.microsoft.com/download/details.aspx?id=40740). Esta ferramenta pode ajudar a aumentar o débito de cópia de segurança com vários destinos de cópia de segurança do stripe.

* **Ficheiros de dados do SQL Server no Azure**: esta nova funcionalidade, [ficheiros de dados do SQL Server no Azure](https://msdn.microsoft.com/library/dn385720.aspx), está disponível a partir de com o SQL Server 2014. Executar o SQL Server com ficheiros de dados no Azure demonstra comparável características de desempenho e como utilizar discos de dados do Azure.

## <a name="next-steps"></a>Passos Seguintes

Para melhores práticas de segurança, consulte [considerações de segurança para o SQL Server em Azure Virtual Machines](virtual-machines-windows-sql-security.md).

Reveja os outros tópicos de Máquina Virtual do SQL Server em [do SQL Server na descrição geral de máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
