---
title: "Problema através de acesso de aplicação personalizada | Microsoft Docs"
description: "Resolver problemas relacionados com acesso de aplicação personalizada"
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
ms.reviewer: japere
ms.openlocfilehash: 217726709a1fdb02275de5a76a1352ea9c350600
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="problem-using-self-service-application-access"></a>Problema através de acesso de aplicação personalizada

Acesso à aplicação self-service é uma excelente forma de permitir que os utilizadores Self-detetar aplicações, opcionalmente, permitir que o grupo de negócio para aprovar o acesso a essas aplicações. Pode permitir que o grupo de negócio gerir as credenciais atribuídas aos utilizadores para a direita da palavra-passe de início de sessão único em aplicações, a partir do respetivos painéis de acesso.

Antes dos utilizadores Self-podem detetar as aplicações a partir do respetivo painel de acesso, tem de ativar **acesso à aplicação self-service** para todas as aplicações que pretende permitir que os utilizadores Self-detetar e pedir acesso.

## <a name="general-issues-to-check-first"></a>Problemas gerais para verificar primeiro

-   Certifique-se de acesso à aplicação self-service está configurado corretamente. Consulte "Como configurar o acesso de aplicação personalizada".

-   Certifique-se de que o utilizador ou grupo tiver sido ativado para pedir acesso à aplicação self-service.

-   Certifique-se de que o utilizador é visitando o local correto para acesso de aplicação personalizada. os utilizadores podem navegar para as respetivas [painel de acesso de aplicação](https://myapps.microsoft.com/) e clique em de **+ adicionar** botão para encontrar as aplicações a que tiver ativado o acesso de self-service.

-   Se o acesso de aplicação personalizada recentemente tiver sido configurado, tente voltar a iniciar e terminar no painel de acesso do utilizador após alguns minutos para ver se as alterações de acesso de self-service tem apareceu.

## <a name="how-to-configure-self-service-application-access"></a>Como configurar o acesso de aplicação personalizada

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
 > Não são suportados grupos.
 >
 >

13. **Opcional:** **para aplicações que expõem funções**, se pretender atribuir utilizadores aprovados self-service a uma função, clique o seletor junto ao **ao qual a função devem ser atribuído aos utilizadores nesta aplicação?** para selecionar a função para que estes utilizadores devem ser atribuídos.

14. Clique em de **guardar** botão na parte superior do painel para concluir.

Depois de concluir a configuração da aplicação de self-service, os utilizadores podem navegar para as respetivas [painel de acesso de aplicação](https://myapps.microsoft.com/) e clique em de **+ adicionar** botão para encontrar as aplicações a que tiver ativado o acesso de self-service. Aprovadores negócio também veem uma notificação no respetivo [painel de acesso de aplicação](https://myapps.microsoft.com/). Pode ativar uma mensagem de e-mail a indicar quando um utilizador tiver solicitado acesso a uma aplicação que requer a sua aprovação. 

Estas aprovações suportam aprovação único fluxos de trabalho apenas, que significa que, se especificar vários aprovadores, qualquer aprovador único pode aprovar acesso à aplicação.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Se estes passos de resolução de problemas não resolverem o problema 

Abra um pedido de suporte com as seguintes informações se disponíveis:

-   ID de correlação de erro

-   UPN (endereço de e-mail do utilizador)

-   TenantID

-   Tipo de browser

-   Fuso horário e tempo/período de tempo durante o erro ocorre

-   Rastreios de fiddler

## <a name="next-steps"></a>Passos seguintes
[Configurar o Azure Active Directory para gestão de grupos self-service](active-directory-accessmanagement-self-service-group-management.md)
