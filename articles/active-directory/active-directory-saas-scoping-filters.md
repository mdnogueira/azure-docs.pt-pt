---
title: "Aprovisionar aplicações com filtros de âmbito | Microsoft Docs"
description: "Saiba como utilizar filtros de âmbito para impedir que os objetos em aplicações que suportam o aprovisionamento de utilizadores automatizada do aprovisionamento se um objeto não satisfazer os requisitos de negócio."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: bcfbda74-e4d4-4859-83bc-06b104df3918
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: markvi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7b38ccba1abb20ec88df8234ae9859caba19d43f
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/01/2017
---
# <a name="attribute-based-application-provisioning-with-scoping-filters"></a>Aprovisionamento de aplicações baseadas em atributos com filtros de âmbito
O objetivo deste artigo é explicar como utilizar filtros de âmbito para definir regras baseadas em atributos que determinam que utilizadores são aprovisionados para uma aplicação.

## <a name="scoping-filter-use-cases"></a>Casos de utilização do filtro de controlo de âmbito

Um filtro de âmbito permite que o Azure Active Directory (Azure AD) serviço de fornecimento incluir ou excluir os utilizadores que tenham um atributo que corresponda a um valor específico. Por exemplo, quando o aprovisionamento de utilizadores do Azure AD para uma aplicação SaaS utilizado por uma equipa de venda, pode especificar que apenas os utilizadores com um atributo de "Departamento" de "Vendas" devem estar no âmbito de aprovisionamento.

Filtros de âmbito podem ser utilizados forma diferente consoante o tipo de aprovisionamento de conector:

* **Saída de aprovisionamento do Azure AD para aplicações SaaS**. Quando o Azure AD é o sistema de origem, [atribuições de utilizador e grupo](active-directory-coreapps-assign-user-azure-portal.md) são o método mais comum para determinar quais os utilizadores que se encontrem no âmbito para o aprovisionamento. Estes atribuições também são utilizadas para ativar o início de sessão único e fornecem um método único para gerir o acesso e o aprovisionamento. Filtros de âmbito podem ser utilizados opcionalmente, além de atribuições ou em vez dos mesmos, para filtrar os utilizadores com base nos valores de atributo.

    >[!TIP]
    > Pode desativar o aprovisionamento com base em atribuições de uma aplicação empresarial alterando as definições no [âmbito](active-directory-saas-app-provisioning.md#how-do-i-set-up-automatic-provisioning-to-an-application) menu sob as definições de aprovisionamento para **sincronizar todos os utilizadores e grupos**. Utilizar esta opção plus baseadas em atributos filtros de âmbito oferece um desempenho mais rápido do que utilizar atribuições baseadas em grupos.  

* **Aprovisionamento de aplicações de HCM para o Azure AD de entrada e o Active Directory**. Quando um [aplicação HCM, tais como Workday](active-directory-saas-workday-tutorial.md) é o sistema de origem, filtros de âmbito são o principal método para determinar quais os utilizadores devem ser aprovisionados da aplicação HCM do Active Directory ou do Azure AD.

Por predefinição, os conectores de aprovisionamento do Azure AD não tem quaisquer filtros âmbito baseadas em atributos configurados. 

## <a name="scoping-filter-construction"></a>O âmbito de construção de filtro

Um filtro de âmbito é composto por um ou mais *cláusulas*. Cláusulas determinam quais os utilizadores têm permissão para passar o filtro de âmbito através da avaliação de atributos de cada utilizador. Por exemplo, poderá ter uma cláusula que requer que o atributo de "State" de um utilizador for igual a "Nova Iorque", pelo que apenas os utilizadores de Nova Iorque são aprovisionados para a aplicação. 

Uma cláusula única define uma condição única para um valor de atributo. Se vários cláusulas são criadas num único filtro de âmbito, estes estiver a avaliadas em conjunto através da utilização de lógica de "E". Isto significa que todas as cláusulas tem de avaliar como "true" por ordem para um utilizador a ser aprovisionado.

Por fim, vários filtros de âmbito podem ser criados para uma única aplicação. Se existirem vários filtros de âmbito, se estiver a avaliadas em conjunto através da utilização de lógica de "Ou". Isto significa que, se todas as cláusulas em qualquer um dos filtros de âmbito configurados avaliar como "true", o utilizador é aprovisionado.

Cada utilizador ou grupo processados pelo serviço de aprovisionamento do Azure AD é sempre avaliado individualmente em relação a cada âmbito de filtro.

Por exemplo, considere o seguinte filtro de âmbito:

![Filtro de âmbito](./media/active-directory-saas-scoping-filters/scoping-filter.PNG) 

De acordo com o filtro de âmbito, os utilizadores devem satisfazer os seguintes critérios para ser aprovisionados:

* Tem de ser em Nova Iorque.
* Tem de trabalhar no departamento de engenharia.
* ID de empregado respetiva empresa tem de estar entre 1,000,000 e 2,000,000.
* Os seus cargo não pode ser nulo nem estar vazio.

## <a name="create-scoping-filters"></a>Criar filtros de âmbito
Filtros de âmbito são configurados como parte dos mapeamentos de atributos para cada conector de aprovisionamento de utilizador do Azure AD. O procedimento seguinte assume que já configurou o aprovisionamento automático para [das aplicações suportadas](active-directory-saas-tutorial-list.md) e estiver a adicionar um filtro de âmbito ao mesmo.

### <a name="create-a-scoping-filter"></a>Criar um filtro de âmbito
1. No [portal do Azure](https://portal.azure.com), vá para o **do Azure Active Directory** > **aplicações empresariais** > **todas as aplicações** secção.

2. Selecione a aplicação que tiver configurado o aprovisionamento automático: por exemplo, "ServiceNow".

3. Selecione o **aprovisionamento** separador.

4. No **mapeamentos** secção, selecione o mapeamento de que pretende configurar um filtro para definir o âmbito: por exemplo, "Sincronizar do Azure Active Directory utilizadores para ServiceNow".

5. Selecione o **âmbito de objeto de origem** menu.

6. Selecione **Adicionar filtro de âmbito**.

7. Definir uma cláusula selecionando uma origem **nome de atributo**, um **operador**e um **valor do atributo** para correspondência. São suportados os seguintes operadores:

   a. **É IGUAL A**. Cláusula devolve "verdadeiro" se o atributo avaliado corresponde ao valor de cadeia de entrada exatamente (sensível às maiúsculas e).

   b. **NÃO É IGUAL A**. Cláusula devolve "verdadeira" se o atributo avaliado não coincidir com o valor de cadeia de entrada (sensível às maiúsculas e).

   c. **É VERDADEIRO**. Cláusula devolve "verdadeira" se o atributo avaliado contém um valor booleano TRUE.

   d. **ESTÁ DEFINIDO COMO FALSE**. Cláusula devolve "verdadeira" se o atributo avaliado contém um valor booleano falso.

   e. **É NULO**. Cláusula devolve "verdadeira" se o atributo avaliado está vazio.

   f. **NÃO É NULA**. Cláusula devolve "verdadeira" se o atributo avaliado não está vazio.

   g. **CORRESPONDÊNCIA DE REGEX**. Cláusula devolve "verdadeira" se o atributo avaliado corresponde a um padrão de expressão regular. Por exemplo: ([1-9][0-9]) corresponde a qualquer número entre 10 e 99.

   h. **NÃO CORRESPONDÊNCIA DE REGEX**. Cláusula devolve "verdadeira" se o atributo avaliado não corresponde a um padrão de expressão regular.

8. Selecione **Adicionar cláusula de definir o âmbito novo**.

9. Opcionalmente, repita os passos 7-8 para adicionar mais cláusulas âmbito.

10. No **título do filtro de controlo de âmbito**, adicionar um nome para o filtro de âmbito.

11. Selecione **OK**.

12. Selecione **OK** novamente no **filtros de âmbito** ecrã. Opcionalmente, repita os passos 6-11 para adicionar outro filtro de âmbito.

13. Selecione **guardar** no **atributo mapeamento** ecrã. 

>[!IMPORTANT] 
> A guardar um novo âmbito acionadores de filtro uma sincronização completa novo para a aplicação, onde todos os utilizadores no sistema de origem são avaliados novamente contra o novo filtro de âmbito. Se um utilizador na aplicação tiver sido anteriormente no âmbito de aprovisionamento, mas fica fora do âmbito, a conta está desativada ou desaprovisionada na aplicação.


## <a name="related-articles"></a>Artigos relacionados
* [Índice de artigos da gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
* [Automatizar utilizador aprovisionamento e desaprovisionamento para aplicações SaaS](active-directory-saas-app-provisioning.md)
* [Personalizar os mapeamentos de atributos para o aprovisionamento de utilizador](active-directory-saas-customizing-attribute-mappings.md)
* [Escrever expressões para mapeamentos de atributos](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Notificações de aprovisionamento de contas](active-directory-saas-account-provisioning-notifications.md)
* [Utilizar o SCIM para ativar o aprovisionamento automático de utilizadores e grupos do Active Directory do Azure para aplicações](active-directory-scim-provisioning.md)
* [Lista de tutoriais sobre como integrar aplicações SaaS](active-directory-saas-tutorial-list.md)

