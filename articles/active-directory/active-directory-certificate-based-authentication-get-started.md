---
title: "Authentication do Azure Active Directory baseada em certificados - introdução | Microsoft Docs"
description: "Saiba como configurar a autenticação baseada em certificado no seu ambiente"
author: MarkusVi
documentationcenter: na
manager: femila
ms.assetid: c6ad7640-8172-4541-9255-770f39ecce0e
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/13/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: 5e423ee6818c50775aa604891951c7ded2a84eb3
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Introdução à autenticação baseada em certificado no Azure Active Directory

Autenticação baseada em certificado permite-lhe ser autenticado pelo Azure Active Directory com um certificado de cliente num dispositivo Windows, Android ou iOS ao ligar a sua conta do Exchange online para:

- Aplicações móveis do Microsoft, tais como o Microsoft Outlook e o Microsoft Word   

- Clientes do Exchange ActiveSync (EAS)

Configurar esta funcionalidade elimina a necessidade de introduzir uma combinação de nome de utilizador e palavra-passe na determinadas correio e aplicações do Microsoft Office no seu dispositivo móvel.

Neste tópico:

- Fornece os passos para configurar e utilizar a autenticação baseada em certificado para os utilizadores de inquilinos Office 365 Enterprise, empresas, educação e US Government planos. Esta funcionalidade está disponível na pré-visualização na China do Office 365, US Government defesa e US Government Federal planos.

- Pressupõe que já tem um [infraestrutura de chaves públicas (PKI)](https://go.microsoft.com/fwlink/?linkid=841737) e [do AD FS](connect/active-directory-aadconnectfed-whatis.md) configurado.    


## <a name="requirements"></a>Requisitos

Para configurar a autenticação baseada em certificados, os seguintes devem ser verdadeiras:  

- Autenticação baseada em certificado (CBA) só é suportada para ambientes de Federated para aplicações de browser ou clientes nativos que utilizam autenticação moderna (ADAL). A única exceção é o Exchange Active Sync (EAS) para EXO que pode ser utilizado para contas, federadas e geridas.

- A autoridade de certificação de raiz e as autoridades de certificação intermédia tem de ser configuradas no Azure Active Directory.  

- Cada autoridade de certificação tem de ter uma lista de revogação de certificados (CRL) que pode ser referenciada através de um Internet com o URL.  

- Tem de ter autoridade, pelo menos, um certificado configurada no Azure Active Directory. Pode encontrar passos relacionados no [configurar as autoridades de certificação](#step-2-configure-the-certificate-authorities) secção.  

- Para clientes do Exchange ActiveSync, o certificado de cliente tem de ter o endereço de e-mail encaminhável do utilizador no Exchange online no nome do Principal ou o valor de nome de RFC822 do campo nome alternativo do requerente. Azure Active Directory mapeia o valor de RFC822 para o atributo de endereço Proxy no diretório.  

- O dispositivo de cliente tem de ter acesso à autoridade, pelo menos, um certificado que emite certificados de cliente.  

- Um certificado de cliente para autenticação de cliente tem de ter sido emitido para o cliente.  




## <a name="step-1-select-your-device-platform"></a>Passo 1: Selecione a sua plataforma de dispositivos

Como primeiro passo, a plataforma de dispositivos que mais lhe interessam, terá de rever o seguinte:

- O suporte de aplicações móveis do Office
- Os requisitos de implementação específico  

As informações relacionadas existe para as seguintes plataformas de dispositivos:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)


## <a name="step-2-configure-the-certificate-authorities"></a>Passo 2: Configurar as autoridades de certificação

Para configurar as autoridades de certificação no Azure Active Directory, para cada autoridade de certificação, carregar o seguinte:

* A seção pública do certificado, no *. cer* formato
* A Internet com URLs onde residem os certificados listas de revogação (CRLs)

O esquema para uma autoridade de certificação procura da seguinte forma:

    class TrustedCAsForPasswordlessAuth
    {
       CertificateAuthorityInformation[] certificateAuthorities;    
    }

    class CertificateAuthorityInformation

    {
        CertAuthorityType authorityType;
        X509Certificate trustedCertificate;
        string crlDistributionPoint;
        string deltaCrlDistributionPoint;
        string trustedIssuer;
        string trustedIssuerSKI;
    }                

    enum CertAuthorityType
    {
        RootAuthority = 0,
        IntermediateAuthority = 1
    }

Para a configuração, pode utilizar o [do Azure Active Directory PowerShell versão 2](/powershell/azure/install-adv2?view=azureadps-2.0):  

1. Inicie o Windows PowerShell com privilégios de administrador.
2. Instale o módulo do Azure AD. Tem de instalar a versão [2.0.0.33 ](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) ou superior.  

        Install-Module -Name AzureAD –RequiredVersion 2.0.0.33

Como primeiro passo de configuração, tem de estabelecer uma ligação com o seu inquilino. Assim que existe uma ligação com o seu inquilino, pode rever, adicionar, eliminar e modificar as autoridades de certificação fidedignas que estão definidas no seu diretório.

### <a name="connect"></a>Ligar

Para estabelecer uma ligação com o seu inquilino, utilize o [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0) cmdlet:

    Connect-AzureAD


### <a name="retrieve"></a>Obter

Para obter as autoridades de certificação fidedignas que estão definidas no seu diretório, utilize o [Get-AzureADTrustedCertificateAuthority](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0) cmdlet.

    Get-AzureADTrustedCertificateAuthority


### <a name="add"></a>Adicionar

Para criar uma autoridade de certificação fidedigna, utilize o [New-AzureADTrustedCertificateAuthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority?view=azureadps-2.0) cmdlet e defina o **crlDistributionPoint** atributo para um valor correto:

    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint=”<CRL Distribution URL>”
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca


### <a name="remove"></a>Remover

Para remover uma autoridade de certificação fidedigna, utilize o [remover AzureADTrustedCertificateAuthority](/powershell/module/azuread/remove-azureadtrustedcertificateauthority?view=azureadps-2.0) cmdlet:

    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]


### <a name="modfiy"></a>Modfiy

Para modificar uma autoridade de certificação fidedigna, utilize o [conjunto AzureADTrustedCertificateAuthority](/powershell/module/azuread/set-azureadtrustedcertificateauthority?view=azureadps-2.0) cmdlet:

    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]


## <a name="step-3-configure-revocation"></a>Passo 3: Configurar revogação

Para revogar um certificado de cliente, o Azure Active Directory obtém a lista de revogação de certificados (CRL) dos URLs carregados como parte das informações de autoridade do certificado e coloca em cache-lo. A última publicar timestamp (**data efetiva** propriedade) no CRL é utilizado para garantir a CRL ainda é válida. A CRL periodicamente é referenciada para revogar o acesso aos certificados que fazem parte da lista.

Se um revogação instantânea mais for necessária (por exemplo, se um utilizador perde um dispositivo), o token de autorização do utilizador pode invalidado. Para invalidar o token de autorização, defina o **StsRefreshTokenValidFrom** campo para este utilizador em particular utilizando o Windows PowerShell. Tem de atualizar o **StsRefreshTokenValidFrom** para cada utilizador que pretende revogar o acesso de campo.

Para garantir que a revogação persistir, tem de definir o **data efetiva** da CRL para uma data posterior ao valor definido pelo **StsRefreshTokenValidFrom** e certifique-se de que o certificado em questão é no CRL.

Os passos seguintes descrevem o processo de atualização e invalidar o token de autorização, definindo o **StsRefreshTokenValidFrom** campo.

**Para configurar revogação:**

1. Ligar com as credenciais de administrador para o serviço MSOL:

        $msolcred = get-credential
        connect-msolservice -credential $msolcred

2. Obter o valor de StsRefreshTokensValidFrom atual para um utilizador:

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom

3. Configure um novo valor de StsRefreshTokensValidFrom para o igual de utilizador para o timestamp atual:

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")

A data definir tem de ser no futuro. Se a data não é no futuro, o **StsRefreshTokensValidFrom** propriedade não está definida. Se a data no futuro, **StsRefreshTokensValidFrom** está definido para a hora atual (não a data de indicada pelo comando Set-MsolUser).


## <a name="step-4-test-your-configuration"></a>Passo 4: Testar a configuração

### <a name="testing-your-certificate"></a>O certificado de teste

Como um teste de configuração primeiro, deve tentar iniciar sessão no [Outlook Web Access](https://outlook.office365.com) ou [SharePoint Online](https://microsoft.sharepoint.com) utilizando o **browser no dispositivo**.

Se o início de sessão for bem sucedida, em seguida, sabe que:

- O certificado de utilizador tiver sido aprovisionado para o seu dispositivo de teste
- AD FS está configurado corretamente  


### <a name="testing-office-mobile-applications"></a>Teste de aplicações móveis do Office

**Para testar a autenticação baseada em certificado na sua aplicação móvel do Office:**

1. No seu dispositivo de teste, instale uma aplicação móvel do Office (por exemplo, o OneDrive).
3. Inicie a aplicação.
4. Introduza o nome de utilizador e, em seguida, selecione o certificado de utilizador que pretende utilizar.

Que deve ser com êxito iniciou sessão.

### <a name="testing-exchange-activesync-client-applications"></a>Testar aplicações de cliente do Exchange ActiveSync

Para aceder ao Exchange ActiveSync (EAS) através da autenticação baseada em certificados, um perfil EAS, que contém o certificado de cliente tem de estar disponível para a aplicação.

O perfil EAS tem de conter as seguintes informações:

- O certificado de utilizador a ser utilizado para autenticação

- O ponto final do EAS (por exemplo, outlook.office365.com)

Um perfil EAS pode ser configurado e colocar o dispositivo através da utilização de gestão de dispositivos móveis (MDM), tais como o Intune ou colocando manualmente o certificado no perfil de EAS no dispositivo.  

### <a name="testing-eas-client-applications-on-android"></a>Aplicações de cliente EAS teste no Android

**Para testar a autenticação de certificado:**  

1. Configure um perfil EAS na aplicação que satisfaça os requisitos acima.  
2. Abra a aplicação e certifique-se de que o correio está a sincronizar.
