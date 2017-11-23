---
title: 'O Azure AD Connect: Resolver problemas de conectividade | Microsoft Docs'
description: Explica como resolver problemas de conectividade com o Azure AD Connect.
services: active-directory
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 3aa41bb5-6fcb-49da-9747-e7a3bd780e64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: fa98672551a2089f1a306c838295dd1980da0bca
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/22/2017
---
# <a name="troubleshoot-connectivity-issues-with-azure-ad-connect"></a>Resolver problemas de conectividade com o Azure AD Connect
Este artigo explica como funciona a conectividade entre o Azure AD Connect e o Azure AD e como resolver problemas de conectividade. Estes problemas são mais prováveis ser vistos num ambiente com um servidor proxy.

## <a name="troubleshoot-connectivity-issues-in-the-installation-wizard"></a>Resolver problemas de conectividade no Assistente de instalação
O Azure AD Connect está a utilizar a autenticação moderna (utilizando a biblioteca ADAL) para autenticação. O Assistente de instalação e o motor de sincronização adequado necessitam de Machine. config para ser corretamente configurados, uma vez que estes dois são aplicações de .NET.

Neste artigo, mostramos como Fabrikam se liga ao Azure AD através do respetivo proxy. O servidor proxy é denominado fabrikamproxy e está a utilizar a porta 8080.

Primeiro tem de certificar-se [ **Machine. config** ](active-directory-aadconnect-prerequisites.md#connectivity) está corretamente configurada.  
![machineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/machineconfig.png)

> [!NOTE]
> Em alguns blogues de terceiros, são documentado que as alterações devem ser feitas miiserver.exe.config em vez disso. No entanto, este ficheiro é substituído em cada atualização, por isso, mesmo que se funciona durante a instalação inicial, o sistema deixa de funcionar actualização primeiro. Por esse motivo, recomenda-se ao atualizar a Machine. config em vez disso.
>
>

O servidor proxy tem de ter também os URLs necessários abertos. A lista oficial é descrita da [intervalos de endereços IP e URLs do Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

URLs, a tabela seguinte é o mínimo de bare absoluto para conseguir ligar ao Azure AD de todo. Esta lista não inclui quaisquer funcionalidades opcionais, tais como a repetição de escrita de palavras-passe ou o Azure AD Connect Health. São documentado aqui para ajudar na resolução de problemas para a configuração inicial.

| URL | Porta | Descrição |
| --- | --- | --- |
| mscrl.microsoft.com |HTTP/80 |Utilizado para transferir o CRL listas. |
| \*. verisign.com |HTTP/80 |Utilizado para transferir o CRL listas. |
| \*. entrust.com |HTTP/80 |Utilizado para transferir as listas de CRL para a MFA. |
| \*.windows.net |HTTPS/443 |Utilizado para iniciar sessão Azure AD. |
| Secure.aadcdn.microsoftonline p.com |HTTPS/443 |Utilizado para a MFA. |
| \*.microsoftonline.com |HTTPS/443 |Utilizado para configurar o seu diretório do Azure AD e importar/exportar dados. |

## <a name="errors-in-the-wizard"></a>Erros no Assistente
O Assistente de instalação está a utilizar dois contextos de segurança diferentes. Na página **ligar para o Azure AD**, está a utilizar o utilizador atualmente com sessão iniciado. Na página **configurar**, está a alterar o [conta que executa o serviço para o motor de sincronização](active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-account). Se existir um problema, aparece provavelmente já os **ligar para o Azure AD** página do assistente, uma vez que a configuração de proxy global.

Os seguintes problemas são os erros mais comuns que encontrar no Assistente de instalação.

### <a name="the-installation-wizard-has-not-been-correctly-configured"></a>O Assistente de instalação não foi corretamente configurado
Este erro ocorre quando o assistente próprio não é possível alcançar o proxy.  
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomachineconfig.png)

* Se vir este erro, verifique se o [Machine. config](active-directory-aadconnect-prerequisites.md#connectivity) foi configurado corretamente.
* Se o que procura correto, siga os passos no [verificar a conectividade de proxy](#verify-proxy-connectivity) para ver se o problema está presente fora, bem como o assistente.

### <a name="a-microsoft-account-is-used"></a>É utilizada uma conta Microsoft
Se utilizar um **conta Microsoft** em vez de um **escolares ou organização** conta, verá um erro genérico.  
![É utilizada uma Account da Microsoft](./media/active-directory-aadconnect-troubleshoot-connectivity/unknownerror.png)

### <a name="the-mfa-endpoint-cannot-be-reached"></a>Não é possível contactar o ponto final MFA
Este erro ocorre se o ponto final **https://secure.aadcdn.microsoftonline-p.com** não é possível aceder e o administrador global tiver a MFA ativada.  
![nomachineconfig](./media/active-directory-aadconnect-troubleshoot-connectivity/nomicrosoftonlinep.png)

* Se vir este erro, certifique-se de que o ponto final **secure.aadcdn.microsoftonline p.com** foi adicionado ao proxy.

### <a name="the-password-cannot-be-verified"></a>Não é possível verificar a palavra-passe
Se o Assistente de instalação for concluída com êxito na ligação para o Azure AD, mas a palavra-passe propriamente dito não é possível verificar a que ver este erro:  
![badpassword](./media/active-directory-aadconnect-troubleshoot-connectivity/badpassword.png)

* É a palavra-passe uma palavra-passe temporária e tem de ser alterada? É, na verdade, a palavra-passe correta? Tente iniciar sessão em https://login.microsoftonline.com (no outro computador do servidor do Azure AD Connect) e certifique-se de que a conta é utilizável.

### <a name="verify-proxy-connectivity"></a>Verifique a conectividade do proxy
Para verificar se o servidor do Azure AD Connect tem conectividade real com o Proxy e a Internet, utilize o PowerShell para ver se o proxy está a permitir o pedidos web, ou não. Na linha de comandos do PowerShell, execute `Invoke-WebRequest -Uri https://adminwebservice.microsoftonline.com/ProvisioningService.svc`. (É tecnicamente a primeira chamada para https://login.microsoftonline.com e este URI funciona bem, mas outro URI é mais rápido responder).

PowerShell utiliza a configuração em Machine. config para contactar o proxy. As definições no winhttp/netsh não devem afetar estes cmdlets.

Se o proxy está configurado corretamente, deve obter um Estado de êxito: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest200.png)

Se receber **não é possível ligar ao servidor remoto**, em seguida, o PowerShell está a tentar efetuar uma chamada direta sem utilizar o proxy ou DNS não está corretamente configurado. Certifique-se de que o **Machine. config** ficheiro está corretamente configurado.
![unabletoconnect](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequestunable.png)

Se o proxy não está corretamente configurado, receberá um erro: ![proxy200](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest403.png)
![proxy407](./media/active-directory-aadconnect-troubleshoot-connectivity/invokewebrequest407.png)

| Erro | Texto de erro | Comentar |
| --- | --- | --- |
| 403 |Proibido |O proxy não foi aberto para o URL pedido. Revê a configuração de proxy e certifique-se a [URLs](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) tenham sido abertos. |
| 407 |Autenticação de proxy necessária |O servidor proxy necessário um início de sessão e não foi fornecido nenhum. Se o servidor proxy requer autenticação, certifique-se para terem esta definição configurada no Config. Certifique-se também que estiver a utilizar as contas de domínio para o utilizador que executa o assistente e para a conta de serviço. |

### <a name="proxy-idle-timeout-setting"></a>Definição de tempo limite de inatividade do proxy
Quando o Azure AD Connect envia um pedido de exportação para o Azure AD, o Azure AD pode demorar até 5 minutos para processar o pedido antes de gerar uma resposta. Isto pode acontecer, especialmente se houver um número de objetos de grupo com associações grande incluídos no mesmo pedido de exportação. Certifique-se de que o limite de tempo inativo do Proxy está configurado para ser superior a cinco minutos. Caso contrário, o problema de conectividade intermitente com o Azure AD pode ser observado no servidor do Azure AD Connect.

## <a name="the-communication-pattern-between-azure-ad-connect-and-azure-ad"></a>O padrão de comunicação entre o Azure AD Connect e o Azure AD
Se seguiu todos estes passos anteriores e ainda não é possível ligar-se, poderá começar neste momento observar os registos de rede. Esta secção é documentar um padrão de conectividade normal e com êxito. Este é também listagem herrings red comuns que podem ser ignoradas quando estiver a ler os registos de rede.

* Existem chamadas para https://dc.services.visualstudio.com. Não é necessário ter abra este URL no proxy para a instalação teve êxito e destas chamadas podem ser ignoradas.
* Pode ver que a resolução de dns lista os anfitriões de ser o nsatc.net de espaço de nome DNS e outros espaços de nomes não em microsoftonline.com reais. No entanto, não existem quaisquer pedidos de serviço web nos nomes de servidor real e não terá de adicionar estes URLs para o proxy.
* Os pontos finais adminwebservice e provisioningapi são pontos finais de deteção e utilizadas para localizar o ponto final real para utilizar. Estes pontos finais são diferentes consoante a sua região.

### <a name="reference-proxy-logs"></a>Registos de proxy de referência
Eis uma captura de um registo de proxy real e a página do Assistente de instalação de onde foi efetuada (as entradas duplicadas para o ponto final do mesmo tem sido removidas). Nesta secção pode ser utilizada como uma referência para os seus próprios registos de proxy e de rede. Os pontos finais reais podem ser diferentes no seu ambiente (em particular esses URLs *itálico*).

**Ligar ao Azure AD**

| Hora | URL |
| --- | --- |
| 1/11/2016 8:31 |Connect://login.microsoftonline.com:443 |
| 1/11/2016 8:31 |Connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:32 |ligar: / /*bba800 âncora*. microsoftonline.com:443 |
| 1/11/2016 8:32 |Connect://login.microsoftonline.com:443 |
| 1/11/2016 8:33 |Connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:33 |ligar: / /*bwsc02 reencaminhamento*. microsoftonline.com:443 |

**Configurar**

| Hora | URL |
| --- | --- |
| 1/11/2016 8:43 |Connect://login.microsoftonline.com:443 |
| 1/11/2016 8:43 |ligar: / /*bba800 âncora*. microsoftonline.com:443 |
| 1/11/2016 8:43 |Connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |Connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |ligar: / /*bba900 âncora*. microsoftonline.com:443 |
| 1/11/2016 8:44 |Connect://login.microsoftonline.com:443 |
| 1/11/2016 8:44 |Connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:44 |ligar: / /*bba800 âncora*. microsoftonline.com:443 |
| 1/11/2016 8:44 |Connect://login.microsoftonline.com:443 |
| 1/11/2016 8:46 |Connect://provisioningapi.microsoftonline.com:443 |
| 1/11/2016 8:46 |ligar: / /*bwsc02 reencaminhamento*. microsoftonline.com:443 |

**Sincronização inicial**

| Hora | URL |
| --- | --- |
| 1/11/2016 8:48 |Connect://login.Windows.NET:443 |
| 1/11/2016 8:49 |Connect://adminwebservice.microsoftonline.com:443 |
| 1/11/2016 8:49 |ligar: / /*bba900 âncora*. microsoftonline.com:443 |
| 1/11/2016 8:49 |ligar: / /*bba800 âncora*. microsoftonline.com:443 |

## <a name="authentication-errors"></a>Erros de autenticação
Esta secção abrange erros que podem ser devolvidos a partir da ADAL (a biblioteca de autenticação utilizada pelo Azure AD Connect) e o PowerShell. O erro explicado deverá ajudá-lo a compreender os passos seguintes.

### <a name="invalid-grant"></a>Conceder inválido
Nome de utilizador ou palavra-passe inválidos. Para obter mais informações, consulte [a palavra-passe não é possível verificar](#the-password-cannot-be-verified).

### <a name="unknown-user-type"></a>Tipo de utilizador desconhecido
Diretório do Azure AD não é possível localizar ou resolvido. Talvez tenta iniciar sessão com um nome de utilizador num domínio não verificado?

### <a name="user-realm-discovery-failed"></a>Falha na deteção de Realm de utilizador
Problemas de configuração de rede ou proxy. Não é possível aceder à rede. Consulte [resolver problemas de conectividade no Assistente de instalação](#troubleshoot-connectivity-issues-in-the-installation-wizard).

### <a name="user-password-expired"></a>Palavra-passe do utilizador expirado
As suas credenciais expiraram. Altere a palavra-passe.

### <a name="authorizationfailure"></a>AuthorizationFailure
Problema desconhecido.

### <a name="authentication-cancelled"></a>Autenticação cancelada
O desafio de autenticação multifator (MFA) foi cancelado.

### <a name="connecttomsonline"></a>ConnectToMSOnline
A autenticação foi efetuada com êxito, mas o Azure AD PowerShell tiver um problema de autenticação.

### <a name="azurerolemissing"></a>AzureRoleMissing
A autenticação foi efetuada com êxito. Não é um administrador global.

### <a name="privilegedidentitymanagement"></a>PrivilegedIdentityManagement
A autenticação foi efetuada com êxito. Depois de ativar a gestão de identidades privilegiadas e atualmente não é um administrador global. Para obter mais informações, consulte [Privileged Identity Management](../active-directory-privileged-identity-management-getting-started.md).

### <a name="companyinfounavailable"></a>CompanyInfoUnavailable
A autenticação foi efetuada com êxito. Não foi possível obter as informações da empresa a partir do Azure AD.

### <a name="retrievedomains"></a>RetrieveDomains
A autenticação foi efetuada com êxito. Não foi possível obter as informações do domínio do Azure AD.

### <a name="unexpected-exception"></a>Exceção inesperada
Mostrado como erro inesperado no Assistente de instalação. Pode ocorrer se tentar utilizar um **Account Microsoft** em vez de um **conta profissional ou organização**.

## <a name="troubleshooting-steps-for-previous-releases"></a>Passos de resolução de problemas para as versões anteriores.
Iniciando com o número de compilação 1.1.105.0 (lançadas Fevereiro de 2016), o Assistente de início de sessão foi extinguida com as versões. Esta secção e a configuração já não devem ser necessárias, mas é mantida como referência.

Para o início de sessão único no Assistente para funcionar, winhttp tem de ser configurado. Esta configuração pode ser feita com [ **netsh**](active-directory-aadconnect-prerequisites.md#connectivity).  
![netsh](./media/active-directory-aadconnect-troubleshoot-connectivity/netsh.png)

### <a name="the-sign-in-assistant-has-not-been-correctly-configured"></a>O Assistente de início de sessão não foi corretamente configurado
Este erro ocorre quando o Assistente de início de sessão não é possível alcançar o proxy ou o proxy não está a permitir que o pedido.
![nonetsh](./media/active-directory-aadconnect-troubleshoot-connectivity/nonetsh.png)

* Se vir este erro, observe a configuração de proxy no [netsh](active-directory-aadconnect-prerequisites.md#connectivity) e certifique-se de que está correto.
  ![netshshow](./media/active-directory-aadconnect-troubleshoot-connectivity/netshshow.png)
* Se o que procura correto, siga os passos no [verificar a conectividade de proxy](#verify-proxy-connectivity) para ver se o problema está presente fora, bem como o assistente.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre como [Integrar as identidades no local ao Azure Active Directory](active-directory-aadconnect.md).
