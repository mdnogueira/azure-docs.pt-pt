---
title: "Azure de gestão - ferramenta de modelação de ameaça Microsoft - exceção | Microsoft Docs"
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
ms.openlocfilehash: bbf357b902474a1812eb7a5a2c914d0c8b91934b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="security-frame-exception-management--mitigations"></a>Moldura de segurança: Gestão de exceções | Mitigações 
| Produtos/serviços | Artigo |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF - não inclua serviceDebug nó no ficheiro de configuração](#servicedebug)</li><li>[WCF - não inclua serviceMetadata nó no ficheiro de configuração](#servicemetadata)</li></ul> |
| **API Web** | <ul><li>[Certifique-se de que o processamento de exceções adequada é efetuado na API Web do ASP.NET](#exception)</li></ul> |
| **Aplicação Web** | <ul><li>[Expõe detalhes de segurança em mensagens de erro](#messages)</li><li>[Implementar a página de processamento de erros de predefinido](#default)</li><li>[Definir o método de implementação de revenda no IIS](#deployment)</li><li>[Exceções se falhar de forma segura](#fail)</li></ul> |

## <a id="servicedebug"></a>WCF - não inclua serviceDebug nó no ficheiro de configuração

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, o NET Framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Unido](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Passos** | Serviços de comunicação Framework WCF (Windows) podem ser configurados para expor as informações de depuração. Depurar informações não devem ser utilizadas em ambientes de produção. O `<serviceDebug>` tag define se a funcionalidade de informações de depuração está ativada para um serviço WCF. Se o atributo includeExceptionDetailInFaults está definido como VERDADEIRO, informações de exceção da aplicação será devolvido a clientes. Os atacantes podem tirar partido as informações adicionais ganham de depuração de saída para montar ataques direcionados no framework, a base de dados ou a outros recursos utilizados pela aplicação. |

### <a name="example"></a>Exemplo
O seguinte ficheiro de configuração inclui o `<serviceDebug>` etiquetas: 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
Desative as informações de depuração no serviço. Isto pode ser conseguido através da remoção de `<serviceDebug>` etiquetas do ficheiro de configuração da sua aplicação. 

## <a id="servicemetadata"></a>WCF - não inclua serviceMetadata nó no ficheiro de configuração

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | Genérico, o NET Framework 3 |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Fortify Unido](https://vulncat.fortify.com/en/vulncat/index.html) |
| **Passos** | Informações sobre um serviço a expor publicamente pode fornecer aos atacantes aprofundadas importantes como podem explorar o serviço. O `<serviceMetadata>` tag ativa a funcionalidade de publicação de metadados. Metadados do serviço podem conter informações confidenciais que não devem estar acessíveis publicamente. No mínimo, permita apenas utilizadores fidedignos aceder o metadados e certifique-se de que não estão expostas informações desnecessárias. Melhor ainda, desative completamente a capacidade de publicar metadados. Uma configuração de WCF segura não irá conter o `<serviceMetadata>` etiquetas. |

## <a id="exception"></a>Certifique-se de que o processamento de exceções adequada é efetuado na API Web do ASP.NET

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC 5, 6 DE MVC |
| **Atributos**              | N/D  |
| **Referências**              | [Excepção a processar a API Web do ASP.NET](http://www.asp.net/web-api/overview/error-handling/exception-handling), [modelo validação na API Web do ASP.NET](http://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Passos** | Por predefinição, os mais exceções não identificadas na API Web do ASP.NET estão convertidas uma resposta HTTP com o código de estado`500, Internal Server Error`|

### <a name="example"></a>Exemplo
Para controlar o código de estado devolvido pela API, `HttpResponseException` podem ser utilizados como mostrado abaixo: 
```C#
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        throw new HttpResponseException(HttpStatusCode.NotFound);
    }
    return item;
}
```

### <a name="example"></a>Exemplo
Para mais controlo sobre a resposta de exceção, o `HttpResponseMessage` classe pode ser utilizada, conforme mostrado abaixo: 
```C#
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        var resp = new HttpResponseMessage(HttpStatusCode.NotFound)
        {
            Content = new StringContent(string.Format("No product with ID = {0}", id)),
            ReasonPhrase = "Product ID Not Found"
        }
        throw new HttpResponseException(resp);
    }
    return item;
}
```
Para detetar exceções não processadas que não sejam do tipo `HttpResponseException`, podem ser utilizados filtros de excepção. Filtros de excepção implementam o `System.Web.Http.Filters.IExceptionFilter` interface. A forma mais simples para escrever um filtro de excepções é derivar a `System.Web.Http.Filters.ExceptionFilterAttribute` classe e substitua o método OnException. 

### <a name="example"></a>Exemplo
Eis um filtro que converte `NotImplementedException` exceções para o código de estado HTTP `501, Not Implemented`: 
```C#
namespace ProductStore.Filters
{
    using System;
    using System.Net;
    using System.Net.Http;
    using System.Web.Http.Filters;

    public class NotImplExceptionFilterAttribute : ExceptionFilterAttribute 
    {
        public override void OnException(HttpActionExecutedContext context)
        {
            if (context.Exception is NotImplementedException)
            {
                context.Response = new HttpResponseMessage(HttpStatusCode.NotImplemented);
            }
        }
    }
}
```

Existem várias formas para registar um filtro de excepções de Web API:
- Por ação
- Pelo controlador
- Global

### <a name="example"></a>Exemplo
Para aplicar o filtro a uma ação específica, adicione o filtro como um atributo para a ação: 
```C#
public class ProductsController : ApiController
{
    [NotImplExceptionFilter]
    public Contact GetContact(int id)
    {
        throw new NotImplementedException("This method is not implemented");
    }
}
```
### <a name="example"></a>Exemplo
Para aplicar o filtro para todas as ações num `controller`, adicionar o filtro como um atributo para o `controller` classe: 

```C#
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>Exemplo
Para aplicar o filtro global a todos os controladores de Web API, adicione uma instância do filtro para o `GlobalConfiguration.Configuration.Filters` coleção. Aplicam os filtros de excepção nesta coleção para qualquer ação do controlador de Web API. 
```C#
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>Exemplo
Para a validação do modelo, o estado de modelo pode ser transmitido ao método CreateErrorResponse conforme mostrado abaixo: 
```C#
public HttpResponseMessage PostProduct(Product item)
{
    if (!ModelState.IsValid)
    {
        return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
    }
    // Implementation not shown...
}
```

Verifique as ligações na secção de referências para obter detalhes adicionais sobre o processamento excecional e a validação do modelo na API Web do ASP.Net 

## <a id="messages"></a>Expõe detalhes de segurança em mensagens de erro

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Mensagens de erro genérica são fornecidas diretamente para o utilizador sem incluir dados confidenciais de aplicações. Exemplos de dados confidenciais incluem:</p><ul><li>Nomes de servidor</li><li>Cadeias de ligação</li><li>Nomes de utilizador</li><li>Palavras-passe</li><li>Procedimentos de SQL</li><li>Detalhes de falhas SQL dinâmicos</li><li>O rastreio da pilha e linhas de código</li><li>Variáveis armazenadas na memória</li><li>Localizações de pasta ou unidade</li><li>Pontos de instalação da aplicação</li><li>Definições de configuração de anfitrião</li><li>Outros detalhes de aplicação interna</li></ul><p>Trapping todos os erros numa aplicação e fornecer as mensagens de erro genérica, bem como ativar erros personalizados no IIS irá ajudar a impedir a divulgação de informações. Base de dados do SQL Server e exceções de .NET processar, entre outros arquiteturas de processamento de erros são especialmente verboso e extremamente úteis para um utilizador mal intencionado a aplicação de criação de perfis. Não diretamente apresentar o conteúdo de uma classe derivada da classe de exceção de .NET e certifique-se de que tem o processamento de exceções adequado, para que uma exceção inesperada inadvertidamente não está a ser gerada diretamente para o utilizador.</p><ul><li>Forneça as mensagens de erro genérica diretamente ao utilizador que abstracta away detalhes específicos encontrados diretamente na mensagem de exceção/erro</li><li>Não apresentar o conteúdo de uma classe de exceção de .NET diretamente para o utilizador</li><li>Trap todas as mensagens de erro e se adequado informar o utilizador através de uma mensagem de erro genérica enviado para o cliente da aplicação</li><li>Expõe o conteúdo da classe de exceção diretamente para o utilizador, especialmente, o valor devolvido de `.ToString()`, ou os valores das propriedades da mensagem ou o rastreio da pilha. Estas informações de registo e apresentar uma mensagem mais innocuous ao utilizador em segurança</li></ul>|

## <a id="default"></a>Implementar a página de processamento de erros de predefinido

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Editar a caixa de diálogo de definições de páginas de erro.NET](https://technet.microsoft.com/library/dd569096(WS.10).aspx) |
| **Passos** | <p>Quando uma aplicação ASP.NET falha e faz com que um HTTP/1.x 500 Erro interno do servidor ou uma configuração de funcionalidades (tais como a filtragem de pedidos) impede que uma página que está a ser apresentada, será gerada uma mensagem de erro. Os administradores podem optar se ou não a aplicação deve apresentar uma mensagem amigável para o cliente, a mensagem de erro detalhada para o cliente ou a mensagem de erro detalhada para o localhost apenas. O <customErrors> tag na Web. config tem três modos de:</p><ul><li>**No:** Especifica que erros personalizados estão ativados. Não se for especificado nenhum atributo defaultRedirect, os utilizadores veem um erro genérico. Os erros personalizados são mostrados a clientes remotos e ao anfitrião local</li><li>**Desativar:** Especifica que erros personalizados estão desativados. Os erros ASP.NET detalhados são apresentados para os clientes remotos e ao anfitrião local</li><li>**RemoteOnly:** Especifica que erros personalizados são apresentados apenas aos clientes remotos e que o ASP.NET erros são apresentados ao anfitrião local. Este é o valor predefinido</li></ul><p>Abra o `web.config` de ficheiros para o site/aplicações e certifique-se de que a etiqueta tem um `<customErrors mode="RemoteOnly" />` ou `<customErrors mode="On" />` definido.</p>|

## <a id="deployment"></a>Definir o método de implementação de revenda no IIS

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Implementação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [implementação de elemento (esquema de definições do ASP.NET)](https://msdn.microsoft.com/library/ms228298(VS.80).aspx) |
| **Passos** | <p>O `<deployment retail>` comutador destina-se pelos servidores IIS de produção. Este parâmetro é utilizado para ajudar as aplicações são executadas com o menor número possíveis leakages de informações de segurança e melhor desempenho possível através da desativação de capacidade da aplicação para gerar a saída do rastreio numa página, desativar a capacidade para apresentar mensagens de erro detalhadas para os utilizadores finais e desativar o comutador de depuração.</p><p>Muitas vezes, comutadores e as opções centrados na programação, como falha solicitar o rastreio e depuração, são ativadas durante o desenvolvimento de Active Directory. Recomenda-se que o método de implementação em qualquer servidor de produção ser definido como revenda. Abra o ficheiro Machine. config e certifique-se de que `<deployment retail="true" />` permaneça definido como verdadeiro.</p>|

## <a id="fail"></a>Exceções se falhar de forma segura

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Falhar de forma segura](https://www.owasp.org/index.php/Fail_securely) |
| **Passos** | Aplicação se falhar de forma segura. Um método que devolva um valor booleano, com base no que determinados decisão é efetuada, deve ter bloco exception cuidadosamente criado. Existem inúmeras lógicas erros devido à qual creep no, quando o bloco de exceção é escrito carelessly de problemas de segurança.|

### <a name="example"></a>Exemplo
```C#
        public static bool ValidateDomain(string pathToValidate, Uri currentUrl)
        {
            try
            {
                if (!string.IsNullOrWhiteSpace(pathToValidate))
                {
                    var domain = RetrieveDomain(currentUrl);
                    var replyPath = new Uri(pathToValidate);
                    var replyDomain = RetrieveDomain(replyPath);

                    if (string.Compare(domain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                    {
                        //// Adding additional check to enable CMS urls if they are not hosted on same domain.
                        if (!string.IsNullOrWhiteSpace(Utilities.CmsBase))
                        {
                            var cmsDomain = RetrieveDomain(new Uri(Utilities.Base.Trim()));
                            if (string.Compare(cmDomain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                            {
                                return false;
                            }
                            else
                            {
                                return true;
                            }
                        }

                        return false;
                    }
                }

                return true;
            }
            catch (UriFormatException ex)
            {
                LogHelper.LogException("Utilities:ValidateDomain", ex);
                return true;
            }
        }
```
O método acima sempre irá devolver True, se acontecer algum exceção. Se o utilizador final fornece um URL incorreto, que respeita o browser, mas o `Uri()` não construtor, isto irá gerar uma exceção e vítima será direcionado para o URL válido, mas com formato incorreto. 