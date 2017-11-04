---
title: "Entrada de validação - ferramenta de modelação de ameaça Microsoft - Azure | Microsoft Docs"
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
ms.openlocfilehash: b7ce6f353cf8cf48d5fb038ee77b0d3fdae16fb7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2017
---
# <a name="security-frame-input-validation--mitigations"></a>Moldura de segurança: Validação de entrada | Mitigações 
| Produtos/serviços | Artigo |
| --------------- | ------- |
| **Aplicação Web** | <ul><li>[Desativar o XSLT scripting para todas as transformações utilizando folhas de estilo não fidedignos](#disable-xslt)</li><li>[Certifique-se de que cada página que pode conter conteúdo controllable utilizador evita interceção de MIME automática](#out-sniffing)</li><li>[Proteger ou desativar a resolução de entidade XML](#xml-resolution)</li><li>[As aplicações que a utilização de http.sys efetuar verificação de colocação em forma canónica de URL](#app-verification)</li><li>[Certifique-se de que os controlos adequados estão em vigor quando os ficheiros dos utilizadores a aceitar](#controls-users)</li><li>[Certifique-se de que os parâmetros de tipo seguro são utilizados na aplicação Web para acesso a dados](#typesafe)</li><li>[Utilize as classes de enlace do modelo separadas ou apresenta uma lista de filtro de enlace para impedir a vulnerabilidade de atribuição em massa do MVC](#binding-mvc)</li><li>[Codificar o resultado da web não fidedignos antes da composição](#rendering)</li><li>[Efetuar a validação de entrada e filtrar no tipo de cadeia todas as propriedades de modelo](#typemodel)</li><li>[Sanitização deve ser aplicada em campos de formulário que aceitam todos os carateres, por exemplo, o editor de texto formatado](#richtext)</li><li>[Não atribua elementos DOM para sinks que não têm a codificação integradas](#inbuilt-encode)</li><li>[Validar se todos os redireccionamentos na aplicação são fechados ou feitos de forma segura](#redirect-safe)</li><li>[Implementar a validação de entrada em todos os parâmetros de tipo de cadeia aceites pelos métodos de controlador](#string-method)</li><li>[Definir limite de tempo limite superior para a expressão regular de processamento para evitar DoS devido a expressões regulares incorretas](#dos-expression)</li><li>[Evite utilizar Html.Raw nas vistas de Razor](#html-razor)</li></ul> | 
| **Base de Dados** | <ul><li>[Não utilize consultas dinâmicas em procedimentos armazenados](#stored-proc)</li></ul> |
| **API Web** | <ul><li>[Certifique-se de que a validação do modelo é efetuada em métodos de Web API](#validation-api)</li><li>[Implementar a validação de entrada em todos os parâmetros de tipo de cadeia aceites pelos métodos de Web API](#string-api)</li><li>[Certifique-se de que os parâmetros de tipo seguro são utilizados na Web API para acesso a dados](#typesafe-api)</li></ul> | 
| **Base de dados de documento do Azure** | <ul><li>[Utilizar consultas SQL parametrized para o DocumentDB](#sql-docdb)</li></ul> | 
| **WCF** | <ul><li>[Validação de entrada de WCF através de vinculação ao esquema](#schema-binding)</li><li>[Validação de WCF - entrada através do parâmetro Inspetores](#parameters)</li></ul> |

## <a id="disable-xslt"></a>Desativar o XSLT scripting para todas as transformações utilizando folhas de estilo não fidedignos

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Segurança XSLT](https://msdn.microsoft.com/library/ms763800(v=vs.85).aspx), [propriedade XsltSettings](http://msdn.microsoft.com/library/system.xml.xsl.xsltsettings.enablescript.aspx) |
| **Passos** | Suporta o XSLT scripting dentro de folhas de estilo utilizando o `<msxml:script>` elemento. Isto permite que as funções personalizadas ser utilizada numa transformação XSLT. O script é executado no contexto do processo de efetuar a transformação. Script XSLT tem de ser desativada quando num ambiente não fidedigno para impedir a execução do código não fidedigno. *Se utilizar o .NET:* XSLT scripting está desativada por predefinição; no entanto, tem de garantir que este não foi explicitamente ativado através de `XsltSettings.EnableScript` propriedade.|

### <a name="example"></a>Exemplo 

```C#
XsltSettings settings = new XsltSettings();
settings.EnableScript = true; // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Exemplo
Se estiver a utilizar com o MSXML 6.0, XSLT scripting está desativada por predefinição; No entanto, tem de se certificar de que este não foi explicitamente ativado através da propriedade do objeto XML DOM AllowXsltScript. 

```C#
doc.setProperty("AllowXsltScript", true); // WRONG: THIS SHOULD BE SET TO false
```

### <a name="example"></a>Exemplo
Se estiver a utilizar o MSXML 5 ou abaixo, XSLT de scripts está ativada por predefinição e tem explicitamente desativá-lo. Defina a propriedade do objeto XML DOM AllowXsltScript como false. 

```C#
doc.setProperty("AllowXsltScript", false); // CORRECT. Setting to false disables XSLT scripting.
```

## <a id="out-sniffing"></a>Certifique-se de que cada página que pode conter conteúdo controllable utilizador evita interceção de MIME automática

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [IE8 V de parte de segurança - proteção completa](http://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx)  |
| **Passos** | <p>Para cada página que possam conter conteúdo controllable do utilizador, tem de utilizar o cabeçalho HTTP `X-Content-Type-Options:nosniff`. Para cumprir este requisito, ou pode definir o cabeçalho necessário página por página para apenas essas páginas que podem conter conteúdo de utilizador controllable ou pode configurá-lo global para todas as páginas na aplicação.</p><p>Cada tipo de ficheiro fornecido de um servidor web tem um associados [tipo de MIME](http://en.wikipedia.org/wiki/Mime_type) (também designado por um *tipo de conteúdo*) que descreve a natureza do conteúdo (ou seja, imagem, texto, aplicação, etc.)</p><p>O cabeçalho X-conteúdo--opções do tipo é um cabeçalho de HTTP que permite aos programadores especificar que o conteúdo não deve ser intercetados na MIME. Este cabeçalho foi concebido para mitigar ataques de Interceção de MIME. Foi adicionado suporte para este cabeçalho no Internet Explorer 8 (IE8)</p><p>Apenas os utilizadores do Internet Explorer 8 (IE8) irão beneficiar X conteúdo-tipo opções. Versões anteriores do Internet Explorer não respeitem atualmente o cabeçalho X-conteúdo--opções do tipo</p><p>Internet Explorer 8 (e versões posterior) são os browsers principais apenas para implementar uma funcionalidade de ativamente MIME de interceção. Se e quando outros browsers principais (Firefox, Safari, Chrome) implementam semelhantes funcionalidades, esta recomendação irá ser atualizada para incluir sintaxe para esses browsers bem</p>|

### <a name="example"></a>Exemplo
Para ativar o cabeçalho necessário global para todas as páginas na aplicação, pode efetuar um dos seguintes: 

* Adicionar o cabeçalho no ficheiro Web. config, se a aplicação for alojada pelos serviços de informação Internet (IIS) 7 

```
<system.webServer> 
  <httpProtocol> 
    <customHeaders> 
      <add name=""X-Content-Type-Options"" value=""nosniff""/>
    </customHeaders>
  </httpProtocol>
</system.webServer> 
```

* Adicionar cabeçalho através da aplicação global\_BeginRequest 

``` 
void Application_BeginRequest(object sender, EventArgs e)
{
  this.Response.Headers[""X-Content-Type-Options""] = ""nosniff"";
} 
```

* Módulo HTTP personalizado implementar 

``` 
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
        if (application.Response.Headers[""X-Content-Type-Options ""] != null) 
          return; 
        application.Response.Headers.Add(""X-Content-Type-Options "", ""nosniff""); 
      } 
  } 

``` 

* Pode ativar o cabeçalho necessário apenas para páginas específicas, adicionando-a respostas individuais: 

```
this.Response.Headers[""X-Content-Type-Options""] = ""nosniff""; 
``` 

## <a id="xml-resolution"></a>Proteger ou desativar a resolução de entidade XML

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Expansão de entidade XML](http://capec.mitre.org/data/definitions/197.html), [ataques XML recusa de serviço e defesas](http://msdn.microsoft.com/magazine/ee335713.aspx), [descrição geral de segurança do MSXML](http://msdn.microsoft.com/library/ms754611(v=VS.85).aspx), [melhores práticas para proteger o MSXML código](http://msdn.microsoft.com/library/ms759188(VS.85).aspx), [ Referência do protocolo NSXMLParserDelegate](http://developer.apple.com/library/ios/#documentation/cocoa/reference/NSXMLParserDelegate_Protocol/Reference/Reference.html), [resolver referências externas](https://msdn.microsoft.com/library/5fcwybb2.aspx) |
| **Passos**| <p>Apesar de não é amplamente utilizado, há uma funcionalidade de XML, que permite que o analisador de XML expandir entidades de macro com valores definidos no documento em si ou a partir de origens externas. Por exemplo, o documento poderá definir uma entidade "companyname" com o valor "Microsoft", por isso que sempre que o texto "&companyname;" é apresentado no documento, é automaticamente substituído com o texto da Microsoft. Em alternativa, o documento poderá definir uma entidade "MSFTStock" que faça referência a um serviço web externo ao obter o valor atual do gráfico de cotações de Microsoft.</p><p>Em seguida, em qualquer altura "&MSFTStock;" é apresentado no documento, é automaticamente substituído com o preço as cotações atual. No entanto, esta funcionalidade pode ser abused criar recusa dos termos de serviço (DoS). Um atacante pode ser aninhado várias entidades para criar um bomb XML exponencial expansão que consome toda a memória disponível no sistema. </p><p>Em alternativa, ele pode criar uma referência externa que fluxos back uma infinita quantidade de dados ou que bloqueia simplesmente o thread. Como resultado, todas as equipas tem de desativar resolução de entidade XML interna e/ou externa completamente se as respetivas aplicações não utilizá-lo, ou manualmente limitar a quantidade de memória e a hora em que a aplicação pode consumir para a resolução de entidade se esta funcionalidade é absolutamente necessário. Se a resolução de entidade não é exigida pela sua aplicação, em seguida, desative-o. </p>|

### <a name="example"></a>Exemplo
Código do .NET Framework, pode utilizar as seguintes abordagens:

```C#
XmlTextReader reader = new XmlTextReader(stream);
reader.ProhibitDtd = true;

XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);

// for .NET 4
XmlReaderSettings settings = new XmlReaderSettings();
settings.DtdProcessing = DtdProcessing.Prohibit;
XmlReader reader = XmlReader.Create(stream, settings);
```
Tenha em atenção que o valor predefinido de `ProhibitDtd` no `XmlReaderSettings` for VERDADEIRO, mas em `XmlTextReader` é falso. Se estiver a utilizar XmlReaderSettings, não é necessário definir ProhibitDtd como verdadeiro explicitamente, mas é recomendado para sake de segurança que efetuar. Note também que a classe de XmlDocument permite a resolução da entidade por predefinição. 

### <a name="example"></a>Exemplo
Para desativar a resolução de entidades para XmlDocuments, utilize o `XmlDocument.Load(XmlReader)` sobrecarga do método de carregamento e defina as propriedades adequadas no argumento de XmlReader para desativar a resolução, conforme ilustrado no seguinte código: 

```C#
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = true;
XmlReader reader = XmlReader.Create(stream, settings);
XmlDocument doc = new XmlDocument();
doc.Load(reader);
```

### <a name="example"></a>Exemplo
Se desativar a resolução de entidade não é possível para a sua aplicação, defina a propriedade de XmlReaderSettings.MaxCharactersFromEntities para um valor razoável de acordo com as necessidades da sua aplicação. Isto irá limitar o impacto de prevenção de expansão exponencial potenciais ataques. O código seguinte fornece um exemplo desta abordagem: 

```C#
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
XmlReader reader = XmlReader.Create(stream, settings);
```

### <a name="example"></a>Exemplo
Se precisar de resolver inline entidades, mas fazer não é necessário resolver as entidades externas, defina a propriedade de XmlReaderSettings.XmlResolver como nulo. Por exemplo: 

```C#
XmlReaderSettings settings = new XmlReaderSettings();
settings.ProhibitDtd = false;
settings.MaxCharactersFromEntities = 1000;
settings.XmlResolver = null;
XmlReader reader = XmlReader.Create(stream, settings);
```
Tenha em atenção que o MSXML6, ProhibitDTD é definido como VERDADEIRO (desativar o processamento DTD) por predefinição. Código de OSX/iOS da Apple, existem dois parsers de XML, pode utilizar: NSXMLParser e libXML2. 

## <a id="app-verification"></a>As aplicações que a utilização de http.sys efetuar verificação de colocação em forma canónica de URL

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Qualquer aplicação que utiliza o http.sys deve seguir estas diretrizes:</p><ul><li>Limita o comprimento do URL a mais do que 16.384 carateres (ASCII ou Unicode). Este é o absoluto comprimento máximo do URL com base na predefinição de serviços de informação Internet (IIS) 6. Web sites devem esforçar-por um período mais curto que estes se possível</li><li>Utilizar as classes de ficheiro e/s padrão do .NET Framework (por exemplo, FileStream) como estes serão tirar partido das regras de colocação em forma canónica do FX .NET</li><li>Explicitamente compilar uma lista de permissões de nomes de ficheiros conhecida</li><li>Rejeitar explicitamente filetypes conhecidos que não processará UrlScan rejeita: exe, bat, cmd, com, htw, ida, idq, htr, idc, shtm [l], stm, impressoras, ini, pol, ficheiros de dados</li><li>Detetar as seguintes exceções:<ul><li>System.ArgumentException (para nomes de dispositivo)</li><li>System.NotSupportedException (para fluxos de dados)</li><li>System.IO.FileNotFoundException (para nomes de ficheiros com caráter de escape inválida)</li><li>System.IO.DirectoryNotFoundException (para dirs com caráter de escape inválida)</li></ul></li><li>*Não* chamar ficheiro Win32 APIs e/s. Num URL inválido transições devolver um erro 400 ao utilizador e inicie o erro real.</li></ul>|

## <a id="controls-users"></a>Certifique-se de que os controlos adequados estão em vigor quando os ficheiros dos utilizadores a aceitar

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Sem restrições de carregamento de ficheiros](https://www.owasp.org/index.php/Unrestricted_File_Upload), [tabela de assinatura de ficheiros](http://www.garykessler.net/library/file_sigs.html) |
| **Passos** | <p>Os ficheiros carregados representam um risco significativo para as aplicações.</p><p>O primeiro passo na muitos ataques é obter alguns códigos para o sistema a ser atacado. Em seguida, ataque apenas tem de encontrar uma forma de obter o código executado. Utilizar um carregamento de ficheiros ajuda-o atacante realizar o primeiro passo. As consequências de carregamento de ficheiros sem restrições podem variar, incluindo aquisições sistema completo, um sistema de ficheiros sobrecarregado ou a base de dados, ataques de sistemas de back-end e simple defacement de reencaminhamento.</p><p>Depende do que a aplicação faz com que o ficheiro carregado e especialmente onde está armazenada. Validação do lado do servidor de carregamentos de ficheiros está em falta. Os seguintes controlos de segurança devem ser implementados para carregar ficheiros de funcionalidade:</p><ul><li>Verificação de extensão de ficheiro (apenas um conjunto de tipo de ficheiro permitido válido deve ser aceite)</li><li>Limite de tamanho máximo do ficheiro</li><li>Ficheiro não deve ser carregado para webroot; a localização deve ser um diretório na unidade de sistema não</li><li>Convenção de nomenclatura deve ser seguido, substitui de forma a que o nome de ficheiro carregado tem algumas randomness, de modo a impedir que o ficheiro</li><li>Devem ser analisados ficheiros antivírus antes de escrever para o disco</li><li>Certifique-se de que o nome de ficheiro e de quaisquer outros metadados (por exemplo, o caminho de ficheiro) são validados caracteres maliciosos</li><li>Assinatura de formato de ficheiro deve ser verificada para impedir que um utilizador carregar um ficheiro masqueraded (por exemplo, carregar um ficheiro. exe alterando a extensão para txt)</li></ul>| 

### <a name="example"></a>Exemplo
Para a criação do última ponto relativas a validação de assinatura do formato de ficheiro, consulte a classe abaixo para obter mais detalhes: 

```C#
        private static Dictionary<string, List<byte[]>> fileSignature = new Dictionary<string, List<byte[]>>
                    {
                    { ".DOC", new List<byte[]> { new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 } } },
                    { ".DOCX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".PDF", new List<byte[]> { new byte[] { 0x25, 0x50, 0x44, 0x46 } } },
                    { ".ZIP", new List<byte[]> 
                                            {
                                              new byte[] { 0x50, 0x4B, 0x03, 0x04 },
                                              new byte[] { 0x50, 0x4B, 0x4C, 0x49, 0x54, 0x55 },
                                              new byte[] { 0x50, 0x4B, 0x53, 0x70, 0x58 },
                                              new byte[] { 0x50, 0x4B, 0x05, 0x06 },
                                              new byte[] { 0x50, 0x4B, 0x07, 0x08 },
                                              new byte[] { 0x57, 0x69, 0x6E, 0x5A, 0x69, 0x70 }
                                                }
                                            },
                    { ".PNG", new List<byte[]> { new byte[] { 0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A } } },
                    { ".JPG", new List<byte[]>
                                    {
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE1 },
                                              new byte[] { 0xFF, 0xD8, 0xFF, 0xE8 }
                                    }
                                    },
                    { ".JPEG", new List<byte[]>
                                        { 
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE2 },
                                            new byte[] { 0xFF, 0xD8, 0xFF, 0xE3 }
                                        }
                                        },
                    { ".XLS", new List<byte[]>
                                            {
                                              new byte[] { 0xD0, 0xCF, 0x11, 0xE0, 0xA1, 0xB1, 0x1A, 0xE1 },
                                              new byte[] { 0x09, 0x08, 0x10, 0x00, 0x00, 0x06, 0x05, 0x00 },
                                              new byte[] { 0xFD, 0xFF, 0xFF, 0xFF }
                                            }
                                            },
                    { ".XLSX", new List<byte[]> { new byte[] { 0x50, 0x4B, 0x03, 0x04 } } },
                    { ".GIF", new List<byte[]> { new byte[] { 0x47, 0x49, 0x46, 0x38 } } }
                };

        public static bool IsValidFileExtension(string fileName, byte[] fileData, byte[] allowedChars)
        {
            if (string.IsNullOrEmpty(fileName) || fileData == null || fileData.Length == 0)
            {
                return false;
            }

            bool flag = false;
            string ext = Path.GetExtension(fileName);
            if (string.IsNullOrEmpty(ext))
            {
                return false;
            }

            ext = ext.ToUpperInvariant();

            if (ext.Equals(".TXT") || ext.Equals(".CSV") || ext.Equals(".PRN"))
            {
                foreach (byte b in fileData)
                {
                    if (b > 0x7F)
                    {
                        if (allowedChars != null)
                        {
                            if (!allowedChars.Contains(b))
                            {
                                return false;
                            }
                        }
                        else
                        {
                            return false;
                        }
                    }
                }

                return true;
            }

            if (!fileSignature.ContainsKey(ext))
            {
                return true;
            }

            List<byte[]> sig = fileSignature[ext];
            foreach (byte[] b in sig)
            {
                var curFileSig = new byte[b.Length];
                Array.Copy(fileData, curFileSig, b.Length);
                if (curFileSig.SequenceEqual(b))
                {
                    flag = true;
                    break;
                }
            }

            return flag;
        }
```

## <a id="typesafe"></a>Certifique-se de que os parâmetros de tipo seguro são utilizados na aplicação Web para acesso a dados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Se utilizar a coleção de parâmetros, trata do SQL Server é a entrada como um valor literal em vez disso, em seguida, como o código executável. A coleção de parâmetros pode ser utilizada para impor restrições de tipo e o comprimento em dados de entrada. Valores fora do intervalo acionam uma excepção. Se não forem utilizados parâmetros de tipo seguro SQL Server, os atacantes poderão executar ataques de injeção que estão incorporados na entrada e não filtrada.</p><p>Utilize os parâmetros de tipo seguro quando construir consultas SQL para evitar possíveis ataques de injeção de SQL que podem ocorrer com entrada e não filtrada. Pode utilizar os parâmetros de tipo seguro com procedimentos armazenados e com as instruções SQL dinâmicas. Os parâmetros são tratados como valores literais pela base de dados e não como o código executável. Os parâmetros também são verificados para o tipo e o comprimento.</p>|

### <a name="example"></a>Exemplo 
O código seguinte mostra como utilizar os parâmetros de tipo seguro com o SqlParameterCollection ao chamar um procedimento armazenado. 

```C#
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter(LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
No exemplo de código anterior, o valor de entrada não pode ser superior a 11 carateres. Se os dados não está em conformidade com o tipo ou definido pelo parâmetro de comprimento, a classe de SqlParameter emite uma exceção. 

## <a id="binding-mvc"></a>Utilize as classes de enlace do modelo separadas ou apresenta uma lista de filtro de enlace para impedir a vulnerabilidade de atribuição em massa do MVC

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Atributos de metadados](http://msdn.microsoft.com/library/system.componentmodel.dataannotations.metadatatypeattribute), [pública chave segurança vulnerabilidade e atenuação](https://github.com/blog/1068-public-key-security-vulnerability-and-mitigation), [guia completo para a atribuição de massa no ASP.NET MVC](http://odetocode.com/Blogs/scott/archive/2012/03/11/complete-guide-to-mass-assignment-in-asp-net-mvc.aspx), [introdução ao EF utilizar MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application#overpost) |
| **Passos** | <ul><li>**Quando deve ser de vulnerabilidades de publicação excessiva? -** Vulnerabilidades de publicação excessiva pode ocorrer a qualquer local vincular as classes do modelo da intervenção do utilizador. Estruturas como MVC podem representar os dados de utilizador em classes de .NET personalizadas, incluindo objetos CLR antigo (POCOs) simples. MVC preenche automaticamente estas classes de modelo com os dados do pedido, fornecendo uma representação conveniente para lidar com intervenção do utilizador. Quando estas classes incluem propriedades que não devem ser definidas pelo utilizador, a aplicação pode estar vulnerável a ataques, que permite que o controlo de utilizador dos dados que se destina a aplicação nunca de publicação excessiva. Como o enlace do modelo MVC, base de dados de aceder a tecnologias, como o objeto relacional mappers como do Entity Framework, muitas vezes, também suporte através de objetos de POCO para representar a base de dados. Estas classes de modelo de dados de fornecer a mesma conveniência no lidar com a base de dados, como sucede MVC no lidar com intervenção do utilizador. Porque MVC e a base de dados suportam modelos semelhantes, à POCO objetos, parece fácil reutilizar as mesmas classes para ambos os efeitos. Esta prática não consegue manter a separação das preocupações e é uma área comum, onde propriedades indesejadas são expostas ao enlace de modelo, ativar a ataques de publicação superior.</li><li>**Por que motivo não deve utilizar a minha classes de modelo de base de dados e não filtradas como parâmetros para os meus ações de MVC? -** Enlace do modelo MVC porque irá vincular a nada nessa classe. Mesmo que os dados não aparecem na vista, um utilizador mal intencionado pode enviar um pedido de HTTP com estes dados incluídos e MVC será gladly vinculá-la porque a ação indica que a classe de base de dados é a forma de dados, deve aceitar entrada do utilizador.</li><li>**Por que motivo deve importantes sobre a forma utilizada para enlace do modelo? -** Enlace do modelo a utilizar o ASP.NET MVC com os modelos abrangentes expõe uma aplicação para ataques de publicação excessiva. Foi possível ativar a publicação superior atacantes alterar os dados da aplicação para além de que o programador pretendido, tais como substituir o preço de um item ou os privilégios de segurança para uma conta. As aplicações devem utilizar o contrato de enlace modelos (ou as listas de filtros de propriedade permitidos específico) para fornecer explícita específicos de ação para que a entrada não fidedigno permitir através do enlace do modelo.</li><li>**Está a ter modelos de enlace separado duplicar apenas código? -** Não, é um fim de separação das preocupações. Se reutilizar os modelos de base de dados em métodos de ação, são indicar qualquer propriedade (ou propriedade secundárias) nessa classe pode ser definida pelo utilizador no pedido de HTTP. Se for esse não o que pretende MVC para o fazer, precisa de uma lista de filtro ou uma forma de classe separado para mostrar MVC os dados que podem resultar em vez disso, a intervenção do utilizador.</li><li>**Se tiver modelos de enlace separado para intervenção do utilizador, é necessário duplicar todos os atributos de anotação os meus dados? -** Não necessariamente. Pode utilizar MetadataTypeAttribute na classe de modelo de base de dados para ligar para os metadados de uma classe de enlace do modelo. Apenas tenha em atenção que o tipo referenciado pelo MetadataTypeAttribute tem de ser um subconjunto do tipo referência (pode ter menos de propriedades, mas não mais).</li><li>**Mover dados novamente e estabelecido entre os modelos de base de dados e modelos de entrada do utilizador é tedious. Pode posso apenas copiar através de todas as propriedades utilizando reflexão? -** Sim. As propriedades só que aparecem nos modelos de enlace são aqueles que tenha determinado para ser seguro para a entrada do utilizador. Não há nenhuma razão de segurança que impeça utilizando reflexão copiar através de todas as propriedades que existem em comum entre estes dois modelos.</li><li>**E vai [vincular (excluir = "â €¦")]. Posso utilizar o que em vez de ter modelos de enlace separado? -** Esta abordagem não é recomendada. Utilizar [vincular (excluir = "â €¦")] significa que qualquer propriedade novo que podem ser ligada por predefinição. Quando é adicionada uma nova propriedade, há um passo adicional de recordar para manter as coisas seguras, em vez de ter a estrutura de serem seguras por predefinição. Consoante o programador é arriscado esta lista de verificação, sempre que é adicionada uma propriedade.</li><li>**Está [vincular (incluir = "â €¦")] útil para operações de edição? -** Não. [Vincular (incluir = "â €¦")] só é adequado para operações de inserção de estilo (adicionar novos dados). Para operações de estilo de ATUALIZAÇÃO (dão origem de dados existentes), utilize outra abordagem, como ter modelos de enlace separado ou transmitir uma lista explícita de propriedades permitidas UpdateModel ou TryUpdateModel. Adicionar uma [vincular (incluir = "â €¦")] o atributo de uma operação de edição significa que o MVC irá criar uma instância de objeto e definir as propriedades listadas, deixando todos os outros nos respetivos valores predefinidos. Quando os dados são mantidos, inteiramente substituirá a entidade existente, repor os valores para as propriedades omitidos para as predefinições. Por exemplo, se IsAdmin foi omitido de uma [vincular (incluir = "â €¦")] o atributo de uma operação de edição, qualquer utilizador cujo nome foi editado através esta ação iria ser reposto no IsAdmin = false (qualquer utilizador editado perderia Estado administrador). Se pretende impedir que as atualizações para determinadas propriedades, utilize uma das outras abordagens acima. Tenha em atenção que algumas versões das ferramentas de MVC geram classes controlador [vincular (incluir = "â €¦")] no editar ações e implica que a remoção de uma propriedade que lista irá impedir ataques de publicação superior. No entanto, como descrito acima, o que abordagem não funcionar conforme pretendido e em vez disso, irá repor os dados nas propriedades de omitido para os respetivos valores predefinidos.</li><li>**Para as operações criar, existem quaisquer avisos com [vincular (incluir = "â €¦")] em vez de modelos de enlace separado? -** Sim. Primeiro esta abordagem não funciona para os cenários de edição, que requerem mantém duas abordagens separadas para mitigar todas as vulnerabilidades de publicação superior. Modelos de enlace segundo, separado imponha a separação das preocupações entre forma utilizada para intervenção do utilizador e a forma utilizada para a persistência, algo [vincular (incluir = "â €¦")] que não. Terceira, tenha em atenção que [vincular (incluir = "â €¦")] só pode processar propriedades de nível superior; Não é possível permitir que apenas as partes de propriedades secundárias (por exemplo, "Details.Name") no atributo. Por fim e talvez mais importante ainda, utilizando [vincular (incluir = "â €¦")] Adiciona um passo adicional que deve ser memorizado sempre a classe é utilizado para enlace do modelo. Se um novo método de ação vincula diretamente para a classe de dados e se esquecer incluir um [vincular (incluir = "â €¦")] o atributo, pode ser vulnerável a ataques, de publicação excessiva o [vincular (incluir = "â €¦")] abordagem é um pouco menos segura por predefinição. Se utilizar [vincular (incluir = "â €¦")], asseguramos sempre lembrar-se de especificá-la sempre as classes de dados aparecem como parâmetros de método de ação.</li><li>**Para as operações criar, colocando e sobre o [vincular (incluir = "â €¦")] atributo na classe de modelo em si? Não esta abordagem evitar a necessidade de não se esqueça de colocar o atributo em cada método da ação? -** Esta abordagem funciona em alguns casos. Utilizar [vincular (incluir = "â €¦")] no tipo de modelo em si (e não nos parâmetros de ação utilizando esta classe), a evitar a necessidade de não se esqueça de incluir o [vincular (incluir = "â €¦")] atributo em cada método de ação. Utilizando o atributo diretamente na classe de forma eficaz cria uma área de superfície separada desta classe para fins de enlace do modelo. No entanto, esta abordagem apenas permite uma forma de enlace de modelo por classe de modelo. Se um método de ação tem de permitir o enlace do modelo de um campo (por exemplo, um administrador só ação que atualiza as funções de utilizador) e outras ações que precisam impedir que o enlace do modelo deste campo, esta abordagem não funcionará. Cada classe só pode ter uma forma de enlace de modelo; Se precisam de ações diferentes formas de enlace de modelo diferente, têm de representar estes formas separadas a utilizar qualquer uma das classes de enlace separado do modelo ou de separar [vincular (incluir = "â €¦")] atributos nos métodos de ação.</li><li>**O que são enlace modelos? São a mesma coisa como modelos de vista? -** Estes são dois conceitos relacionados. O termo de modelo de enlace refere-se a um modelo de classe utilizada numa ação é a lista de parâmetros (forma transmitida MVC enlace do modelo para o método da ação). O modelo de vista de prazo refere-se a uma classe de modelo transmitida a partir de um método de ação para uma vista. A utilização de um modelo de vista específico é uma abordagem comum para transmitir dados a partir de um método de ação para uma vista. Muitas vezes, esta forma também é adequada para o enlace do modelo e o modelo de vista do termo pode ser utilizado para designar o mesmo modelo utilizado em ambos os locais. Para que seja precisa, este procedimento aborda especificamente enlace modelos, concentrar-se na forma transmitida para a ação que é o que é importante para fins de atribuição em massa.</li></ul>| 

## <a id="rendering"></a>Codificar o resultado da web não fidedignos antes da composição

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, MVC6 de formulários, MVC5, Web |
| **Atributos**              | N/D  |
| **Referências**              | [Como impedir a criação de scripts entre sites no ASP.NET](http://msdn.microsoft.com/library/ms998274.aspx), [script entre sites](http://cwe.mitre.org/data/definitions/79.html), [Cheat folha do prevenção XSS (scripts entre sites)](https://www.owasp.org/index.php/XSS_(Cross_Site_Scripting)_Prevention_Cheat_Sheet) |
| **Passos** | Processamento de scripts entre sites (frequentemente abreviado como XSS) é um vetor de ataque de serviços online ou qualquer/componente da aplicação que consome entrada a partir do web. Vulnerabilidades XSS podem permitir que um atacante ao executar script na máquina de outro utilizador através de uma aplicação web vulnerável. Scripts maliciosos podem ser utilizados para roubar cookies e caso contrário adulterar a máquina de uma vítima através do JavaScript. XSS é evitada ao validar a intervenção do utilizador, garantindo que está bem formado e codificação antes de este é composta numa página web. Validação de entrada e saída codificação podem ser feitas utilizando a biblioteca de proteção de Web. Para código gerido (C\#, VB.net, etc.), utilize um ou mais adequado métodos codificação da biblioteca Web proteção (Anti-XSS), consoante o contexto em que a entrada de utilizador obtém apresentada:| 

### <a name="example"></a>Exemplo

```C#
* Encoder.HtmlEncode 
* Encoder.HtmlAttributeEncode 
* Encoder.JavaScriptEncode 
* Encoder.UrlEncode
* Encoder.VisualBasicScriptEncode 
* Encoder.XmlEncode 
* Encoder.XmlAttributeEncode 
* Encoder.CssEncode 
* Encoder.LdapEncode 
```

## <a id="typemodel"></a>Efetuar a validação de entrada e filtrar no tipo de cadeia todas as propriedades de modelo

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC6 genérico, MVC5, |
| **Atributos**              | N/D  |
| **Referências**              | [A adição de validação](http://www.asp.net/mvc/overview/getting-started/introduction/adding-validation), [validar o modelo de dados numa aplicação MVC](http://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [ao servir pelos princípios para as suas aplicações de ASP.NET MVC](http://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Passos** | <p>Todos os parâmetros de entrada tem de ser validados antes de poderem são utilizados na aplicação para se certificar de que a aplicação é protegida contra entradas do utilizador mal intencionado. Valide os valores de entrada utilizando validações de expressão regular no lado do servidor com uma estratégia de validação da lista branca. Unsanitized introduções de utilizador / parâmetros transmitidos para os métodos podem fazer com código de vulnerabilidades de injeção.</p><p>Para aplicações web, também podem incluir pontos de entrada campos do formulário, QueryStrings, os cookies, cabeçalhos de HTTP e os parâmetros do serviço web.</p><p>As seguintes verificações de validação de entrada tem de ser executadas após o enlace do modelo:</p><ul><li>As propriedades do modelo devem ser anotadas com anotação RegularExpression, para aceitar caracteres permitidos e o comprimento permissível máximo</li><li>Os métodos de controlador devem efetuar ModelState validade</li></ul>|

## <a id="richtext"></a>Sanitização deve ser aplicada em campos de formulário que aceitam todos os carateres, por exemplo, o editor de texto formatado

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Codificar entrada insegura](https://msdn.microsoft.com/library/ff647397.aspx#paght000003_step3), [Sanitizer de HTML](https://github.com/mganss/HtmlSanitizer) |
| **Passos** | <p>Identifique todas as etiquetas de marcação estáticos que pretende utilizar. É uma prática comum para restringir a formatação para elementos HTML seguros, tais como `<b>` (negrito) e `<i>` (itálico).</p><p>Antes de escrever os dados, codificar de HTML-lo. Isto faz com que quaisquer scripts maliciosos seguro, causando-lo para ser processado como texto, não como o código executável.</p><ol><li>Desativar a validação de pedido do ASP.NET adicionando o o ValidateRequest = "false" atributo como diretiva @ página</li><li>Codificar a entrada de cadeia com o método HtmlEncode</li><li>Utilize um StringBuilder e chame o método de substituição para remover seletivamente a codificação nos elementos HTML que pretende permitir</li></ol><p>O página-na validação do pedido ASP.NET desativa referências definindo `ValidateRequest="false"`. -Lo a entrada de codifica HTML e seletivamente permite o `<b>` e `<i>` em alternativa, também pode ser utilizada uma biblioteca de .NET de sanitização de HTML.</p><p>HtmlSanitizer é uma biblioteca de .NET para a limpeza de fragmentos HTML e os documentos de construções que podem conduzir a ataques XSS. Utiliza AngleSharp para analisar, a manipulação e a compor HTML e CSS. HtmlSanitizer pode ser instalado como um pacote NuGet e pode ser transmitida a entrada de utilizador relevantes HTML ou CSS sanitização métodos, conforme aplicável, no lado do servidor. Tenha em atenção que Sanitização como um controlo de segurança deve ser considerada apenas como uma opção de último.</p><p>Validação de entrada e saída codificação são consideradas a melhor controlos de segurança.</p> |

## <a id="inbuilt-encode"></a>Não atribua elementos DOM para sinks que não têm a codificação integradas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | Muitas funções de javascript não fazem nada codificação por predefinição. Ao atribuir a entrada não fidedigna para os elementos de DOM através essas funções, poderão resultar entre execuções de script (XSS) do site.| 

### <a name="example"></a>Exemplo
Seguem-se exemplos inseguras: 

```
document.getElementByID("div1").innerHtml = value;
$("#userName").html(res.Name);
return $('<div/>').html(value)
$('body').append(resHTML);   
```
Não utilize `innerHtml`; em vez disso, utilize `innerText`. Da mesma forma, em vez de `$("#elm").html()`, utilize`$("#elm").text()` 

## <a id="redirect-safe"></a>Validar se todos os redireccionamentos na aplicação são fechados ou feitos de forma segura

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [A estrutura de autorização do OAuth 2.0 - Redirectors abertas](http://tools.ietf.org/html/rfc6749#section-10.15) |
| **Passos** | <p>Estrutura de aplicação que requerem redirecionamento para uma localização fornecido pelo utilizador tem de restringir os destinos de redirecionamento possíveis para uma lista predefinida de "seguro" de sites ou domínios. Todos os redireccionamentos na aplicação tem de ser fechado/segura.</p><p>Para efetuar este procedimento:</p><ul><li>Identifique todos os redirecionamentos</li><li>Implemente uma mitigação adequada para cada tipo de redirecionamento. Mitigações adequadas incluem confirmação de lista de permissões ou de utilizador de redirecionamento. Se a um web site ou serviço com uma vulnerabilidade do redirecionamento abra utiliza fornecedores de identidade OAuth/Facebook/OpenID, um atacante pode roubar o token de início de sessão do utilizador e representar esse utilizador. Isto é um risco inerente ao utilizar OAuth, que é descrita no RFC 6749 "O OAuth 2.0 autorização Framework", secção 10.15 "abrir redireciona" da mesma forma, as credenciais de utilizadores podem ficar comprometidas por ataques de phishing spear utilizando redirecionamentos abertos</li></ul>|

## <a id="string-method"></a>Implementar a validação de entrada em todos os parâmetros de tipo de cadeia aceites pelos métodos de controlador

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC6 genérico, MVC5, |
| **Atributos**              | N/D  |
| **Referências**              | [Validar o modelo de dados numa aplicação MVC](http://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [ao servir pelos princípios para as suas aplicações de ASP.NET MVC](http://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Passos** | Métodos de que apenas aceitam o tipo de dados primitivo e não modelos como argumento, deve ser feita utilizando uma expressão Regular de validação de entrada. Aqui Regex.IsMatch deve ser utilizada com um padrão regex válido. Se a entrada não corresponde à expressão Regular especificada, o controlo não deverá continuar e deve ser apresentado um aviso sobre a falha de validação adequado.| 

## <a id="dos-expression"></a>Definir limite de tempo limite superior para a expressão regular de processamento para evitar DoS devido a expressões regulares incorretas

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, MVC6 de formulários, MVC5, Web  |
| **Atributos**              | N/D  |
| **Referências**              | [Propriedade de DefaultRegexMatchTimeout](https://msdn.microsoft.com/library/system.web.configuration.httpruntimesection.defaultregexmatchtimeout.aspx) |
| **Passos** | Para assegurar o ataques denial of service contra formatado criadas expressões regulares, o que fazer com que uma grande quantidade de controlo inverso, defina o tempo limite global predefinido. Se o tempo de processamento demora mais que o limite superior definido, poderia acionar uma exceção de tempo limite. Se nada estiver configurado, o tempo limite seria infinito.| 

### <a name="example"></a>Exemplo
Por exemplo, a seguinte configuração irá gerar um RegexMatchTimeoutException, se o processamento demora mais de 5 segundos: 

```C#
<httpRuntime targetFramework="4.5" defaultRegexMatchTimeout="00:00:05" />
```

## <a id="html-razor"></a>Evite utilizar Html.Raw nas vistas de Razor

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Aplicação Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| Passo | Páginas Web do ASP.Net (Razor) execute a codificação de HTML automática. Todas as cadeias impressas por nuggets código incorporado (@ blocos) são automaticamente codificado em HTML. No entanto, quando `HtmlHelper.Raw` método é invocado, devolve markup que não é codificado em HTML. Se `Html.Raw()` é utilizado o método de programa auxiliar, se ignora a proteção automática de codificação que fornece Razor.|

### <a name="example"></a>Exemplo
Segue-se um exemplo inseguras: 

```C#
<div class="form-group">
            @Html.Raw(Model.AccountConfirmText)
        </div>
        <div class="form-group">
            @Html.Raw(Model.PaymentConfirmText)
        </div>
</div>
```
Não utilize `Html.Raw()` , a menos que tem de apresentar markup. Este método não efetua a saída codificação implicitamente. Por exemplo, a utilizar outros programas de ajuda do ASP.NET`@Html.DisplayFor()` 

## <a id="stored-proc"></a>Não utilize consultas dinâmicas em procedimentos armazenados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de Dados | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Um ataque de injeção de SQL explorar vulnerabilidades na validação de entrada para executar comandos arbitrários na base de dados. Isto pode ocorrer quando a aplicação utiliza entrada para construir dinâmicas instruções SQL para aceder à base de dados. Também pode ocorrer se o seu código utiliza os procedimentos armazenados que são transmitidos cadeias que contêm a intervenção do utilizador não processados. Utilizar o ataque de injeção de SQL, o atacante pode executar comandos arbitrários na base de dados. Todas as instruções SQL (incluindo as instruções SQL em procedimentos armazenados) tem de ser parametrizadas. Aceitam carateres que têm um significado especial para o SQL Server (como aspa simples) sem problemas, porque estes são tipos de dados inflexíveis instruções parametrizadas do SQL Server. |

### <a name="example"></a>Exemplo
Segue-se um exemplo de procedimento armazenado de dinâmica inseguras: 

```C#
CREATE PROCEDURE [dbo].[uspGetProductsByCriteria]
(
  @productName nvarchar(200) = NULL,
  @startPrice float = NULL,
  @endPrice float = NULL
)
AS
 BEGIN
  DECLARE @sql nvarchar(max)
  SELECT @sql = ' SELECT ProductID, ProductName, Description, UnitPrice, ImagePath' +
       ' FROM dbo.Products WHERE 1 = 1 '
       PRINT @sql
  IF @productName IS NOT NULL
     SELECT @sql = @sql + ' AND ProductName LIKE ''%' + @productName + '%'''
  IF @startPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice > ''' + CONVERT(VARCHAR(10),@startPrice) + ''''
  IF @endPrice IS NOT NULL
     SELECT @sql = @sql + ' AND UnitPrice < ''' + CONVERT(VARCHAR(10),@endPrice) + ''''

  PRINT @sql
  EXEC(@sql)
 END
```

### <a name="example"></a>Exemplo
Segue-se o mesmo procedimento armazenado implementado de forma segura: 
```C#
CREATE PROCEDURE [dbo].[uspGetProductsByCriteriaSecure]
(
             @productName nvarchar(200) = NULL,
             @startPrice float = NULL,
             @endPrice float = NULL
)
AS
       BEGIN
             SELECT ProductID, ProductName, Description, UnitPrice, ImagePath
             FROM dbo.Products where
             (@productName IS NULL or ProductName like '%'+ @productName +'%')
             AND
             (@startPrice IS NULL or UnitPrice > @startPrice)
             AND
             (@endPrice IS NULL or UnitPrice < @endPrice)         
       END
```

## <a id="validation-api"></a>Certifique-se de que a validação do modelo é efetuada em métodos de Web API

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | MVC5, MVC6 |
| **Atributos**              | N/D  |
| **Referências**              | [Validação do modelo na API Web do ASP.NET](http://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Passos** | Quando um cliente envia dados para uma API web, é obrigatório para validar os dados antes de efetuar qualquer processamento. Para APIs da Web de ASP.NET que aceite modelos como entrada, utilize anotações de dados em modelos para definir as regras de validação nas propriedades do modelo.|

### <a name="example"></a>Exemplo
O código seguinte demonstra a mesma: 

```C#
using System.ComponentModel.DataAnnotations;

namespace MyApi.Models
{
    public class Product
    {
        public int Id { get; set; }
        [Required]
        [RegularExpression(@"^[a-zA-Z0-9]*$", ErrorMessage="Only alphanumeric characters are allowed.")]
        public string Name { get; set; }
        public decimal Price { get; set; }
        [Range(0, 999)]
        public double Weight { get; set; }
    }
}
```

### <a name="example"></a>Exemplo
O método de ação dos controladores de API, validade do modelo tem de ser explicitamente marcada como mostrado abaixo: 

```C#
namespace MyApi.Controllers
{
    public class ProductsController : ApiController
    {
        public HttpResponseMessage Post(Product product)
        {
            if (ModelState.IsValid)
            {
                // Do something with the product (not shown).

                return new HttpResponseMessage(HttpStatusCode.OK);
            }
            else
            {
                return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
            }
        }
    }
}
```

## <a id="string-api"></a>Implementar a validação de entrada em todos os parâmetros de tipo de cadeia aceites pelos métodos de Web API

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, MVC 5, 6 de MVC |
| **Atributos**              | N/D  |
| **Referências**              | [Validar o modelo de dados numa aplicação MVC](http://msdn.microsoft.com/library/dd410404(v=vs.90).aspx), [ao servir pelos princípios para as suas aplicações de ASP.NET MVC](http://msdn.microsoft.com/magazine/dd942822.aspx) |
| **Passos** | Métodos de que apenas aceitam o tipo de dados primitivo e não modelos como argumento, deve ser feita utilizando uma expressão Regular de validação de entrada. Aqui Regex.IsMatch deve ser utilizada com um padrão regex válido. Se a entrada não corresponde à expressão Regular especificada, o controlo não deverá continuar e deve ser apresentado um aviso sobre a falha de validação adequado.|

## <a id="typesafe-api"></a>Certifique-se de que os parâmetros de tipo seguro são utilizados na Web API para acesso a dados

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | API Web | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | N/D  |
| **Passos** | <p>Se utilizar a coleção de parâmetros, trata do SQL Server é a entrada como um valor literal em vez disso, em seguida, como o código executável. A coleção de parâmetros pode ser utilizada para impor restrições de tipo e o comprimento em dados de entrada. Valores fora do intervalo acionam uma excepção. Se não forem utilizados parâmetros de tipo seguro SQL Server, os atacantes poderão executar ataques de injeção que estão incorporados na entrada e não filtrada.</p><p>Utilize os parâmetros de tipo seguro quando construir consultas SQL para evitar possíveis ataques de injeção de SQL que podem ocorrer com entrada e não filtrada. Pode utilizar os parâmetros de tipo seguro com procedimentos armazenados e com as instruções SQL dinâmicas. Os parâmetros são tratados como valores literais pela base de dados e não como o código executável. Os parâmetros também são verificados para o tipo e o comprimento.</p>|

### <a name="example"></a>Exemplo
O código seguinte mostra como utilizar os parâmetros de tipo seguro com o SqlParameterCollection ao chamar um procedimento armazenado. 

```C#
using System.Data;
using System.Data.SqlClient;

using (SqlConnection connection = new SqlConnection(connectionString))
{ 
DataSet userDataset = new DataSet(); 
SqlDataAdapter myCommand = new SqlDataAdapter(LoginStoredProcedure", connection); 
myCommand.SelectCommand.CommandType = CommandType.StoredProcedure; 
myCommand.SelectCommand.Parameters.Add("@au_id", SqlDbType.VarChar, 11); 
myCommand.SelectCommand.Parameters["@au_id"].Value = SSN.Text; 
myCommand.Fill(userDataset);
}  
```
No exemplo de código anterior, o valor de entrada não pode ser superior a 11 carateres. Se os dados não está em conformidade com o tipo ou definido pelo parâmetro de comprimento, a classe de SqlParameter emite uma exceção. 

## <a id="sql-docdb"></a>Utilizar consultas SQL parametrized para Cosmos DB

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | Base de dados de documento do Azure | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico |
| **Atributos**              | N/D  |
| **Referências**              | [Anunciar SQL parametrização Forced no DocumentDB](https://azure.microsoft.com/blog/announcing-sql-parameterization-in-documentdb/) |
| **Passos** | Embora o DocumentDB suporta apenas consultas de só de leitura, injeção de SQL é possível se as consultas são construídas por concatenar com a intervenção do utilizador. Poderá ser possível que um utilizador obtenha acesso aos dados que não devem estar a aceder aos dentro da mesma coleção por maliciosas consultas SQL de composição. Utilize consultas parametrizadas do SQL Server se as consultas são construídas com base na entrada de utilizador. |

## <a id="schema-binding"></a>Validação de entrada de WCF através de vinculação ao esquema

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, o NET Framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff647820.aspx) |
| **Passos** | <p>Falta de validação leva a ataques de injeção de tipo diferente.</p><p>Validação da mensagem representa uma linha de defesa na proteção da sua aplicação de WCF. Com esta abordagem, validar mensagens utilizando esquemas para proteger as operações de serviço WCF contra ataques por um cliente malicioso. Valide todas as mensagens recebidas pelo cliente para proteger o cliente contra ataques por um serviço malicioso. Validação da mensagem torna possível validar o mensagens operações consumir contratos de mensagem ou contratos de dados, que não podem ser efetuados utilizando a validação do parâmetro. Validação da mensagem permite-lhe criar lógica de validação no interior de esquemas, deste modo, fornecendo uma maior flexibilidade e reduzir o tempo de desenvolvimento. Esquemas podem ser reutilizadas em diferentes aplicações dentro da organização, criar normas de representação de dados. Além disso, validação de mensagem permite-lhe proteger operações quando consumirem tipos de dados mais complexos, envolvendo contratos que representa a lógica de negócio.</p><p>Para efetuar a validação da mensagem, primeiro crie um esquema que representa as operações do seu serviço e os tipos de dados consumidos por essas operações. Em seguida, crie uma classe .NET que implementa um inspector de mensagem de cliente personalizadas e inspector de mensagem de emissor personalizado para validar as mensagens enviadas/recebido do serviço. Em seguida, implementar um comportamento de ponto final personalizadas para ativar a validação de mensagem com o cliente e o serviço. Por fim, implementar um elemento de configuração personalizada na classe que lhe permite expor o comportamento de ponto final personalizado expandida no ficheiro de configuração do serviço ou o cliente"</p>|

## <a id="parameters"></a>Validação de WCF - entrada através do parâmetro Inspetores

| Título                   | Detalhes      |
| ----------------------- | ------------ |
| **Componente**               | WCF | 
| **Fase SDL**               | Compilação |  
| **Tecnologias aplicáveis** | Genérico, o NET Framework 3 |
| **Atributos**              | N/D  |
| **Referências**              | [MSDN](https://msdn.microsoft.com/library/ff647875.aspx) |
| **Passos** | <p>Validação de entrada e de dados representa um importante de linha de defesa na proteção da sua aplicação de WCF. Deve validar todos os parâmetros expostos em operações de serviço WCF para proteger o serviço de ataque por um cliente malicioso. Por outro lado, também deve validar todos os valores de retorno recebidos pelo cliente para proteger o cliente contra ataques por um serviço malicioso</p><p>WCF fornece pontos de extensibilidade diferentes que lhe permitem personalizar o comportamento de tempo de execução WCF através da criação de extensões personalizadas. Mensagem Inspetores e parâmetro Inspetores são dois mecanismos de extensibilidade utilizados para obter maior controlo sobre os dados passar entre um cliente e um serviço. Deve utilizar inspetores de parâmetro para a validação de entrada e utilizar inspetores mensagem apenas quando precisar de inspecionar a mensagem completa fluir dentro e fora de um serviço.</p><p>Para efetuar a validação de entrada, irá criar uma classe de .NET e implementar uma inspector de parâmetro personalizado para validar os parâmetros em operações no seu serviço. Em seguida, irá implementar um comportamento de ponto final personalizadas para ativar a validação no cliente e o serviço. Por fim, irá implementar um elemento de configuração personalizada na classe que lhe permite expor o comportamento de ponto final personalizado expandida no ficheiro de configuração do serviço ou o cliente</p>|
