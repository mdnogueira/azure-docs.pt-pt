---
title: "Armazenamento do Azure da pilha: Diferenças e as considerações"
description: "Compreenda as diferenças entre o armazenamento de pilha do Azure e Storage do Azure, juntamente com considerações de implementação de pilha do Azure."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
ms.reviwer: xiaofmao
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/08/2017
ms.author: jeffgilb
ms.openlocfilehash: 1dc099fa234e217b682c88f2214fe271c916eec2
ms.sourcegitcommit: 93902ffcb7c8550dcb65a2a5e711919bd1d09df9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/09/2017
---
# <a name="azure-stack-storage-differences-and-considerations"></a>Armazenamento do Azure da pilha: Diferenças e as considerações

*Aplica-se a: Azure pilha integrado sistemas e Kit de desenvolvimento de pilha do Azure*

Armazenamento de pilha do Azure é um conjunto de serviços de cloud de armazenamento na pilha do Microsoft Azure. Armazenamento de pilha do Azure fornece blob, tabela, fila e funcionalidade de gestão de conta com semântica consistentes com o Azure.

Este artigo resume as diferenças de armazenamento do Azure pilha conhecidas do armazenamento do Azure. Também resume outras considerações a lembrar quando implementa a pilha do Azure. Para saber mais sobre das principais diferenças entre a pilha do Azure e do Azure, consulte o [chave considerações](azure-stack-considerations.md) tópico.

## <a name="cheat-sheet-storage-differences"></a>Cheat folha: diferenças de armazenamento

| Funcionalidade | Azure (global) | Azure Stack |
| --- | --- | --- |
|Armazenamento de ficheiros|Baseado na nuvem partilhas de ficheiros SMB suportadas|Ainda não suportado
|Encriptação do Serviço de Armazenamento do Azure para Dados Inativos|encriptação AES de 256 bits|Ainda não suportado
|Tipo de conta de armazenamento|Contas do Blob storage do Azure e para fins gerais|Para fins gerais apenas
|Opções de replicação|Armazenamento localmente redundante, o armazenamento georredundante, o armazenamento georredundante com acesso de leitura e o armazenamento com redundância de zona|Armazenamento localmente redundante
|Armazenamento Premium|Totalmente suportado|Pode ser aprovisionado, mas sem limite de desempenho ou garantir
|Managed disks|Premium e padrão suportados|Ainda não suportado
|Nome do blob|1024 caracteres (2.048 bytes)|880 carateres (1,760 bytes)
|Tamanho máximo do blob de bloco|4.75 TB (100 MB X 50 000 blocos)|50 000 4 MB (aprox 195 GB)
|Cópia de instantâneos do blob de página|Cópia de segurança do Azure não geridos VM discos anexados a uma VM em execução suportada|Ainda não suportado
|Cópia de instantâneo incremental de blob de página|Suportado de blobs de página do Azure standard e Premium|Ainda não suportado
|Tamanho máximo do blob de página|8 TB|1 TB
|Tamanho de página do blob de página|512 bytes|4 KB
|Tamanho da chave de linha e chave de partição da tabela|1024 caracteres (2.048 bytes)|400 carateres (800 bytes)

### <a name="metrics"></a>Métricas
Também existem algumas diferenças com as métricas do storage:
* Os dados de transação nas métricas do storage não distingue largura de banda de rede internos ou externos.
* Os dados de transação nas métricas do storage não incluem acesso a máquina virtual para os discos montados.

## <a name="api-version"></a>Versão de API
São suportadas as seguintes versões com armazenamento de pilha do Azure:

* Serviços de dados de armazenamento do Azure: [2015-04-05 versão de REST API](https://docs.microsoft.com/rest/api/storageservices/Version-2015-04-05?redirectedfrom=MSDN)
* Serviços de gestão de armazenamento do Azure: [2015-05-01-preview, 2015-06-15 e 2016-01-01](https://docs.microsoft.com/rest/api/storagerp/?redirectedfrom=MSDN) 

## <a name="next-steps"></a>Passos seguintes

* [Começar a utilizar as ferramentas de desenvolvimento do armazenamento de pilha do Azure](azure-stack-storage-dev.md)
* [Introdução ao armazenamento de pilha do Azure](azure-stack-storage-overview.md)

