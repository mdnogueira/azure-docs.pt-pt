---
title: "Tutorial: Configurar à área de trabalho ao Facebook para aprovisionamento de utilizadores | Microsoft Docs"
description: "Saiba como aprovisionar e anular o aprovisionamento contas de utilizador do Azure AD para a área de trabalho por Facebook automaticamente."
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
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: a347eedbf5511dc83e1bc7721667441cfb87cb59
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-configure-workplace-by-facebook-for-user-provisioning"></a>Tutorial: Configurar à área de trabalho ao Facebook para aprovisionamento de utilizadores

Este tutorial mostra-lhe os passos necessários para automaticamente as contas de utilizador aprovisionar e aprovisionar desativação do Azure Active Directory (Azure AD) à área de trabalho por Facebook.

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração do Azure AD com área de trabalho, Facebook, precisa do seguinte:

- Uma subscrição do Azure AD
- Uma área de trabalho por Facebook-início de sessão único (SSO) ativada subscrição

Para testar os passos neste tutorial, siga estas recomendações:

- Não utilize o seu ambiente de produção, a menos que seja necessário.
- Se não tiver um ambiente de avaliação do Azure AD, pode obter um [oferta da avaliação de um mês](https://azure.microsoft.com/pricing/free-trial/).

## <a name="assign-users-to-workplace-by-facebook"></a>Atribuir utilizadores a área de trabalho por Facebook

AD do Azure utiliza um conceito chamado "atribuições de" para determinar quais os utilizadores devem receber acesso às aplicações selecionadas. No contexto de aprovisionamento da conta de utilizador automáticas, apenas os utilizadores e grupos que tenham sido atribuídos a uma aplicação no Azure AD são sincronizados.

Antes de configurar e ativar o serviço de aprovisionamento, tem de decidir quais os utilizadores e grupos no Azure AD representam os utilizadores que necessitam de aceder à área de trabalho através da aplicação do Facebook. Pode, em seguida, atribuir estes utilizadores à sua área de trabalho através da aplicação do Facebook ao seguir as instruções no [atribuir um utilizador ou grupo a uma aplicação empresarial](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal).

>[!IMPORTANT]
>*   Testar a configuração de aprovisionamento através da atribuição de um único utilizador do Azure AD para a área de trabalho por Facebook. Atribua utilizadores e grupos adicionais mais tarde.
>*   Quando atribui um utilizador à área de trabalho, Facebook, tem de selecionar uma função de utilizador válido. A função de acesso predefinida não funciona para o aprovisionamento.

## <a name="enable-automated-user-provisioning"></a>Ativar o aprovisionamento automatizado do utilizador

Esta secção orienta-o ao ligar o seu Azure AD para a API de área de trabalho por Facebook de aprovisionamento da conta de utilizador. Também irá aprender a configurar o serviço de aprovisionamento para criar, atualizar e desativar as contas de utilizador atribuído na área de trabalho por Facebook. Isto é baseado no utilizador e a atribuição de grupo no Azure AD.

>[!Tip]
>Também pode optar por ativada baseados em SAML SSO para a área de trabalho, Facebook, por seguir as instruções fornecidas a [portal do Azure](https://portal.azure.com). Pode ser configurado SSO independentemente aprovisionamento automático, apesar destas duas funcionalidades complementam entre si.

### <a name="configure-user-account-provisioning-to-workplace-by-facebook-in-azure-ad"></a>Configurar a conta de utilizador do aprovisionamento à área de trabalho por Facebook no Azure AD

AD do Azure suporta a capacidade para sincronizar automaticamente os detalhes da conta de utilizadores atribuídos à área de trabalho por Facebook. Esta sincronização automática permite à área de trabalho ao Facebook para obter os dados de que necessita para autorizar os utilizadores para acesso, antes de lhes tentar iniciar sessão pela primeira vez. É também anular Aprovisiona os utilizadores da área de trabalho por Facebook quando foi revogado acesso no Azure AD.

1. No [portal do Azure](https://portal.azure.com), selecione **do Azure Active Directory** > **aplicações da empresa** > **todas as aplicações**.

2. Se já tiver configurado a área de trabalho ao Facebook para SSO, procure a instância da área de trabalho, Facebook, utilizando o campo de pesquisa. Caso contrário, selecione **adicionar** e procure **à área de trabalho por Facebook** na Galeria de aplicações. Selecione **à área de trabalho por Facebook** nos resultados de pesquisa e adicione-o à sua lista de aplicações.

3. Selecione a instância da área de trabalho por Facebook e, em seguida, selecione o **aprovisionamento** separador.

4. Definir **modo de aprovisionamento** para **automática**. 

    ![Captura de ecrã da área de trabalho por Facebook opções de aprovisionamento](./media/active-directory-saas-facebook-at-work-provisioning-tutorial/provisioning.png)

5. Sob o **credenciais de administrador** secção, introduza o **segredo Token** e o **URL de inquilino** da sua área de trabalho pelo administrador do Facebook.

6. No portal do Azure, selecione **Testar ligação** para garantir que o Azure AD pode ligar à sua área de trabalho através da aplicação do Facebook. Se a ligação falhar, certifique-se de que a sua área de trabalho pela conta do Facebook tem permissões de administração de equipa.

7. Introduza o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de aprovisionamento no **correio eletrónico de notificação** campo e selecione a caixa de verificação.

8. Selecione **Guardar**.

9. Na secção mapeamentos, selecione **sincronizar utilizadores do Azure Active Directory à área de trabalho por Facebook**.

10. No **mapeamentos de atributos** secção, reveja os atributos de utilizador são sincronizados a partir do Azure AD para a área de trabalho por Facebook. Os atributos selecionados como **correspondência** propriedades são utilizadas para corresponder as contas de utilizador na área de trabalho por Facebook para operações de atualização. Ao consolidar as alterações, selecione **guardar**.

11. Para ativar o Azure AD aprovisionamento de serviço para a área de trabalho, Facebook, no **definições** secção, altere o **estado de aprovisionamento** para **no**.

12. Selecione **Guardar**.

Para obter mais informações sobre como configurar o aprovisionamento automático, consulte [a documentação do Facebook](https://developers.facebook.com/docs/facebook-at-work/provisioning/cloud-providers).

Agora, pode criar uma conta de teste. Aguarde até 20 minutos para verificar que a conta foi sincronizada à área de trabalho por Facebook.

## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento da conta de utilizador para aplicações da empresa](active-directory-saas-tutorial-list.md)
* [O que é o acesso a aplicações e início de sessão no Azure Active Directory?](active-directory-appssoaccess-whatis.md)
* [Configurar o início de sessão único](active-directory-saas-facebook-at-work-tutorial.md)

