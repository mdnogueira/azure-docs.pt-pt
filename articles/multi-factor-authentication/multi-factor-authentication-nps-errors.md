---
title: "Resolver códigos de erro para a extensão de NPS de MFA do Azure | Microsoft Docs"
description: "Obter ajuda a resolver problemas com a extensão NPS para o Azure multi-factor Authentication com específicas resoluções para mensagens de erro comuns"
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: it-pro
ms.openlocfilehash: 173353d67772c2549aa1b8ec9f2a471bd1c65677
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="resolve-error-messages-from-the-nps-extension-for-azure-multi-factor-authentication"></a>Resolver mensagens de erro da extensão do NPS para o multi-factor Authentication do Azure

Se encontrar erros com a extensão NPS para o Azure multi-factor Authentication, utilize este artigo para alcançar uma resolução mais rapidamente. 

## <a name="troubleshooting-steps-for-common-errors"></a>Passos de resolução de problemas para erros comuns

| Código de erro | Passos de resolução de problemas |
| ---------- | --------------------- |
| **CONTACT_SUPPORT** | [Contacte o suporte](#contact-microsoft-support)e mencionar a lista dos passos para recolher registos. Fornece o mesmo informações como pode sobre o que aconteceu antes do erro, incluindo o id de inquilino e o nome principal de utilizador (UPN). |
| **CLIENT_CERT_INSTALL_ERROR** | Poderá existir um problema com a forma como o certificado de cliente foi instalado ou associado ao seu inquilino. Siga as instruções em [a extensão de NPS do MFA de resolução de problemas](multi-factor-authentication-nps-extension.md#troubleshooting) para investigar problemas de certificado de cliente. |
| **ESTS_TOKEN_ERROR** | Siga as instruções em [a extensão de NPS do MFA de resolução de problemas](multi-factor-authentication-nps-extension.md#troubleshooting) para investigar o certificado de cliente e ADAL token problemas. |
| **HTTPS_COMMUNICATION_ERROR** | O servidor NPS não é possível receber respostas de MFA do Azure. Certifique-se de que as firewalls bidirectionally aberta para o tráfego de e para https://adnotifications.windowsazure.com |
| **HTTP_CONNECT_ERROR** | No servidor que executa a extensão NPS, certifique-se de que pode chegar https://adnotifications.windowsazure.com e https://login.microsoftonline.com/. Se esses sites não carregar, resolver problemas de conectividade nesse servidor. |
| **REGISTRY_CONFIG_ERROR** | Uma chave está em falta no registo para a aplicação, o que poderá ser porque a [script do PowerShell](multi-factor-authentication-nps-extension.md#install-the-nps-extension) não foi executado após a instalação. A mensagem de erro deverá incluir a chave em falta. Certifique-se de que a chave em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa. |
| **REQUEST_FORMAT_ERROR** <br> Atributo de userName\Identifier Radius obrigatório em falta um pedido RADIUS. Certifique-se de que o NPS está a receber pedidos RADIUS | Este erro normalmente reflete um problema de instalação. A extensão NPS deve ser instalada em servidores NPS que podem receber pedidos RADIUS. Os servidores NPS que são instalados como as dependências para serviços como o RDG e o RRAS não recebem pedidos radius. Extensão de NPS não funciona quando instalado durante essas instalações e erros de saída, uma vez que este não é possível ler os detalhes do pedido de autenticação. |
| **REQUEST_MISSING_CODE** | Certifique-se de que o protocolo de encriptação da palavra-passe entre os servidores NPS e NAS suporta o método de autenticação secundária que estiver a utilizar. **PAP** suporta todos os métodos de autenticação do MFA do Azure na nuvem: chamada telefónica, mensagem de texto unidirecional, a notificação de aplicação móvel e código de verificação da aplicação móvel. **CHAPV2** e **EAP** suportam a chamada telefónica e notificação da aplicação móvel. |
| **USERNAME_CANONICALIZATION_ERROR** | Certifique-se de que o utilizador esteja presente na instância do Active Directory no local e de que o serviço NPS tem permissões para aceder ao diretório. Se estiver a utilizar confiança entre florestas, [contacte o suporte](#contact-microsoft-support) para obter ajuda. |


   

### <a name="alternate-login-id-errors"></a>Erros de ID de início de sessão alternativo

| Código de erro | Mensagem de erro | Passos de resolução de problemas |
| ---------- | ------------- | --------------------- |
| **ALTERNATE_LOGIN_ID_ERROR** | Erro: pesquisa de userObjectSid falhada | Certifique-se de que o utilizador existir na instância do Active Directory no local. Se estiver a utilizar confiança entre florestas, [contacte o suporte](#contact-microsoft-support) para obter ajuda. |
| **ALTERNATE_LOGIN_ID_ERROR** | Erro: Falha de pesquisa de LoginId alternativo | Certifique-se de que LDAP_ALTERNATE_LOGINID_ATTRIBUTE está definido como um [atributos do Active Directory válido](https://msdn.microsoft.com/library/ms675090(v=vs.85).aspx). <br><br> Se LDAP_FORCE_GLOBAL_CATALOG está definido como True ou LDAP_LOOKUP_FORESTS está configurado com um valor não vazio, certifique-se de que configurou um Catálogo Global e que o atributo AlternateLoginId é adicionado ao mesmo. <br><br> Se LDAP_LOOKUP_FORESTS está configurado com um valor não vazio, certifique-se de que o valor é correto. Se existir mais do que um nome de floresta, os nomes têm de ser separados com ponto e vírgula, não espaços. <br><br> Se estes passos não corrigir o problema, [contacte o suporte](#contact-microsoft-support) para obter mais ajuda. |
| **ALTERNATE_LOGIN_ID_ERROR** | Erro: O valor de LoginId alternativa está vazio | Certifique-se de que o atributo AlternateLoginId está configurado para o utilizador. |


## <a name="errors-your-users-may-encounter"></a>Podem encontrar os seus utilizadores de erros

| Código de erro | Mensagem de erro | Passos de resolução de problemas |
| ---------- | ------------- | --------------------- |
| **AccessDenied** | Inquilino do autor da chamada não tem permissões de acesso para efetuar a autenticação para o utilizador | Verifique se o domínio de inquilino e o domínio do nome principal de utilizador (UPN) são as mesmas. Por exemplo, certifique-se de que user@contoso.com está a tentar autenticar para o inquilino Contoso. O UPN representa um utilizador válido para o inquilino no Azure. |
| **AuthenticationMethodNotConfigured** | O método de autenticação especificado não foi configurado para o utilizador | Tem do utilizador adicione ou verifique se os respetivos métodos de verificação, de acordo com as instruções em [gerir as definições de verificação em dois passos](./end-user/multi-factor-authentication-end-user-manage-settings.md). |
| **AuthenticationMethodNotSupported** | Método de autenticação especificado não é suportado. | Recolher todos os seus registos que incluem este erro, e [contacte o suporte](#contact-microsoft-support). Quando contactar o suporte, forneça o nome de utilizador e o método de verificação secundário que acionou o erro. |
| **BecAccessDenied** | Chamada no MSODS Bec devolveu acesso negado, provavelmente, o nome de utilizador não está definido no inquilino do | O utilizador está presente no Active Directory no local, mas não está sincronizado para o Azure AD por AD Connect. Em alternativa, o utilizador está em falta para o inquilino. Adicionar o utilizador para o Azure AD e os adicionar os respetivos métodos de verificação, de acordo com as instruções em [gerir as definições de verificação em dois passos](./end-user/multi-factor-authentication-end-user-manage-settings.md). |
| **InvalidFormat** ou **StrongAuthenticationServiceInvalidParameter** | O número de telefone está num formato irreconhecível | Tem de corrigir os respetivos números de telefone de verificação de utilizador. |
| **InvalidSession** | A sessão especificada é inválida ou pode ter expirado | A sessão demorou mais do que três minutos a concluir. Certifique-se de que o utilizador é introduzir o código de verificação ou a responder à notificação da aplicação, com três minutos de iniciar o pedido de autenticação. Se que não corrigir o problema, verifique que não existem nenhum latências de rede entre o cliente, o servidor de NAS, o servidor NPS e o ponto final do MFA do Azure.  |
| **NoDefaultAuthenticationMethodIsConfigured** | Não foi configurado nenhum método de autenticação predefinido para o utilizador | Tem do utilizador adicione ou verifique se os respetivos métodos de verificação, de acordo com as instruções em [gerir as definições de verificação em dois passos](./end-user/multi-factor-authentication-end-user-manage-settings.md). Certifique-se de que o utilizador tem escolhido um método de autenticação predefinido e configurar este método para a conta. |
| **OathCodePinIncorrect** | Código errado e pin introduzido. | Este erro não é esperado na extensão do NPS. Se o utilizador detetar, [contacte o suporte](#contact-microsoft-support) para resolução de problemas de ajuda. |
| **ProofDataNotFound** | Dados de prova não foi configurados para o método de autenticação especificado. | Tem do utilizador tente um método de verificação diferente ou adicione um novo métodos de verificação, de acordo com as instruções em [gerir as definições de verificação em dois passos](./end-user/multi-factor-authentication-end-user-manage-settings.md). Se o utilizador continuar a ver este erro depois de confirmar a que o método de verificação está corretamente configurado, [contacte o suporte](#contact-microsoft-support). |
| **SMSAuthFailedWrongCodePinEntered** | Código errado e pin introduzido. (OneWaySMS) | Este erro não é esperado na extensão do NPS. Se o utilizador detetar, [contacte o suporte](#contact-microsoft-support) para resolução de problemas de ajuda. |
| **TenantIsBlocked** | Inquilino está bloqueado | [Contacte o suporte](#contact-microsoft-support) com o ID de diretório da página de propriedades do Azure AD no portal do Azure. |
| **UserNotFound** | O utilizador especificado não foi encontrado | O inquilino já não é visível como o Active Directory no Azure AD. Verifique se a sua subscrição está ativa e tiver necessários primeiro as aplicações de terceiros. Certifique-se também o inquilino no requerente do certificado é conforme esperado e o certificado é ainda válido e registados no principal de serviço. |

## <a name="messages-your-users-may-encounter-that-arent-errors"></a>As mensagens que os utilizadores podem encontrar-se que não são erros

Por vezes, os utilizadores podem receber mensagens da multi-factor Authentication devido a falha do pedido de autenticação. Estes não são erros no produto de configuração, mas são avisos intencional explicar por que motivo foi negado um pedido de autenticação.

| Código de erro | Mensagem de erro | Passos recomendados | 
| ---------- | ------------- | ----------------- |
| **OathCodeIncorrect** | Código errado entered\OATH código incorreto | Não é um erro, o utilizador ter introduzido código errado. | O utilizador introduziu o código de errado. Este que tente novamente a pedir um novo código ou voltar a iniciar sessão. | 
| **SMSAuthFailedMaxAllowedCodeRetryReached** | Repetição de código permitido máxima atingida | O utilizador não foi possível o desafio de verificação demasiadas vezes. Consoante as suas definições, poderão ter de ser desbloqueado por um administrador agora.  |
| **SMSAuthFailedWrongCodeEntered** | Code mal introduzido/texto mensagem OTP incorreta | O utilizador introduziu o código de errado. Este que tente novamente a pedir um novo código ou voltar a iniciar sessão. |

## <a name="errors-that-require-support"></a>Erros que necessitam de suporte

Se encontrar um destes erros, recomendamos que lhe [contacte o suporte](#contact-microsoft-support) para obter ajuda diagnóstico. Não há nenhum conjunto padrão de passos que pode resolver estes erros. Quando contactar o suporte, certifique-se incluir como muito informações quanto possível sobre os passos que conduziram ao erro e as informações de inquilino.

| Código de erro | Mensagem de erro |
| ---------- | ------------- |
| **InvalidParameter** | Pedido não pode ser nulo |
| **InvalidParameter** | ObjectId não pode ser nula nem vazia para ReplicationScope: {0} |
| **InvalidParameter** | O comprimento de CompanyName \{0} \ é maior do que o comprimento máximo permitido {1} |
| **InvalidParameter** | UserPrincipalName não pode ser nulo nem estar vazio |
| **InvalidParameter** | O TenantId fornecido não se encontra no formato correto |
| **InvalidParameter** | SessionId não pode ser nulo nem estar vazio |
| **InvalidParameter** | Não foi possível resolver a qualquer ProofData pedido ou no Msods. O ProofData é desconhecido |
| **InternalError** |  |
| **OathCodePinIncorrect** |  |
| **VersionNotSupported** |  |
| **MFAPinNotSetup** |  |

## <a name="next-steps"></a>Passos seguintes

### <a name="troubleshoot-user-accounts"></a>Resolver problemas de contas de utilizador

Se os utilizadores são [problemas com verificação de dois passos](./end-user/multi-factor-authentication-end-user-troubleshoot.md), ajudar a problemas de Self-diagnosticá-las. 

### <a name="contact-microsoft-support"></a>Contacte o suporte da Microsoft

Se precisar de mais ajuda, contacte um técnico de suporte através de [suporte de servidor do Azure multi-factor Authentication](https://support.microsoft.com/oas/default.aspx?prid=14947). Quando contactar-nos, é útil se pode incluir a maior quantidade informações sobre o problema quanto possível. Pode fornecer as informações incluem a página onde vimos o erro, o código de erro específico, o ID de sessão específico, o ID do utilizador que vimos o erro e registos de depuração.

Para recolher registos de depuração para diagnóstico de suporte, utilize os seguintes passos: 

1. Abra uma linha de comandos de administrador e execute estes comandos:

   ```
   Mkdir c:\NPS
   Cd NPS
   netsh trace start Scenario=NetConnection capture=yes tracefile=c:\NPS\nettrace.etl
   logman create trace "NPSExtension" -ow -o c:\NPS\NPSExtension.etl -p {7237ED00-E119-430B-AB0F-C63360C8EE81} 0xffffffffffffffff 0xff -nb 16 16 -bs 1024 -mode Circular -f bincirc -max 4096 -ets
   logman update trace "NPSExtension" -p {EC2E6D3A-C958-4C76-8EA4-0262520886FF} 0xffffffffffffffff 0xff -ets
   ```

2. Reproduza o problema

3. Pare o rastreio com estes comandos:

   ```
   logman stop "NPSExtension" -ets
   netsh trace stop
   wevtutil epl AuthNOptCh C:\NPS\%computername%_AuthNOptCh.evtx
   wevtutil epl AuthZOptCh C:\NPS\%computername%_AuthZOptCh.evtx
   wevtutil epl AuthZAdminCh C:\NPS\%computername%_AuthZAdminCh.evtx
   Start .
   ```

4. Zip o conteúdo da pasta C:\NPS e anexe o ficheiro zipped para o caso de suporte.


