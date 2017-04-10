---
title: Licenciamento do Azure RemoteApp | Microsoft Docs
description: Saiba como funciona o licenciamento no Azure RemoteApp.
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: ff8ebd20-61a1-4f10-87a6-234a170534c9
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 43d0dbb905b2f2b9d98fb3bf8c073ba1c4b6f4c4
ms.lasthandoff: 03/31/2017


---
# <a name="how-does-licensing-work-in-azure-remoteapp"></a>Como funciona o licenciamento no Azure RemoteApp?
> [!IMPORTANT]
> O Azure RemoteApp vai ser descontinuado a 31 de agosto de 2017. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

Portanto, configurou o serviço Azure RemoteApp, criou os modelos e está preparado para publicar aplicações para os utilizadores. Contudo, ainda existe um último aspeto a compreender: o licenciamento. Como funciona o licenciamento para o RemoteApp e as aplicações partilhadas através do RemoteApp?

O RemoteApp não requer quaisquer licenças do Windows ou CALs de Ambiente de Trabalho Remoto. A sua subscrição trata do RemoteApp. (Consulte os detalhes dos [planos de preços](https://azure.microsoft.com/pricing/details/remoteapp).)

Se utilizar uma das imagens incluídas na subscrição, pode partilhar qualquer uma das aplicações instaladas nessa imagem sem que seja necessária uma licença separada. Por exemplo, se utilizar a imagem de modelo do Windows Server 2012 R2 para criar a coleção, pode partilhar o System Center Endpoint Protection com os utilizadores. As únicas exceções a esta regra são o Office 365 ProPlus, que necessita de uma subscrição separada, e o Office 2013, que não pode ser partilhado numa coleção de produção.

Caso pretenda utilizar a imagem de modelo do Office 365 incluída no RemoteApp, deve ter um plano *existente* do Office 365 ProPlus. O mesmo se aplica a qualquer aplicação do Office 365 publicada através de um modelo personalizado. Deve ativar as aplicações com a sua própria subscrição. Tal aplica-se a subscrições pagas e de avaliação. Se pretender utilizar a imagem de modelo do Office 365 durante a avaliação *e já não tiver uma subscrição*, aceda à página do Office 365 para [inscrever-se](https://go.microsoft.com/fwlink/p/?LinkID=403802) para uma subscrição de avaliação. Consulte [Como o RemoteApp e o Office funcionam em conjunto?](remoteapp-o365.md) para obter mais informações.

Caso, durante o período de avaliação, não pretenda obter uma subscrição de avaliação do Office 365, utilize a imagem de modelo do Office 2013 Professional Plus incluída no RemoteApp. Esta imagem de modelo apenas pode ser utilizada durante 30 dias e não pode ser convertida numa coleção paga.

Para outras aplicações, deve certificar-se de que tem a licença para partilhar a aplicação.

Faz sentido, certo? Pode publicar qualquer aplicação cuja partilha realizada por si esteja legalmente autorizada. Deve certificar-se de que tem, efetivamente, direito de partilhar os programas.

Tenha em atenção que não pode utilizar uma CAL ou um contrato de Licenciamento em Volume numa coleção na nuvem. *Pode* utilizar um contrato de Licenciamento em Volume para ativar aplicações na coleção híbrida (exceto o Office). Tem apenas de instalar aquelas na imagem de modelo do suporte de dados de Licenciamento em Volume. Siga as informações do fabricante da aplicação para instalar licenças num Ambiente de Trabalho Remoto.


