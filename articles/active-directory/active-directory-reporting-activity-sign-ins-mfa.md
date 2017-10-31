---
title: "Referência para relatórios da autenticação multifator no portal do Azure | Microsoft Docs"
description: "Informações de referência para os relatórios da autenticação multifator no portal do Azure"
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/24/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 3b817c59658f4a5d102a3d65a17fade254e0257c
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2017
---
# <a name="reference-for-multi-factor-authentication-reporting-in-the-azure-portal"></a>Referência para os relatórios da autenticação multifator no portal do Azure

Com os [relatórios no Azure Active Directory (Azure AD)](active-directory-reporting-azure-portal.md) no [portal do Azure](https://portal.azure.com), pode obter todas as informações de que precisa para determinar o estado de funcionamento do seu ambiente.

Os [relatórios de atividades de início de sessão](active-directory-reporting-activity-sign-ins.md) dão-lhe informações sobre a utilização de aplicações geridas e de atividades de início de sessão dos utilizadores, que incluem informações sobre a utilização da autenticação multifator (MFA). 

Os dados de MFA disponibilizam-lhe informações sobre como esta está a funcionar na sua organização. Permite-lhe responder a perguntas como: 

- O início de sessão foi submetido à MFA? 

- Como é que o utilizador concluiu a MFA? 

- Por que motivo é que o utilizador não conseguiu concluir a MFA?  

Ao agregar todos os dados de inícios de sessão, pode ficar com uma ideia melhor relativamente à experiência da MFA na sua organização. Os dados ajudam-lhe a responder a perguntas como: 

- Quantos utilizadores são submetidos à MFA?  

- Quantos utilizadores não conseguem concluir a submissão da MFA? 

- Quais são os problemas mais comuns da MFA com que os utilizadores finais se deparam? 


Estes dados estão disponíveis através do portal do Azure e da [API de relatórios](active-directory-reporting-api-getting-started-azure-portal.md). 


## <a name="data-structure"></a>Estrutura dos dados


Os relatórios de atividades de início de sessão para a MFA dão-lhe acesso às informações seguintes:

**MFA necessária:** se a MFA é necessária para o início de sessão ou não. A MFA pode ser necessário devido a MFA por utilizador individual, a acesso condicional ou a outros motivos. Os valores possíveis são `Yes` ou `No`.

**Método de autenticação da MFA:** o método de autenticação que o utilizador utilizou para concluir a MFA. Os valores possíveis são: 

- Mensagem de texto 

- Notificação de aplicação móvel 

- Chamada telefónica (telefone de autenticação) 

- Código de verificação de aplicação móvel 

- Chamada telefónica (telefone do escritório) 

- Chamada telefónica (telefone de autenticação alternativo) 

**Detalhe de autenticação da MFA:** versão limpa do número de telefone, por exemplo, + X XXXXXXXX64. 

**Resultado da MFA:** obter mais informações sobre se a MFA foi cumprida ou recusada:

- Se a MFA for cumprida, esta coluna disponibiliza mais informações sobre como foi cumprida. 

- Se for recusada, a coluna mostrará o motivo da recusa. Os valores possíveis são `Satisfied` ou `Denied`. 

A secção seguinte mostra os valores de cadeias possíveis para o campo de resultados da MFA.

## <a name="status-strings"></a>Cadeias de estado

Esta secção mostra os possíveis valores para a cadeia de estado de resultado da MFA.

### <a name="satisfied-status-strings"></a>Cadeia de estado cumprido


- Multi-Factor Authentication do Azure

    - concluída na cloud 

    - expirou devido a políticas configuradas no inquilino 

    - registo concluído 

    - cumprida por reclamação no token 

    - cumprida por reclamação no token 

    - cumprida por reclamação no token 

    - cumprida por reclamação no token 

    - cumprida por reclamação fornecida por fornecedor externo 

    - cumprida por autenticação forte 

    - ignorada porque o fluxo executado foi o fluxo de início de sessão de mediador do Windows 

    - ignorada porque o fluxo executado foi o fluxo de início de sessão de mediador do Windows 

    - ignorada devido a palavra-passe de aplicação 

    - ignorada devido a localização 

    - ignorada devido a dispositivo registado 
    
    - ignorada devido a dispositivo memorizado 

    - concluída com êxito 

- Redirecionado para fornecedor externo para autenticação multifator 

 
### <a name="denied-status-strings"></a>Cadeia de estado recusado

Multi-Factor Authentication do Azure recusada; 

- autenticação em curso 

- tentativa de autenticação duplicada 

- código errado introduzido demasiadas vezes 

- autenticação inválida 

- código de verificação de aplicação móvel inválido 

- configuração incorreta 

- a chamada telefónica foi para o correio de voz 

- o formato do número de telefone é inválido 

- erro de serviço 

- não é possível comunicar com o telefone do utilizador 

- não é possível enviar a notificação de aplicação móvel para o dispositivo 

- não é possível enviar a notificação de aplicação móvel 

- o utilizador recusou a autenticação 

- o utilizador não respondeu à notificação de aplicação móvel 

- o utilizador não tem nenhum método de verificação registado 

- o utilizador introduziu um código incorreto 

- o utilizador introduziu um PIN incorreto 

- o utilizador desligou a chamada telefónica sem que a autenticação fosse bem sucedida 

- o utilizador está bloqueado 

- o utilizador nunca introduziu o código de verificação 

- o utilizador não foi encontrado 
 
- o código de verificação já foi utilizado uma vez 



## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, veja [Relatórios do Azure Active Directory](active-directory-reporting-azure-portal.md).




























