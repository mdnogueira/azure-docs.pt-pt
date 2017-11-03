---
title: "O que fazer em caso de um Azure service interrupção que afeta o Cofre de chaves do Azure | Microsoft Docs"
description: "Saiba o que fazer em caso de uma interrupção do serviço do Azure que afeta o Cofre de chaves do Azure."
services: key-vault
documentationcenter: 
author: adamglick
manager: mbaldwin
editor: 
ms.assetid: 19a9af63-3032-447b-9d1a-b0125f384edb
ms.service: key-vault
ms.workload: key-vault
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2017
ms.author: sumedhb;aglick
ms.openlocfilehash: 6419d54c54e7d19103419262b79e7a5268b2268c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Disponibilidade do Cofre de chaves do Azure e de redundância
O Cofre de chaves do Azure inclui várias camadas de redundância para se certificar de que as chaves e segredos permanecem disponíveis para a sua aplicação, mesmo se os componentes individuais do serviço falharem.

O conteúdo do seu Cofre de chaves é replicado na região e para uma região secundária, pelo menos, 150 quilómetros ausente mas a mesma geografia. Isto mantém elevada durabilidade das suas chaves e segredos. Consulte o [Azure emparelhado regiões](https://docs.microsoft.com/en-us/azure/best-practices-availability-paired-regions) documento para obter detalhes sobre os pares de região específica.

Se falharem a componentes individuais no serviço Cofre de chaves, alternativos componentes na região passo para servir o pedido para se certificar de que não há nenhum degradação de funcionalidade. Não é necessário efetuar qualquer ação para acionar esta. -Ocorre automaticamente e será transparente para si.

No evento raro uma região do Azure completa não está disponível, os pedidos efetuadas do Cofre de chaves do Azure nessa região são encaminhados automaticamente (*pós-falha*) para uma região secundária. Quando a região primária estiver novamente disponível, os pedidos são encaminhados novamente (*falha novamente*) para a região primária. Novamente, não é necessário efetuar qualquer ação porque isto ocorre automaticamente.

Existem algumas limitações a ter em consideração:

* Em caso de uma ativação pós-falha de região, poderá demorar alguns minutos para que o serviço de ativação pós-falha. Os pedidos efetuados, durante este tempo poderão falhar até que conclua a ativação pós-falha.
* Após uma ativação pós-falha estiver concluída, o seu Cofre de chaves está em modo só de leitura. Se a pedidos que são suportados neste modo:
  * Lista cofres de chaves
  * Obter propriedades de cofres de chaves
  * Lista os segredos
  * Obter segredos
  * Lista de chaves
  * Obter chaves (propriedades do)
  * Encriptar
  * Desencriptar
  * Moldar
  * Desenrolar
  * Verificar
  * Início de sessão
  * Cópia de segurança
* Após uma ativação pós-falha é falha novamente, todos os tipos de pedido (incluindo leitura *e* pedidos de escrita) estão disponíveis.

