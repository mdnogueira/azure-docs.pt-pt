---
title: "Tutorial: Integração do Azure Active Directory a área de trabalho por Facebook | Microsoft Docs"
description: "Saiba como configurar o início de sessão entre o Azure Active Directory e à área de trabalho por Facebook."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 6341e67e-8ce6-42dc-a4ea-7295904a53ef
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2017
ms.author: jeedes
ms.openlocfilehash: 9b22679c304248ed7ba7a6bd9eaf82b64f7143cf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configuring-workplace-by-facebook-for-user-provisioning"></a>Tutorial: Configurar à área de trabalho ao Facebook para aprovisionamento de utilizadores

O objetivo deste tutorial é mostrar-lhe os passos que necessários para executar na área de trabalho por Facebook e o Azure AD para aprovisionar e anular o aprovisionamento contas de utilizador do Azure AD para a área de trabalho por Facebook automaticamente.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com área de trabalho, Facebook, terá dos seguintes itens:

- Uma subscrição do Azure AD
- Uma área de trabalho por Facebook início de sessão único subscrição ativado

> [!NOTE]
> Para testar os passos neste tutorial, não recomendamos a utilização num ambiente de produção.

Para testar os passos neste tutorial, deve seguir estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter uma avaliação de um mês [aqui](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assigning-users-to-workplace-by-facebook"></a>Atribuir utilizadores a área de trabalho por Facebook

Azure Active Directory utiliza um conceito chamado "atribuições de" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento da conta de utilizador automáticas, apenas os utilizadores e grupos que tenham sido "atribuídos" a uma aplicação no Azure AD é sincronizado.

Antes de configurar e ativar o serviço de aprovisionamento, tem de decidir quais os utilizadores e/ou grupos no Azure AD representam os utilizadores que necessitam de aceder à área de trabalho através da aplicação do Facebook. Depois de decidir, pode atribuir estes utilizadores à sua área de trabalho através da aplicação do Facebook ao seguir as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-workplace-by-facebook"></a>Sugestões importantes para atribuir utilizadores a área de trabalho por Facebook

*   Recomenda-se que um único utilizador do Azure AD é atribuído à área de trabalho ao Facebook para testar a configuração de aprovisionamento. Os utilizadores adicionais e/ou grupos podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador a área de trabalho, Facebook, tem de selecionar uma função de utilizador válido. A função de "Acesso predefinida" não funciona para o aprovisionamento.

## <a name="enable-user-provisioning"></a>Ativar o aprovisionamento de utilizador

Esta secção orienta-o de ligar o seu Azure AD à área de trabalho através da API de aprovisionamento da conta de utilizador do Facebook e configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuído na área de trabalho por Facebook com base no utilizador e grupo atribuição no Azure AD.

>[!Tip]
>Também pode optar por ativada baseados em SAML Single Sign-On para a área de trabalho, Facebook, seguindo as instruções fornecidas no [portal do Azure](https://portal.azure.com). O início de sessão único a pode ser configurado independentemente aprovisionamento automático, apesar destas duas funcionalidades complementar dos entre si.

### <a name="to-configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Para configurar a conta aprovisionamento de utilizadores à área de trabalho por Facebook no Azure AD:

O objetivo desta secção consiste em descrevem como ativar o aprovisionamento de contas de utilizador do Active Directory à área de trabalho por Facebook.

AD do Azure suporta a capacidade para sincronizar automaticamente os detalhes da conta de utilizadores atribuídos à área de trabalho por Facebook. Esta sincronização automática permite à área de trabalho ao Facebook para obter os dados de que necessita para autorizar os utilizadores para acesso, antes de lhes tentar iniciar sessão pela primeira vez. É também anular Aprovisiona os utilizadores da área de trabalho por Facebook quando foi revogado acesso no Azure AD.

1. No [portal do Azure](https://portal.azure.com), navegue para o **do Azure Active Directory** > **aplicações da empresa** > **todas as aplicações** secção.

2. Se já tiver configurado a área de trabalho ao Facebook para o início de sessão único, procure a instância da área de trabalho por Facebook utilizando o campo de pesquisa. Caso contrário, selecione **adicionar** e procure **à área de trabalho por Facebook** na Galeria de aplicações. Selecione os resultados da pesquisa à área de trabalho por Facebook e adicioná-lo à sua lista de aplicações.

3. Selecione a instância da área de trabalho, Facebook, em seguida, selecione o **aprovisionamento** separador.

4. Definir o **modo de aprovisionamento** para **automática**. 

    ![Aprovisionamento](./media/active-directory-saas-workplacebyfacebook-provisioning-tutorial/provisioning.png)

5. Sob o **credenciais de administrador** secção, introduza o Token do segredo e o URL de inquilino da sua área de trabalho pelo administrador do Facebook.

6. No portal do Azure, clique em **Testar ligação** para garantir que o Azure AD pode ligar à sua área de trabalho através da aplicação do Facebook. Se a ligação falhar, certifique-se à sua área de trabalho pela conta do Facebook permissões de administração de equipa.

7. Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **correio eletrónico de notificação** campo e marque a caixa de verificação.

8. Clique em **guardar.**

9. Na secção mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory à área de trabalho por Facebook.**

10. No **mapeamentos de atributos** secção, reveja os atributos de utilizador são sincronizados a partir do Azure AD para a área de trabalho por Facebook. Os atributos selecionados como **correspondência** propriedades são utilizadas para corresponder as contas de utilizador na área de trabalho por Facebook para operações de atualização. Selecione o botão Guardar para consolidar as alterações.

11. Para ativar o Azure AD aprovisionamento do serviço para a área de trabalho, Facebook, altere o **estado de aprovisionamento** para **no** no **definições** secção

12. Clique em **guardar.**

Para obter mais informações sobre como configurar o aprovisionamento automático, consulte [https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers)

Agora, pode criar uma conta de teste. Aguarde até 20 minutos para verificar que a conta foi sincronizada à área de trabalho por Facebook.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações da empresa](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar o início de sessão único](active-directory-saas-workplacebyfacebook-tutorial.md)

