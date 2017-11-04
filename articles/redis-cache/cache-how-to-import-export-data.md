---
title: Importar e exportar dados na Cache de Redis do Azure | Microsoft Docs
description: "Saiba como importar e exportar dados para e do armazenamento de Blobs com as instâncias de Cache de Redis do Azure premium"
services: redis-cache
documentationcenter: 
author: steved0x
manager: douge
editor: 
ms.assetid: 4a68ac38-87af-4075-adab-569d37d7cc9e
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache-redis
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: sdanie
ms.openlocfilehash: 5e6d731f0a1cecc1a191c74a45e37a9b94fd98ee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="import-and-export-data-in-azure-redis-cache"></a>Importar e exportar dados na Cache de Redis do Azure
Importar/exportar é uma operação de gestão de dados de Cache de Redis do Azure, que lhe permite importar dados para a Cache de Redis do Azure ou exportar dados a partir da Cache de Redis do Azure por importar e exportar um instantâneo de base de dados de Cache de Redis (RDB) de uma cache premium para um blob numa do Azure Conta de armazenamento. 

- **Exportar** -pode exportar os instantâneos de RDB de Cache de Redis do Azure para um Blob de página.
- **Importar** -pode importar os instantâneos de RDB de Cache de Redis a partir de um Blob de página ou um Blob de blocos.

Importar/exportar permite-lhe migrar entre instâncias diferentes de Cache de Redis do Azure ou povoar a cache com os dados antes de utilização.

Este artigo fornece um guia para importar e exportar dados com a Cache de Redis do Azure e fornece respostas às perguntas mais comuns.

> [!IMPORTANT]
> Importar/exportar está em pré-visualização e só está disponível para [escalão premium](cache-premium-tier-intro.md) coloca em cache.
>
>

## <a name="import"></a>Importar
Importação pode ser utilizada para colocar ficheiros RDB compatíveis Redis a partir de qualquer servidor Redis em execução em qualquer nuvem ou o ambiente, incluindo Redis em execução no Linux, Windows ou qualquer fornecedor de nuvem como Amazon Web Services e outros. Importação de dados é uma forma fácil de criar uma cache com dados pré-preenchidos. Durante o processo de importação, a Cache de Redis do Azure carrega os ficheiros RDB storage do Azure para a memória e, em seguida, insere as chaves para a cache.

> [!NOTE]
> Antes de iniciar a operação de importação, certifique-se de que o ficheiro de base de dados de Redis (RDB) ou os ficheiros são carregados na página ou bloco blobs no armazenamento do Azure, na mesma região e subscrição enquanto a instância da Cache de Redis do Azure. Para obter mais informações, consulte [introdução ao Blob storage do Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Se exportou o ficheiro RDB utilizando o [Azure Redis Cache exportar](#export) funcionalidade, o seu ficheiro RDB já está armazenado num blob de página e está pronto para importar.
>
>

1. Para importar um ou mais blobs de cache exportado [navegue até à sua cache](cache-configure.md#configure-redis-cache-settings) no portal do Azure e clique em **importar dados** do **menu recursos**.

    ![Importar dados][cache-import-data]
2. Clique em **escolha Blob(s)** e selecione a conta de armazenamento que contém os dados a importar.

    ![Escolha a conta de armazenamento][cache-import-choose-storage-account]
3. Clique no contentor que contém os dados a importar.

    ![Escolha o contentor][cache-import-choose-container]
4. Selecione um ou mais blobs para importar clicando área à esquerda do nome do blob e, em seguida, clique em **selecione**.

    ![Escolha os blobs][cache-import-choose-blobs]
5. Clique em **importar** para iniciar o processo de importação.

   > [!IMPORTANT]
   > A cache não está acessível aos clientes de cache durante o processo de importação e quaisquer dados existentes na cache são eliminados.
   >
   >

    ![Importar][cache-import-blobs]

    Pode monitorizar o progresso da operação de importação, seguindo as notificações do portal do Azure, ou visualizando eventos o [registo de auditoria](../azure-resource-manager/resource-group-audit.md).

    ![Progresso de importação][cache-import-data-import-complete]

## <a name="export"></a>Exportar
Exportação permite-lhe exportar os dados armazenados na Cache de Redis do Azure para Redis compatíveis RDB ficheiros. Pode utilizar esta funcionalidade para mover dados de uma instância da Cache de Redis do Azure para outra ou para outro servidor de Redis. Durante o processo de exportação, um ficheiro temporário é criado na VM que aloja a instância de servidor de Cache de Redis do Azure e o ficheiro é carregado para a conta de armazenamento designada. Quando concluir a operação de exportação com o estado de sucesso ou falha, o ficheiro temporário é eliminado.

1. Para exportar os conteúdos atuais da cache para o armazenamento, [navegue até à sua cache](cache-configure.md#configure-redis-cache-settings) no portal do Azure e clique em **exportar dados** do **menu recursos**.

    ![Escolha o contentor de armazenamento][cache-export-data-choose-storage-container]
2. Clique em **escolha o contentor de armazenamento** e selecionar a conta de armazenamento pretendido. A conta de armazenamento tem de ser na mesma subscrição e região como a sua cache.

   > [!IMPORTANT]
   > Exportar funciona com os blobs de página, são suportadas pelo clássico e contas de armazenamento do Resource Manager, mas não são suportadas pelo [contas do Blob storage](../storage/blobs/storage-blob-storage-tiers.md#blob-storage-accounts) neste momento.
   >
   >

    ![Conta de armazenamento][cache-export-data-choose-account]
3. Escolha o contentor de blob pretendida e clique em **selecione**. Para utilizar novos um contentor, clique em **adicionar contentor** adicioná-lo primeiro e, em seguida, selecioná-lo a partir da lista.

    ![Escolha o contentor de armazenamento][cache-export-data-container]
4. Escreva um **prefixo do nome do Blob** e clique em **exportar** para iniciar o processo de exportação. O prefixo de nome do blob é utilizado para o prefixo os nomes dos ficheiros gerados por esta operação de exportação.

    ![Exportar][cache-export-data]

    Pode monitorizar o progresso da operação de exportação, seguindo as notificações do portal do Azure, ou visualizando eventos o [registo de auditoria](../azure-resource-manager/resource-group-audit.md).

    ![Exportar dados concluída][cache-export-data-export-complete]

    Caches permanecem disponíveis para utilização durante o processo de exportação.

## <a name="importexport-faq"></a>FAQ para importar/exportar
Esta secção contém perguntas mais frequentes sobre a funcionalidade de importação/exportação.

* [Os preços camadasm podem utilizar a importação/exportação?](#what-pricing-tiers-can-use-importexport)
* [Pode importar dados a partir de qualquer servidor de Redis?](#can-i-import-data-from-any-redis-server)
* [Quais as versões RDB que pode a importar](#what-rdb-versions-can-i-import)
* [A minha cache está disponível durante uma operação de importação/exportação?](#is-my-cache-available-during-an-importexport-operation)
* [Pode utilizar para importar/exportar com o cluster de Redis?](#can-i-use-importexport-with-redis-cluster)
* [Como funciona com uma definição de bases de dados personalizado para importar/exportar?](#how-does-importexport-work-with-a-custom-databases-setting)
* [Como é diferente da persistência de Redis importação/exportação?](#how-is-importexport-different-from-redis-persistence)
* [Pode automatizar utilizando o PowerShell, CLI ou outros clientes de gestão para importar/exportar?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Foi recebido um erro de tempo limite durante a minha operação de importação/exportação. O que significa?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [Posso obteve um erro ao exportar os meus dados ao Blob Storage do Azure. O que aconteceu?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Os preços camadasm podem utilizar a importação/exportação?
Importar/exportar está disponível apenas o escalão de preços premium.

### <a name="can-i-import-data-from-any-redis-server"></a>Pode importar dados a partir de qualquer servidor de Redis?
Sim, para além de importar os dados exportados a partir de instâncias de Cache de Redis do Azure, pode importar ficheiros RDB a partir de qualquer servidor Redis em execução em qualquer nuvem ou o ambiente, por exemplo, o Linux, Windows, ou na nuvem fornecedores, como os Amazon Web Services. Para tal, carregue o ficheiro RDB do servidor de Redis pretendido para um blob de página ou o bloco de uma conta de armazenamento do Azure e, em seguida, importe-o para a instância da Cache de Redis do Azure premium. Por exemplo, poderá pretender exportar os dados da sua cache de produção e importe-o para uma cache do utilizado como parte de um ambiente de teste para testar ou a migração.

> [!IMPORTANT]
> Para importar com êxito os dados exportados a partir do Redis servidores que não seja a Cache de Redis do Azure ao utilizar um blob de página, o tamanho do blob de página deve obrigatoriamente estar alinhado num limite de 512 bytes. Código de exemplo efetuar quaisquer preenchimento bytes necessários, consulte [carregamento de blogue de página de exemplo](https://github.com/JimRoberts-MS/SamplePageBlobUpload).
> 
> 

### <a name="what-rdb-versions-can-i-import"></a>Quais as versões RDB que pode a importar

Cache de Redis do Azure suporta a importação RDB até através de RDB versão 7.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>A minha cache está disponível durante uma operação de importação/exportação?
* **Exportar** - Caches permanecem disponíveis e pode continuar a utilizar a cache durante uma operação de exportação.
* **Importar** - Caches ficar indisponíveis quando é iniciada uma operação de importação e fiquem disponíveis para utilização quando concluir a operação de importação.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Pode utilizar para importar/exportar com o cluster de Redis?
Sim, e pode importar/exportar entre uma cache em cluster e uma cache agrupado. Dado que o cluster de Redis [só suporta a base de dados 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), quaisquer dados nas bases de dados que não seja 0 não são importados. Quando for importados dados em cache em cluster, as chaves são redistribuídas entre shards do cluster.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Como funciona com uma definição de bases de dados personalizado para importar/exportar?
Alguns escalões de preços têm diferentes [limites de bases de dados](cache-configure.md#databases), por isso, existem algumas considerações ao importar se configurou um valor personalizado para o `databases` definição durante a criação da cache.

* Quando importar para um escalão de preço um inferior `databases` limite que a camada a partir da qual exportou:
  * Se estiver a utilizar o número predefinido de `databases`, que é 16 para todos os escalões de preços, não se tenha perdido nenhum dado.
  * Se estiver a utilizar um número personalizado de `databases` que fica dentro dos limites para a camada à qual está a importar, não se tenha perdido nenhum dado.
  * Se os dados exportados continha dados numa base de dados excede os limites do novo escalão, os dados dessas bases de dados superiores não são importados.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Como é diferente da persistência de Redis importação/exportação?
Persistência de Cache de Redis do Azure permite-lhe manter os dados armazenados no Redis ao Storage do Azure. Quando a persistência estiver configurada, a Cache de Redis do Azure mantém um instantâneo da cache de Redis num formato binário de Redis para o disco com base numa frequência de cópia de segurança configurável. Se ocorrer um evento de catastrófica que desativa a cache de primário e réplica, os dados da cache são restaurados automaticamente utilizando o instantâneo mais recente. Para obter mais informações, consulte [como configurar a persistência de dados para uma Cache de Redis do Azure Premium](cache-how-to-premium-persistence.md).

Importação / exportação permite-lhe colocar dados em ou exportar a partir da Cache de Redis do Azure. Não configurar cópia de segurança e restauro utilizando a persistência de Redis.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Pode automatizar utilizando o PowerShell, CLI ou outros clientes de gestão para importar/exportar?
Sim, para o PowerShell instruções consulte [para importar uma cache de Redis](cache-howto-manage-redis-cache-powershell.md#to-import-a-redis-cache) e [para exportar uma cache de Redis](cache-howto-manage-redis-cache-powershell.md#to-export-a-redis-cache).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Foi recebido um erro de tempo limite durante a minha operação de importação/exportação. O que significa?
Se permanecer no **importar dados** ou **exportar dados** painel superior a 15 minutos antes de iniciar a operação, receberá um erro com uma mensagem de erro semelhante ao seguinte exemplo:

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Para resolver este problema, iniciar a importação ou exportação antes de 15 minutos expirou.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Posso obteve um erro ao exportar os meus dados ao Blob Storage do Azure. O que aconteceu?
Exportação funciona apenas com os ficheiros RDB armazenados como blobs de páginas. Outros tipos de BLOBs não são atualmente suportados, incluindo contas do blob storage com camadas de acesso frequente e esporádico. Para obter mais informações, consulte [contas do Blob storage](../storage/blobs/storage-blob-storage-tiers.md#blob-storage-accounts).

## <a name="next-steps"></a>Passos seguintes
Saiba como utilizar as funcionalidades de cache do mais premium.

* [Introdução ao escalão Premium de Cache de Redis do Azure](cache-premium-tier-intro.md)    

<!-- IMAGES -->
[cache-settings-import-export-menu]: ./media/cache-how-to-import-export-data/cache-settings-import-export-menu.png
[cache-export-data-choose-account]: ./media/cache-how-to-import-export-data/cache-export-data-choose-account.png
[cache-export-data-choose-storage-container]: ./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png
[cache-export-data-container]: ./media/cache-how-to-import-export-data/cache-export-data-container.png
[cache-export-data-export-complete]: ./media/cache-how-to-import-export-data/cache-export-data-export-complete.png
[cache-export-data]: ./media/cache-how-to-import-export-data/cache-export-data.png
[cache-import-data]: ./media/cache-how-to-import-export-data/cache-import-data.png
[cache-import-choose-storage-account]: ./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png
[cache-import-choose-container]: ./media/cache-how-to-import-export-data/cache-import-choose-container.png
[cache-import-choose-blobs]: ./media/cache-how-to-import-export-data/cache-import-choose-blobs.png
[cache-import-blobs]: ./media/cache-how-to-import-export-data/cache-import-blobs.png
[cache-import-data-import-complete]: ./media/cache-how-to-import-export-data/cache-import-data-import-complete.png
