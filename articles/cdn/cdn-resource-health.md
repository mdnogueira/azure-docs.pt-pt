---
title: Monitorizar o estado de funcionamento dos recursos do Azure CDN | Microsoft Docs
description: Saiba como monitorizar o estado de funcionamento dos seus recursos da CDN do Azure com o estado de funcionamento de recursos de Azure.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: 
ms.assetid: bf23bd89-35b2-4aca-ac7f-68ee02953f31
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 37fe208f5087f318e665e76825127854b4a11c98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Monitorizar o estado de funcionamento dos recursos da CDN do Azure
  
Estado de funcionamento de recursos de CDN do Azure é um subconjunto de [estado de funcionamento de recursos do Azure](../resource-health/resource-health-overview.md).  Pode utilizar o estado de funcionamento de recursos do Azure para monitorizar o estado de funcionamento dos recursos da CDN e receber acionável orientações para resolução de problemas.

>[!IMPORTANT] 
>Estado de funcionamento de recursos do Azure CDN contas atualmente apenas para o estado de funcionamento global entrega da CDN e as capacidades de API.  Estado de funcionamento de recursos do Azure CDN não verifica os pontos finais da CDN individuais.
>
>Os sinais de feed de estado de funcionamento de recursos de CDN do Azure podem ser até 15 minutos atrasados.

## <a name="how-to-find-azure-cdn-resource-health"></a>Como localizar o estado de funcionamento de recursos de CDN do Azure

1. No [portal do Azure](https://portal.azure.com), navegue para o perfil de CDN.

2. Clique em de **definições** botão.

    ![Botão de definições](./media/cdn-resource-health/cdn-profile-settings.png)

3. Em *suporte + resolução de problemas*, clique em **estado de funcionamento do recurso**.

    ![Estado de funcionamento de recursos CDN](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>Também pode encontrar os recursos CDN listados no *estado de funcionamento do recurso* na peça de mosaico do *ajuda + suporte* painel.  Pode obter rapidamente para *ajuda + suporte* clicando a dentro de um círculo **?** no canto superior direito do portal.
>
> ![Ajuda + suporte](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Mensagens de específicos da CDN do Azure

Estados relacionados com o estado de funcionamento de recursos de CDN do Azure podem ser encontrados abaixo.

|Mensagem | Ação recomendada |
|---|---|
|Poderá ter parado, removidos ou configurado incorretamente um ou mais dos seus pontos finais da CDN | Poderá ter parado, removidos ou configurado incorretamente um ou mais dos seus pontos finais da CDN.|
|Lamentamos, o serviço de gestão da CDN está indisponível | Verifique novamente aqui para atualizações de estado Se o problema persistir após o tempo de resolução previsto, contacte o suporte.|
|Pedimos desculpa, que os pontos finais da CDN podem ser afetados por problemas em curso com alguns dos nossos fornecedores CDN | Verifique novamente aqui para atualizações de estado Utilize a ferramenta de resolução de problemas para saber como testar a sua origem e o ponto final de CDN Se o problema persistir após o tempo de resolução previsto, contacte o suporte. |
|Lamentamos, as alterações de configuração de ponto final CDN sofram atrasos de propagação | Verifique novamente aqui para atualizações de estado Se as alterações de configuração não são propagadas para completamente no prazo previsto, contacte o suporte.|
|Lamentamos, que mas está a ter problemas ao carregar o portal suplementar | Verifique novamente aqui para atualizações de estado Se o problema persistir após o tempo de resolução previsto, contacte o suporte.|
Lamentamos, que mas está a ter problemas com alguns dos nossos fornecedores CDN | Verifique novamente aqui para atualizações de estado Se o problema persistir após o tempo de resolução previsto, contacte o suporte. |

## <a name="next-steps"></a>Passos seguintes

- [Leu uma descrição geral do Estado de funcionamento de recursos do Azure](../resource-health/resource-health-overview.md)
- [Resolver problemas com a compressão de CDN](./cdn-troubleshoot-compression.md)
- [Resolver problemas com 404 erros](./cdn-troubleshoot-endpoint.md)