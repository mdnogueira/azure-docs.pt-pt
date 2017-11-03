---
title: "Cópia de segurança manual para SAP HANA em Azure Virtual Machines | Microsoft Docs"
description: "Guia de cópia de segurança para SAP HANA fornece duas principais possibilidades de cópia de segurança para SAP HANA em máquinas virtuais do Azure"
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
ms.openlocfilehash: 9e5b124643b753f404ba6012d3df998f567be59a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Guia de segurança para SAP HANA em Máquinas Virtuais do Azure

## <a name="getting-started"></a>Introdução

O guia de cópia de segurança para SAP HANA em execução em máquinas virtuais do Azure apenas descrevem tópicos específicos do Azure. Para gerais SAP HANA cópia de segurança itens relacionados, consulte a documentação de SAP HANA (consulte _documentação de cópia de segurança de SAP HANA_ posteriormente neste artigo).

O foco deste artigo é dois principais cópia de segurança as possibilidades de SAP HANA em máquinas virtuais do Azure:

- Cópia de segurança HANA ao sistema de ficheiros numa máquina de Virtual do Linux do Azure (consulte [SAP HANA Backup do Azure no nível de ficheiro](sap-hana-backup-file-level.md))
- Cópia de segurança HANA com base no instantâneos de armazenamento utilizando a funcionalidade de instantâneos do blob storage do Azure manualmente ou o serviço de cópia de segurança do Azure (consulte [cópia de segurança de SAP HANA com base nos instantâneos de armazenamento](sap-hana-backup-storage-snapshots.md))

SAP HANA oferece uma cópia de segurança API, o que permite as ferramentas de cópia de segurança de terceiros integrar diretamente com o SAP HANA. (Que não está dentro do âmbito deste guia.) Não há nenhum integração direta de SAP HANA com o serviço de cópia de segurança do Azure direito disponível com base nesta API.

SAP HANA é oficialmente suportado no tipo de VM do Azure GS5 como única instância com uma restrição adicional para cargas de trabalho do OLAP (consulte [localizar plataformas IaaS certificadas](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html) no Web site do SAP). Este artigo será atualizado como novas ofertas de SAP HANA no Azure fique disponível.

Há também uma solução híbrida de SAP HANA disponíveis no Azure, onde SAP HANA é executada não virtualizados em servidores físicos. No entanto, este guia de cópia de segurança de SAP HANA Azure inclui um ambiente do Azure puro onde SAP HANA é executado numa VM do Azure, não SAP HANA em execução no &quot;instâncias grandes.&quot; Consulte [descrição geral de SAP HANA (instâncias de grandes dimensões) e arquitetura no Azure](hana-overview-architecture.md) para obter mais informações sobre esta solução de cópia de segurança no &quot;instâncias grande&quot; com base nos instantâneos de armazenamento.

Informações gerais sobre os produtos SAP suportadas no Azure podem ser encontradas no [1928533 de nota SAP](https://launchpad.support.sap.com/#/notes/1928533).

As seguintes três figuras dar uma descrição geral das opções de cópia de segurança de SAP HANA atualmente a utilizar as capacidades do Azure nativas e também mostram três cenários de cópia de segurança futuros potenciais. Os artigos relacionados [SAP HANA Backup do Azure no nível de ficheiro](sap-hana-backup-file-level.md) e [cópia de segurança de SAP HANA com base nos instantâneos de armazenamento](sap-hana-backup-storage-snapshots.md) descrevem estas opções em mais detalhe, incluindo as considerações de desempenho e de tamanho para SAP HANA cópias de segurança que são várias-terabytes de tamanho.

![Esta ilustração mostra duas possibilidades para guardar o estado atual do VM](media/sap-hana-backup-guide/image001.png)

Esta ilustração mostra a possibilidade de guardar o estado atual do VM, quer através do serviço de cópia de segurança do Azure ou manual instantâneos de discos VM. Com esta abordagem, um &#39; t tem de gerir cópias de segurança de SAP HANA. O desafio do cenário de instantâneo de disco é a consistência do sistema de ficheiros e um Estado de disco com consistência de aplicações. O tópico de consistência é mencionado na secção _consistência de dados SAP HANA quando tirar instantâneos de armazenamento_ posteriormente neste artigo. Funcionalidades e restrições do serviço de cópia de segurança do Azure relacionados com as cópias de segurança de SAP HANA também são abordadas neste artigo.

![Esta ilustração mostra as opções para colocar um SAP HANA ficheiro de cópia de segurança dentro da VM](media/sap-hana-backup-guide/image002.png)

Esta ilustração mostra as opções para colocar uma cópia de segurança de ficheiros de SAP HANA dentro da VM e, em seguida, armazenar ficheiros de cópia de segurança HANA algures senão utilizando ferramentas diferentes. Fazer uma cópia de segurança HANA necessita de mais tempo do que uma solução de cópia de segurança baseada em instantâneo, mas tem vantagens relativamente a integridade e consistência. Obter mais detalhes são fornecidos neste artigo.

![Este figura mostra um potencial futuro SAP HANA cópia de segurança cenário](media/sap-hana-backup-guide/image003.png)

Este figura mostra um potencial futuro SAP HANA cópia de segurança cenário. Se SAP HANA permitido fazer cópias de segurança de uma replicação secundária, deverá adicionar a opções adicionais para estratégias de cópia de segurança. Atualmente, não é possível, de acordo com um post na SAP HANA Wiki:

_&quot;É possível efetuar cópias de segurança no lado secundário?_

_Não, atualmente pode apenas colocar dados e iniciar as cópias de segurança no lado primário. Se estiver ativada a cópia de segurança automática de registos, depois de aquisições para o lado secundário, as cópias de segurança do registo automaticamente serão escritas não existe.&quot;_

## <a name="sap-resources-for-hana-backup"></a>Recursos SAP para cópia de segurança HANA

### <a name="sap-hana-backup-documentation"></a>Documentação de cópia de segurança de SAP HANA

- [Introdução ao SAP HANA administração](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [Planear a sua estratégia de recuperação e cópia de segurança](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [Agendar cópia de segurança HANA utilizando ABAP DBACOCKPIT](http://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Cópias de segurança de dados agendadas (SAP HANA Cockpit)](http://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- FAQ sobre SAP HANA de cópia de segurança no [1642148 de nota SAP](https://launchpad.support.sap.com/#/notes/1642148)
- FAQ sobre instantâneos de base de dados e armazenamento de SAP HANA no [2039883 de nota SAP](https://launchpad.support.sap.com/#/notes/2039883)
- Sistemas de ficheiros de rede unsuitable para cópia de segurança e recuperação no [1820529 de nota SAP](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="why-sap-hana-backup"></a>Por que motivo SAP cópia de segurança HANA?

Storage do Azure oferece a disponibilidade e fiabilidade a box (consulte [introdução ao Storage do Microsoft Azure](../../../storage/common/storage-introduction.md) para obter mais informações sobre o storage do Azure).

O mínimo para &quot;cópia de segurança&quot; é a depender do SLA do Azure, mantendo os ficheiros de dados e de registo de SAP HANA em VHDs de Azure ligado ao servidor do SAP HANA VM. Esta abordagem abrange falhas VM, mas não potenciais danos de dados SAP HANA e ficheiros de registo ou erros lógicos, como a eliminação de dados ou ficheiros acidentalmente. As cópias de segurança também são necessárias para compatibilidade ou motivos legais. Em suma, não há sempre uma necessidade para cópias de segurança de SAP HANA.

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>Como verificar a correcção da cópia de segurança de SAP HANA
Quando é recomendado utilizar instantâneos de armazenamento, executar um restauro de teste num sistema diferente. Esta abordagem fornece uma forma de garantir que uma cópia de segurança está corretos e internos processos para cópia de segurança e restaurar o trabalho conforme esperado. Apesar de ser um impacto significativo hurdle no local, é muito mais fácil realizar na nuvem, fornecendo temporariamente os recursos necessários para esta finalidade.

Tenha em atenção que efetuar um restauro simple e a verificar se HANA está ativo e em execução não são suficiente. Idealmente, um deve executar uma verificação de consistência da tabela para Certifique-se de que a base de dados restaurada é adequado. SAP HANA oferece vários tipos de verificações de consistência descritos [1977584 de nota SAP](https://launchpad.support.sap.com/#/notes/1977584).

Também podem encontrar informações sobre a verificação de consistência da tabela no Web site do SAP em [tabela e verificações de consistência do catálogo](http://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b).

Para cópias de segurança de ficheiros padrão, um restauro de teste não é necessário. Existem duas ferramentas de SAP HANA que ajudam a verificar a cópia de segurança, que pode ser utilizada para restauro: hdbbackupdiag e hdbbackupcheck. Consulte [manualmente a verificar se a recuperação é possível](https://help.sap.com/saphelp_hanaplatform/helpdata/en/77/522ef1e3cb4d799bab33e0aeb9c93b/content.htm) para obter mais informações sobre estas ferramentas.

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>Os profissionais de TI e contras de cópia de segurança HANA versus instantâneos de armazenamento

SAP &#39; preferência de conceder t para a cópia de segurança HANA versus instantâneos de armazenamento. Lista os respetivos os profissionais de TI e contras, para que um pode determinar que utilizar consoante a situação e a tecnologia de armazenamento disponível (consulte [planear a cópia de segurança e a estratégia de recuperação](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)).

No Azure, tenha em consideração o facto de que o blob do Azure de instantâneos funcionalidade &#39; t garantir a consistência do sistema de ficheiros (consulte [utilizar instantâneos de blob com o PowerShell](https://blogs.msdn.microsoft.com/cie/2016/05/17/using-blob-snapshots-with-powershell/)). A secção seguinte, _consistência de dados SAP HANA quando tirar instantâneos de armazenamento_, descreve algumas considerações relativas a esta funcionalidade.

Além disso, um tem de compreender as implicações de faturação ao trabalhar com frequência com instantâneos do blob, conforme descrito neste artigo: [compreender como instantâneos acumular encargos](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)— se encontra &#39; t como óbvios como utilizar o Azure virtual discos.

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>Consistência de dados SAP HANA quando tirar instantâneos de armazenamento

Consistência de aplicações e de sistema de ficheiros é um problema complexo quando tirar instantâneos de armazenamento. A forma mais fácil para evitar problemas seria encerrar SAP HANA ou, talvez até a máquina virtual completa. Um encerramento pode ser doable com uma demonstração ou criar protótipos ou até mesmo um sistema de desenvolvimento, mas não é uma opção para um sistema de produção.

No Azure, um tem tenha em atenção que o blob do Azure de instantâneos funcionalidade &#39; consistência do sistema de ficheiros de garantia de t. Ajustar funciona no entanto, utilizando o SAP HANA instantâneo funcionalidade, desde que não há um único disco virtual envolvidos. Mas, mesmo com um único disco itens adicionais têm de ser verificado. [SAP nota 2039883](https://launchpad.support.sap.com/#/notes/2039883) tem informações importantes sobre as cópias de segurança de SAP HANA através de instantâneos de armazenamento. Por exemplo, menciona que, com o sistema de ficheiros XFS, é necessário para executar **xfs\_fixar** antes de iniciar um instantâneo de armazenamento para garantir a consistência (consulte [xfs\_freeze(8) - página de man do Linux ](https://linux.die.net/man/8/xfs_freeze) para obter detalhes sobre **xfs\_fixar**).

O tópico de consistência fica ainda mais difícil caso em que um sistema de ficheiro único abranja vários discos/volumes. Por exemplo, ao utilizar mdadm ou LVM e striping. A nota SAP mencionado Estados:

_&quot;Mas tenha em atenção que o sistema de armazenamento tem de garantir a consistência de e/s ao criar um instantâneo de armazenamento por volume de dados SAP HANA, ou seja, tem de ser uma operação atómica snapshotting de um volume de dados específicos do serviço de SAP HANA.&quot;_

Pressupondo que há um sistema de ficheiros XFS expansão quatro discos virtuais do Azure, os passos seguintes fornecem um instantâneo consistente que representa a área de dados HANA:

- Preparar o instantâneo HANA
- Fixar o sistema de ficheiros (por exemplo, utilizar **xfs\_fixar**)
- Criar todos os instantâneos do blob necessários no Azure
- Libertar poder pelo sistema de ficheiros
- Confirme o instantâneo HANA

Recomenda-se utilizar o procedimento acima em todos os casos, seja no lado seguro, independentemente do sistema de ficheiros. Ou, se se tratar de um único disco ou striping, através de mdadm ou LVM entre múltiplos discos.

É importante confirmar se o instantâneo HANA. Devido ao &quot;cópia ao escrever,&quot; SAP HANA poderão não requerer o espaço em disco adicional nisto preparar instantâneo modo. &#39; s também não é possível iniciar novas cópias de segurança até é confirmado o instantâneo de SAP HANA.

Serviço de cópia de segurança do Azure utiliza extensões de VM do Azure para asseguramos a consistência do sistema de ficheiros. Estas extensões VM não estão disponíveis para utilização autónoma. Um ainda, tem de gerir a consistência de SAP HANA. Consulte o artigo relacionado [SAP HANA Azure Backup no nível de ficheiro](sap-hana-backup-file-level.md) para obter mais informações.

### <a name="sap-hana-backup-scheduling-strategy"></a>Estratégia de agendamento de cópia de segurança de SAP HANA

O artigo de SAP HANA [planear a cópia de segurança e a estratégia de recuperação](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) Estados de um plano básico para fazer cópias de segurança:

- Armazenamento de instantâneos (diário)
- Cópia de segurança de dados completo com o formato de ficheiro ou bacint (uma vez por semana)
- Cópias de segurança do registo automática

Opcionalmente, um foi possível ir completamente sem instantâneos de armazenamento pode ser substituídos HANA diferenças das cópias de segurança, como as cópias de segurança incrementais ou diferenciais (consulte [cópias de segurança diferencial](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm)).

O guia de administração de HANA fornece uma lista de exemplo. -Sugere uma recuperar SAP HANA para um ponto específico no tempo, utilizando a seguinte sequência das cópias de segurança:

1. Cópia de segurança de dados completa
2. Cópia de segurança diferencial
3. Cópia de segurança incremental 1
4. Cópia de segurança incremental 2
5. Cópias de segurança do registo

Relativamente a um agendamento exato relativamente a quando e com que frequência deverá ocorrer um tipo específico de cópia de segurança, não é possível dar orientação geral — é demasiado específicas do cliente e depende ocorrem quantos alterações de dados no sistema. Uma recomendação básica do lado do SAP, que pode ser visto como orientação geral, é, para disponibilizar um HANA cópia de segurança completa, uma vez por semana.
Sobre cópias de segurança de registo, consulte a documentação de SAP HANA [cópias de segurança do registo](https://help.sap.com/saphelp_hanaplatform/helpdata/en/c3/bb7e33bb571014a03eeabba4e37541/content.htm).

SAP recomenda também fazer alguma manutenção do catálogo de cópias de segurança para impedi-la de crescer infinitamente (consulte [Housekeeping de catálogo de cópias de segurança e de armazenamento de cópia de segurança](http://help.sap.com/saphelp_hanaplatform/helpdata/en/ca/c903c28b0e4301b39814ef41dbf568/content.htm)).

### <a name="sap-hana-configuration-files"></a>Ficheiros de configuração de SAP HANA

Conforme indicado na FAQ no [1642148 de nota SAP](https://launchpad.support.sap.com/#/notes/1642148), os ficheiros de configuração de SAP HANA não fazem parte de uma cópia de segurança HANA padrão. Não são essenciais para restaurar um sistema. A configuração de HANA foi possível alterar manualmente após o restauro. No caso de um gostaria de obter a mesma configuração personalizada durante o processo de restauro, é necessário fazer uma cópia de segurança de ficheiros de configuração HANA separadamente.

Standard HANA as cópias de segurança se pretender um sistema de ficheiros de cópia de segurança de HANA dedicado, um foi também copia os ficheiros de configuração para o mesmo sistema de ficheiros de cópia de segurança e, em seguida, copie tudo em conjunto para o destino final de armazenamento como útil armazenamento de Blobs.

### <a name="sap-hana-cockpit"></a>SAP HANA Cockpit

SAP HANA Cockpit oferece a possibilidade de monitorização e gestão de SAP HANA através de um browser. Também permite que o processamento de cópias de segurança de SAP HANA e, por conseguinte, podem ser utilizado como uma alternativa ao SAP HANA Studio e ABAP DBACOCKPIT (consulte [SAP HANA Cockpit](https://help.sap.com/saphelp_hanaplatform/helpdata/en/73/c37822444344f3973e0e976b77958e/content.htm) para obter mais informações).

![Esta ilustração mostra o ecrã de administração do SAP HANA Cockpit da base de dados](media/sap-hana-backup-guide/image004.png)

Esta ilustração mostra o ecrã de administração do SAP HANA Cockpit da base de dados e o mosaico de cópia de segurança no lado esquerdo. Ver o mosaico de cópia de segurança necessita de permissões de utilizador adequada para a conta de início de sessão.

![As cópias de segurança podem ser monitorizadas no SAP HANA Cockpit enquanto estiverem em curso](media/sap-hana-backup-guide/image005.png)

As cópias de segurança podem ser monitorizadas no SAP HANA Cockpit enquanto estiverem em curso e, assim que estiver concluído, todos os detalhes de cópia de segurança estão disponíveis.

![Um exemplo utilizando o Firefox numa VM do Azure SLES 12 com ambiente de trabalho Gnome](media/sap-hana-backup-guide/image006.png)

Foram efetuadas as capturas de ecrã anteriores de uma VM do Windows Azure. Este é um exemplo utilizando o Firefox numa VM do Azure SLES 12 com ambiente de trabalho Gnome. Mostra a opção de definir agendas de cópia de segurança de SAP HANA Cockpit do SAP HANA. Como um também pode ver, sugere data/hora como um prefixo para os ficheiros de cópia de segurança. No SAP HANA Studio, o prefixo de predefinição está &quot;concluída\_dados\_cópia de segurança&quot; ao efetuar uma cópia de segurança completa. É recomendado utilizar um prefixo exclusivo.

### <a name="sap-hana-backup-encryption"></a>Encriptação de cópias de segurança de SAP HANA

SAP HANA oferece a encriptação de dados e de registo. Se os dados de SAP HANA e de registo não estão encriptados, em seguida, as cópias de segurança também não estão encriptadas. Trata-se até o cliente para utilizar alguma forma de solução de terceiros para encriptar as cópias de segurança de SAP HANA. Consulte [dados e encriptação de Volume de registo](https://help.sap.com/saphelp_hanaplatform/helpdata/en/dc/01f36fbb5710148b668201a6e95cf2/content.htm) para saber mais sobre a encriptação de SAP HANA.

No Microsoft Azure, um cliente utilizar a funcionalidade de encriptação da VM do IaaS para encriptar. Por exemplo, um utilizar discos de dados dedicado ligados à VM, que são utilizados para armazenar cópias de segurança de SAP HANA, em seguida, faça cópias destes discos.

Serviço de cópia de segurança do Azure pode processar VMs/discos encriptados (consulte [como fazer cópias de segurança e restaurar encriptados máquinas virtuais com o Backup do Azure](../../../backup/backup-azure-vms-encryption.md)).

Outra opção seria manter a VM do SAP HANA e respetivos discos sem encriptação e armazenar os ficheiros de cópia de segurança de SAP HANA numa conta do storage para o qual foi ativada a encriptação (consulte [encriptação do serviço de armazenamento do Azure para dados Inativos](../../../storage/common/storage-service-encryption.md)).

## <a name="test-setup"></a>Configuração de teste

### <a name="test-virtual-machine-on-azure"></a>Máquina Virtual de teste no Azure

Uma instalação de SAP HANA na VM do Azure GS5 foi utilizada para os seguintes testes de cópia de segurança/restauro.

![Esta ilustração mostra a parte da descrição geral portal do Azure para o teste HANA VM](media/sap-hana-backup-guide/image007.png)

Esta ilustração mostra a parte da descrição geral portal do Azure para a VM de teste HANA.

### <a name="test-backup-size"></a>Tamanho de cópia de segurança de teste

![Este figura foi obtida a partir da consola de cópia de segurança no HANA Studio e mostra o tamanho do ficheiro de cópia de segurança de 229 GB para o servidor de índice HANA](media/sap-hana-backup-guide/image008.png)

Uma tabela fictício foi preenchida cópias de segurança com a dados para obter um tamanho de cópia de segurança do total de dados de mais de 200 GB para os dados de desempenho realistas. A figura foi obtida a partir da consola de cópia de segurança no HANA Studio e mostra o tamanho do ficheiro de cópia de segurança de 229 GB para o servidor de índice HANA. Para os testes, utilizou-se o prefixo de cópia de segurança predefinido "COMPLETE_DATA_BACKUP" no Studio do SAP HANA. Nos sistemas de produção real, deve ser definido um prefixo mais útil. SAP HANA Cockpit sugere data/hora.

### <a name="test-tool-to-copy-files-directly-to-azure-storage"></a>Testar a ferramenta para copiar ficheiros diretamente ao armazenamento do Azure

Para transferir ficheiros de cópia de segurança de SAP HANA diretamente ao armazenamento de Blobs do Azure ou partilhas de ficheiros do Azure, a ferramenta de blobxfer foi utilizada uma vez que suporta a ambos os destinos e podem ser facilmente integrados em scripts de automatização devido à respetiva interface de linha de comandos. A ferramenta de blobxfer está disponível no [GitHub](https://github.com/Azure/blobxfer).

### <a name="test-backup-size-estimation"></a>Estimativa do tamanho de cópia de segurança de teste

É importante estimar o tamanho da cópia de segurança de SAP HANA. Esta estimativa ajuda a melhorar o desempenho ao definir o tamanho do ficheiro de cópia de segurança máximo para um número de ficheiros de cópia de segurança, devido a paralelismo durante uma cópia de ficheiros. (Os detalhes são explicados posteriormente neste artigo.) Um também deverá decidir se pretende fazer uma cópia de segurança completa ou uma cópia de segurança diferencial (incremental ou diferencial).

Felizmente, há uma instrução SQL simple que calcula o tamanho dos ficheiros de cópia de segurança: **selecione \* do M\_cópia de segurança\_tamanho\_estimativas** (consulte [estimativa o espaço necessário no sistema de ficheiros para uma cópia de segurança de dados](https://help.sap.com/saphelp_hanaplatform/helpdata/en/7d/46337b7a9c4c708d965b65bc0f343c/content.htm)).

![O resultado da declaração SQL corresponde quase exatamente o tamanho real da cópia de segurança completa de dados no disco](media/sap-hana-backup-guide/image009.png)

Para o sistema de teste, o resultado da declaração SQL corresponde quase exatamente o tamanho real da cópia de segurança completa de dados no disco.

### <a name="test-hana-backup-file-size"></a>Tamanho do ficheiro de cópia de segurança de HANA de teste

![A consola de cópia de segurança HANA Studio permite um para restringir o tamanho de ficheiro máximo dos ficheiros de cópia de segurança HANA](media/sap-hana-backup-guide/image010.png)

A consola de cópia de segurança HANA Studio permite um para restringir o tamanho de ficheiro máximo dos ficheiros de cópia de segurança HANA. No ambiente de exemplo, essa funcionalidade torna possível a obter vários ficheiros mais pequenos de cópia de segurança em vez de um ficheiro de cópia de segurança de 230 GB. Ficheiro mais pequeno tem um impacto significativo no desempenho (consulte o artigo relacionado [SAP HANA Azure Backup no nível de ficheiro](sap-hana-backup-file-level.md)).

## <a name="summary"></a>Resumo

Com base nos resultados de teste, que as tabelas seguintes mostram os profissionais de TI e contras de soluções de cópia de segurança de uma base de dados SAP HANA em execução em máquinas virtuais do Azure.

**Cópia de segurança SAP HANA ao sistema de ficheiros e copiar os ficheiros de cópia de segurança, posteriormente, para o destino de cópia de segurança final**

|Solução                                           |Profissionais de TI                                 |Contras                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Manter cópias de segurança HANA em discos VM                      |Sem esforços de gestão adicional     |Eats local espaço em disco VM           |
|Ferramenta de Blobxfer para copiar ficheiros de cópia de segurança para armazenamento de BLOBs |Paralelismo para copiar vários ficheiros, escolha para utilizar o armazenamento de BLOBs esporádico | Manutenção de ferramenta adicionais e de script personalizado | 
|Cópia de BLOBs através do Powershell ou a CLI                    |Nenhuma ferramenta adicional necessária, pode ser efetuada através do Azure Powershell ou a CLI |processo manual, o cliente tem a asseguramos scripting e a gestão de blobs copiados do restauro|
|Copiar para a partilha NFS                                  |Pós-processamento de ficheiros de cópia de segurança em outro VM sem impacto no servidor de HANA|Processo de cópia lenta|
|Blobxfer copiar para o serviço de ficheiros do Azure                |Não eat espaço no locais discos VM|Não existem direta escrever suporte por restrição de cópia de segurança, tamanho HANA da partilha de ficheiros atualmente em 5 TB|
|Agente de cópia de segurança do Azure                                 | Seria solução preferencial         | Atualmente não está disponível no Linux    |



**Cópia de segurança SAP HANA com base nos instantâneos de armazenamento**

|Solução                                           |Profissionais de TI                                 |Contras                                  |
|---------------------------------------------------|-------------------------------------|--------------------------------------|
|Serviço de cópia de segurança do Azure                               | Permite a cópia de segurança VM com base nos instantâneos do blob | Quando não utilizar o restauro ao nível do ficheiro, requer a criação de uma nova VM para o processo de restauro, em seguida, implica a necessidade de uma nova chave de licença de SAP HANA|
|Instantâneos do manual blob                              | Flexibilidade para criar e restaurar os discos VM específicos sem alterar o ID exclusivo da VM|Todas as tarefas manuais, tem de ser efetuada pelo cliente|

## <a name="next-steps"></a>Passos seguintes
* [SAP HANA Backup do Azure no nível de ficheiro](sap-hana-backup-file-level.md) descreve a opção de cópia de segurança baseada em ficheiros.
* [Cópia de segurança de SAP HANA com base nos instantâneos de armazenamento](sap-hana-backup-storage-snapshots.md) descreve a opção de cópia de segurança do instantâneo com base em armazenamento.
* Para saber como estabelecer elevada disponibilidade e o plano de recuperação de desastres do SAP HANA no Azure (instâncias de grande), consulte o artigo [SAP HANA (instâncias de grande) elevada disponibilidade e recuperação após desastre no Azure](hana-overview-high-availability-disaster-recovery.md).
