---
title: Mover grandes quantidades de dados de armazenamento na nuvem no Azure / | Microsoft Docs
description: "Uma descrição geral dos métodos diferentes para mover dados para e do armazenamento do Azure."
services: storage
documentationcenter: 
author: JarrettRenshaw
manager: msmets
editor: tysonn
ms.assetid: 5e3947a9-d99b-4108-9d57-3eb67c03e7ba
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/30/2017
ms.author: jarrettr
ms.openlocfilehash: db0f09433750a3af2d70039d780a25ad64bb4df1
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/27/2017
---
# <a name="moving-data-to-and-from-azure-storage"></a>Mover dados de e para o Storage do Azure
Se pretender mover os dados no local ao Storage do Azure (ou vice-versa), existem várias formas para efetuar este procedimento. A abordagem que funciona melhor para si dependerá do seu cenário. Este artigo irá fornecer uma rápida descrição geral dos diferentes cenários e ofertas adequadas para cada um deles.

## <a name="building-applications"></a>Criar aplicações
Se estiver a criar uma aplicação, programação com a API REST ou uma das nossas bibliotecas de cliente muitos é uma excelente forma de mover dados para e do armazenamento do Azure.

Armazenamento do Azure fornece bibliotecas de cliente avançado para .NET, iOS, Java, Android, plataforma Universal do Windows (UWP), Xamarin, C++, Node.JS, PHP, Ruby e Python. As bibliotecas de cliente oferecem funcionalidades avançadas, tais como lógica de repetição, registo e carregamentos paralelos. Também pode desenvolver diretamente na API REST, que pode ser chamada por qualquer linguagem que efetue pedidos HTTP/HTTPS.

Consulte [introdução ao Blob Storage do Azure](../blobs/storage-dotnet-how-to-use-blobs.md) para obter mais informações.

Além disso, estamos também oferecem a [biblioteca de movimento de dados de armazenamento do Azure](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement) que é uma biblioteca foi concebida para elevado desempenho cópia dos dados para e do Azure. Consulte a nossa biblioteca de movimento de dados [documentação](https://github.com/Azure/azure-storage-net-data-movement) para obter mais informações. 

## <a name="quickly-viewinginteracting-with-your-data"></a>Quickly viewing/interacting with your data (Ver/interagir rapidamente com os seus dados)
Se pretender uma forma fácil de ver os seus dados de armazenamento do Azure ao ter também a capacidade de carregar e transferir os dados, em seguida, considere utilizar um Explorador de armazenamento do Azure.

Consulte a nossa lista de [exploradores de armazenamento do Azure](../storage-explorers.md) para obter mais informações.

## <a name="system-administration"></a>Administração de sistema
Se exigir ou são mais confortável com um utilitário da linha de comandos (por exemplo, os administradores de sistema), aqui estão algumas opções a ter em consideração:

### <a name="azcopy"></a>AzCopy
O AzCopy é um utilitário de linha de comandos do Windows concebido para copiar dados de e para o Storage do Azure com um elevado desempenho. Também pode copiar os dados dentro de uma conta de armazenamento, ou entre contas de armazenamento diferente.

Consulte [transferir dados com o utilitário de linha de comandos do AzCopy](storage-use-azcopy.md) para obter mais informações.

### <a name="azure-powershell"></a>Azure PowerShell
O Azure PowerShell é um módulo que oferece cmdlets para gerir serviços no Azure. É uma shell de linha de comandos e linguagem de script baseada em tarefas concebida especialmente para a administração de sistemas.

Consulte [utilizar o Azure PowerShell com o Storage do Azure](storage-powershell-guide-full.md) para obter mais informações.

### <a name="azure-cli"></a>CLI do Azure
CLI do Azure fornece um conjunto de código aberto, comandos de várias plataformas para trabalhar com serviços do Azure. CLI do Azure está disponível no Windows, OSX e Linux.

Consulte [utilizando a CLI do Azure com o Storage do Azure](../storage-azure-cli.md) para obter mais informações.

## <a name="moving-large-amounts-of-data-with-a-slow-network"></a>Mover grandes quantidades de dados com uma rede lenta
Um dos maiores desafios associados mover grandes quantidades de dados é o tempo de transferência. Se pretender obter os dados do Storage do Azure sem ter de se preocupar sobre os custos de redes ou escrever código, em seguida, importar/exportar do Azure é uma solução adequada.

Consulte [do Azure para importar/exportar](../storage-import-export-service.md) para obter mais informações.

## <a name="backing-up-your-data"></a>Criar cópias de segurança dos seus dados
Se precisar simplesmente a cópia de segurança de dados ao Storage do Azure, cópias de segurança do Azure é a forma de aceder. Esta é uma solução poderosa para fazer uma cópia de segurança de dados no local e as VMs do Azure.

Consulte [cópia de segurança do Azure](../../backup/backup-introduction-to-azure-backup.md) para obter mais informações.

## <a name="accessing-your-data-on-premises-and-from-the-cloud"></a>Aceder a dados no local e da nuvem
Se precisar de uma solução para aceder aos dados no local e na nuvem, em seguida, deve considerar a utilização solução de armazenamento de nuvem híbrida do Azure, StorSimple. Esta solução é composta por um dispositivo físico StorSimple que inteligentemente frequentemente arquivos utilizado dados em SSDs, ocasionalmente utilizados dados em HDDs e inativos/cópia de segurança/dados de arquivo no armazenamento do Azure.

Consulte [StorSimple](../../storsimple/storsimple-overview.md) para obter mais informações.

## <a name="recovering-your-data"></a>Recuperar os dados
Quando tiver aplicações e cargas de trabalho no local, terá de uma solução que permite a sua empresa continuar a ser executado em caso de desastre. O Azure Site Recovery processa a replicação, ativação pós-falha e recuperação de máquinas virtuais e servidores físicos. Dados replicados são guardados no armazenamento do Azure, permitindo-lhe eliminar a necessidade de um datacenter secundário no local.

Consulte [do Azure Site Recovery](../../site-recovery/site-recovery-overview.md) para obter mais informações.
### <a name="moving-data-faq"></a>Mover dados FAQ:
## <a name="can-i-migrate-vhds-from-one-region-to-another-without-copying"></a>Posso migrar VHDs de uma região para outro sem copiar?
É a única forma de copiar VHDs entre região para copiar os dados entre contas de armazenamento em cada região. Pode utilizar o AZCopy para este. Ver os dados de transferência com o utilitário de linha de comandos de AzCopy para obter mais informações. Para grandes quantidades de dados, pode também importar/exportar do Azure. Consulte [do Azure para importar/exportar](https://docs.microsoft.com/azure/storage/storage-import-export-service) para obter mais informações.
