---
title: "Do Azure AD Connect: Autenticação pass-through - bloqueio inteligente | Microsoft Docs"
description: "Este artigo descreve como autenticação de pass-through do Azure Active Directory (Azure AD) protege as contas no local contra ataques de palavra-passe de força bruta na nuvem."
services: active-directory
keywords: "Authentication do Azure AD Connect pass-through, a instalação do Active Directory, os componentes necessários para o Azure AD, SSO, o início de sessão único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/19/2017
ms.author: billmath
ms.openlocfilehash: 771741fd7da8c9b6932851851aaca148f9596643
ms.sourcegitcommit: c5eeb0c950a0ba35d0b0953f5d88d3be57960180
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/24/2017
---
# <a name="azure-active-directory-pass-through-authentication-smart-lockout"></a>A autenticação pass-through do Azure Active Directory: Bloqueio inteligente

## <a name="overview"></a>Descrição geral

Azure AD protege contra ataques de palavra-passe de força bruta e impede que os utilizadores genuínos as aplicações do Office 365 e do SaaS que está a ser bloqueado. Esta capacidade, denominada **bloqueio inteligente**, é suportada quando utiliza a autenticação pass-through como método de início de sessão. Bloqueio inteligente está ativada por predefinição para todos os inquilinos e estiver a proteger as contas de utilizador sempre; Não é necessário para ativá-la.

Bloqueio inteligente funciona por manter um registo do início de sessão de tentativas falhadas e depois de uma determinada **limiar de bloqueio**, começando uma **duração do bloqueio**. Quaisquer tentativas de início de sessão do atacante durante o período de bloqueio são rejeitadas. Se persistir de ataque, o subsequente falhado início de sessão tenta após a duração do bloqueio terminar resultado no tempo durações de bloqueio.

>[!NOTE]
>O limiar de bloqueio a predefinição é 10 tentativas falhadas, e a duração do bloqueio de predefinição é 60 segundos.

Bloqueio inteligente também distingue entre inícios de sessão dos utilizadores genuínos e para os atacantes e bloqueios apenas enviados os atacantes na maioria dos casos. Esta funcionalidade impede os atacantes de forma maliciosa poderá utilizadores genuínos. Utilizamos passado o comportamento de início de sessão, dos utilizadores dispositivos e browsers e outros sinais para distinguir entre utilizadores genuínos e os atacantes. Estamos constantemente estão a melhorar o nosso algoritmos.

Porque a autenticação pass-through reencaminha os pedidos de validação de palavra-passe no seu local no Active Directory (AD), tem de impedir atacantes de bloqueio de contas de anúncios a que os utilizadores. Uma vez que tem as suas políticas de bloqueio da conta (especificamente, [ **limiar de bloqueio de conta** ](https://technet.microsoft.com/library/hh994574(v=ws.11).aspx) e [ **Repor conta bloqueio contador após políticas**](https://technet.microsoft.com/library/hh994568(v=ws.11).aspx)), terá de configurar devidamente limiar de bloqueio e valores de duração do bloqueio do Azure AD para filtrar ataques na nuvem, antes de atingirem no local AD.

>[!NOTE]
>A funcionalidade de bloqueio inteligente está livre e é _no_ por predefinição para todos os clientes. No entanto, modificar o limiar de bloqueio e valores de duração do bloqueio utilizando Graph API do Azure AD tem o seu inquilino ter pelo menos uma licença do Azure AD Premium P2. Não precisa de uma licença do Azure AD Premium P2 _por utilizador_ para obter a funcionalidade de bloqueio inteligente com a autenticação pass-through.

Para garantir que os seus utilizadores no local Contas de anúncios são bem protegidas, certifique-se de que:

1.  Limiar de bloqueio do Azure AD é _menos_ ao limiar de bloqueio de conta do AD. Recomendamos que defina os valores de forma a que o limiar de bloqueio de conta do AD é, pelo menos, dois ou três vezes o limiar de bloqueio do Azure AD.
2.  Duração do bloqueio do Azure AD (representado em segundos) é _mais_ que de reposição de conta bloqueio contador após do AD (representado em minutos).

>[!IMPORTANT]
>Um administrador não é possível desbloqueie as contas na nuvem se foi bloqueados para a capacidade de bloqueio inteligente. Terá de aguardar o período de bloqueio expirar.

## <a name="verify-your-ad-account-lockout-policies"></a>Certifique-se as políticas de bloqueio de conta do AD

Utilize as instruções seguintes para verificar as políticas de bloqueio da conta do AD:

1.  Abra a ferramenta de gestão de políticas de grupo.
2.  Edite a política de grupo é aplicada a todos os utilizadores, por exemplo, a política de domínio predefinida.
3.  Navegue até à política de bloqueio de computador Configuration\Policies\Windows definições Settings\Account Policies\Account.
4.  Certifique-se os valores de limiar de bloqueio de conta e de reposição de conta bloqueio contador depois.

![Políticas de bloqueio da conta do AD](./media/active-directory-aadconnect-pass-through-authentication/pta5.png)

## <a name="use-the-graph-api-to-manage-your-tenants-smart-lockout-values-needs-premium-license"></a>Utilize a Graph API gerir os valores do seu inquilino bloqueio inteligente (necessita de licença Premium)

>[!IMPORTANT]
>Modificar o limiar de bloqueio e valores de duração do bloqueio utilizando Graph API do Azure AD é uma funcionalidade do Azure AD Premium P2. Também tem de ser um Administrador Global no seu inquilino.

Pode utilizar [gráfico Explorer](https://developer.microsoft.com/graph/graph-explorer) para ler, definir e atualizar os valores de bloqueio inteligente do Azure AD. Mas também pode efetuar estas operações através de programação.

### <a name="read-smart-lockout-values"></a>Valores de bloqueio de Smart de leitura

Siga estes passos, leia os valores de bloqueio inteligente do inquilino:

1. Inicie sessão no Explorador do gráfico como um Administrador Global do seu inquilino. Se lhe for solicitado, conceda acesso para as permissões de pedido.
2. Clique em "Modificar permissões" e selecione a permissão "Directory.ReadWrite.All".
3. Configurar o pedido da Graph API da seguinte forma: Definir versão para "BETA", tipo de pedido "GET" e o URL para `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Clique em "Executar a consulta" para ver os valores de bloqueio inteligente do seu inquilino. Se ainda não definido valores do seu inquilino antes, pode ver um conjunto vazio.

### <a name="set-smart-lockout-values"></a>Definir valores de bloqueio inteligente

Siga estes passos para definir valores de bloqueio inteligente do seu inquilino (pela primeira vez apenas):

1. Inicie sessão no Explorador do gráfico como um Administrador Global do seu inquilino. Se lhe for solicitado, conceda acesso para as permissões de pedido.
2. Clique em "Modificar permissões" e selecione a permissão "Directory.ReadWrite.All".
3. Configurar o pedido da Graph API da seguinte forma: Definir versão para "BETA", tipo "POST" e o URL de pedido `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Copie e cole o seguinte pedido JSON para o campo "Corpo do pedido".
5. Clique em "Executar a consulta" para definir os valores de bloqueio inteligente do seu inquilino.

```
{
  "templateId": "5cf42378-d67d-4f36-ba46-e8b86229381d",
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "300"
    },
    {
      "name": "LockoutThreshold",
      "value": "5"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

>[!NOTE]
>Se não estiver a utilizá-los, pode deixar o **BannedPasswordList** e **EnableBannedPasswordCheck** valores como vazia ("") e "false", respetivamente.

Certifique-se de que definiu os valores de bloqueio inteligente do seu inquilino corretamente utilizando [estes passos](#read-smart-lockout-values).

### <a name="update-smart-lockout-values"></a>Atualize os valores de bloqueio inteligente

Siga estes passos para atualizar os valores do seu inquilino bloqueio inteligente (se já definiu-los antes):

1. Inicie sessão no Explorador do gráfico como um Administrador Global do seu inquilino. Se lhe for solicitado, conceda acesso para as permissões de pedido.
2. Clique em "Modificar permissões" e selecione a permissão "Directory.ReadWrite.All".
3. [Siga estes passos para ler os valores do seu inquilino bloqueio inteligente](#read-smart-lockout-values). Copiar o `id` valor (um GUID) do item com o "displayName" como "PasswordRuleSettings".
4. Configurar o pedido da Graph API da seguinte forma: Definir versão para "BETA", tipo de pedido "Corrigir" e o URL para `https://graph.microsoft.com/beta/<your-tenant-domain>/settings/<id>` -utilize o GUID do passo 3 para `<id>`.
5. Copie e cole o seguinte pedido JSON para o campo "Corpo do pedido". Altere os valores de bloqueio inteligente conforme apropriado.
6. Clique em "Executar a consulta" para atualizar os valores de bloqueio inteligente do seu inquilino.

```
{
  "values": [
    {
      "name": "LockoutDurationInSeconds",
      "value": "30"
    },
    {
      "name": "LockoutThreshold",
      "value": "4"
    },
    {
      "name" : "BannedPasswordList",
      "value": ""
    },
    {
      "name" : "EnableBannedPasswordCheck",
      "value": "false"
    }
  ]
}
```

Certifique-se de que foi atualizado com valores do seu inquilino bloqueio inteligente corretamente utilizando [estes passos](#read-smart-lockout-values).

## <a name="next-steps"></a>Passos seguintes
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - para apresentação de pedidos de funcionalidades de novo.
