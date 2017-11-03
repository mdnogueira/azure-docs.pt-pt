---
title: "Certificado de renovação para utilizadores do Office 365 e o Azure AD | Microsoft Docs"
description: "Este artigo explica como resolver problemas relacionados com e-mails que notificação-los sobre como renovar um certificado para utilizadores do Office 365."
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: curtand
ms.assetid: 543b7dc1-ccc9-407f-85a1-a9944c0ba1be
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2017
ms.author: billmath
ms.openlocfilehash: 675f5b31eb60a75e060a397f01777e427c068c64
ms.sourcegitcommit: 4ed3fe11c138eeed19aef0315a4f470f447eac0c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/23/2017
---
# <a name="renew-federation-certificates-for-office-365-and-azure-active-directory"></a>Renovar os certificados de Federação para o Office 365 e o Azure Active Directory
## <a name="overview"></a>Descrição geral
Para a Federação com êxito entre o Azure Active Directory (Azure AD) e serviços de Federação do Active Directory (AD FS), os certificados utilizados pelo AD FS para assinar os tokens de segurança para o Azure AD devem corresponder ao que está configurado no Azure AD. Qualquer erro de correspondência pode levar a confiança quebrada. Azure AD garante que são mantidas sincronizadas estas informações quando implementar o AD FS e Proxy de aplicações Web (para acesso à extranet).

Este artigo fornece informações adicionais para gerir os certificados de assinatura de tokens e mantê-las em sincronização com o Azure AD, nos seguintes casos:

* Não está a implementar o Proxy de aplicações Web e, por conseguinte, os metadados de Federação não estão disponível na extranet.
* A configuração predefinida do AD FS não estiver a utilizar para certificados de assinatura de tokens.
* Estão a utilizar um fornecedor de identidade de terceiros.

## <a name="default-configuration-of-ad-fs-for-token-signing-certificates"></a>Configuração predefinida do AD FS para certificados de assinatura de tokens
A assinatura de tokens certificados de desencriptação de tokens são certificados autoassinados, normalmente e são ideais para um ano. Por predefinição, o AD FS inclui um processo de renovação automática denominado **AutoCertificateRollover**. Se estiver a utilizar o AD FS 2.0 ou posterior, Office 365 e o Azure AD atualizar automaticamente o certificado antes de expirar.

### <a name="renewal-notification-from-the-office-365-portal-or-an-email"></a>Notificação de renovação do portal do Office 365 ou uma mensagem de e-mail
> [!NOTE]
> Se recebeu uma mensagem de e-mail ou uma portal notificação pedir-lhe para renovar o certificado para o Office, consulte [gerir alterações para certificados de assinatura de tokens](#managecerts) para verificar se é necessário efetuar qualquer ação. A Microsoft está em consideração um possível problema que pode levar para enviar notificações para a renovação de certificado que está a ser enviada, mesmo quando é necessária nenhuma ação.
>
>

Tenta do Azure AD monitorizar os metadados de Federação e atualizar o token de certificados de assinatura, conforme indicado por estes metadados. prazo de 30 dias antes da expiração do token de assinatura de certificados, do Azure AD verifica se os novos certificados estão disponíveis através de consultas de metadados de Federação.

* Se com êxito, pode consultar os metadados de Federação e obter novos certificados, nenhuma notificação por e-mail ou aviso no portal do Office 365 é emitido para o utilizador.
* Se este não é possível obter o token de novo certificados de assinatura, ou porque os metadados de Federação não estão acessível ou não está ativado o rollover de certificado automático, o Azure AD emite uma notificação por e-mail e um aviso no portal do Office 365.

![Notificação do portal do Office 365](./media/active-directory-aadconnect-o365-certs/notification.png)

> [!IMPORTANT]
> Se estiver a utilizar o AD FS, para assegurar a continuidade do negócio,. Certifique-se de que os servidores têm as seguintes atualizações para que não ocorrem falhas de autenticação para problemas conhecidos. Isto alivia problemas de servidor do AD FS proxy conhecidos para esta períodos de renovação futuras e renovação:
>
> Server 2012 R2 - [do Windows Server rollup de Maio de 2014](http://support.microsoft.com/kb/2955164)
>
> Server 2008 R2 e 2012 - [autenticação através do proxy de falha no Windows Server 2012 ou Windows 2008 R2 SP1](http://support.microsoft.com/kb/3094446)
>
>

## Verifique se os certificados precisam de ser atualizados<a name="managecerts"></a>
### <a name="step-1-check-the-autocertificaterollover-state"></a>Passo 1: Verificar o estado de AutoCertificateRollover
No servidor do AD FS, abra o PowerShell. Verifique se o valor de AutoCertificateRollover está definido como True.

    Get-Adfsproperties

![AutoCertificateRollover](./media/active-directory-aadconnect-o365-certs/autocertrollover.png)

>[!NOTE] 
>Se estiver a utilizar o AD FS 2.0, primeiro execute Add-Pssnapin Microsoft.Adfs.Powershell.

### <a name="step-2-confirm-that-ad-fs-and-azure-ad-are-in-sync"></a>Passo 2: Confirme que o AD FS e o Azure AD são sincronizadas
No servidor do AD FS, abra a linha de comandos da Azure AD PowerShell e ligar ao Azure AD.

> [!NOTE]
> Pode transferir o Azure AD PowerShell [aqui](https://technet.microsoft.com/library/jj151815.aspx).
>
>

    Connect-MsolService

Verificar os certificados configurados no AD FS e o Azure AD confia propriedades para o domínio especificado.

    Get-MsolFederationProperty -DomainName <domain.name> | FL Source, TokenSigningCertificate

![Get-MsolFederationProperty](./media/active-directory-aadconnect-o365-certs/certsync.png)

Se os thumbprints em ambas as saídas corresponderem, os seus certificados estão sincronizados com o Azure AD.

### <a name="step-3-check-if-your-certificate-is-about-to-expire"></a>Passo 3: Verificar se o certificado está prestes a expirar
Na saída de Get-MsolFederationProperty ou Get-AdfsCertificate, verifique a data em "Não após." Se a data for ausente menos de 30 dias, deverá tomar medidas.

| AutoCertificateRollover | Certificados sincronizados com o Azure AD | Os metadados de Federação são acessível publicamente | Validade | Ação |
|:---:|:---:|:---:|:---:|:---:|
| Sim |Sim |Sim |- |É necessária nenhuma ação. Consulte [assinatura de tokens de renovação de certificado automaticamente](#autorenew). |
| Sim |Não |- |Menos de 15 dias |Renove imediatamente. Consulte [assinatura de tokens de renovação de certificado manualmente](#manualrenew). |
| Não |- |- |Menos de 30 dias |Renove imediatamente. Consulte [assinatura de tokens de renovação de certificado manualmente](#manualrenew). |

\[Não importa -]

## Renovar o token de certificado de assinatura automaticamente (recomendado)<a name="autorenew"></a>
Não precisa de efetuar quaisquer passos manuais se ambos os procedimentos seguintes se verificarem:

* Implementou o Proxy de aplicações Web, que pode ativar o acesso aos metadados de Federação da extranet.
* Estão a utilizar a configuração do AD FS (AutoCertificateRollover está ativado).

Verifique o seguinte para confirmar que o certificado pode ser atualizado automaticamente.

**1. A propriedade do AD FS AutoCertificateRollover tem de ser definida como True.** Isto indica que o AD FS gerar automaticamente os certificados de desencriptação de tokens e de assinatura de tokens novo, antes do antigo aqueles expirarem.

**2. Os metadados de Federação do AD FS são acessível publicamente.** Certifique-se de que os metadados de Federação estão acessível publicamente, ao navegar para o seguinte URL de um computador com a internet pública (retire da rede empresarial):

/federationmetadata/2007-06/federationmetadata.xml https:// (your_FS_name)

onde `(your_FS_name) `é substituído com o nome de anfitrião do serviço de Federação utiliza a sua organização, por exemplo, fs.contoso.com.  Se for possível verificar ambos destas definições com êxito, não precisa de fazer mais nada.  

Exemplo: https://fs.contoso.com/federationmetadata/2007-06/federationmetadata.xml

## Renovar o token manualmente o certificado de assinatura<a name="manualrenew"></a>
Pode optar por renovar o token manualmente os certificados de assinatura. Por exemplo, poderão funcionar melhor para a renovação manual os seguintes cenários:

* Tokens de assinatura de certificados são os certificados autoassinados não. A razão mais comum para esta situação é que a sua organização gere certificados do AD FS inscritos a partir de uma autoridade de certificação organizacional.
* Segurança de rede não permite que os metadados de Federação publicamente disponíveis.

Nestes cenários, sempre que atualizar o token de assinatura de certificados, tem também de atualizar o domínio do Office 365, utilizando o comando do PowerShell, MsolFederatedDomain de atualização.

### <a name="step-1-ensure-that-ad-fs-has-new-token-signing-certificates"></a>Passo 1: Certifique-se de que o AD FS tem novos certificados de assinatura de tokens
**Configuração não predefinida**

Se estiver a utilizar uma configuração de não predefinidas do AD FS (onde **AutoCertificateRollover** está definido como **falso**), provavelmente, está a utilizar certificados personalizados (não autoassinados). Para obter mais informações sobre como renovar os certificados de assinatura de tokens do AD FS, consulte [orientação para os clientes não utilizar o AD FS autoassinado certificados](https://msdn.microsoft.com/library/azure/JJ933264.aspx#BKMK_NotADFSCert).

**Metadados de Federação não estão disponíveis publicamente**

Por outro lado, se **AutoCertificateRollover** está definido como **verdadeiro**, mas os metadados de Federação não estão acessível publicamente, primeiro certifique-se de que os novos certificados de assinatura de tokens tem sido gerados pelo AD FS. Confirme que tem o novo token de certificados de assinatura, efetuando os seguintes passos:

1. Certifique-se de que tem sessão iniciada num servidor do AD FS primário.
2. Verifique os certificados de assinatura atuais do AD FS ao abrir uma janela de comando do PowerShell e executando o seguinte comando:

    PS C:\>assinatura de tokens Get-ADFSCertificate – CertificateType

   > [!NOTE]
   > Se estiver a utilizar o AD FS 2.0, deve executar Add-Pssnapin Microsoft.Adfs.Powershell primeiro.
   >
   >
3. Observe a saída do comando em todos os certificados listados. Se o AD FS gerou um novo certificado, deverá ver dois certificados no resultado: um para o qual o **IsPrimary** valor é **verdadeiro** e o **NotAfter** data está dentro de 5 dias e outra para o qual **IsPrimary** é **falso** e **NotAfter** trata de um ano no futuro.
4. Se vir apenas um certificado e o **NotAfter** data está dentro de 5 dias, tem de gerar um novo certificado.
5. Para gerar um novo certificado, execute o seguinte comando numa linha de comandos do PowerShell: `PS C:\>Update-ADFSCertificate –CertificateType token-signing`.
6. Certifique-se a atualização, executando novamente o seguinte comando: PS c\>assinatura de tokens Get-ADFSCertificate – CertificateType

Dois certificados deverá estar listados agora, um deles tem uma **NotAfter** data de, aproximadamente, de um ano no futuro e para o qual o **IsPrimary** valor é **falso**.

### <a name="step-2-update-the-new-token-signing-certificates-for-the-office-365-trust"></a>Passo 2: Atualizar o novo token de assinatura de certificados para a confiança do Office 365
Atualize o Office 365 com o novo token certificados de assinatura para ser utilizada para a fidedignidade, da seguinte forma.

1. Abra o módulo do Microsoft Azure Active Directory para o Windows PowerShell.
2. Executar $cred = Get-Credential. Quando este cmdlet irá pedir-lhe credenciais, escreva as credenciais de conta de administrador de serviço de nuvem.
3. Executar Connect MsolService – $cred de credenciais. Este cmdlet liga ao serviço em nuvem. Criar um contexto que liga ao serviço em nuvem é necessário antes de executar qualquer um dos cmdlets adicionais instalados pela ferramenta.
4. Se estiver a executar estes comandos num computador que não seja o servidor de Federação principal do AD FS, execute Set-MSOLAdfscontext-computador <AD FS primary server>, onde <AD FS primary server> é o nome FQDN interno do servidor do AD FS principal. Este cmdlet cria um contexto que liga-o para o AD FS.
5. Executar atualização MSOLFederatedDomain – DomainName <domain>. Este cmdlet atualiza as definições do AD FS para o serviço de nuvem e configura a relação de confiança entre os dois.

> [!NOTE]
> Se tiver de suportar vários domínios de nível superior, por exemplo, contoso.com e fabrikam.com, tem de utilizar o **SupportMultipleDomain** mudar com quaisquer cmdlets. Para obter mais informações, consulte [suporte para múltiplos domínios de nível de topo](active-directory-aadconnect-multiple-domains.md).
>


## Reparar a confiança do Azure AD através do Azure AD Connect<a name="connectrenew"></a>
Se tiver configurado o farm do AD FS e a confiança do Azure AD através do Azure AD Connect, pode utilizar o Azure AD Connect para detetar se é necessário efetuar qualquer ação para os certificados de assinatura de tokens. Se precisar de renovar os certificados, pode utilizar o Azure AD Connect para fazê-lo.

Para obter mais informações, consulte [reparar a confiança](active-directory-aadconnect-federation-management.md).

## <a name="ad-fs-and-azure-ad-certificate-update-steps"></a>Passos de atualização de certificados do AD FS e o Azure AD
Token de certificados de assinatura de X509 padrão são certificados que são utilizados para assinar com segurança todos os tokens que o servidor de Federação emite. Os certificados de desencriptação de tokens são X509 padrão que são utilizados para desencriptar quaisquer entradas de tokens. 

Por predefinição, o AD FS está configurado para gerar automaticamente, certificados de desencriptação de tokens de assinatura de tokens e o momento da configuração inicial e quando os certificados são aproximar-se a respetiva data de expiração.

Azure AD tenta obter um novo certificado a partir do seu metadados do serviço de Federação 30 dias antes da expiração do certificado atual. No caso de não está disponível um novo certificado nessa altura, do Azure AD irá continuar a monitorizar os metadados em intervalos regulares de diários. Assim que o novo certificado está disponível nos metadados, as definições de Federação para o domínio são atualizadas com as novas informações de certificado. Pode utilizar `Get-MsolDomainFederationSettings` para verificar se vir o novo certificado no NextSigningCertificate / SigningCertificate.

Para obter mais informações sobre a assinatura de tokens certificados no AD FS consulte [obter e configurar a assinatura de tokens e certificados de desencriptação de tokens do AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ts-td-certs-ad-fs)