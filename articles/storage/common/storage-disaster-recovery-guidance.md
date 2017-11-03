---
title: O que fazer em caso de uma falha de armazenamento do Azure | Microsoft Docs
description: O que fazer em caso de uma falha de armazenamento do Azure
services: storage
documentationcenter: .net
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 8f040b0f-8926-4831-ac07-79f646f31926
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 1/19/2017
ms.author: tamram
ms.openlocfilehash: c768bdbb8e1ce2aae3eb5b6db0e48977366c83fb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="what-to-do-if-an-azure-storage-outage-occurs"></a>O que fazer se ocorrer uma falha do Armazenamento do Azure
Na Microsoft, trabalhamos rígido para se certificar de que os nossos serviços estão sempre disponíveis. Por vezes, força se para além dos nosso impacto de controlo-nos de formas que provocar falhas de serviço não planeada num ou mais regiões. Para ajudar a lidar com estas ocorrências raras, podemos fornecer as seguintes orientações de alto nível para serviços de armazenamento do Azure.

## <a name="how-to-prepare"></a>Como preparar
É fundamental para cada cliente preparar o seu próprio plano de recuperação após desastre. O esforço para recuperar a partir de uma falha de armazenamento, normalmente, envolve pessoal de operações e procedimentos automatizados para reativar as suas aplicações num Estado de funcionamento. Consulte a documentação do Azure abaixo para criar o seu próprio plano de recuperação após desastre:

* [Recuperação após desastre e elevada disponibilidade para aplicações do Azure](/azure/architecture/resiliency/disaster-recovery-high-availability-azure-applications.md)
* [Orientações técnicas sobre resiliência do Azure](/azure/architecture/resiliency.md)
* [Serviço de recuperação de sites do Azure](https://azure.microsoft.com/services/site-recovery/)
* [Replicação do Armazenamento do Azure](storage-redundancy.md)
* [Serviço de cópia de segurança do Azure](https://azure.microsoft.com/services/backup/)

## <a name="how-to-detect"></a>Como detetar
O modo recomendado para determinar o estado do serviço do Azure é para subscrever o [Dashboard de estado de funcionamento do serviço de Azure](https://azure.microsoft.com/status/).

## <a name="what-to-do-if-a-storage-outage-occurs"></a>O que fazer se ocorrer uma falha de armazenamento
Se um ou mais serviços de armazenamento estão temporariamente indisponíveis num ou mais regiões, existem duas opções para a ter em consideração. Se pretendidos ao nível acesso imediato aos seus dados, considere opção 2.

### <a name="option-1-wait-for-recovery"></a>Opção 1: Aguardar para recuperação
Neste caso, não é necessária qualquer ação da sua parte. Estamos a trabalhar diligently para restaurar a disponibilidade do serviço do Azure. Pode monitorizar o estado do serviço no [Dashboard de estado de funcionamento do serviço de Azure](https://azure.microsoft.com/status/).

### <a name="option-2-copy-data-from-secondary"></a>Opção 2: Copiar dados a partir do secundário
Se tiver escolhido [armazenamento georredundante com acesso de leitura (RA-GRS)](storage-redundancy.md#read-access-geo-redundant-storage) (recomendado) para as contas do storage, terá acesso de leitura aos seus dados da região secundária. Pode utilizar ferramentas como [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md)e o [biblioteca de movimento de dados do Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) para copiar dados a partir da região secundária para outra conta de armazenamento numa região unimpacted e, em seguida, aponte as suas aplicações para essa conta de armazenamento para ambos de leitura e escrita a disponibilidade.

## <a name="what-to-expect-if-a-storage-failover-occurs"></a>O que pode esperar se ocorre uma ativação pós-falha de armazenamento
Se tiver escolhido [armazenamento georredundante (GRS)](storage-redundancy.md#geo-redundant-storage) ou [armazenamento georredundante com acesso de leitura (RA-GRS)](storage-redundancy.md#read-access-geo-redundant-storage) (recomendado), armazenamento do Azure irá manter os seus dados duráveis em duas regiões (principais e secundários). Em ambas as regiões, o Storage do Azure mantém constantemente várias réplicas dos seus dados.

Quando um desastre regional afeta a região primária, vamos primeiro tentar restaurar o serviço nessa região. Depende a natureza desastre e respetivos impactos, em ocasiões raras, não poderá restaurar a região primária. Nessa altura, iremos efetuar uma ativação pós-falha georreplicação. A replicação de dados por várias regiões é um processo assíncrono que pode envolver um atraso, pelo que é possível que as alterações que ainda não tem sido replicadas para a região secundária podem ser perdidas. Pode consultar o ["Hora da última sincronização" da conta de armazenamento](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/) para obter detalhes sobre o estado de replicação.

Alguns pontos relacionados com a experiência de ativação de pós-falha georreplicação de armazenamento:

* Armazenamento georreplicação-ativação pós-falha só será acionada pela equipa do Storage do Azure – não é necessária nenhuma ação de cliente.
* Os existente armazenamento pontos finais de serviço para os blobs, tabelas, filas e ficheiros permanecer o mesmo após a ativação pós-falha; a entrada DNS fornecido pelo Microsoft terá de ser atualizado para mudar da região primária para a região secundária.  Microsoft irá efetuar esta atualização automaticamente como parte do processo de ativação pós-falha de georreplicação.
* Antes e durante a ativação pós-falha georreplicação, não terá acesso de escrita à sua conta de armazenamento devido ao impacto de desastre, mas ainda pode ler partir do secundário se a conta do storage tiver sido configurada como RA-GRS.
* Quando a ativação pós-falha georreplicação foi concluída e as alterações DNS propagadas, leitura e escrita à sua conta do storage serão retomados; Isto pontos que era possível o ponto final secundário. 
* Tenha em atenção que tenha acesso de escrita se tiver GRS ou RA-GRS configurada para a conta de armazenamento. 
* Pode consultar ["Última hora de ativação pós-falha Georreplicação" da conta de armazenamento](https://msdn.microsoft.com/library/azure/ee460802.aspx) para obter mais detalhes.
* Após a ativação pós-falha, a conta do storage será completamente estar a funcionar, mas num estado "degradado", dado que é realmente alojada numa região autónomo com nenhuma possíveis de georreplicação. Para mitigar este risco, iremos restaurar região principal original e, em seguida, efetue uma reativação pós-falha georreplicação para restaurar o estado original. Se a região primária original é irrecuperável, iremos irá alocar noutra região secundária.
  Para obter mais detalhes sobre a infraestrutura de replicação do Storage do Azure georreplicação, consulte o artigo do blogue da equipa de armazenamento sobre [opções de redundância e RA-GRS](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

## <a name="best-practices-for-protecting-your-data"></a>Melhores práticas para proteger os dados
Existem algumas abordagens recomendadas fazer cópias de segurança os dados de armazenamento regularmente.

* Utilizar discos VM – o [serviço de cópia de segurança do Azure](https://azure.microsoft.com/services/backup/) para copiar em segurança os discos VM utilizados por máquinas virtuais do Azure.
* Blobs de blocos – criar um [instantâneo](https://msdn.microsoft.com/library/azure/hh488361.aspx) de cada blob de blocos ou copiar os blobs para outra conta de armazenamento na utilização de região outro [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md), ou o [biblioteca de movimento de dados do Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).
* Utilizar tabelas – [AzCopy](storage-use-azcopy.md) para exportar os dados da tabela para outra conta de armazenamento noutra região.
* Utilize ficheiros – [AzCopy](storage-use-azcopy.md) ou [Azure PowerShell](storage-powershell-guide-full.md) para copiar os ficheiros para outra conta de armazenamento noutra região.

Para obter informações sobre como criar aplicações que tirem o máximo partido da funcionalidade RA-GRS, consulte [conceber altamente disponível aplicações utilizando o armazenamento RA-GRS](../storage-designing-ha-apps-with-ragrs.md)

