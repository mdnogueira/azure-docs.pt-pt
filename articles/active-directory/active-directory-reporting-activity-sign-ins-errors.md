---
title: "Códigos de erro dos relatórios de atividades de início de sessão no portal do Azure Active Directory | Microsoft Docs"
description: "Referência aos códigos de erro dos relatórios de atividades de início de sessão."
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
ms.date: 07/12/2017
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: 2a1b7b87df2cd8fa2e98f217480b46f5f6334297
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="sign-in-activity-report-error-codes-in-the-azure-active-directory-portal"></a>Códigos de erro dos relatórios de atividades de início de sessão no portal do Azure Active Directory

Com as informações fornecidas pelo relatório de inícios de sessão de utilizador, encontrará respostas a perguntas como:

- Quem iniciou sessão com o Azure Active Directory?
- Em que aplicações foi iniciada sessão?
- Que inícios de sessão falharam e porquê?

Este tópico lista os códigos de erro e as descrições associadas. 

## <a name="how-can-i-display-failed-sign-ins"></a>Como posso apresentar inícios de sessão com falha? 

O primeiro ponto de entrada de todos os dados de atividades de início de sessão é **[Inícios de sessão](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns)**, na secção de **Atividade** do **Azure Active**.


![Atividade de início de sessão](./media/active-directory-reporting-activity-sign-ins-errors/61.png "Atividade de início de sessão")


Nos relatórios de inícios de sessão, pode apresentar todos os inícios de sessão com falha ao selecionar **Falha** como o **Estado do início de sessão**.


![Atividade de início de sessão](./media/active-directory-reporting-activity-sign-ins-errors/06.png "Atividade de início de sessão")

Clicar num item na lista apresentada abre o painel **Detalhes da Atividade: inícios de sessão**. Esta vista mostra-lhe todos os detalhes que o Azure Active Directory monitoriza relativamente a inícios de sessão, incluindo o **código de erro do início de sessão** e um **motivo para a falha**.

![Atividade de início de sessão](./media/active-directory-reporting-activity-sign-ins-errors/05.png "Atividade de início de sessão")


Como alternativa à utilização do portal do Azure para aceder aos dados de inícios de sessão, também pode utilizar a [API de relatórios](active-directory-reporting-api-getting-started-azure-portal.md).


A secção seguinte proporciona-lhe uma descrição geral completa de todos os possíveis erros e as descrições associadas. 

## <a name="error-codes"></a>Códigos de erro

| Erro| Descrição |
| --- | --- |
| 50001| O principal de serviço com o nome X não foi encontrado no inquilino com o nome Y. Este erro pode acontecer se a aplicação não tiver sido instalada pelo administrador do inquilino. Ou se o principal do recurso não tiver sido encontrado no diretório ou for inválido|
| 50008| As asserções SAML estão em falta ou estão mal configuradas no token.|
| 50011| O endereço de resposta está em falta, está mal configurado ou não corresponde ao endereço de resposta configurado para a aplicação.|
| 50053| A aplicação foi bloqueada porque o utilizador tentou iniciar sessão demasiadas vezes com um ID de utilizador ou uma palavra-passe inválida.|
| 50054| Foi utilizada uma palavra-passe antiga para a autenticação.|
| 50055| Palavra-passe inválida; palavra-passe expirada introduzida.|
| 50057| A conta de utilizador está desativada.|
| 50058| Não foram encontradas informações sobre a identidade do utilizador entre as credenciais fornecidas, ou o utilizador não foi encontrado no inquilino, ou um pedido de início de sessão silencioso foi enviado, mas nenhum utilizador tem sessão iniciada ou o serviço não conseguiu autenticar o utilizador.|
| 50074| A autenticação forte (segundo fator) é necessária.|
| 50079| O utilizador tem de se inscrever na autenticação de segundo fator|
| 50126| O nome de utilizador ou a palavra-passe é inválida ou o nome de utilizador ou a palavra-passe no local é inválida.|
| 50131| Utilizado em vários erros de acesso condicional. Por exemplo, estado do dispositivo Windows, pedido bloqueado devido a atividades suspeitas, decisões de políticas de acesso e políticas de segurança.|
| 50133| A sessão é inválida devido à palavra-passe ter expirado ou ter sido alterada recentemente.|
| 50144| A palavra-passe do Active Directory do utilizador expirou.|
| 65001| A aplicação X não tem permissão para aceder à aplicação Y ou a permissão foi revogada. Ou o utilizador ou o administrador não permitiu utilizar a aplicação com o ID X. Envie um pedido de autorização interativo para este utilizador e este recurso. Ou o utilizador ou o administrador não permitiu utilizar a aplicação com o ID X. Envie um pedido de autorização ao administrador do seu inquilino para agir em nome da Aplicação :Y para o Recurso : Z.|
| 65005| A lista de acesso a recursos necessária para a aplicação não contém aplicações detetáveis pelo recurso, ou a aplicação cliente pediu acesso a um recurso que não foi especificado nesta lista de acesso a recursos necessária, ou o serviço Graph devolveu um pedido inválido ou o recurso não foi encontrado.|
| 70001| A aplicação com o nome X não foi encontrada no inquilino com o nome Y. Este erro pode acontecer se a aplicação não tiver sido instalada pelo administrador do inquilino ou não tiver sido permitida por qualquer utilizador do inquilino. Poderá ter enviado o pedido de autenticação para o inquilino errado.|
| 80001| Não existe nenhum Agente de Autenticação disponível.|
| 80002| O pedido de validação da palavra-passe do Agente de Autenticação atingiu o tempo limite.|
| 80003| O Agente de Autenticação recebeu uma resposta inválida.|
| 80004| Foi utilizado um Nome Principal de Utilizador (UPN) no pedido de início de sessão.|
| 80005| Agente de Autenticação: ocorreu um erro.|
| 80007| O Agente de Autenticação não se consegue ligar ao Active Directory.|
| 80010| O Agente de Autenticação não conseguiu desencriptar a palavra-passe.|
| 81001| A permissão do Kerberos do utilizador é demasiado grande.|
| 81002| Não é possível validar a permissão do Kerberos do utilizador.|
| 81003| Não é possível validar a permissão do Kerberos do utilizador.|
| 81004| Falha ao tentar a autenticação do Kerberos.|
| 81008| Não é possível validar a permissão do Kerberos do utilizador.|
| 81009| Não é possível validar a permissão do Kerberos do utilizador.|
| 81010| O SSO integrado falhou porque a permissão do Kerberos do utilizador expirou ou é inválida.|
| 81011| Não é possível encontrar o objeto de utilizador com base nas informações na permissão do Kerberos do utilizador.|
| 81012| O utilizador que está a tentar iniciar sessão no Azure AD é diferente do utilizador que tem sessão iniciada no dispositivo.|
| 81013| Não é possível encontrar o objeto de utilizador com base nas informações na permissão do Kerberos do utilizador.|
| 90014| Utilizado em vários casos nos quais um campo que era esperado não está presente na credencial.|
| 90093| O Graph devolveu com um código de erro proibido para o pedido.|



## <a name="next-steps"></a>Passos seguintes

Para obter mais detalhes, veja [Relatórios de atividade de início de sessão no portal do Azure Active Directory](active-directory-reporting-activity-sign-ins.md).

