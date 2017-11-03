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
ms.openlocfilehash: d626f71aa21cea562ef6c9554c05e6de027e7f4d
ms.sourcegitcommit: 76a3cbac40337ce88f41f9c21a388e21bbd9c13f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="planning-for-an-azure-file-sync-preview-deployment"></a>Planear uma implementação de sincronização de ficheiros do Azure (pré-visualização)
O Azure File Sync (pré-visualização) permite-lhe centralizar as partilhas de ficheiros da sua organização nos Ficheiros do Azure sem abdicar da flexibilidade, do desempenho e da compatibilidade de um servidor de ficheiros no local. Isto é feito ao transformar os Servidores do Windows numa cache rápida da partilha de ficheiros do Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter o número de caches que precisar em todo o mundo.

Este guia descreve o que considerar quando implementar a sincronização de ficheiros do Azure. É recomendado que leia [planear uma implementação de ficheiros do Azure](storage-files-planning.md) guia de teste. 

## <a name="understanding-azure-file-sync-terminology"></a>Noções sobre a terminologia de sincronização de ficheiros do Azure
Antes de avançar para os detalhes de sincronização de ficheiros do Azure, é importante perceber a terminologia.

### <a name="storage-sync-service"></a>Serviço de sincronização de armazenamento
O serviço de sincronização de armazenamento é o recurso mais superior do Azure, que representa a sincronização de ficheiros do Azure. O recurso de serviço de sincronização de armazenamento é um elemento de rede do recurso de conta de armazenamento e similarily pode ser implementado em grupos de recurso do Azure. Um recurso de nível superior distinto do recurso de conta de armazenamento é necessário porque o serviço de sincronização de armazenamento pode criar relações de sincronização com várias contas do storage através de vários grupos de sincronização. Uma subscrição pode ter vários recursos de armazenamento sincronização serviço implementados.

### <a name="sync-group"></a>Grupo de sincronização
Um grupo de sincronização define a topologia de sincronização para um conjunto de ficheiros. Pontos finais dentro de um grupo de sincronização serão mantidos sincronizados entre si. Se, por exemplo, tiver dois conjuntos diferentes de ficheiros que pretende gerir com AFS, iria criar dois grupos de sincronização e adicionar pontos finais diferentes para cada um. Um serviço de sincronização de armazenamento pode alojar tantos de sincronização de grupos, conforme necessário.  

### <a name="registered-server"></a>Servidor registado
O objeto de servidor registado representa uma relação de confiança entre o servidor (ou o cluster) e o serviço de sincronização de armazenamento. Pode registar o número de servidores a uma instância de serviço de sincronização de armazenamento que for necessário. No entanto, um servidor (ou o cluster) só pode ser registado com um serviço de sincronização de armazenamento em qualquer momento.

### <a name="azure-file-sync-agent"></a>Agente de sincronização de ficheiros do Azure
O agente de sincronização de ficheiros do Azure é um pacote transferível, que permitem que um servidor do Windows sejam sincronizados com um ficheiro de Azure partilha. O agente de sincronização de ficheiros do Azure é composto por três componentes principais: 
- **FileSyncSvc.exe**: o serviço do Windows é responsável pela monitorização de alterações em pontos finais do servidor e iniciar as sessões de sincronização para o Azure em segundo plano.
- **StorageSync.sys**: o sistema de ficheiros de sincronização de ficheiros do Azure filtrar, responsável ficheiros camados para ficheiros do Azure (nuvem quando camadas está ativado).
- **Cmdlets de gestão do PowerShell**: os cmdlets do PowerShell para interagir com o fornecedor de recursos do Azure Microsoft.StorageSync. Pode encontrá-las nas seguintes localizações (por predefinição):
    - C:\Programas\Microsoft Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Programas\Microsoft Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Ponto final do servidor
Um ponto final do servidor representa uma localização específica no servidor registado, tais como uma pasta no volume do servidor ou na raiz do volume. Vários pontos finais de servidor pode existir no mesmo volume se os espaços de nomes não são sobreposição (por exemplo, F:\sync1 e F:\sync2). Pode configurar políticas de camadas na nuvem individualmente para cada ponto final do servidor. Se adicionar uma localização do servidor com um conjunto de ficheiros como um ponto final do servidor a um grupo de sincronização, os ficheiros serão Unidos com quaisquer outros ficheiros já em execução no grupo de sincronização de outros pontos finais.

### <a name="cloud-endpoint"></a>Ponto final da nuvem
Um ponto final da nuvem é uma partilha de ficheiros do Azure que faz parte de um grupo de sincronização. As sincronizações de partilha de ficheiros do Azure completos e uma partilha de ficheiros do Azure só podem ser um membro de um ponto final da nuvem e, por conseguinte, um grupo de sincronização. Se adicionar uma partilha de ficheiros do Azure com um conjunto de ficheiros como um ponto final da nuvem para um grupo de sincronização, os ficheiros serão Unidos com quaisquer outros ficheiros já em execução no grupo de sincronização de outros pontos finais.

> [!Important]  
> Suporta a sincronização de ficheiros do Azure efetuar alterações ao ficheiro Azure partilham diretamente, no entanto tenha em atenção que quaisquer alterações efetuadas na partilha de ficheiros de Azure primeiro tem de ser detetados por uma tarefa de deteção de alteração de sincronização de ficheiros do Azure, que é apenas initatiated para um ponto final da nuvem, uma vez a cada 24 horas. Consulte o [FAQ de ficheiros do Azure](storage-files-faq.md#afs-change-detection) para obter mais informações.

### <a name="cloud-tiering"></a>Disposição em camadas na cloud 
Nuvem camadas é uma funcionalidade opcional de sincronização de ficheiros do Azure, que permitem que raramente utilizado ou aceder a ficheiros a camadas para ficheiros do Azure. Quando um ficheiro está em camadas, o sistema de ficheiros de sincronização de ficheiros do Azure filtrar (StorageSync.sys) substitui o ficheiro localmente com um ponteiro ou reanálise ponto, que representa um URL para o ficheiro nos ficheiros do Azure. Um ficheiro em camadas tem o atributo "offline" definido no NTFS, para que aplicações de terceiros podem identificar os ficheiros em camadas. Quando um utilizador abre um ficheiro em camadas, sincronização de ficheiros do Azure recalls totalmente os dados de ficheiro de ficheiros do Azure sem o necessidade de saber o ficheiro do utilizador não são armazenados localmente no sistema. Esta funcionalidade também é conhecido como gestão de armazenamento hierárquico (HSM).

## <a name="azure-file-sync-interoperability"></a>Interoperabilidade de sincronização de ficheiros do Azure 
Esta secção abrange interoperabilidade de sincronização de ficheiros do Azure com o Windows Server funcionalidades e funções e das soluções de terceiros.

### <a name="supported-versions-of-windows-server"></a>Versões suportadas do Windows Server
Atualmente, as versões suportadas do Windows Server por uma sincronização de ficheiros do Azure são:

| Versão | SKUs suportados | Opções de implementação suportados |
|---------|----------------|------------------------------|
| Windows Server 2016 | Datacenter e Standard | Completo (servidor com uma IU) |
| Windows Server 2012 R2 | Datacenter e Standard | Completo (servidor com uma IU) |

Versões futuras do Windows Server serão adicionadas à medida que são lançadas e versões mais antigas do Windows podem ser adicionadas com base nos comentários dos utilizadores.

> [!Important]  
> É recomendável manter todos os servidores do Windows utilizada com sincronização de ficheiros do Azure atualizada com as atualizações mais recentes do Windows Update. 

### <a name="file-system-features"></a>As funcionalidades do sistema de ficheiros
| Funcionalidade | Estado de suporte | Notas |
|---------|----------------|-------|
| Listas de controlo de acesso (ACLs) | Totalmente suportado | ACLs do Windows são mantidas através da sincronização de ficheiros do Azure e são impostas pelo Windows Server em pontos finais do servidor. ACLs do Windows não são (ainda) suportado pelo Azure ficheiros se os ficheiros são acedidos diretamente na nuvem. |
| Ligações fixas | Foi ignorada | |
| Ligações simbólicas | Foi ignorada | |
| Os pontos de montagem | Parcialmente suportada | Pontos de montagem podem ser a raiz de um ponto final do servidor, mas serão ignorados se contida no espaço de nomes do ponto final do servidor. |
| Junctions | Foi ignorada | |
| Pontos de reanálise | Foi ignorada | |
| Compressão NTFS | Totalmente suportado | |
| Ficheiros dispersos | Totalmente suportado | Irão sincronizar ficheiros dispersos (não bloqueado), mas serão sincronizados para a nuvem como um ficheiro completo. Se alterar o conteúdo do ficheiro na nuvem (ou noutro servidor), o ficheiro já não será disperso, quando a alteração é transferida |
| Fluxos de dados alternativos (anúncios) | Preservados, mas não sincronizado | |

> [!Note]  
> São suportados apenas os volumes NTFS.

### <a name="failover-clustering"></a>Clustering de ativação pós-falha
Clustering de ativação pós-falha do Windows Server é suportada pela sincronização de ficheiros do Azure para a opção de implementação de "Servidor de ficheiros de utilização geral". Clustering de ativação pós-falha não é suportada em "Servidor de ficheiros de escalamento horizontal para dados de aplicação" (SOFS) ou num cluster Volumes Partilhados (CSV).

> [!Note]  
> O agente de sincronização de ficheiros do Azure tem de ser instalado em cada nó no Cluster de ativação pós-falha para a sincronização funcione corretamente.

### <a name="windows-server-data-deduplication"></a>A eliminação de duplicados de dados de servidor do Windows
Volumes sem nuvem camadas ativado, sincronização de ficheiros do Azure suporta a eliminação de duplicados de dados que está a ser ativada no volume. Não é suportada na interoperabilidade entre a sincronização de ficheiros do Azure com a nuvem em camadas ativada e eliminação de duplicados de dados neste momento.

### <a name="anti-virus-solutions"></a>Soluções de software antivírus
Porque o software antivírus funciona através da análise de ficheiros de código malicioso conhecido, o software antivírus podem causar a devolução de chamada de ficheiros em camadas. Uma vez que os ficheiros em camadas tem o atributo "offline" definido, recomendamos a consultar o fornecedor de software sobre como configurar a solução para ignorar a leitura de ficheiros offline. 

As seguintes soluções são conhecidas para suportar a ignorar os ficheiros offline:

- [Symantec Endpoint Protection](https://support.symantec.com/en_US/article.tech173752.html)
- [Segurança de ponto final de McAfee](https://kc.mcafee.com/resources/sites/MCAFEE/content/live/PRODUCT_DOCUMENTATION/26000/PD26799/en_US/ens_1050_help_0-00_en-us.pdf) (consulte "Apenas o que precisa de análise" secção na página 90)
- [Kaspersky software antivírus](https://support.kaspersky.com/4684)
- [Proteção de ponto final da Sophos](https://community.sophos.com/kb/en-us/40102)
- [TrendMicro OfficeScan](https://success.trendmicro.com/solution/1114377-preventing-performance-or-backup-and-restore-issues-when-using-commvault-software-with-osce-11-0#collapseTwo) 

### <a name="backup-solutions"></a>Soluções de cópia de segurança
Como soluções de software antivírus, soluções de cópia de segurança podem fazer com que a devolução de chamada de ficheiros em camadas. Recomendamos a utilização de uma solução de cópia de segurança de nuvem a cópia de segurança da partilha de ficheiros do Azure, em vez de utilizar um produto de cópia de segurança no local.

### <a name="encryption-solutions"></a>Soluções de encriptação
Suporte para soluções de encriptação depende da forma como são implementados. Sincronização de ficheiros do Azure é conhecida para trabalhar com:

- Encriptação BitLocker
- O Azure RMS (e legado Active Directory RMS)

Sincronização de ficheiros do Azure é conhecida não trabalhar com:

- NTFS encriptados sistema de ficheiros (EFS)

Em geral, a sincronização de ficheiros do Azure devem ser capaz de interoperabilidade com soluções de encriptação que manter-se abaixo o sistema de ficheiros, como o BitLocker e soluções implementadas no formato de ficheiro, tal como BitLocker, mas não foi efetuada nenhuma interop especial para interoperabilidade com soluções de manter-se acima do sistema de ficheiros (por exemplo, o sistema de ficheiros encriptados do NTFS).

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Outras soluções de gestão de armazenamento hierárquico (HSM)
Nenhuma outra solução de gestão de armazenamento hierárquico deve ser utilizada com sincronização de ficheiros do Azure.

## <a name="region-availability"></a>Disponibilidade de região
Sincronização de ficheiros do Azure só está disponível nas regiões seguintes na pré-visualização:

| Região | Localização do Centro de dados |
|--------|---------------------|
| EUA Oeste | Califórnia, EUA |
| Europa Ocidental | Países Baixos |
| Sudeste Asiático | Singapura |
| Leste da Austrália | Novo Wales-Sul, da Austrália |

Pré-visualização, só é suportada sincronização com uma partilha de ficheiros do Azure na mesma região que o serviço de sincronização de armazenamento.

## <a name="azure-file-sync-agent-update-policy"></a>Política de atualização do agente do Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Passos seguintes
* [Planear uma implementação de ficheiros do Azure](storage-files-planning.md)
* [Implementar os ficheiros do Azure](storage-files-deployment-guide.md)
* [Implementar a sincronização de ficheiros do Azure](storage-sync-files-deployment-guide.md)
