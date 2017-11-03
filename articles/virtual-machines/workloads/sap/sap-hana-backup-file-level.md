---
title: "Cópia de segurança do SAP HANA do Azure no nível de ficheiro | Microsoft Docs"
description: "Existem duas principais cópia de segurança as possibilidades de SAP HANA em máquinas virtuais do Azure, este artigo abrange SAP HANA Backup do Azure no nível de ficheiro"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 3/13/2017
ms.author: rclaus
ms.openlocfilehash: 5db0ceb1648b5afa278e1cbe1c42fce8033bfdc1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sap-hana-azure-backup-on-file-level"></a>Cópia de segurança do SAP HANA do Azure no nível de ficheiro

## <a name="introduction"></a>Introdução

Isto faz parte de uma série de três partes de artigos relacionados numa cópia de segurança de SAP HANA. [Cópia de segurança manual para SAP HANA em Azure Virtual Machines](./sap-hana-backup-guide.md) fornece uma descrição geral e informações de introdução, e [cópia de segurança de SAP HANA com base nos instantâneos de armazenamento](./sap-hana-backup-storage-snapshots.md) abrange a opção de cópia de segurança do instantâneo com base em armazenamento.

Observar os tamanhos de VM do Azure, um pode ver que um GS5 permite 64 discos de dados anexados. Para grandes sistemas de SAP HANA, um número significativo de discos já pode ser executado para ficheiros de registo e dados, possivelmente, em combinação com RAID de software para o débito de e/s de disco ideal. Em seguida, a questão não é onde pretende armazenar os ficheiros cópia de segurança de SAP HANA, o que podem preencher os discos de dados anexados ao longo do tempo? Consulte [tamanhos de máquinas virtuais do Linux no Azure](../../linux/sizes.md) para as tabelas de tamanho da VM do Azure.

Não há nenhum integração de cópia de segurança de SAP HANA disponível com o serviço de cópia de segurança do Azure neste momento. É a forma de padrão para gerir a cópia de segurança/restauro ao nível do ficheiro com uma baseado no ficheiro de cópia de segurança por SAP HANA Studio ou através de instruções SQL do SAP HANA. Consulte [SAP HANA SQL e referência de vistas de sistema](https://help.sap.com/hana/SAP_HANA_SQL_and_System_Views_Reference_en.pdf) para obter mais informações.

![Esta ilustração mostra a caixa de diálogo do item de menu de cópia de segurança no SAP HANA Studio](media/sap-hana-backup-file-level/image022.png)

Esta ilustração mostra a caixa de diálogo do item de menu de cópia de segurança no Studio do SAP HANA. Quando escolher o tipo &quot;ficheiro,&quot; um tem de especificar um caminho no sistema de ficheiros onde SAP HANA escreve os ficheiros de cópia de segurança. Restauro funciona da mesma forma.

Embora esta opção SOA simples e reencaminhar diretamente, existem algumas considerações. Tal como mencionado anteriormente, uma VM do Azure tem uma limitação do número de discos de dados que podem ser anexados. Poderá não ser capacidade para armazenar ficheiros de cópia de segurança de SAP HANA nos sistemas de ficheiros da VM, dependendo do tamanho dos requisitos de débito da base de dados e de disco, que poderão envolver software RAID utilizando striping em dados de vários discos. Várias opções para mover estes ficheiros de cópia de segurança e gerir as restrições de tamanho de ficheiro e o desempenho quando o processamento de terabytes de dados, são fornecidas posteriormente neste artigo.

Outra opção, o que oferece maior liberdade relativamente à capacidade total, é o armazenamento de Blobs do Azure. Enquanto um blob único também está limitado a 1 TB, a capacidade total de um contentor de blob único está atualmente 500 TB. Além disso, proporciona aos clientes a opção para selecionar so-called &quot;frios&quot; armazenamento de BLOBs, que tem uma vantagem de custo. Consulte [Blob Storage do Azure: frequente e esporádico camadas de armazenamento](../../../storage/blobs/storage-blob-storage-tiers.md) para obter detalhes sobre o armazenamento de BLOBs de acesso esporádico.

Para segurança adicional, utilize uma conta de armazenamento de georreplicação para armazenar as cópias de segurança de SAP HANA. Consulte [replicação de armazenamento do Azure](../../../storage/common/storage-redundancy.md) para obter detalhes sobre replicação de conta de armazenamento.

Uma conta de armazenamento de cópia de segurança dedicado que seja georreplicação um foi colocar VHDs dedicados para cópias de segurança de SAP HANA. Caso contrário, é um foi possível copiar os VHDs que manter as cópias de segurança de SAP HANA para uma conta de armazenamento de georreplicação ou para uma conta de armazenamento que está numa região diferente.

## <a name="azure-backup-agent"></a>Agente de cópia de segurança do Azure

Cópia de segurança do Azure oferece a opção de não apenas uma cópia de segurança completas VMs, mas também ficheiros e diretórios através do agente de cópia de segurança, que tem de ser instalado o SO convidado. Mas a partir de Dezembro de 2016, este agente só é suportado no Windows (consulte [cópia de segurança um Windows Server ou o cliente para o Azure utilizando o modelo de implementação Resource Manager](../../../backup/backup-configure-vault.md)).

É uma solução primeiro de copiar ficheiros de cópia de segurança de SAP HANA para uma VM do Windows no Azure (por exemplo, através de partilha SAMBA) e, em seguida, utilizar o agente de cópia de segurança do Azure a partir daí. Embora seja tecnicamente exequível,-seria adicionam complexidade e abrandar a cópia de segurança ou restaurar processo bastante um pouco devido a cópia entre o Linux e a VM do Windows. Não se recomenda a seguir esta abordagem.

## <a name="azure-blobxfer-utility-details"></a>Detalhes de utilitário blobxfer do Azure

Para armazenar os diretórios e ficheiros no armazenamento do Azure, um pode utilizar a CLI ou o PowerShell ou desenvolva uma ferramenta utilizando um do [Azure SDKs](https://azure.microsoft.com/downloads/). Existe também um utilitário de prontos a utilizar, o AzCopy, para copiar dados para armazenamento do Azure, mas é apenas Windows (consulte [transferir dados com o utilitário de linha de comandos do AzCopy](../../../storage/common/storage-use-azcopy.md)).

Por conseguinte blobxfer foi utilizado para copiar ficheiros de cópia de segurança de SAP HANA. -Lo é open source, utilizado por vários clientes em ambientes de produção e disponível em [GitHub](https://github.com/Azure/blobxfer). Esta ferramenta permite-uma copiar dados diretamente para o blob storage do Azure ou partilha de ficheiros do Azure. Também oferece uma gama de funcionalidades úteis, como md5 hash ou paralelismo automático quando copiar um diretório com vários ficheiros.

## <a name="sap-hana-backup-performance"></a>Desempenho de cópia de segurança de SAP HANA

![Nesta captura de ecrã é a consola de cópia de segurança de SAP HANA SAP HANA Studio](media/sap-hana-backup-file-level/image023.png)

É esta captura de ecrã da consola de cópia de segurança de SAP HANA no Studio do SAP HANA. Demorou cerca de 42 minutos para fazer a cópia de segurança do GB 230 num disco único armazenamento padrão do Azure ligado à VM com o sistema de ficheiros XFS HANA.

![Nesta captura de ecrã é de YaST na VM de teste de SAP HANA](media/sap-hana-backup-file-level/image024.png)

Nesta captura de ecrã é de YaST na VM de teste de SAP HANA. Um pode ver o disco único 1 TB para cópia de segurança de SAP HANA tal como mencionado anteriormente. Demorou cerca de 42 minutos a cópia de segurança GB 230. Além disso, cinco discos de 200 GB estivesse anexados e software RAID md0 criados com striping sobre estas cinco discos de dados do Azure.

![Repetir a cópia de segurança mesma software RAID com striping entre cinco ligado a discos de dados de armazenamento padrão do Azure](media/sap-hana-backup-file-level/image025.png)

Repetir a cópia de segurança mesma software RAID com striping entre cinco ligado discos de dados de armazenamento padrão do Azure colocados a hora da cópia de 42 minutos até 10 minutos. Os discos foram anexados sem colocação em cache para a VM. Para que fique óbvios débito de escrita de disco como importante destina-se a hora da cópia. Em seguida, um foi possível mudar para o armazenamento do Azure premium para obter mais acelerar o processo para um desempenho ideal. Em geral, o armazenamento do Azure premium deve ser utilizado para sistemas de produção.

## <a name="copy-sap-hana-backup-files-to-azure-blob-storage"></a>Copiar ficheiros de cópia de segurança de SAP HANA para o armazenamento de Blobs do Azure

A partir de Dezembro de 2016, a melhor opção para armazenar rapidamente os ficheiros de cópia de segurança de SAP HANA é blob storage do Azure. Um contentor de blob único tem um limite de 500 TB suficiente para a maioria dos sistemas de SAP HANA, em execução numa GS5 VM no Azure, para manter cópias de segurança de SAP HANA suficientes. Os clientes têm a opção entre &quot;frequente&quot; e &quot;amovíveis&quot; armazenamento de BLOBs (consulte [Blob Storage do Azure: frequente e esporádico camadas de armazenamento](../../../storage/blobs/storage-blob-storage-tiers.md)).

Com a ferramenta de blobxfer, é fácil copiar os ficheiros de cópia de segurança de SAP HANA diretamente ao armazenamento de Blobs do Azure.

![Aqui um pode ver os ficheiros de uma cópia de segurança de ficheiros de SAP HANA completa](media/sap-hana-backup-file-level/image026.png)

Aqui um pode ver os ficheiros de uma cópia de segurança de ficheiro completo do SAP HANA. Existem quatro ficheiros e dos maiores tem aproximadamente 230 GB.

![Demorou aproximadamente 3000 segundos para copiar os 230 GB para um contentor de blob de conta de armazenamento padrão do Azure](media/sap-hana-backup-file-level/image027.png)

Não a utilizar no teste inicial md5 hash, demorou aproximadamente 3000 segundos para copiar os 230 GB para um contentor de blob de conta de armazenamento padrão do Azure.

![Nesta captura de ecrã, um pode ver aspeto no portal do Azure](media/sap-hana-backup-file-level/image028.png)

Nesta captura de ecrã, um pode ver o aspeto no portal do Azure. Um contentor do blob denominado &quot;sap-hana-cópias de segurança&quot; foi criado e inclui os quatro blobs, que representam os ficheiros de cópia de segurança de SAP HANA. Um dos atributos tem um tamanho de aproximadamente 230 GB.

A consola de cópia de segurança HANA Studio permite um para restringir o tamanho de ficheiro máximo dos ficheiros de cópia de segurança HANA. No ambiente de exemplo, se um desempenho melhorado, tornando a possível ter vários ficheiros de cópia de segurança mais pequenos, em vez de um ficheiro grande de 230 GB.

![Definir o limite de tamanho de ficheiro de cópia de segurança no não de lado HANA &#39; t melhorar o tempo de cópia de segurança](media/sap-hana-backup-file-level/image029.png)

Definir o limite de tamanho de ficheiro de cópia de segurança no não de lado HANA &#39; t melhorar a hora da cópia de segurança, porque os ficheiros são escritos sequencialmente, como mostrado na figura que. O limite de tamanho de ficheiro foi definido para 60 GB, pelo que a cópia de segurança criada quatro ficheiros de dados de grandes dimensões em vez do ficheiro de único 230 GB.

![Para testar o paralelismo da ferramenta blobxfer, o tamanho de ficheiro máximo para cópias de segurança HANA, em seguida, foi definido como 15 GB](media/sap-hana-backup-file-level/image030.png)

Para testar o paralelismo da ferramenta blobxfer, o tamanho máximo de ficheiro de cópias de segurança HANA, em seguida, foi definido como 15 GB, o que resultou no 19 ficheiros de cópia de segurança. Esta configuração colocada a hora para blobxfer copiar os 230 GB para o armazenamento de Blobs do Azure de 3000 segundos até 875 segundos.

Este resultado é devido ao limite de 60 MB/seg de escrita de um blob do Azure. Paralelismo através de vários blobs resolve o congestionamento, mas não existe um downside: aumentar o desempenho da ferramenta blobxfer para copiar todos os estes HANA ficheiros de cópia para o armazenamento de Blobs do Azure coloca a carga numa HANA VM e da rede. A operação do sistema HANA passa a ser afetada.

## <a name="blob-copy-of-dedicated-azure-data-disks-in-backup-software-raid"></a>Cópia de BLOBs de discos de dados do Azure dedicada no RAID de software de cópia de segurança

Ao contrário da VM dados disco cópia de segurança manual, esta abordagem, um não fazer cópia de segurança todos os discos de dados numa VM para guardar a instalação de SAP completa, incluindo dados HANA HANA regista os ficheiros e ficheiros de configuração. Em vez disso, a ideia é ter dedicado RAID de software de com striping em dados do Azure vários VHDs para armazenar uma cópia de segurança de ficheiro completa do SAP HANA. Um copia apenas estes discos, com a cópia de segurança de SAP HANA. Estes foi facilmente ser mantidas uma conta de armazenamento de cópia de segurança HANA dedicada, ou ligados a um dedicado &quot;gestão VM de cópia de segurança&quot; para processamento adicional.

![Todos os VHDs envolvidos foram copiados com o * * start-azurestorageblobcopy * * comando do PowerShell](media/sap-hana-backup-file-level/image031.png)

Depois da cópia de segurança para o local RAID de software foi concluída, todos os VHDs envolvidos foram copiados com o **início azurestorageblobcopy** comando do PowerShell (consulte [início AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy)). Como afeta apenas o sistema de ficheiros dedicado para manter os ficheiros de cópia de segurança, não existem sem preocupações de consistência de ficheiro de registo ou dados SAP HANA no disco. Uma vantagem deste comando é que funciona enquanto a VM permanece online. Para ter a certeza de que nenhum processo escreve o conjunto de cópia de segurança do stripe, lembre-se de que desmonte-lo antes da cópia de BLOBs e montá-la novamente posteriormente. Ou um poderia utilizar uma forma adequada de &quot;fixar&quot; o sistema de ficheiros. Por exemplo, através de xfs\_fixar para o sistema de ficheiros XFS.

![Nesta captura de ecrã mostra a lista de blobs no contentor de vhds no portal do Azure](media/sap-hana-backup-file-level/image032.png)

Nesta captura de ecrã mostra a lista de blobs a &quot;vhds&quot; contentor no portal do Azure. A captura de ecrã mostra os cinco VHDs, que foram ligados ao servidor do SAP HANA VM para servir o software de RAID para manter os ficheiros de cópia de segurança de SAP HANA. Também mostra as cinco cópias, foram efetuadas através do comando de cópia de Blobs.

![Para fins de teste, as cópias dos discos RAID de software de cópia de segurança de SAP HANA estivesse anexadas a VM do servidor de aplicações](media/sap-hana-backup-file-level/image033.png)

Para fins de teste, as cópias dos discos RAID de software de cópia de segurança de SAP HANA estivesse anexadas a VM do servidor de aplicações.

![Copia o VM foi encerrada para anexar o disco do servidor de aplicações](media/sap-hana-backup-file-level/image034.png)

O servidor de aplicações VM foi encerrado anexar o disco copia. Depois de iniciar a VM, os discos e o RAID foram detetados corretamente (montado através do UUID). Apenas o ponto de montagem estava em falta, que foi criada através do particionador YaST. Seguidamente as cópias de ficheiro de cópia de segurança de SAP HANA deixou de estar visível no nível do SO.

## <a name="copy-sap-hana-backup-files-to-nfs-share"></a>Copie os ficheiros de cópia de segurança de SAP HANA partilha NFS

Para lessen o impacto potencial no sistema de SAP HANA de uma perspetiva de espaço em disco ou de desempenho, considere um poderá armazenar os ficheiros de cópia de segurança de SAP HANA numa partilha NFS. Tecnicamente funciona, mas significa utilizar um segundo VM do Azure como o anfitrião da partilha NFS. Não deve ser um tamanho VM pequeno, devido a largura de banda de rede VM. -Faria sentido, em seguida, para encerrar esta &quot;cópia de segurança de VM&quot; e colocá-lo apenas para executar a cópia de segurança de SAP HANA. Escrever em NFS partilha coloca a carga na rede e afeta o sistema de SAP HANA, mas apenas gerir, posteriormente, os ficheiros de cópia de segurança no &quot;cópia de segurança de VM&quot; não seria influenciar de todo o sistema de SAP HANA.

![Uma partilha NFS a partir de outra VM do Azure foi montada para o servidor de SAP HANA VM](media/sap-hana-backup-file-level/image035.png)

Para verificar que os NFS utilizar as maiúsculas e minúsculas, uma partilha NFS a partir de outra VM do Azure foi montada no servidor de SAP HANA VM. Não ocorreu nenhuma especial NFS otimização aplicado.

![Demorou 1 hora e 46 minutos a fazer a cópia de segurança diretamente](media/sap-hana-backup-file-level/image036.png)

A partilha NFS foi um conjunto de stripe rápida, como no servidor de SAP HANA. Contudo, demorou 1 hora e 46 minutos para fazer a cópia de segurança diretamente nas que partilha NFS, em vez de 10 minutos, quando definir a escrita faixa local.

![A alternativa não foi muito mais rápida em 1 hora e 43 minutos](media/sap-hana-backup-file-level/image037.png)

A alternativa de fazer uma cópia de segurança para um conjunto de local stripe e copiar para a partilha NFS incluída no nível do SO (simples **cp - avr** comandos) não foi muito mais rápida. Demorou 1 hora e 43 minutos.

Funciona, mas o desempenho não foi ideal para o teste de cópia de segurança 230 GB. Deverá ver mesmo um para vários terabytes.

## <a name="copy-sap-hana-backup-files-to-azure-file-service"></a>Copie os ficheiros de cópia de segurança de SAP HANA ao serviço de ficheiros do Azure

É possível montar uma partilha de ficheiros do Azure dentro de uma VM com Linux do Azure. O artigo [como utilizar o File storage do Azure com o Linux](../../../storage/files/storage-how-to-use-files-linux.md) fornece detalhes sobre como fazê-lo. Tenha em atenção que não há atualmente um limite de quota de 5 TB de uma partilha de ficheiros do Azure e um limite de tamanho de ficheiro de 1 TB por ficheiro. Consulte [metas de desempenho e escalabilidade do Storage do Azure](../../../storage/common/storage-scalability-targets.md) para obter informações sobre limites de armazenamento.

Testes demonstraram, no entanto, que não de cópia de segurança de SAP HANA &#39; t trabalhar diretamente com este tipo de montagem CIFS. Também é declarado no [1820529 de nota SAP](https://launchpad.support.sap.com/#/notes/1820529) que CIFS não é recomendada.

![Esta ilustração mostra um erro na caixa de diálogo de cópia de segurança no SAP HANA Studio](media/sap-hana-backup-file-level/image038.png)

Esta ilustração mostra um erro na caixa de diálogo de cópia de segurança no SAP HANA Studio, ao tentar efetuar cópias de segurança diretamente a uma partilha de ficheiros do Azure CIFS montada. Para um tem uma cópia de segurança de SAP HANA padrão para um sistema de ficheiros VM primeiro e, em seguida, copie os ficheiros de cópia de segurança a partir daí ao serviço de ficheiros do Azure.

![Esta ilustração mostra demorou cerca de 929 segundos para copiar ficheiros de cópia de segurança de SAP HANA 19](media/sap-hana-backup-file-level/image039.png)

Esta ilustração mostra demorou cerca de 929 segundos para copiar 19 ficheiros de cópia de segurança de SAP HANA com um tamanho total de aproximadamente 230 GB para a partilha de ficheiros do Azure.

![A estrutura de diretório de origem na SAP HANA VM foi copiada para a partilha de ficheiros do Azure](media/sap-hana-backup-file-level/image040.png)

Nesta captura de ecrã, um pode ver que a estrutura de diretório de origem na SAP HANA VM foi copiada para a partilha de ficheiros do Azure: um diretório (hana\_cópia de segurança\_fsl\_15 gb) e 19 ficheiros de cópia de segurança individuais.

Armazenamento de ficheiros de cópia de segurança de SAP HANA nos ficheiros do Azure pode ser uma opção interessante no futuro quando cópias de segurança de ficheiros de SAP HANA suportam diretamente. Ou quando torna possível para ficheiros de montagem Azure através de NFS e o limite de quota máxima é consideravelmente maior 5 TB.

## <a name="next-steps"></a>Passos seguintes
* [Cópia de segurança manual para SAP HANA em Azure Virtual Machines](sap-hana-backup-guide.md) fornece uma descrição geral e informações sobre começar a utilizar.
* [Cópia de segurança de SAP HANA com base nos instantâneos de armazenamento](sap-hana-backup-storage-snapshots.md) descreve a opção de cópia de segurança do instantâneo com base em armazenamento.
* Para saber como estabelecer elevada disponibilidade e o plano de recuperação de desastres do SAP HANA no Azure (instâncias de grande), consulte o artigo [SAP HANA (instâncias de grande) elevada disponibilidade e recuperação após desastre no Azure](hana-overview-high-availability-disaster-recovery.md).
