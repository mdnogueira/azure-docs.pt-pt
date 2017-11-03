---
title: 'O Azure AD Connect: Totalmente integrada Single Sign-On - perguntas mais frequentes | Microsoft Docs'
description: Respostas a perguntas mais frequentes sobre o Azure Active Directory totalmente integrada Single Sign-On.
services: active-directory
keywords: "o que é o Azure AD Connect, a instalação do Active Directory, os componentes necessários para o Azure AD, SSO, o início de sessão único"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2017
ms.author: billmath
ms.openlocfilehash: f603c4f0305184bfefe23a02b07cef134c83e678
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-seamless-single-sign-on-frequently-asked-questions"></a>Azure Active Directory totalmente integrada Single Sign-On: Perguntas mais frequentes

Neste artigo, vamos abordar perguntas mais frequentes sobre o Azure Active Directory totalmente integrada Single Sign-On (SSO totalmente integrado). Manter a verificação para o novo conteúdo.

## <a name="what-sign-in-methods-do-seamless-sso-work-with"></a>Os métodos de início de sessão funcionam SSO totalmente integrado com?

SSO totalmente integrada pode ser combinado com uma o [sincronização de Hash de palavra-passe](active-directory-aadconnectsync-implement-password-synchronization.md) ou [autenticação pass-through](active-directory-aadconnect-pass-through-authentication.md) métodos de início de sessão. No entanto esta funcionalidade não pode ser utilizada com serviços de Federação do Active Directory (ADFS).

## <a name="is-seamless-sso-a-free-feature"></a>É uma funcionalidade livre de SSO totalmente integrado?

SSO totalmente integrada é uma funcionalidade livre e não precisa de quaisquer edições pagas do Azure AD para utilizá-lo. Continua a ser livre quando a funcionalidade atinge disponibilidade geral.

## <a name="what-applications-take-advantage-of-domainhint-or-loginhint-parameter-capability-of-seamless-sso"></a>Que aplicações tirar partido das `domain_hint` ou `login_hint` capacidade de parâmetro do SSO totalmente integrado?

Estamos no processo de compilar a lista de aplicações que enviar estes parâmetros e aqueles que não. Se tiver aplicações estão interessadas nas, indique a secção de comentários.

## <a name="does-seamless-sso-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Suporta SSO totalmente integrado `Alternate ID` como o nome de utilizador, em vez de `userPrincipalName`?

Sim. Suporta SSO totalmente integrado `Alternate ID` como o nome de utilizador quando configurados no Azure AD Connect, conforme mostrado [aqui](active-directory-aadconnect-get-started-custom.md). Nem todas as aplicações do Office 365 suportam `Alternate ID`. Consulte a documentação específica da aplicação para a declaração de suporte.

## <a name="i-want-to-register-non-windows-10-devices-with-azure-ad-without-using-ad-fs-can-i-use-seamless-sso-instead"></a>Pretende registar dispositivos não Windows 10 com o Azure AD, sem utilizar o AD FS. Posso utilizar o SSO totalmente integrada em vez disso?

Sim, este cenário precisa de versão 2.1 ou posterior do [cliente associação à área de trabalho](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacc-computer-account"></a>Como pode posso rollover de chave de desencriptação Kerberos do `AZUREADSSOACC` conta de computador?

É importante frequentemente rollover de chave de desencriptação Kerberos do `AZUREADSSOACC` conta de computador (que representa o Azure AD) criada no seu local floresta do AD.

>[!IMPORTANT]
>Recomendamos vivamente que implementar através da chave de desencriptação de Kerberos, pelo menos, a cada 30 dias.

Siga estes passos no servidor no local onde está a executar o Azure AD Connect:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Passo 1. Obter a lista de florestas do AD onde SSO totalmente integrada foi ativada

1. Em primeiro lugar, transfira e instale o [assistente Microsoft Online Services início de sessão](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Em seguida, transferir e instalar o [módulo do Azure Active Directory de 64 bits do Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Navegue para o `%programfiles%\Microsoft Azure Active Directory Connect` pasta.
4. Importe o módulo do PowerShell de SSO totalmente integrada utilizando este comando: `Import-Module .\AzureADSSO.psd1`.
5. Execute o PowerShell como administrador. No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Este comando deverá dar-lhe um pop-up para introduzir as credenciais de Administrador Global do inquilino.
6. Chamar `Get-AzureADSSOStatus`. Este comando apresenta a lista de florestas do AD (ver a lista de "Domínios") em que esta funcionalidade foi ativada.

### <a name="step-2-update-the-kerberos-decryption-key-on-each-ad-forest-that-it-was-set-it-up-on"></a>Passo 2. Atualizar a chave de desencriptação de Kerberos em cada floresta do AD que este foi configurá-lo no

1. Chamar `$creds = Get-Credential`. Quando lhe for pedido, introduza as credenciais de administrador de domínio de floresta do AD pretendida.
2. Chamar `Update-AzureADSSOForest -OnPremCredentials $creds`. Este comando atualiza a chave de desencriptação de Kerberos para o `AZUREADSSOACC` conta de computador nesta floresta específica do AD e atualiza-o no Azure AD.
3. Repita os passos anteriores para cada floresta do AD que configurou a funcionalidade no.

>[!IMPORTANT]
>Certifique-se de que _não_ executar o `Update-AzureADSSOForest` comando mais do que uma vez. Caso contrário, a funcionalidade deixa de funcionar até que o tempo de permissões de Kerberos dos seus utilizadores expirarem e são emitido novamente pelo seu Active Directory no local.

## <a name="how-can-i-disable-seamless-sso"></a>Como desativar a SSO totalmente integrado?

Pode ser desativado SSO totalmente integrado com o Azure AD Connect.

Executar o Azure AD Connect, escolha "Alterar utilizador-página sessão" e clique em "Seguinte". Em seguida, desmarque a opção "Ativar o início de sessão único em". Continue com o assistente. Após a conclusão do assistente, SSO totalmente integrado está desativado no seu inquilino.

No entanto, verá uma mensagem no ecrã que lê da seguinte forma:

"O início de sessão único está agora desactivado, mas existem passos manuais adicionais para executar para concluir a limpeza. Saiba mais"

Para concluir o processo, siga estes passos manuais no servidor no local onde está a executar o Azure AD Connect:

### <a name="step-1-get-list-of-ad-forests-where-seamless-sso-has-been-enabled"></a>Passo 1. Obter a lista de florestas do AD onde SSO totalmente integrada foi ativada

1. Em primeiro lugar, transfira e instale o [assistente Microsoft Online Services início de sessão](http://go.microsoft.com/fwlink/?LinkID=286152).
2. Em seguida, transferir e instalar o [módulo do Azure Active Directory de 64 bits do Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).
3. Navegue para o `%programfiles%\Microsoft Azure Active Directory Connect` pasta.
4. Importe o módulo do PowerShell de SSO totalmente integrada utilizando este comando: `Import-Module .\AzureADSSO.psd1`.
5. Execute o PowerShell como administrador. No PowerShell, chame `New-AzureADSSOAuthenticationContext`. Este comando deverá dar-lhe um pop-up para introduzir as credenciais de Administrador Global do inquilino.
6. Chamar `Get-AzureADSSOStatus`. Este comando apresenta a lista de florestas do AD (ver a lista de "Domínios") em que esta funcionalidade foi ativada.

### <a name="step-2-manually-delete-the-azureadssoacct-computer-account-from-each-ad-forest-that-you-see-listed"></a>Passo 2. Elimine manualmente o `AZUREADSSOACCT` conta de computador de cada floresta do AD que vê listados.

## <a name="next-steps"></a>Passos seguintes

- [**Início Rápido** ](active-directory-aadconnect-sso-quick-start.md) - obter cópias de segurança e executar o SSO totalmente integrada de AD do Azure.
- [**Descrição detalhada da Technical** ](active-directory-aadconnect-sso-how-it-works.md) -compreender como funciona esta funcionalidade.
- [**Resolver problemas** ](active-directory-aadconnect-troubleshoot-sso.md) -Saiba como resolver problemas comuns com a funcionalidade.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - para apresentação de pedidos de funcionalidades de novo.
