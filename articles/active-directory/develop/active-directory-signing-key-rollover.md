---
title: O Rollover da chave de assinatura no Azure AD | Microsoft Docs
description: "Este artigo aborda as assinatura rollover de chave melhores práticas para o Azure Active Directory"
services: active-directory
documentationcenter: .net
author: dstrockis
manager: krassk
editor: 
ms.assetid: ed964056-0723-42fe-bb69-e57323b9407f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2016
ms.author: dastrock
ms.custom: aaddev
ms.openlocfilehash: 228bb9058537af1e4eb38207c376c2eb86aee68c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="signing-key-rollover-in-azure-active-directory"></a>Iniciar o rollover da chave no Azure Active Directory
Este tópico descreve o que precisa de saber sobre as chaves públicas que são utilizadas para assinar os tokens de segurança no Azure Active Directory (Azure AD). É importante ter em atenção que estas rollover de chaves periodicamente e, numa emergência, pode ser feito imediatamente. Todas as aplicações que utilizam o Azure AD devem ser capazes de lidar com o processo de rollover de chave ou estabelecer um processo manual de rollover periódica de através de programação. Continuar a ler para compreender como funcionam as chaves, como a avaliar o impacto de rollover à sua aplicação e como atualizar a sua aplicação ou estabelecer um processo manual de rollover periódica para processar o rollover da chave, se necessário.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Descrição geral das chaves de assinatura no Azure AD
Do Azure AD utiliza a criptografia de chave pública incorporada nas normas da indústria para estabelecer fidedignidade entre si próprio e as aplicações que a utilizam. Em termos práticos, isto funciona da seguinte forma: AD do Azure utiliza uma chave de assinatura que é composta por um par de chaves público e privado. Quando um utilizador inicia sessão a uma aplicação que utiliza o Azure AD para autenticação, o Azure AD cria um token de segurança que contém informações sobre o utilizador. Este token é assinada pelo Azure AD utilizando a respetiva chave privada antes de ser enviada para a aplicação. Para verificar que o token é válido e, na verdade, teve origem do Azure AD, a aplicação tem de validar a assinatura do token utilizando a chave pública exposta pelo Azure AD que está contido no inquilino do [OpenID Connect documento de identificação](http://openid.net/specs/openid-connect-discovery-1_0.html) ou SAML/WS-Fed [documento de metadados de Federação](active-directory-federation-metadata.md).

Por motivos de segurança, do Azure AD assinatura faz chave periodicamente e, em caso de emergência, pode ser feito imediatamente. Qualquer aplicação que se integra com o Azure AD deve estar preparada para processar um evento de rollover de chave, independentemente da frequência podem ocorrer. Se não, e a aplicação tenta utilizar uma chave expirada para verificar a assinatura de um token, o pedido de início de sessão irá falhar.

Sempre há mais do que uma chave válida no documento de identificação OpenID Connect e o documento de metadados de Federação. A aplicação deve estar preparado para utilizar qualquer uma das chaves especificadas no documento, uma vez que uma chave poderá ser revertida em breve, outro pode ser sua substituição e assim sucessivamente.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Como avaliar se a aplicação será afetada e o que fazer sobre o assunto
Como a aplicação processa o rollover da chave depende de variáveis, como o tipo de aplicação ou o protocolo de identidade e a biblioteca foi utilizada. As secções abaixo avaliar se os tipos de aplicações mais comuns são afetados pelo rollover da chave e fornecem orientações sobre como atualizar a aplicação para suportar o rollover automático ou atualizar manualmente a chave.

* [Aplicações de cliente nativo aceder a recursos](#nativeclient)
* [As aplicações Web / APIs aceder a recursos](#webclient)
* [As aplicações Web / APIs proteger os recursos e compilada com serviços de aplicações do Azure](#appservices)
* [As aplicações Web / APIs proteger recursos com o .NET OWIN OpenID Connect, WS-Fed ou WindowsAzureActiveDirectoryBearerAuthentication middleware](#owin)
* [As aplicações Web / APIs proteger recursos com o middleware .NET Core OpenID Connect ou JwtBearerAuthentication](#owincore)
* [As aplicações Web / APIs proteger recursos com o módulo de passport-azure-ad Node.js](#passport)
* [As aplicações Web / APIs proteger os recursos e criados com o Visual Studio 2015 ou Visual Studio 2017](#vs2015)
* [Aplicações Web de proteger os recursos e criados com o Visual Studio 2013](#vs2013)
* [APIs da Web proteger os recursos e criados com o Visual Studio 2013](#vs2013_webapi)
* [Aplicações Web de proteger os recursos e criados com o Visual Studio 2012](#vs2012)
* [Aplicações Web de proteger os recursos e criados com o Visual Studio 2010, o 2008 utilizando o Windows Identity Foundation](#vs2010)
* [As aplicações Web / APIs proteger recursos utilizar quaisquer outras bibliotecas de ou implementar manualmente a qualquer um dos protocolos suportados](#other)

Esta orientação é **não** aplicável para:

* As aplicações adicionadas a partir da Galeria de aplicações do Azure do AD (incluindo personalizada) ter orientações separada relativamente a chaves de assinatura. [Obter mais informações.](../active-directory-sso-certs.md)
* No local não tem aplicações publicadas através do proxy de aplicações de preocupar com chaves de assinatura.

### <a name="nativeclient"></a>Aplicações de cliente nativo aceder a recursos
Aplicações que só estão a aceder a recursos (revertidos Microsoft Graph, KeyVault, API do Outlook e outras APIs Microsoft), geralmente, apenas obter um token e transmita-o ao longo para o proprietário do recurso. Uma vez que estes não estiver a proteger os recursos, estes não inspecionar o token e, por conseguinte, não é necessário para se certificar de que está a ser assinado corretamente.

As aplicações cliente nativo, se o ambiente de trabalho ou de dispositivo móvel, inseridas nesta categoria e, por conseguinte, que não são afetadas pelo rollover.

### <a name="webclient"></a>As aplicações Web / APIs aceder a recursos
Aplicações que só estão a aceder a recursos (revertidos Microsoft Graph, KeyVault, API do Outlook e outras APIs Microsoft), geralmente, apenas obter um token e transmita-o ao longo para o proprietário do recurso. Uma vez que estes não estiver a proteger os recursos, estes não inspecionar o token e, por conseguinte, não é necessário para se certificar de que está a ser assinado corretamente.

As aplicações Web e APIs que estão a utilizar o fluxo só de aplicação web (credenciais de cliente / certificado de cliente), inseridas nesta categoria e, por conseguinte, que não são afetadas pelo rollover.

### <a name="appservices"></a>As aplicações Web / APIs proteger os recursos e compilada com serviços de aplicações do Azure
A autenticação dos serviços aplicacionais Azure / funcionalidade de autorização (EasyAuth) já tem a lógica necessária para processar automaticamente o rollover da chave.

### <a name="owin"></a>As aplicações Web / APIs proteger recursos com o .NET OWIN OpenID Connect, WS-Fed ou WindowsAzureActiveDirectoryBearerAuthentication middleware
Se a aplicação estiver a utilizar o .NET OWIN OpenID Connect, WS-Fed ou WindowsAzureActiveDirectoryBearerAuthentication middleware, já tem a lógica necessária para processar automaticamente o rollover da chave.

Pode confirmar que a aplicação está a utilizar qualquer um destes procurando qualquer os seguintes fragmentos Startup.cs ou Startup.Auth.cs da sua aplicação

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
     // ...
     });
```
```
 app.UseWindowsAzureActiveDirectoryBearerAuthentication(
     new WindowsAzureActiveDirectoryBearerAuthenticationOptions
     {
     // ...
     });
```

### <a name="owincore"></a>As aplicações Web / APIs proteger recursos com o middleware .NET Core OpenID Connect ou JwtBearerAuthentication
Se a aplicação estiver a utilizar o middleware .NET Core OWIN OpenID Connect ou JwtBearerAuthentication, já tem a lógica necessária para processar automaticamente o rollover da chave.

Pode confirmar que a aplicação está a utilizar qualquer um destes procurando qualquer os seguintes fragmentos Startup.cs ou Startup.Auth.cs da sua aplicação

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
     });
```

### <a name="passport"></a>As aplicações Web / APIs proteger recursos com o módulo de passport-azure-ad Node.js
Se a aplicação estiver a utilizar o módulo de passport-ad Node.js, já tem a lógica necessária para processar automaticamente o rollover da chave.

Pode confirmar que a aplicação passport-ad procurando o fragmento seguinte na app.js sua aplicação

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="vs2015"></a>As aplicações Web / APIs proteger os recursos e criados com o Visual Studio 2015 ou Visual Studio 2017
Se a aplicação foi criada utilizando um modelo de aplicação web no Visual Studio 2015 ou Visual Studio 2017 e selecionado **profissional e escolar contas** do **alterar autenticação** menu, já tem a lógica necessária para processar automaticamente o rollover da chave. Esta lógica, incorporada middleware OWIN OpenID Connect, obtém e coloca em cache as chaves de documento de identificação OpenID Connect e atualiza periodicamente.

Se adicionar autenticação à sua solução manualmente, a aplicação poderá não ter a lógica de rollover de chave necessário. Terá de escrever pessoalmente ou, siga os passos no [aplicações Web / APIs utilizar quaisquer outras bibliotecas de ou implementar manualmente a qualquer um dos protocolos suportados](#other).

### <a name="vs2013"></a>Aplicações Web de proteger os recursos e criados com o Visual Studio 2013
Se a aplicação foi criada utilizando um modelo de aplicação web no Visual Studio 2013 e selecionado **contas institucionais** do **alterar autenticação** menu, já tem a lógica necessária às para processar automaticamente o rollover da chave. Esta lógica armazena o identificador exclusivo da sua organização e as informações de chaves de assinatura nas duas tabelas de base de dados associadas ao projeto. Pode encontrar a cadeia de ligação para a base de dados no ficheiro Web. config do projeto.

Se adicionar autenticação à sua solução manualmente, a aplicação poderá não ter a lógica de rollover de chave necessário. Terá de escrever pessoalmente ou, siga os passos no [aplicações Web / APIs utilizar quaisquer outras bibliotecas de ou implementar manualmente a qualquer um dos protocolos suportados](#other).

Os passos seguintes irão ajudá-lo, certifique-se de que a lógica está a funcionar corretamente na sua aplicação.

1. No Visual Studio 2013, abra a solução e, em seguida, clique em de **Explorador de servidores** separador na janela à direita.
2. Expanda **ligações de dados**, **DefaultConnection**e, em seguida, **tabelas**. Localize o **IssuingAuthorityKeys** tabela, faça duplo clique nele e, em seguida, clique em **Mostrar dados de tabela**.
3. No **IssuingAuthorityKeys** tabela, pode haver pelo menos uma linha que corresponde ao valor de thumbprint para a chave. Elimine quaisquer linhas na tabela.
4. Clique com botão direito do **inquilinos** tabela e, em seguida, clique em **Mostrar dados de tabela**.
5. No **inquilinos** tabela, pode haver pelo menos uma linha que corresponde a um identificador de inquilino único diretório. Elimine quaisquer linhas na tabela. Se não eliminar as linhas em ambos os **inquilinos** tabela e **IssuingAuthorityKeys** tabela, obterá um erro durante a execução.
6. Crie e execute a aplicação. Depois de ter sessão iniciada para a sua conta, pode parar a aplicação.
7. Volte à **Explorador de servidores** e observe os valores existentes no **IssuingAuthorityKeys** e **inquilinos** tabela. Irá notar que tenham sido repovoados automaticamente, com as informações adequadas do documento de metadados de Federação.

### <a name="vs2013"></a>APIs da Web proteger os recursos e criados com o Visual Studio 2013
Se criou uma aplicação API da web no Visual Studio 2013, utilizando o modelo de Web API e, em seguida, selecionar **contas institucionais** do **alterar autenticação** menu, já tem o necessário lógica na sua aplicação.

Se tiver configurado manualmente authentication, siga as instruções abaixo para saber como configurar a sua API Web para atualizar automaticamente as informações de chaves.

O fragmento de código seguinte demonstra como obter as chaves mais recentes do documento de metadados de Federação e, em seguida, utilizar o [processador Token JWT](https://msdn.microsoft.com/library/dn205065.aspx) para validar o token. O fragmento de código parte do princípio de que irá utilizar o seus próprios mecanismo de colocação em cache para a chave a persistência para validar os tokens futuros do Azure AD, se, ser na base de dados, o ficheiro de configuração ou noutro local.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Classic Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="vs2012"></a>Aplicações Web de proteger os recursos e criados com o Visual Studio 2012
Se a aplicação foi criada no Visual Studio 2012, provavelmente, utilizou a identidade e a ferramenta de acesso para configurar a sua aplicação. Também é provável que está a utilizar o [validar o registo de nome de emissor (VINR)](https://msdn.microsoft.com/library/dn205067.aspx). O VINR é responsável pela manutenção informações sobre fornecedores de identidade fidedigna (Azure AD) e as chaves utilizadas para validar os tokens emitidos por-los. O VINR também torna mais fácil atualizar automaticamente as informações da chave armazenadas num ficheiro Web. config ao transferir o documento de metadados de Federação mais recente associado com o seu diretório, a verificar se a configuração está desatualizada com o documento mais recente, e a atualizar a aplicação para utilizar a nova chave conforme necessário.

Se tiver criado a sua aplicação utilizar qualquer um dos exemplos de código ou documentação instruções fornecida pela Microsoft, a lógica de rollover da chave já está incluída no seu projeto. Vai notar que o código abaixo já existe no seu projeto. Se a aplicação ainda não tiver esta lógica, siga os passos abaixo para adicioná-lo e certifique-se de que está a funcionar corretamente.

1. No **Explorador de soluções**, adicione uma referência para o **System** assemblagem para o projeto adequado.
2. Abra o **Global.asax.cs** de ficheiros e adicione o seguinte utilizando as diretivas de:
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. Adicione o seguinte método para o **Global.asax.cs** ficheiro:
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. Invocar o **RefreshValidationSettings()** método o **Application_Start()** método **Global.asax.cs** conforme mostrado:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

Depois de ter seguido estes passos, Web. config da sua aplicação será atualizada com as informações mais recentes do documento de metadados de Federação, incluindo as chaves mais recentes. Esta atualização irá ocorrer sempre que o conjunto aplicacional recicla no IIS; Por predefinição o IIS está definido para reciclar aplicações cada 29 horas.

Siga os passos abaixo para confirmar que a lógica de rollover de chave está a funcionar.

1. Depois de ter verificado que a aplicação está a utilizar o código acima, abra o **Web. config** de ficheiros e navegue para o  **<issuerNameRegistry>**  bloco, especificamente procura o seguinte algumas linhas:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. No  **<add thumbprint=””>**  definição, altere o valor de thumbprint ao substituir qualquer caráter com outro. Guardar o **Web. config** ficheiro.
3. Criar a aplicação e volte a executar. Se pode concluir o processo de início de sessão, a aplicação está com êxito a atualizar a chave ao transferir as informações necessárias do documento de metadados de Federação do Active. Se estiver a ter problemas em iniciar sessão, certifique-se de que as alterações na sua aplicação estão corretas ao ler o [adicionar início de sessão no seu Using de aplicação Web do Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) tópico, ou transferir e inspecionar o seguinte exemplo de código: [ Aplicação multi-inquilino de nuvem do Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).

### <a name="vs2010"></a>Aplicações Web de proteger os recursos e criados com o Visual Studio 2008 ou 2010 e a v 1.0 do Windows Identity Foundation (WIF) para o .NET 3.5
Se criou uma aplicação no v 1.0 do WIF, não há nenhum mecanismo fornecido para atualizar automaticamente a configuração da sua aplicação para utilizar uma nova chave.

* *A forma mais fácil* utilizar as ferramentas de FedUtil incluída no SDK do WIF, que pode obter o documento de metadados mais recente e atualizar a configuração.
* Atualize a sua aplicação para o .NET 4.5, que inclui a versão mais recente do WIF localizado no espaço de nomes de sistema. Em seguida, pode utilizar o [validar o registo de nome de emissor (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) para efetuar as atualizações automáticas da configuração da aplicação.
* Efetue um rollover manual de acordo com as instruções no final deste documento de orientação.

Instruções para utilizar o FedUtil para atualizar a configuração:

1. Certifique-se de que tem a v 1.0 do WIF SDK instalado no computador de desenvolvimento para o Visual Studio 2008 ou 2010. Pode [transferi-lo a partir daqui](https://www.microsoft.com/en-us/download/details.aspx?id=4451) se que ainda não instalou-lo.
2. No Visual Studio, abra a solução e, em seguida, clique com o botão direito no projeto aplicável e selecione **atualizar os metadados de Federação**. Se esta opção não estiver disponível, FedUtil e/ou a v 1.0 do WIF SDK não foi instalado.
3. A partir da linha de comandos, selecione **atualização** para começar a atualizar os metadados de Federação. Se tiver acesso ao ambiente de servidor em que a aplicação está alojada, opcionalmente, pode utilizar do FedUtil [Programador de atualização automática de metadados](https://msdn.microsoft.com/library/ee517272.aspx).
4. Clique em **concluir** para concluir o processo de atualização.

### <a name="other"></a>As aplicações Web / APIs proteger recursos utilizar quaisquer outras bibliotecas de ou implementar manualmente a qualquer um dos protocolos suportados
Se estiver a utilizar outras bibliotecas algumas ou manualmente implementado qualquer um dos protocolos suportados, terá de rever a biblioteca ou a implementação para se certificar de que a chave a ser obtida a partir do documento de identificação OpenID Connect ou os metadados de Federação documento. Uma forma para verificar isto é fazer uma pesquisa no seu código ou um código da biblioteca de quaisquer chamadas enviados para o documento de identificação de OpenID ou o documento de metadados de Federação.

Se a chave que estão a ser armazenados algures ou codificado na sua aplicação, pode manualmente obter a chave e a atualização, em conformidade por efetuar um rollover manual de acordo com as instruções no final deste documento de orientação. **É vivamente encouraged que melhorar a sua aplicação para suportar o rollover automático** utilizando qualquer um dos contorno abordagens neste artigo para evitar interrupções futuras e a sobrecarga se o Azure AD aumenta a cadência de rollover de ou tem de emergência rollover de fora de banda.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Como testar a aplicação para determinar se vai ser afetado
Pode validar se a aplicação suporta o rollover de chave automático, transferir os scripts e siga as instruções no [este repositório do GitHub.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-you-application-does-not-support-automatic-rollover"></a>Como efetuar um rollover manual se a aplicação não suporta o rollover automático
Se a aplicação **não** suporta rollover automático, é necessário estabelecer um processo que periodicamente monitores do Azure AD da assinatura de chaves e efetua um rollover manual em conformidade. [Este repositório do GitHub](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) contém scripts e obter instruções sobre como fazê-lo.

