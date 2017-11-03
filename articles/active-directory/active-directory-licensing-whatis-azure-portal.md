---
title: "O que é o licenciamento no Azure Active Directory com base no grupo? | Microsoft Docs"
description: "Descrição do Azure Active Directory baseadas em grupos de licenciamento, como funciona e melhores práticas"
services: active-directory
keywords: Licenciamento do Azure AD
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/29/2017
ms.author: curtand
ms.reviewer: piotrci
ms.custom: H1Hack27Feb2017;it-pro
ms.openlocfilehash: d6c76af713a73b965e87a2f470125f2e65565975
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="group-based-licensing-basics-in-azure-active-directory"></a>Com base no grupo Noções básicas licenciamento no Azure Active Directory

Utilizar o Microsoft paga serviços em nuvem, tais como o Office 365, Enterprise Mobility + segurança, Dynamics CRM e outros produtos semelhantes, necessita de licenças. Estas licenças são atribuídas a cada utilizador que precisa de aceder a estes serviços. Para gerir licenças, os administradores utilizar um dos portais de gestão (Office ou do Azure) e os cmdlets do PowerShell. Azure Active Directory (Azure AD) é a infraestrutura subjacente que suporta a gestão de identidade para todos os serviços de nuvem da Microsoft. Azure AD armazena informações sobre os Estados de atribuição de licença aos utilizadores.

Até agora, licenças só foi possível atribuir ao nível do utilizador individual, que pode efetuar em grande escala gestão difícil. Por exemplo, para adicionar ou remover licenças de utilizador com base nas alterações organizacionais, tais como os utilizadores aderir ou abandonar o fileparser a organização ou de um departamento, um administrador, muitas vezes, tem de escrever um script do PowerShell complexo. Este script faz com que chamadas individuais para o serviço em nuvem.

Para abordar os desafios, do Azure AD agora inclui baseadas em grupos de licenciamento. Pode atribuir um ou mais licenças de produtos a um grupo. Azure AD assegura que as licenças estão atribuídas a todos os membros do grupo. Os novos membros que aderir ao grupo são atribuídos as licenças adequadas. Quando estes deixam o grupo, essas licenças são removidas. Isto elimina a necessidade para automatizar a gestão de licenças através do PowerShell para refletir as alterações na organização e estrutura departamental numa base por utilizador.

## <a name="features"></a>Funcionalidades

Seguem-se as funcionalidades principais do baseadas em grupos de licenciamento:

- Licenças podem ser atribuídas a nenhum grupo de segurança no Azure AD. Grupos de segurança podem ser sincronizado no local, utilizando o Azure AD Connect. Também pode criar grupos de segurança diretamente no Azure AD (também denominados grupos de apenas na nuvem) ou automaticamente através da funcionalidade de grupo dinâmico do Azure AD.

- Quando uma licença do produto é atribuída a um grupo, o administrador pode desativar um ou mais planos de serviço no produto. Normalmente, isto é feito quando a organização ainda não está pronta para começar a utilizar um serviço incluído num produto. Por exemplo, o administrador poderá atribuir do Office 365 para departamento, mas desativar temporariamente o serviço do Yammer.

- Todos os cloud services da Microsoft que necessitam de licenciamento de nível de utilizador são suportadas. Isto inclui todos os do Office 365 produtos, Enterprise Mobility + de segurança e Dynamics CRM.

- Com base no grupo de licenciamento está atualmente disponível apenas através de [portal do Azure](https://portal.azure.com). Se utilizar principalmente outros portais de gestão para o utilizador e a gestão de grupo, como o portal do Office 365, pode continuar a fazê-lo. Mas deve utilizar o portal do Azure para gerir as licenças ao nível do grupo.

- Azure AD gere automaticamente as modificações de licença que resultam de alterações de associação de grupo. Normalmente, as modificações de licença são eficazes dentro de minutos de uma alteração de associação.

- Um utilizador pode ser um membro de vários grupos com as políticas de licença especificados. Um utilizador também pode ter alguns licenças que foram atribuídas diretamente, fora de quaisquer grupos. O estado do utilizador resultante é uma combinação de todos os produtos atribuído e licenças de serviço.

- Em alguns casos, não não possível atribuir licenças de um utilizador. Por exemplo, poderá não existir licenças suficientes disponíveis no inquilino ou serviços em conflito poderão foram atribuídos ao mesmo tempo. Os administradores têm acesso a informações sobre quem do Azure AD não conseguiu totalmente processar licenças do grupo de utilizadores. Pode então tomar as medidas corretivas com base nestas informações.

- Durante a pré-visualização pública, uma subscrição de avaliação ou paga para as edições do Azure AD Basic ou Premium é necessário no inquilino para utilizar a gestão de baseado no grupo de licenças.

## <a name="your-feedback-is-welcome"></a>Os seus comentários são bem-vindo!

Se tiver comentários ou pedidos funcionalidades,. partilhá-los com utilizarmos [neste fórum](https://feedback.azure.com/forums/169401-azure-active-directory/category/317677-group-based-licensing).

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre outros cenários para gestão de licenças através de com base no grupo de licenciamento, consulte:

* [Introdução ao licenças no Azure Active Directory](active-directory-licensing-get-started-azure-portal.md)
* [Atribuir licenças a um grupo no Azure Active Directory](active-directory-licensing-group-assignment-azure-portal.md)
* [Identificar e resolver problemas de licenciamento para um grupo no Azure Active Directory](active-directory-licensing-group-problem-resolution-azure-portal.md)
* [Como migrar os utilizadores licenciados individuais para baseadas em grupos de licenciamento no Azure Active Directory](active-directory-licensing-group-migration-azure-portal.md)
* [Azure Active Directory baseadas em grupos licenciamento cenários adicionais](active-directory-licensing-group-advanced.md)
