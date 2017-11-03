---
title: "Integração do Centro de dados do Azure pilha - identidade"
description: Saiba como integrar a pilha do Azure AD FS com o seu centro de dados do AD FS
services: azure-stack
author: troettinger
ms.service: azure-stack
ms.topic: article
ms.date: 10/20/2017
ms.author: victorh
keywords: 
ms.openlocfilehash: e43b9c7a854bc7150247a2b92d2d37ad6d74c705
ms.sourcegitcommit: 804db51744e24dca10f06a89fe950ddad8b6a22d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/30/2017
---
# <a name="azure-stack-datacenter-integration---identity"></a>Integração do Centro de dados do Azure pilha - identidade

*Aplica-se a: Azure pilha integrado sistemas*

Pilha do Azure pode ser implementada utilizando o Azure Active Directory (Azure AD) ou os serviços de Federação do Active Directory (AD FS) que os fornecedores de identidade. Esta opção têm de ser efetuada antes da implementação. A implementação através do AD FS é também referida como implementar a pilha do Azure no modo desligado.

A tabela seguinte mostra as diferenças entre as opções de duas identidade:


||Fisicamente desligada|Fisicamente ligados|
|---------|---------|---------|
|Faturação|Tem de ser capacidade<br> Apenas Enterprise Agreement (EA)|Capacidade ou Pay-como-utiliza<br>EA ou o fornecedor de solução em nuvem (CSP)|
|Identidade|Tem de ser do AD FS|Azure AD ou AD FS|
|Sindicação do Marketplace|Não se encontra disponível|Suportado<br>BYOL de licenciamento|
|Registo|Recomendado, necessita de suporte de dados amovível<br> e um dispositivo ligado separado.|Automatizada|
|Patch e atualização|Necessário, necessita de suporte de dados amovível<br> e um dispositivo ligado separado.|Pacote de atualização pode ser transferido diretamente<br> através da Internet para a pilha do Azure.|

> [!IMPORTANT]
> Não é possível mudar o fornecedor de identidade sem voltar a implementar a solução completa de pilha do Azure.

## <a name="active-directory-federation-services-and-graph"></a>Serviços de Federação do Active Directory e do Graph

Implementar com o AD FS permite identidades na floresta do Active Directory existente para autenticar com recursos na pilha do Azure. Esta floresta existente do Active Directory requer uma implementação do AD FS para permitir a criação de uma fidedignidade de Federação do AD FS.

A autenticação é uma parte da identidade. Para gerir a função de acesso baseado em controlo (RBAC) na pilha do Azure, o componente de gráfico tem de ser configurado. Quando o acesso a um recurso é delegado, a conta de utilizador na floresta do Active Directory existente, utilizando o protocolo LDAP procura o componente de gráfico.

![Arquitetura de pilha do AD FS do Azure](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

O AD FS existente é o conta token serviço de segurança (STS) que envia afirmações para a pilha do Azure AD FS (o STS de recurso). Na pilha do Azure, automatização cria a confiança do fornecedor de afirmações com o ponto final de metadados para o AD FS existente.

No AD FS existente, tem de ser configurada uma confiança de entidade confiadora. Este passo não é efetuado pela automatização e tem de ser configurado pela operadora de rede. O ponto final de metadados de pilha do Azure é descrito no ficheiro AzureStackStampDeploymentInfo.JSON ou através de ponto final com privilégios, executando o comando `Get-AzureStackInfo`.

A configuração de confiança de entidade entidade confiadora também necessita de configurar as regras de transformação de afirmações que são fornecidas pela Microsoft.

Para a configuração de gráfico, uma conta de serviço tem de ser desde que tenha a permissão no Active Directory existente de leitura. Esta conta é necessária como entrada para a automatização ativar cenários RBAC.

Para o último passo, um novo proprietário está configurado para a subscrição do fornecedor predefinido. Esta conta tem acesso total a todos os recursos quando tem sessão iniciada portal de administrador de pilha do Azure.

Requisitos:


|Componente|Requisito|
|---------|---------|
|Graph|Microsoft Active Directory 2012/2012 R2/2016|
|AD FS|Windows Server 2012/2012 R2/2016|

## <a name="setting-up-graph-integration"></a>Configurar a integração do gráfico

São necessárias como entradas para os parâmetros de automatização as seguintes informações:


|Parâmetro|Descrição|Exemplo|
|---------|---------|---------|
|CustomADGlobalCatalog|FQDN da floresta do Active Directory de destino<br>que pretende integrar com o|contoso.com|
|CustomADAdminCredentials|Um utilizador com permissão de leitura de LDAP|YOURDOMAIN\graphservice|

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>Criar conta de utilizador no Active Directory existente (opcional)

Opcionalmente, pode criar uma conta para o serviço de gráfico no Active Directory existente. Execute este passo se ainda não tiver uma conta que pretende utilizar.

1. No Active Directory existente, crie a seguinte conta de utilizador (recomendação):
   - Nome de utilizador: graphservice
   - Palavra-passe: utilizar uma palavra-passe segura<br>Configure a palavra-passe nunca expirem.

   Não é necessária nenhum permissões especiais ou associação

**Automatização de Acionador para configurar o gráfico**

Para este procedimento, utilize um computador na sua rede de centro de dados que pode comunicar com o ponto final com privilégios na pilha do Azure.

2. Abra uma sessão do Windows PowerShell elevada (executar como administrador) e ligar para o endereço IP do ponto final com privilégios. Utilize as credenciais para CloudAdmin para se autenticar.

   ```
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

3. Agora que o se estiver ligado ao ponto final com privilégios, execute os seguintes comandos. Quando lhe for pedido, especifique as credenciais da conta de utilizador que pretende utilizar para o serviço do gráfico (por exemplo, graphservice).

   `Register-DirectoryService -CustomADGlobalCatalog contoso.com`

   > [!IMPORTANT]
   > Aguarde que as credenciais de pop-up (Get-Credential não é suportado no ponto final com privilégios) e introduza as credenciais da conta de serviço do gráfico.

**Gráfico protocolos e portas**

Serviço de gráfico na pilha do Azure utiliza as seguintes portas e protocolos para comunicar com o destino do Active Directory:


|Tipo|Porta|Protocolo|
|---------|---------|---------|
|LDAP|389|TCP E UDP|
|LDAP SSL|636|TCP|
|LDAP GC|3268|TCP|
|SSL LDAP DE GC|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>Configurar a integração do AD FS ao transferir os metadados de Federação

As seguintes informações não são necessárias como entrada para os parâmetros de automatização:


|Parâmetro|Descrição|Exemplo|
|---------|---------|---------|
|CustomAdfsName|Nome do fornecedor de afirmações. <cr>Parece dessa forma, na página de destino do AD FS.|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|Ligação de metadados de Federação|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.XML|


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Automatização de Acionador para configurar a confiança do fornecedor de afirmações na pilha do Azure

Para este procedimento, utilize um computador que possa comunicar com o ponto final com privilégios na pilha do Azure. É esperado que o certificado utilizado pela conta de STS AD FS é considerado fidedigno pela pilha de Azure.

1. Abra uma sessão elevada do Windows PowerShell e ligue para o ponto final com privilégios.

   ```
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Agora que o se estiver ligado ao ponto final com privilégios, execute o seguinte comando utilizando os parâmetros adequados para o seu ambiente:

   `Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml`

3. Execute o seguinte comando para atualizar o proprietário da subscrição do fornecedor predefinida, utilizando os parâmetros adequados para o seu ambiente:

   `Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"`

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>Configurar a integração do AD FS, fornecendo o ficheiro de metadados de Federação

Utilize este método se a uma das condições seguintes forem verdadeira:

- A cadeia de certificados é diferente para o AD FS em comparação comparado todos os outros pontos finais na pilha do Azure.
- Não há sem conectividade de rede para o servidor do AD FS existente da instância do AD FS da pilha do Azure.

As seguintes informações não são necessárias como entrada para os parâmetros de automatização:


|Parâmetro|Descrição|Exemplo|
|---------|---------|---------|
|CustomAdfsName|Nome do fornecedor de afirmações. Parece que forma na página de destino do AD FS.|Contoso|
|CustomADFSFederationMetadataFile|Ficheiro de metadados de Federação|https://ad01.contoso.com/federationmetadata/2007-06/federationmetadata.XML|

### <a name="create-federation-metadata-file"></a>Criar ficheiro de metadados de Federação

Para o seguinte procedimento, tem de utilizar um computador que tenha conectividade de rede para a implementação do AD FS existente, o que torna-se a conta de STS. Além disso, os certificados necessários tem de estar instalados.

1. Abra uma sessão elevada do Windows PowerShell e execute o seguinte comando, utilizando os parâmetros adequados para o seu ambiente:

   ```
   [XML]$Metadata = Invoke-WebRequest -URI https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml -UseBasicParsing

   $Metadata.outerxml|out-file c:\metadata.xml
   ```

2. Copie o ficheiro de metadados para uma partilha que esteja acessível a partir do ponto final com privilégios.


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Automatização de Acionador para configurar a confiança do fornecedor de afirmações na pilha do Azure

Para este procedimento, utilize um computador que possa comunicar com o ponto final com privilégios na pilha do Azure.

1. Abra uma sessão elevada do Windows PowerShell e ligue para o ponto final com privilégios.

   ```
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Agora que o se estiver ligado ao ponto final com privilégios, execute o seguinte comando utilizando os parâmetros adequados para o seu ambiente:

   `Register-CustomAdfs -CustomAdfsName Contoso – CustomADFSFederationMetadataFile \\share\metadataexample.xml`

3. Execute o seguinte comando para atualizar o proprietário da subscrição do fornecedor predefinida, utilizando os parâmetros adequados para o seu ambiente:

   `Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"`

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>Configurar a entidade confiadora na implementação do AD FS existente (conta STS)

A Microsoft fornece um script que configura a confiança da entidade confiadora, incluindo as regras de transformação de afirmações. Utilizar o script é opcional, pois pode executar os comandos manualmente.

Pode transferir o script de programa auxiliar de [ferramentas de pilha do Azure](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity) no Github.

Se optar por executar manualmente os comandos, siga estes passos:

1. Copie o seguinte conteúdo para um ficheiro. txt (por exemplo, guardado como c:\ClaimRules.txt) no membro de instância ou farm de AD FS do seu centro de dados:

   ```
   @RuleTemplate = "LdapClaims"
   @RuleName = "Name claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "UPN claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "ObjectID claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid"]
   => issue(Type = "http://schemas.microsoft.com/identity/claims/objectidentifier", Issuer = c.Issuer, OriginalIssuer = c.OriginalIssuer, Value = c.Value, ValueType = c.ValueType);

   @RuleName = "Family Name and Given claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname", "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname"), query = ";sn,givenName;{0}", param = c.Value);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all Group SID claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
   => issue(claim = c);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all windows account name claims"
   c:[Type == http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname]
   => issue(claim = c);
   ```

2. Para ativar a autenticação baseada em formulários do Windows, abra uma sessão do Windows PowerShell como um utilizador elevados e execute o seguinte comando:

   `Set-AdfsProperties -WIASupportedUserAgents @("MSAuthHost/1.0/In-Domain","MSIPC","Windows Rights Management Client","Kloud")`

3. Para adicionar a confiança da entidade confiadora, execute o seguinte comando do Windows PowerShell na sua instância do AD FS ou um membro de farm. Certifique-se de que atualiza o ponto final do AD FS e aponte para o ficheiro criado no passo 1.

   **Para o AD FS 2016**

   `Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone"`

   **Para o AD FS 2012/2012 R2**

   `Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true`

   > [!IMPORTANT]
   > Tem de utilizar o snap-in MMC do AD FS para configurar as regras de autorização de emissão ao utilizar o Windows Server 2012 ou 2012 R2 AD FS.

4. Quando utilizar o Internet Explorer ou o browser Edge para aceder a pilha do Azure, deve ignorar enlaces de token. Caso contrário, as tentativas de início de sessão falharem. Na sua instância do AD FS ou um membro de farm, execute o seguinte comando:

   `Set-AdfsProperties -IgnoreTokenBinding $true`

## <a name="spn-creation"></a>Criação de SPN

Existem vários cenários que requerem a utilização de um nome principal de serviço (SPN) para autenticação. Seguem-se alguns exemplos:
- Utilização da CLI com a implementação do AD FS da pilha do Azure
- Pacote de gestão do System Center para o Azure pilha quando implementado com o AD FS
- Fornecedores de recursos na pilha do Azure quando implementado com o AD FS
- Várias aplicações
- É necessário um início de sessão não interativo

Para obter mais informações sobre como criar um SPN, consulte [criar serviço principal para o AD FS](https://docs.microsoft.com/en-us/azure/azure-stack/azure-stack-create-service-principals#create-service-principal-for-ad-fs).


## <a name="troubleshooting"></a>Resolução de problemas

### <a name="configuration-rollback"></a>Reversão da configuração

Se ocorrer um erro que deixa o ambiente num Estado em que já não pode autenticar, uma opção de reversão está disponível.

1. Abra uma sessão elevada do Windows PowerShell e execute os seguintes comandos:

   ```
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Em seguida, execute o seguinte cmdlet:

   `Reset-DatacenterIntegationConfiguration`

   Depois de executar a ação de reversão, todas as alterações de configuração são revertidas. É possível apenas a autenticação com o utilizador de "CloudAdmin" incorporada.

   > [!IMPORTANT]
   > Tem de configurar o proprietário original da subscrição de fornecedor predefinido

   `Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"`

### <a name="collecting-additional-logs"></a>Recolher registos adicionais

Se qualquer um dos cmdlets falharem, podem recolher registos adicionais utilizando o `Get-Azurestacklogs` cmdlet.

1. Abra uma sessão elevada do Windows PowerShell e execute os seguintes comandos:

   ```
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Em seguida, execute o seguinte cmdlet:

   `Get-AzureStackLog -OutputPath \\myworstation\AzureStackLogs -FilterByRole ECE`


## <a name="next-steps"></a>Passos seguintes

[Azure pilha integração do Centro de dados - publicar pontos finais](azure-stack-integrate-endpoints.md)
