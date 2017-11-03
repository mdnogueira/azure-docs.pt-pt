---
title: "Testar a sua oferta de serviço de dados para o Marketplace | Microsoft Docs"
description: "Compreenda como testar a sua oferta de serviço de dados para o Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: e861bd11-f74d-4d77-b4b5-23fb463644ad
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2016
ms.author: hascipio; avikova
ms.openlocfilehash: 56a8aad7484fed18b74200ffa7acf22363625a15
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="testing-your-data-service-offer-in-staging"></a>Testar a sua oferta de serviço de dados no modo de transição
> [!IMPORTANT]
> **Neste momento, estamos já não integração qualquer nova editores de serviço de dados. Novo dataservices não irá obter aprovada para listagem.** Se tiver uma aplicação de negócio de SaaS que pretende publicar no AppSource pode encontrar mais informações [aqui](https://appsource.microsoft.com/partners). Se tiver um aplicações IaaS ou serviço do programador que pretende publicar no Azure Marketplace, pode encontrar mais informações [aqui](https://azure.microsoft.com/marketplace/programs/certified/).
> 
> 

Depois de concluir os primeiros dois passos de [criar a sua conta Microsoft Developer](marketplace-publishing-accounts-creation-registration.md) e [criar a sua oferta de serviço de dados de mensagens em fila no Portal de publicação](marketplace-publishing-data-service-creation.md) está pronto para disponibilizar a oferta do Azure Marketplace. Este tópico explica como o primeiro, intermédio, passo chamado "Transição"

Transição significa implementar a sua oferta no privado "sandbox", onde pode testar e verificar a respetiva funcionalidade antes de enviá-lo para produção. A oferta irão aparecer na transição faria para um cliente que tenha implementado.

## <a name="step-1-pushing-your-offer-to-staging"></a>Passo 1. Enviar a sua oferta para teste
Enviar a sua oferta para teste permite-lhe testar a oferta para ficar disponível para subscritores futuros.  Pode ver como oferta irá aparecer e os subscrever os dados de função.  

  ![desenho](media/marketplace-publishing-data-service-test-in-staging/step-1.1.png)

1. Início de sessão para o [Portal de publicação](https://publish.windowsazure.com)
2. Selecione **serviços de dados** na janela de navegação esquerdo
3. Selecione a sua oferta que pretende enviar para o teste. Verá o ecrã acima.
4. Clique em **Push para o teste** botão.  
5. Se existirem problemas com a oferta que necessária para ser concluída antes de enviar para o teste, verá uma lista apresentada.  Corrija estes itens clicando em cada item na lista. Quando todas as correções efetuadas, clique em **Push transição** botão novamente.

Se existirem sem problemas com a sua oferta, verá a janela de pop-up abaixo.  

Se o estiver a planear não/não aprovados a anexação a oferta no Portal do Azure (atualmente limitou capacidade), em seguida, apenas a fechar a janela de pop-up.

Para testar o seu serviço de dados no Portal do Azure (para além de DataMarket portal), será necessário um ID de subscrição do Azure para testar com.  Este ID de subscrição irá identificar a conta que terá permissão para testar a sua oferta.  

Cortar e colar o ID de subscrição e clique na marca de verificação para continuar.

  ![desenho](media/marketplace-publishing-data-service-test-in-staging/step-1.2.png)

> [!NOTE]
> Estes IDs de subscrições do Azure só são necessárias para teste e transição no [Azure Management Portal](https://manage.windowsazure.com). Não são necessários para testar no Azure Marketplace.
> 
> 

O aparecimento do ecrã que aparece mostra que a publicação está a decorrer, apresentando no ícone "em curso" realçado amarelo abaixo. Enviar para transição demora entre 10 a 15 minutos.  Se demora mais, atualize primeiro o seu browser (prima F5 no IE).  Nos casos raros onde a oferta é ainda enviar por push para transição depois de uma hora, clique em Contacte-na associar ao indique que há um problema.

  ![desenho](media/marketplace-publishing-data-service-test-in-staging/step-1.3.png)

Quando o Push de teste estiver concluído no ícone "em curso" irão mover de paragem e o estado serão atualizadas para "Teste".  Agora está pronto para testar a sua oferta.  

## <a name="step-2-test-your-staged-offer-in-datamarket"></a>Passo 2. Testar a sua oferta faseada DataMarket
Clique na hiperligação seguinte texto **"consulte o seu serviço oferecem em..."** para apresentar o ecrã se o subscritor verá quando a sua oferta vai para produção e aparecerá na DataMarket.

  ![desenho](media/marketplace-publishing-data-service-test-in-staging/step-2.2.png)

Testar ou certifique-se de que cada um dos 12 itens marcados acima para garantir que todos os logótipos os preços/transações, texto, imagens, documentação e as ligações estão corretos e a funcionar corretamente.  Esta é uma boa altura para garantir que quaisquer valores de teste que introduziu ao criar a sua oferta foram substituídos por valores reais.

1. Logótipo da oferta
2. Nome da oferta
3. Editor nome/de ligação ao site da sua empresa
4. Categorias de pesquisa para a sua oferta
5. Hiperligação de suporte da sua oferta para o ajudar a subscritores
6. Contextual descrição para a sua oferta
7. Plano de oferta que mostram os detalhes de faturação
8. Associar ao código de implementação
9. Imagens de exemplo que ilustram a utilização de dados da oferta
10. Metadados de entrada/saída para cada serviço dentro da oferta
11. Termos do oferta de utilização
12. Pré-visualização de dados da oferta

Por fim, verifique o serviço irá funcionar através de Datamarket clicando na hiperligação "EXPLORAR este conjunto de dados".  Irá abrir uma nova janela na ferramenta chamamos "Explorador de serviço", de modo pode pré-visualizar os resultados da consulta relativamente ao seu serviço.  Nesta janela, pode introduzir os parâmetros necessários e ver os resultados apresentados por uma consulta relativamente ao seu serviço.   Além disso, apresentado é o URL para a sua consulta.  

> [!NOTE]
> Lembre-se de que reveja a descrição textual do serviço apresentada na parte superior.  E se a oferta é composta por mais do que um serviço chamar, clique nos separadores na parte inferior para mudar para o serviço seguinte para rever e testar.
> 
> 

## <a name="next-step"></a>Passo seguinte
Se estiver a ter problemas e precisar de ajuda para resolvê-los contacte [suporte do Editor de Azure](http://go.microsoft.com/fwlink/?LinkId=272975).

Se estiver satisfeito e pronto para publicar a sua oferta, leia o [solicitar aprovação para Push para produção](marketplace-publishing-push-to-production.md) documentação.

## <a name="see-also"></a>Veja Também
* [Introdução: Como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)

