---
title: "Do Azure AD Connect: Autenticação pass-through - bloqueio inteligente | Microsoft Docs"
description: "Este artigo descreve como autenticação de pass-through do Azure Active Directory (Azure AD) protege as contas no local contra ataques de palavra-passe de força bruta na nuvem"
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
ms.openlocfilehash: 2e1468c6a576ab71b85e3f69e5914df6ee9e4d5a
ms.sourcegitcommit: f67f0bda9a7bb0b67e9706c0eb78c71ed745ed1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2017
---
# <a name="azure-active-directory-pass-through-authentication-smart-lockout"></a>A autenticação pass-through do Azure Active Directory: Bloqueio inteligente

## <a name="overview"></a>Descrição geral

Azure Active Directory (Azure AD) protege contra ataques de palavra-passe de força bruta e impede que os utilizadores genuínos que está a ser bloqueado as aplicações do Office 365 e do SaaS. Esta capacidade, denominada *bloqueio inteligente*, é suportada quando utiliza a autenticação pass-through como método de início de sessão. Bloqueio inteligente está ativado por predefinição para todos os inquilinos e que protege continuamente as contas de utilizador.

Bloqueio inteligente mantém um registo dos tentativas de início de sessão falhadas. Depois de uma determinada *limiar de bloqueio*, este inicia um *duração do bloqueio*. Bloqueio inteligente rejeita todas as tentativas para iniciar sessão a partir do atacante durante o período de bloqueio. Se o ataque continua, subsequente falhada início de sessão tenta após a duração do bloqueio termina resultado no tempo durações de bloqueio.

>[!NOTE]
>O limiar de bloqueio predefinido é 10 tentativas falhadas. A duração do bloqueio de predefinição é 60 segundos.

Bloqueio inteligente também distingue entre inícios de sessão dos utilizadores genuínos e inícios de sessão de atacantes. Na maioria dos casos, bloqueia enviados apenas os atacantes. Esta funcionalidade impede os atacantes de forma maliciosa poderá utilizadores genuínos. Bloqueio inteligente utiliza passado o dos comportamento de início de sessão, os utilizadores dispositivos e browsers e outros sinalizar distinguir entre utilizadores genuínos e os atacantes. Os algoritmos são constantemente melhorados.

Autenticação pass-through reencaminha os pedidos de validação da palavra-passe no Active Directory no local, pelo que necessita de impedir atacantes de bloqueio de contas do Active Directory dos utilizadores. Do Active Directory tem as suas próprias políticas de bloqueio de conta, especificamente, [limiar de bloqueio de conta](https://technet.microsoft.com/library/hh994574(v=ws.11).aspx) e [reposição do contador de bloqueio da conta após](https://technet.microsoft.com/library/hh994568(v=ws.11).aspx) políticas. Configure os Azure AD bloqueio limiar e de bloqueio de valores de duração adequadamente para filtrar ataques na nuvem, antes de atingirem do Active Directory no local.

>[!NOTE]
>A funcionalidade de bloqueio inteligente está livre e é _no_ por predefinição para todos os clientes. Mas se pretender modificar do Azure AD bloqueio limiar e de bloqueio de valores de duração com Graph API, o seu inquilino tem de ter pelo menos uma licença do Azure AD Premium P2. Não precisa de uma licença do Azure AD Premium P2 _por utilizador_ para obter a funcionalidade de bloqueio inteligente com a autenticação pass-through.

Para garantir que contas de Active Directory no local dos seus utilizadores são bem protegidas, terá de se certificar de que:

   * O limiar de bloqueio do Azure AD é _menos_ ao limiar de bloqueio de conta do Active Directory. Defina os valores de forma a que o limiar de bloqueio de conta do Active Directory, pelo menos, dois ou três vezes superior ao limiar de bloqueio do Azure AD.
   * A duração do bloqueio do Azure AD (representada em segundos) é _mais_ que o Active Directory repor o contador de bloqueio da conta após a duração (representada em minutos).

>[!IMPORTANT]
>Um administrador não é possível desbloqueie as contas na nuvem se foi bloqueados para a capacidade de bloqueio inteligente. O administrador tem de aguardar o período de bloqueio expirar.

## <a name="verify-your-active-directory-account-lockout-policies"></a>Certifique-se as políticas de bloqueio de conta do Active Directory

Utilize as instruções seguintes para verificar as políticas de bloqueio de conta do Active Directory:

1.  Abra a ferramenta de gestão de políticas de grupo.
2.  Editar a política de grupo é aplicada a todos os utilizadores, por exemplo, o **política de domínio predefinida**.
3.  Navegue até à **configuração do computador** > **políticas** > **definições do Windows** > **definições de segurança**   >  **Políticas de conta** > **política de bloqueio de conta**.
4.  Certifique-se a **limiar de bloqueio de conta** e **reposição do contador de bloqueio da conta após** valores.

![Políticas de bloqueio de conta do Active Directory](./media/active-directory-aadconnect-pass-through-authentication/pta5.png)

## <a name="use-the-graph-api-to-manage-your-tenants-smart-lockout-values-requires-a-premium-license"></a>Utilize a Graph API gerir os valores do seu inquilino bloqueio inteligente (necessita de uma licença de Premium)

>[!IMPORTANT]
>A modificação do Azure AD bloqueio limiar e de bloqueio de valores de duração, utilizando a Graph API é uma funcionalidade do Azure AD Premium P2. Também tem de ser um administrador global no seu inquilino.

Pode utilizar [gráfico Explorer](https://developer.microsoft.com/graph/graph-explorer) para ler, definir e atualize os valores de bloqueio do Azure AD inteligente. Também pode efetuar estas operações através de programação.

### <a name="view-smart-lockout-values"></a>Valores de bloqueio inteligente de vista

Siga estes passos para ver os valores de bloqueio inteligente do inquilino:

1. Inicie sessão no Explorador do gráfico como um administrador global do seu inquilino. Se lhe for pedida, conceda acesso para as permissões de pedido.
2. Selecione **modificar permissões**e, em seguida, selecione o **Directory.ReadWrite.All** permissão.
3. Configurar o pedido da Graph API da seguinte forma: definir a versão para **BETA**, o tipo de pedido para **obter**e o URL para `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Selecione **executar consulta** para ver os valores de bloqueio inteligente do seu inquilino. Se ainda não definido valores do seu inquilino antes, pode ver um conjunto vazio.

### <a name="set-smart-lockout-values"></a>Definir valores de bloqueio inteligente

Siga estes passos para definir os valores de bloqueio inteligente do seu inquilino (necessários para a primeira vez):

1. Inicie sessão no Explorador do gráfico como um administrador global do seu inquilino. Se lhe for pedida, conceda acesso para as permissões de pedido.
2. Selecione **modificar permissões**e, em seguida, selecione o **Directory.ReadWrite.All** permissão.
3. Configurar o pedido da Graph API da seguinte forma: definir a versão para **BETA**, o tipo de pedido para **POST**e o URL para `https://graph.microsoft.com/beta/<your-tenant-domain>/settings`.
4. Copie e cole o seguinte pedido JSON para o **corpo do pedido** campo.
5. Selecione **executar consulta** para definir os valores de bloqueio inteligente do seu inquilino.

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
>Se não utilizá-los, pode deixar o **BannedPasswordList** e **EnableBannedPasswordCheck** valores como vazio (**""**) e **falso** respetivamente.

Certifique-se de que definiu os valores do seu inquilino bloqueio inteligente corretamente, utilizando os passos no [valores de bloqueio inteligente vista](#view-smart-lockout-values).

### <a name="update-smart-lockout-values"></a>Atualize os valores de bloqueio inteligente

Siga estes passos para atualizar os valores do seu inquilino bloqueio inteligente (se defini-los antes):

1. Inicie sessão no Explorador do gráfico como um administrador global do seu inquilino. Se lhe for pedida, conceda acesso para as permissões de pedido.
2. Selecione **modificar permissões**e, em seguida, selecione o **Directory.ReadWrite.All** permissão.
3. [Siga estes passos para ver os valores do seu inquilino bloqueio inteligente](#view-smart-lockout-values). Copiar o `id` valor (um GUID) do item com **displayName** como **PasswordRuleSettings**.
4. Configurar o pedido da Graph API da seguinte forma: definir a versão para **BETA**, o tipo de pedido para **PATCH**e o URL para `https://graph.microsoft.com/beta/<your-tenant-domain>/settings/<id>`. Utilize o GUID do passo 3 para `<id>`.
5. Copie e cole o seguinte pedido JSON para o **corpo do pedido** campo. Altere os valores de bloqueio inteligente conforme apropriado.
6. Selecione **executar consulta** para atualizar os valores de bloqueio inteligente do seu inquilino.

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

Certifique-se de que foi atualizado com os valores do seu inquilino bloqueio inteligente corretamente, utilizando os passos no [valores de bloqueio inteligente vista](#view-smart-lockout-values).

## <a name="next-steps"></a>Passos seguintes
[UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): Utilize o Fórum do Azure Active Directory para novos pedidos de funcionalidade de ficheiros.
