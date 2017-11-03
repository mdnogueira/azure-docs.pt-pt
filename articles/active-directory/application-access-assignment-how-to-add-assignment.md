---
title: "Como atribuir utilizadores e grupos a uma aplicação | Microsoft Docs"
description: "Atribuir utilizadores para a aplicação para conceder acesso"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 61536612e0dd5102b8f5e911c350826846f5ed77
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-assign-users-and-groups-to-an-application"></a>Como atribuir utilizadores e grupos a uma aplicação

Antes dos utilizadores podem efetuar qualquer uma do abaixo para uma aplicação específica, tem primeiro **atribuí-las para a aplicação** para lhe conceder acesso:

-   Aceder a uma aplicação por **navegar para o URL da aplicação diretamente** (também conhecido como iniciado por SP início de sessão).

-   Aceder a uma aplicação utilizando o **URL de acesso de utilizador** uma aplicação **propriedades** página (também conhecido como IDP iniciada pelo início de sessão).

-   Ver uma aplicação aparece no respetivo [painel de acesso de aplicação](https://myapps.microsoft.com/) ou aplicação móvel.

-   Ver uma aplicação aparece no respetivo [iniciador da aplicação do Office 365](https://support.office.com/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a).

## <a name="methods-to-assign-applications-with-azure-active-directory"></a>Métodos para atribuir as aplicações com o Azure Active Directory 

Existem 3 formas pode atribuir as aplicações com o Azure Active Directory:

-   [Atribuir um utilizador diretamente a uma aplicação como um administrador](#assign-a-user-directly-as-an-administrator)

-   [Atribuir um grupo diretamente a uma aplicação como um administrador](#assign-a-group-directly-to-an-application-as-an-administrator)

-   [Ativar o acesso de aplicação self-service permitir aos utilizadores localizar as suas próprias aplicações](#enable-self-service-application-access-to-allow-users-to-find-their-own-applications)

## <a name="assign-a-user-directly-as-an-administrator"></a>Atribuir um utilizador diretamente como um administrador

Para atribuir diretamente um ou mais utilizadores a uma aplicação, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

  * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7.  Quando carrega a aplicação, clique em **utilizadores e grupos** do menu de navegação esquerdo da aplicação.

8.  Clique em de **adicionar** botão do **utilizadores e grupos** lista para abrir o **adicionar atribuição** painel.

9.  Clique em de **utilizadores e grupos** Seletor do **adicionar atribuição** painel.

10. Escreva o **nome completo** ou **endereço de correio eletrónico** do utilizador que está interessado atribuir para o **pesquisa por nome ou endereço de e-mail** caixa de pesquisa.

11. Coloque o cursor sobre o **utilizador** na lista de revela um **caixa de verificação**. Clique na caixa de verificação junto a fotografia do perfil do utilizador ou logótipo para adicionar o utilizador para o **selecionados** lista.

12. **Opcional:** se gostaria de **adicionar mais do que um utilizador**, tipo noutra **nome completo** ou **endereço de correio eletrónico** para o **pesquisa por nome ou endereço de e-mail** caixa de pesquisa e clique na caixa de verificação para adicionar este utilizador para o **selecionados** lista.

13. Quando tiver terminado de selecionar utilizadores, clique em de **selecione** botão para os adicionar à lista de utilizadores e grupos atribuídos à aplicação.

14. **Opcional:** clique o **selecionar função** Seletor no **adicionar atribuição** painel para selecionar uma função para atribuir aos utilizadores que selecionou.

15. Clique em de **atribuir** botão para atribuir a aplicação para os utilizadores selecionados.

Após um curto período de tempo, os utilizadores que selecionou conseguir iniciar estas aplicações utilizando os métodos descritos na secção de descrição de solução.

## <a name="assign-a-group-directly-to-an-application-as-an-administrator"></a>Atribuir um grupo diretamente a uma aplicação como um administrador

Para atribuir um ou mais grupos diretamente a uma aplicação, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

  * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende atribuir um utilizador a partir da lista.

7.  Quando carrega a aplicação, clique em **utilizadores e grupos** do menu de navegação esquerdo da aplicação.

8.  Clique em de **adicionar** botão do **utilizadores e grupos** lista para abrir o **adicionar atribuição** painel.

9.  Clique em de **utilizadores e grupos** Seletor do **adicionar atribuição** painel.

10. Escreva o **nome do grupo completa** do grupo que está interessado atribuir para o **pesquisa por nome ou endereço de e-mail** caixa de pesquisa.

11. Coloque o cursor sobre o **grupo** na lista de revela um **caixa de verificação**. Clique na caixa de verificação junto a fotografia do perfil ou logótipo para adicionar o utilizador para o grupo de **selecionados** lista.

12. **Opcional:** se gostaria de **adicionar mais de um grupo**, tipo noutra **nome do grupo completa** para o **pesquisa por nome ou endereço de e-mail** caixa de pesquisa e clique na caixa de verificação para adicionar este grupo para o **selecionados** lista.

13. Quando tiver terminado de selecionar grupos, clique em de **selecione** botão para os adicionar à lista de utilizadores e grupos atribuídos à aplicação.

14. **Opcional:** clique o **selecionar função** Seletor no **adicionar atribuição** painel para selecionar uma função para atribuir aos grupos que selecionou.

15. Clique em de **atribuir** botão para atribuir a aplicação em grupos selecionados.

Após um curto período de tempo, os utilizadores nos grupos que selecionou conseguir iniciar estas aplicações utilizando os métodos descritos na secção de descrição de solução. Se estes são grupos dinâmicos, poderão existir alguns atraso de processamento adicional nestes atribuições de apresentação para estes atribuídos a grupos de utilizadores.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Ativar o acesso de aplicação self-service permitir aos utilizadores localizar as suas próprias aplicações

Acesso à aplicação self-service é uma excelente forma de permitir que os utilizadores Self-detetar aplicações, opcionalmente, permitir que o grupo de negócio para aprovar o acesso a essas aplicações. Pode permitir que o grupo de negócio gerir as credenciais atribuídas aos utilizadores para a direita da palavra-passe de início de sessão único em aplicações, a partir do respetivos painéis de acesso.

Para ativar o acesso de aplicação personalizada para uma aplicação, siga os passos abaixo:

1.  Abra o [ **Portal do Azure** ](https://portal.azure.com/) e inicie sessão como um **Administrador Global.**

2.  Abra o **extensão do Active Directory do Azure** clicando **mais serviços** na parte inferior do menu de navegação esquerda principal.

3.  Escreva **"do Azure Active Directory**" na caixa de pesquisa de filtro e selecione o **do Azure Active Directory** item.

4.  Clique em **aplicações empresariais** do menu de navegação esquerdo do Azure Active Directory.

5.  Clique em **todas as aplicações** para ver uma lista de todas as suas aplicações.

   * Se não vir a aplicação que pretende mostrar aqui, utilize o **filtro** controlo na parte superior a **lista de todas as aplicações** e defina o **mostrar** opção para **todas as aplicações.**

6.  Selecione a aplicação que pretende ativar o Self-Service acesso a partir da lista.

7.  Quando carrega a aplicação, clique em **self-service** do menu de navegação esquerdo da aplicação.

8.  Para ativar o acesso de aplicação self-service para esta aplicação, ative o **permitir que os utilizadores pedir acesso a esta aplicação?** alternar para **Sim.**

9.  Em seguida, para selecionar o grupo a que os utilizadores que pedem acesso a esta aplicação deve ser adicionado, clique em Seletor junto a etiqueta **ao qual o grupo devem atribuídos possível adicionar utilizadores?** e selecione um grupo.

10. **Opcional:** se pretender necessitam de uma aprovação de negócio antes dos utilizadores estão autorizados acesso, defina o **exigir a aprovação antes de conceder acesso a esta aplicação?** alternar para **Sim**.

11. **Opcional: para aplicações utilizando a palavra-passe início de sessão único em apenas,** se pretender permitir que esses aprovadores de negócio especificar as palavras-passe que são enviadas para esta aplicação para os utilizadores aprovados, defina o **permitir aprovadores definir palavras-passe de utilizador para esta aplicação?** alternar para **Sim**.

12. **Opcional:** para especificar os aprovadores de negócio que têm permissão para aprovar o acesso a esta aplicação, clique em Seletor junto a etiqueta **quem tem permissão para aprovar o acesso a esta aplicação?** para selecionar até 10 aprovadores de negócio individuais.

  >[!NOTE]
  >Não são suportados grupos.
  >
  >

13. **Opcional:** **para aplicações que expõem funções**, se pretender atribuir utilizadores aprovados self-service a uma função, clique o seletor junto ao **ao qual a função devem ser atribuído aos utilizadores nesta aplicação?** para selecionar a função para que estes utilizadores devem ser atribuídos.

14. Clique em de **guardar** botão na parte superior do painel para concluir.

Depois de concluir a configuração da aplicação de self-service, os utilizadores podem navegar para as respetivas [painel de acesso de aplicação](https://myapps.microsoft.com/) e clique em de **+ adicionar** botão para encontrar as aplicações a que tiver ativado o acesso de self-service. Aprovadores negócio também veem uma notificação no respetivo [painel de acesso de aplicação](https://myapps.microsoft.com/). Pode ativar uma mensagem de e-mail a indicar quando um utilizador tiver solicitado acesso a uma aplicação que requer a sua aprovação. 

Estas aprovações suportam apenas, fluxos de trabalho de aprovação único, o que significa que, se especificar vários aprovadores, qualquer aprovador único pode acesso aprovador à aplicação.

## <a name="next-steps"></a>Passos seguintes
[Fornecer início de sessão único às suas aplicações com o Proxy da aplicação](active-directory-application-proxy-sso-using-kcd.md)
