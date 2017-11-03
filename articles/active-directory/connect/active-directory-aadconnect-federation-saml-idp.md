---
title: "Azure AD Connect: Utilizar um fornecedor de identidade 2.0 para início de sessão único em | Microsoft Docs"
description: "Este tópico descreve a utilizar um Idp compatível de SAML 2.0 para início de sessão único no."
services: active-directory
author: billmath
manager: femila
ms.custom: it-pro
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/13/2017
ms.author: billmath
ms.openlocfilehash: 048697f87383662506fb851bb3ea510c2cddf043
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
#  <a name="use-a-saml-20-identity-provider-idp-for-single-sign-on"></a>Utilize um fornecedor de identidade 2.0 (IdP) para início de sessão único na

Este tópico contém informações sobre como utilizar um SAML 2.0 perfil SP Lite em conformidade com base em fornecedor de identidade como preferencial segurança Token serviço (STS) / fornecedor de identidade. Isto é útil em que já tem um diretório de utilizador e palavra-passe armazenar no local que pode ser acedido utilizando SAML 2.0. Este diretório de utilizador existente, pode ser utilizado para início de sessão no Office 365 e outros recursos protegidos pelo AD do Azure. O perfil SP de 2.0 de SAML-Lite baseia-se na norma de identidade federada de Security Assertion Markup Language (SAML) amplamente utilizado para fornecer uma arquitetura de início de sessão e atributo exchange.

>[!NOTE]
>Para obter uma lista de terceiros 3rd Idps que testou para utilização com o Azure AD, consulte o [lista de compatibilidades de Federação do AD do Azure](active-directory-aadconnect-federation-compatibility.md)

Com o seu SAML 2.0 está corretamente configurada perfil baseado em IdP, a Microsoft suporta esta experiência de início de sessão como a integração de um serviço em nuvem Microsoft, como o Office 365. Fornecedores de identidade de SAML 2.0 são produtos de terceiros e, por conseguinte, a Microsoft não fornece suporte para a implementação, a configuração, as melhores práticas sobre-los de resolução de problemas. Uma vez configurados corretamente, a integração com o fornecedor de identidade pode ser testada para configuração adequada, utilizando a ferramenta Analisador de conectividade de Microsoft que é descrita detalhadamente mais abaixo do SAML 2.0. Para obter mais informações sobre o fornecedor de identidade baseada em perfil SP de 2.0 de SAML-Lite, peça a organização que fornecido-lo.

>[!IMPORTANT]
>Só um conjunto limitado de clientes está disponível neste cenário de início de sessão com fornecedores de identidade de SAML 2.0, isto inclui:

>- Clientes baseados na Web, tais como o Outlook Web Access e ao SharePoint Online
- Os clientes de correio eletrónico avançada que utilizam autenticação básica e um método de acesso do Exchange suportado, tais como IMAP, POP, sincronização Active Directory, MAPI, etc. (o ponto final de protocolo de cliente avançado é necessário, implementado), incluindo:
    - Outlook 2013/Microsoft Outlook 2010/Outlook 2016, Apple iPhone (várias versões de iOS)
    - Vários dispositivos Android da Google
    - Windows Phone 7, Windows Phone 7.8 e Windows Phone 8.0
    - Cliente de correio do Windows 8 e Windows 8.1 cliente de correio
    - Cliente de correio do Windows 10

Todos os outros clientes não estão disponíveis neste cenário de início de sessão com o seu fornecedor de identidade SAML 2.0. Por exemplo, o cliente de ambiente de trabalho do Lync 2010 não é possível iniciar sessão no serviço com o seu fornecedor de identidade SAML 2.0 configurado para o início de sessão único.

## <a name="azure-ad-saml-20-protocol-requirements"></a>Requisitos de protocolo do Azure AD SAML 2.0
Este tópico contém os requisitos detalhados na mensagem de formatação que o fornecedor de identidade de SAML 2.0 tem de implementar a federar com o Azure AD para ativar o início de sessão para um ou mais cloud services da Microsoft (tais como o Office 365) e protocolo. A SAML 2.0 entidade confiadora (SP STS) para um serviço de nuvem da Microsoft utilizado neste cenário é do Azure AD.

É recomendado que certifique-se o fornecedor de identidade de SAML 2.0 mensagens de saída ser tão semelhante aos rastreios de exemplo fornecidos quanto possível. Além disso, utilize os valores de atributo específico do fornecido metadados do Azure AD sempre que possível. Quando estiver satisfeito com as mensagens de saída, pode testar com o analisador de conectividade da Microsoft, conforme descrito abaixo.

Os metadados do Azure AD podem ser transferido a partir deste URL: [https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml](http://https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml).
Para os clientes na China utilizando a instância de China específicos do Office 365, o seguinte ponto final da Federação deve ser utilizado: [https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml](https://nexus.partner.microsoftonline-p.cn/federationmetadata/saml20/federationmetadata.xml).

## <a name="saml-protocol-requirements"></a>Requisitos do protocolo SAML
Esta secção detalhes como os pares de mensagem de pedido e resposta são colocados em conjunto em ordem para ajudar a formatar as mensagens corretamente.

Azure AD pode ser configurado para trabalhar com fornecedores de identidade que utilizam o perfil de SAML 2.0 SP Lite com alguns requisitos específicos, conforme listado abaixo. Utilizar as mensagens de pedido e resposta SAML de exemplo, juntamente com automatizadas e manuais de teste, pode trabalhar para alcançar a interoperabilidade com o Azure AD.

## <a name="signature-block-requirements"></a>Requisitos do bloco de assinatura
Dentro da mensagem de resposta de SAML o nó de assinatura contém informações sobre a assinatura digital para a própria mensagem. O bloco de assinaturas tem os seguintes requisitos:

1. O próprio nó asserção tem de ser assinado
2.  O algoritmo RSA sha1 tem de ser utilizado como o DigestMethod. Outros algoritmos de assinatura digital não são aceites.
   `<ds:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1"/>`
3.  Também pode iniciar o documento XML. 
4.  O algoritmo de transformação tem de corresponder aos valores no seguinte exemplo:`<ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature"/>
       <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#"/>`
9.  O algoritmo de ajusta ao SignatureMethod tem de corresponder ao seguinte exemplo:`<ds:SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1"/>`

## <a name="supported-bindings"></a>Enlaces suportados
Enlaces de transporte de estão relacionados com os parâmetros de comunicações que são necessários. Os requisitos seguintes aplicam-se como nos enlaces

1. HTTPS é necessário transporte.
2.  Azure AD irá requerer HTTP POST para submissão token durante o início de sessão
3.  Azure AD irá utilizar o HTTP POST para o pedido de autenticação para o fornecedor de identidade e o REDIRECIONAMENTO para a mensagem de fim de sessão para o fornecedor de identidade.

## <a name="required-attributes"></a>Atributos necessários
Esta tabela mostra os requisitos de atributos específicos na mensagem de SAML 2.0.
 
|Atributo|Descrição|
| ----- | ----- |
|NameID|O valor desta asserção tem de ser o mesmo que ImmutableID o utilizador do Azure AD. Pode ser até 64 carateres alfa numérico. Tem de estar codificados caracteres HTML não seguros, por exemplo um caráter "+" é apresentado como ".2B".|
|IDPEmail|O nome Principal de utilizador (UPN) está listado na resposta SAML como um elemento com o nome IDPEmail este é UserPrincipalName (UPN o utilizador) no Azure AD/Office 365. O UPN está no formato de endereço de correio eletrónico. Valor UPN no Windows Office 365 (Azure Active Directory).|
|emissor|Isto é necessário ser um URI de fornecedor de identidade. Não deve reutilizar o emissor de mensagens de exemplo. Se tiver vários domínios de nível superior na sua inquilinos do Azure AD o emissor tem de corresponder a definição de URI especificada configurada por domínio.|

>[!IMPORTANT]
>Azure AD atualmente suporta o seguinte formato de URI do NameID para SAML 2.0:urn:oasis:names:tc:SAML:2.0:nameid-formato: persistente.

## <a name="sample-saml-request-and-response-messages"></a>Mensagens de pedido e resposta SAML do exemplo
É apresentado um par de mensagem de pedido e resposta para a troca de mensagem de início de sessão.
Esta é uma mensagem de pedido de exemplo que é enviada do Azure AD para um fornecedor de identidade de SAML 2.0 de exemplo. O fornecedor de identidade de SAML 2.0 de exemplo está configurado para utilizar o protocolo SAML-P nos serviços de Federação do Active Directory (AD FS). Testar a interoperabilidade também foi concluída com outros fornecedores de identidade de SAML 2.0.

    `<samlp:AuthnRequest xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion" ID="_7171b0b2-19f2-4ba2-8f94-24b5e56b7f1e" IssueInstant="2014-01-30T16:18:35Z" Version="2.0" AssertionConsumerServiceIndex="0" >
    <saml:Issuer>urn:federation:MicrosoftOnline</saml:Issuer>
    <samlp:NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
    </samlp:AuthnRequest>`

Esta é uma mensagem de resposta de exemplo que é enviada do fornecedor de identidade em conformidade de SAML 2.0 de exemplo para o Azure AD / Office 365.

    `<samlp:Response ID="_592c022f-e85e-4d23-b55b-9141c95cd2a5" Version="2.0" IssueInstant="2014-01-31T15:36:31.357Z" Destination="https://login.microsoftonline.com/login.srf" Consent="urn:oasis:names:tc:SAML:2.0:consent:unspecified" InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
    </samlp:Status>
    <Assertion ID="_7e3c1bcd-f180-4f78-83e1-7680920793aa" IssueInstant="2014-01-31T15:36:31.279Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>http://WS2012R2-0.contoso.com/adfs/services/trust</Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      <ds:SignedInfo>
        <ds:CanonicalizationMethod Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
        <ds:SignatureMethod Algorithm="http://www.w3.org/2000/09/xmldsig#rsa-sha1" />
        <ds:Reference URI="#_7e3c1bcd-f180-4f78-83e1-7680920793aa">
          <ds:Transforms>
            <ds:Transform Algorithm="http://www.w3.org/2000/09/xmldsig#enveloped-signature" />
            <ds:Transform Algorithm="http://www.w3.org/2001/10/xml-exc-c14n#" />
          </ds:Transforms>
          <ds:DigestMethod Algorithm="http://www.w3.org/2000/09/xmldsig#sha1" />
          <ds:DigestValue>CBn/5YqbheaJP425c0pHva9PhNY=</ds:DigestValue>
        </ds:Reference>
      </ds:SignedInfo>
      <ds:SignatureValue>TciWMyHW2ZODrh/2xrvp5ggmcHBFEd9vrp6DYXp+hZWJzmXMmzwmwS8KNRJKy8H7XqBsdELA1Msqi8I3TmWdnoIRfM/ZAyUppo8suMu6Zw+boE32hoQRnX9EWN/f0vH6zA/YKTzrjca6JQ8gAV1ErwvRWDpyMcwdYCiWALv9ScbkAcebOE1s1JctZ5RBXggdZWrYi72X+I4i6WgyZcIGai/rZ4v2otoWAEHS0y1yh1qT7NDPpl/McDaTGkNU6C+8VfjD78DrUXEcAfKvPgKlKrOMZnD1lCGsViimGY+LSuIdY45MLmyaa5UT4KWph6dA==</ds:SignatureValue>
      <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#">
        <ds:X509Data>
          <ds:X509Certificate>MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyhBREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDTE1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9ybWVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/+3ZWxd9T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEMb2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcCAwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvyJOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBySx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==</ds:X509Certificate>
        </ds:X509Data>
      </KeyInfo>
    </ds:Signature>
    <Subject>
      <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent">ABCDEG1234567890</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="_049917a6-1183-42fd-a190-1d2cbaf9b144" NotOnOrAfter="2014-01-31T15:41:31.357Z" Recipient="https://login.microsoftonline.com/login.srf" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2014-01-31T15:36:31.263Z" NotOnOrAfter="2014-01-31T16:36:31.263Z">
      <AudienceRestriction>
        <Audience>urn:federation:MicrosoftOnline</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="IDPEmail">
        <AttributeValue>administrator@contoso.com</AttributeValue>
      </Attribute>
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2014-01-31T15:36:30.200Z" SessionIndex="_7e3c1bcd-f180-4f78-83e1-7680920793aa">
      <AuthnContext>
        <AuthnContextClassRef>urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
    </Assertion>
    </samlp:Response>`

## <a name="configure-your-saml-20-compliant-identity-provider"></a>Configurar o seu fornecedor de identidade em conformidade SAML 2.0
Este tópico contém orientações sobre como configurar o seu fornecedor de identidade de SAML 2.0 para federar com o Azure AD para ativar o acesso de início de sessão único para um ou mais serviços cloud Microsoft (tais como o Office 365) utilizando o protocolo SAML 2.0. A entidade confiadora um serviço em nuvem da Microsoft utilizados neste cenário do SAML 2.0 é do Azure AD.

## <a name="add-azure-ad-metadata"></a>Adicionar metadados do Azure AD
O fornecedor de identidade de SAML 2.0 tem de cumprir para obter informações sobre o Azure AD entidade confiadora. Azure AD publica os metadados em https://nexus.microsoftonline-p.com/federationmetadata/saml20/federationmetadata.xml.

Recomenda-se que sempre importar os metadados mais recente do Azure AD quando configurar o seu fornecedor de identidade de SAML 2.0. Tenha em atenção que o Azure AD não ler os metadados do fornecedor de identidade.

## <a name="add-azure-ad-as-a-relying-party"></a>Adicionar do Azure AD como uma entidade confiadora
Tem de ativar a comunicação entre o fornecedor de identidade de SAML 2.0 e o Azure AD. Esta configuração irá seja dependente do seu fornecedor de identidade específico e deve consultar a documentação para o mesmo. Normalmente, seria definido o ID de terceiros entidade confiadora para a mesma que a entityID dos metadados do Azure AD.

>[!NOTE]
>Certifique-se de que o relógio no seu servidor de fornecedor de identidade de SAML 2.0 está sincronizado com uma origem de hora exata. Um período de tempo do relógio incorreta pode fazer com que os inícios de sessão federados falhar.

## <a name="install-windows-powershell-for-sign-on-with-saml-20-identity-provider"></a>Instale o Windows PowerShell para início de sessão com o fornecedor de identidade de SAML 2.0
Depois de ter configurado o fornecedor de identidade de SAML 2.0 para utilização com o início de sessão no Azure AD, o passo seguinte é transferir e instalar o módulo Azure Active Directory para Windows PowerShell. Depois de instalado, irá utilizar estes cmdlets para configurar os domínios do Azure AD como domínios federados.

O módulo Azure Active Directory para Windows PowerShell é uma transferência para gerir os dados das organizações no Azure AD. Este módulo instala um conjunto de cmdlets do Windows PowerShell; execute estes cmdlets para configurar o acesso de início de sessão único para o Azure AD e, por sua vez em todos os serviços em nuvem tem subscritos. Para obter instruções sobre como transferir e instalar os cmdlets, consulte [http://technet.microsoft.com/library/jj151815.aspx](http://technet.microsoft.com/library/jj151815.aspx)

## <a name="set-up-a-trust-between-your-saml-identity-provider-and-azure-ad"></a>Configurar uma confiança entre o fornecedor de identidade e o Azure AD
Antes de configurar a Federação num domínio do Azure AD, tem de ter um domínio personalizado configurado. Não é possível federar o domínio predefinido que é fornecido pela Microsoft. O domínio predefinido da Microsoft termina com "onmicrosoft.com".
Irá executar uma série de cmdlets na interface de linha de comandos do Windows PowerShell para adicionar ou converter domínios para o início de sessão único.

Cada domínio do Azure Active Directory que pretende federar com o fornecedor de identidade de SAML 2.0 tem de ser adicionado como um domínio de início de sessão único ou convertido para ser um único início de sessão no domínio de um domínio padrão. Adicionar ou converter um domínio configura uma confiança entre o seu fornecedor de identidade de SAML 2.0 e o Azure AD.

O procedimento seguinte orienta-o através de conversão de um domínio padrão existente para um domínio Federado com SP de 2.0 de SAML-Lite. Tenha em atenção que o domínio pode ocorrer uma falha que afeta os utilizadores a cópia de segurança 2 horas depois de efetuar este passo.

## <a name="configuring-a-domain-in-your-azure-ad-directory-for-federation"></a>Configurar um domínio no seu diretório do Azure AD para a Federação


1. Ligar ao seu diretório do Azure AD como um administrador de inquilino: MsolService ligar.
2.  Configure o seu domínio do Office 365 pretendido para utilizar a Federação com SAML 2.0:`$dom = "contoso.com" $BrandName - "Sample SAML 2.0 IDP" $LogOnUrl = "https://WS2012R2-0.contoso.com/passiveLogon" $LogOffUrl = "https://WS2012R2-0.contoso.com/passiveLogOff" $ecpUrl = "https://WS2012R2-0.contoso.com/PAOS" $MyURI = "urn:uri:MySamlp2IDP" $MySigningCert = @" MIIC7jCCAdagAwIBAgIQRrjsbFPaXIlOG3GTv50fkjANBgkqhkiG9w0BAQsFADAzMTEwLwYDVQQDEyh BREZTIFNpZ25pbmcgLSBXUzIwMTJSMi0wLnN3aW5mb3JtZXIuY29tMB4XDTE0MDEyMDE1MTY0MFoXDT E1MDEyMDE1MTY0MFowMzExMC8GA1UEAxMoQURGUyBTaWduaW5nIC0gV1MyMDEyUjItMC5zd2luZm9yb WVyLmNvbTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAKe+rLVmXy1QwCwZwqgbbp1/kupQ VcjKuKLitVDbssFyqbDTjP7WRjlVMWAHBI3kgNT7oE362Gf2WMJFf1b0HcrsgLin7daRXpq4Qi6OA57 sW1YFMj3sqyuTP0eZV3S4+ZbDVob6amsZIdIwxaLP9Zfywg2bLsGnVldB0+XKedZwDbCLCVg+3ZWxd9 T/jV0hpLIIWr+LCOHqq8n8beJvlivgLmDJo8f+EITnAxWcsJUvVai/35AhHCUq9tc9sqMp5PWtabAEM b2AU72/QlX/72D2/NbGQq1BWYbqUpgpCZ2nSgvlWDHlCiUo//UGsvfox01kjTFlmqQInsJVfRxF5AcC AwEAATANBgkqhkiG9w0BAQsFAAOCAQEAi8c6C4zaTEc7aQiUgvnGQgCbMZbhUXXLGRpjvFLKaQzkwa9 eq7WLJibcSNyGXBa/SfT5wJgsm3TPKgSehGAOTirhcqHheZyvBObAScY7GOT+u9pVYp6raFrc7ez3c+ CGHeV/tNvy1hJNs12FYH4X+ZCNFIT9tprieR25NCdi5SWUbPZL0tVzJsHc1y92b2M2FxqRDohxQgJvy JOpcg2mSBzZZIkvDg7gfPSUXHVS1MQs0RHSbwq/XdQocUUhl9/e/YWCbNNxlM84BxFsBUok1dH/gzBy Sx+Fc8zYi7cOq9yaBT3RLT6cGmFGVYZJW4FyhPZOCLVNsLlnPQcX3dDg9A==" "@ $uri = "http://WS2012R2-0.contoso.com/adfs/services/trust" $Protocol = "SAMLP" Set-MsolDomainAuthentication -DomainName $dom -FederationBrandName $dom -Authentication Federated -PassiveLogOnUri $MyURI -ActiveLogOnUri $ecpUrl -SigningCertificate $MySigningCert -IssuerUri $uri -LogOffUri $url -PreferredAuthenticationProtocol $Protocol` 

3.  Pode obter a cadeia de codificação base64 assinatura do certificado no seu ficheiro de metadados do IDP. Um exemplo de nesta localização foi fornecido, mas pode diferir ligeiramente com base na sua implementação.

    `<IDPSSODescriptor protocolSupportEnumeration="urn:oasis:names:tc:SAML:2.0:protocol"> <KeyDescriptor use="signing"> <KeyInfo xmlns="http://www.w3.org/2000/09/xmldsig#"> <X509Data> <X509Certificate>MIIC5jCCAc6gAwIBAgIQLnaxUPzay6ZJsC8HVv/QfTANBgkqhkiG9w0BAQsFADAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwHhcNMTMxMTA0MTgxMzMyWhcNMTQxMTA0MTgxMzMyWjAvMS0wKwYDVQQDEyRBREZTIFNpZ25pbmcgLSBmcy50ZWNobGFiY2VudHJhbC5vcmcwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQCwMdVLTr5YTSRp+ccbSpuuFeXMfABD9mVCi2wtkRwC30TIyPdORz642MkurdxdPCWjwgJ0HW6TvXwcO9afH3OC5V//wEGDoNcI8PV4enCzTYFe/h//w51uqyv48Fbb3lEXs+aVl8155OAj2sO9IX64OJWKey82GQWK3g7LfhWWpp17j5bKpSd9DBH5pvrV+Q1ESU3mx71TEOvikHGCZYitEPywNeVMLRKrevdWI3FAhFjcCSO6nWDiMqCqiTDYOURXIcHVYTSof1YotkJ4tG6mP5Kpjzd4VQvnR7Pjb47nhIYG6iZ3mR1F85Ns9+hBWukQWNN2hcD/uGdPXhpdMVpBAgMBAAEwDQYJKoZIhvcNAQELBQADggEBAK7h7jF7wPzhZ1dPl4e+XMAr8I7TNbhgEU3+oxKyW/IioQbvZVw1mYVCbGq9Rsw4KE06eSMybqHln3w5EeBbLS0MEkApqHY+p68iRpguqa+W7UHKXXQVgPMCpqxMFKonX6VlSQOR64FgpBme2uG+LJ8reTgypEKspQIN0WvtPWmiq4zAwBp08hAacgv868c0MM4WbOYU0rzMIR6Q+ceGVRImlCwZ5b7XKp4mJZ9hlaRjeuyVrDuzBkzROSurX1OXoci08yJvhbtiBJLf3uPOJHrhjKRwIt2TnzS9ElgFZlJiDIA26Athe73n43CT0af2IG6yC7e6sK4L3NEXJrwwUZk=</X509Certificate> </X509Data> </KeyInfo> </KeyDescriptor>` 

Para mais informações sobre "Set-MsolDomainAuthentication", consulte: [http://technet.microsoft.com/library/dn194112.aspx](http://technet.microsoft.com/library/dn194112.aspx).

>[!NOTE]
>Tem de executar a utilizar "$ecpUrl ="https://WS2012R2-0.contoso.com/PAOS"" apenas se configurar uma extensão ECP para o fornecedor de identidade. Dependem de clientes do Exchange Online, excluindo o Outlook Web aplicação (OWA), um pedido POST baseada em ponto final de Active Directory. Se o seu SAML 2.0 STS implementa um ponto final ativo semelhante à implementação de ECP do Shibboleth de um ponto final ativo poderá ser possível para estes clientes avançados interagir com o serviço de Exchange Online.

Assim que tiver sido configurada a Federação pode voltar a mudar para "não federada" (ou "gerido"), no entanto, esta alteração executa até duas horas a concluir e necessita de atribuir novas palavras-passe aleatórias para o início de sessão baseado na nuvem a cada utilizador. Mudar para "gerido" poderá ser necessário em alguns cenários para repor um erro nas definições. Para mais informações sobre a conversão de domínio, consulte: [http://msdn.microsoft.com/library/windowsazure/dn194122.aspx](http://msdn.microsoft.com/library/windowsazure/dn194122.aspx).

## <a name="provision-user-principals-to-azure-ad--office-365"></a>Aprovisionar principais de utilizador para o Azure AD / Office 365
Antes de pode autenticar os utilizadores do Office 365, terá de aprovisionar do Azure AD com os principais de utilizador que correspondem a asserção na afirmação SAML 2.0. Se destes principais de utilizador não são conhecidos para o Azure AD com antecedência, em seguida, estes não podem ser utilizados para o início de sessão federado. O Azure AD Connect ou o Windows PowerShell pode ser utilizado para aprovisionar principais de utilizador.

O Azure AD Connect pode ser utilizado para aprovisionar principais para os domínios no seu diretório do Azure AD do Active Directory no local. Para obter mais informações, consulte [integrar os diretórios no local ao Azure Active Directory](active-directory-aadconnect.md).

Também pode ser utilizado o Windows PowerShell para automatizar a adição de novos utilizadores ao Azure AD e sincronizar as alterações do diretório no local. Para utilizar os cmdlets do Windows PowerShell tem de transferir o [do Azure Active Directory módulos](https://docs.microsoft.com/powershell/azure/install-adv2?view=azureadps-2.0).

Este procedimento mostra como adicionar um único utilizador para o Azure AD.


1. Ligar ao seu diretório do Azure AD como um administrador de inquilino: MsolService ligar.
2.  Crie um novo principal de utilizador:` New-MsolUser
        -UserPrincipalName elwoodf1@contoso.com
        -ImmutableId ABCDEFG1234567890
        -DisplayName "Elwood Folk"
        -FirstName Elwood 
        -LastName Folk 
        -AlternateEmailAddresses "Elwood.Folk@contoso.com" 
        -LicenseAssignment "samlp2test:ENTERPRISEPACK" 
        -UsageLocation "US" ` 

Para obter mais informações sobre a modificação do "MsolUser novo", [http://technet.microsoft.com/library/dn194096.aspx](http://technet.microsoft.com/library/dn194096.aspx)

>[!NOTE]
>O valor de "UserPrinciplName" tem de corresponder ao valor que irá enviar para "IDPEmail" no seu afirmações SAML 2.0 e o valor de "ImmutableID" tem de corresponder ao valor enviado no seu asserção "NameID".

## <a name="verify-single-sign-on-with-your-saml-20-idp"></a>Certifique-se início de sessão único com o IDP de 2.0 SAML
Como administrador, antes de a verifica e gerir início de sessão (também chamado de identidade Federação), reveja as informações e execute os passos nos seguintes artigos para configurar o início de sessão com o seu fornecedor de identidade baseada em SP de 2.0 de SAML-Lite:


1.  Rever os requisitos de protocolo do Azure AD SAML 2.0
2.  Configurou o seu fornecedor de identidade de SAML 2.0
3.  Instale o Windows PowerShell para o início de sessão único com o fornecedor de identidade de SAML 2.0
4.  Configurar uma confiança entre o fornecedor de identidade de SAML 2.0 e o Azure AD
5.  Aprovisionado um principal de utilizador de teste conhecidos para o Azure Active Directory (Office 365) através do Windows PowerShell ou do Azure AD Connect.
6.  Configurar a utilização de sincronização de diretórios [do Azure AD Connect](active-directory-aadconnect.md).

Depois de configurar início de sessão único com o fornecedor de identidade SAML 2.0 SP-Lite com base, deverá certificar-se de que está a funcionar corretamente.

>[!NOTE]
>Se converter um domínio, em vez de adicionar um, pode demorar até 24 horas para configurar o início de sessão único.
Antes de verificar o início de sessão único, deve concluir a configuração da sincronização do Active Directory, sincronizar os diretórios e ativar os utilizadores sincronizados.

### <a name="use-the-tool-to-verify-that-single-sign-on-has-been-set-up-correctly"></a>Utilize a ferramenta para verificar que esse início de sessão está configurada corretamente
Para verificar que esse início de sessão está configurada corretamente, pode executar o procedimento seguinte para confirmar que é possível iniciar sessão serviço em nuvem com as suas credenciais de empresa.

Microsoft forneceu uma ferramenta que pode utilizar para testar o seu fornecedor de identidade baseada em SAML 2.0. Antes de executar a ferramenta de teste tem de ter configurado um inquilino do Azure AD a federar com o seu fornecedor de identidade.

>[!NOTE]
>O analisador de conectividade requer o Internet Explorer 10 ou posterior.



1. Transferir o analisador de conectividade, [https://testconnectivity.microsoft.com/?tabid=Client](https://testconnectivity.microsoft.com/?tabid=Client).
2.  Clique em instalar agora para começar a transferir e instalar a ferramenta.
3.  Selecione "Posso não é possível configurar a Federação com o Office 365, Azure ou outros serviços que utilizam o Azure Active Directory".
4.  Depois da ferramenta é transferida e em execução, verá a janela de diagnóstico de conectividade. A ferramenta que irá seguir teste a ligação de Federação.
5.  O analisador de conectividade irá abrir o IDP de 2.0 SAML para início de sessão, introduza as credenciais para o principal de utilizador que está a testar: ![SAML](media/active-directory-aadconnect-federation-saml-idp/saml1.png)
6.  No teste de início de sessão janela Federação deve introduzir um nome de conta e palavra-passe para o inquilino do Azure AD que está configurado para ser Federado com o seu fornecedor de identidade de SAML 2.0. A ferramenta de tentativa de início de sessão utilizando as credenciais e resultados detalhados do testes realizados durante a tentativa de início de sessão serão fornecidos como saída.
![SAML](media/active-directory-aadconnect-federation-saml-idp/saml2.png)
7. Esta janela mostra um resultado de falha de teste. Clicar na revisão dos resultados em pormenor irão mostrar informações sobre os resultados de cada teste que foi efetuada. Também pode guardar os resultados no disco para partilhá-los.
 
>[!NOTE]
>O analisador de conectividade também os testes de Federação Active Directory utilizando o WS *-com base e ECP/PAOS protocolos. Se não estiver a utilizar estas pode ignorar o erro seguinte: testar o Active Directory início de sessão fluxo utilizando o ponto final de Federação do Active Directory do seu fornecedor de identidade.

### <a name="manually-verify-that-single-sign-on-has-been-set-up-correctly"></a>Verificar manualmente que esse início de sessão está configurada corretamente
Verificação manual fornece passos adicionais que pode tomar para se certificar de que o seu fornecedor de identidade de SAML 2.0 está a funcionar corretamente em vários cenários.
Para verificar que esse início de sessão está configurada corretamente, conclua os seguintes passos:


1. Num computador associado a um domínio, inicie sessão no seu serviço de nuvem utilizando o mesmo nome de início de sessão que utiliza para credenciais da sua empresa.
2.  Clique dentro da caixa de palavra-passe. Se o início de sessão único está configurada, a caixa de palavra-passe será sombreada, e verá a seguinte mensagem: "são agora necessárias para iniciar sessão em <your company>."
3.  Clique em início de sessão em <your company> ligação. Se conseguir iniciar sessão, em seguida, o início de sessão único tiver sido configurado.

## <a name="next-steps"></a>Passos Seguintes


- [Gestão de serviços de Federação do Active Directory e personalização com o Azure AD Connect](active-directory-aadconnect-federation-management.md)
- [Lista de compatibilidades de federação do Azure AD](active-directory-aadconnect-federation-compatibility.md)
- [Instalação personalizada do Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
