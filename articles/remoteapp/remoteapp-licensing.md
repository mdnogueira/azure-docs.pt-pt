<properties
    pageTitle="Licenciamento do Azure RemoteApp | Microsoft Azure"
    description="Saiba como funciona o licenciamento no Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="05/18/2016"
    ms.author="elizapo" />


# Como funciona o licenciamento no Azure RemoteApp?


Portanto, configurou o serviço Azure RemoteApp, criou os modelos e está preparado para publicar aplicações para os utilizadores. Contudo, ainda existe um último aspeto a compreender: o licenciamento. Como funciona o licenciamento para o RemoteApp e as aplicações partilhadas através do RemoteApp?

O RemoteApp não requer quaisquer licenças do Windows ou CALs de Ambiente de Trabalho Remoto. A sua subscrição trata do RemoteApp. (Consulte os detalhes dos [planos de preços](https://azure.microsoft.com/pricing/details/remoteapp).)

Se utilizar uma das imagens incluídas na subscrição, pode partilhar qualquer uma das aplicações instaladas nessa imagem sem que seja necessária uma licença separada. Por exemplo, se utilizar a imagem de modelo do Windows Server 2012 R2 para criar a coleção, pode partilhar o System Center Endpoint Protection com os utilizadores. As únicas exceções a esta regra são o Office 365 ProPlus, que necessita de uma subscrição separada, e o Office 2013, que não pode ser partilhado numa coleção de produção.

Caso pretenda utilizar a imagem de modelo do Office 365 incluída no RemoteApp, deve ter um plano *existente* do Office 365 ProPlus. O mesmo se aplica a qualquer aplicação do Office 365 publicada através de um modelo personalizado. Deve ativar as aplicações com a sua própria subscrição. Tal aplica-se a subscrições pagas e de avaliação. Se pretender utilizar a imagem de modelo do Office 365 durante a avaliação *e já não tiver uma subscrição*, aceda à página do Office 365 para [inscrever-se](https://go.microsoft.com/fwlink/p/?LinkID=403802) para uma subscrição de avaliação. Consulte [Como o RemoteApp e o Office funcionam em conjunto?](remoteapp-o365.md) para obter mais informações.

Caso, durante o período de avaliação, não pretenda obter uma subscrição de avaliação do Office 365, utilize a imagem de modelo do Office 2013 Professional Plus incluída no RemoteApp. Esta imagem de modelo apenas pode ser utilizada durante 30 dias e não pode ser convertida numa coleção paga.

Para outras aplicações, deve certificar-se de que tem a licença para partilhar a aplicação.

Faz sentido, certo? Pode publicar qualquer aplicação cuja partilha realizada por si esteja legalmente autorizada. Deve certificar-se de que tem, efetivamente, direito de partilhar os programas.

Tenha em atenção que não pode utilizar uma CAL ou um contrato de Licenciamento em Volume numa coleção na nuvem. *Pode* utilizar um contrato de Licenciamento em Volume para ativar aplicações na coleção híbrida (exceto o Office). Tem apenas de instalar aquelas na imagem de modelo do suporte de dados de Licenciamento em Volume. Siga as informações do fabricante da aplicação para instalar licenças num Ambiente de Trabalho Remoto.



<!--HONumber=Jun16_HO2-->


