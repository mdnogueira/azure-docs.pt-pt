---
title: "Não técnicos pré-requisitos para criar uma oferta para o Azure Marketplace | Microsoft Docs"
description: Compreenda os requisitos para criar e implementar uma oferta no Azure Marketplace para outras pessoas para comprar.
services: marketplace-publishing
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: 3dae463b-8f48-4f52-8fa8-4e3975f09f43
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/18/2016
ms.author: hascipio
ms.openlocfilehash: 4f86d444a2f2b97fd8605d480db358813bc39fd3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="general-prerequisites-for-creating-an-offer-for-the-azure-marketplace"></a>Pré-requisitos gerais para criar uma oferta para o Azure Marketplace
Compreenda os pré-requisitos gerais, negócio processo-centrado em que são necessárias para avançar com o processo de criação da oferta.

## <a name="ensure-that-you-are-registered-as-a-seller-with-microsoft"></a>Certifique-se de que estão registados como um vendedor com a Microsoft
Para instruções detalhadas sobre como registar uma conta de vendedor com a Microsoft, aceda a [conta criação e registo](marketplace-publishing-accounts-creation-registration.md).

* **Se a sua empresa já está registada como um vendedor no Dev Center e de que pretende criar uma nova oferta,** , em seguida, início de sessão para a publicação portal com o mesmo id de correio eletrónico com que Dev Center registo foi efetuado. Este passo é necessário para que o portal Dev Center e publicação estão ligados entre si.
* **Se a sua empresa já está registada como um vendedor no Dev Center e de que deseja editar uma oferta existente,** , em seguida, o início de sessão para a publicação portal com a conta de administrador ou com uma conta que é adicionada como coadministrador na publicação do portal. Passos para adicionar uma conta de coadministrador são indicados abaixo.

## <a name="steps-to-add-a-co-admin-in-the-publishing-portal"></a>Passos para adicionar um coadministrador no Portal de publicação
Administradores da publicação portal pode adicionar os outros membros da empresa, que estiver a trabalhar na aplicação, como um coadministrador na publicação portal. **Partindo do princípio que são o administrador,** indicados abaixo estão os passos para adicionar um co-administrador.

> [!NOTE]
> Para os novos utilizadores, antes adiciona um coadministrador a publicação portal, certifique-se de que criou, pelo menos, uma aplicação a publicação do portal. Isto é necessário como o **PUBLICADORES** separador são apresentados apenas depois de criar pelo menos uma aplicação na publicação portal.
> 
> 

1. Certifique-se de que o id de correio eletrónico coadministrador um account(MSA) da Microsoft. Se não estiver, registe-o como uma MSA com este [ligação](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Certifique-se de que existe pelo menos uma aplicação com a conta de administrador antes de tentar adicionar um co-administrador.
3. Depois de terminar os passos acima, início de sessão para a publicação portal com o id de correio eletrónico de coadministrador e, em seguida, a fim de sessão.
4. Agora início de sessão para a publicação portal com o id de correio eletrónico de admin.
5. Navegue para publicadores -> selecione a sua conta -> administradores -> Adicionar coadministrador (captura de ecrã indicada abaixo)
   
    ![desenho](media/marketplace-publishing-pre-requisites/imgAddAdmin_05.png)
6. Certifique-se de que os ids de correio eletrónico fornecidos em várias fases do processo de publicação (por exemplo, Dev Center, publicação portal) são monitorizados para qualquer comunicação da Microsoft.
7. Para o registo de Dev Center Evite utilizar uma conta associada a uma única pessoa. Isto é sugerido para remover a dependência de um indivíduo.
8. Se enfrentam quaisquer problemas com o registo de Dev Center, em seguida, volte emitir um pedido de utilizar esta [ligação](https://developer.microsoft.com/en-us/windows/support).

## <a name="steps-to-delete-a-co-admin-in-the-publishing-portal"></a>Passos para eliminar um coadministrador na publicação portal
**Partindo do princípio que são o administrador,** indicados abaixo estão os passos para eliminar um co-administrador.

1. Início de sessão para a publicação portal com o id de correio eletrónico de admin.
2. Navegue para **publicadores** -> selecione a sua conta -> **administradores** -> **Coadministradores**.
3. Clique em de **X** junto coadministrador que pretende eliminar tot (captura de ecrã indicada abaixo).
   
    ![desenho](media/marketplace-publishing-pre-requisites/imgDeleteAdmin_03.png)

> [!IMPORTANT]
> Não é necessário que concluir a dedução dos impostos e banco as informações da empresa, se estiver a planear publicar ofertas apenas livres (ou utilizar a sua própria licença).
> 
> O registo da empresa têm de ser concluído para começar a utilizar. No entanto, enquanto a sua empresa funciona nas informações dedução dos impostos e banco na conta do Microsoft Developer, os programadores podem começar a trabalhar sobre como criar a imagem de máquina virtual no [Portal publicação](https://publish.windowsazure.com), recuperá-la certificadas e testá-lo no ambiente de teste do Azure. Terá da aprovação de conta vendedor completos apenas para o passo final de publicação da oferta no Azure Marketplace.
> 
> 

## <a name="acquire-an-azure-pay-as-you-go-subscription"></a>Adquirir uma subscrição do Azure "pay as you go"
Esta é a subscrição que irá utilizar para criar as imagens VM e entregar através de imagens para a [Azure Marketplace](https://azure.microsoft.com/marketplace/). Se não tiver uma subscrição existente, em seguida, inscrever-se no https://account.windowsazure.com/signup?offer=ms-azr-0003p.

## <a name="sell-from-countries"></a>"Vende de" países
> [!WARNING]
> Para poder propor os serviços no Azure Marketplace, tem de se certificar de que a entidade registada é a partir de um dos países aprovados "vende-de". Esta restrição é por razões de dividendos e taxation. Iremos ativamente procura para expandir esta lista de países num futuro próximo, por isso, esteja atento e não. Para a lista completa, consulte a secção 1b do [políticas de participação do Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).
> 
> 

## <a name="next-steps"></a>Passos seguintes
Depois dos pré-requisitos não técnicos estiverem concluídos, em seguida é a oferta pré-requisitos técnicos específicos. Clique na ligação para o artigo para o tipo de oferta respetivos que gostaria de criar para o Azure Marketplace.

* [VM técnicas pré-requisitos](marketplace-publishing-vm-image-creation-prerequisites.md)
* [Solução modelo técnicas pré-requisitos](marketplace-publishing-solution-template-creation-prerequisites.md)

## <a name="see-also"></a>Consultar também
* [Introdução: como publicar uma oferta no Azure Marketplace](marketplace-publishing-getting-started.md)

