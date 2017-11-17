---
title: "Série SKU indisponível | Microsoft Docs"
description: "Alguns série SKU não está disponível para a subscrição para esta região selecionada."
services: Azure Supportability
documentationcenter: 
author: stevendotwang
manager: rajatk
editor: 
ms.service: azure-supportability
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/09/2017
ms.author: xingwan
ms.openlocfilehash: 62964d0c5d75168226a35b25e5c256a1b57f3f81
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/16/2017
---
# <a name="region-or-sku-unavailable"></a>Região ou SKU indisponível
Este artigo descreve como resolver o problema de uma subscrição do Azure não ter acesso a uma região ou um SKU de VM.

## <a name="symptoms"></a>Sintomas

### <a name="when-deploying-a-virtual-machine-you-receive-one-of-the-following-error-messages"></a>Quando implementar uma máquina virtual, receberá uma das seguintes mensagens de erro:
```
Code: SkuNotAvailable
Message: The requested size for resource '<resource>' is currently not available in location 
'<location>' zones '<zone>' for subscription '<subscriptionID>'. Please try another size or 
deploy to a different location or zones. See https://aka.ms/azureskunotavailable for details.
```

```
Message: Your subscription doesn’t support virtual machine creation in <location>. Choose a 
different location. Supported locations are <list of locations>
```

```
Code: NotAvailableForSubscription
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-purchasing-reserved-virtual-machine-instances-you-receive-one-of-the-following-error-messages"></a>Quando a compra de instâncias de Máquina Virtual reservado, receberá uma das seguintes mensagens de erro:

```
Message: Your subscription doesn’t support virtual machine reservation in <location>. Choose a 
different location. Supported locations are: <list of locations>  
```

```
Message: This size is currently unavailable in this location for this subscription
```

### <a name="when-creating-a-support-request-to-increase-compute-core-quota-a-region-or-a-sku-family-is-not-available-for-selection"></a>Ao criar um pedido de suporte para aumentar a quota de núcleos de computação, uma região ou uma família SKU não está disponível para seleção.

## <a name="solution"></a>Solução
Primeiro, recomendamos que considere uma região alternativo ou SKU que satisfaz as suas necessidades de negócio. Se não for possível encontrar uma região adequado ou SKU, criar um "gestão de subscrição" [pedido de suporte](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) seguindo os passos abaixo:


- Na página Noções básicas, selecione o tipo de problema como "Gestão de subscrição", selecione a subscrição e clique em "Seguinte".

![Painel Básico](./media/SKU-series-unavailable/BasicsSubMgmt.png)


-   Na página do problema, selecione o tipo de problema como "Outras perguntas geral".
- Na seção Detalhes:
  - Indique se estiver à procura para implementar máquinas virtuais ou uma compra instâncias de Máquina Virtual reservado
  - Especifique a região, SKU e o número de instâncias de máquina virtual que está a planear implementar ou adquirir


![Problema](./media/SKU-series-unavailable/ProblemSubMgmt.png)

-   Introduza os seus detalhes de contacto e clique em "Criar".

![Informações de Contacto](./media/SKU-series-unavailable/ContactInformation.png)

## <a name="feedback"></a>Comentários
Podemos sempre estão abertos para comentários e sugestões! Envie-na [sugestões](https://feedback.azure.com/forums/266794-support-feedback). Além disso, pode interagir com connosco através de [Twitter](https://twitter.com/azuresupport) ou [fóruns do MSDN](https://social.msdn.microsoft.com/Forums/azure).

## <a name="learn-more"></a>Saiba mais
[FAQ de suporte do Azure](https://azure.microsoft.com/support/faq)

