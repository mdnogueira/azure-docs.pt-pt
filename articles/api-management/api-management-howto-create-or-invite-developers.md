---
title: Como gerir contas de utilizador na API Management do Azure | Microsoft Docs
description: Saiba como criar ou convidar os utilizadores na API Management do Azure
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 078abfa5-1e4f-4c9d-b9c7-a172bd19c1a2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 34343194cb9896916cb02cb8acf61af24f0784c9
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-manage-user-accounts-in-azure-api-management"></a>Como gerir contas de utilizador na API Management do Azure
Na API Management, os programadores são os utilizadores com as APIs que expõe a utilizar a API Management. Este guia mostra a como criar e convidar programadores para utilizar as APIs e os produtos que disponibiliza aos mesmos com a instância de API Management. Para informações sobre como gerir contas de utilizador através de programação, consulte o [entidade utilizador](https://msdn.microsoft.com/library/azure/dn776330.aspx) documentação o [API REST de gestão](https://msdn.microsoft.com/library/azure/dn776326.aspx) referência.

## <a name="create-developer"></a>Criar um novo Programador
Para criar um novo programador, clique em **portal do publicador** no Portal do Azure para o seu serviço de API Management. Isto leva-o para o portal do publicador da API Management. Se ainda não criou uma instância de serviço de Gestão de API, consulte [Criar uma instância de serviço de Gestão de API][Create an API Management service instance] no tutorial [Introdução à Gestão de API do Azure][Get started with Azure API Management].

![Portal do publicador][api-management-management-console]

Clique em **utilizadores** do **API Management** menu à esquerda e, em seguida, clique em **adicionar utilizador**.

![Criar o programador][api-management-create-developer]

Introduza o **E-Mail**, **palavra-passe**, e **nome** para o programador nova e clique em **guardar**.

![Criar o programador][api-management-add-new-user]

Por predefinição, são contas de programador recém-criado **Active Directory**e associados a **programadores** grupo.

![Novo para programadores][api-management-new-developer]

Contas de programador, que estão num **Active Directory** Estado pode ser utilizado para aceder a todas as APIs para o qual têm subscrições. Para associar o programador recentemente criado grupos adicionais, consulte [como associar grupos a programadores][How to associate groups with developers].

## <a name="invite-developer"></a>Convidar um programador
Para convidar um programador, clique em **utilizadores** do **API Management** menu à esquerda e, em seguida, clique em **convidar utilizador**.

![Convidar programadores][api-management-invite-developer]

Introduza o nome e endereço de e-mail do programador e clique em **convidar**.

![Convidar programadores][api-management-invite-developer-window]

É apresentada uma mensagem de confirmação, mas o programador recentemente convidado não aparecer na lista de até depois de serem aceitarem o convite. 

![Convidar confirmação][api-management-invite-developer-confirmation]

Quando um programador está convidado, é enviado um e-mail para o programador. Este e-mail é gerado utilizando um modelo e é personalizável. Para obter mais informações, consulte [configurar modelos de e-mail][Configure email templates].

Depois do convite é aceite, a conta fica ativa.

## <a name="block-developer"></a> Desativar ou reativar uma conta de programador
Por predefinição, são contas de programador recentemente criados ou convidados **Active Directory**. Para desativar uma conta de programador, clique em **bloco**. Para reativar uma conta de programador bloqueadas, clique em **ativar**. Uma conta de programador bloqueadas não pode aceder ao portal de programador ou chamar as APIs. Para eliminar uma conta de utilizador, clique em **eliminar**.

![Para programadores do bloco][api-management-new-developer]

## <a name="reset-a-user-password"></a>Repor uma palavra-passe do utilizador
Para repor a palavra-passe para uma conta de utilizador, clique no nome da conta.

![Repor palavra-passe][api-management-view-developer]

Clique em **Repor palavra-passe** para enviar uma ligação para o utilizador para repor a palavra-passe.

![Repor palavra-passe][api-management-reset-password]

Para trabalhar programaticamente com contas de utilizador, consulte o [entidade utilizador](https://msdn.microsoft.com/library/azure/dn776330.aspx) documentação o [API REST de gestão](https://msdn.microsoft.com/library/azure/dn776326.aspx) referência. Para repor uma palavra-passe da conta de utilizador para um valor específico, pode utilizar o [atualizar um utilizador](https://msdn.microsoft.com/library/azure/dn776330.aspx#UpdateUser) operação e especifique a palavra-passe pretendida.

## <a name="pending-verification"></a>Verificação pendentes
![Verificação pendentes][api-management-pending-verification]

## <a name="next-steps"> </a>Passos seguintes
Quando é criada uma conta de programador, pode associá-lo com as funções e subscrever os produtos e APIs. Para obter mais informações, consulte [como criar e utilizar grupos][How to create and use groups].

[api-management-management-console]: ./media/api-management-howto-create-or-invite-developers/api-management-management-console.png
[api-management-add-new-user]: ./media/api-management-howto-create-or-invite-developers/api-management-add-new-user.png
[api-management-create-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-create-developer.png
[api-management-invite-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer.png
[api-management-new-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-new-developer.png
[api-management-invite-developer-window]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-window.png
[api-management-invite-developer-confirmation]: ./media/api-management-howto-create-or-invite-developers/api-management-invite-developer-confirmation.png
[api-management-pending-verification]: ./media/api-management-howto-create-or-invite-developers/api-management-pending-verification.png
[api-management-view-developer]: ./media/api-management-howto-create-or-invite-developers/api-management-view-developer.png
[api-management-reset-password]: ./media/api-management-howto-create-or-invite-developers/api-management-reset-password.png


[Create a new developer]: #create-developer
[Invite a developer]: #invite-developer
[Deactivate or reactivate a developer account]: #block-developer
[Next steps]: #next-steps
[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Configure email templates]: api-management-howto-configure-notifications.md#email-templates
