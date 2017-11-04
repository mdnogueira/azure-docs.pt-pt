---
title: Personalizar os mapeamentos de atributos do Azure AD | Microsoft Docs
description: "Identificar os mapeamentos de atributos para aplicações de SaaS no Azure Active Directory como a pode modificá-los para abordar as necessidades de negócio."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 549e0b8c-87ce-4c9b-b487-b7bf0155dc77
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/17/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6921ca86efeea9d1255bb2d1773f55daa48b9b4a
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="customizing-user-provisioning-attribute-mappings-for-saas-applications-in-azure-active-directory"></a>Personalização de utilizador aprovisionamento mapeamentos de atributos para aplicações de SaaS no Azure Active Directory
Microsoft Azure AD fornece suporte para o aprovisionamento de utilizadores para aplicações de SaaS de terceiros, tais como o Salesforce, Google Apps e outros. Se tiver utilizadores de aprovisionamento para uma aplicação SaaS de terceiros ativada, o Portal de gestão do Azure controla os valores de atributo na forma de uma configuração denominada "mapeamento de atributos".

Não há um conjunto pré-configuradas de mapeamentos de atributos entre objetos de utilizador do Azure AD e objetos de utilizador de cada aplicação SaaS. Algumas aplicações, gerir outros tipos de objetos, tais como grupos ou contactos. <br> 
 Pode personalizar os mapeamentos de atributos de predefinição consoante as suas necessidades de negócio. Isto significa que pode alterar ou eliminar mapeamentos de atributos existente ou criar novos mapeamentos de atributos.

No portal do Azure AD, pode aceder a esta funcionalidade, clicando num **mapeamentos** configuração em **aprovisionamento** no **gerir** secção um  **Aplicação empresarial**.


![Salesforce][5] 

Ao clicar num **mapeamentos** configuração, abre o relacionados **atributo mapeamento** painel.  
Existem mapeamentos de atributos que são necessários para uma aplicação SaaS a funcionar corretamente. Para os atributos necessários, o **eliminar** funcionalidade não está disponível.


![Salesforce][6]  

No exemplo acima, pode ver que o **Username** atributo de um objeto gerido no Salesforce é preenchido com o **userPrincipalName** valor o objeto ligado para o Azure Active Directory.

Pode personalizar existente **mapeamentos de atributos** clicando um mapeamento. Esta ação abre o **Editar atributo** painel.

![Salesforce][7]  


  

## <a name="understanding-attribute-mapping-types"></a>Compreender tipos de mapeamento de atributos
Com mapeamentos de atributos, pode controlar a forma como os atributos são preenchidos numa aplicação SaaS de terceiros. Existem quatro tipos de mapeamento diferentes suportados:

* **Direto** – o atributo de destino é preenchido com o valor de um atributo do objeto ligado no Azure AD.
* **Constante** – o atributo de destino é preenchido com uma cadeia específica que especificou.
* **Expressão** -o atributo de destino é preenchido com base no resultado de uma expressão de tipo de script. 
  Para obter mais informações, consulte [escrever expressões para mapeamentos de atributos no Azure Active Directory](active-directory-saas-writing-expressions-for-attribute-mappings.md).
* **Nenhum** -o atributo de destino for deixado inalterado. No entanto, se o atributo de destino é alguma vez vazio, é preenchido com o valor predefinido que especificar.

Para além destes quatro tipos de mapeamento de atributo básica, os mapeamentos de atributos personalizados suportam o conceito de opcional **predefinido** valor atribuição. A atribuição de valor predefinido garante que um atributo de destino é preenchido com um valor se não existir nenhuma um valor no Azure AD, nem o objeto de destino. A configuração mais comuns é a deixar isto em branco.


## <a name="understanding-attribute-mapping-properties"></a>Noções sobre as propriedades de mapeamento de atributos

Na secção anterior, poderá já foram introduzidos para a propriedade de tipo de mapeamento de atributos.
Para além desta propriedade mapeamentos de atributos também suportam os seguintes atributos:

- **Atributo de origem** -o atributo de utilizador do sistema de origem (por ex.: Azure Active Directory).
- **Atributo de destino** – o atributo de utilizador no sistema de destino (por ex.: ServiceNow).
- **Corresponder objetos utilizando este atributo** – se ou não desta mapeamento deve ser utilizado para identificar exclusivamente os utilizadores entre os sistemas de origem e de destino. Normalmente, isto está definido no atributo userPrincipalName ou correio no Azure AD, que normalmente é mapeado para um campo de nome de utilizador numa aplicação de destino.
- **Correspondência de precedência** – vários atributos de correspondência pode ser definido. Quando existem vários, estes são avaliados por ordem definida por este campo. Assim que for encontrada uma correspondência, não são necessárias mais correspondência de atributos são avaliados.
- **Aplicar este mapeamento**
    - **Sempre** – aplicar desta mapeamento em ambos os criação de utilizador e as ações de atualização
    - **Apenas durante a criação** -aplicar desta mapeamento apenas ações de criação de utilizador


## <a name="what-you-should-know"></a>O que deve conhecer

Microsoft Azure AD fornece uma implementação eficaz de um processo de sincronização. Num ambiente inicializado, apenas os objetos que necessitam de atualizações são processados durante um ciclo de sincronização. Atualizar mapeamentos de atributos tem um impacto no desempenho de um ciclo de sincronização. Uma atualização para a configuração de mapeamento de atributos requer que todos os objetos geridos para ser reavaliadas. É uma melhor prática recomendada para manter o número de alterações consecutivas para os mapeamentos de atributos no mínimo.

## <a name="next-steps"></a>Passos seguintes

* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)
* [Automatizar utilizador aprovisionamento/desaprovisionamento para aplicações SaaS](active-directory-saas-app-provisioning.md)
* [Escrever expressões para mapeamentos de atributos](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtros de âmbito para o aprovisionamento de utilizador](active-directory-saas-scoping-filters.md)
* [Utilizar o SCIM para ativar o aprovisionamento automático de utilizadores e grupos do Azure Active Directory a aplicações](active-directory-scim-provisioning.md)
* [Notificações de aprovisionamento de contas](active-directory-saas-account-provisioning-notifications.md)
* [Lista de tutoriais sobre como integrar aplicações SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[1]: ./media/active-directory-saas-customizing-attribute-mappings/ic765497.png
[2]: ./media/active-directory-saas-customizing-attribute-mappings/ic775419.png
[3]: ./media/active-directory-saas-customizing-attribute-mappings/ic775420.png
[4]: ./media/active-directory-saas-customizing-attribute-mappings/ic775421.png
[5]: ./media/active-directory-saas-customizing-attribute-mappings/21.png
[6]: ./media/active-directory-saas-customizing-attribute-mappings/22.png
[7]: ./media/active-directory-saas-customizing-attribute-mappings/23.png

