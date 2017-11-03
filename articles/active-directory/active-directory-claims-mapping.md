---
title: "Afirmações mapeamento no Azure Active Directory (pré-visualização pública) | Microsoft Docs"
description: "Esta página descreve o mapeamento de afirmações de Azure Active Directory."
services: active-directory
author: billmath
manager: femila
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2017
ms.author: billmath
ms.openlocfilehash: 78dbbe085fca26ad529c6262ba852f3c06ace404
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="claims-mapping-in-azure-active-directory-public-preview"></a>Afirmações mapeamento no Azure Active Directory (pré-visualização pública)

>[!NOTE]
>Esta funcionalidade substitui e substitui o [afirmações personalização](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization) oferecidas hoje através do portal. Se personalizar afirmações através do portal, além do método do gráfico/PowerShell detailhado neste documento na mesma aplicação, tokens emitiram para que a aplicação ignorará a configuração no portal.
Configurações efetuadas através de métodos detalhados neste documento não serão apresentadas no portal.

Esta funcionalidade é utilizada por administradores de inquilinos para personalizar as afirmações emitidas em tokens para uma aplicação específica no seu inquilino. Pode utilizar políticas para de mapeamento de afirmações:

- Selecione as afirmações estão incluídas em tokens.
- Crie tipos de afirmação que já existe.
- Escolher ou alterar a origem de dados emitidos nas afirmações específicas.

>[!NOTE]
>Esta capacidade está atualmente em pré-visualização pública. Esteja preparado para reverter ou remover quaisquer alterações. A funcionalidade está disponível em qualquer subscrição do Azure Active Directory (Azure AD) durante a pré-visualização pública. No entanto, quando a funcionalidade torna-se geralmente disponível, alguns aspetos da funcionalidade poderão necessitar de uma subscrição do Azure Active Directory premium.

## <a name="claims-mapping-policy-type"></a>Mapeamento de tipo de política de afirmações
No Azure AD, uma **política** objeto representa um conjunto de regras impostas em aplicações individuais ou em todas as aplicações numa organização. Cada tipo de política tem uma estrutura exclusiva, com um conjunto de propriedades que, em seguida, são aplicadas a objetos aos quais estão atribuídos.

As afirmações A política de mapeamento é um tipo de **política** objeto que modifica afirmações emitidas em tokens emitidos para aplicações específicas.

## <a name="claim-sets"></a>Conjuntos de afirmações
Existem determinados conjuntos de afirmações que definem como e quando são utilizados em tokens.

### <a name="core-claim-set"></a>Conjunto de afirmações de núcleo
Estão presentes em cada token, independentemente da política de afirmações no conjunto de afirmações de núcleos. Estas afirmações também são consideradas restrito e não podem ser modificadas.

### <a name="basic-claim-set"></a>Conjunto de afirmações básico
O conjunto de afirmações básico inclui as afirmações que são emitidas por predefinição para tokens (para além do conjunto de afirmações core). Estas afirmações podem ser omitidas ou modificadas ao utilizar as afirmações de mapeamento de políticas.

### <a name="restricted-claim-set"></a>Conjunto de afirmações restrito
Não não possível modificar afirmações restritas utilizando a política. Não é possível alterar a origem de dados e não a transformação é aplicada ao gerar estas afirmações.

#### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>Tabela 1: Conjunto de afirmações de restrito JSON Web tokens (JWT)
|Tipo de afirmação (nome)|
| ----- |
|_claim_names|
|_claim_sources|
|access_token|
|account_type|
|Acr|
|Ator|
|actortoken|
|aio|
|altsecid|
|amr|
|app_chain|
|app_displayname|
|app_res|
|appctx|
|appctxsender|
|AppID|
|appidacr|
|Asserção|
|at_hash|
|aud|
|auth_data|
|auth_time|
|authorization_code|
|azp|
|azpacr|
|c_hash|
|ca_enf|
|Cc|
|cert_token_use|
|client_id|
|cloud_graph_host_name|
|cloud_instance_name|
|cnf|
|código|
|controlos|
|credential_keys|
|CSR|
|csr_type|
|DeviceID|
|dns_names|
|domain_dns_name|
|domain_netbios_name|
|e_exp|
|Correio eletrónico|
|ponto final|
|enfpolids|
|EXP|
|expires_on|
|grant_type|
|Gráfico|
|group_sids|
|grupos|
|hasgroups|
|hash_alg|
|home_oid|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/AuthenticationInstant|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/AuthenticationMethod|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/Expiration|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/Expired|
|http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/emailaddress|
|http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/Name|
|http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/NameIdentifier|
|iat|
|identityprovider|
|IDP|
|in_corp|
|Instância|
|IPADDR|
|isbrowserhostedapp|
|ISS|
|jwk|
|key_id|
|key_type|
|mam_compliance_url|
|mam_enrollment_url|
|mam_terms_of_use_url|
|mdm_compliance_url|
|mdm_enrollment_url|
|mdm_terms_of_use_url|
|nameid|
|NBF|
|netbios_name|
|nonce|
|OID|
|on_prem_id|
|onprem_sam_account_name|
|onprem_sid|
|openid2_id|
|palavra-passe|
|platf|
|polids|
|pop_jwk|
|preferred_username|
|previous_refresh_token|
|primary_sid|
|PUID|
|pwd_exp|
|pwd_url|
|redirect_uri|
|refresh_token|
|refreshtoken|
|request_nonce|
|Recursos|
|Função|
|funções|
|Âmbito|
|SCP|
|SID|
|Assinatura|
|signin_state|
|src1|
|src2|
|sub|
|tbid|
|tenant_display_name|
|tenant_region_scope|
|thumbnail_photo|
|TID|
|tokenAutologonEnabled|
|trustedfordelegation|
|unique_name|
|UPN|
|user_setting_sync_url|
|o nome de utilizador|
|UTI|
|ver|
|verified_primary_email|
|verified_secondary_email|
|wids|
|win_ver|

#### <a name="table-2-security-assertion-markup-language-saml-restricted-claim-set"></a>Tabela 2: Security Assertion Markup Language (SAML) restringe o conjunto de afirmações
|Tipo de afirmação (URI)|
| ----- |
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/Expiration|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/Expired|
|http://schemas.microsoft.com/Identity/Claims/accesstoken|
|http://schemas.microsoft.com/Identity/Claims/openid2_id|
|http://schemas.microsoft.com/Identity/Claims/identityprovider|
|http://schemas.microsoft.com/Identity/Claims/objectidentifier|
|http://schemas.microsoft.com/Identity/Claims/PUID|
|http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/NameIdentifier [MR1] |
|http://schemas.microsoft.com/Identity/Claims/tenantid|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/AuthenticationInstant|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/AuthenticationMethod|
|http://schemas.microsoft.com/accesscontrolservice/2010/07/Claims/identityprovider|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/Groups|
|http://schemas.microsoft.com/Claims/Groups.Link|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/role|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/wids|
|http://schemas.microsoft.com/2014/09/devicecontext/Claims/iscompliant|
|http://schemas.microsoft.com/2014/02/devicecontext/Claims/isknown|
|http://schemas.microsoft.com/2012/01/devicecontext/Claims/ismanaged|
|http://schemas.microsoft.com/2014/03/psso|
|http://schemas.microsoft.com/Claims/authnmethodsreferences|
|http://schemas.xmlsoap.org/ws/2009/09/Identity/Claims/actor|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/samlissuername|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/confirmationkey|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/windowsaccountname|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/primarygroupsid|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/primarysid|
|http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/authorizationdecision|
|http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/Authentication|
|http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/SID|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/denyonlyprimarygroupsid|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/denyonlyprimarysid|
|http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/denyonlysid|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/denyonlywindowsdevicegroup|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/windowsdeviceclaim|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/windowsdevicegroup|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/windowsfqbnversion|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/windowssubauthority|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/windowsuserclaim|
|http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/x500distinguishedname|
|http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/UPN|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/GroupSID|
|http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/SPN|
|http://schemas.microsoft.com/ws/2008/06/Identity/Claims/ispersistent|
|http://schemas.xmlsoap.org/ws/2005/05/Identity/Claims/privatepersonalidentifier|
|http://schemas.microsoft.com/Identity/Claims/SCOPE|

## <a name="claims-mapping-policy-properties"></a>Mapeamento de propriedades da política de afirmações
Utilize as propriedades de uma política de mapeamento para o controlo que afirmações são emitidas e, em que os dados de origem de afirmações. Se nenhuma política estiver definida, o sistema emite tokens que contém o conjunto de afirmações core, o conjunto de afirmações básico e quaisquer afirmações opcionais que a aplicação tiver optado por receber.

### <a name="include-basic-claim-set"></a>Incluir o conjunto de afirmações básico

**Cadeia:** IncludeBasicClaimSet

**Tipo de dados:** booleano (VERDADEIRO ou FALSO)

**Resumo:** esta propriedade determina se o conjunto de afirmações básico está incluído em tokens afetados por esta política. 

- Se definido como True, todas as afirmações no conjunto de afirmações básicas é emitido em tokens afetados pela política. 
- Se definido como False, afirmações no conjunto de afirmações básico não estiverem em tokens, a menos que estes são adicionados individualmente na propriedade de esquema de afirmações da mesma política.

>[!NOTE] 
>Estão presentes em cada token, independentemente da que esta propriedade está definida como afirmações no conjunto de afirmações de núcleos. 

### <a name="claims-schema"></a>Esquema de afirmações

**Cadeia:** ClaimsSchema

**Tipo de dados:** blob JSON com uma ou mais entradas de esquema de afirmação

**Resumo:** esta propriedade define que afirmações estão presentes em tokens afetados pela política, além disso, ao conjunto de afirmações básico e o principal conjunto de afirmações.
Para cada entrada de esquema de afirmação definida nesta propriedade, determinadas informações são necessárias. Tem de especificar onde os dados for proveniente de (**valor** ou **par de ID de origem/**), e os dados de afirmação que é emitido como (**tipo de afirmação**).

### <a name="claim-schema-entry-elements"></a>Elementos de entrada do esquema de afirmação

**Valor:** o elemento de valor define um valor estático, como os dados a ser emitidos na afirmação.

**Par/ID de origem:** a origem e o ID de elementos definem onde os dados na afirmação de origem. 

O elemento de origem deve ser definido como um dos seguintes: 


- "utilizador": os dados na afirmação são uma propriedade no objeto User. 
- "aplicação": os dados na afirmação são uma propriedade no principal de serviço de aplicação (cliente). 
- "recursos": os dados na afirmação são uma propriedade no principal de serviço de recursos.
- "público-alvo": os dados na afirmação são uma propriedade no principal de serviço que é o público-alvo do token (cliente ou recurso principal de serviço).
- "empresa": os dados na afirmação são uma propriedade no objeto do inquilino de recursos da empresa.
- "transformação": os dados na afirmação são de transformação de afirmações (consulte a secção "Transformação de afirmações" neste artigo). 

Se a origem de transformação, a **TransformationID** elemento têm de ser incluído nesta definição de afirmação bem.

O elemento ID identifica cuja propriedade na origem fornece o valor para a afirmação. A tabela seguinte lista os valores de ID válido para cada valor de origem.

#### <a name="table-3-valid-id-values-per-source"></a>Tabela 3: Valores de ID válida por origem
|Origem|ID|Descrição|
|-----|-----|-----|
|Utilizador|Apelido|Nome de família|
|Utilizador|givenName|Nome fornecido|
|Utilizador|DisplayName|Nome a apresentar|
|Utilizador|ObjectId|ObjectID|
|Utilizador|capacidade de correio|Endereço de correio eletrónico|
|Utilizador|userPrincipalName|Nome Principal de utilizador|
|Utilizador|Departamento|Departamento|
|Utilizador|onpremisessamaccountname|No nome da conta de Sam local|
|Utilizador|netbiosname|Nome NetBios|
|Utilizador|dnsdomainname|Nome de domínio de DNS|
|Utilizador|onpremisesecurityidentifier|Identificador de segurança no local|
|Utilizador|companyname|Nome da Organização|
|Utilizador|streetAddress|Morada|
|Utilizador|postalcode|Código postal|
|Utilizador|preferredlanguange|Idioma preferencial|
|Utilizador|onpremisesuserprincipalname|UPN no local|
|Utilizador|mailNickname|Alcunha de correio|
|Utilizador|extensionattribute1|Atributo de extensão 1|
|Utilizador|extensionattribute2|Atributo de extensão 2|
|Utilizador|extensionattribute3|Atributo de extensão 3|
|Utilizador|extensionattribute4|Atributo de extensão 4|
|Utilizador|extensionattribute5|Atributo de extensão 5|
|Utilizador|extensionattribute6|Atributo de extensão 6|
|Utilizador|extensionattribute7|Atributo de extensão 7|
|Utilizador|extensionattribute8|Atributo de extensão 8|
|Utilizador|extensionattribute9|Atributo de extensão 9|
|Utilizador|extensionattribute10|Atributo de extensão 10|
|Utilizador|extensionattribute11|Atributo de extensão 11|
|Utilizador|extensionattribute12|Atributo de extensão 12|
|Utilizador|extensionattribute13|Atributo de extensão 13|
|Utilizador|extensionattribute14|Atributo de extensão 14|
|Utilizador|extensionattribute15|Atributo de extensão 15|
|Utilizador|othermail|Outro correio|
|Utilizador|País|País|
|Utilizador|city|Cidade|
|Utilizador|state|Estado|
|Utilizador|jobtitle|Cargo|
|Utilizador|Campo IDdeEmpregado|ID de empregado|
|Utilizador|facsimiletelephonenumber|Número de telefone facsimile|
|aplicação, recursos, público-alvo|DisplayName|Nome a apresentar|
|aplicação, recursos, público-alvo|objeto|ObjectID|
|aplicação, recursos, público-alvo|etiquetas|Etiqueta de Principal de serviço|
|Empresa|tenantcountry|País do inquilino|

**TransformationID:** tem de ser fornecido o elemento de TransformationID apenas se o elemento de origem está definido como "transformação".

- Este elemento tem de coincidir com o elemento de ID da entrada de transformação no **ClaimsTransformation** propriedade que define a forma como os dados para esta afirmação são gerados.

**Tipo de afirmação:** o **JwtClaimType** e **SamlClaimType** elementos definem de que a esta entrada de esquema de afirmação refere-se a afirmação.

- O JwtClaimType tem de conter o nome de afirmação para ser emitidos na JWTs.
- O SamlClaimType tem de conter o URI de afirmação para ser emitidos em SAML tokens.

>[!NOTE]
>Nomes e os URIs de afirmações no conjunto de afirmações restrito não podem ser utilizado para os elementos de tipo de afirmação. Para obter mais informações, consulte a secção "Exceções e restrições" neste artigo.

### <a name="claims-transformation"></a>Transformação de afirmações

**Cadeia:** ClaimsTransformation

**Tipo de dados:** blob JSON, com uma ou mais entradas de transformação 

**Resumo:** utilizar esta propriedade para aplicar as transformações comuns aos dados de origem, para gerar os dados de saída de afirmações especificados no esquema de afirmações.

**ID:** utilize o elemento de ID para fazer referência a esta entrada de transformação na entrada TransformationID afirmações esquema. Este valor tem de ser exclusivo para cada entrada de transformação dentro desta política.

**TransformationMethod:** o elemento de TransformationMethod identifica que operação é executada para gerar os dados para a afirmação.

Com base no método escolhido, é esperado um conjunto de entradas e saídas. Estes são definidos utilizando o **InputClaims**, **InputParameters** e **OutputClaims** elementos.

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>Tabela 4: Os métodos de transformação e esperadas entradas e saídas
|TransformationMethod|Entrada esperada|Resultado esperado|Descrição|
|-----|-----|-----|-----|
|Associar|string1, string2, separador|outputClaim|As associações de entrada cadeias utilizando um separador de sessão. Por exemplo: string1: "foo@bar.com", string2: "sandbox", o separador: "." resulta na outputClaim: "foo@bar.com.sandbox"|
|ExtractMailPrefix|capacidade de correio|outputClaim|Extrai a parte do local de um endereço de e-mail. Por exemplo: correio: "foo@bar.com" resulta na outputClaim: "foo". Não @ sessão estiver presente, em seguida, a cadeia de entrada orignal é devolvida como está.|

**InputClaims:** utilize um elemento de InputClaims para passar os dados a partir de uma entrada de esquema de afirmação para uma transformação. Tem dois atributos: **ClaimTypeReferenceId** e **TransformationClaimType**.

- **ClaimTypeReferenceId** está associado ao elemento de ID da entrada de esquema de afirmação para localizar a afirmação de entrada adequada. 
- **TransformationClaimType** é utilizado para dar um nome exclusivo para esta entrada. Este nome tem de corresponder a uma das entradas esperadas para o método de transformação.

**InputParameters:** utilize um elemento de InputParameters para transferir um valor constante a uma transformação. Tem dois atributos: **valor** e **ID**.

- **Valor** é o valor real constante a ser transmitido.
- **ID** é utilizado para dar um nome exclusivo para esta entrada. Este nome tem de corresponder a uma das entradas esperadas para o método de transformação.

**OutputClaims:** utilize um elemento de OutputClaims para armazenar os dados gerados por uma transformação e associá-la para uma entrada de esquema de afirmação. Tem dois atributos: **ClaimTypeReferenceId** e **TransformationClaimType**.

- **ClaimTypeReferenceId** está associado com o ID da entrada de esquema de afirmação para localizar a afirmação de saída adequado.
- **TransformationClaimType** é utilizado para dar um nome exclusivo para esta saída. Este nome tem de corresponder a um as saídas esperadas para o método de transformação.

### <a name="exceptions-and-restrictions"></a>Exceções e restrições

**SAML NameID e UPN:** os atributos a partir da qual origem os valores NameID e UPN e as transformações de afirmações que são permitidas são limitados.

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>Tabela 5: Atributos permitidos como origem de dados para SAML NameID
|Origem|ID|Descrição|
|-----|-----|-----|
|Utilizador|capacidade de correio|Endereço de correio eletrónico|
|Utilizador|userPrincipalName|Nome Principal de utilizador|
|Utilizador|onpremisessamaccountname|No nome da conta de Sam local|
|Utilizador|Campo IDdeEmpregado|ID de empregado|
|Utilizador|extensionattribute1|Atributo de extensão 1|
|Utilizador|extensionattribute2|Atributo de extensão 2|
|Utilizador|extensionattribute3|Atributo de extensão 3|
|Utilizador|extensionattribute4|Atributo de extensão 4|
|Utilizador|extensionattribute5|Atributo de extensão 5|
|Utilizador|extensionattribute6|Atributo de extensão 6|
|Utilizador|extensionattribute7|Atributo de extensão 7|
|Utilizador|extensionattribute8|Atributo de extensão 8|
|Utilizador|extensionattribute9|Atributo de extensão 9|
|Utilizador|extensionattribute10|Atributo de extensão 10|
|Utilizador|extensionattribute11|Atributo de extensão 11|
|Utilizador|extensionattribute12|Atributo de extensão 12|
|Utilizador|extensionattribute13|Atributo de extensão 13|
|Utilizador|extensionattribute14|Atributo de extensão 14|
|Utilizador|extensionattribute15|Atributo de extensão 15|

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>Tabela 6: Métodos de transformação permitidos para SAML NameID
|TransformationMethod|Restrições|
| ----- | ----- |
|ExtractMailPrefix|Nenhuma|
|Associar|O sufixo que está a ser associado tem de ser um domínio verificado do inquilino de recursos.|

### <a name="custom-signing-key"></a>Chave de assinatura de personalizado
Uma chave de assinatura personalizada deve ser atribuída para o objeto principal de serviço para uma política de mapeamento de afirmações entre em vigor. Todos os tokens emitidos foi afetados pela política são assinados com esta chave. Aplicações tem de ser configuradas para aceitar tokens assinados com esta chave. Isto garante a confirmação tokens foram modificados pelo criador das afirmações a política de mapeamento. Esta ação protege as aplicações de afirmações, as políticas criadas pelo atores maliciosos de mapeamento.

### <a name="cross-tenant-scenarios"></a>Cenários entre inquilinos
Afirmações de políticas de mapeamento não se aplicam a utilizadores convidados. Se um utilizador convidado tenta aceder a uma aplicação com uma política atribuída para o principal de serviço de mapeamento de afirmações, o token de predefinido é emitido (a política não tem efeito).

## <a name="claims-mapping-policy-assignment"></a>Mapeamento de atribuição de política de afirmações
Políticas de mapeamento de afirmações só podem ser atribuídas a objetos de principal de serviço.

### <a name="example-claims-mapping-policies"></a>Políticas de mapeamento de afirmações de exemplo

No Azure AD, muitos cenários são possíveis quando pode personalizar afirmações emitidas em tokens para os principais de serviço específicos. Nesta secção, iremos guiá-lo através de alguns cenários comuns que podem ajudar a grasp como utilizar as afirmações de tipo de política de mapeamento.

#### <a name="prerequisites"></a>Pré-requisitos
Nos exemplos a seguir, criar, atualizar, ligar e eliminar políticas de principais de serviço. Se não estiver familiarizado com o Azure AD, recomendamos que pode obter informações sobre como obter um inquilino do Azure AD antes de avançar com estes exemplos. 

Para começar a utilizar, efetue os seguintes passos:


1. Transferir a versão mais recente [versão de pré-visualização pública do módulo do Azure AD PowerShell](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.127).
2.  Execute o comando Connect para iniciar sessão sua conta de administrador do Azure AD. Executar este comando sempre que iniciar uma sessão de novo.
    
     ``` powershell
    Connect-AzureAD -Confirm
    
    ```
3.  Para ver todas as políticas que foram criadas na sua organização, execute o seguinte comando. Recomendamos que execute este comando após a maioria das operações nos seguintes cenários, para verificar que as políticas estão a ser criadas, conforme esperado.
   
    ``` powershell
        Get-AzureADPolicy
    
    ```
#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>Exemplo: Criar e atribuir uma política omitir as afirmações básicas a partir de tokens emitidos para um principal de serviço.
Neste exemplo, criar uma política que remove a conjunto de afirmações básica a partir de tokens emitidos para os principais de serviço ligado.


1. Crie uma política de mapeamento de afirmações. Esta política, principais de serviço ligado para específico, remove a conjunto a partir de tokens de afirmações básica.
    1. Para criar uma política, execute este comando: 
    
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims” -Type "ClaimsMappingPolicy"
    ```
    2. Para ver a nova política de e para obter a política ObjectId, execute o seguinte comando:
    
     ``` powershell
    Get-AzureADPolicy
    ```
2.  Atribua a política para o principal de serviço. Também terá de obter o ObjectId do seu serviço principal. 
    1.  Para ver os principais de serviço da sua organização, pode consultar o Microsoft Graph. Em alternativa, no Explorador do Azure AD Graph, inicie sessão na sua conta do Azure AD.
    2.  Quando tiver o ObjectId do seu serviço principal, execute o seguinte comando:  
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```
#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>Exemplo: Criar e atribuir uma política para incluir o campo IDdeEmpregado e TenantCountry como afirmações no tokens emitidos para um principal de serviço.
Neste exemplo, criar uma política que adiciona o campo IDdeEmpregado e TenantCountry para tokens emitidos para os principais de serviço ligado. O campo IDdeEmpregado é emitido como o tipo de afirmação de nome em SAML tokens e JWTs. O TenantCountry é emitido como o tipo de afirmação de país em SAML tokens e JWTs. Neste exemplo, vamos continuar a incluir as afirmações básicas definidas em tokens.

1. Crie uma política de mapeamento de afirmações. Esta política, ligada principais de serviço específicos, adiciona os campo IDdeEmpregado e TenantCountry afirmações para tokens.
    1. Para criar uma política, execute este comando:  
     
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name","JwtClaimType":"name"},{"Source":"company","ID":" tenantcountry ","SamlClaimType":" http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country ","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample” -Type "ClaimsMappingPolicy"
    ```
    
    2. Para ver a nova política de e para obter a política ObjectId, execute o seguinte comando:
     
     ``` powershell  
    Get-AzureADPolicy
    ```
2.  Atribua a política para o principal de serviço. Também terá de obter o ObjectId do seu serviço principal. 
    1.  Para ver os principais de serviço da sua organização, pode consultar o Microsoft Graph. Em alternativa, no Explorador do Azure AD Graph, inicie sessão na sua conta do Azure AD.
    2.  Quando tiver o ObjectId do seu serviço principal, execute o seguinte comando:  
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```
#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>Exemplo: Criar e atribuir uma política que utiliza uma transformação de afirmações em tokens emitidos para um principal de serviço.
Neste exemplo, criar uma política que emite uma afirmação personalizada "JoinedData" ao JWTs emitido para principais de serviço ligado. Esta afirmação contém um valor criado ao associar os dados armazenados no atributo extensionattribute1 no objeto user com ".sandbox". Neste exemplo, vamos excluir as afirmações básicas definidas em tokens.


1. Crie uma política de mapeamento de afirmações. Esta política, ligada principais de serviço específicos, adiciona os campo IDdeEmpregado e TenantCountry afirmações para tokens.
    1. Para criar uma política, execute este comando: 
     
     ``` powershell
    New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformation":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"Id":"string2","Value":"sandbox"},{"Id":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample” -Type "ClaimsMappingPolicy"
    ```
    
    2. Para ver a nova política de e para obter a política ObjectId, execute o seguinte comando: 
     
     ``` powershell
    Get-AzureADPolicy
    ```
2.  Atribua a política para o principal de serviço. Também terá de obter o ObjectId do seu serviço principal. 
    1.  Para ver os principais de serviço da sua organização, pode consultar o Microsoft Graph. Em alternativa, no Explorador do Azure AD Graph, inicie sessão na sua conta do Azure AD.
    2.  Quando tiver o ObjectId do seu serviço principal, execute o seguinte comando: 
     
     ``` powershell
    Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
    ```
