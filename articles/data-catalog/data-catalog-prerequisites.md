---
title: "Os pré-requisitos de catálogo de dados do Azure | Microsoft Docs"
description: "Saiba mais sobre os pré-requisitos que necessita para começar com o catálogo de dados do Azure."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: ef497a54-dc4d-4820-b5bf-c361b64b964d
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 10/01/2017
ms.author: maroche
ms.openlocfilehash: 7d4ed0cc06e8b218b2b63c1d899701ba2eafd648
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2017
---
# <a name="azure-data-catalog-prerequisites"></a>Pré-requisitos do Catálogo de Dados do Azure

Terá algumas coisas asseguramos antes de poder configurar o catálogo de dados do Azure. Não se preocupe, este processo não demorar.

## <a name="azure-subscription"></a>Subscrição do Azure
Para configurar o catálogo de dados, tem de ser o proprietário ou coproprietário de uma subscrição do Azure.

As subscrições do Azure ajudam-na organizar acesso aos recursos do serviço em nuvem, tais como o catálogo de dados. As subscrições também ajudam a controlar como a utilização de recursos é comunicada, faturada e paga para. Cada subscrição pode ter uma configuração de faturação e pagamento separada, para que possa ter subscrições e planos variam consoante o departamento, projeto, escritório regional e assim sucessivamente. Cada serviço em nuvem pertence a uma subscrição e tem de ter uma subscrição antes de configurar o catálogo de dados. Para saber mais, veja [Gerir contas, subscrições e funções administrativas](../active-directory/active-directory-assign-admin-roles-azure-portal.md).

## <a name="azure-active-directory"></a>Azure Active Directory
Para configurar o catálogo de dados, tem de ser assinado com uma conta de utilizador do Azure Active Directory (Azure AD).

O Azure AD proporciona uma forma fácil de a sua empresa gerir a identidade e o acesso, tanto na nuvem, como no local. Os utilizadores podem utilizar uma única conta escolar ou profissional para o início de sessão único para qualquer nuvem e no local aplicação web. Catálogo de dados utiliza o Azure AD para autenticar o início de sessão. Para obter mais informações, consulte [que é o Azure Active Directory?](../active-directory/active-directory-whatis.md).

> [!NOTE]
> Utilizando o [portal do Azure](http://portal.azure.com/), pode iniciar sessão com uma conta Microsoft pessoal ou um Azure Active Directory conta escolar ou profissional. Para configurar o catálogo de dados através do portal do Azure ou o [portal do catálogo de dados](http://www.azuredatacatalog.com), tem de iniciar sessão com uma conta do Azure Active Directory, não uma conta pessoal.
>
>

## <a name="active-directory-policy-configuration"></a>Configuração de política do Active Directory
Poderá encontrar uma situação em que pode iniciar sessão portal do catálogo de dados, mas quando tenta iniciar sessão para a ferramenta de registo da origem de dados, tiver uma mensagem de erro que o impede de início de sessão. Este comportamento de problema pode ocorrer apenas quando está na rede da empresa, ou pode ocorrer apenas quando estiver a ligar a partir de fora da rede da empresa.

A ferramenta de registo da origem de dados utiliza a autenticação de formulários para validar as credenciais de utilizador no Active Directory. Para ajudar a iniciar sessão com êxito, o administrador do Active Directory tem de ativar a autenticação de formulários na política de autenticação Global.

Na política de autenticação Global, métodos de autenticação podem ser ativados em separado para intranet e extranet ligações, conforme mostrado na captura de ecrã seguinte. Erros de início de sessão poderão ocorrer se a autenticação de formulários não está ativada para a rede a partir do qual está a ligar.

 ![Política de autenticação Global do Active Directory](./media/data-catalog-prerequisites/global-auth-policy.png)

## <a name="next-steps"></a>Passos seguintes
Para obter mais informações, consulte [configurar políticas de autenticação](https://technet.microsoft.com/library/dn486781.aspx).
