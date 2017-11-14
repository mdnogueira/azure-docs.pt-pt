---
title: "Planear uma implementação de sincronização de ficheiros do Azure (pré-visualização) | Microsoft Docs"
description: "Saiba o que deve considerar quando planear uma implementação de ficheiros do Azure."
services: storage
documentationcenter: 
author: wmgries
manager: klaasl
editor: jgerend
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2017
ms.author: wgries
ms.openlocfilehash: 241b744f5c5e89f53addb4d41d732245d76ef9a3
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2017
---
# <a name="planning-for-an-azure-file-sync-preview-deployment"></a>Planear uma implementação de sincronização de ficheiros do Azure (pré-visualização)
Utilize sincronização de ficheiros do Azure (pré-visualização) para centralizar o processamento de partilhas de ficheiros da sua organização nos ficheiros do Azure, mantendo o flexibilidade, o desempenho e a compatibilidade de um servidor de ficheiros no local. Sincronização de ficheiros do Azure transforma do Windows Server para uma cache rápida da Azure da partilha de ficheiros. Pode utilizar qualquer protocolo de que está disponível no Windows Server para aceder aos seus dados localmente, incluindo SMB, NFS e FTPS. Pode ter caches tantos conforme necessário por todo o mundo.

Este artigo descreve considerações importantes sobre uma implementação de sincronização de ficheiros do Azure. Recomendamos que leia também [planear uma implementação de ficheiros do Azure](storage-files-planning.md). 

## <a name="azure-file-sync-terminology"></a>Terminologia de sincronização de ficheiros do Azure
Antes de obter para os detalhes do planeamento de uma implementação de sincronização de ficheiros do Azure, é importante perceber a terminologia.

### <a name="storage-sync-service"></a>Serviço de sincronização de armazenamento
O serviço de sincronização de armazenamento é o recurso mais superior do Azure para a sincronização de ficheiros do Azure. O recurso de serviço de sincronização de armazenamento é um elemento de rede do recurso de conta de armazenamento e da mesma forma pode ser implementado em grupos de recursos do Azure. Um recurso de nível superior distinto do recurso de conta de armazenamento é necessário porque o serviço de sincronização de armazenamento pode criar relações de sincronização com várias contas do storage através de vários grupos de sincronização. Uma subscrição pode ter vários recursos de armazenamento sincronização serviço implementados.

### <a name="sync-group"></a>Grupo de sincronização
Um grupo de sincronização define a topologia de sincronização para um conjunto de ficheiros. Pontos finais dentro de um grupo de sincronização são mantidos sincronizados entre si. Se, por exemplo, tiver dois conjuntos diferentes de ficheiros que pretende gerir com sincronização de ficheiros do Azure, iria criar dois grupos de sincronização e adicionar pontos finais diferentes para cada grupo de sincronização. Um serviço de sincronização de armazenamento pode alojar tantos de sincronização de grupos, conforme necessário.  

### <a name="registered-server"></a>Servidor registado
O objeto de servidor registado representa uma relação de confiança entre o servidor (ou o cluster) e o serviço de sincronização de armazenamento. Pode registar o número de servidores a uma instância de serviço de sincronização de armazenamento que pretender. No entanto, servidor (ou num cluster) pode ser registado com o serviço de sincronização apenas um armazenamento de cada vez.

### <a name="azure-file-sync-agent"></a>Agente de sincronização de ficheiros do Azure
O agente de sincronização de ficheiros do Azure é um pacote transferível, que permite que o Windows Server ser sincronizados com uma partilha de ficheiros do Azure. O agente de sincronização de ficheiros do Azure tem três componentes principais: 
- **FileSyncSvc.exe**: O serviço do Windows que é responsável para monitorização de alterações em pontos finais do servidor e para iniciar sessões de sincronização para o Azure em segundo plano.
- **StorageSync.sys**: A sincronização de ficheiros do Azure ficheiro filtro do sistema, que é responsável por camadas ficheiros para ficheiros do Azure (nuvem quando camadas está ativado).
- **Cmdlets de gestão do PowerShell**: cmdlets do PowerShell que utilizar para interagir com o fornecedor de recursos do Microsoft.StorageSync Azure. Pode encontrar estas nas seguintes localizações (predefinição):
    - C:\Programas\Microsoft Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Programas\Microsoft Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Ponto final do servidor
Um ponto final do servidor representa uma localização específica no servidor registado, tais como uma pasta no volume do servidor ou na raiz do volume. Vários pontos finais de servidor pode existir no mesmo volume se os espaços de nomes não se sobrepuserem (por exemplo, F:\sync1 e F:\sync2). Pode configurar políticas de camadas na nuvem individualmente para cada ponto final do servidor. Se adicionar uma localização do servidor que tenha um conjunto de ficheiros como um ponto final do servidor a um grupo de sincronização, esses ficheiros são intercalados com outros ficheiros que já estejam nesse outros pontos finais no grupo de sincronização.

### <a name="cloud-endpoint"></a>Ponto final da nuvem
Um ponto final da nuvem é uma partilha de ficheiros do Azure que faz parte de um grupo de sincronização. As sincronizações de partilha de ficheiros do Azure completa e uma partilha de ficheiros do Azure podem ser um membro de apenas um ponto final da nuvem. Por conseguinte, uma partilha de ficheiros do Azure pode ser um membro de apenas um grupo de sincronização. Se adicionar uma partilha de ficheiros do Azure que tenha um conjunto de ficheiros como um ponto final da nuvem para um grupo de sincronização, os ficheiros existentes são intercalados com outros ficheiros que já estejam nesse outros pontos finais no grupo de sincronização.

> [!Important]  
> Sincronização de ficheiros do Azure suporta diretamente a efetuar alterações à partilha de ficheiros do Azure. No entanto, quaisquer alterações efetuadas na partilha de ficheiros do Azure primeiro tem de ser detetados por uma tarefa de deteção de alteração de sincronização de ficheiros do Azure. Uma tarefa de deteção de alteração é iniciada, para um ponto final da nuvem, apenas uma vez a cada 24 horas. Para obter mais informações, consulte [ficheiros do Azure perguntas mais frequentes](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Disposição em camadas na cloud 
Nuvem camadas é uma funcionalidade opcional de sincronização de ficheiros do Azure na qual raramente utilizados ou acedidos ficheiros podem ser colocado em camadas para ficheiros do Azure. Quando um ficheiro está em camadas, o filtro de sistema de ficheiros de sincronização de ficheiros do Azure (StorageSync.sys) substitui o ficheiro localmente com um ponteiro, ou um ponto de reanálise. O ponto de reanálise representa um URL para o ficheiro nos ficheiros do Azure. Um ficheiro em camadas tem o atributo "offline" definido no NTFS, para que aplicações de terceiros podem identificar os ficheiros em camadas. Quando um utilizador abre um ficheiro em camadas, sincronização de ficheiros do Azure recalls totalmente os dados de ficheiro de ficheiros do Azure sem o necessidade de saber o que não é armazenado o ficheiro localmente no sistema do utilizador. Esta funcionalidade também é conhecido como gestão de armazenamento hierárquico (HSM).

## <a name="azure-file-sync-interoperability"></a>Interoperabilidade de sincronização de ficheiros do Azure 
Esta secção abrange interoperabilidade de sincronização de ficheiros do Azure com o Windows Server funcionalidades e funções e das soluções de terceiros.

### <a name="supported-versions-of-windows-server"></a>Versões suportadas do Windows Server
Atualmente, as versões suportadas do Windows Server por uma sincronização de ficheiros do Azure são:

| Versão | SKUs suportados | Opções de implementação suportados |
|---------|----------------|------------------------------|
| Windows Server 2016 | Datacenter e Standard | Completo (servidor com uma IU) |
| Windows Server 2012 R2 | Datacenter e Standard | Completo (servidor com uma IU) |

Versões futuras do Windows Server serão adicionadas à medida que são lançadas. Versões anteriores do Windows podem ser adicionadas com base nos comentários dos utilizadores.

> [!Important]  
> É recomendável manter todos os servidores que utiliza com sincronização de ficheiros do Azure atualizada com as atualizações mais recentes do Windows Update. 

### <a name="file-system-features"></a>As funcionalidades do sistema de ficheiros
| Funcionalidade | Estado de suporte | Notas |
|---------|----------------|-------|
| Listas de controlo de acesso (ACLs) | Totalmente suportado | ACLs do Windows são mantidas através da sincronização de ficheiros do Azure e são impostas pelo Windows Server em pontos finais do servidor. ACLs do Windows não são (ainda) suportado pelo Azure ficheiros se os ficheiros são acedidos diretamente na nuvem. |
| Ligações fixas | Ignorada | |
| Ligações simbólicas | Ignorada | |
| Os pontos de montagem | Parcialmente suportada | Pontos de montagem poderão ser a raiz de um ponto final do servidor, mas são ignorados se estes estão contidos no espaço de nomes de um ponto final do servidor. |
| Junctions | Ignorada | |
| Pontos de reanálise | Ignorada | |
| Compressão NTFS | Totalmente suportado | |
| Ficheiros dispersos | Totalmente suportado | Sincronização de ficheiros dispersos (não são bloqueadas), mas que a sincronização na nuvem como um ficheiro completo. Se alterar o conteúdo do ficheiro na nuvem (ou noutro servidor), o ficheiro já não consta disperso quando a alteração é transferida. |
| Fluxos de dados alternativos (anúncios) | Preservados, mas não sincronizado | |

> [!Note]  
> São suportados apenas os volumes NTFS.

### <a name="failover-clustering"></a>Clustering de ativação pós-falha
Clustering de ativação pós-falha do Windows Server é suportada pela sincronização de ficheiros do Azure para a opção de implementação de "Servidor de ficheiros de utilização geral". Clustering de ativação pós-falha não é suportada em "Servidor de ficheiros de escalamento horizontal para dados de aplicação" (SOFS) ou em Volumes Partilhados em cluster (CSVs).

> [!Note]  
> O agente de sincronização de ficheiros do Azure tem de ser instalado em cada nó no Cluster de ativação pós-falha para a sincronização funcione corretamente.

### <a name="data-deduplication"></a>A eliminação de duplicados de dados
Para volumes que não tenham uma cloud camadas ativada, a sincronização de ficheiros do Azure suporta a ser ativada no volume do Windows eliminação de duplicados de dados de servidor. Atualmente, não suportamos a interoperabilidade entre a sincronização de ficheiros do Azure com a nuvem em camadas ativado e a eliminação de duplicados de dados.

### <a name="antivirus-solutions"></a>Soluções de antivírus
Porque antivírus funciona através da análise de ficheiros de código malicioso conhecido, um produto antivírus pode causar a devolução de chamada de ficheiros em camadas. Porque os ficheiros em camadas tem o atributo "offline" definido, recomendamos a consultar o fornecedor de software para aprender a configurar a solução para ignorar a leitura de ficheiros offline. 

As seguintes soluções são conhecidas para suportar a ignorar os ficheiros offline:

- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [Segurança de ponto final de McAfee](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) (consulte "Apenas o que precisa de análise" na página 90 do PDF)
- [Kaspersky software antivírus](https://support.kaspersky.com/4684)
- [Proteção de ponto final da Sophos](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>Soluções de cópia de segurança
Como soluções de antivírus, soluções de cópia de segurança poderão causar a devolução de chamada de ficheiros em camadas. Recomendamos que utilize uma solução de cópia de segurança da nuvem para criar cópias de segurança da partilha de ficheiros do Azure em vez de um produto de cópia de segurança no local.

### <a name="encryption-solutions"></a>Soluções de encriptação
Suporte para soluções de encriptação depende da forma como são implementados. Sincronização de ficheiros do Azure é conhecida para trabalhar com:

- Encriptação BitLocker
- Azure Rights Management Services (Azure RMS) (e legado Active Directory RMS)

Sincronização de ficheiros do Azure é conhecida não trabalhar com:

- NTFS encriptados sistema de ficheiros (EFS)

Em geral, a sincronização de ficheiros do Azure deve suportar a interoperabilidade com soluções de encriptação que manter-se abaixo o sistema de ficheiros, tais como o BitLocker e com soluções que são implementadas no formato de ficheiro, tal como BitLocker. Não foi efetuada nenhuma interoperabilidade especial para soluções que manter-se acima do sistema de ficheiros (por exemplo, o EFS de NTFS).

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Outras soluções de gestão de armazenamento hierárquico (HSM)
Não existem outras soluções HSM devem ser utilizadas com sincronização de ficheiros do Azure.

## <a name="region-availability"></a>Disponibilidade de região
Sincronização de ficheiros do Azure está disponível apenas nas regiões seguintes na pré-visualização:

| Região | Localização do Centro de dados |
|--------|---------------------|
| EUA Oeste | Califórnia, EUA |
| Europa Ocidental | Países Baixos |
| Sudeste Asiático | Singapura |
| Leste da Austrália | Novo Wales-Sul, da Austrália |

Pré-visualização, suportamos a sincronizar apenas com uma partilha de ficheiros do Azure que se encontra na mesma região que o serviço de sincronização de armazenamento.

## <a name="azure-file-sync-agent-update-policy"></a>Política de atualização do agente do Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Passos seguintes
* [Planear uma implementação de ficheiros do Azure](storage-files-planning.md)
* [Implementar ficheiros do Azure](storage-files-deployment-guide.md)
* [Implementar a sincronização de ficheiros do Azure](storage-sync-files-deployment-guide.md)
