---
title: "Utilizar o sistema de gestão de identidade entre domínios aprovisionar automaticamente os utilizadores e grupos do Azure Active Directory para aplicações | Microsoft Docs"
description: "Azure Active Directory podem aprovisionar automaticamente os utilizadores e grupos a qualquer arquivo de aplicação ou identidade que é fronted por um serviço web com a interface definida na especificação de protocolo SCIM"
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: 4d86f3dc-e2d3-4bde-81a3-4a0e092551c0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2017
ms.author: asmalser
ms.reviewer: asmalser
ms.custom: aaddev;it-pro;oldportal
ms.openlocfilehash: 91978cee88d55c99bcb63c63cdaf01581ae84668
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="using-system-for-cross-domain-identity-management-to-automatically-provision-users-and-groups-from-azure-active-directory-to-applications"></a>Utilizar o sistema para a gestão de identidade entre domínios para aprovisionar automaticamente os utilizadores e grupos do Azure Active Directory para aplicações

## <a name="overview"></a>Descrição geral
Azure Active Directory (Azure AD) podem aprovisionar automaticamente os utilizadores e grupos a qualquer arquivo de aplicação ou identidade que é fronted por um serviço web com a interface definidos no [sistema para o protocolo de gestão de identidade entre domínios (SCIM) 2.0 especificação](https://tools.ietf.org/html/draft-ietf-scim-api-19). Do Azure Active Directory pode enviar pedidos para criar, modificar ou eliminar atribuídos a utilizadores e grupos para o serviço web. O serviço web, em seguida, pode traduzir os pedidos em operações no arquivo de identidade de destino. 

> [!IMPORTANT]
> A Microsoft recomenda que faça a gestão do Azure AD com o [centro de administração do Azure AD](https://aad.portal.azure.com) no portal do Azure em vez de utilizar o portal clássico do Azure referenciado neste artigo. 



![][0]
*Figura 1: Aprovisionamento do Azure Active Directory para um arquivo de identidade através de um serviço web*

Esta capacidade pode ser utilizada em conjunto com a capacidade de "traga a sua própria aplicação" no Azure AD para ativar o início de sessão único e utilizador automáticas para aplicações que fornecem ou são fronted por um serviço web SCIM de aprovisionamento.

Existem dois casos de utilização para utilizar o SCIM no Azure Active Directory:

* **Aprovisionamento de utilizadores e grupos para aplicações que suportam o SCIM** aplicações que suportem o SCIM 2.0 e utilizam tokens de portador do OAuth para a autenticação funciona com o Azure AD sem configuração.
* **Criar a sua própria solução de aprovisionamento de aplicações que suportem outros aprovisionamento baseado na API** para aplicações não SCIM, pode criar um ponto final de SCIM traduzir entre o ponto final do Azure AD SCIM e qualquer API suporta a aplicação para utilizador o aprovisionamento. Para ajudar a desenvolver um ponto final SCIM, fornecemos bibliotecas de infraestrutura de idioma comum (CLI) juntamente com os exemplos de código que mostram como para fornecer um ponto final SCIM e traduzir mensagens SCIM.  

## <a name="provisioning-users-and-groups-to-applications-that-support-scim"></a>Aprovisionamento de utilizadores e grupos para aplicações que suportam o SCIM
Azure AD pode ser configurado para automaticamente a aprovisionar atribuída utilizadores e grupos para aplicações que implementa um [sistema entre domínios para gestão de identidades 2 (SCIM)](https://tools.ietf.org/html/draft-ietf-scim-api-19) serviço web e aceitar tokens de portador do OAuth para autenticação. Na especificação SCIM 2.0, as aplicações têm de cumprir estes requisitos:

* Suporta a criação de utilizadores e/ou grupos, de acordo com a secção 3.3 do protocolo SCIM.  
* Suporta a modificação de utilizadores e/ou grupos com pedidos de patch de acordo com a secção 3.5.2 do protocolo SCIM.  
* Suporta a obter um recurso de acordo com a secção 3.4.1 do protocolo SCIM conhecido.  
* Suporta a consultar os utilizadores e/ou grupos, de acordo com a secção 3.4.2 do protocolo SCIM.  Por predefinição, os utilizadores são consultados pelo externalId e grupos são consultados pelo displayName.  
* Suporta a consultar o utilizador por ID e pelo Gestor de acordo com a secção 3.4.2 do protocolo SCIM.  
* Suporta grupos por ID e membros de acordo com a secção 3.4.2 do protocolo SCIM a consultar.  
* Aceita tokens de portador do OAuth para autorização de acordo com a secção 2.1 do protocolo SCIM.

Contacte o seu fornecedor de aplicação, ou na documentação do fornecedor da aplicação de declarações do Estado de compatibilidade com estes requisitos.

### <a name="getting-started"></a>Introdução
Aplicações que suportam o perfil SCIM descrito neste artigo podem ser ligadas ao Azure Active Directory utilizando a funcionalidade de "aplicação não Galeria" na Galeria de aplicações do Azure AD. Assim que estiver ligado, o Azure AD executa um processo de sincronização onde-consulta o ponto final SCIM da aplicação para utilizadores atribuídos e grupos e cria ou modifica-los de acordo com os detalhes de atribuição a cada 20 minutos.

**Para ligar uma aplicação que suporta o SCIM:**

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Navegue para * * do Azure Active Directory > aplicações da empresa e selecione **nova aplicação > todos os > aplicação de Galeria não**.
3. Introduza um nome para a sua aplicação e clique em **adicionar** ícone para criar um objeto de aplicação.
    
  ![][1]
  *Figura 2: Galeria de aplicações do Azure AD*
    
4. No ecrã de resultante, selecione o **aprovisionamento** separador na coluna esquerda.
5. No **modo de aprovisionamento** menu, selecione **automática**.
    
  ![][2]
  *Figura 3: Configurar o aprovisionamento no portal do Azure*
    
6. No **URL de inquilino** campo, introduza o URL de ponto final SCIM da aplicação. Exemplo: https://api.contoso.com/scim/v2/
7. Se o ponto final SCIM requer um token de portador do OAuth a partir de um emissor diferente do Azure AD, em seguida, copie o token de portador do OAuth necessário para a opção **segredo Token** campo. Se este campo for deixado em branco, o Azure AD incluído um token de portador do OAuth emitido a partir do Azure AD com cada pedido. Aplicações que utilizam o Azure AD como um fornecedor de identidade pode validar do Azure AD-emitido token.
8. Clique em de **Testar ligação** botão com o Azure Active Directory tentam estabelecer ligação ao ponto final do SCIM. Se as tentativas de falharem, são apresentadas informações de erro.  
9. Se as tentativas para ligar com êxito a aplicação, em seguida, clique em **guardar** para guardar as credenciais de administrador.
10. No **mapeamentos** secção, existem dois conjuntos selecionáveis de mapeamentos de atributos: um para objetos de utilizador e outro para objetos de grupo. Selecione cada um para rever os atributos que são sincronizados a partir do Azure Active Directory para a sua aplicação. Os atributos selecionados como **correspondência** propriedades são utilizadas para fazer corresponder os utilizadores e grupos na sua aplicação para as operações de atualização. Selecione o botão Guardar para consolidar as alterações.

    >[!NOTE]
    >Opcionalmente, pode desativar a sincronização de objetos de grupo através da desativação de mapeamento "grupos". 

11. Em **definições**, a **âmbito** campo define quais os utilizadores e grupos ou estão sincronizados. Selecionar "Sincronização só atribuir utilizadores e grupos" (recomendado) só irá sincronizar os utilizadores e grupos atribuídos no **utilizadores e grupos** separador.
12. Assim que a configuração estiver concluída, altere o **estado de aprovisionamento** para **no**.
13. Clique em **guardar** para iniciar o Azure AD que o serviço de fornecimento. 
14. Se apenas a sincronizar atribuídos a utilizadores e grupos (recomendados), é necessário selecionar o **utilizadores e grupos** separador e atribuir utilizadores e/ou grupos que pretende sincronizar.

Depois de é iniciado a sincronização inicial, pode utilizar o **registos de auditoria** separador para monitorize o progresso, que mostra todas as ações efetuadas pelo serviço de aprovisionamento na sua aplicação. Para obter mais informações sobre como ler o Azure AD, os registos de aprovisionamento, consulte [relatórios sobre o aprovisionamento da conta de utilizador automáticas](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting).

>[!NOTE]
>A sincronização inicial demora mais para efetuar a sincronizações subsequentes, o que ocorrer aproximadamente a cada 20 minutos, desde que o serviço está em execução. 


## <a name="building-your-own-provisioning-solution-for-any-application"></a>Criar a sua própria solução de aprovisionamento para qualquer aplicação
Ao criar um serviço web SCIM que interaja com o Azure Active Directory, pode ativar o único utilizador de início de sessão e automático de aprovisionamento para praticamente qualquer aplicação que fornece uma API de aprovisionamento de utilizadores REST ou SOAP.

Eis como funciona:

1. O Azure AD fornece uma biblioteca de infraestrutura de idioma comum com o nome [Microsoft.SystemForCrossDomainIdentityManagement](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/). Integradores de sistema e os programadores podem utilizar esta biblioteca para criar e implementar um web baseado em SCIM ponto final de serviço com capacidade para ligar do Azure AD para o arquivo de identidade de qualquer aplicação.
2. Mapeamentos são implementados no serviço web para mapear o esquema de utilizador padronizado para o esquema de utilizador e o protocolo necessárias para a aplicação.
3. O URL de ponto final está registado no Azure AD como parte de uma aplicação personalizada na Galeria de aplicações.
4. Utilizadores e grupos que estão atribuídos a esta aplicação no Azure AD. Após a atribuição, estes são colocados uma fila para ser sincronizado a aplicação de destino. O processo de sincronização a fila de processamento é executado a cada 20 minutos.

### <a name="code-samples"></a>Exemplos de Código
Para efetuar este processo mais fácil, um conjunto de [exemplos de código](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master) fornecidas que criar um ponto de final do serviço de web SCIM e demonstrar o aprovisionamento automático. É um exemplo de um fornecedor que mantém um ficheiro com as linhas de valores separados por vírgulas que representam os utilizadores e grupos.  O outro é de um fornecedor que funciona com o serviço Amazon Web Services identidade e gestão de acesso.  

**Pré-requisitos**

* Visual Studio 2013 ou posterior
* [Azure SDK for .NET](https://azure.microsoft.com/downloads/) (Azure SDK para .NET)
* Computador Windows que suporte a arquitetura do ASP.NET 4.5 para ser utilizado como o ponto final SCIM. Esta máquina tem de estar acessível a partir da nuvem
* [Uma subscrição do Azure com uma versão de avaliação ou licenciada do Azure AD Premium](https://azure.microsoft.com/services/active-directory/)
* O exemplo de Amazon AWS requer bibliotecas do [Toolkit do AWS para Visual Studio](http://docs.aws.amazon.com/AWSToolkitVS/latest/UserGuide/tkv_setup.html). Para obter mais informações, consulte o ficheiro Leia-me incluído com o exemplo.

### <a name="getting-started"></a>Introdução
É a forma mais fácil de implementar um ponto final de SCIM pode aceitar pedidos de aprovisionamento do Azure AD para criar e implementar o código de exemplo que produz os utilizadores aprovisionados para um ficheiro de valores separados por vírgulas (CSV).

**Para criar um ponto final SCIM de exemplo:**

1. Transferir o pacote de exemplo de código em [https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master](https://github.com/Azure/AzureAD-BYOA-Provisioning-Samples/tree/master)
2. Deszipe o pacote e colocá-lo no seu computador Windows numa localização como C:\AzureAD-BYOA-Provisioning-Samples\.
3. Nesta pasta, inicie a solução de FileProvisioningAgent no Visual Studio.
4. Selecione **ferramentas > Gestor de pacotes de biblioteca > consola do Gestor de pacotes**e execute os seguintes comandos para o projeto de FileProvisioningAgent resolver as referências de solução:
  ```` 
   Install-Package Microsoft.SystemForCrossDomainIdentityManagement
   Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
   Install-Package Microsoft.Owin.Diagnostics
   Install-Package Microsoft.Owin.Host.SystemWeb
  ````
5. Compilar o projeto de FileProvisioningAgent.
6. Iniciar a aplicação de linha de comandos do Windows (como administrador) e utilizar o **cd** comando para alterar o diretório a sua **\AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug** pasta.
7. Execute o seguinte comando, substituindo < endereço ip > com o nome de domínio ou endereço IP da máquina Windows:
  ````   
   FileAgnt.exe http://<ip-address>:9000 TargetFile.csv
  ````
8. No Windows sob **definições do Windows > redes e definições da Internet**, selecione o **Firewall do Windows > Definições Avançadas**e criar um **regras de entrada** que permite o acesso de entrada para a porta 9000.
9. Se o computador do Windows estiver atrás de um router, o router tem de ser configurado para efetuar a conversão de acesso de rede entre a respetiva porta 9000 que é exposto à internet e da porta 9000 no computador Windows. Isto é necessário para o Azure AD poder aceder a este ponto final na nuvem.

**Para registar o ponto final SCIM de exemplo no Azure AD:**

1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
2. Navegue para * * do Azure Active Directory > aplicações da empresa e selecione **nova aplicação > todos os > aplicação de Galeria não**.
3. Introduza um nome para a sua aplicação e clique em **adicionar** ícone para criar um objeto de aplicação. O objeto de aplicação criado destina-se para representar a aplicação de destino, teria de ser aprovisionamento e implementação-início de sessão único para e não apenas o ponto final SCIM.
4. No ecrã de resultante, selecione o **aprovisionamento** separador na coluna esquerda.
5. No **modo de aprovisionamento** menu, selecione **automática**.
    
  ![][2]
  *Figura 4: Configurar o aprovisionamento no portal do Azure*
    
6. No **URL de inquilino** campo, introduza o URL e a porta do seu ponto final SCIM expostos a internet. Isto seria algo como http://testmachine.contoso.com:9000 ou http://<ip-address>:9000/, onde < endereço ip > é internet expostos IP endereço.  
7. Se o ponto final SCIM requer um token de portador do OAuth a partir de um emissor diferente do Azure AD, em seguida, copie o token de portador do OAuth necessário para a opção **segredo Token** campo. Se este campo for deixado em branco, o Azure AD irá incluir um token de portador do OAuth emitido a partir do Azure AD com cada pedido. Aplicações que utilizam o Azure AD como um fornecedor de identidade pode validar do Azure AD-emitido token.
8. Clique em de **Testar ligação** botão com o Azure Active Directory tentam estabelecer ligação ao ponto final do SCIM. Se as tentativas de falharem, são apresentadas informações de erro.  
9. Se as tentativas para ligar com êxito a aplicação, em seguida, clique em **guardar** para guardar as credenciais de administrador.
10. No **mapeamentos** secção, existem dois conjuntos selecionáveis de mapeamentos de atributos: um para objetos de utilizador e outro para objetos de grupo. Selecione cada um para rever os atributos que são sincronizados a partir do Azure Active Directory para a sua aplicação. Os atributos selecionados como **correspondência** propriedades são utilizadas para fazer corresponder os utilizadores e grupos na sua aplicação para as operações de atualização. Selecione o botão Guardar para consolidar as alterações.
11. Em **definições**, a **âmbito** campo define quais os utilizadores e grupos ou estão sincronizados. Selecionar "Sincronização só atribuir utilizadores e grupos" (recomendado) só irá sincronizar os utilizadores e grupos atribuídos no **utilizadores e grupos** separador.
12. Assim que a configuração estiver concluída, altere o **estado de aprovisionamento** para **no**.
13. Clique em **guardar** para iniciar o Azure AD que o serviço de fornecimento. 
14. Se apenas a sincronizar atribuídos a utilizadores e grupos (recomendados), é necessário selecionar o **utilizadores e grupos** separador e atribuir utilizadores e/ou grupos que pretende sincronizar.

Depois de é iniciado a sincronização inicial, pode utilizar o **registos de auditoria** separador para monitorize o progresso, que mostra todas as ações efetuadas pelo serviço de aprovisionamento na sua aplicação. Para obter mais informações sobre como ler o Azure AD, os registos de aprovisionamento, consulte [relatórios sobre o aprovisionamento da conta de utilizador automáticas](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-saas-provisioning-reporting).

O passo final na verificação a amostra é para abrir o ficheiro de TargetFile.csv na pasta \AzureAD-BYOA-Provisioning-Samples\ProvisioningAgent\bin\Debug no seu computador Windows. Depois do processo de aprovisionamento é executado, este ficheiro mostra os detalhes de todas as atribuído e aprovisionamento de utilizadores e grupos.

### <a name="development-libraries"></a>Bibliotecas de desenvolvimento
Para desenvolver o seu próprio serviço web que está em conformidade com a especificação de SCIM, primeiro familiarizar-se com as seguintes bibliotecas fornecidas pela Microsoft para ajudar a acelerar o processo de desenvolvimento: 

1. Bibliotecas de idioma infraestrutura (CLI) comum são disponibilizadas para utilização com idiomas com base nessa infraestrutura, como c#. Uma das bibliotecas desses, [Microsoft.SystemForCrossDomainIdentityManagement.Service](https://www.nuget.org/packages/Microsoft.SystemForCrossDomainIdentityManagement/), declara uma interface, Microsoft.SystemForCrossDomainIdentityManagement.IProvider, mostrada na ilustração seguinte: um programador utilizando as bibliotecas seria implementar essa interface com uma classe que pode ser referida, genericamente, como um fornecedor. As bibliotecas de ativar o programador implementar um serviço web que está em conformidade com a especificação de SCIM. O serviço web pode ser alojado está dentro de serviços de informação Internet ou qualquer executável assemblagem de infraestrutura de idioma comum. Pedido é convertido em chamadas para métodos do fornecedor, o que podem ser programados pelo programador para operar em algumas arquivo de identidade.
  
  ![][3]
  
2. [Express route processadores](http://expressjs.com/guide/routing.html) disponíveis para analisar os objetos de pedidos de node.js que representa as chamadas (conforme definido pela especificação SCIM), efetuadas a um serviço web do node.js.   

### <a name="building-a-custom-scim-endpoint"></a>Criar um ponto final SCIM personalizado
Utilizar as bibliotecas do CLI, os programadores utilizando essas bibliotecas podem alojar os respetivos serviços dentro qualquer assemblagem de infraestrutura de idioma comum executável ou dentro de serviços de informação Internet. Eis o código de exemplo para alojar um serviço numa assemblagem executável, o endereço http://localhost:9000: 

    private static void Main(string[] arguments)
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IProvider and 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
      new DevelopersMonitor();
    Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
      new DevelopersProvider(arguments[1]);
    Microsoft.SystemForCrossDomainIdentityManagement.Service webService = null;
    try
    {
        webService = new WebService(monitor, provider);
        webService.Start("http://localhost:9000");

        Console.ReadKey(true);
    }
    finally
    {
        if (webService != null)
        {
            webService.Dispose();
            webService = null;
        }
    }
    }

    public class WebService : Microsoft.SystemForCrossDomainIdentityManagement.Service
    {
    private Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor;
    private Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider;

    public WebService(
      Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitoringBehavior, 
      Microsoft.SystemForCrossDomainIdentityManagement.IProvider providerBehavior)
    {
        this.monitor = monitoringBehavior;
        this.provider = providerBehavior;
    }

    public override IMonitor MonitoringBehavior
    {
        get
        {
            return this.monitor;
        }

        set
        {
            this.monitor = value;
        }
    }

    public override IProvider ProviderBehavior
    {
        get
        {
            return this.provider;
        }

        set
        {
            this.provider = value;
        }
    }
    }

Este serviço tem de ter um HTTP endereço e o servidor de certificado de autenticação do que a autoridade de certificação de raiz é um dos seguintes: 

* CNNIC
* Comodo
* Root da CyberTrust
* DigiCert
* GeoTrust
* GlobalSign
* Go Daddy
* VeriSign
* WoSign

Um certificado de autenticação de servidor pode ser vinculado a uma porta num anfitrião Windows utilizando o utilitário de shell de rede: 

    netsh http add sslcert ipport=0.0.0.0:443 certhash=0000000000003ed9cd0c315bbb6dc1c08da5e6 appid={00112233-4455-6677-8899-AABBCCDDEEFF}  

Aqui, o valor fornecido para o argumento de certhash é o thumbprint do certificado, enquanto o valor fornecido para o argumento de appid é um identificador exclusivo global arbitrário.  

Para alojar o serviço dentro de serviços de informação de Internet, um programador iria criar uma assemblagem de biblioteca de código CLA com uma classe com o nome de arranque no espaço de nomes predefinido da assemblagem.  Eis um exemplo de uma classe deste tipo: 

    public class Startup
    {
    // Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IMonitor and  
    // Microsoft.SystemForCrossDomainIdentityManagement.Service are all defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Service.dll.  

    Microsoft.SystemForCrossDomainIdentityManagement.IWebApplicationStarter starter;

    public Startup()
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IMonitor monitor = 
          new DevelopersMonitor();
        Microsoft.SystemForCrossDomainIdentityManagement.IProvider provider = 
          new DevelopersProvider();
        this.starter = 
          new Microsoft.SystemForCrossDomainIdentityManagement.WebApplicationStarter(
            provider, 
            monitor);
    }

    public void Configuration(
      Owin.IAppBuilder builder) // Defined in in Owin.dll.  
    {
        this.starter.ConfigureApplication(builder);
    }
    }

### <a name="handling-endpoint-authentication"></a>Autenticação de ponto final de processamento
Pedidos do Azure Active Directory incluem um token de portador do OAuth 2.0.   Qualquer serviço receber o pedido deve autenticar o emissor como sendo o Azure Active Directory em nome de inquilino do Azure Active Directory esperado, para acesso ao serviço web do Azure Active Directory Graph.  No token, o emissor é identificado por uma afirmação de iss, como "iss": "https://sts.windows.net/cbb1a5ac-f33b-45fa-9bf5-f37db0fed422/".  Neste exemplo, o endereço base do valor de afirmação, https://sts.windows.net, identifica o Azure Active Directory como o emissor, enquanto o segmento de endereço relativo, cbb1a5ac-f33b-45fa-9bf5-f37db0fed422, é um identificador exclusivo do Azure Active Directory inquilino em nome que o token foi emitido.  Se o token foi emitido para aceder ao serviço web do Azure Active Directory Graph, em seguida, o identificador do que o serviço, 00000002-0000-0000-c000-000000000000, deve ter o valor de afirmação de aud do token.  

Os programadores através das bibliotecas CLA fornecidas pela Microsoft para a criação de um serviço SCIM podem autenticar pedidos do Azure Active Directory utilizando o pacote de Microsoft.Owin.Security.ActiveDirectory seguindo estes passos: 

1. De um fornecedor, implemente a propriedade Microsoft.SystemForCrossDomainIdentityManagement.IProvider.StartupBehavior fazendo com que devolvem um método a chamar sempre que o serviço está iniciado: 

  ````
    public override Action\<Owin.IAppBuilder, System.Web.Http.HttpConfiguration.HttpConfiguration\> StartupBehavior
    {
      get
      {
        return this.OnServiceStartup;
      }
    }

    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder,  // Defined in Owin.dll.  
      System.Web.Http.HttpConfiguration configuration)  // Defined in System.Web.Http.dll.  
    {
    }
  ````

2. Adicione o seguinte código para este método para que qualquer pedido de qualquer um dos pontos finais do serviço autenticados como bearing um token emitido pelo Azure Active Directory em nome de um inquilino especificado, para acesso ao serviço web do Azure AD Graph: 

  ````
    private void OnServiceStartup(
      Owin.IAppBuilder applicationBuilder IAppBuilder applicationBuilder, 
      System.Web.Http.HttpConfiguration HttpConfiguration configuration)
    {
      // IFilter is defined in System.Web.Http.dll.  
      System.Web.Http.Filters.IFilter authorizationFilter = 
        new System.Web.Http.AuthorizeAttribute(); // Defined in System.Web.Http.dll.configuration.Filters.Add(authorizationFilter);

      // SystemIdentityModel.Tokens.TokenValidationParameters is defined in    
      // System.IdentityModel.Token.Jwt.dll.
      SystemIdentityModel.Tokens.TokenValidationParameters tokenValidationParameters =     
        new TokenValidationParameters()
        {
          ValidAudience = "00000002-0000-0000-c000-000000000000"
        };

      // WindowsAzureActiveDirectoryBearerAuthenticationOptions is defined in 
      // Microsoft.Owin.Security.ActiveDirectory.dll
      Microsoft.Owin.Security.ActiveDirectory.
      WindowsAzureActiveDirectoryBearerAuthenticationOptions authenticationOptions =
        new WindowsAzureActiveDirectoryBearerAuthenticationOptions()    {
        TokenValidationParameters = tokenValidationParameters,
        Tenant = "03F9FCBC-EA7B-46C2-8466-F81917F3C15E" // Substitute the appropriate tenant’s 
                                                      // identifier for this one.  
      };

      applicationBuilder.UseWindowsAzureActiveDirectoryBearerAuthentication(authenticationOptions);
    }
  ````


## <a name="user-and-group-schema"></a>Esquema de utilizador e grupo
Azure Active Directory pode aprovisionar dois tipos de recursos para os serviços web SCIM.  Os tipos de recursos são utilizadores e grupos.  

Recursos de utilizador são identificados pelo identificador de esquema, urn: ietf:params:scim:schemas:extension:enterprise:2.0:User, que está incluído nesta especificação de protocolo: http://tools.ietf.org/html/draft-ietf-scim-core-schema.  O mapeamento predefinido de atributos de utilizadores no Azure Active Directory para os atributos de recursos de urn: ietf:params:scim:schemas:extension:enterprise:2.0:User é fornecido na tabela 1, abaixo.  

Grupo de recursos é identificados pelo identificador de esquema, http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  Tabela 2, abaixo, mostra o mapeamento predefinido de atributos de grupos no Azure Active Directory para os atributos de recursos de http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  

### <a name="table-1-default-user-attribute-mapping"></a>1 de tabela: Mapeamento de atributos de utilizador de predefinido
| Utilizador do Active Directory do Azure | urn: ietf:params:scim:schemas:extension:enterprise:2.0:User |
| --- | --- |
| IsSoftDeleted |Active Directory |
| displayName |displayName |
| Facsimile TelephoneNumber |.value phoneNumbers [eq "faxes" do tipo] |
| givenName |name.givenName |
| jobTitle |Título |
| capacidade de correio |mensagens de correio eletrónico [eq "trabalho" do tipo] .value |
| mailNickname |externalId |
| Gestor |Gestor |
| Mobile |.value phoneNumbers [eq de tipo "móvel"] |
| objectId |ID |
| PostalCode |.postalCode endereços [eq "trabalho" do tipo] |
| Endereços de proxy |mensagens de correio eletrónico [Escreva eq "outro"]. Valor |
| OfficeName de entrega de física |endereços [Escreva eq "outro"]. Formatado |
| StreetAddress |.streetAddress endereços [eq "trabalho" do tipo] |
| Apelido |name.familyName |
| Número de telefone |.value phoneNumbers [eq "trabalho" do tipo] |
| utilizador PrincipalName |Nome de utilizador |

### <a name="table-2-default-group-attribute-mapping"></a>2 da tabela: O mapeamento de atributos de grupo predefinido
| Grupo do Active Directory do Azure | http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group |
| --- | --- |
| displayName |externalId |
| capacidade de correio |mensagens de correio eletrónico [eq "trabalho" do tipo] .value |
| mailNickname |displayName |
| Membros |Membros |
| objectId |ID |
| proxyAddresses |mensagens de correio eletrónico [Escreva eq "outro"]. Valor |

## <a name="user-provisioning-and-de-provisioning"></a>Aprovisionamento de utilizador e aprovisionamento automatizados.
A ilustração seguinte mostra as mensagens que o Azure Active Directory envia a um serviço SCIM para gerir o ciclo de vida de um utilizador no arquivo de identidade do outro. O diagrama mostra também como um serviço SCIM implementado utilizando as bibliotecas CLI fornecida pela Microsoft para edifício que desses serviços implica esses pedidos chamadas para os métodos de um fornecedor.  

![][4]
*Figura 5: Aprovisionamento de utilizador e a sequência de aprovisionamento automatizados.*

1. Azure Active Directory consulta o serviço para um utilizador com um valor de atributo externalId correspondente o valor do atributo mailNickname de um utilizador no Azure AD. A consulta é expresso como um pedido de protocolo HTTP (Hypertext Transfer), tal como neste exemplo, wherein jyoung é uma amostra de uma mailNickname de um utilizador no Azure Active Directory: 
  ````
    GET https://.../scim/Users?filter=externalId eq jyoung HTTP/1.1
    Authorization: Bearer ...
  ````
  Se o serviço foi criado com as bibliotecas de infraestrutura de idioma comum fornecidas pela Microsoft para implementar serviços SCIM, em seguida, o pedido é convertido uma chamada para o método de consulta do fornecedor do serviço.  Segue-se a assinatura do método de que: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Protocol.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource[]> Query(
      Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters parameters, 
      string correlationIdentifier);
  ````
  Segue-se a definição da Microsoft.SystemForCrossDomainIdentityManagement.IQueryParameters interface: 
  ````
    public interface IQueryParameters: 
      Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
        System.Collections.Generic.IReadOnlyCollection <Microsoft.SystemForCrossDomainIdentityManagement.IFilter> AlternateFilters 
        { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IRetrievalParameters
    {
      system.Collections.Generic.IReadOnlyCollection<string> ExcludedAttributePaths 
      { get; }
      System.Collections.Generic.IReadOnlyCollection<string> RequestedAttributePaths 
      { get; }
      string SchemaIdentifier 
      { get; }
    }

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IFilter
    {
        Microsoft.SystemForCrossDomainIdentityManagement.IFilter AdditionalFilter 
          { get; set; }
        string AttributePath 
          { get; } 
        Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator FilterOperator 
          { get; }
        string ComparisonValue 
          { get; }
    }

    public enum Microsoft.SystemForCrossDomainIdentityManagement.ComparisonOperator
    {
        Equals
    }
  ````
  No seguinte exemplo de uma consulta para um utilizador com um valor especificado para o atributo externalId, os valores dos argumentos transmitidos para o método de consulta são: 
  * parâmetros. AlternateFilters.Count: 1
  * parâmetros. AlternateFilters.ElementAt(0). AttributePath: "externalId"
  * parâmetros. AlternateFilters.ElementAt(0). ComparisonOperator: ComparisonOperator.Equals
  * parâmetros. AlternateFilter.ElementAt(0). ComparisonValue: "jyoung"
  * correlationIdentifier: System.Net.Http.HttpRequestMessage.GetOwinEnvironment["owin. RequestId"] 

2. Se a resposta a uma consulta para o serviço web para um utilizador com um valor de atributo externalId que corresponde ao valor de atributo de mailNickname de um utilizador não devolver quaisquer utilizadores, em seguida, do Azure Active Directory solicita que o serviço de aprovisionar um utilizador correspondente para o no Azure Active Directory.  Eis um exemplo deste pedido: 
  ````
    POST https://.../scim/Users HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas":
      [
        "urn:ietf:params:scim:schemas:core:2.0:User",
        "urn:ietf:params:scim:schemas:extension:enterprise:2.0User"],
      "externalId":"jyoung",
      "userName":"jyoung",
      "active":true,
      "addresses":null,
      "displayName":"Joy Young",
      "emails": [
        {
          "type":"work",
          "value":"jyoung@Contoso.com",
          "primary":true}],
      "meta": {
        "resourceType":"User"},
       "name":{
        "familyName":"Young",
        "givenName":"Joy"},
      "phoneNumbers":null,
      "preferredLanguage":null,
      "title":null,
      "department":null,
      "manager":null}
  ````
  As bibliotecas de infraestrutura de idioma comum fornecidas pela Microsoft para implementar serviços SCIM seriam implica uma chamada para o método de criação do fornecedor do serviço desse pedido.  O método de criação tem esta assinatura: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource is defined in 
    // Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  

    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> Create(
      Microsoft.SystemForCrossDomainIdentityManagement.Resource resource, 
      string correlationIdentifier);
  ````
  Num pedido para aprovisionar um utilizador, o valor do argumento de recursos é uma instância do Microsoft.SystemForCrossDomainIdentityManagement. Classe de Core2EnterpriseUser, definido na biblioteca Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  Se o pedido para aprovisionar o utilizador for bem sucedida, em seguida, a implementação do método deverá devolver uma instância do Microsoft.SystemForCrossDomainIdentityManagement. Classe de Core2EnterpriseUser, com o valor da propriedade identificador definida para o identificador exclusivo do utilizador recentemente aprovisionado.  

3. Para atualizar um utilizador ao conhecido existem num arquivo de identidade fronted por um SCIM, o Azure Active Directory prossegue ao solicitar o estado atual do utilizador do serviço com um pedido, tais como: 
  ````
    GET ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
  ````
  Num serviço criado com as bibliotecas de infraestrutura de idioma comum fornecidas pela Microsoft para implementar serviços SCIM, o pedido é convertido uma chamada para o método de obtenção do fornecedor do serviço.  Segue-se a assinatura do método obter: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.Resource and 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
    // are defined in Microsoft.SystemForCrossDomainIdentityManagement.Schemas.  
    System.Threading.Tasks.Task<Microsoft.SystemForCrossDomainIdentityManagement.Resource> 
       Retrieve(
         Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters 
           parameters, 
           string correlationIdentifier);

    public interface 
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceRetrievalParameters:   
        IRetrievalParameters
        {
          Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
            ResourceIdentifier 
              { get; }
    }
    public interface Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier
    {
        string Identifier 
          { get; set; }
        string Microsoft.SystemForCrossDomainIdentityManagement.SchemaIdentifier 
          { get; set; }
    }
  ````
  No exemplo de um pedido para obter o estado atual de um utilizador, os valores das propriedades do objeto fornecido como o valor do argumento parâmetros são: 
  
  * Identificador: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

4. Se um atributo de referência está a ser atualizado, em seguida, o serviço para determinar se o valor do atributo de referência no arquivo de identidade atual fronted pelo serviço já consulta o Azure Active Directory corresponde ao valor desse atributo no Active Directory do Azure Diretório. Para os utilizadores, o atributo só de que o valor atual é consultado desta forma é o atributo de gestor. Eis um exemplo de um pedido para determinar se o atributo de Gestor de um objeto de utilizador em particular tem atualmente um determinado valor: 
  ````
    GET ~/scim/Users?filter=id eq 54D382A4-2050-4C03-94D1-E769F1D15682 and manager eq 2819c223-7f76-453a-919d-413861904646&attributes=id HTTP/1.1
    Authorization: Bearer ...
  ````
  O valor do parâmetro de consulta atributos id, significa que não existir um objeto de utilizador que satisfaça a expressão fornecida como o valor do parâmetro de consulta de filtro, em seguida, o serviço é esperado para responder com um urn: ietf:params:scim:schemas:core:2.0:User ou recurso de urn: ietf:params:scim:schemas:extension:enterprise:2.0:User, incluindo apenas o valor do atributo de id esse recurso.  O valor da **id** atributo é conhecido para o requerente. Está incluído no valor do parâmetro de consulta filtro; o objetivo pedir para o mesmo é, na verdade pedir uma representação mínima de um recurso que que satisfaçam a expressão de filtro como uma indicação de se pretende ou não existe qualquer esse objeto.   

  Se o serviço foi criado com as bibliotecas de infraestrutura de idioma comum fornecidas pela Microsoft para implementar serviços SCIM, em seguida, o pedido é convertido uma chamada para o método de consulta do fornecedor do serviço. O valor das propriedades do objeto fornecido como o valor do argumento parâmetros são os seguintes: 
  
  * parâmetros. AlternateFilters.Count: 2
  * parâmetros. AlternateFilters.ElementAt(x). AttributePath: "id"
  * parâmetros. AlternateFilters.ElementAt(x). ComparisonOperator: ComparisonOperator.Equals
  * parâmetros. AlternateFilter.ElementAt(x). ComparisonValue: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * parâmetros. AlternateFilters.ElementAt(y). AttributePath: "Gestor"
  * parâmetros. AlternateFilters.ElementAt(y). ComparisonOperator: ComparisonOperator.Equals
  * parâmetros. AlternateFilter.ElementAt(y). ComparisonValue: "2819c223-7f76-453a-919d-413861904646"
  * parâmetros. RequestedAttributePaths.ElementAt(0): "id"
  * parâmetros. SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

  Aqui, o valor do índice x pode ser 0 e o valor de y o índice pode ser 1, ou o valor x, poderá ser 1 e o valor de y pode ser 0, consoante a ordem das expressões de parâmetro de consulta de filtro.   

5. Eis um exemplo de um pedido do Azure Active Directory para um serviço SCIM para atualizar um utilizador: 
  ````
    PATCH ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
    Content-type: application/json
    {
      "schemas": 
      [
        "urn:ietf:params:scim:api:messages:2.0:PatchOp"],
      "Operations":
      [
        {
          "op":"Add",
          "path":"manager",
          "value":
            [
              {
                "$ref":"http://.../scim/Users/2819c223-7f76-453a-919d-413861904646",
                "value":"2819c223-7f76-453a-919d-413861904646"}]}]}
  ````
  As bibliotecas de infraestrutura de idioma comum da Microsoft para implementar serviços SCIM seriam implica uma chamada para o método de atualização do fornecedor do serviço pedido. Segue-se a assinatura do método de atualização: 
  ````
    // System.Threading.Tasks.Tasks and 
    // System.Collections.Generic.IReadOnlyCollection<T>
    // are defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IPatch, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation, 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationName, 
    // Microsoft.SystemForCrossDomainIdentityManagement.IPath and 
    // Microsoft.SystemForCrossDomainIdentityManagement.OperationValue 
    // are all defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 

    System.Threading.Tasks.Task Update(
      Microsoft.SystemForCrossDomainIdentityManagement.IPatch patch, 
      string correlationIdentifier);

    public interface Microsoft.SystemForCrossDomainIdentityManagement.IPatch
    {
    Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase 
      PatchRequest 
        { get; set; }
    Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier 
      ResourceIdentifier 
        { get; set; }        
    }

    public class PatchRequest2: 
      Microsoft.SystemForCrossDomainIdentityManagement.PatchRequestBase
    {
    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation> 
        Operations
        { get;}

    public void AddOperation(
      Microsoft.SystemForCrossDomainIdentityManagement.PatchOperation operation);
    }

    public class PatchOperation
    {
    public Microsoft.SystemForCrossDomainIdentityManagement.OperationName 
      Name
      { get; set; }

    public Microsoft.SystemForCrossDomainIdentityManagement.IPath 
      Path
      { get; set; }

    public System.Collections.Generic.IReadOnlyCollection
      <Microsoft.SystemForCrossDomainIdentityManagement.OperationValue> Value
      { get; }

    public void AddValue(
      Microsoft.SystemForCrossDomainIdentityManagement.OperationValue value);
    }

    public enum OperationName
    {
      Add,
      Remove,
      Replace
    }

    public interface IPath
    {
      string AttributePath { get; }
      System.Collections.Generic.IReadOnlyCollection<IFilter> SubAttributes { get; }
      Microsoft.SystemForCrossDomainIdentityManagement.IPath ValuePath { get; }
    }

    public class OperationValue
    {
      public string Reference
      { get; set; }

      public string Value
      { get; set; }
    }
  ````
    No exemplo de um pedido para atualizar um utilizador, o objeto fornecido como o valor do argumento patch tem estes valores de propriedade: 
  
  * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"
  * (PatchRequest como PatchRequest2). Operations.Count: 1
  * (PatchRequest como PatchRequest2). Operations.ElementAt(0). OperationName: OperationName.Add
  * (PatchRequest como PatchRequest2). Operations.ElementAt(0). Path.AttributePath: "Gestor"
  * (PatchRequest como PatchRequest2). Operations.ElementAt(0). Value.Count: 1
  * (PatchRequest como PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Referência: http://.../scim/Users/2819c223-7f76-453a-919d-413861904646
  * (PatchRequest como PatchRequest2). Operations.ElementAt(0). Value.ElementAt(0). Valor: 2819c223-7f76-453a-919d-413861904646

6. Para anular o aprovisionamento um utilizador a partir de um arquivo de identidade fronted por um serviço SCIM, do Azure AD envia um pedido, tal como: 
  ````
    DELETE ~/scim/Users/54D382A4-2050-4C03-94D1-E769F1D15682 HTTP/1.1
    Authorization: Bearer ...
  ````
  Se o serviço foi criado com as bibliotecas de infraestrutura de idioma comum fornecidas pela Microsoft para implementar serviços SCIM, em seguida, o pedido é convertido uma chamada para o método de eliminação do fornecedor do serviço.   Este método tem esta assinatura: 
  ````
    // System.Threading.Tasks.Tasks is defined in mscorlib.dll.  
    // Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier, 
    // is defined in Microsoft.SystemForCrossDomainIdentityManagement.Protocol. 
    System.Threading.Tasks.Task Delete(
      Microsoft.SystemForCrossDomainIdentityManagement.IResourceIdentifier  
        resourceIdentifier, 
      string correlationIdentifier);
  ````
  O objecto fornecido como o valor do argumento resourceIdentifier tem estes valores de propriedade no exemplo de um pedido para anular o aprovisionamento um utilizador: 
  
  * ResourceIdentifier.Identifier: "54D382A4-2050-4C03-94D1-E769F1D15682"
  * ResourceIdentifier.SchemaIdentifier: "urn: ietf:params:scim:schemas:extension:enterprise:2.0:User"

## <a name="group-provisioning-and-de-provisioning"></a>Aprovisionamento de grupo e de aprovisionamento automatizados.
A ilustração seguinte mostra as mensagens que Azure AcD envia a um serviço SCIM para gerir o ciclo de vida de um grupo num arquivo de identidade do outro.  Essas mensagens diferem das mensagens relativas aos utilizadores de três formas diferentes: 

* O esquema de um recurso de grupo é identificado como http://schemas.microsoft.com/2006/11/ResourceManagement/ADSCIM/Group.  
* Pedidos para obter grupos de definir a que o atributo de membros é a serem excluídos qualquer recurso fornecido em resposta ao pedido.  
* Pedidos para determinar se um atributo de referência tem um determinado valor são pedidos sobre o atributo de membros.  

![][5]
*Figura 6: Aprovisionamento de grupo e a sequência de aprovisionamento automatizados.*

## <a name="related-articles"></a>Artigos relacionados
* [Índice de Artigos da Gestão da Aplicação no Azure Active Directory](active-directory-apps-index.md)
* [Automatizar utilizador aprovisionamento/desaprovisionamento para aplicações SaaS](active-directory-saas-app-provisioning.md)
* [Personalizar os mapeamentos de atributos para o aprovisionamento de utilizador](active-directory-saas-customizing-attribute-mappings.md)
* [Escrever expressões para mapeamentos de atributos](active-directory-saas-writing-expressions-for-attribute-mappings.md)
* [Filtros de âmbito para o aprovisionamento de utilizador](active-directory-saas-scoping-filters.md)
* [Notificações de aprovisionamento de contas](active-directory-saas-account-provisioning-notifications.md)
* [Lista de tutoriais sobre como integrar aplicações SaaS](active-directory-saas-tutorial-list.md)

<!--Image references-->
[0]: ./media/active-directory-scim-provisioning/scim-figure-1.PNG
[1]: ./media/active-directory-scim-provisioning/scim-figure-2a.PNG
[2]: ./media/active-directory-scim-provisioning/scim-figure-2b.PNG
[3]: ./media/active-directory-scim-provisioning/scim-figure-3.PNG
[4]: ./media/active-directory-scim-provisioning/scim-figure-4.PNG
[5]: ./media/active-directory-scim-provisioning/scim-figure-5.PNG
