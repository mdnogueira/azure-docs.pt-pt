---
title: Testar a sua oferta VM para o Marketplace | Microsoft Docs
description: Compreenda como a imagem VM de teste para o Azure Marketplace.
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 7a41c3c6-625c-4478-b804-e124dee89040
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: hascipio
ms.openlocfilehash: 26f856059b381be91b9cdd1f98a11dc90813c0c5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="test-your-vm-offer-for-the-azure-marketplace-in-staging"></a>Testar a sua oferta VM para o Azure Marketplace em transição
Transição significa implementar o SKU no privado "sandbox", onde pode testar e validar a sua funcionalidade antes de o implementar no Marketplace. O SKU não aparece na transição faria para um cliente que tenha implementado. A imagem VM tem de ser certificada para ser enviado para o teste.

## <a name="step-1-push-your-offer-to-staging"></a>Passo 1: Push oferta para teste
1. No **publicar** separador, clique em **Push transição**.
   
    ![desenho](media/marketplace-publishing-vm-image-test-in-staging/vm-image-push-to-staging.png)
2. Se o Portal de publicação notifica-o de erros, corrija-os.
3. No **quem pode aceder à sua oferta testada?** caixa de diálogo, introduza a lista de subscrições do Azure que irá utilizar para pré-visualizar a sua oferta no [portal de pré-visualização do Azure](https://portal.azure.com).
   
   > [!NOTE]
   > Em caso de máquinas virtuais e modelos de solução,. **não** subscrições da lista branca do tipo CSP, DreamSpark ou do Azure no Open.
   > 
   > 

    > Em caso de máquinas virtuais, ao clicar no botão **PUSH para o teste**, os passos seguintes são executados atrás cena. Poderá ver o progresso de cada passo no separador Publicar na publicação portal. Tem de verificar esta página num intervalo regular (até que o estado é apresentado teste) para qualquer informação de falha que precisam de correção da sua end.

    > - Em primeiro lugar o pedido de teste vai para a equipa de certificação que validar o vhd. No entanto, se o pedido tem tem apenas alterações de marketing, em seguida, o passo de certificação é ignorado.
    > - Assim que a certificação estiver concluída, da oferta de início da replicação em todos os centros de dados do Azure. Geralmente, demora 24-48hours a conclusão da replicação mas poderá demorar até uma semana, consoante o tamanho do vhd. No entanto, se o pedido tem tem apenas alterações de marketing, em seguida, a replicação é mais rápida.
    > - Quando a replicação estiver concluída, em seguida, a oferta estará disponível o [portal do Azure](http:/portal.azure.com). AT que o estado do tempo ficar TESTADO na publicação portal. Uma oferta pré-configurada está visível no [portal do Azure](http:/portal.azure.com) apenas utilizando os indicados de correio eletrónico associados à subscrição com o qual a oferta é testada.

1. Iniciar sessão para o [portal de pré-visualização do Azure](https://portal.azure.com) utilizando uma das subscrições Azure listadas no passo anterior.
2. Localizar a sua oferta e validar os pontos de imagem VM:
   
   * Certifique-se de que marketing conteúdo apresentado corretamente no Marketplace.
   * Implementação de ponto a ponto da imagem de VM.
     
      ![portal de mapa img](media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg)

> [!IMPORTANT]
> A oferta irá permanecer na transição até notificar Microsoft através do Portal de publicação [**publicar** separador > clique no botão **"Pedir aprovação para Push para Production"**] que está pronto para emitir para produção. Este é um ideal têm todos os membros da sua equipa de verificação sobre tudo em preparação para a sua oferta vai listadas.
> 
> A plataforma de teste foi concebida para testar a oferta no modo de pré-visualização pelo fabricante. É vivamente desencorajar-se utilizar esta platofrm para fins de commerical.
> 
> 

## <a name="next-steps"></a>Passos seguintes
Agora que a oferta é "teste" e tiver testado as suas funcionalidades e conteúdo de marketing, pode avançar para a fase final de publicação, **passo 4**: [implementar oferta do Marketplace](marketplace-publishing-push-to-production.md).

## <a name="see-also"></a>Consultar também
* [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)

