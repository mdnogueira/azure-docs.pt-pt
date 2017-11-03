---
title: "Ocorreu um erro inesperado ao efetuar o consentimento para uma aplicação | Microsoft Docs"
description: "Explica como erros que podem ocorrer durante o processo de consenting para uma aplicação e o que pode fazer sobre os mesmos"
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
ms.openlocfilehash: fd500fdd4c8642bad96dcf71eebcf1fad461a35f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="unexpected-error-when-performing-consent-to-an-application"></a>Ocorreu um erro inesperado ao efetuar o consentimento para uma aplicação

Este artigo aborda os erros que podem ocorrer durante o processo de consenting para uma aplicação. Se estiver a resolver problemas inesperado consentimento pede-lhe que não contém quaisquer mensagens de erro, consulte [cenários de autenticação para o Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-scenarios).

Muitas aplicações que se integram com o Azure Active Directory requerem permissões para aceder a outros recursos para funcionar. Quando estes recursos também estão integrados no Azure Active Directory, permissões para aceder aos mesmos, muitas vezes, é pedido utilizando a estrutura de consentimento comuns. 

Isto resulta num pedido de consentimento a ser apresentado, que normalmente ocorre na primeira vez uma aplicação é utilizada, mas também pode ocorrer num utilize subsequente da aplicação.

Determinadas condições devem ser verdadeiras para um utilizador consentimento para as permissões que necessita de uma aplicação. Se estas condições não estiverem reunidas, podem ocorrer vários erros. Estas incluem:

## <a name="requesting-not-authorized-permissions-error"></a>Erro de permissões não autorizado a pedir
* **AADSTS90093:** &lt;clientAppDisplayName&gt; está a solicitar um ou mais permissões que não autorizado a conceder. Contacte um administrador, o que pode consentimento para esta aplicação em seu nome.

Este erro ocorre quando um utilizador que não seja um administrador da empresa tenta utilizar uma aplicação que está a pedir as permissões que apenas um administrador pode conceder. Este erro pode ser resolvido por um administrador conceder acesso à aplicação em nome da sua organização.

## <a name="policy-prevents-granting-permissions-error"></a>Política impede a conceder permissões de erro
* **AADSTS90093:** de um administrador &lt;tenantDisplayName&gt; tem de definir uma política que o impede de concessão de permissões &lt;nome da aplicação&gt; as permissões que está a solicitar. Contacte um administrador de &lt;tenantDisplayName&gt;, que pode conceder permissões para esta aplicação em seu nome.

Este erro ocorre quando um administrador de empresa desativa a capacidade dos utilizadores autorizar aplicações, em seguida, um utilizador de administrador não tenta utilizar uma aplicação que requer consentimento. Este erro pode ser resolvido por um administrador conceder acesso à aplicação em nome da sua organização.

## <a name="intermittent-problem-error"></a>Erro de problema intermitente
* **AADSTS90090:** parece encontrámos um problema intermitente gravar as permissões que foi efetuada uma tentativa para conceder a &lt;clientAppDisplayName&gt;. Tente novamente mais tarde.

Este erro indica que ocorreu um problema do lado do serviço intermitente. Podem ser resolvido através da tentativa de consentimento para novamente a aplicação.

## <a name="resource-not-available-error"></a>Recurso erro não está disponível
* **AADSTS65005:** a aplicação &lt;clientAppDisplayName&gt; solicitadas permissões para aceder a um recurso &lt;resourceAppDisplayName&gt; que não está disponível. 

Contacte o programador da aplicação.

##  <a name="resource-not-available-in-tenant-error"></a>Recurso não está disponível no erro de inquilino
* **AADSTS65005:** &lt;clientAppDisplayName&gt; está a solicitar acesso a um recurso &lt;resourceAppDisplayName&gt; que não está disponível na sua organização &lt; tenantDisplayName&gt;. 

Certifique-se de que este recurso se encontra disponível ou contacte o administrador de &lt;tenantDisplayName&gt;.

## <a name="permissions-mismatch-error"></a>Erro de falta de correspondência de permissões
* **AADSTS65005:** a aplicação pedida consentimento para recursos de acesso &lt;resourceAppDisplayName&gt;. Este pedido falhou porque não corresponde ao modo como a aplicação foi previamente configurada durante o registo de aplicação. Contactar a aplicação vendor.* *

Estes erros que todas ocorrerem quando a aplicação de que um utilizador está a tentar consentimento para solicitar permissões para aceder a uma aplicação de recursos que não pode ser localizada no diretório da organização (inquilino). Isto pode ocorrer por vários motivos:

-   O programador da aplicação de cliente tiver configurado os seus aplicação incorretamente, provocando-lo a pedir acesso a um recurso inválido. Neste caso, o programador da aplicação tem de atualizar a configuração da aplicação cliente para resolver este problema.

-   Um Principal de serviço que representa a aplicação de recurso de destino não existem na organização, ou existia no passado, mas foi removido. Para resolver este problema, um Principal de serviço para a aplicação de recursos têm de ser aprovisionado na organização, para a aplicação de cliente pode pedir permissões ao mesmo. Isto pode acontecer num número de formas, consoante o tipo de aplicação, incluindo:

    -   Adquirir uma subscrição para a aplicação de recursos (aplicações publicadas da Microsoft)

    -   Consenting para a aplicação de recursos

    -   Conceder as permissões de aplicações através do Portal do Azure

    -   Adicionar a aplicação na Galeria de aplicações do Azure AD

## <a name="next-steps"></a>Passos seguintes 

[As aplicações, permissões e consentimento no Azure Active Directory (v1 ponto final)](https://docs.microsoft.com/azure/active-directory/active-directory-apps-permissions-consent)<br>

[Âmbitos, permissões e consentimento no Azure Active Directory (ponto final v 2.0)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)


