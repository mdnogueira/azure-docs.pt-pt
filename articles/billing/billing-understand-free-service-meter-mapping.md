---
title: "Serviço para medir mapeamento gratuitamente conta - Azure | Microsoft Docs"
description: "Compreenda o serviço para medir o mapeamento para os serviços incluídos em conta gratuita."
services: 
documentationcenter: 
author: amberbhargava
manager: amberb
editor: 
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: amberb
ms.openlocfilehash: 9d7e355e755f2bac8929ab16f7f71aa3b0702658
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="understand-free-service-to-meter-mapping"></a>Compreender o serviço gratuito para o mapeamento do medidor

Todos os serviços do Azure emite utilização contra medidores, que o sistema de faturação do Azure utiliza para cobram utilizadores para os serviços. Para compreender melhor a utilização de services gratuitos, vamos ver o serviço para medir o mapeamento para estes serviços. Para saber como criar os serviços gratuitos, consulte [criar os serviços gratuitos com conta gratuita do Azure](billing-create-free-services-included-free-account.md).

## <a name="service-to-meter-mapping-for-free-account-eligible-services"></a>Serviço para medir mapeamento gratuitamente serviços elegíveis da conta 

|    Serviço   | Nome do medidor no portal do Azure | Nome de medição de utilização/API de ficheiros | ID do Medidor |
| ------------ | -------------------------- | -------------------------| -------- |
| VM do Linux B1S | Horas de computação – Standard_B1 VM | Livres - horas de computação | 8260cba2-4437-47d1-a31e-2561cd370f50
| B1S Windows VM | Horas de computação – Standard_B1 VM (Windows) | Livres - horas de computação | ff3e6fa5-ee46-478e-8d0e-b629f4f8a8ac
| B1S VM - endereços IP públicos  | Horas de endereço IP, endereços IP públicos | Horas de endereço IP - gratuitas | ae56b367-2708-4454-a3d9-2be7b2364ea1
| CosmosDB | Armazenamento (GB) - Cosmos DB | Armazenamento (GB) - gratuito | 59c78b09-08e2-466a-9f3b-57a94c9e2f31
| CosmosDB | 100 unidades de pedido (horas) - Cosmos DB | 100 unidades de pedido (horas) - gratuito | 5d638a6f-e221-41cf-ae3f-0f81d368cef6 
| Armazenamento de Ficheiros | E/s padrão - ficheiros (GB) - redundante localmente | E/s padrão - ficheiros (GB) - gratuito | a7f2aa67-b9a2-4593-a413-6ec86d6c8e5b
| Armazenamento de Ficheiros | E/S Standard - Unidades de Operação de Leitura de Ficheiros (por 10 mil) | E/s padrão - ficheiros leitura unidades de operação de (em 10 000s) - gratuito | 6207404d-3389-4d20-9087-cc078ddc3fd9
| Armazenamento de Ficheiros | E/S Standard - Unidades de Operação de Escrita de Ficheiros (por 10 mil) | E/s padrão - ficheiros unidades de operação de escrita (em 10 000s) - gratuito | 223d8004-d29a-46cf-b4f4-d2d34b12548b
| Armazenamento de Ficheiros | E/S Standard - Unidades de Operação de Protocolo de Ficheiros (por 10 mil) | E/s padrão - ficheiros unidades de operação de protocolo (em 10 000s) - gratuito | a347d8cc-51d1-4a0e-b9eb-76f67566c3f5
| Armazenamento de Ficheiros | E/S Standard - Unidades de Operação de Lista de Ficheiros (por 10 mil) | E/s padrão - ficheiros unidades de operação de lista (em 10 000s) - gratuito | e8ae79ad-c2ab-4d82-b226-dd3c33dfd40c
| Armazenamento de BLOBs de blocos quentes | E/s padrão - operações de leitura de BLOBs de blocos quentes (em 10 000s) | E/s padrão - Blob de bloco quente ler operações (em 10 000s) - gratuitas |fd7cfa1e-026e-4be1-871b-1c2386e8902e
| Armazenamento de BLOBs de blocos quentes | E/s padrão - Blob de bloco quente (GB) - redundante localmente | E/s padrão - Blob de bloco quente (GB) - gratuito | 67a3a3fd-826f-42c1-8843-bffa14f0da13
| Armazenamento de BLOBs de blocos quentes | E/s padrão - operações de escrita de BLOBs de blocos quentes (em 10 000s) | E/s padrão - Blob escrita operações de blocos quentes (em 10 000s) - gratuito | b34bbb76-edce-4c2d-a288-81a2db1fea53
| Armazenamento de BLOBs de blocos quentes  | E/s padrão - operações de escrita/lista de BLOBs de blocos quentes (em 10 000s) | E/s padrão - Blob escrita/lista operações de blocos quentes (em 10 000s) - gratuito | 7e68cf36-1198-4d3b-baa7-86a74c5b3079
| Gerido disco *  | Disco gerido Standard/instantâneos (GB) - redundante localmente | Disco gerido Standard/instantâneos (GB) - gratuito | ad94c237-52a5-4804-ae65-38c5bf85ef42
| Gerido disco *  | Operações de disco gerido Standard (em 10 000s) | Disco gerido Standard operações (em 10 000s) - gratuitas | 82cc6ea4-0abd-43ac-ACC0-ec34edf0f14c
| Gerido disco *  | Armazenamento Premium - Blob de páginas/P6 (unidades) - redundante localmente | Armazenamento Premium - Blob de páginas/P6 (unidades) - gratuito | 2b98c168-27CA-4cc1-b509-e887dec87657
| Base de Dados SQL | Dias de base de dados no padrão S0 - base de dados SQL | Dias de base de dados no padrão S0 - gratuitos | dd6b69d3-9be0-4a91-abff-2c58bbcafd1d
| Partilhado - largura de banda * * | Transferências de Dados de Saída (GB) | Transferência de dados enviados (GB) - gratuito | 0fc067a1-65d2-46da-b24b-7a9cbe2c69bd

\*Se criar uma máquina virtual do Windows e escolha disco gerido, irá consumir medição de disco gerido como parte da máquina virtual.

\** Podem ser consumidos medidores partilhados através de vários serviços. Por exemplo, máquinas virtuais e armazenamento emitir utilização contra Out(GB) de transferência de dados de medição.





## <a name="need-help-contact-support"></a>Precisa de ajuda? Contactar o suporte

Se precisar de ajuda, [contacte o suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) para obter o seu problema resolvido rapidamente.
