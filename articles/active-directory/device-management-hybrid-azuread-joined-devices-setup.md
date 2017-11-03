---
title: "Como configurar híbrida do Azure Active Directory dispositivos associados a um | Microsoft Docs"
description: "Saiba como configurar dispositivos do Azure Active Directory associados de híbrida."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/07/2017
ms.author: markvi
ms.reviewer: jairoc
ms.openlocfilehash: c43d6bcd62690fe41599888b06ee9828c8e40fc0
ms.sourcegitcommit: bd0d3ae20773fc87b19dd7f9542f3960211495f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/18/2017
---
# <a name="how-to-configure-hybrid-azure-active-directory-joined-devices"></a>Como configurar dispositivos do Azure Active Directory associados de híbrida

Gestão de dispositivos no Azure Active Directory (Azure AD), pode certificar-se de que os utilizadores acedem aos seus recursos dos dispositivos que cumprem as normas de segurança e conformidade. Para obter mais detalhes, consulte [introdução à gestão de dispositivos no Azure Active Directory](device-management-introduction.md).

Se tiver um ambiente do Active Directory no local e que pretende associar os dispositivos associados a um domínio para o Azure AD, pode realizar esta através da configuração híbrida do Azure AD associado dispositivos. O tópico fornece os passos relacionados. 


## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar a configuração híbrida do Azure AD associado dispositivos no seu ambiente, deve ser familiarizar com os cenários suportados e as restrições.  

Para melhorar a legibilidade das descrições, este tópico utiliza o termo seguinte: 

- **Dispositivos atuais do Windows** -este prazo refere-se a dispositivos associados a um domínio a executar o Windows 10 ou Windows Server 2016.
- **Dispositivos de nível inferior do Windows** -este prazo refere-se a todos os **suportado** dispositivos Windows associados a um domínio, que estão em execução Windows 10 nem do Windows Server 2016.  


### <a name="windows-current-devices"></a>Dispositivos atuais do Windows

- Para dispositivos que executem o ambiente de trabalho de sistema operativo do Windows, recomendamos que utilize o Windows 10 aniversário da atualização (versão 1607) ou posterior. 
- O registo de dispositivos atuais do Windows **é** suportada em ambientes não federada, tais como configurações de sincronização de hash de palavra-passe.  


### <a name="windows-down-level-devices"></a>Dispositivos de nível inferior do Windows

- São suportados os seguintes dispositivos de nível inferior do Windows:
    - Windows 8.1
    - Windows 7
    - Windows Server 2012 R2
    - Windows Server 2012
    - Windows Server 2008 R2
- O registo de dispositivos de nível inferior do Windows **é** suportada em ambientes não federada através de totalmente integrada de sessão único [do Azure Active Directory totalmente integrada Single Sign-On](https://aka.ms/hybrid/sso).
- O registo de dispositivos de nível inferior do Windows **não é** suportados para dispositivos com perfis itinerantes. Se estiver a depender de perfis ou definições de roaming, utilize o Windows 10.



## <a name="prerequisites"></a>Pré-requisitos

Antes de iniciar a ativação híbrida do Azure AD associado dispositivos na sua organização, tem de certificar-se de que está a executar uma versão atualizada do Azure AD connect.

Do Azure AD Connect:

- Mantém a associação entre a conta de computador no seu no local do Active Directory (AD) e o objeto de dispositivo no Azure AD. 
- Permite que outros dispositivos relacionados com funcionalidades como o Windows Hello para empresas.



## <a name="configuration-steps"></a>Passos de configuração

Pode configurar híbrida do Azure AD associado dispositivos para vários tipos de plataformas de dispositivos do Windows. Este tópico inclui os passos necessários para todos os cenários de configuração típica.  

Utilize a tabela seguinte para obter uma descrição geral dos passos necessários para o seu cenário:  



| Passos                                      | Sincronização de hash de atual e a palavra-passe do Windows | Atual do Windows e a Federação | Windows de nível inferior |
| :--                                        | :-:                                    | :-:                            | :-:                |
| Passo 1: Configurar o ponto de ligação de serviço | ![Marcar][1]                            | ![Marcar][1]                    | ![Marcar][1]        |
| Passo 2: Configurar a emissão de afirmações           |                                        | ![Marcar][1]                    | ![Marcar][1]        |
| Passo 3: Ativar dispositivos não Windows 10      |                                        |                                | ![Marcar][1]        |
| Passo 4: Implementação de controlo e implementação     | ![Marcar][1]                            | ![Marcar][1]                    | ![Marcar][1]        |
| Passo 5: Verificar se a dispositivos associados          | ![Marcar][1]                            | ![Marcar][1]                    | ![Marcar][1]        |



## <a name="step-1-configure-service-connection-point"></a>Passo 1: Configurar o ponto de ligação de serviço

O objeto de (SCP) do ponto de ligação de serviço é utilizado pelos seus dispositivos durante o registo para detetar informações de inquilino do Azure AD. No local no Active Directory (AD), o objeto de SCP de dispositivos híbridos do Azure AD associado tem de existir na configuração de atribuição de nomes de partição de contexto de floresta do computador. Não há apenas um contexto de nomenclatura de configuração por floresta. Numa configuração várias floresta do Active Directory, o ponto de ligação de serviço tem de existir em todas as florestas que contêm os computadores associados a um domínio.

Pode utilizar o [ **Get-ADRootDSE** ](https://technet.microsoft.com/library/ee617246.aspx) cmdlet para obter o contexto de nomenclatura de configuração da sua floresta.  

Para uma floresta com o nome de domínio do Active Directory *fabrikam.com*, é o contexto de nomenclatura de configuração:

`CN=Configuration,DC=fabrikam,DC=com`

Na sua floresta, o objeto de SCP para o registo automático de dispositivos associados a um domínio está localizado em:  

`CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,[Your Configuration Naming Context]`

Dependendo de como tiver implementado o Azure AD Connect, o objeto de SCP poderá já tiverem sido configurado.
Pode verificar a existência do objeto e obter os valores de deteção com o seguinte script do Windows PowerShell: 

    $scp = New-Object System.DirectoryServices.DirectoryEntry;

    $scp.Path = "LDAP://CN=62a0ff2e-97b9-4513-943f-0d221bd30080,CN=Device Registration Configuration,CN=Services,CN=Configuration,DC=fabrikam,DC=com";

    $scp.Keywords;

O **$scp. As palavras-chave** saída mostra as informações de inquilino do Azure AD, por exemplo:

    azureADName:microsoft.com
    azureADId:72f988bf-86f1-41af-91ab-2d7cd011db47

Se o ponto de ligação de serviço não existir, pode criá-la executando o `Initialize-ADSyncDomainJoinedComputerSync` cmdlet no seu servidor do Azure AD Connect. Credencial de administrador de empresa é necessária para executar este cmdlet.  
O cmdlet:

- Cria o ponto de ligação de serviço na floresta do Active Directory do Azure AD Connect está ligado. 
- Requer que especifique o `AdConnectorAccount` parâmetro. Esta é a conta que está configurada como o Active Directory a conta do conector no Azure AD connect. 


O script seguinte mostra um exemplo para utilizar o cmdlet. Este script, `$aadAdminCred = Get-Credential` requer que escreva um nome de utilizador. Tem de fornecer o nome de utilizador no formato de nome principal (UPN) utilizador (`user@example.com`). 


    Import-Module -Name "C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1";

    $aadAdminCred = Get-Credential;

    Initialize-ADSyncDomainJoinedComputerSync –AdConnectorAccount [connector account name] -AzureADCredentials $aadAdminCred;

O `Initialize-ADSyncDomainJoinedComputerSync` cmdlet:

- Utiliza o módulo PowerShell do Active Directory e as ferramentas do AD DS, que dependem de serviços da Web do Active Directory em execução num controlador de domínio. Serviços da Web do Active Directory é suportada em controladores de domínio com o Windows Server 2008 R2 e posterior.
- Só é suportado pelo **MSOnline PowerShell versão do módulo 1.1.166.0**. Para transferir este módulo, utilize esta opção [ligação](http://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185).   
- Se as ferramentas do AD DS não estiver instaladas, o `Initialize-ADSyncDomainJoinedComputerSync` irá falhar.  As ferramentas do AD DS podem ser instaladas através do Gestor de servidor em funcionalidades - ferramentas de administração de servidor remoto - ferramentas de administração de função.

Para controladores de domínio a executar o Windows Server 2008 ou de versões anteriores, utilize o script abaixo para criar o ponto de ligação de serviço.

Numa configuração de várias floresta, deve utilizar o script seguinte para criar o ponto de ligação de serviço em cada floresta onde existem computadores:
 
    $verifiedDomain = "contoso.com"    # Replace this with any of your verified domain names in Azure AD
    $tenantID = "72f988bf-86f1-41af-91ab-2d7cd011db47"    # Replace this with you tenant ID
    $configNC = "CN=Configuration,DC=corp,DC=contoso,DC=com"    # Replace this with your AD configuration naming context

    $de = New-Object System.DirectoryServices.DirectoryEntry
    $de.Path = "LDAP://CN=Services," + $configNC

    $deDRC = $de.Children.Add("CN=Device Registration Configuration", "container")
    $deDRC.CommitChanges()

    $deSCP = $deDRC.Children.Add("CN=62a0ff2e-97b9-4513-943f-0d221bd30080", "serviceConnectionPoint")
    $deSCP.Properties["keywords"].Add("azureADName:" + $verifiedDomain)
    $deSCP.Properties["keywords"].Add("azureADId:" + $tenantID)

    $deSCP.CommitChanges()


## <a name="step-2-setup-issuance-of-claims"></a>Passo 2: Configurar a emissão de afirmações

No Azure federado configuração do AD, dispositivos dependem de serviços de Federação do Active Directory (AD FS) ou um terceiros 3rd no local o serviço de Federação para autenticar com o Azure AD. Dispositivos autenticar-se para obter um token de acesso para registar contra o serviço de registo de dispositivos de diretório Active Directory do Azure (Azure DRS).

Windows dispositivos atuais autenticam utilizando a autenticação integrada do Windows para um ponto de final de WS-Trust Active Directory (versões 1.3 ou 2005) alojado pelo serviço de Federação no local.

> [!NOTE]
> Quando utilizar o AD FS, quer **adfs/services/confiança/13/windowstransport** ou **adfs/services/confiança/2005/windowstransport** tem de estar ativada. Se estiver a utilizar o Proxy Web da autenticação, certifique-se também que este ponto final está publicado através do proxy. Pode ver que os parâmetros são ativados através da consola de gestão do AD FS em **serviço > pontos finais**.
>
>Se não tiver o AD FS como o serviço de Federação no local, siga as instruções do seu fornecedor para se certificar de que suportam o WS-Trust 1.3 ou pontos finais de 2005 e que estes são publicadas através do ficheiro de troca de metadados (MEX).

As seguintes afirmações têm de existir no token recebido pelo Azure DRS para registo de dispositivos para concluir. Azure DRS irá criar um objeto de dispositivo no Azure AD com algumas destas informações para o qual é seguidamente utilizadas pelo Azure AD Connect para associar o objeto de dispositivo recentemente criado com a computador conta no local.

* `http://schemas.microsoft.com/ws/2012/01/accounttype`
* `http://schemas.microsoft.com/identity/claims/onpremobjectguid`
* `http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`

Se tiver mais do que um nome de domínio verificado, terá de fornecer a afirmação seguinte computadores:

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`

Se já estiver a emitir uma afirmação de ImmutableID (por exemplo, o ID de início de sessão alternativo) tem de fornecer uma afirmação correspondente para computadores:

* `http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`

As secções seguintes, encontrará informações sobre:
 
- Os valores de que cada afirmação deve ter
- Como uma definição deverá ter o seguinte aspeto no AD FS

A definição de ajuda-o para verificar se os valores estão presentes ou se terá de criá-los.

> [!NOTE]
> Se não utilizar o AD FS para o servidor de Federação no local, siga as instruções do fornecedor para criar a configuração adequada para emitir estes afirmações.

### <a name="issue-account-type-claim"></a>Afirmações de tipo de conta do problema

**`http://schemas.microsoft.com/ws/2012/01/accounttype`**-Esta afirmação tem de conter um valor de **DJ**, que identifica o dispositivo como um computador associado ao domínio. No AD FS, pode adicionar uma regra de transformação de emissão que tem este aspeto:

    @RuleName = "Issue account type for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "DJ"
    );

### <a name="issue-objectguid-of-the-computer-account-on-premises"></a>Emitir objectGUID do computador conta no local

**`http://schemas.microsoft.com/identity/claims/onpremobjectguid`**-Esta afirmação tem de conter o **objectGUID** valor da conta de computador local. No AD FS, pode adicionar uma regra de transformação de emissão que tem este aspeto:

    @RuleName = "Issue object GUID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );
 
### <a name="issue-objectsid-of-the-computer-account-on-premises"></a>Emitir Sidobjeto do computador conta no local

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`**-Esta afirmação tem de conter o o **Sidobjeto** valor da conta de computador local. No AD FS, pode adicionar uma regra de transformação de emissão que tem este aspeto:

    @RuleName = "Issue objectSID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(claim = c2);

### <a name="issue-issuerid-for-computer-when-multiple-verified-domain-names-in-azure-ad"></a>Emitir issuerID para computador quando múltiplos verificar nomes de domínio no Azure AD

**`http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid`**-Esta afirmação tem de conter o Uniform Resource Identifier (URI) de qualquer um dos nomes de domínio verificado que estabelecer ligação com o serviço de Federação no local (AD FS ou terceiros 3rd) emissora o token. No AD FS, pode adicionar regras de transformação de emissão que aspeto dos abaixo por essa ordem específica depois dos acima. Tenha em atenção que uma regra para emitir explicitamente a regra para os utilizadores é necessária. Em regras abaixo, é adicionada uma regra primeiro identificar utilizador vs. a autenticação de computador.

    @RuleName = "Issue account type with the value User when its not a computer"
    NOT EXISTS(
    [
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "DJ"
    ]
    )
    => add(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "User"
    );
    
    @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
    c1:[
        Type == "http://schemas.xmlsoap.org/claims/UPN"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "User"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = regexreplace(
        c1.Value, 
        ".+@(?<domain>.+)", 
        "http://${domain}/adfs/services/trust/"
        )
    );
    
    @RuleName = "Issue issuerID for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = "http://<verified-domain-name>/adfs/services/trust/"
    );


Na afirmação acima,

- `<verified-domain-name>`é um marcador de posição que é necessário substituir com um dos seus nomes de domínio verificado no Azure AD. Por exemplo, valor = "http://contoso.com/adfs/services/trust/"



Para obter mais detalhes sobre os nomes de domínio verificado, consulte [adicionar um nome de domínio personalizado ao Azure Active Directory](active-directory-add-domain.md).  
Para obter uma lista dos seus domínios verificados da empresa, pode utilizar o [Get-MsolDomain](/powershell/module/msonline/get-msoldomain?view=azureadps-1.0) cmdlet. 

![Get-MsolDomain](./media/active-directory-conditional-access-automatic-device-registration-setup/01.png)

### <a name="issue-immutableid-for-computer-when-one-for-users-exist-eg-alternate-login-id-is-set"></a>Emitir ImmutableID para computador quando existe uma para os utilizadores (por exemplo, início de sessão alternativo ID está definido)

**`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`**-Esta afirmação tem de conter um valor válido para computadores. No AD FS, pode criar uma regra de transformação de emissão da seguinte forma:

    @RuleName = "Issue ImmutableID for computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ] 
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );

### <a name="helper-script-to-create-the-ad-fs-issuance-transform-rules"></a>Script de programa auxiliar para criar regras de transformação de emissão do AD FS

O script seguinte ajuda-o com a criação de emissão descritas acima de regras de transformação.

    $multipleVerifiedDomainNames = $false
    $immutableIDAlreadyIssuedforUsers = $false
    $oneOfVerifiedDomainNames = 'example.com'   # Replace example.com with one of your verified domains
    
    $rule1 = '@RuleName = "Issue account type for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "DJ"
    );'

    $rule2 = '@RuleName = "Issue object GUID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/identity/claims/onpremobjectguid"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );'

    $rule3 = '@RuleName = "Issue objectSID for domain-joined computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(claim = c2);'

    $rule4 = ''
    if ($multipleVerifiedDomainNames -eq $true) {
    $rule4 = '@RuleName = "Issue account type with the value User when it is not a computer"
    NOT EXISTS(
    [
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "DJ"
    ]
    )
    => add(
        Type = "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value = "User"
    );
    
    @RuleName = "Capture UPN when AccountType is User and issue the IssuerID"
    c1:[
        Type == "http://schemas.xmlsoap.org/claims/UPN"
    ]
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2012/01/accounttype", 
        Value == "User"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = regexreplace(
        c1.Value, 
        ".+@(?<domain>.+)", 
        "http://${domain}/adfs/services/trust/"
        )
    );
    
    @RuleName = "Issue issuerID for domain-joined computers"
    c:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", 
        Value = "http://' + $oneOfVerifiedDomainNames + '/adfs/services/trust/"
    );'
    }

    $rule5 = ''
    if ($immutableIDAlreadyIssuedforUsers -eq $true) {
    $rule5 = '@RuleName = "Issue ImmutableID for computers"
    c1:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", 
        Value =~ "-515$", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ] 
    && 
    c2:[
        Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", 
        Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"
    ]
    => issue(
        store = "Active Directory", 
        types = ("http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID"), 
        query = ";objectguid;{0}", 
        param = c2.Value
    );'
    }

    $existingRules = (Get-ADFSRelyingPartyTrust -Identifier urn:federation:MicrosoftOnline).IssuanceTransformRules 

    $updatedRules = $existingRules + $rule1 + $rule2 + $rule3 + $rule4 + $rule5

    $crSet = New-ADFSClaimRuleSet -ClaimRule $updatedRules 

    Set-AdfsRelyingPartyTrust -TargetIdentifier urn:federation:MicrosoftOnline -IssuanceTransformRules $crSet.ClaimRulesString 

### <a name="remarks"></a>Observações 

- Este script acrescenta as regras para as regras existentes. Não execute o script duas vezes porque o conjunto de regras seria adicionado duas vezes. Certifique-se de que nenhum correspondentes existem regras para estas afirmações (sob condições correspondentes) antes de executar novamente o script.

- Se tiver vários nomes de domínio verificado (conforme ilustrado no portal do Azure AD ou através do cmdlet Get-MsolDomains), defina o valor da **$multipleVerifiedDomainNames** no script para **$true**. Certifique-se também que remova qualquer afirmação issuerid existente que poderá ter sido criada pelo Azure AD Connect ou através de outros meios. Eis um exemplo para esta regra:


        c:[Type == "http://schemas.xmlsoap.org/claims/UPN"]
        => issue(Type = "http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid", Value = regexreplace(c.Value, ".+@(?<domain>.+)",  "http://${domain}/adfs/services/trust/")); 

- Se já ter emitido uma **ImmutableID** de afirmação para contas de utilizador, defina o valor de **$immutableIDAlreadyIssuedforUsers** no script para **$true**.

## <a name="step-3-enable-windows-down-level-devices"></a>Passo 3: Ativar os dispositivos de nível inferior do Windows

Se alguns dos seus dispositivos associados a um domínio Windows nível dispositivos, tem de:

- Defina uma política no Azure AD para permitir aos utilizadores registar dispositivos.
 
- Configurar o serviço de Federação no local para emitir afirmações para suportar **autenticação integrada de Windows (IWA)** para registo de dispositivos.
 
- Adicione o ponto de final de autenticação de dispositivo do Azure AD para as zonas de Intranet locais, para evitar avisos de certificado ao autenticar o dispositivo.

### <a name="set-policy-in-azure-ad-to-enable-users-to-register-devices"></a>Definir a política no Azure AD para permitir aos utilizadores registar dispositivos

Para registar dispositivos de nível inferior do Windows, tem de certificar-se de que a definição para permitir aos utilizadores registar dispositivos no Azure AD está definida. No portal do Azure, pode encontrar esta definição em:

`Azure Active Directory > Users and groups > Device settings`
    
A seguinte política tem de ser definida **todos os**: **os utilizadores podem registar os seus dispositivos com o Azure AD**

![Registar dispositivos](./media/active-directory-conditional-access-automatic-device-registration-setup/23.png)


### <a name="configure-on-premises-federation-service"></a>Configurar o serviço de Federação no local 

O serviço de Federação no local tem de suportar a emitir o **authenticationmehod** e **wiaormultiauthn** afirmações quando recebe um pedido de autenticação para o Azure AD entidade confiadora que contém um parâmetro de resouce_params com um valor de codificação, como mostrado abaixo:

    eyJQcm9wZXJ0aWVzIjpbeyJLZXkiOiJhY3IiLCJWYWx1ZSI6IndpYW9ybXVsdGlhdXRobiJ9XX0

    which decoded is {"Properties":[{"Key":"acr","Value":"wiaormultiauthn"}]}

Quando é fornecido este pedido, o serviço de Federação no local tem de autenticar o utilizador através da autenticação integrada do Windows e após concluído com sucesso, tem de emitir dois afirmações seguintes:

    http://schemas.microsoft.com/ws/2008/06/identity/authenticationmethod/windows
    http://schemas.microsoft.com/claims/wiaormultiauthn

No AD FS, tem de adicionar uma regra de transformação de emissão que transmite-através do método de autenticação.  

**Para adicionar esta regra:**

1. Na consola de gestão do AD FS, aceda a `AD FS > Trust Relationships > Relying Party Trusts`.
2. Os objetos de confiança das entidades confiadoras plataforma de identidade do Microsoft Office 365 com o botão direito e, em seguida, selecione **editar regras de afirmação**.
3. No **regras de transformação de emissão** separador, selecione **Adicionar regra**.
4. No **regra de afirmação** lista de modelo, selecione **enviar afirmações utilizando uma regra personalizada**.
5. Selecione **seguinte**.
6. No **nome da regra de afirmação** caixa, escreva **regra de afirmação de método de autenticação**.
7. No **regra de afirmação** caixa, escreva a seguinte regra:

    `c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"] => issue(claim = c);`

8. No servidor de Federação, escreva o comando do PowerShell abaixo depois de substituir  **\<RPObjectName\>**  com o nome de objeto de terceiros entidade confiadora para os objetos de confiança das entidades confiadoras do Azure AD. Este objeto é normalmente denominado **plataforma de identidade do Microsoft Office 365**.
   
    `Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn`

### <a name="add-the-azure-ad-device-authentication-end-point-to-the-local-intranet-zones"></a>Adicionar o ponto de final de autenticação de dispositivo do Azure AD para as zonas de Local Intranet

Para evitar certificado pede-lhe quando autenticam os utilizadores no registo de dispositivos para o Azure AD, pode emitir uma política para os seus dispositivos associados a um domínio para adicionar o seguinte URL para a zona de Local Intranet no Internet Explorer:

`https://device.login.microsoftonline.com`

## <a name="step-4-control-deployment-and-rollout"></a>Passo 4: Implementação de controlo e implementação

Quando tiver concluído os passos necessários, os dispositivos associados a um domínio, estará pronto associar automaticamente do Azure AD:

- Dispositivos de todos os associados a um domínio que executam o Windows 10 aniversário da atualização e o Windows Server 2016 automaticamente registar com o Azure AD no dispositivo reinicie ou sessão do utilizador. 

- Novos dispositivos registar com o Azure AD quando o dispositivo for reiniciado depois de concluída a operação de associação de domínio.

- Dispositivos que foram anteriormente do Azure AD registado (por exemplo, para o Intune) transição para "*associados a um domínio, registado no AAD*"; no entanto, demora algum tempo para que este processo em todos os dispositivos devido ao fluxo normal da atividade de utilizador e domínio.

### <a name="remarks"></a>Observações

- Pode utilizar um objeto de política de grupo para controlar a implementação do registo automático do Windows 10 e computadores associados a domínios do Windows Server 2016.

- Windows 10 de Novembro de 2015 Update automaticamente associa com o Azure AD **apenas** se o objeto de política de grupo de implementação estiver definido.

- A implementação de computadores de nível inferior do Windows, pode implementar um [pacote do Windows Installer](#windows-installer-packages-for-non-windows-10-computers) para computadores que selecionou.

- Se enviar o objeto de política de grupo para dispositivos associados a domínios do Windows 8.1, é tentada uma associação; No entanto, é recomendado que utilize o [pacote do Windows Installer](#windows-installer-packages-for-non-windows-10-computers) para associar a todos os dispositivos de nível inferior do Windows. 

### <a name="create-a-group-policy-object"></a>Criar um objeto de política de grupo 

Para controlar a implementação de computadores atuais do Windows, deve implementar o **registar computadores associados a um domínio como dispositivos** objeto de política de grupo para os dispositivos que pretende registar. Por exemplo, pode implementar a política para uma unidade organizacional ou para um grupo de segurança.

**Para definir a política:**

1. Abra **Gestor de servidor**e, em seguida, aceda a `Tools > Group Policy Management`.
2. Vá para o nó de domínio que corresponde ao domínio onde pretende ativar o registo automático de computadores atuais do Windows.
3. Clique com botão direito **objetos de política de grupo**e, em seguida, selecione **novo**.
4. Escreva um nome para o objeto de política de grupo. Por exemplo, * associação do Azure AD híbrido. 
5. Clique em **OK**.
6. Clique com o botão direito do novo objeto de política de grupo e, em seguida, selecione **editar**.
7. Aceda a **configuração do computador** > **políticas** > **modelos administrativos** > **componentes do Windows** > **registo de dispositivos**. 
8. Clique com botão direito **registar computadores associados a um domínio como dispositivos**e, em seguida, selecione **editar**.
   
   > [!NOTE]
   > Este modelo de política de grupo foi alterado de versões anteriores da consola de gestão de política de grupo. Se estiver a utilizar uma versão anterior da consola, aceda a `Computer Configuration > Policies > Administrative Templates > Windows Components > Workplace Join > Automatically workplace join client computers`. 

7. Selecione **ativado**e, em seguida, clique em **aplicar**.
8. Clique em **OK**.
9. Ligar o objeto de política de grupo para uma localização da sua preferência. Por exemplo, pode associá-lo a uma unidade organizacional específica. Também foi ligue-o a um grupo de segurança específicos de computadores que associar automaticamente com o Azure AD. Para configurar esta política de todos os computadores associados a domínios Windows 10 e Windows Server 2016 na sua organização, ligar o objeto de política de grupo ao domínio.

### <a name="windows-installer-packages-for-non-windows-10-computers"></a>Pacotes do Windows Installer para computadores não - Windows 10

Para associar computadores associados a domínios de nível inferior de Windows num ambiente federado, pode transferir e instalar estas pacote do Windows Installer (. msi) a partir do Centro de transferências no [associação de área de trabalho da Microsoft para computadores Windows de 10](https://www.microsoft.com/en-us/download/details.aspx?id=53554) página.

Pode implementar o pacote utilizando um sistema de distribuição de software, como o System Center Configuration Manager. O pacote de suporte as opções de instalação silenciosa padrão com o *silenciosos* parâmetro. System Center Configuration Manager Current Branch oferece vantagens adicionais de versões anteriores, como a capacidade de controlar registos concluídos. Para obter mais informações, consulte [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager).

O instalador cria uma tarefa agendada no sistema que é executado no contexto do utilizador. A tarefa é acionada quando o utilizador inicia sessão Windows. A tarefa associa automaticamente o dispositivo com o Azure AD com as credenciais de utilizador após a autenticação utilizando a autenticação integrada do Windows. Para obter a tarefa agendada, no dispositivo, vá para **Microsoft** > **Workplace Join**e, em seguida, aceda à biblioteca do Programador de tarefas.

## <a name="step-5-verify-joined-devices"></a>Passo 5: Verificar se a dispositivos associados

Pode verificar os dispositivos associados com êxito na sua organização utilizando o [Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice) cmdlet no [módulo Azure Active Directory PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

O resultado deste cmdlet mostra os dispositivos que são registados e associados com o Azure AD. Para obter todos os dispositivos, utilize o **-todos os** parâmetro e, em seguida, filtre-los utilizando o **deviceTrustType** propriedade. Associado a um domínio dispositivos têm um valor de **associados a um domínio**.

## <a name="next-steps"></a>Passos seguintes

* [Introdução à gestão de dispositivos no Azure Active Directory](device-management-introduction.md)



<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
